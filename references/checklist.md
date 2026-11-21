# i18n PR review & QA checklist

Run this on any feature that adds or changes user-facing text.

## Extraction & keys

- [ ] No hardcoded user-facing strings in code (JSX text, attributes, `aria-label`, `placeholder`, errors, toasts).
- [ ] Every literal reaches the UI through a message catalog key.
- [ ] No string concatenation or sentence splitting — named placeholders only.
- [ ] Keys are semantic, feature-grouped, and follow the linted naming convention.
- [ ] Reusable strings use `common.*`; no duplicate keys with different wording.
- [ ] Translators have context: description + placeholder meanings, full sentences (punctuation included).

## Messages

- [ ] ICU used for all plurals, selects, and ordinals; no `if (count === 1)`.
- [ ] `other` present in every ICU plural/select.
- [ ] All required CLDR categories provided for each target locale (esp. Arabic, Russian, Polish).
- [ ] `count` passed as a number, not a string.
- [ ] `#` used for the formatted count; `=0` used only for genuine zero-state copy.
- [ ] Plural edge cases tested: 0, 1, 2, 5, 11, 21, 100, 1,000,000.

## Formatting

- [ ] Dates/times/numbers/currency/percent/lists/relative-time/sorting use `Intl.*`, never manual templates.
- [ ] No `Date.toLocaleDateString()` without a locale argument.
- [ ] Dates stored UTC; money stored as `{ amount, currency }`; formatted only at display.

## Locale & routing

- [ ] BCP 47 locale tags used (`en-US`, `zh-CN`, `ar-EG`).
- [ ] Negotiation order: user choice → URL → Accept-Language → default.
- [ ] Choice persists; URL prefix works; `<html lang>` and `<html dir>` set from locale.
- [ ] Each locale has a crawlable URL with `hreflang` alternates (use `seo` skill).
- [ ] Missing-key fallback goes to default locale, never a raw key.

## RTL

- [ ] Logical CSS properties used; no physical left/right where direction matters.
- [ ] Only directional icons mirrored; logos/text/digits untouched.
- [ ] Mixed-direction content isolated with `<bdi>`.
- [ ] RTL screenshot reviewed for every screen.

## Verification gates

- [ ] Pseudo-localization run — surfaced hardcoded strings, truncation, concatenation.
- [ ] Key sets diffed across all catalogs; CI fails on missing keys.
- [ ] Translation completeness checked for every new locale.
- [ ] Locale routing + `<html lang/dir>` verified end to end.

## Common pitfalls (quick reference)

| Symptom | Likely cause |
|---|---|
| Raw key shown instead of text | Key typo, catalog not loaded, or missing key with no fallback |
| "1 messages" / wrong plural in Russian/Arabic | Missing CLDR categories or `count` passed as string |
| French `Bonjour !` renders `Bonjour!` | Message split / punctuation hardcoded outside catalog |
| Layout breaks in Arabic | Physical CSS properties instead of logical ones |
| Text reorders in sentence | Missing `<bdi>` around RTL variable |
| `NaN`/wrong number format | Manual number formatting instead of `Intl.NumberFormat` |
| Wrong date order between users | `toLocaleDateString()` without explicit locale |
