# Content Intelligence

AI-powered Search Console & Analytics interpretation platform.

Content Intelligence is an intelligent SEO decision engine that transforms raw Google Search Console and Google Analytics data into clear, actionable recommendations. Instead of showing dashboards full of numbers, it explains what is actually happening to your content, why traffic changes occur, and what actions will produce the highest SEO impact.

> The goal is not more reports. The goal is knowing exactly what to do next.

## Core features

| Feature | Where it lives |
|---|---|
| **Search Console Intelligence** — trends in clicks, impressions, CTR and positions, explained in plain language | `lib/gsc/client.ts`, `lib/engines/diagnosis.ts` |
| **Opportunity Finder** — queries at positions 6–15 with real impressions and weak CTR, ranked by traffic potential | `lib/engines/opportunity.ts`, `/opportunities` |
| **Content Health Monitor** — every article classified as Healthy / Monitor / Needs Refresh / Critical | `lib/engines/health.ts`, `/health` |
| **AI Diagnosis Engine** — identifies *causes* (seasonality, CTR decline, ranking loss, SERP features / AI Overview, competitor pressure, technical), each with a confidence level | `lib/engines/diagnosis.ts`, `POST /api/diagnose` |
| **Refresh Priority Engine** — one 0–100 score answering "what do I update first?", with documented weights | `lib/engines/refresh-priority.ts` |
| **CTR Intelligence** — expected-CTR curve by position; pages with visibility but no clicks are flagged for title/meta work | `lib/ctr-model.ts` |
| **Seasonal Intelligence** — year-over-year comparison; seasonal drops are explicitly excluded from action lists ("Zostaw w spokoju") | `lib/engines/seasonal.ts` |
| **Content Lifecycle** — New → Growing → Established → Peak → Declining → Needs Refresh → Archived; recommendations adapt to the stage | `lib/engines/lifecycle.ts` |
| **Weekly SEO Briefing** — deterministic facts + AI prose; wire to a Monday cron | `lib/briefing.ts`, `GET /api/briefing`, `/briefing` |
| **Executive Dashboard** — opens with the **Verdict**: a written editorial note, not a wall of metrics | `app/page.tsx`, `lib/verdict.ts` |

## Architecture

```
lib/
  types.ts              domain types — the only contract between layers
  window-stats.ts       28-day window aggregation, deltas, sparkline buckets
  ctr-model.ts          expected CTR by position + CTR gap
  engines/              pure, deterministic, dependency-free functions
    seasonal.ts         YoY demand analysis
    health.ts           0–100 score with named degradation signals
    lifecycle.ts        stage = f(age, trend, distance from peak)
    diagnosis.ts        rule cascade: cause + confidence + action
    opportunity.ts      page-one candidates ranked by potential clicks
    refresh-priority.ts weighted priority score (weights documented in code)
  gsc/client.ts         Search Console REST wrapper (no SDK) + demo fallback
  demo/data.ts          seeded, deterministic dataset covering every diagnosis path
  analyze.ts            orchestrator: the only place engines meet I/O
  briefing.ts           weekly briefing skeleton
  anthropic.ts          minimal Messages API client (BYOK)
  verdict.ts            composes the dashboard's opening sentences
app/
  page.tsx              Executive Dashboard (Verdict → priorities → leave alone)
  opportunities/        Opportunity Finder table
  health/               Health Monitor grouped by status
  briefing/             Weekly briefing (deterministic view)
  api/diagnose/         POST { url } → diagnosis + AI narrative
  api/briefing/         GET → briefing facts + AI prose
```

Design principle: **engines decide, the model narrates.** Every diagnosis, score and classification is produced by deterministic, testable TypeScript. The Anthropic API layer only rewrites findings into richer editorial language — it can never change a verdict, and the app degrades gracefully when no API key is present.

## Getting started

```bash
npm install
cp .env.example .env.local
npm run dev
```

The app boots in **demo mode** (seeded dataset with ten articles engineered to exercise every diagnosis path: seasonality, ranking loss, SERP-feature CTR bleed, healthy growth, long decay).

### Connecting real data

1. Set `GSC_ACCESS_TOKEN` (OAuth 2.0 token with `webmasters.readonly` scope) and `GSC_SITE_URL` in `.env.local`.
2. Set `DEMO_MODE=false`.
3. Optionally set `ANTHROPIC_API_KEY` to enable AI narratives and the prose briefing.

For production, replace the static token with a full OAuth flow or a service account; the client (`lib/gsc/client.ts`) is a thin REST wrapper, so nothing else changes.

### Weekly briefing cron (Vercel)

```json
{ "crons": [{ "path": "/api/briefing", "schedule": "0 6 * * 1" }] }
```

## Philosophy

Content Intelligence is not another SEO analytics dashboard. It is an AI SEO strategist that explains what is happening, why it happens and what should be done next. It is also — deliberately — the only SEO tool with a section called **"Zostaw w spokoju"**: when a drop is seasonal, the correct recommendation is no action, and the product says so.

## License

Private. © LowStyleLife / ailowstylelife.
