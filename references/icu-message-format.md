# ICU MessageFormat — deep reference

ICU is the industry standard for translatable strings containing variables, plurals, gender, and formatted values. Libraries: FormatJS / `react-intl`, `next-intl`, `i18next` + `i18next-icu`, Lingui, Angular, Java.

## Syntax quick table

| Argument | Syntax | Use |
|---|---|---|
| Variable | `{name}` | named placeholder |
| Plural | `{count, plural, one {# item} other {# items}}` | counts via CLDR rules |
| Select | `{gender, select, female {She} male {He} other {They}}` | gender, role, status |
| Select ordinal | `{rank, selectordinal, one {#st} two {#nd} few {#rd} other {#th}}` | rankings, positions |
| Number | `{price, number, ::currency/USD}` | currency, percent, compact |
| Date | `{date, date, long}` | locale-aware date |
| Time | `{time, time, short}` | locale-aware time |

## Rules

- **Single braces** for arguments. `{{name}}` is a parser error (not Mustache/Handlebars).
- `#` inside a plural branch is replaced by the formatted numeric value of the count.
- `=value` is an exact-match override evaluated *before* CLDR rules — use it for special zero/one copy: `=0 {No messages}`.
- `other` is **always required**.
- Plural categories come from Unicode CLDR, never hardcoded: `zero | one | two | few | many | other`. The runtime selects via `Intl.PluralRules(locale)`.
- `offset:` subtracts a fixed number before applying rules — e.g. "You and N others liked this" without app-level arithmetic: `{count, plural, offset:1 one {You liked this} other {You and # others liked this}}`.

## Plural rules vary by language (do not assume English)

| Language | Categories |
|---|---|
| English | one, other (2) |
| French | one, many, other |
| Japanese / Chinese / Korean | other only (1) |
| Russian / Polish / Ukrainian | one, few, many, other (3–4) |
| Arabic | zero, one, two, few, many, other (6) |

Provide every category the target language requires. Missing categories silently fall back and read wrong to native speakers.

## Nested arguments

One string can encode plural + gender combinations (word order and pronouns vary by language):

```
{gender, select,
  female {{count, plural, one {She left # comment} other {She left # comments}}}
  male   {{count, plural, one {He left # comment} other {He left # comments}}}
  other  {{count, plural, one {They left # comment} other {They left # comments}}}
}
```

Concatenating `actor + " shared " + count + " files"` is untranslatable in most languages.

## Common mistakes

- Passing `count` as a string (`'5'`) — `Intl.PluralRules` needs a number.
- Missing plural categories for the target locale.
- v3/v4 key format mismatch (`key_plural`/`key_0` vs v4) — resolves to base key or nothing.
- Assuming English rules everywhere: `0` is `other` in English but `zero` in Arabic.
- Rendering before async catalogs load — shows raw keys. Turn library `debug: true` on to trace key resolution.

## MessageFormat 2 (MF2)

ICU is being succeeded by Unicode MessageFormat 2 (Final Candidate since 2025). MF2 uses explicit `.match` selectors and handles gender+plural more cleanly. Adoption is early — not yet the pragmatic default. Track it via your library (e.g. i18next MF2 support) before migrating.
