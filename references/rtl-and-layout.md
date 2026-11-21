# RTL & layout

RTL (Arabic, Hebrew, Persian, Urdu) is more than flipping `text-align`. Layout, icons, animations, and number formatting all need attention.

## Set direction from the locale

```html
<html lang="ar-EG" dir="rtl">
```

- `dir="ltr"` / `dir="rtl"` from the resolved locale.
- `dir="auto"` for user-generated content whose direction is unknown.
- Use `lang` on any element whose language differs from the document.

## Use logical CSS properties (the single most important practice)

Logical properties map to physical sides based on `dir`, so the same CSS works for both directions:

| Physical (breaks RTL) | Logical (direction-aware) |
|---|---|
| `margin-left` | `margin-inline-start` |
| `margin-right` | `margin-inline-end` |
| `padding-left` / `padding-right` | `padding-inline-start` / `padding-inline-end` |
| `left` / `right` | `inset-inline-start` / `inset-inline-end` |
| `text-align: left` | `text-align: start` |
| `border-left` | `border-inline-start` |
| `border-top-left-radius` | `border-start-start-radius` |
| `transform: translateX(...)` | mirror sign in RTL or use logical animation values |

Flexbox/Grid already flow by writing mode — prefer `gap`, `justify-content`, `align-items` over positional margins, and they mostly "just work".

## Mirror only directional things

- **Mirror**: back/next arrows,前进/后退 icons, progress bars, breadcrumb chevrons, carousel controls, decorative directional flourishes.
- **Never mirror**: logos, brand marks, text, digits, phone numbers, or icons with inherent direction (e.g. a clock showing time — though clock *hand direction* is culturally nuanced).
- Use CSS `transform: scaleX(-1)` on a dedicated `.rtl-mirror` icon class, or supply a separate mirrored asset.

## Mixed-direction text

Wrap user-generated or embedded content in `<bdi>` (bidirectional isolate) so it can't reorder the surrounding sentence:

```html
<p>User <bdi>أحمد</bdi> left a comment.</p>
```

Also isolate variables inside ICU messages when they may be RTL: `<bdi>{name}</bdi>`.

## Numbers in RTL

Arabic/Hebrew text is RTL but numbers are usually LTR (bidirectional). Rely on `Intl.NumberFormat(locale)` and the browser's bidi algorithm; don't manually reverse digit strings. Test phone numbers, prices, and dates rendered inside RTL sentences.

## Design constraints

- Assume strings grow ~30% (German is the classic stress case). Avoid fixed-width buttons; allow wrapping; don't truncate critical text.
- Test forms in RTL: labels, placeholders, validation messages, and input alignment.
- Check animations and `translateX` transforms under RTL — mirror where direction conveys meaning.
- Screenshot every screen in an RTL locale as part of QA; logical-property bugs are invisible in LTR.
