# Log-only honeypot / canary paths

Detection aids for a site **you operate**. Goal: notice automated probing or wholesale scraping early. **Not** a place to inject prompts, malware, or fake data meant to corrupt other agents.

## Principles

1. **Log and alert only** — return a boring `404` or `401`; no special payloads.
2. Paths must be **unlinked** from real UX, sitemaps, and `llms.txt`.
3. Do not put secrets in the canary response body.
4. Document canaries for your own team so pentests and monitors stay sane.
5. Treat hits as signals, not proof of a specific attacker.

## Example path patterns

Pick obscure, stable paths that normal users never hit:

```text
/canary/v1/healthz-internal-do-not-use
/hp/robots-ghost-endpoint
/.well-known/ai-canary/probe
/api/internal/canary/token-check
```

## Implementation sketch (any stack)

1. Register the route to return `404` (or `401` if behind a dummy auth check).
2. Emit a structured log line: timestamp, path, IP, UA, referer, request id.
3. Alert when volume exceeds a tiny baseline (even one hit may be worth a ticket on a quiet site).
4. Optionally set a unique canary string in a **non-indexed** asset you control; watch for that string appearing in third-party dumps or paste sites (leak detection)—still no poisoning of others.

## What not to do

- Do not serve HTML that tries to jailbreak or redirect a visiting agent.
- Do not return deliberately corrupt JSON to “poison” scrapers’ downstream systems.
- Do not advertise canaries in `robots.txt` with cute comments that teach attackers your scheme (Disallow without explanation is enough if you list them at all; many operators omit them entirely and rely on auth + monitoring).
- Do not use honeypots on sites you do not own.

## Verify

```bash
curl -sS -o /dev/null -w "%{http_code}\n" https://example.com/canary/v1/healthz-internal-do-not-use
# Expect 404 (or 401). Confirm a log/alert fired for your test request id.
```
