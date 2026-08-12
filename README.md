<div align="center">

# BodyQ

### The AI-Powered Fitness & Health Assistant

**40+ features, 12 core AI Modules, 1 ecosystem**

## Overview
BodyQ **— AI-Powered Fitness & Health Assistant —** is a health platform combining multimodal AI integration, LLM orchestration, real-time computer-vision posture correction, tool-calling, a real-time retrieval-augmented generation (RAG) pipeline, vector-based memory systems, proactive event detection, a gamified habit engine and a cross-device ecosystem— all engineered for elite personal performance.

The AI **is** the product, not a bolted-on feature. Every business rule that matters (streak detection, achievement checks, proactive coaching triggers) lives at the database level, so it's consistent no matter which client calls it.

🥈 **Award Winning Project**

[Overview](#overview) · [Architecture](#system-architecture) · [Tech Stack](#tech-stack) · [Meet Your AI Coach](#meet-alexi) · [Feature Inventory](#feature-inventory) · [Database](#database-schema) · [Security](#security-architecture) · [Getting Started](#getting-started) 


</div>

---
## Meet Your AI Coaches


### The RAG Pipeline, End to End

```
Mobile App          Edge Function (Deno)              Groq API        Supabase
    │  { userId, query }      │                          │                 │
    ├─────────────────────────▶                          │                 │
    │                         │  1. RETRIEVE             │                 │
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


---

## System Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                              │
│                                                                  │
│  ┌─────────────────────┐        ┌─────────────────────────────┐  │
│  │  Mobile App (Expo)  │        │   Web Frontend (Next.js)    │  │
│  │  React Native 0.81  │        │   Next.js 14 (App Router)   │  │
│  │  5-tab navigation   │        │   Marketing + Admin Panel   │  │
│  │  20+ screens        │        │   TypeScript + Tailwind     │  │
│  └────────┬────────────┘        └──────────────┬──────────────┘  │
│           │                                    │                 │
└───────────┼────────────────────────────────────┼─────────────────┘
            │            REST / RPC / Auth       │
            ▼                                    ▼
┌──────────────────────────────────────────────────────────────────┐
│                      SUPABASE BACKEND                            │
│                                                                  │
│  ┌──────────────┐   ┌──────────────┐   ┌────────────────────┐    │
│  │  Supabase    │   │   Edge       │   │   PostgreSQL 17    │    │
│  │  Auth (JWT)  │   │   Functions  │   │   Tables + RPC +   │    │
│  │  Email/Pass  │   │   (Deno 2)   │   │   RLS + Triggers   │    │
│  └──────────────┘   └──────┬───────┘   └────────────────────┘    │
│                            │                                     │
└────────────────────────────┼─────────────────────────────────────┘
                             │
              ┌──────────────┼────────────────┐
              ▼              ▼                ▼
       ┌────────────┐   ┌────────────┐   ┌────────────┐
       │  Groq API  │   │  Google    │   │  GitHub    │
       │  Llama 3.3 │   │  Gemini    │   │  Exercise  │
       │  AI Coach  │   │  Food AI   │   │  DB (free) │
       └────────────┘   └────────────┘   └────────────┘
```

---

## Tech Stack

| Layer | Technology | Version | Purpose |
|---|---|---|---|
| Mobile Runtime | Expo | 54.0.33 | RN build/deploy toolchain |
| Mobile Framework | React Native | 0.81.5 | Cross-platform mobile UI |
| Mobile UI | React | 19.1.0 | Component rendering (mobile) |
| Web Framework | Next.js | 14.2.30 | SSR/SSG, App Router |
| Web UI | React | 18.3.1 | Component rendering (web) |
| Web Styling | Tailwind CSS | 3.4.6 | Utility-first CSS |
| Web Animation | Framer Motion / GSAP | 11.3.8 / – | Motion, scroll & timeline animation |
| Web Charts | Recharts | 3.8.1 | Admin data visualization |
| Language | TypeScript | 5.5.3 | Web frontend |
| Language | JavaScript (ES6+) | – | Mobile frontend |
| Database | PostgreSQL | 17 | Primary data store |
| BaaS | Supabase | – | Auth, DB, Edge Functions, Storage, Realtime |
| Edge Runtime | Deno | 2 | Edge Function execution |
| LLM Inference | Groq API | – | Coaching, plans, insights |
| Coaching Model | Llama 3.3-70b-versatile | – | Main conversation model |
| Fast Model | Llama 3.1-8b-instant | – | Plan generation & insights |
| Vision AI | Google Gemini | – | Food image recognition |
| Navigation | React Navigation | 7.x | Mobile stack + tabs |
| Local Persistence | AsyncStorage | – | Mobile key-value store |
| Animation (Mobile) | react-native-reanimated | – | Native-thread animation |
| Camera | expo-camera | – | Barcode / photo food scanning |
| Sensors | expo-sensors | – | Pedometer (step tracking) |
| Audio | expo-av + expo-speech | – | Voice recording + TTS |
| Notifications | expo-notifications | – | Local push notifications |
| Auth Protocol | JWT (Supabase Auth) | – | 1-hour session expiry, rotating refresh |
| Data Isolation | Row-Level Security (RLS) | – | Per-user table policies |

---

## Meet Yara


| | |
|---|---|
| 🧠 **Conversational Health Coach** | A RAG-powered assistant built on **Groq (Llama 3.3 70B)**. She retrieves 30 days of a user's real activity, nutrition, workout, and body-metrics data across 5 parallel queries, holds **cross-session memory** (injuries, equipment, preferences, dislikes, goals), and proactively surfaces events — hydration goals hit, sleep patterns slipping, workout streaks — without being asked. Every conversation executes real actions server-side (log water, navigate screens, update targets). |
| 🎙️ **Voice Assistant (Alexi)** | A hands-free voice layer (Groq Whisper for STT, Expo Speech for TTS) that listens to spoken commands and executes them directly in-app — navigate screens, log water intake, log sleep — replying out loud. Built for mid-workout use when typing isn't an option. |
| 🎥 **Posture AI — Real-Time Form Correction** | On-device computer vision (MediaPipe / pose estimation) tracks 33 body landmarks, calculates joint angles via vector dot-products, and delivers form feedback with a **sub-500ms latency target**. Squat depth, back angle, knee valgus — corrected live, not in a post-workout summary. |
---

## Feature Inventory

**40+ shipped features** across mobile, web, and backend. Core features are organized as following:

### 🔐 Identity, Onboarding & Access
| # | Feature | How it works |
|---|---|---|
| 1 | **User Authentication** | Supabase Auth, JWT (1h expiry, rotating refresh). `AuthContext` listens to `onAuthStateChange`. Web uses cookie sessions via `@supabase/ssr`; middleware protects `/app/*` (authenticated) and `/dashboard/*` (admin role). |
| 2 | **Onboarding Wizard** | Multi-step form (goal, metrics, experience, injuries, equipment, focus areas, sleep, stress, diet) → `onboarding-plan` Edge Function → Groq (Llama 3.1-8B) generates a 7-day plan → profile saved. |
| 3 | **App Tour** | First-run spotlight walkthrough via tour refs attached to key UI, state persisted in AsyncStorage. |
| 26 | **Profile Management** | Full editable profile (goal, biometrics, assistant tone, equipment, diet) feeding directly into Yara's coaching context. |
| 27 | **Settings** | Help Center, Report a Problem, Terms & Trust Center, notification prefs (AsyncStorage). |
| 37 | **Role-Based Access Control** | Three roles (`user`/`admin`/`super_admin`). Middleware route checks + RLS on 16+ tables + `SECURITY DEFINER` for privileged cross-user ops. |

### 📊 Daily Tracking & Nutrition
| # | Feature | How it works |
|---|---|---|
| 4 | **Home Dashboard** | Single-RPC aggregation (`get_daily_dashboard_v5`) driving the calorie ring, water tracker, step count, sleep, muscle-fatigue heatmap. `TodayContext` is the single source of truth. |
| 5 | **Calorie & Macro Tracking** | Animated SVG ring + macro bars computed from `food_logs × foods`, benchmarked against `calorie_targets`. |
| 6 | **Water Tracking** | `log_water_ml(user_id, delta, date)` atomic RPC. Hitting 2000ml fires the activity-events trigger. |
| 7 | **Step Counting** | `expo-sensors` Pedometer, batched to `increment_steps` every 30s to cut DB writes. |
| 8 | **Sleep Logging** | `log_sleep_data(user_id, hours, quality, date)`; 2 consecutive nights <6h fires a warning event. |
| 9 | **Nutrition / Meal Logging** | Search, scan, or photograph a meal; every path lands in `food_logs` for unified macro totals. |
| 10 | **Food Scanner (Barcode + AI Photo)** | Barcode → local DB lookup. Photo → **client-side Google Gemini call** (flagged as a security). |

### 🏋️ Training & Movement
| # | Feature | How it works |
|---|---|---|
| 11 | **Workout Training Hub** | GitHub free-exercise-db with a 3s timeout fallback to local `exercises.json`. Admin CRUD for `workout_plans`. |
| 12 | **Active Workout Session** | Live timer → `workout_sessions` on completion → `muscle_fatigue` update → `check_achievements` RPC → `yara_detect_workout_events` trigger. |
| 13 | **Posture AI** | Camera-based real-time posture analysis; session metrics logged to `ai_sessions` for admin analytics. |
| 14 | **Muscle Fatigue Tracking** | `EXERCISE_MUSCLES` mapping + `RECOVERY_MAP` recovery windows, visualized as a Home-screen heatmap. |
| 30 | **Workout History** | `fetchWorkoutHistory()` — currently unbounded, no pagination (see limitations). |
| 24 | **Schedule & Planning** | Pub/sub `scheduleStore` with AsyncStorage persistence; Yara can modify it via action commands. |

### 🤖 AI Coaching System
| # | Feature | How it works |
|---|---|---|
| 15 | **Yara AI Coach (Text)** | `ai-assistant` Edge Function — full RAG pipeline described [above](#meet-yara). |
| 16 | **Yara AI Coach (Voice)** | Same pipeline via Whisper STT + `expo-speech` TTS. |
| 17 | **Cross-Session Memory** | `user_memory` table, 9 categories, `add_user_memory` / `get_user_memory` / `delete_user_memory` RPCs. |
| 18 | **Proactive Events** | `yara_events` table + 3 detector triggers + `get_pending_yara_events` / `consume_yara_events`. |
| 19 | **AI Insights Generation** | User-triggered `yara-insights` (4 categorized cards) or admin-batch `generate-user-insights`, cached to avoid duplicate Groq calls. |
| 20 | **AI Training Plan Generation** | Onboarding (`onboarding-plan`, pre-auth) or in-app (`/api/generate-plan`) — 7-day split with defensive JSON parsing + retry. |
| 38 | **Batch Insight Generation** | Admin-key-gated batch regeneration across the full user base via `generate-user-insights`. |

### 🏆 Gamification & Retention
| # | Feature | How it works |
|---|---|---|
| 21 | **XP & Levels** | `award_xp` (`SECURITY DEFINER`) logs to `xp_log`, updates `profiles.xp_current/xp_total`, checks level formula `50 × level × (level − 1)`. |
| 22 | **Achievements** | `check_achievements` evaluates the catalog after every workout; awards XP; triggers celebration overlay. |
| 23 | **Streaks** | `record_user_visit` on every app open; milestone path UI (`MilestonePath`, `useStreaks`). |
| 31 | **Flappy Bird Mini-Game** | Purely client-side, zero backend dependency. |

### 📈 Reporting & Community
| # | Feature | How it works |
|---|---|---|
| 25 | **Reports & Analytics (User)** | HTML report aggregating workout, nutrition, body-metrics, and sleep trends via summary RPCs. |
| 28 | **Problem Reporting** | `reports` table with `open → in_progress → resolved` workflow; admin-only read policy. |
| 29 | **Community & Messaging** | UI screens exist (`CommunityCenter`, `MessagesInbox`, `DMThread`) — **backend support is thin** (see limitations). |
| 39 | **Notifications** | Local scheduling via `expo-notifications`; prefs in AsyncStorage; admin-managed templates on web. |

### 🖥️ Platform, Admin & Infrastructure
| # | Feature | How it works |
|---|---|---|
| 33 | **Event Bus** | In-memory pub/sub, 9 event types (`MEAL_LOGGED`, `WATER_LOGGED`, `WORKOUT_COMPLETED`, etc.) decoupling every screen. |
| 32 | **Custom Splash Screen** | Animated boot screen while fonts, session, and initial data load in parallel. |
| 34 | **Admin Dashboard** | 15+ Next.js pages — KPIs, user management, content CRUD, support workflow, notifications, subscriptions, audit logs, real-time monitoring. |
| 35 | **Admin Analytics** | KPI cards with sparklines, 30-day growth charts, revenue breakdown by plan, AI session accuracy. |
| 36 | **Marketing Website** | 11 lazy-loaded sections (hero, features, AI showcase, pricing, testimonials, CTA) — Framer Motion + GSAP. |

---

## Database Schema

### Entity Overview

```
auth.users (Supabase built-in)
    │
    ├── profiles (1:1)           role, goal, activity_level, xp_current, xp_total, level, last_active
    ├── daily_activity (1:N)     date, steps, water_ml, sleep_hours, sleep_quality, calories_burned
    ├── body_metrics (1:N)       weight_kg, body_fat_pct, recorded_at
    ├── calorie_targets (1:1)    calorie_target, protein_target, carbs_target, fat_target
    ├── food_logs (1:N) → foods  food_id, consumed_at, meal_type, quantity_grams
    ├── workout_sessions (1:N)   started_at, ended_at, calories_burned, avg_posture_score
    ├── muscle_fatigue (1:N)     muscle_name, fatigue_pct
    ├── workout_plans (1:N)      name, category, difficulty, exercises (JSON), ai_adapted
    ├── achievements (1:N)       achievement_id, icon, xp_reward, earned_at
    ├── xp_log (1:N)             source, amount, description
    ├── ai_insights (1:N)        insight_type, message, source, is_read
    ├── user_insights (1:N)      insight_type, message, icon, color
    ├── ai_sessions (1:N)        type (posture), duration, accuracy, error
    ├── user_memory (1:N)        category, fact (≤240 chars), last_used_at
    ├── yara_events (1:N)        event_type, payload, severity, dedupe_key, consumed_at
    ├── reports (1:N)            issue_type, subject, details, status
    └── subscriptions (1:N)      plan, amount, status
```

### Key RPC Functions

| Function | Purpose |
|---|---|
| `get_user_full_activity_summary(uuid)` | 30-day activity averages |
| `get_user_nutrition_summary(uuid)` | 30-day macro totals + recent meals |
| `get_user_workout_summary(uuid)` | Recent session list |
| `get_user_body_metrics_history(uuid)` | Weight/body-fat time series |
| `get_user_ai_history(uuid)` | Recent AI coaching sessions |
| `log_water_ml` / `log_sleep_data` / `increment_steps` | Atomic daily-activity upserts |
| `award_xp` / `check_achievements` / `get_user_level_info` | Gamification engine |
| `add_user_memory` / `get_user_memory` / `delete_user_memory` | Cross-session memory CRUD |
| `get_pending_yara_events` / `consume_yara_events` | Proactive event queue |
| `get_insights_data(uuid, period)` | Stats feeding insight generation |
| `record_user_visit(uuid)` | Login-streak tracking |

### Database Triggers

| Trigger | Fires On | Detects |
|---|---|---|
| `yara_detect_workout_events()` | `workout_sessions` INSERT/UPDATE | First workout of week; streak milestones (3/7/14/30 days) |
| `yara_detect_activity_events()` | `daily_activity` INSERT/UPDATE | Water target hit (2000ml); 2-night low-sleep streak (<6h) |
| `yara_detect_body_metrics_events()` | `body_metrics` INSERT | Significant weight change |

17 migrations track this schema's evolution, from the initial insights RPC through the proactive-events system, login-streak tracking, and achievement-deduplication cleanup.

---

## Security Architecture

| Mechanism | Implementation |
|---|---|
| **Authentication** | Supabase Auth, email/password, JWT (1h expiry), rotating refresh tokens |
| **Authorization** | Role-based (`user`/`admin`/`super_admin`) via Next.js middleware + RLS |
| **Row-Level Security** | All 16+ user-owned tables: `auth.uid() = user_id` |
| **API Key Isolation** | Groq key lives in Edge Function secrets only; anon key is client-safe; `service_role` never bundled |
| **CORS** | Shared `cors.ts` applied across every Edge Function |
| **Input Validation** | JSON parsing guards, memory fact length checks (≤240 chars), category enum validation |
| **Error Containment** | Trigger functions wrap logic in exception handlers so errors never cascade to the triggering write |
| **Rate Limiting (Auth)** | 2 emails/hr, 30 SMS/hr, 30 token refreshes/5min |
| **Privileged Operations** | `SECURITY DEFINER` RPCs (`award_xp`, `check_achievements`, memory CRUD) bypass RLS safely for legitimate cross-boundary writes |

---

## State Management (Mobile)

| Store | Type | Persisted | Key Data |
|---|---|---|---|
| `AuthContext` | React Context | Supabase session (AsyncStorage) | user, isNewUser, shouldShowTour |
| `TodayContext` | React Context | Supabase DB | goals, foodLogs, waterMl, sleepHours, muscleFatigue, steps |
| `MilestoneContext` | React Context | Supabase DB | currentStreak, unlocks, pendingCelebration |
| `scheduleStore` | Pub/Sub | AsyncStorage | Exercise schedule, daily completion |
| `eventBus` | Pub/Sub | Not persisted | Transient cross-screen events |

---


## Repository Structure

```
BodyQ/
├── frontend/                    # Next.js web app (marketing + admin)
│   ├── app/                     # App Router: api/, app/, dashboard/, login/, signup/, onboarding/
│   ├── components/              # app/shared, auth/, dashboard/, sections/, ui/
│   ├── context/                 # AuthContext
│   ├── hooks/                   # useAppData, useStreaks
│   ├── lib/supabase/             # Clients + 8 query modules
│   └── middleware.ts             # Auth + role gate
│
├── mobile-frontend/              # Expo React Native app
│   ├── screens/                  # 20+ screens (auth/, nutrition/, workout/, sleep/, community/, settings/)
│   ├── components/               # 25+ components (home/, shared/, food-scanner/, reports/, onBoarding/)
│   ├── context/                  # AuthContext, TodayContext, MilestoneContext
│   ├── services/                 # 12+ service modules
│   ├── store/                    # scheduleStore.js
│   └── lib/                      # eventBus.js, supabase client
│
├── supabase/
│   ├── migrations/               # 17 SQL migrations
│   └── functions/                # ai-assistant, onboarding-plan, yara-insights,
│                                  # generate-user-insights, _shared/cors.ts
│
├── WearApp/                      # Reserved for future Wear OS integration (currently empty)
└── README.md
```

---

## Getting Started

### Prerequisites
- Node.js 18+, npm
- [Supabase CLI](https://supabase.com/docs/guides/cli)
- Expo CLI (`npx expo`)
- Supabase project + Groq / Google Gemini API keys

### Setup

```bash
git clone https://github.com/<your-org>/bodyq.git
cd bodyq

# Backend
supabase init
supabase start                                # local Postgres 17 + Studio
supabase link --project-ref YOUR_PROJECT_REF
supabase db push                               # runs all 17 migrations

supabase secrets set GROQ_API_KEY=your_key
supabase secrets set ADMIN_SECRET=your_secret
supabase functions deploy ai-assistant
supabase functions deploy onboarding-plan
supabase functions deploy yara-insights
supabase functions deploy generate-user-insights

# Web (Next.js)
cd frontend && npm install && npm run dev       # localhost:3000

# Mobile (Expo)
cd mobile-frontend && npm install && npx expo start
```

### Environment Variables

| Variable | Location | Purpose |
|---|---|---|
| `NEXT_PUBLIC_SUPABASE_URL` | `frontend/.env.local` | Public Supabase project URL |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | `frontend/.env.local` | Public anon key |
| `SUPABASE_SERVICE_ROLE_KEY` | Supabase Dashboard (secret) | Admin database access |
| `GROQ_API_KEY` | Supabase Function Secrets | Groq LLM key |
| `ADMIN_SECRET` | Supabase Function Secrets | Batch operation auth |
| `EXPO_PUBLIC_SUPABASE_URL` | `mobile-frontend/.env` | Mobile Supabase URL |
| `EXPO_PUBLIC_SUPABASE_ANON_KEY` | `mobile-frontend/.env` | Mobile anon key |
| `GEMINI_API_KEY` | `mobile-frontend` (client-side — see limitations) | Google Gemini food scanner |

---
