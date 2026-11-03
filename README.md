# AI site defense skill

A Cursor / Grok-Bot style **skill** (`SKILL.md`) for **engineering hardening** of websites **you operate** against AI agents that scrape or reverse-engineer product logic.

Focus: auth, secrets hygiene, API design, rate limits/WAF, production build hygiene, controlled disclosure (`robots.txt` / `llms.txt`), and **log-only** detection honeypots.

This is **not** a copyright/attribution-tripwire kit, **not** a guarantee of un-reverse-engineerability, and **not** tooling for attacking third-party sites or poisoning other agents.

## Ethics boundary (read this)

| In scope | Out of scope / forbidden |
|----------|---------------------------|
| Harden sites **you** control | Attack or probe third-party sites |
| Auth, secrets, API authz, rate limits, WAF | Malicious prompt injection into visitors/agents |
| Build hygiene (no public sourcemaps with private logic) | Scare “illegal” copy as a “control” |
| Controlled public maps via robots/llms.txt | Preference manipulation / competitor smear injections |
| Log-only canary paths for detection | Poisoning/fake data to corrupt *other* agents |
| Honest limits of minify/obfuscation | Jailbreaks / weaponized indirect injection |

**Honesty:** minification and obfuscation slow casual inspection. If logic or secrets are client-side, determined reverse-engineering still works.

## What’s in the box

| Path | Purpose |
|------|---------|
| [`SKILL.md`](./SKILL.md) | Installable skill (YAML frontmatter + layered recipe) |
| [`examples/robots.txt.example`](./examples/robots.txt.example) | Controlled disclosure crawl preferences |
| [`examples/llms.txt.example`](./examples/llms.txt.example) | Public navigation map for agents (no traps) |
| [`examples/honeypot-path.example.md`](./examples/honeypot-path.example.md) | Log-only canary URL guidance |
| [`examples/nextjs-security-checklist.md`](./examples/nextjs-security-checklist.md) | Next.js build/API hardening checklist |
| [`CONTRIBUTING.md`](./CONTRIBUTING.md) | Contribution scope and anti-patterns |

## Install (Cursor / agent skills)

1. Copy `SKILL.md` into your skills directory (or paste the body via your bot’s UpdateSkill / skill-authoring flow).
2. Keep the frontmatter `name` and `description` so the agent knows **when** to load it.
3. Point the agent at this repo’s `examples/` when applying changes to a site you control.

Frontmatter:

```yaml
name: AI site defense
description: Use when hardening a site you control against AI agents scraping or reverse-engineering product logic — engineering controls only (auth, secrets, API design, rate limits/WAF, build hygiene, controlled disclosure, log-only honeypots); not for attacking third-party agents.
```

## Layered recipe (summary)

1. **Inventory** — what an unauthenticated agent can already fetch (routes, bundles, APIs, docs).
2. **Secrets / API** — server-only secrets; authorize every sensitive call; assume hostile clients.
3. **Edge controls** — rate limits, WAF/bot management, logging on expensive or sensitive routes.
4. **Build hygiene** — no public sourcemaps for private logic; env server-only; minify ≠ secrecy.
5. **Controlled disclosure** — `robots.txt` / `llms.txt` advertise only what you intend agents to see.
6. **Detection** — optional log-only honeypot/canary paths (alert, do not weaponize).
7. **Verify** — bundle audit, map check, rate-limit and canary probes; document residual risk.

Full steps, Do/Don't, and limits: [`SKILL.md`](./SKILL.md).

## 中文摘要

本仓库提供面向 **站点所有者** 的 Agent Skill：用**工程加固**降低 AI Agent / 爬虫对**你所运营站点**产品逻辑的逆向与滥用风险。范围包括鉴权、密钥卫生、API 设计、限流/WAF、生产构建卫生（如禁止公开 sourcemap）、通过 `robots.txt` / `llms.txt` 做**受控披露**，以及**仅记日志**的蜜罐/金丝雀路径。

**不在范围：** 对访客或第三方 Agent 的恶意 prompt 注入、恐吓式“违法”文案、偏好操纵、投毒/伪造数据破坏其他系统、越狱，以及对第三方站点的攻击。

请诚实告知：压缩/混淆只能提高随手查看的成本；逻辑或密钥在客户端时，坚定的逆向仍可成功。

## License

[MIT](./LICENSE) © 2026 Viyozc

## Disclaimer

Engineering guidance only. Vendor crawler names, WAF features, and platform build flags change—verify current docs. Nothing here constitutes legal advice.
