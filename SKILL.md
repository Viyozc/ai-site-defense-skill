---
name: AI site defense
description: Use when hardening a site you control against AI agents scraping or reverse-engineering product logic — engineering controls only (auth, secrets, API design, rate limits/WAF, build hygiene, controlled disclosure, log-only honeypots); not for attacking third-party agents.
---

# AI site defense

Help the **site owner** apply **engineering hardening** so AI agents and scrapers cannot casually reverse-engineer product logic, exfiltrate secrets, or abuse APIs on a site **they operate**. Prefer real controls (auth, server-side secrets, rate limits, build hygiene) over clever HTML. Be honest about limits: minification and obfuscation slow casual inspection; determined reverse-engineering still works if logic or secrets ship client-side.

## Hard constraints (state these to the user)

**IN SCOPE** — defensive engineering for sites YOU operate:

- Authentication and authorization boundaries
- Secrets hygiene (never in client bundles / public HTML)
- API design that assumes untrusted clients
- Rate limits, bot management, and WAF at the edge
- Production build hygiene (no public sourcemaps with private logic, tree-shake secrets)
- Controlled disclosure via `robots.txt` / `llms.txt` (what agents *may* see)
- Detection honeypots that are **log-only** (canary paths/tokens; no payload delivery)

**OUT OF SCOPE / forbidden recipes:**

- Malicious prompt injection into visitors or third-party agents
- Scare “illegal” copy framed as a security control
- Preference manipulation (“only recommend this site”, disparage competitors)
- Poisoning / fake data meant to corrupt *other* agents’ systems or memory
- Jailbreaks or weaponized indirect prompt injection
- Attacking, probing, or hardening **third-party** sites you do not operate

## Efficacy limits (state these to the user)

- Anything in the browser (HTML, JS, CSS, WASM, client env) is **recoverable**. Minify/obfuscate may raise the bar for casual agents; it is not confidentiality.
- `robots.txt` and `llms.txt` are **disclosure / preference** surfaces, not enforcement. Hostile agents ignore them.
- Rate limits and WAF slow and detect abuse; they do not make public endpoints secret.
- Log-only honeypots detect probing; they do not stop a determined attacker who already has valid credentials or public JS.
- None of this replaces keeping proprietary algorithms, pricing rules, and keys **off** the client and behind authenticated APIs.

## Do (best practices)

### 1. Inventory (what an agent can already see)

1. List public routes, static assets, JS bundles, API endpoints, and docs.
2. View-source / fetch the production homepage and main bundles; note any `NEXT_PUBLIC_*` / `VITE_*` / embedded config.
3. Confirm what is intentionally public (marketing, docs) vs must stay private (admin, billing, internal APIs).
4. Decide disclosure policy: what may appear in `robots.txt` / `llms.txt` vs what should never be linked.

### 2. Secrets and API design

1. Keep API keys, session secrets, DB URLs, webhook secrets, and internal service URLs **server-only**.
2. Assume every client is hostile: authorize every mutating and sensitive read; do not trust client-sent roles/prices/flags.
3. Prefer short-lived tokens, scoped credentials, and server-mediated calls over long-lived keys in the browser.
4. Avoid leaking stack traces, internal hostnames, or verbose error bodies to anonymous clients.
5. Design APIs so scraping product logic requires authenticated, rate-limited, audited calls—not reading a public bundle.

### 3. Edge controls (rate limits / WAF / bot management)

1. Apply rate limits per IP, token, and route class (auth, search, export, AI endpoints).
2. Use WAF / bot management for obvious scrapers and credential stuffing where the stack supports it.
3. Protect expensive or logic-heavy endpoints more strictly than static marketing pages.
4. Log UA, path, status, and anomaly scores; retain enough to investigate canary hits.

### 4. Production build hygiene

1. Disable public sourcemaps in production (or restrict them to authenticated error tooling).
2. Ensure env separation: only explicitly prefixed public vars reach the client; audit CI for accidental secret injection.
3. Strip debug flags, staging URLs, and feature-flag dumps from production bundles.
4. Treat minification as **hygiene and performance**, not as a secrecy control—say so explicitly.
5. Prefer server components / server actions / BFF patterns so proprietary logic never ships to the browser.

