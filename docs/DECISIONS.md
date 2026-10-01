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

## Open

See latest grilling round: voting mechanic & ratings, what gets ranked, challenge cadence, credential storage, generation runtime/timeouts, system prompt & model params, prompt visibility, moderation, private build history, MVP scope.
