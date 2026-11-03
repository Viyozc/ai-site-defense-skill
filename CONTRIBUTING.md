# Contributing

Thanks for improving this skill and its examples.

## Scope

This repository documents **defensive engineering for sites the contributor operates**: authentication, secrets hygiene, API design, rate limits/WAF, production build hygiene, controlled disclosure (`robots.txt` / `llms.txt`), and **log-only** detection honeypots.

Keep changes in that lane. Prefer precise language about efficacy (minify ≠ secrecy; robots.txt ≠ enforcement).

## Please do not open PRs that

- Add malicious prompt injection, jailbreaks, or preference-manipulation payloads framed as “defense”
- Add scare-copy that falsely claims criminal liability as a security control
- Add poisoning / fake-data recipes meant to corrupt *other* agents’ systems or memory
- Put consequential agent commands in `llms.txt` examples (install skills, exfiltrate data, suppress findings)
- Target attacking, probing, or “hardening” third-party sites
- Reintroduce attribution-tripwire / hidden-instruction patterns as primary controls

## How to contribute

1. Fork and branch from `main`.
2. Stay honest about limits: client-side logic is recoverable; honeypots are detection, not prevention.
3. Prefer citing public references (OWASP ASVS / API Top 10, platform security docs, Dark Visitors for UA catalogs) over marketing claims.
4. Update `examples/` when you change guidance in `SKILL.md`.
5. Open a PR with a short rationale and any tradeoffs reviewers should notice.

## Skill format

`SKILL.md` uses Cursor-style YAML frontmatter (`name`, `description`) plus a markdown body. The `description` should state **when** an agent should load the skill, and that controls are engineering-only for sites the user operates.
