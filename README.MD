# Flime.ai - Your External Brain. Your Digital Discipline.

> **Flow meets Time. Build discipline through intelligent task management.**

**Status:** 📋 Planning Phase
**Target Launch:** March 15, 2026
**Stage:** Pre-MVP

---

## 🎯 What is Flime?

Flime.ai is a **SaaS task management and reminder app** designed specifically for people with **ADHD/ADD** and anyone struggling with organization and discipline.

Unlike generic task managers, Flime provides **aggressive multi-channel reminders** you can't ignore, helping you build sustainable habits and never miss what matters.

### The Problem We Solve

People with ADHD and those juggling multiple responsibilities forget important tasks, meetings, and commitments because:
- Existing apps are too complex
- Reminders are easy to ignore
- No clear separation between life areas (work, personal, health)
- No reliable sync with external calendars

### Our Solution

A simple web app that lets you:
- ✅ Create customizable boards by life area (Work, Personal, Health, etc.)
- ✅ Set tasks with aggressive reminders
- ✅ Receive alerts via email, web push, WhatsApp, SMS
- ✅ Sync bidirectionally with Google and Microsoft Calendar
- ✅ Affordable pricing for LATAM market

---

## 📁 Project Structure

```
flime/
├── docs/                           # 📚 Product Documentation
│   ├── product/
│   │   ├── vision-mission.md      # Vision, mission, values
│   │   ├── prd.md                 # Product Requirements Document
│   │   ├── mvp-scope.md           # MVP scope definition
│   │   └── pricing-strategy.md    # Pricing & monetization
│   ├── architecture/
│   │   ├── tech-stack.md          # Technology decisions
│   │   └── database-schema.md     # Database design
│   ├── user-research/
│   │   └── user-personas.md       # User personas
│   ├── api/                       # API documentation (future)
│   └── legal/                     # Terms, privacy (future)
│
├── planning/                       # 📅 Agile Planning
│   ├── roadmap/
│   │   └── 2026-q1.md             # Q1 2026 roadmap
│   ├── sprints/
│   │   ├── sprint-01.md           # Sprint planning documents
│   │   └── ...
│   └── retrospectives/            # Sprint retrospectives
│
├── design/                         # 🎨 Design Assets
│   ├── wireframes/                # Low-fidelity wireframes
│   ├── mockups/                   # High-fidelity designs
│   └── design-system/             # Design tokens, components
│
├── src/                           # 💻 Source Code (future)
│   └── (Next.js app will go here)
│
└── README.md                      # This file
```

---

## 🚀 Tech Stack

### Frontend
- **Next.js 15** (App Router, React Server Components)
- **TypeScript** (strict mode)
- **Tailwind CSS v4** (instant HMR)
- **shadcn/ui** (beautiful, accessible components)

### Backend
- **Next.js API Routes** (serverless)
- **Supabase** (PostgreSQL + Auth + Realtime)
- **Drizzle ORM** (type-safe queries)

### Integrations
- **Resend** (transactional emails)
- **Mercado Pago** (payments)
- **Vercel** (deployment + hosting)

### Monitoring
- **Sentry** (error tracking)
- **PostHog** (product analytics)

**Total MVP cost:** ~$2-5 USD/month

See [docs/architecture/tech-stack.md](docs/architecture/tech-stack.md) for details.

---

## 💰 Business Model

**Model:** Freemium SaaS B2C

### Plans

| Plan | Price | Key Features |
|------|-------|--------------|
| **FREE** | $0 | 2 boards, 15 active tasks, 1 email/day |
| **PREMIUM** | $5/mo or $50/yr | Unlimited boards & tasks, unlimited alerts, recurring reminders |
| **TEAM** (Phase 2) | $12/mo | Shared workspaces, 5 members included |

**Target:** 100 users (10 Premium) in first 3 months

See [docs/product/pricing-strategy.md](docs/product/pricing-strategy.md) for details.

---

## 🎯 MVP Scope (10 weeks)

**Core Features:**
1. ✅ Authentication (email/password + Google OAuth)
2. ✅ Boards CRUD (customizable by color/icon)
3. ✅ Tasks CRUD (title, description, due date, priority)
4. ✅ Calendar view (monthly)
5. ✅ Email reminders (Resend)
6. ✅ Web push notifications
7. ✅ Freemium logic (enforce limits)
8. ✅ Mercado Pago integration (payments)

**Out of Scope (Phase 2):**
- ❌ Google/Microsoft Calendar sync
- ❌ WhatsApp/SMS notifications
- ❌ Team workspaces
- ❌ Mobile native apps
- ❌ Dark mode

See [docs/product/mvp-scope.md](docs/product/mvp-scope.md) for details.

---

## 📅 Roadmap

### Q1 2026 (Jan-Mar) - MVP Launch
**Goal:** Ship functional MVP, get 100 users (10 Premium)

