<div align="center">
            
## BodyQ

## The AI-Powered Fitness & Health Assistant

<img src="assets/Alexi_mascott.png" alt="Alexi AI Assistant" width="200"/>

**40+ features · 12 core AI modules · 1 integrated ecosystem (mobile + web + wearable)**


## Overview

**BodyQ** is an AI-powered fitness and health platform designed to combine personalized coaching, real-time computer vision, nutrition intelligence, activity tracking, proactive assistance, gamification, and cross-device experiences in a single ecosystem.

The platform is built around the principle that **AI is a core part of the product rather than an isolated feature**.

BodyQ combines Multimodal AI, LLM-powered health coaching, Retrieval-Augmented Generation (RAG), Cross-session AI memory, Tool/action calling, Real-time computer vision, Proactive event detection, Personalized training-plan generation, Nutrition and food recognition, Gamification and habit tracking, Real-time activity tracking, Wearable device Integration and Administrative analytics dashboard.


> 🥈 **Award-Winning Project**
</div>


# Table of Contents

[Overview](#overview) · [Core AI System](#core-ai-system) · [System Architecture](#system-architecture) · [Tech Stack](#tech-stack) · [Feature Inventory](#feature-inventory) · [Database Architecture](#database-architecture) · [AI Data Flow](#ai-data-flow) · [Security Architecture](#security-architecture) · [Mobile State Management](#mobile-state-management) · [Repository Structure](#repository-structure) · [Getting Started](#getting-started) · [Environment Variables](#environment-variables)

---

# Core AI System


**AI Coach** is BodyQ's primary conversational health assistant and the RAG-powered coaching identity. It combines LLM-powered conversation (text coaching), Retrieval-Augmented Generation (RAG), Cross-session memory, Proactive events, Server-side actions, and Personalized response generation

---

**Voice Assistant (Alexi)** is the voice interface. It provides a hands-free interaction layer for situations where typing is inconvenient, particularly during workouts. The voice assistant can interpret commands such as logging activities and navigating through the application.

Pipeline:

```text
User Speech -> Groq Whisper -> Speech-to-Text -> AI Assistant -> Action / Response -> Expo Speech -> Spoken Response
```
---

**Posture AI**

BodyQ includes a real-time computer-vision system for exercise form analysis. The posture system uses the device camera to detects body landmarks, track 33 pose landmarks, calculate joint angles, evaluates exercise form and provide real-time corrective feedback, all while storing session-level metrics for analytics

The system targets **sub-500 ms feedback latency** for real-time interaction.

---

# RAG Pipeline

The AI Assistant implements a **retrieval-augmented generation (RAG)** pipeline that grounds every response in the user's application data. 
When a request is received, the Supabase Edge Function retrieves relevant data in parallel (profile, recent activity, nutrition, workouts, body metrics, cross-session memory, and pending proactive events). This context is combined with predefined business rules, user preferences, and relevant memories to construct the prompt supplied to the Groq/Llama model. 
The generated response is processed for structured commands such as [MEMORY], [ACTION], and [EVENT_ACK], allowing the system to:
- execute authorized database RPCs
- update application state.
- sanitize and return the response to the mobile application.
The resulting pipeline provides a controlled flow of retrieval, context augmentation, generation, action processing, persistence, and response delivery.

Retrieved database information is converted into structured natural-language context before being passed to the model rather than exposing raw database structures directly.

---

# System Architecture

```text
┌──────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                             │
│                                                                  │
│  ┌─────────────────────┐        ┌─────────────────────────────┐  │
│  │  Mobile App (Expo)  │        │   Web Frontend (Next.js)    │  │
│  │  React Native 0.81  │        │   Next.js 14 App Router     │  │
│  │  5-tab navigation   │        │   Marketing + Admin Panel   │  │
│  │  20+ screens        │        │   TypeScript + Tailwind     │  │
│  └────────┬────────────┘        └──────────────┬──────────────┘  │
│           │                                    │                 │
└───────────┼────────────────────────────────────┼─────────────────┘
            │             REST / RPC / Auth       │
            ▼                                    ▼
┌──────────────────────────────────────────────────────────────────┐
│                       SUPABASE BACKEND                           │
│                                                                  │
│  ┌──────────────┐   ┌──────────────┐   ┌────────────────────┐    │
│  │ Supabase     │   │ Edge         │   │ PostgreSQL 17      │    │
│  │ Auth (JWT)   │   │ Functions    │   │ Tables + RPC +     │    │
│  │ Email/Pass   │   │ Deno 2       │   │ RLS + Triggers     │    │
│  └──────────────┘   └───────┬──────┘   └────────────────────┘    │
│                             │                                    │
└─────────────────────────────┼────────────────────────────────────┘
                              │
               ┌──────────────┼────────────────┐
               ▼              ▼                ▼
        ┌────────────┐  ┌────────────┐  ┌──────────────┐
        │  Groq API  │  │  Google    │  │   Exercise   │
        │  Llama     │  │  Gemini    │  │   Database   │
        │  AI Coach  │  │  Food AI   │  │   GitHub     │
        └────────────┘  └────────────┘  └──────────────┘
```

---

# Tech Stack

| Layer            | Technology              | Purpose                                     |
| ---------------- | ----------------------- | ------------------------------------------- |
| Mobile Runtime   | Expo 54.0.33            | Mobile build and deployment                 |
| Mobile Framework | React Native 0.81.5     | Cross-platform mobile application           |
| Mobile UI        | React 19.1.0            | Mobile component rendering                  |
| Web Framework    | Next.js 14.2.30         | Web application and App Router              |
| Web UI           | React 18.3.1            | Web component rendering                     |
| Web Styling      | Tailwind CSS 3.4.6      | Utility-first styling                       |
| Web Animation    | Framer Motion / GSAP    | Animation and interaction                   |
| Web Charts       | Recharts 3.8.1          | Analytics visualization                     |
| Language         | TypeScript 5.5.3        | Web application                             |
| Language         | JavaScript ES6+         | Mobile application                          |
| Database         | PostgreSQL 17           | Primary database                            |
| Backend Platform | Supabase                | Auth, DB, Edge Functions, Storage, Realtime |
| Edge Runtime     | Deno 2                  | Edge Function execution                     |
| LLM Inference    | Groq API                | AI coaching and generation                  |
| Main LLM         | Llama 3.3-70B           | Conversational AI                           |
| Fast LLM         | Llama 3.1-8B            | Plans and insights                          |
| Vision AI        | Google Gemini           | Food image recognition                      |
| Navigation       | React Navigation 7.x    | Mobile navigation                           |
| Persistence      | AsyncStorage            | Local key-value storage                     |
| Mobile Animation | React Native Reanimated | Native-thread animations                    |
| Camera           | Expo Camera             | Food and barcode scanning                   |
| Sensors          | Expo Sensors            | Step tracking                               |
| Audio            | Expo AV / Expo Speech   | Voice interaction                           |
| Notifications    | Expo Notifications      | Local notifications                         |
| Authentication   | Supabase Auth / JWT     | Authentication and sessions                 |
| Data Isolation   | PostgreSQL RLS          | User-level data protection                  |

---

# Feature Inventory

## 🔐 Identity, Onboarding & Access

|  # | Feature                       | Implementation                                                                      |
| -: | ----------------------------- | ----------------------------------------------------------------------------------- |
|  1 | **User Authentication**       | Supabase Auth, JWT sessions, rotating refresh tokens, authentication state listener |
|  2 | **Onboarding Wizard**         | Multi-step profile collection followed by AI-generated 7-day plan                   |
|  3 | **App Tour**                  | First-run guided walkthrough persisted with AsyncStorage                            |
| 26 | **Profile Management**        | Editable goals, biometrics, preferences, equipment, diet and assistant settings     |
| 27 | **Settings**                  | Help Center, reporting, terms, trust center and notification preferences            |
| 37 | **Role-Based Access Control** | `user`, `admin`, and `super_admin` roles with middleware and RLS                    |

---

## 📊 Daily Tracking & Nutrition

|  # | Feature                      | Implementation                                               |
| -: | ---------------------------- | ------------------------------------------------------------ |
|  4 | **Home Dashboard**           | Aggregated daily data through `get_daily_dashboard_v5`       |
|  5 | **Calorie & Macro Tracking** | Food logs combined with nutritional data and calorie targets |
|  6 | **Water Tracking**           | Atomic `log_water_ml` RPC                                    |
|  7 | **Step Counting**            | Expo Pedometer with batched database writes                  |
|  8 | **Sleep Logging**            | Sleep duration and quality tracking with event detection     |
|  9 | **Nutrition / Meal Logging** | Search, barcode and image-based meal logging                 |
| 10 | **Food Scanner**             | Barcode lookup and AI-powered food recognition               |

---

## 🏋️ Training & Movement

|  # | Feature                     | Implementation                                            |
| -: | --------------------------- | --------------------------------------------------------- |
| 11 | **Workout Training Hub**    | Exercise database with local fallback                     |
| 12 | **Active Workout Sessions** | Live sessions, completion tracking and achievement checks |
| 13 | **Posture AI**              | Real-time camera-based exercise form analysis             |
| 14 | **Muscle Fatigue Tracking** | Muscle mapping and recovery-window calculations           |
| 24 | **Schedule & Planning**     | Persistent workout schedule with AI modification          |
| 30 | **Workout History**         | Historical workout session retrieval                      |

---

## 🤖 AI Coaching

|  # | Feature                      | Implementation                                         |
| -: | ---------------------------- | ------------------------------------------------------ |
| 15 | **Yara AI Coach**            | RAG-powered conversational health assistant            |
| 16 | **Voice AI Coach**           | Whisper speech recognition + Yara + text-to-speech     |
| 17 | **Cross-Session Memory**     | Persistent user memory with categorized facts          |
| 18 | **Proactive Events**         | Database-driven event detection and notification queue |
| 19 | **AI Insights**              | Personalized insight generation with caching           |
| 20 | **AI Training Plans**        | AI-generated 7-day training plans                      |
| 38 | **Batch Insight Generation** | Admin-controlled generation across users               |

---

## 🏆 Gamification & Retention

|  # | Feature          | Implementation                                  |
| -: | ---------------- | ----------------------------------------------- |
| 21 | **XP & Levels**  | Database-backed XP system and level calculation |
| 22 | **Achievements** | Automatic achievement evaluation after workouts |
| 23 | **Streaks**      | Visit tracking and milestone progression        |
| 31 | **Mini-Game**    | Client-side Flappy Bird implementation          |

---

## 📈 Reporting & Community

|  # | Feature                      | Implementation                                               |
| -: | ---------------------------- | ------------------------------------------------------------ |
| 25 | **User Reports & Analytics** | Aggregated workout, nutrition, sleep and body-metric reports |
| 28 | **Problem Reporting**        | Support workflow: `open → in_progress → resolved`            |
| 29 | **Community & Messaging**    | Community, inbox and direct-message interfaces               |
| 39 | **Notifications**            | Local notification scheduling and administrative templates   |

> **Current limitation:** Community and messaging interfaces exist, but backend functionality remains limited.

---

## 🖥️ Platform, Admin & Infrastructure

|  # | Feature                  | Implementation                                                    |
| -: | ------------------------ | ----------------------------------------------------------------- |
| 32 | **Custom Splash Screen** | Parallel initialization of fonts, authentication and initial data |
| 33 | **Event Bus**            | In-memory pub/sub system for cross-screen events                  |
| 34 | **Admin Dashboard**      | 15+ administrative pages                                          |
| 35 | **Admin Analytics**      | KPIs, growth charts, revenue breakdown and AI analytics           |
| 36 | **Marketing Website**    | Animated marketing site with lazy-loaded sections                 |

---

# Database Architecture

BodyQ uses PostgreSQL through Supabase as its primary application database.

## Entity Overview

```text
auth.users
    │
    ├── profiles
    ├── daily_activity
    ├── body_metrics
    ├── calorie_targets
    ├── food_logs ──────────→ foods
    ├── workout_sessions
    ├── muscle_fatigue
    ├── workout_plans
    ├── achievements
    ├── xp_log
    ├── ai_insights
    ├── user_insights
    ├── ai_sessions
    ├── user_memory
    ├── yara_events
    ├── reports
    └── subscriptions
```

## Core Tables

| Table              | Purpose                                         |
| ------------------ | ----------------------------------------------- |
| `profiles`         | User identity, goals, preferences, XP and level |
| `daily_activity`   | Steps, water, sleep and daily activity          |
| `body_metrics`     | Historical body measurements                    |
| `calorie_targets`  | Personalized nutrition targets                  |
| `food_logs`        | User meal records                               |
| `foods`            | Food and nutrition information                  |
| `workout_sessions` | Completed workout sessions                      |
| `muscle_fatigue`   | Muscle fatigue and recovery state               |
| `workout_plans`    | Workout plans and AI adaptations                |
| `achievements`     | Earned achievements                             |
| `xp_log`           | XP transaction history                          |
| `ai_insights`      | Generated AI insights                           |
| `user_insights`    | User-facing insight cards                       |
| `ai_sessions`      | AI session analytics                            |
| `user_memory`      | Persistent AI memory                            |
| `yara_events`      | Proactive AI events                             |
| `reports`          | User support reports                            |
| `subscriptions`    | Subscription information                        |

---

# Key RPC Functions

| Function                               | Purpose                                      |
| -------------------------------------- | -------------------------------------------- |
| `get_user_full_activity_summary(uuid)` | Retrieves 30-day activity averages           |
| `get_user_nutrition_summary(uuid)`     | Retrieves nutrition history and macro totals |
| `get_user_workout_summary(uuid)`       | Retrieves recent workout sessions            |
| `get_user_body_metrics_history(uuid)`  | Retrieves body-metric history                |
| `get_user_ai_history(uuid)`            | Retrieves AI coaching history                |
| `log_water_ml()`                       | Atomically updates water intake              |
| `log_sleep_data()`                     | Records sleep data                           |
| `increment_steps()`                    | Atomically increments daily steps            |
| `award_xp()`                           | Awards XP and updates user progression       |
| `check_achievements()`                 | Evaluates achievement conditions             |
| `get_user_level_info()`                | Retrieves level information                  |
| `add_user_memory()`                    | Stores an AI memory                          |
| `get_user_memory()`                    | Retrieves relevant user memory               |
| `delete_user_memory()`                 | Removes stored memory                        |
| `get_pending_yara_events()`            | Retrieves pending proactive events           |
| `consume_yara_events()`                | Marks events as consumed                     |
| `get_insights_data(uuid, period)`      | Retrieves data for insight generation        |
| `record_user_visit(uuid)`              | Tracks application visits and streaks        |

---

# Database Triggers

BodyQ moves important business logic into database triggers so that critical events are detected consistently.

| Trigger                             | Fires On           | Detects                                    |
| ----------------------------------- | ------------------ | ------------------------------------------ |
| `yara_detect_workout_events()`      | `workout_sessions` | First weekly workout and streak milestones |
| `yara_detect_activity_events()`     | `daily_activity`   | Water targets and low-sleep patterns       |
| `yara_detect_body_metrics_events()` | `body_metrics`     | Significant body-metric changes            |

The current schema has evolved through **17 database migrations**, covering the progression from the initial insight system to proactive events, streak tracking and achievement deduplication.

---

# AI Data Flow

The main AI interaction follows this lifecycle:

```text
User Request
     │
     ▼
Authentication
     │
     ▼
Supabase Edge Function
     │
     ▼
Retrieve User Context
     │
     ├── Profile
     ├── Activity
     ├── Nutrition
     ├── Workouts
     ├── Body Metrics
     ├── Memory
     └── Events
     │
     ▼
Context Construction
     │
     ▼
LLM Generation
     │
     ▼
Structured Output Processing
     │
     ├── Memory commands
     ├── Application actions
     └── Event acknowledgement
     │
     ▼
RPC Execution
     │
     ▼
Output Sanitization
     │
     ▼
Response
```

This architecture allows the AI assistant to move beyond conversation and interact with the application's underlying state.

---

# Security Architecture

| Mechanism                 | Implementation                                        |
| ------------------------- | ----------------------------------------------------- |
| **Authentication**        | Supabase Auth with email/password and JWT             |
| **Authorization**         | Role-based access control                             |
| **Row-Level Security**    | User-owned tables protected through `auth.uid()`      |
| **API Key Isolation**     | Server-side AI keys stored in Edge Function secrets   |
| **CORS**                  | Shared CORS configuration across Edge Functions       |
| **Input Validation**      | JSON parsing, enum validation and length restrictions |
| **Error Containment**     | Database trigger exception handling                   |
| **Rate Limiting**         | Supabase authentication rate limits                   |
| **Privileged Operations** | Controlled `SECURITY DEFINER` RPCs                    |

### Important Security Note

The Gemini API key used by the food scanner is currently accessed client-side. This is a known security limitation and should be moved behind a server-side Edge Function before production deployment.

---

# Mobile State Management

| Store              | Type          | Persistence             | Main Data                     |
| ------------------ | ------------- | ----------------------- | ----------------------------- |
| `AuthContext`      | React Context | AsyncStorage / Supabase | Authentication state          |
| `TodayContext`     | React Context | Supabase                | Daily goals and tracking      |
| `MilestoneContext` | React Context | Supabase                | Streaks and achievements      |
| `scheduleStore`    | Pub/Sub       | AsyncStorage            | Workout schedule              |
| `eventBus`         | Pub/Sub       | Not persisted           | Temporary cross-screen events |

---

# Repository Structure

```text
BodyQ/
│
├── README.md
│
├── assets/
│   └── yara.png
│
├── frontend/
│   ├── app/
│   │   ├── api/
│   │   ├── app/
│   │   ├── dashboard/
│   │   ├── login/
│   │   ├── signup/
│   │   └── onboarding/
│   │
│   ├── components/
│   │   ├── app/
│   │   ├── shared/
│   │   ├── auth/
│   │   ├── dashboard/
│   │   ├── sections/
│   │   └── ui/
│   │
│   ├── context/
│   ├── hooks/
│   ├── lib/
│   │   └── supabase/
│   └── middleware.ts
│
├── mobile-frontend/
│   ├── screens/
│   │   ├── auth/
│   │   ├── nutrition/
│   │   ├── workout/
│   │   ├── sleep/
│   │   ├── community/
│   │   └── settings/
│   │
│   ├── components/
│   ├── context/
│   ├── services/
│   ├── store/
│   └── lib/
│
├── supabase/
│   ├── migrations/
│   └── functions/
│       ├── ai-assistant/
│       ├── onboarding-plan/
│       ├── yara-insights/
│       ├── generate-user-insights/
│       └── _shared/
│           └── cors.ts
│
└── WearApp/
    └── # Reserved for future Wear OS integration
```

---

# Getting Started

## Prerequisites

Install the following:

* Node.js 18+
* npm
* Supabase CLI
* Expo CLI
* A Supabase project
* Groq API key
* Google Gemini API key

---

## Installation

Clone the repository:

```bash
git clone https://github.com/<your-org>/bodyq.git
cd bodyq
```

---

## Backend Setup

Initialize and start Supabase:

```bash
supabase init
supabase start
```

Link the project:

```bash
supabase link --project-ref YOUR_PROJECT_REF
```

Apply database migrations:

```bash
supabase db push
```

Configure secrets:

```bash
supabase secrets set GROQ_API_KEY=your_key
supabase secrets set ADMIN_SECRET=your_secret
```

Deploy the Edge Functions:

```bash
supabase functions deploy ai-assistant
supabase functions deploy onboarding-plan
supabase functions deploy yara-insights
supabase functions deploy generate-user-insights
```

---

## Web Application

```bash
cd frontend
npm install
npm run dev
```

The web application will run locally through the Next.js development server.

---

## Mobile Application

```bash
cd mobile-frontend
npm install
npx expo start
```

Use the Expo development interface to launch the application on a physical device or emulator.

---

# Environment Variables

## Web

Location:

```text
frontend/.env.local
```

| Variable                        | Purpose                                    |
| ------------------------------- | ------------------------------------------ |
| `NEXT_PUBLIC_SUPABASE_URL`      | Public Supabase project URL                |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Public Supabase anonymous key              |
| `SUPABASE_SERVICE_ROLE_KEY`     | Server-side administrative database access |

---

## Supabase Edge Functions

Configured through Supabase secrets:

| Variable       | Purpose                    |
| -------------- | -------------------------- |
| `GROQ_API_KEY` | Groq LLM access            |
| `ADMIN_SECRET` | Protected batch operations |

---

## Mobile

Location:

```text
mobile-frontend/.env
```

| Variable                        | Purpose                        |
| ------------------------------- | ------------------------------ |
| `EXPO_PUBLIC_SUPABASE_URL`      | Supabase project URL           |
| `EXPO_PUBLIC_SUPABASE_ANON_KEY` | Supabase anonymous key         |
| `GEMINI_API_KEY`                | Gemini food-recognition access |

---

# Project Philosophy

BodyQ is designed around a simple principle:

> **The AI should understand the user's context, not just the user's question.**

