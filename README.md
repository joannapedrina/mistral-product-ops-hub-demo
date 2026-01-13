# Product Operations Hub

A product operations dashboard showing how teams can centralize feedback, track launches, and stay aligned across Engineering, Sales, and Leadership.

**[View Live Demo](https://joannapedrina.github.io/mistral-product-ops-hub-demo/)**

## What This Is

**This is a UI prototype + technical spec.**

The demo shows the user experience with sample data. The docs below describe how you'd build the real thing.

| Layer | Status |
|-------|--------|
| UI/UX Design | Done |
| Sample Data | Done |
| Backend API | Spec only |
| Database | Spec only |
| AI Integration | Spec only |
| Connectors | Spec only |

## The Problem

| Challenge | Solution |
|-----------|----------|
| Feedback scattered across tools | Unified dashboard pulling from all sources |
| Sales doesn't know what's shipping | Launch calendar with status filters |
| Launch readiness unclear | Checklists with team playbooks |
| No single source of truth | Hub connecting all views |

## Architecture

```
                    PRODUCT OPERATIONS HUB
    +--------------------------------------------------+
    |                                                  |
    |   Feedback       Launch         Launch           |
    |   Dashboard      Calendar       Checklists       |
    |       |             |              |             |
    |       +-------------+-------------+              |
    |                     |                            |
    |               Unified API                        |
    |                     |                            |
    +---------------------|----------------------------+
                          |
        +-----------------+------------------+
        |                 |                  |
    Intercom          Linear             Gong
    Zendesk           Jira               Chorus
    Support           GitHub             Calls
```

## Components

### Product Operations Hub
Main command center with tabs:
- Overview: key metrics
- Feedback Dashboard: aggregated customer feedback
- Launch Calendar: interactive roadmap
- Launch Checklists: team playbooks

### Feedback Dashboard
Pulls from multiple sources:

| Support | Product | Sales |
|---------|---------|-------|
| Intercom | Linear | Gong recordings |
| Zendesk | GitHub Issues | Chorus transcripts |
| Help Scout | Canny | CRM notes |

**AI does the heavy lifting:**
- Categorizes: Bug, Feature Request, Improvement, Question
- Scores sentiment: Positive/Neutral/Negative
- Suggests priority based on customer tier + severity
- Tags with technical keywords and product areas
- Detects duplicates using embeddings

**Customer enrichment:**
- Looks up company, ARR, plan from CRM
- Boosts priority for at-risk customers
- Shows historical context (previous tickets, requests)

### Launch Calendar
Two views:
- Calendar: 12-month grid
- Timeline: quarterly breakdown

Filters by status (Shipped, Beta, In Dev, At Risk) and team (Engineering, ML, Product).

Syncs with Linear/Jira for real status. Links back to customer requests.

### Launch Checklists

| Launch Type | Complexity |
|-------------|------------|
| Major Model Release | High |
| API/Platform Update | Medium |
| Enterprise Feature | Medium |
| Experimental/Beta | Low |
| Bug Fix / Hotfix | Low |
| Documentation | Low |

Each team gets their own checklist:
- Engineering: tests, docs, monitoring, rollback plan
- Marketing: blog, social, email, landing page
- Sales: battlecards, demo, pricing, FAQ
- Legal: ToS, privacy, compliance
- Support: KB articles, training, escalation paths

## Data Flow

**Feedback to Roadmap:**
```
Customer submits feedback (Intercom)
    |
    v
AI triage: categorize, score, detect duplicates
    |
    v
Feedback Dashboard: aggregate by theme, track votes
    |
    v
PM reviews top requests
    |
    v
Creates Linear project --> appears in Launch Calendar
    |
    v
Generates checklist --> Launch Checklists
```

**Launch to Customer Notification:**
```
Feature ships
    |
    v
Query: find all customers who requested this
    |
    v
Send: in-app message, email, Intercom campaign
```

## Connector Pattern

Each source uses an adapter:

```javascript
interface DataSourceAdapter {
  authenticate(): Promise<void>;
  fetchFeedback(params): Promise<FeedbackItem[]>;
  fetchIncremental(since: Date): Promise<FeedbackItem[]>;
  transformToStandard(raw): FeedbackItem;
}
```

Sync strategies:
- Batch: daily at 2am for backfill
- Incremental: every 15 min for recent updates
- Webhook: real-time for new items

## Unified Data Model

All feedback normalizes to:

```javascript
{
  id: "fb_12345",
  source: "intercom",
  source_id: "conv_abc123",
  title: "Need CPU inference support",
  description: "We can't use GPUs in our edge deployment...",
  category: "feature_request",
  priority: "high",
  sentiment_score: -0.3,
  customer: {
    id: "cust_789",
    email: "user@company.com",
    company: "Acme Corp",
    tier: "enterprise",
    arr: 50000,
    health_score: 72
  },
  tags: ["cpu", "edge", "deployment"],
  product_area: "inference",
  created_at: "2026-01-10T14:30:00Z",
  status: "open"
}
```

## Tech Stack (Production)

| Layer | Tech |
|-------|------|
| Frontend | Next.js 15, React, TypeScript |
| UI | Shadcn/ui, Tailwind |
| Charts | Recharts |
| State | Zustand, TanStack Query |
| Backend | Next.js API Routes |
| Database | PostgreSQL |
| Cache | Redis (Upstash) |
| Queue | BullMQ |
| AI | Mistral API |
| Auth | Auth.js |
| Hosting | Vercel |

## Design Principles

**Clarity over complexity**
- Show summary first, details on click
- Max 3-4 metrics per screen
- No jargon

**Honest data**
- Y-axis starts at 0
- Show sample sizes
- Display when data was last updated

**Actionable**
- Every metric links to underlying data
- Priority = Impact x Urgency x Customer Value

## Files

```
index.html                       # Landing page
product-ops-hub.html             # Main hub
dashboard-mockup-v4-real-data.html   # Feedback dashboard
interactive-launch-calendar.html     # Calendar
launch-checklist.html            # Checklists
README.md                        # This file
```

## Try It

1. Open the Hub
2. Check the Overview tab
3. Explore Feedback (filter by source, category)
4. Check Calendar (toggle views, filter by team)
5. View Checklists (pick a launch type)

## Future

- [ ] Real-time updates via WebSocket
- [ ] Slack bot
- [ ] Custom dashboard builder
- [ ] Mobile views
- [ ] Dark mode

Built with HTML, CSS, vanilla JS.

*Sample data for demo purposes. Example use case: Mistral AI.*
