# One Punch — design decisions

Running log from the design grilling sessions. "Settled" items are agreed with the user; "Open" items are still being decided — don't build against them yet.

## Settled

1. **Generation runs on our server with the user's credentials.** Users connect OpenRouter (OAuth PKCE, user pays per token) or paste a direct provider API key (Anthropic / OpenAI / Google). Our backend makes exactly one call with our fixed system prompt + their prompt, so model, prompt, and one-shot-ness are verified. No paste-in of externally generated HTML. Consumer chat subscriptions (Claude Pro/Max, ChatGPT Plus) can't be used — providers don't permit third-party apps to use them.
2. **Prompt limit: 1000 characters** (trial; may tighten later).
3. **Artifact = one self-contained HTML file** (inline CSS/JS), optionally loading scripts from a small CDN allowlist (e.g. three.js, p5, Phaser, Tailwind CDN). No other network access. Multi-file projects possibly later.
4. **Build, then submit or scrap.** Users can generate as many times as they like privately; each build can be submitted to the arena or scrapped. No attempt counter is shown publicly.
5. **Both freeform and challenges**, launching with challenges first (shared brief, like-vs-like matchups). Freeform arena follows.
6. **Leagues:** Ranked leagues keyed by exact model ID (e.g. `claude-sonnet-5-5`), plus an Open league where any model faces any model (doubles as a model leaderboard).
7. **Two categories at launch:** Games and Websites. Maybe Toys/Visuals later.
8. **Accounts required** to submit and to vote (GitHub + Google sign-in). Author/model hidden until after a vote.
9. **Stack:** Next.js (App Router, TypeScript) on Vercel; Supabase (Postgres, Auth, Storage). Artifacts served from a separate user-content domain in a sandboxed iframe. Supabase org currently has 7 projects, all paused, so a new free-tier project fits under the 2-active-project limit.

10. **Voting:** blind pairwise A/B (same challenge + league), options A / B / tie / both bad. Glicko-2 ratings.
11. **Leaderboards:** entries (per challenge + league), players (season points from placements), models (from Open league).
12. **Challenges:** weekly. Submissions open 7 days; voting runs during and 3 days after; then standings freeze. One entry per user per challenge per league, swappable until submissions close.
13. **Credentials:** stored encrypted at rest server-side, user can disconnect any time. (May shift with the hosted-plan idea — see Open.)
14. **Model params fixed for ranked:** provider default temperature, ~32k max output, model's default reasoning. System prompt is public.
15. **Prompts hidden during an active challenge**, public after it closes.
16. **Private build history** kept (prompt, model, HTML, cost); reopen/submit/delete any time.
17. **Moderation:** report button, auto-hide after N distinct reports, admin review queue. No auto-classifier at launch.

18. **Free trial:** each new account gets one free Sonnet 5.5 build. Abuse control = one per account + a global daily spend cap on free builds (~$10/day; "free builds are out for today" when hit). Add GitHub-account-age or phone checks only if abuse appears. Free builds can enter the ranked Sonnet 5.5 league.
19. **Paid plan (credits):** monthly subscription buys credits; cost per build scales with model (Sonnet 1, Opus 2, Fable 5). Starting point ~$10–12/mo for 25 credits; credits expire monthly. Stripe Checkout + Customer Portal. One-off top-up packs later.
20. **Hosted builds go through Vercel AI Gateway** (one key, many providers, billed via Vercel). BYOK users keep OpenRouter OAuth + direct provider keys. AI Gateway also accepts per-request BYOK credentials (`providerOptions.gateway.byok`), so one AI SDK code path can serve both.
21. **BYOK is free forever.** The paid plan is convenience, not a toll.
22. **Launch order:** MVP ships with free builds (no payments). Payments are implemented before the site is considered complete.
23. **Launch model menu:** ~5–6 curated models (Claude Sonnet 5.5, Claude Opus 5.5, top GPT and Gemini frontier + mid-tier). Exact IDs verified against the gateway at build time. A model's ranked league opens only once it has enough entries; until then those entries compete in the Open league.

## Open

- Round 4: exact MVP feature list (is BYOK in MVP?), generation runtime vs Vercel limits & plan, challenge briefs, profiles & sharing, live generation UX, visual style.

Vercel facts (2026-10): team "peytonr7272-gmailcom's projects". Vercel's Hobby plan is for non-commercial use, so a Pro upgrade is needed before taking payments. Function `maxDuration` can go up to 1800s on paid plans with Fluid compute; Hobby is much lower.

Reference cost (Anthropic list prices, 2026-09): Sonnet 5.5 $2/$10 per MTok in/out; Opus 5.5 $4/$20; Fable 5.1 $10/$50. A one-shot of ~3k input + 20–30k output (thinking counts as output) ≈ $0.20–0.35 on Sonnet 5.5, ≈ $0.40–0.65 on Opus 5.5, ≈ $1.00–1.50 on Fable 5.1.
