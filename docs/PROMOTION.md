# Promotion & discovery plan

Current state: repo is public, MIT, 20 topics, keyword-optimized README, v1.0.0 release, CI validation workflow, and installable via `npx skills add PraiseTechzw/i18n-localization-skill`. The `skills` CLI detects it as one skill (`i18n-localization`).

## Immediate: open 3 awesome-list PRs

Forks + feature branches are already pushed. Open these while signed in to GitHub, then paste the prepared bodies from `/tmp/opencode/pr-*.md` (or write a short note — each repo's rules are already satisfied by the branch content).

| List | PR URL | Notes |
|---|---|---|
| ComposioHQ/awesome-claude-skills (80k) | https://github.com/ComposioHQ/awesome-claude-skills/compare/master...PraiseTechzw:awesome-claude-skills:add-i18n-localization-skill?expand=1 | Includes in-repo skill folder in their template |
| karanb192/awesome-claude-skills (605) | https://github.com/karanb192/awesome-claude-skills/compare/main...PraiseTechzw:awesome-claude-skills-1:add-i18n-localization?expand=1 | Table row, alphabetical, two columns |
| Ezeafk/awesome-agent-skills (106) | https://github.com/Ezeafk/awesome-agent-skills/compare/main...PraiseTechzw:awesome-agent-skills:add-i18n-localization-skill?expand=1 | Coding row with 9/10 score |

Quick opener (Linux):

```bash
xdg-open 'https://github.com/ComposioHQ/awesome-claude-skills/compare/master...PraiseTechzw:awesome-claude-skills:add-i18n-localization-skill?expand=1'
xdg-open 'https://github.com/karanb192/awesome-claude-skills/compare/main...PraiseTechzw:awesome-claude-skills-1:add-i18n-localization?expand=1'
xdg-open 'https://github.com/Ezeafk/awesome-agent-skills/compare/main...PraiseTechzw:awesome-agent-skills:add-i18n-localization-skill?expand=1'
```

## At 10 stars (or immediately, if rules allow)

- `philipbankier/awesome-agent-skills` — quality bar: 10 stars. Format: `- [name](url) - Short description. ![GitHub stars](...)`. One entry per PR.

## At 25 stars AND 30 days old

- `awesome-opencode/awesome-opencode` (11k) — add `data/projects/i18n-localization-skill.yaml` (not README). Fields: `name`, `repo`, `tagline` (≤120 chars), `description`, optional `min_version`. Must state OpenCode V1/V2 support in the description.
- `VoltAgent/awesome-agent-skills` (37k) — append `- **[PraiseTechzw/i18n-localization-skill](https://github.com/PraiseTechzw/i18n-localization-skill)** - Short description (≤10 words)` to Community Skills → Development and Testing. PR title: `Add skill: PraiseTechzw/i18n-localization-skill`.
- `heilcheng/awesome-agent-skills` (6.4k) — append one line to Community Skills in README: `- [PraiseTechzw/i18n-localization-skill](https://github.com/PraiseTechzw/i18n-localization-skill) - Description under 100 characters`. Their automated checker will comment on the PR; fix findings in the same PR.

## Never (rule conflicts)

- `travisvn/awesome-claude-skills` — explicitly rejects AI-assisted submissions and requires 10 stars. Do not submit.

## Growth levers that actually move GitHub ranking

GitHub search/trending weight: stars > topics > description > README keywords > freshness > forks/issues. You control the last four; stars come from reach.

1. Add a short demo GIF/screenshot of the skill catching a plural bug — visuals drive stars.
2. Write a 3-minute blog post / dev.to article with the "1 messages" example and link back.
3. Share in relevant communities (r/webdev, r/reactjs, Hacker News "Show HN", lodash/claude-code Discord channels).
4. Add a `CONTRIBUTORS` section and respond to every issue quickly — activity signals help.
5. Keep commits flowing: framework recipes, plural edge cases, MessageFormat 2 notes.
