<div align="center">

# BodyQ

### The AI-Powered Fitness & Health Assistant

**40+ features, 12 core AI Modules, 1 ecosystem**

## Overview
BodyQ **— AI-Powered Fitness & Health Assistant —** is a health platform combining multimodal AI integration, LLM orchestration, real-time computer-vision posture correction, tool-calling, a real-time retrieval-augmented generation (RAG) pipeline, vector-based memory systems, proactive event detection, a gamified habit engine and a cross-device ecosystem— all engineered for elite personal performance.

🥈 **Award Winning Project**

[Overview](#overview) · [Meet Yara & Alexi](#meet-your-ai-coaches) · [Architecture](#architecture) · [Tech Stack](#tech-stack) · [Feature Map](#complete-feature-map) · [Getting Started](#getting-started) · [Business Model](#business-model) · [Roadmap](#roadmap)

</div>

---
## Meet Your AI Coaches

| | |
|---|---|
| 🧠 **Conversational Health Coach** | A RAG-powered assistant built on **Groq (Llama 3.3 70B)**. She retrieves 30 days of a user's real activity, nutrition, workout, and body-metrics data across 5 parallel queries, holds **cross-session memory** (injuries, equipment, preferences, dislikes, goals), and proactively surfaces events — hydration goals hit, sleep patterns slipping, workout streaks — without being asked. Every conversation executes real actions server-side (log water, navigate screens, update targets). |
| 🎙️ **Voice Assistant (Alexi)** | A hands-free voice layer (Groq Whisper for STT, Expo Speech for TTS) that listens to spoken commands and executes them directly in-app — navigate screens, log water intake, log sleep — replying out loud. Built for mid-workout use when typing isn't an option. |
| 🎥 **Posture AI — Real-Time Form Correction** | On-device computer vision (MediaPipe / pose estimation) tracks 33 body landmarks, calculates joint angles via vector dot-products, and delivers form feedback with a **sub-500ms latency target**. Squat depth, back angle, knee valgus — corrected live, not in a post-workout summary. |
---

## Architecture

```
bodyq/
├── apps/
│   ├── mobile/          # React Native (Expo SDK 54) — member-facing app
│   └── admin/           # Next.js 14 (App Router) — gym owner dashboard
├── packages/
│   ├── ai-engine/       # Shared recommendation, correlation & posture logic
│   ├── types/           # Shared TypeScript types & design tokens
│   └── utils/           # Shared utilities (BMR/TDEE calc, formatting)
├── supabase/
│   ├── migrations/      # PostgreSQL schema, RLS policies, triggers
│   └── functions/       # Deno Edge Functions (RAG pipeline, AI plans, insights)
└── turbo.json
```

### The RAG Pipeline, End to End

```
Mobile App          Edge Function (Deno)         Groq / Claude API      Supabase
    │  { userId, query }      │                          │                  │
    ├─────────────────────────▶                          │                  │
    │                         │  1. RETRIEVE             │                  │
    │                         │  5 parallel RPCs: profile, activity,       │
    │                         │  nutrition, workouts, body metrics,        │
    │                         │  cross-session memory, pending events      │
    │                         ├──────────────────────────────────────────▶ │
    │                         ◀──────────────────────────────────────────┤ │
    │                         │  2. AUGMENT                                │
    │                         │  Formats retrieved rows as natural         │
    │                         │  language (not raw JSON) + rules +         │
    │                         │  tone preference + memory + events         │
    │                         │  3. GENERATE                               │
    │                         ├─────────────────────────▶                  │
    │                         ◀─────────────────────────┤                  │
    │                         │  4. PROCESS                                │
    │                         │  Extracts [MEMORY], [ACTION], [EVENT_ACK]  │
    │                         │  tags → executes RPCs → sanitizes output   │
    │                         │  5. PERSIST                                │
    │                         ├──────────────────────────────────────────▶ │
    ◀─────────────────────────┤  clean response                            │
```

**Proactive intelligence** is powered entirely by PostgreSQL triggers — no polling, no cron jobs:

- `yara_detect_workout_events` → first workout of the week, streak milestones (3/7/14/30 days)
- `yara_detect_activity_events` → hydration target hit, 2-night low-sleep streaks
- `yara_detect_body_metrics_events` → significant weight-change milestones

Each event is deduplicated via a `dedupe_key`, queued in `yara_events`, and surfaced naturally the next time the user talks to Yara — then marked consumed.

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Mobile** | React Native · Expo SDK 54 · Expo Router · Zustand · TanStack Query |
| **Admin Web** | Next.js 14 · Tailwind CSS · Recharts · Framer Motion |
| **Backend** | Supabase (PostgreSQL, Row-Level Security, Auth, Storage) · Deno Edge Functions |
| **Conversational AI** | Groq API — Llama 3.3 70B (coaching), Llama 3.1 8B-Instant (plans & insights) |
| **Speech** | Groq Whisper (speech-to-text) · Expo Speech (text-to-speech) |
| **Computer Vision** | MediaPipe (posture analysis) · custom joint-angle engine |
| **Food Recognition** | Google Gemini API (multimodal photo → nutrition estimate) |
| **Wearables** | WearOS · Kotlin · Jetpack Compose |
| **Charts & Data Viz** | Victory Native (mobile) · Recharts (web) |
| **Monorepo Tooling** | Turborepo · TypeScript everywhere |

---

## Complete Feature Map

BodyQ ships **40+ production features** across mobile, web, and backend. Core features are organized below by system.

### 🔐 Identity, Onboarding & Access

| # | Feature | Summary |
|---|---|---|
| 1 | **User Authentication** | Supabase Auth with JWT (mobile) and cookie-based SSR sessions (web).|
| 2 | **Onboarding Wizard** | 8–10 step guided flow (goal, metrics, experience, equipment, injuries, focus areas, lifestyle, diet) before the user ever sees the home screen. |
| 3 | **App Tour** | First-run spotlight walkthrough of key App features. |
| 26 | **Profile Management** | Full editable profile (avatar, goals, biometrics, assistant tone, equipment, diet).|
| 27 | **Settings** | Notification preferences, Help Center, Report a Problem, Terms & Trust Center. |
| 37 | **Role-Based Access Control** | Three-layer defense: Next.js middleware route checks, Postgres RLS on every user-owned table, and `SECURITY DEFINER` functions for privileged cross-user operations. |

### 📊 Daily Tracking & Nutrition

| # | Feature | Summary |
|---|---|---|
| 4 | **Home Dashboard** | Real-time animated calorie ring, macro bars, water tracker, live step count, and a muscle-fatigue heatmap — all driven by a single aggregated RPC and refreshed via an in-memory event bus. |
| 5 | **Calorie & Macro Tracking** | Animated SVG rings and progress bars computed from `food_logs × foods`, benchmarked against per-user `calorie_targets`. |
| 6 | **Water Tracking** | One-tap logging via an atomic upsert RPC; automatically fires a celebratory Yara event when the daily target is hit. |
| 7 | **Step Counting** | Hardware pedometer integration with 30-second batched syncing to minimize database writes, plus background-flush handling. |
| 8 | **Sleep Logging** | Manual sleep duration + quality entry; triggers a proactive Yara warning after two consecutive low-sleep nights. |
| 9 | **Nutrition / Meal Logging** | Search, scan, or photograph any meal; logs flow into a unified `food_logs` pipeline that powers every downstream nutrition metric. |
| 10 | **Food Scanner (Barcode + AI Photo)** | Barcode lookup against a local 2,000-item database, or point-and-shoot photo recognition via **Google Gemini**, complete with portion estimation and a health score. |

### 🏋️ Training & Movement

| # | Feature | Summary |
|---|---|---|
| 11 | **Workout Training Hub** | Browses 800+ exercises from a public exercise database (with offline fallback), organized by category with AI-recommended daily picks. |
| 12 | **Active Workout Session** | Live session tracking with set/rep logging, automatic muscle-fatigue updates, achievement checks, and streak-detection triggers on completion. |
| 13 | **Posture AI** | Real-time camera-based form analysis with joint-angle scoring, live visual/audio feedback, and session accuracy logged for admin analytics. |
| 14 | **Muscle Fatigue Tracking** | Per-muscle fatigue percentages that decay over time, visualized as a body heatmap, and factored directly into Yara's training recommendations. |
| 30 | **Workout History** | Full reverse-chronological session log with expandable exercise-level detail. |
| 24 | **Schedule & Planning** | A local, pub/sub-driven weekly planner that Yara can modify directly via conversational action commands. |

### 🤖 AI Coaching System

| # | Feature | Summary |
|---|---|---|
| 15 | **Yara AI Coach (Text)** | 850+ line RAG Edge Function orchestrating 5 parallel context queries, memory retrieval, event surfacing, prompt construction, and action execution — powered by Llama 3.3 70B. |
| 16 | **Yara AI Coach (Voice)** | Full voice round-trip: record → Whisper transcription → identical coaching pipeline → spoken response via TTS. |
| 17 | **Cross-Session Memory** | 9-category long-term memory (injury, medical, diet, equipment, schedule, preference, dislike, goal, other), extracted automatically from conversation and injected into every future prompt. |
| 18 | **Proactive Events** | Three database triggers detect meaningful patterns (streaks, hydration, sleep, weight milestones) with zero polling — Yara surfaces them unprompted. |
| 19 | **AI Insights Generation** | On-demand or admin-batch generation of 4 categorized insight cards (Performance, Correlation, Optimization, Prediction, Recovery, Nutrition) via fast Llama 3.1 8B. |
| 20 | **AI Training Plan Generation** | Structured 7-day plan generation with warm-ups, cool-downs, coaching tips, and defensive JSON-parsing with automatic retry on malformed output. |
| 38 | **Batch Insight Generation** | Admin-triggered bulk regeneration of insight cards across the entire user base, secured behind an admin key. |

### 🏆 Gamification & Retention

| # | Feature | Summary |
|---|---|---|
| 21 | **XP & Levels** | Atomic, `SECURITY DEFINER`-protected XP engine awarding points for workouts, meals, and streaks, with an auditable transaction log and dynamic level thresholds. |
| 22 | **Achievements** | Milestone catalog (workout counts, streaks, weight goals) evaluated after every session, complete with animated celebration overlays. |
| 23 | **Streaks** | Consecutive-day activity tracking on every app open, visualized as a milestone path toward the next reward. |
| 31 | **Flappy Bird Mini-Game** | A self-contained, purely client-side mini-game for engagement between sets — zero backend dependency. |

### 📈 Reporting & Community

| # | Feature | Summary |
|---|---|---|
| 25 | **Reports & Analytics (User)** | Aggregated 30-day trend reports across activity, nutrition, workouts, and body composition, rendered as a rich HTML viewer. |
| 28 | **Problem Reporting** | In-app bug/feature-request submission flowing into a status-tracked admin workflow (open → in progress → resolved). |
| 29 | **Community & Messaging** | Community hub and DM infrastructure (flagged as an area for future hardening — see [Roadmap](#roadmap)). |
| 39 | **Notifications** | Locally scheduled reminders (workout, hydration, meal-logging) with admin-managed campaign templates. |

### 🖥️ Platform, Admin & Infrastructure

| # | Feature | Summary |
|---|---|---|
| 33 | **Event Bus** | Lightweight in-memory pub/sub decoupling every screen — 9 event types keep the UI reactive without prop-drilling or tight coupling. |
| 32 | **Custom Splash Screen** | Branded animated boot screen orchestrating font loading, session restoration, and initial data prefetch in parallel. |
| 34 | **Admin Dashboard** | 15+ page Next.js control center — KPIs, analytics, user management, support, content CRUD, notifications, subscriptions, and audit logs. |
| 35 | **Admin Analytics** | Animated KPI cards with sparklines, 30-day growth charts, revenue breakdowns by plan tier, and AI session distribution. |
| 36 | **Marketing Website** | An 11-section, scroll-animated public landing page (Next.js, Framer Motion, GSAP) showcasing the product to prospective gyms and members. |
---

## Database Schema (Highlights)

BodyQ's PostgreSQL schema spans 20+ tables, every one RLS-protected with `auth.uid() = user_id` policies. Core entities:

```
profiles ──┬── body_metrics            calorie_targets
           ├── cycle_tracking          daily_activity
           ├── food_logs ── foods      user_meals ── meal_ingredients
           ├── workout_sessions ── workout_exercises ── exercises
           ├── ai_insights             notifications
           └── admin_roles (super_admin / content_admin / support)
```
---

## Getting Started

### Prerequisites
- Node.js 18+ and npm
- [Supabase CLI](https://supabase.com/docs/guides/cli)
- Expo CLI (`npx expo`)
- A Supabase project + Groq / Google Gemini API keys

### Setup

```bash
# 1. Clone and install
git clone https://github.com/<your-org>/bodyq.git
cd bodyq
npm install

# 2. Configure Supabase
supabase init
supabase start                          # local Postgres + Studio at :54323
supabase link --project-ref YOUR_PROJECT_REF
supabase db push

# 3. Set Edge Function secrets
supabase secrets set GROQ_API_KEY=your_key
supabase secrets set ANTHROPIC_API_KEY=your_key   # optional, for plan generation
supabase secrets set GEMINI_API_KEY=your_key

# 4. Deploy Edge Functions
supabase functions deploy ai-assistant
supabase functions deploy onboarding-plan
supabase functions deploy yara-insights

# 5. Run the apps
cd apps/mobile && npx expo start        # mobile
cd apps/admin && npm run dev            # admin dashboard (localhost:3000)
```

Environment variables are validated with Zod on boot — see `.env.example` in each app for the required keys (`SUPABASE_URL`, `SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_ROLE_KEY`, etc.).

---

## License

Proprietary — © BodyQ. All rights reserved. This repository and its contents are not licensed for redistribution or commercial use without explicit permission.
