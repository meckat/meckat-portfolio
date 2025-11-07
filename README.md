Engineering Showcase: Architecture of a Multi-Region AI SaaS Product
This document provides a high-level architectural overview of my primary work: MECKs Translator, a complete, proprietary AI-powered SaaS ecosystem.

All repositories described herein are Closed Source. This showcase is intended to demonstrate my capabilities in system design, MLOps, and full-stack engineering by presenting the architecture of this complex, real-world application.

The Product: The MECKs Translator Ecosystem
MECKs Translator is not a single application, but a complete, scalable platform composed of four main components that work in unison:

The Core Bot: The scalable, multi-region heart of the system, handling all AI operations.

The Dashboard: The SaaS control plane (Next.js 15) for configuration, billing (Stripe), and business KPIs.

The Landing Page: The marketing front-end, complete with a newsletter funnel (PostgreSQL) and live status polling (Redis).

The Demos: An interactive React-based showcase for customer onboarding.

1. Architecture: MECKs-Translator (The Core Bot)
Purpose: The core Discord bot, engineered as a scalable gateway process with detached worker backends to deliver AI-powered translations and summaries.

Tech Stack & Architectural Highlights:

System Design: Node.js 22 utilizing the discord.js ShardingManager for horizontal scaling across thousands of servers.

Infrastructure: Deployed via Docker to Fly.io for Multi-Region (e.g., fra, iad) operation, orchestrated with process groups (gateway/worker).

Resilience & Queuing: Redis (Upstash) is used for multi-layer caching, distributed locks, and a robust background worker queue featuring regional routing and failover logic.

MLOps (In-House AI): Leverages self-hosted LLM endpoints (e.g., Llama models) to replace expensive, inflexible third-party APIs.

Performance: Implements multi-stage caching (Redis + PostgreSQL) and Usage Normalization logic for strict cost control.

Observability: A Leader-Shard-Scheduler manages cronjobs, system status monitoring, and aggregates data for internal Business Intelligence tools (/usage-overview).

2. Architecture: MECKs-Translator-Dashboard
Purpose: The administrative SaaS platform (built on Next.js 15) for bot configuration, subscription management, and monitoring key business metrics.

Tech Stack & Architectural Highlights:

Framework: Next.js 15 (App Router) with React 19 and NextAuth (Discord OAuth).

SaaS Features: Full Stripe integration for billing, subscriptions, and plan management.

System Interaction: Features real-time Cache Invalidation (via Upstash Redis), allowing the dashboard to instantly push configuration changes to the Core Bot across all regions.

Business Intelligence: Aggregates and displays critical product KPIs: revenue, cache-hit rates, active server trends, and user login patterns.

Background Jobs: Runs a dedicated Reminder-Worker process for scheduled notifications.

3. Architecture: MECKs-Translator-Landingpage
Purpose: The marketing and documentation gateway, featuring self-service demos, pricing, and a full wiki.

Tech Stack & Architectural Highlights:

Frontend: A Vite Multi-Page App (MPA) built with modular Vanilla JS and Tailwind 4.

Backend: A dedicated Express server provides static assets and two critical microservices:

Newsletter API: A funnel that validates and writes new subscribers directly to a PostgreSQL database.

Status-Polling API: Fetches live maintenance and incident banners from Upstash Redis (the same instance used by the Core Bot).

4. Architecture: MECKs-Translator-Demos
Purpose: A separate, interactive microsite that simulates the bot's core features (onboarding, translation, tickets) as a guided Discord-like experience to drive customer conversion.

Tech Stack & Architectural Highlights:

Frontend: A Vite multi-entry build with React 19 and i18next for full internationalization.

UI/UX: Recreates a Discord-native feel using custom components (Message, TypingIndicator) to build an ImmersiveShowcase.

Deployment: Served as a high-performance static site via Nginx on Fly.io.
