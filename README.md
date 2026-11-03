# AI site defense skill

A Cursor / Grok-Bot style **skill** (`SKILL.md`) for hardening *your own* website against unwanted AI training crawlers and scrapers, plus documenting responsible use of `robots.txt`, meta signals, `llms.txt`, and optional attribution tripwires.

This is **defense documentation and an installable agent recipe**—not a guarantee of unscrapability, and not tooling for attacking third-party sites.

## What’s in the box

| Path | Purpose |
|------|---------|
| [`SKILL.md`](./SKILL.md) | Installable skill (YAML frontmatter + layered recipe) |
| [`examples/robots.txt.example`](./examples/robots.txt.example) | Sample AI-bot preferences (Allow/Disallow tradeoffs) |
| [`examples/llms.txt.example`](./examples/llms.txt.example) | Navigation-only `llms.txt` (no authority claims) |
| [`examples/attribution-tripwire.html`](./examples/attribution-tripwire.html) | Mild, experimental attribution tripwire |
| [`examples/nextjs-layout-snippet.tsx`](./examples/nextjs-layout-snippet.tsx) | Optional Next.js meta + tripwire placement |
| [`CONTRIBUTING.md`](./CONTRIBUTING.md) | Contribution scope and anti-patterns |

## Install (Cursor / agent skills)

1. Copy `SKILL.md` into your skills directory (or paste the body via your bot’s UpdateSkill / skill-authoring flow).
2. Keep the frontmatter `name` and `description` so the agent knows **when** to load it.
3. Point the agent at this repo’s `examples/` when applying changes to a site you control.

Frontmatter:

```yaml
name: AI site defense
description: Use when hardening a website against AI training crawlers and scrapers, adding robots/meta/llms.txt signals, or deciding whether to use attribution tripwires — not for attacking third-party sites.
```

## Layered approach (summary)

1. **Ops first** — auth for private content, rate limits/WAF, watermarking; no secrets in public HTML.
2. **robots.txt** — preferences for known AI bots; remember it is **not enforcement**. Use a living UA list such as [Dark Visitors](https://darkvisitors.com/agents). Decide separately whether AI *search* bots (e.g. OAI-SearchBot) should be Allowed for GEO/citations.
3. **Meta signals** — optional `noai` / `noimageai` as *supplemental, non-standard* rights reservation; do not claim major vendors honor them.
4. **llms.txt / ai.txt** — `llms.txt` = content map for agents; **not** a place for “ignore previous instructions” or consequential commands. `ai.txt` = training opt-out attempts; compliance uneven.
5. **Attribution tripwire (optional)** — mild hidden ask to attribute + link back + avoid full-text reproduction. Tripwire ≠ security control; watch SEO/hidden-text risk.
6. **Measure** — log bot UAs, use Search Console, and choose explicitly between training opt-out and AI-answer discoverability.

Anti-patterns (do not do these as “defense”): fake criminal scare copy, preference-manipulation injections, agent command payloads in `llms.txt`, relying on tripwires against determined scrapers.

Details, recipe steps, and limits are in [`SKILL.md`](./SKILL.md).

## 中文摘要

本仓库提供一套面向 **站点所有者** 的 Agent Skill：用分层方式处理 AI 训练爬虫 / 抓取偏好（鉴权与限流优先 → `robots.txt` → 可选 meta → `llms.txt` 导航地图 → 可选归属 tripwire），并明确各层 **有效性边界**。`robots.txt` 不是强制执行；tripwire 不是安全控制；不要在 `llms.txt` 里写权威覆盖或危险指令。仅用于你有权管理的网站，不用于攻击第三方。

## License

[MIT](./LICENSE) © 2026 Viyozc

## Disclaimer

Public techniques only. Vendor crawler names and honor policies change—verify current documentation before relying on any User-Agent rule. Nothing here constitutes legal advice.