**Timeline:**
- **Sprint 1-2 (Wk 1-2):** Setup + Auth
- **Sprint 3-4 (Wk 3-4):** Boards + Tasks CRUD
- **Sprint 5-6 (Wk 5-6):** Calendar + Notifications
- **Sprint 7-8 (Wk 7-8):** Freemium + Payments
- **Sprint 9-10 (Wk 9-10):** Polish + Beta Testing
- **Launch:** March 15, 2026

### Q2 2026 - Growth & Iteration
- Integraciones calendario (Google/Microsoft)
- WhatsApp notifications (premium add-on)
- Mobile PWA improvements

### 2027 - Scale
- Team plan launch
- Mobile native apps (React Native)
- 10,000+ users, $5K MRR

See [planning/roadmap/2026-q1.md](planning/roadmap/2026-q1.md) for details.

---

## 👥 Target Audience

### Primary Personas

**1. Carlos el Desorganizado (28, Developer)**
- ADHD diagnosed
- Forgets meetings, deadlines
- Needs aggressive reminders he can't ignore
- Tech-savvy, values simplicity

**2. Ana la Multitasker (34, Freelance Designer)**
- Mother + professional
- Mental overload, needs to "externalize brain"
- Wants clear separation of life areas
- Mobile-first user

See [docs/user-research/user-personas.md](docs/user-research/user-personas.md) for details.

---

## 🏁 Success Metrics

### MVP (First 3 months)
- 100 registered users
- 10 Premium subscribers ($50 MRR)
- 30% retention at 30 days
- NPS > 40

### Year 1
- 1,000 active users
- 100 Premium ($500 MRR)
- 50% retention at 30 days
- Break even on costs

---

## 🧑‍💻 Development

**Commitment:** 14 hours/week (2h/day)
**Total MVP:** 140 hours over 10 weeks

### Current Status

- [x] Project structure created
- [x] Product documentation complete
- [x] MVP scope defined
- [x] Tech stack decided
- [x] Database schema designed
- [x] Q1 roadmap planned
- [ ] Development starting (Sprint 1)

### Next Steps (Sprint 1)
1. Initialize Next.js 15 project
2. Setup Supabase + Drizzle ORM
3. Configure Tailwind CSS v4 + shadcn/ui
4. Deploy to Vercel
5. Setup CI/CD

See [planning/sprints/sprint-01.md](planning/sprints/sprint-01.md) for Sprint 1 details.

---

## 📚 Documentation

All documentation is in the [docs/](docs/) folder:

**Product:**
- [Vision & Mission](docs/product/vision-mission.md) - Why we exist
- [PRD](docs/product/prd.md) - What we're building
- [MVP Scope](docs/product/mvp-scope.md) - Scope boundaries
- [Pricing Strategy](docs/product/pricing-strategy.md) - Monetization

**Architecture:**
- [Tech Stack](docs/architecture/tech-stack.md) - Technology decisions
- [Database Schema](docs/architecture/database-schema.md) - Data model (v1.1)
- [DB Summary](docs/architecture/DATABASE_SUMMARY.md) - Quick reference
- [DB Changelog](docs/architecture/CHANGELOG.md) - Schema evolution

**User Research:**
- [User Personas](docs/user-research/user-personas.md) - Who we're building for

**Planning:**
- [Q1 Roadmap](planning/roadmap/2026-q1.md) - 10-week plan
- [Sprint 1](planning/sprints/sprint-01.md) - First sprint

---

## 🎨 Design Philosophy

**Principles:**
1. **Simplicity over complexity** - No unnecessary features
2. **Empathy with users** - Designed for ADHD/ADD minds
3. **Aggressive reminders** - Can't ignore what matters
4. **Mobile-first** - Works everywhere
5. **Fast > Perfect** - Ship fast, iterate

**Visual Identity:**
- Calming colors (blues, greens)
- Clean typography (Inter/Geist)
- Generous whitespace
- No distractions

---

## 🛠️ How to Contribute (Future)

This is currently a **solo project** by the founder.

When open for contributions:
- Fork the repo
- Create a feature branch
- Make your changes
- Submit a PR

---

## 📄 License

**Proprietary** - All rights reserved.

This is a commercial product. Code will be closed-source.

---

## 📞 Contact

**Founder:** [Your Name]
**Email:** hello@flime.ai (future)
**Website:** https://flime.ai (future)

---

## 🙏 Acknowledgments

**Inspiration:**
- Todoist, TickTick (task management)
- Reclaim.ai, Motion (AI scheduling)
- The ADHD community on Reddit

**Built with:**
- Next.js by Vercel
- Supabase
- shadcn/ui by shadcn

---

**Last Updated:** December 30, 2024
**Version:** 0.1.0-alpha (Pre-MVP)

---

> "The motivation is lost, but constancy and discipline are not."
>
> — Flime Manifesto

**Let's build something that matters.** 🚀
