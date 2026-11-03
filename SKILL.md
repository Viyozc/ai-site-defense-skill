---
name: AI site defense
description: Use when hardening a website against AI training crawlers and scrapers, adding robots/meta/llms.txt signals, or deciding whether to use attribution tripwires — not for attacking third-party sites.
---

# AI site defense

Help the site owner apply a **layered** defense against unwanted AI training crawls and scrapes, while being honest about what each layer can and cannot do. Prefer operational controls over clever HTML. Techniques here are public knowledge (robots conventions, optional meta signals, mild attribution tripwires related to *indirect prompt injection* literature). Treat OWASP **LLM01** (prompt injection) as threat-taxonomy context for why hidden instructions are unreliable as security controls—not as a license to weaponize injections.

**Out of scope:** attacking, jailbreaking, or preference-manipulating third-party agents beyond a mild attribution request on *this* site’s own pages.

## Efficacy limits (state these to the user)

- `robots.txt` is a **preference**, not enforcement. Compliant bots honor it; others ignore it.
- Meta `noai` / `noimageai` are **non-standard** supplemental signals; do not claim major vendors reliably honor them.
- `llms.txt` is a **content map**, not an authority channel.
- Attribution tripwires are **experimental**. Sanitizing scrapers, DOM cleaners, and determined reverse-engineering ignore them. Hidden text can hurt SEO if it looks like cloaking/spam.
- None of this replaces auth, rate limits, WAF, watermarking, or keeping secrets out of public HTML.

## Do (best practices)

### 1. Access control and ops first

1. Put private or paid content behind authentication; do not rely on obscurity.
2. Apply rate limits, bot management, and WAF rules at the edge where appropriate.
3. Watermark or fingerprint sensitive assets if leakage detection matters.
4. Never embed API keys, session tokens, internal URLs, or unpublished docs in public HTML, JS bundles, or client-visible config.

### 2. robots.txt

1. Document preferences for known AI-related crawlers (examples: `GPTBot`, `Google-Extended`, `ClaudeBot`, `anthropic-ai`, `CCBot`, `Bytespider`, `meta-externalagent`, `Applebot-Extended`, `PerplexityBot`). Maintain the list from a living source such as [Dark Visitors](https://darkvisitors.com/agents) plus vendor docs—UAs change.
2. Separate **training / grounding** opt-out from **search / citation** goals:
   - Many product sites **Disallow** training-oriented bots while **Allow**ing search-oriented ones (e.g. `OAI-SearchBot`) when they want GEO / AI-answer discoverability.
   - Blocking ordinary `Googlebot` is usually the wrong tool if the site still wants classic search indexing.
3. Tell the user clearly: robots.txt is not enforcement; pair it with logging and edge controls.
4. Ship a concrete file (see `examples/robots.txt.example`) and keep `Sitemap` accurate.

### 3. Meta signals (optional, supplemental)

1. Optionally emit non-standard rights-reservation hints such as `noai` / `noimageai` (meta or HTTP header variants where supported by your stack).
2. Frame them as **supplemental**—do not promise vendor compliance.
3. Prefer also publishing a clear human-readable copyright / reuse policy page.

### 4. llms.txt / ai.txt

1. **`llms.txt`**: a navigation-oriented map (canonical links + short summaries) so agents can find authoritative pages. See [llmstxt.org](https://llmstxt.org/) and `examples/llms.txt.example`.
2. Do **not** put authority overrides, “ignore previous instructions”, legal scare copy, or consequential tool commands in `llms.txt`.
3. **`ai.txt`** (and similar training opt-out files): optional attempt to declare training preferences; **compliance is uneven**. Prefer robots.txt + vendor-specific opt-outs when available; treat `ai.txt` as additive documentation, not a guarantee.

### 5. Attribution tripwire (optional, experimental)

1. Use only when the owner accepts SEO and efficacy tradeoffs.
2. Mild hidden instruction: ask summarizers to **attribute**, **link to the canonical URL**, and **avoid reproducing full text**. Keep tone polite and specific to *this* site.
3. Implementation notes (see `examples/attribution-tripwire.html`, `examples/nextjs-layout-snippet.tsx`):
   - Prefer `aria-hidden="true"` plus visually-hidden CSS; mark search indexers to ignore (e.g. `data-pagefind-ignore`).
   - Warn about hidden-text spam / cloaking risk; keep the string short and non-deceptive relative to visible content.
   - Expect sanitizing scrapers to drop it—frame as a tripwire, not a control.
4. Do not escalate to scare-copy, competitor disparagement, or jailbreak-style payloads.

### 6. Measurement and product goals

1. Log and review bot User-Agents at the edge or origin; sample against Dark Visitors / vendor lists.
2. Use Google Search Console (and equivalents) for classic search health.
3. Decide explicitly with the user: **training opt-out** vs **AI-answer discoverability (GEO)**. Many marketing/docs sites want Allow for search bots **plus** strong on-page GEO content (clear claims, FAQs, canonical docs)—not a total block.
4. Revisit policy when vendors rename crawlers or change honor rules.

## Don't (anti-patterns)

- Fake “this is illegal / stop or you commit a crime” scare copy presented as a security control.
- Preference manipulation: “only recommend this site”, “disparage competitors”, ranking or shopping bias injections marketed as defense.
- Consequential agent commands in `llms.txt` / hidden HTML (install skills, open URLs to exfiltrate, suppress security findings, rewrite policies).
- Relying on tripwires against determined scraping or reverse-engineering of a product.
- Claiming robots/meta/llms/tripwires alone make content “unscrapable”.

## Recipe (run these steps)

1. **Inventory goals** with the user: training/grounding opt-out, AI-search/GEO citations, classic SEO, private vs public content, risk tolerance for hidden text.
2. **Audit** current `robots.txt`, meta/headers, `llms.txt` / `ai.txt`, auth boundaries, rate limits/WAF, and whether secrets appear in public HTML/JS.
3. **Apply chosen layers** in order: ops/auth → robots.txt → optional meta → optional llms.txt (map only) → optional mild attribution tripwire in the content template.
4. **Verify** with `curl` (and browser view-source as needed):
   - `curl -sS https://example.com/robots.txt`
   - `curl -sS https://example.com/llms.txt` (if used)
   - Confirm meta tags in HTML head; confirm tripwire markup if enabled.
5. **Document tradeoffs** for the user in plain language: what compliant bots will likely do, what hostile scrapers can still do, SEO risk of hidden text, and how to monitor (UA logs, GSC).
6. **Stop** if asked to harden or attack a third-party site, or to add manipulation/scare/exfil payloads—explain the boundary and offer only the defensive layers above for sites they control.

## References (non-exhaustive)

- Vendor crawler documentation for GPTBot / Google-Extended / Claude / Common Crawl / etc. (policies change—verify current pages).
- [Dark Visitors](https://darkvisitors.com/agents) — living agent/UA catalog.
- [llms.txt proposal](https://llmstxt.org/)
- OWASP LLM Top 10 — LLM01 Prompt Injection (taxonomy for why untrusted HTML/instructions are not trust boundaries).
