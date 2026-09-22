# Hospital Resource Allocation Dashboard

An interactive decision-support dashboard for the [Hospital Resource Allocation Optimization](../README.md) project. It lets a user configure a demand scenario, run the resource-allocation optimizer, and inspect the resulting staffing plan, utilization, and a 24-hour patient-flow simulation.

Built with **React 18**, **TypeScript**, **Vite**, **Tailwind CSS**, and **Supabase** (Postgres) for persistence.

## Features

- **Scenario configuration** — set demand level, available doctors/nurses/beds/equipment, average treatment time, shift length, and per-unit costs.
- **Weighted optimization** — tune the relative importance of waiting time, utilization, cost, and workload balance, then run the allocator.
- **Results overview** — optimal doctor/nurse/bed/equipment counts, expected waiting time, utilization rates, and total operational cost.
- **24-hour simulation** — a stochastic patient-flow simulation (Poisson arrivals, exponential service times) visualized as time-series charts.
- **Scenario comparison** — save multiple optimization runs side by side to compare trade-offs.
- **Persistence** — scenarios, optimization results, and simulation snapshots are stored in Supabase so they survive a page reload.

## Getting started

### Prerequisites

- Node.js 18+
- A free [Supabase](https://supabase.com) project (for persistence)

### Setup

```bash
# from the dashboard/ directory
npm install
cp .env.example .env
```

Fill in `.env` with your Supabase project's URL and anon key (**Settings → API** in the Supabase dashboard):

```
VITE_SUPABASE_URL=your-supabase-project-url
VITE_SUPABASE_ANON_KEY=your-supabase-anon-key
```

Apply the database schema by running the SQL migration in `supabase/migrations/` against your Supabase project (via the Supabase SQL editor, or the Supabase CLI):

```bash
supabase db push
```

This creates three tables — `scenarios`, `optimization_results`, and `simulation_results` — with row-level security enabled.

### Run

```bash
npm run dev       # start the dev server
npm run build      # production build
npm run typecheck  # TypeScript checks
npm run lint       # ESLint
```

## Project structure

```
dashboard/
├── src/
│   ├── App.tsx                # top-level layout, tab routing, optimization/simulation orchestration
│   ├── components/
│   │   ├── Sidebar.tsx        # scenario configuration form
│   │   ├── Results.tsx        # optimal allocation + KPI summary
│   │   ├── Charts.tsx         # 24-hour simulation time series
│   │   ├── Comparison.tsx     # side-by-side scenario comparison
│   │   └── Tabs.tsx           # tab primitives
│   └── lib/
│       ├── optimization.ts    # heuristic resource-allocation solver
│       ├── simulation.ts      # stochastic patient-flow simulator
│       └── supabase.ts        # Supabase client + data-access functions
└── supabase/migrations/       # database schema
```

## Implementation note

The optimizer in `src/lib/optimization.ts` is a fast, closed-form heuristic (derived from M/M/c queueing approximations) rather than a full mixed-integer program — it's designed for instant, interactive feedback in the browser. The [written report](../report/report.pdf) describes and evaluates the full MILP + genetic-algorithm formulation that this dashboard's methodology is based on; see the report for the formal optimization results.
