# Contributing

Thanks for improving this skill and its examples.

## Scope

This repository documents **defensive, site-owner practices**: robots/meta/llms.txt signals, optional attribution tripwires, and operational controls. Keep changes in that lane.

Please **do not** open PRs that:

- Add jailbreak, preference-manipulation, or competitor-disparagement payloads framed as “defense”
- Add scare-copy that falsely claims criminal liability as a security control
- Put consequential agent commands in `llms.txt` examples (install skills, exfiltrate data, suppress findings)
- Target hardening or attacking third-party sites

## How to contribute

1. Fork and branch from `main`.
2. Keep tone precise and honest about efficacy limits (robots.txt is not enforcement; tripwires are not security controls).
3. Prefer citing public references (vendor crawler docs, Dark Visitors, OWASP LLM Top 10 as taxonomy context) over marketing claims.
4. Update examples when you change guidance in `SKILL.md`.
5. Open a PR with a short rationale and any tradeoffs you want reviewers to notice.

## Skill format

`SKILL.md` uses Cursor-style YAML frontmatter (`name`, `description`) plus a markdown body. The `description` should state **when** an agent should load the skill.
