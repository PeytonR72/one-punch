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

## Open

- **Monetization (new idea, round 3):** one free Sonnet 5.5 build for a new user's first build, then BYOK or a monthly paid plan to use models through us. Sub-questions: trial abuse limits, plan shape (credits vs flat builds), which provider gateway we use for hosted builds, whether BYOK stays free, whether paid ships in MVP, payments provider.
- Later: MVP scope & launch order, who writes challenge briefs, Vercel function time limits for long generations, profiles & sharing, visual style.

Reference cost (Anthropic list prices, 2026-09): Sonnet 5.5 $2/$10 per MTok in/out; Opus 5.5 $4/$20; Fable 5.1 $10/$50. A one-shot of ~3k input + 20–30k output (thinking counts as output) ≈ $0.20–0.35 on Sonnet 5.5, ≈ $0.40–0.65 on Opus 5.5, ≈ $1.00–1.50 on Fable 5.1.
