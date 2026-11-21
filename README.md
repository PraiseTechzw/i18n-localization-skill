# i18n-localization-skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/PraiseTechzw/i18n-localization-skill?style=social)](https://github.com/PraiseTechzw/i18n-localization-skill/stargazers)
[![GitHub release](https://img.shields.io/github/v/release/PraiseTechzw/i18n-localization-skill)](https://github.com/PraiseTechzw/i18n-localization-skill/releases)
[![SKILL.md](https://img.shields.io/badge/format-SKILL.md-blue)](SKILL.md)

An **internationalization (i18n) and localization (l10n) skill for AI coding agents**. It teaches your agent to extract every user-facing string, write ICU MessageFormat messages with correct CLDR plural rules, format dates/numbers/currency with the Intl API, support RTL with logical CSS, and verify with pseudo-localization.

Works with **Claude Code, OpenCode, VS Code / GitHub Copilot, Cursor, Gemini CLI, OpenAI Codex** — any agent that reads a `SKILL.md` file.

> i18n · l10n · internationalization · localization · ICU MessageFormat · CLDR plural rules · Intl API · RTL · pseudo-localization · globalization · translation · developer tools · AI coding agent skill

## Why this exists

Most apps bolt translations on at the end, which produces hardcoded strings, broken plurals ("1 messages"), mangled dates, and RTL layouts that fall apart. This skill encodes the rules that prevent those bugs **before** they ship — and it's framework-agnostic, so it applies to React, Next.js, Vue, Svelte, Node, Expo/React Native, and plain web apps.

It was authored to fill a real gap: no general-purpose i18n/l10n skill existed in the agent-skills ecosystem, despite it being one of the most common sources of production bugs.

## When the agent should use it

The description is written to trigger automatically on phrases like:

- "add a language" / "localize this app" / "support Arabic"
- "fix this plural bug" / "wrong translation for Russian/Arabic"
- "audit hardcoded strings" / "find untranslated text"
- "build locale routing" / "set up i18n" / "add hreflang"
- "make this RTL-ready" / "layout breaks in Arabic"

## What it covers

| Area | What the agent learns |
|---|---|
| **String extraction** | No hardcoded UI strings; never concatenate sentences; semantic, linted keys grouped by feature |
| **ICU MessageFormat** | Interpolation, `plural`, `select`, `selectordinal`, nested arguments, `#`, `=0` overrides, `offset:` |
| **CLDR plurals** | `zero / one / two / few / many / other` per locale — English has 2 forms, Arabic 6, Japanese 1 |
| **Intl API** | `DateTimeFormat`, `NumberFormat`, `RelativeTimeFormat`, `ListFormat`, `Collator`; UTC storage, display-time formatting |
| **RTL & layout** | CSS logical properties, icon mirroring, `<bdi>` isolation, numbers in RTL, growth-tolerant design |
| **Verification** | Pseudo-localization, missing-key CI checks, plural edge cases, RTL screenshot review, fallback checks |

## Install

### One-liner (all agents)

```bash
npx -y skills add PraiseTechzw/i18n-localization-skill -g --copy -y
```

This auto-detects your installed agents and installs the skill globally.

### Choose specific agents

```bash
npx -y skills add PraiseTechzw/i18n-localization-skill -s '*' -a '*' -g --copy -y
```

### Manual install

Copy `SKILL.md` and the `references/` folder into your agent's skills directory:

| Agent | Directory |
|---|---|
| OpenCode | `~/.config/opencode/skills/i18n-localization/` |
| Claude Code | `~/.claude/skills/i18n-localization/` |
| VS Code / Copilot | `~/.copilot/skills/i18n-localization/` |
| Gemini CLI / Codex | `~/.agents/skills/i18n-localization/` |

## Quick examples the skill produces

ICU plural message — one key, correct per locale:

```jsonc
{
  "inbox.message_count": "{count, plural, =0 {No messages} one {# message} other {# messages}}"
}
```

Locale-aware formatting — never manual templates:

```js
new Intl.NumberFormat('de-DE', { style: 'currency', currency: 'EUR' }).format(99.99); // 99,99 €
new Intl.DateTimeFormat('ja-JP').format(new Date());                                  // 2026/12/31
new Intl.RelativeTimeFormat('en').format(-3, 'day');                                  // "3 days ago"
```

RTL-safe CSS with logical properties:

```css
.card { margin-inline-start: 16px; text-align: start; border-inline-start: 2px solid blue; }
```

## Repository layout

```
.
├── SKILL.md                          # core instructions + workflow
└── references/
    ├── icu-message-format.md         # full ICU syntax, nesting, MessageFormat 2
    ├── intl-formatting.md            # Intl recipes, locale negotiation, BCP 47
    ├── rtl-and-layout.md             # logical properties, mirroring, <bdi>
    └── checklist.md                  # PR review & QA checklist, pitfalls
```

`SKILL.md` holds the core workflow; the detailed references are loaded only when needed, which keeps the skill token-efficient.

## Contributing

Contributions are welcome — especially new framework recipes, additional plural/RTL edge cases, and translation-tooling notes. See [CONTRIBUTING.md](CONTRIBUTING.md). Please open an issue or PR rather than silent edits.

## License

[MIT](LICENSE) — free for personal and commercial use. Open source, forever.

## Keywords

i18n, l10n, internationalization, localization, globalization, ICU MessageFormat, CLDR, plural rules, Intl API, DateTimeFormat, NumberFormat, RTL, right-to-left, logical CSS, pseudo-localization, hreflang, translation, developer experience, AI agent skill, Claude Code skill, OpenCode skill, Copilot skill, agent skills, SKILL.md
