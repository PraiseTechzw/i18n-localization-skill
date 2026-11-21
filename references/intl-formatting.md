# Intl formatting recipes

Always format with the resolved locale, and only at display time. Store raw values: dates in UTC (ISO 8601), money as `{ amount, currency }`, percentages as numbers.

## Dates & times

```js
const d = new Date('2026-03-15T14:30:00Z');
new Intl.DateTimeFormat(locale).format(d);                    // locale order + calendar
new Intl.DateTimeFormat(locale, { dateStyle: 'long' }).format(d);
new Intl.DateTimeFormat(locale, { timeStyle: 'short', timeZone: 'Europe/Berlin' }).format(d);
```

Examples: `12/31/2026` (en-US), `31.12.2026` (de-DE), `2026/12/31` (ja-JP). Never `Date.toLocaleDateString()` without a locale argument.

## Numbers & currency

```js
new Intl.NumberFormat(locale).format(1234.56);                        // 1,234.56 / 1.234,56
new Intl.NumberFormat(locale, { style: 'currency', currency: 'EUR' }).format(99.99); // 99,99 € / €99.99
new Intl.NumberFormat(locale, { style: 'percent' }).format(0.42);     // 42%
new Intl.NumberFormat(locale, { notation: 'compact' }).format(3580);  // 3.6K (locale-dependent)
new Intl.NumberFormat(locale, { style: 'unit', unit: 'kilometer' }).format(5);
```

## Relative time, lists, collation

```js
new Intl.RelativeTimeFormat(locale).format(-3, 'day');   // "3 days ago"
new Intl.ListFormat(locale).format(['a', 'b', 'c']);     // "a, b, and c" / "a, b und c"
new Intl.Collator(locale).compare('ä', 'z');             // locale-correct sort order
```

Sorting by code point puts accented letters in the wrong place — use `Intl.Collator` for any user-visible list.

## Locale tags (BCP 47)

Use `en`, `fr`, `es`, or region-tagged `en-US`, `zh-CN`, `ar-EG`. Region tags matter for accurate date/number/currency formatting. Validate tags with `Intl.getCanonicalLocales(tag)`.

## Locale negotiation order

1. Explicit user choice (persisted in account/preferences) — highest priority.
2. URL / route prefix (`/fr/...`, `?lang=fr`) — shareable and crawlable.
3. `Accept-Language` header (server) / `navigator.languages` (client).
4. Configured default locale.

Set `<html lang>` and `<html dir>` from the resolved locale. For SEO, give each locale its own URL with `hreflang` alternates (see the `seo` skill).

## ICU embedded formatting

ICU messages can format inline, so translators keep control of placement:

```
{price, number, ::currency/USD .00}
{date, date, long}
{time, time, short}
{count, number, ::compact-short}
{rank, selectordinal, one {#st} two {#nd} few {#rd} other {#th}}
```
