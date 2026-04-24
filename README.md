# PlateRoute.ai

> An AI meal-planning agent that routes each meal across cooking, delivery, and dining out — in one weekly loop.

PlateRoute.ai generates a personalized 7-day meal plan every week and executes it across Swiggy's MCP platform. Each meal gets routed to the channel that makes sense that day: home-cooked with ingredients auto-ordered via **Instamart**, delivered via **Swiggy Food**, or reserved for dine-in via **Dineout**. Plans adapt to your schedule, macros, and budget — and get better every week.

## Why

Most meal planners assume you'll cook every meal. Most food delivery apps make weekdays expensive and unhealthy. The real unlock of having groceries, restaurants, and reservations in one agent is that a single weekly plan can route each meal to whichever channel fits — cook when you have time, order when you don't.

No standalone meal planner can do this today.

## What a week looks like

| Day | Context | Route |
|-----|---------|-------|
| Mon | WFH, 30 min free | Cook — 3-ingredient stir-fry from Sunday's Instamart order |
| Tue | Gym day, no time | Order — pre-approved chicken bowl hitting 40g protein |
| Wed | Normal evening | Cook — batch dal for three meals |
| Thu | Busy | Leftovers from Wednesday |
| Fri | Date night | Dineout reservation instead of delivery |
| Sat | Weekend | Cook — involved recipe the agent picked |
| Sun | Plan next week | Instamart order based on actual usage |

## How it works

### The routing loop

```
User profile + schedule + targets
              ↓
     Planner service (LLM)
              ↓
   7-day plan with per-meal routing
              ↓
    ┌─────────┼──────────┐
    ↓         ↓          ↓
Instamart  Swiggy    Dineout
(groceries) Food    (reservations)
(cooking)  (delivery)
    ↓         ↓          ↓
    └─────────┴──────────┘
              ↓
     Per-meal feedback
              ↓
    Preference model update
              ↓
      Next week's plan
```

### Core components

**Planner service** — Generates weekly meal plans from user profile (dietary targets, allergies, cooking skill), schedule (WFH days, gym days, late meetings), and budget.

**Pantry state store** — Maintains a running model of what's in your kitchen. Seeded from Instamart order history, decremented by scheduled recipes, reconciled weekly. This is the feature that lets the agent add *only missing ingredients* to the grocery cart — no manual list editing.

**Preference model** — Updates from per-meal feedback (thumbs up/down + "too much/too little"). Over 4–6 weeks it learns your real preferences (not aspirational ones), which recipes you actually execute, and which restaurants reliably hit your macros.

**Execution layer** — Calls Swiggy Food, Instamart, and Dineout MCP tools to place orders and book reservations.

## MCP integration

| Server | Role |
|--------|------|
| **Swiggy Instamart** | Pantry + groceries. Consolidated weekly cart, mid-week top-ups, staples on a subscription-like rhythm. |
| **Swiggy Food** | No-cook days. Caches 5–10 macro-approved orders per cuisine; picks one based on today's remaining calorie and protein budget. |
| **Swiggy Dineout** | Flex / social meals. Books a table when the plan calls for dining out. |

## Stack

- **Backend:** Python (FastAPI) on AWS
- **Database:** Postgres (pantry state, preference model, plan history)
- **Frontend:** Next.js — onboarding, weekly plan review, feedback capture
- **Agent runtime:** MCP client embedded in Claude initially; standalone web app to follow
- **Attribution:** All user-facing surfaces preserve Swiggy / Instamart / Dineout branding

## Hard problems (open)

**Macro accuracy on restaurant dishes.** Swiggy's menu data won't give reliable grams of protein. Plan: curated database of "trusted" dishes per restaurant, seeded from user feedback and LLM-assisted estimation that users can correct. Start with 50 well-characterized dishes, not 5000 vague ones.

**Pantry state drift.** Users don't log what they actually eat. Plan: assume plans were followed, reconcile weekly with one yes/no question per item, allow occasional fridge photos for a vision-model sanity check.

## Roadmap

- [ ] MVP: weekly plan generation, Instamart cart assembly, single-restaurant ordering
- [ ] Pantry reconciliation flow
- [ ] Preference learning loop
- [ ] Dineout integration for flex nights
- [ ] Macro database for top 50 restaurant dishes
- [ ] Standalone web app (currently Claude-embedded)
- [ ] Mid-week replanning when a cook slot is skipped

## Status

Early-stage — applying for Swiggy Builders Club access. This repo will host the MVP once API access is granted.

## Alignment with Swiggy Builders guidelines

PlateRoute.ai is designed to live inside Swiggy's platform rules, not around them:

- Not an aggregator — orchestrates *within* Swiggy, not across platforms
- No competitive intelligence or scraping
- Full attribution — users always see they're ordering from Swiggy / Instamart / Dineout
- Grows platform engagement — increases Instamart basket size and Swiggy Food frequency on days that would otherwise be skipped

## Contact

Built by [your name] — reach out at [your email] or [LinkedIn].

---

*PlateRoute.ai is not affiliated with Swiggy. Built on Swiggy's MCP Builders Club platform.*
