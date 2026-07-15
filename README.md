# content-intelligence
AI-owy silnik decyzyjny SEO, który interpretuje dane z Google Search Console i Analytics. Zamiast dashboardów pokazuje werdykt: co się dzieje z treścią, dlaczego zmienia się ruch i co zrobić dalej. Diagnoza przyczyn, priorytet odświeżeń, wykrywanie szans i sezonowości. Next.js 14 + TypeScript, deterministyczne silniki, opcjonalna narracja AI 
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