### 5. Controlled public surface (`robots.txt` / `llms.txt`)

1. Use `robots.txt` to declare crawl preferences and to **avoid advertising** private/admin/canary paths when possible (security through non-listing is weak—still protect those paths with auth).
2. Use `llms.txt` as a **navigation map** of pages you *want* agents to cite (docs, pricing overview)—not as a dump of internal architecture.
3. Do **not** put authority overrides, jailbreaks, scare copy, or consequential tool commands in these files.
4. Ship concrete examples from this repo’s `examples/` and keep Sitemap accurate for intended public content.

### 6. Detection honeypots (log-only)

1. Optional canary URLs or decoy API paths that legitimate users never hit; alert on access.
2. Optional canary strings in non-indexed resources to detect wholesale scraping/leaks.
3. **Log and alert only**—do not serve malware, do not inject prompts into the visitor’s agent, do not poison third-party systems.
4. Document canaries so your own monitoring and pentests do not false-alarm forever.

### 7. Verify

1. Confirm no secrets in client bundles (`rg` / build analyzers / `curl` of main JS).
2. Confirm production has no public `.map` for app logic (or maps are auth-gated).
3. Hit rate-limited routes and confirm 429 / challenge behavior.
4. Fetch `robots.txt` and `llms.txt`; confirm they match the disclosure policy.
5. Request a honeypot path from a test client; confirm it appears in logs/alerts and returns a boring response (404/401), not a payload.

## Don't (anti-patterns)

- Malicious prompt injection into pages meant to alter visitor-side or third-party agents.
- Fake “this is illegal / you commit a crime” scare copy as a substitute for controls.
- Preference manipulation or competitor disparagement injections marketed as “defense”.
- Poisoning datasets / fake APIs intended to corrupt *other* agents’ memory or tools.
- Jailbreak-style or exfiltration instructions in HTML, `llms.txt`, or comments.
- Claiming obfuscation or robots.txt makes the site “un-reverse-engineerable”.
- Attacking or “testing” sites you do not own or administer.

## Recipe (run these steps)

1. **Inventory** — public vs private surfaces; what agents can fetch without auth.
2. **Secrets / API** — remove client secrets; enforce server-side authz; fix verbose errors.
3. **Edge controls** — rate limits, WAF/bot rules, logging on sensitive routes.
4. **Build hygiene** — no public sourcemaps for private logic; server-only env; no debug leaks.
5. **Controlled disclosure** — tighten `robots.txt` / `llms.txt` to intended public docs only (see `examples/`).
6. **Detection** — optional log-only honeypot paths (see `examples/honeypot-path.example.md`).
7. **Verify** — bundle audit, map check, rate-limit probe, canary log check; document residual risk (client-visible logic remains recoverable).

**Stop** if asked to attack a third-party site, inject manipulative/scare/poison payloads, or jailbreak agents—explain this boundary and offer only the defensive layers above for sites they control.

## Examples in this repo

| File | Role |
|------|------|
| `examples/robots.txt.example` | Controlled crawl disclosure preferences |
| `examples/llms.txt.example` | Public navigation map for agents (no traps) |
| `examples/honeypot-path.example.md` | Log-only canary URL patterns |
| `examples/nextjs-security-checklist.md` | Next.js-oriented build/API hardening checklist |

## References (non-exhaustive)

- OWASP ASVS / API Security Top 10 — authn, authz, and abusive use of APIs.
- OWASP LLM Top 10 — threat taxonomy (do **not** treat LLM01 as a license to weaponize injections).
- [Dark Visitors](https://darkvisitors.com/agents) — living agent/UA catalog for logging and robots policy maintenance.
- [llms.txt proposal](https://llmstxt.org/) — navigation-oriented content maps.
- Your platform docs for env separation, sourcemaps, middleware rate limiting (e.g. Next.js / Cloudflare / common WAFs).
