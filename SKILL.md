---
name: i18n-localization
description: Internationalize and localize apps — extract every user-facing string, write ICU MessageFormat messages with correct CLDR plurals, format dates/numbers/currency with Intl, support RTL with logical CSS, and verify with pseudo-localization. Use when adding a language, building locale routing, fixing plural/gender/translation bugs, auditing hardcoded strings, or shipping an app to multiple locales.
---

# i18n-localization

Make software language- and locale-ready. Two phases, do them in order:

- **i18n** — build the app so it *can* be localized (once per feature).
- **l10n** — actually adapt it for a specific locale (once per language).

Most bugs come from skipping i18n and bolting on translations later. Everything user-facing must flow through a message catalog, and everything locale-sensitive must use the platform's locale-aware APIs.

## Jump to

1. [The non-negotiables](#the-non-negotiables)
2. [Workflow](#workflow)
3. [Message keys & catalogs](#message-keys--catalogs)
4. [ICU messages](#icu-messages)
5. [Locale-aware formatting](#locale-aware-formatting)
6. [RTL & layout](#rtl--layout)
7. [Testing & verification](#testing--verification)
8. [Deep references](#deep-references)

## The non-negotiables

Violating any of these creates untranslatable strings or wrong output:

1. **No hardcoded user-facing strings.** Every label, message, error, tooltip, aria-label, and placeholder goes in a catalog. If it reaches the UI, it is a key.
2. **Never concatenate or split sentences.** Word order differs by language. Use named placeholders: `t('greeting', { name, count })` — never `'Hello ' + name + ' you have ' + count`.
3. **ICU MessageFormat for plurals and gender**, never `if (count === 1)`. English has 2 plural forms; Arabic has 6, Russian/Polish 3–4, Japanese/Chinese 1.
4. **Intl for values**, never `toString()` / manual templates. Dates, numbers, currency, percent, lists, relative time, sorting.
5. **Assume text grows and flows both ways.** German strings run ~30% longer than English; Arabic/Hebrew are RTL. Layout must survive both.
6. **Localize error and exception messages too** — including API/protocol messages — and attach language + base direction metadata to natural-language fields.

## Workflow

Follow this order when internationalizing a feature or reviewing a PR:

1. **Inventory strings** — grep the changed code for literals in JSX/text/attributes (`grep -nE '>[A-Z][a-z]{2,}|"[A-Z][a-z]{2,}'`). List every literal that reaches the UI.
2. **Choose a key convention and enforce it** — see below. Establish it before writing keys; lint it.
3. **Extract into a source-of-truth catalog** — one file per locale, grouped by *feature* (not by component file). Keep a `common.*` namespace for reusable strings.
4. **Write full ICU messages** — every dynamic message gets interpolation, plural categories, and select branches in one key. Provide translator context (description + placeholders) alongside each key.
5. **Replace formatting with Intl** — swap every manual date/number/currency for `Intl.*` at display time. Store dates in UTC and money as `{ amount, currency }`.
6. **Wire locale negotiation** — explicit user choice (persisted) → URL/route prefix → `Accept-Language` → default. Set `<html lang>` and `dir` from the resolved locale. Use BCP 47 tags (e.g. `en-US`, `zh-CN`, `ar-EG`).
7. **Handle RTL** — logical CSS properties, mirrored directional icons, `<bdi>` for mixed-direction user content. See [RTL & layout](#rtl--layout).
8. **Verify** — pseudo-localization, plural edge cases, RTL screenshot, missing-key check. See [Testing](#testing--verification).

## Message keys & catalogs

- **Semantic, feature-grouped keys**: `checkout.submit`, `inbox.message_count`. Not `components.CtaButton.label` — components get reorganized, features don't.
- **Stable naming convention**, linted in CI (e.g. lowercase dot-separated, no spaces). A typo in a key is a silent missing translation.
- **One catalog per locale**, same key set. Missing keys fall back to the default locale — never to a raw key in production.
- **Give translators context**: each key carries a description and the meaning of each placeholder (`{name}` = user's display name). "Submit" the verb and "Submit" the noun need different keys.
- **Keep complete messages per key**, including punctuation and surrounding text — translators need the whole sentence to order it correctly.

## ICU messages

A single key holds the whole translatable string. See [references/icu-message-format.md](references/icu-message-format.md) for the full syntax.

```jsonc
{
  "welcome": "Welcome back, {name}!",
  "inbox.message_count": "{count, plural, =0 {No messages} one {# message} other {# messages}}",
  "activity.comment": "{gender, select, female {{count, plural, one {She left # comment} other {She left # comments}}} male {{count, plural, one {He left # comment} other {He left # comments}}} other {{count, plural, one {They left # comment} other {They left # comments}}}}"
}
```

Rules that prevent the common bugs:

- Use **single** curly braces for arguments. `{{name}}` is a parser error in FormatJS/react-intl.
- `#` is replaced by the formatted `count`. `=0` is an exact-value override that fires before CLDR rules — use it for zero-state copy that differs structurally.
- **Always include `other`** — it is required. Use CLDR categories `zero | one | two | few | many | other`; the runtime picks per locale via `Intl.PluralRules`. Don't hardcode thresholds.
- **Pass count as a number**, not a string (`'5'` won't select correctly).
- Never special-case `count === 0` globally — `0` is `other` in English but `zero` in Arabic.
- Test plural edge cases: `0, 1, 2, 5, 11, 21, 100, 1000000`.

## Locale-aware formatting

Format only at display time, with the resolved locale. See [references/intl-formatting.md](references/intl-formatting.md) for recipes.

```js
new Intl.DateTimeFormat(locale).format(dateObj);          // 12/31/2026 vs 31.12.2026 vs 2026/12/31
new Intl.NumberFormat(locale, { style: 'currency', currency }).format(amount); // $99.99 vs 99,99 €
new Intl.NumberFormat(locale, { notation: 'compact' }).format(n);
new Intl.RelativeTimeFormat(locale).format(-3, 'day');    // "3 days ago"
new Intl.ListFormat(locale).format(['a', 'b', 'c']);      // "a, b, and c"
new Intl.Collator(locale).compare(a, b);                  // locale-correct sorting
```

Never call `Date.toLocaleDateString()` without a locale argument — it inherits the browser locale and produces inconsistent output. ICU messages can embed formatting directly: `{price, number, ::currency/USD}`, `{date, date, long}`, `{rank, selectordinal, one {#st} two {#nd} few {#rd} other {#th}}`.

## RTL & layout

See [references/rtl-and-layout.md](references/rtl-and-layout.md). Core moves:

- Use **CSS logical properties** (`margin-inline-start`, `padding-inline-end`, `text-align: start`, `border-inline-start`) instead of physical left/right. They flip automatically under `dir="rtl"`.
- Set `<html lang="..." dir="rtl|ltr">` from the locale. `dir="auto"` for user-generated content.
- **Mirror only directional icons** (arrows, back/next, progress). Never mirror logos, text, or digits.
- Wrap mixed-direction user content in `<bdi>` so it can't reorder surrounding text.
- Design for longer strings: avoid fixed-width buttons, allow wrapping, test with German.

## Testing & verification

A translation is not done until these pass. See [references/checklist.md](references/checklist.md) for the full PR checklist.

- **Pseudo-localization** — render with accented characters and expansion (e.g. `Hello` → `Ḣëḷḷöööö`) to expose hardcoded strings, truncation, and concatenation bugs before translators are involved.
- **Missing-key audit** — diff key sets across all locale catalogs; CI must fail on a key present in one locale and absent in another.
- **Plural edge cases** — run `0, 1, 2, 5, 11, 21, 100` through every plural key in every language; check native-speaker correctness for categories beyond English.
- **RTL pass** — screenshot every screen in an RTL locale; verify logical properties, icon mirroring, and `<bdi>`.
- **Fallback check** — remove a translation and confirm it falls back to the default locale, never to a raw key.
- **Locale routing** — verify choice persists, URL prefix works, `Accept-Language` is respected, and `<html lang/dir>` updates.

## Deep references

| File | When to read |
|---|---|
| [references/icu-message-format.md](references/icu-message-format.md) | Authoring complex plural/select/ordinal/date messages, MessageFormat 2 notes |
| [references/intl-formatting.md](references/intl-formatting.md) | Date/number/currency/list/relative-time/collator recipes, time zones |
| [references/rtl-and-layout.md](references/rtl-and-layout.md) | Mirroring layouts, logical properties, mixed-direction text |
| [references/checklist.md](references/checklist.md) | PR review and QA checklist, common pitfalls |

Framework notes: this skill is framework-agnostic. For React/Next, pair with the project's i18n library — `next-intl`, `react-intl`/FormatJS, or `i18next` + `i18next-icu` — but the catalog/ICU/Intl/RTL rules above apply regardless of library. For Expo/React Native, the same catalog and ICU rules hold; use the platform Intl/formatting equivalents for on-device rendering.
