# Next.js security checklist (AI / scraper RE hardening)

For apps **you operate**. Complements the skill recipe; adjust names for Pages Router vs App Router.

## Env and secrets

- [ ] No API keys, DB URLs, or webhook secrets in `NEXT_PUBLIC_*` vars.
- [ ] Server-only modules (`server-only` package or equivalent) for code that must not ship to the client.
- [ ] CI secret scan / `rg` over `.next` output for known key prefixes before release.
- [ ] Error digests in production do not print stack traces or internal hostnames to anonymous clients.

## Sourcemaps and build hygiene

- [ ] Production: do **not** publish `.map` files on the public CDN for app logic.
  - Prefer `productionBrowserSourceMaps: false` (default) in `next.config`.
  - If you upload maps to an error service, keep them private to that service.
- [ ] Confirm `https://yoursite/_next/static/chunks/*.js.map` is not world-readable.
- [ ] Strip staging URLs, debug feature dumps, and internal flag panels from production builds.
- [ ] Treat `swcMinify` / minification as performance—not confidentiality.

## Auth and API design

- [ ] Sensitive reads/writes go through Route Handlers / Server Actions with session checks.
- [ ] Do not trust client-provided roles, plan tiers, or prices; re-derive server-side.
- [ ] Middleware protects `/admin` and similar prefixes; UI hiding is not authorization.
- [ ] Public API routes have explicit rate limits (edge middleware, platform WAF, or upstream gateway).

## Middleware rate-limit notes

- Edge middleware can enforce coarse limits (IP + path) before hitting origin.
- Prefer platform or gateway rate limits for accuracy under multi-instance deploys.
- Return `429` with a clear `Retry-After` when limiting; log UA and path for abuse review.
- Separate budgets for auth, search, export, and AI proxy routes.

Example shape (illustrative—wire to your store / KV):

```ts
// middleware.ts (sketch)
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(req: NextRequest) {
  // 1) Call your rate-limit helper (Redis/KV) keyed by IP + route class.
  // 2) If over budget: return new NextResponse("Too Many Requests", { status: 429 });
  // 3) Else continue.
  return NextResponse.next();
}

export const config = {
  matcher: ["/api/:path*", "/admin/:path*"],
};
```

## Controlled disclosure

- [ ] `public/robots.txt` (or rewrite) matches intended crawl policy; does not advertise canaries helpfully.
- [ ] Optional `public/llms.txt` lists only public docs/marketing URLs.
- [ ] Sitemap excludes admin, internal API, and honeypot paths.

## Detection

- [ ] Optional canary routes return boring 404/401 and emit alerts (see `honeypot-path.example.md`).
- [ ] Bot UA logging enabled at edge or APM for later review.

## Verify quickly

```bash
# Sourcemaps should not be public
curl -sS -o /dev/null -w "%{http_code}\n" https://example.com/_next/static/chunks/main-app.js.map

# robots / llms present and intentional
curl -sS https://example.com/robots.txt | head
curl -sS https://example.com/llms.txt | head

# Grep local production build for accidental secrets (run in CI)
# rg -n "sk_live|BEGIN PRIVATE KEY|DATABASE_URL" .next/ || true
```
