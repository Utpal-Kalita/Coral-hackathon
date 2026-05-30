# Product Requirements Document (PRD)
## The Knowledge Archaeologist
### Pirates of the Coral-bean Hackathon | WeMakeDevs x Coral

---

## 1. Executive Summary

**The Knowledge Archaeologist** is an enterprise AI agent that reconstructs institutional knowledge by querying across GitHub, Slack, Linear, Sentry, and Notion using a single SQL interface powered by Coral. When engineers ask "Why does our system do X?", the agent joins live data from multiple sources and returns a contextual, cited answer with full provenance.

**Tagline:** *"Your company's memory, queryable in one SQL statement."*

---

## 2. Problem Statement

### The Pain Point
Engineering teams lose 3–6 hours per week per developer searching for context buried across disparate tools:
- **GitHub:** Code changes, PR descriptions, review comments
- **Slack:** Architecture decisions, incident discussions, team debates
- **Linear/Jira:** Issue history, status changes, ticket links
- **Sentry:** Error patterns, first-seen dates, affected services
- **Notion:** Documentation that is often stale or incomplete

When senior engineers leave, get sick, or simply wrote code 18 months ago, this tribal knowledge evaporates. Current documentation is static and dies the moment it is written.

### Impact
- **Onboarding cost:** New engineers take 2–4 weeks to understand system rationale
- **Incident resolution:** Root cause analysis delayed by knowledge gaps
- **Re-work:** Teams rebuild solutions because they cannot find the original reasoning
- **Decision paralysis:** Fear of changing unknown systems leads to technical debt accumulation

---

## 3. Product Goals

### Primary Goal
Build an agent that can answer any "Why does our system...?" question by reconstructing the answer from live data across 4+ sources in under 10 seconds.

### Secondary Goals
1. Demonstrate the power of Coral's cross-source SQL JOINs
2. Prove that institutional knowledge can be self-reconstructing rather than manually documented
3. Create a reusable pattern for enterprise agent builders
4. Win the Track 1 (Enterprise Agent) grand prize

### Success Metrics
| Metric | Target |
|--------|--------|
| Query response time | < 10 seconds |
| Sources joined per query | ≥ 4 |
| Answer accuracy (human-rated) | ≥ 80% |
| Demo question answered end-to-end | 2 minutes |
| GitHub stars on project repo | ≥ 50 |

---

## 4. User Personas

### Persona 1: The New Engineer (Alex)
- **Role:** Junior Backend Engineer, 2 weeks into the job
- **Pain:** "I see a weird retry logic in the payment service. Nobody on my team knows why it's there."
- **Need:** Fast, trusted answers with links back to original discussions
- **Usage:** 3–5 queries per day during onboarding

### Persona 2: The On-Call Engineer (Priya)
- **Role:** Senior SRE, handling a production incident at 2 AM
- **Pain:** "This service is failing. I need to know when it was last changed and what the team discussed."
- **Need:** Incident context correlated across deploys, errors, and Slack threads
- **Usage:** 1–2 urgent queries during incidents

### Persona 3: The Tech Lead (Marcus)
- **Role:** Engineering Manager, planning a refactor
- **Pain:** "I want to remove this legacy feature but I don't know what depends on it or why it was added."
- **Need:** Historical decision trace with risk assessment
- **Usage:** 1 query per major decision

---

## 5. Core Features

### Feature 1: Natural Language to Coral SQL
- **Description:** User types a question in plain English. The agent translates it into a Coral SQL query.
- **Example:**
  - Input: "Why do we retry webhooks 7 times?"
  - Output SQL: Cross-source JOIN across GitHub, Slack, Linear, Sentry
- **Priority:** P0 — Critical

### Feature 2: Cross-Source Temporal JOINs
- **Description:** The agent correlates events across time (e.g., Slack discussions *between* PR creation and merge).
- **Example:** Find Slack messages in #architecture from Nov 1–15, 2024, that mention "webhook" and correlate with PRs merged in that window.
- **Priority:** P0 — Critical

### Feature 3: Cited Answer Generation
- **Description:** The agent returns a human-readable paragraph with inline citations linking back to source data (PR, Slack thread, Linear issue).
- **Example output:**
  > "The webhook retry logic (7 attempts) was introduced in [PR #442](link) to handle Stripe rate-limiting during Black Friday 2024. The [#architecture thread](link) shows the team debated 3 vs 7 retries—7 won because Stripe's docs recommend 6+ for 429s. There is an active [Linear issue #881](link) to reduce this to 5 after the infrastructure upgrade."
- **Priority:** P0 — Critical

### Feature 4: Provenance Dashboard
- **Description:** A simple web UI with a search bar and result cards showing the reconstructed answer, raw SQL, and source links.
- **Priority:** P1 — High

### Feature 5: Source Spec Builder (Bounty Stack)
- **Description:** If a required source is missing from Coral, build and submit a custom source spec to earn the Special Bounty.
- **Priority:** P2 — Medium (if time permits)

---

## 6. Technical Architecture

### System Diagram
```

┌─────────────────┐
│   User Query    │
│  "Why do we..." │
└────────┬────────┘
│
▼
┌─────────────────┐     ┌─────────────────┐
│   LLM Router    │────▶│  Coral SQL Query  │
│ (Claude/Cursor) │     │   Generator       │
└─────────────────┘     └────────┬────────┘
│
┌────────────┼────────────┐
▼            ▼            ▼
┌─────────┐  ┌─────────┐  ┌─────────┐
│  GitHub │  │  Slack  │  │  Linear │
│  Source │  │  Source │  │  Source │
└────┬────┘  └────┬────┘  └────┬────┘
│            │            │
└────────────┼────────────┘
▼
┌─────────────────┐
│  Coral Engine   │
│ (Local, 100%)   │
│  JOIN + Resolve │
└────────┬────────┘
│
▼
┌─────────────────┐
│  Result Cards   │
│  (Cited Answer) │
└─────────────────┘

```

### Tech Stack
| Layer | Technology |
|-------|------------|
| Query Interface | Next.js + Tailwind CSS |
| LLM Orchestration | Claude via MCP (Coral integration) |
| Data Layer | Coral CLI / Coral MCP Server |
| Sources | GitHub, Slack, Linear, Sentry, Notion |
| Deployment | Local-first (100% local credentials) |
| Optional | Vercel for demo frontend |

---

## 7. Data Model & SQL Schema

### Core Query Pattern
```sql
SELECT 
    pr.title AS decision,
    pr.body AS technical_rationale,
    pr.merged_at AS when_decided,
    sl.text AS team_discussion,
    sl.timestamp AS discussed_when,
    sl.permalink AS slack_link,
    l.title AS related_issue,
    l.status AS issue_status,
    l.url AS linear_link,
    s.title AS error_title,
    s.first_seen AS error_date,
    s.count AS error_frequency
FROM github.pull_requests pr
LEFT JOIN slack.messages sl 
    ON sl.channel = '#architecture'
    AND sl.text ILIKE '%webhook%'
    AND sl.timestamp BETWEEN pr.created_at AND pr.merged_at
LEFT JOIN linear.issues l 
    ON l.title ILIKE '%webhook%'
    AND l.created_at >= pr.merged_at
LEFT JOIN sentry.issues s 
    ON s.title ILIKE '%webhook%'
    AND s.first_seen >= pr.merged_at
WHERE pr.merged_at > NOW() - INTERVAL '12 months'
  AND (pr.title ILIKE '%webhook%' OR pr.body ILIKE '%webhook%')
ORDER BY pr.merged_at DESC
LIMIT 10;
```

Source Configuration

SourceTableKey FieldsAuth
GitHub`pull_requests``title`, `body`, `merged_at`, `created_at`GitHub PAT
Slack`messages``text`, `timestamp`, `channel`, `permalink`Slack Bot Token
Linear`issues``title`, `status`, `created_at`, `url`Linear API Key
Sentry`issues``title`, `first_seen`, `count`, `level`Sentry Auth Token
Notion`pages``title`, `content`, `last_edited`Notion Integration Token

---

8. User Interface

Screen 1: Search Bar (Home)
- Centered search input with placeholder: "Why does our system do X?"
- Example queries below as clickable chips:
  - "Why do we retry webhooks 7 times?"
  - "When did we switch from JWT to sessions?"
  - "What caused the payment outage in March?"

Screen 2: Results View
- Top: Reconstructed answer paragraph with inline citations
- Middle: "View SQL" toggle showing the Coral query
- Bottom: Source cards with icons (GitHub, Slack, Linear, Sentry) showing raw snippets and deep links

Screen 3: Source Detail (Modal)
- Full content of the source item (e.g., full Slack thread, full PR description)
- Timestamp and author
- Direct link to open in native app

---

9. Demo Script (2 Minutes)

TimeActionWhat to Say
0:00Show problem"Our senior engineer left. Nobody knows why the auth service uses JWT instead of sessions."
0:15Type question"Let's ask the Knowledge Archaeologist."
0:30Reveal SQL"This is the Coral query joining GitHub, Slack, and Linear in real time."
0:45Show answer"It found the 2024 architecture debate, the PR that implemented it, and the Linear issue tracking the migration."
1:00Show citations"Every claim has a source. Click any link to see the original thread."
1:15Show local execution"All credentials and data stay on this machine. Zero cloud leakage."
1:30The close"This used to take 4 hours of digging. Now it's one SQL query. This is what Coral was built for."

---

10. Risks & Mitigations

RiskLikelihoodImpactMitigation
Coral source not available for a toolMediumHighBuild custom source spec (also wins bounty)
Rate limits during demoLowHighCache schema and sample data locally before demo
LLM generates invalid SQLMediumMediumAdd SQL validation layer; fallback to simpler query
Team member drops outLowHighDocument everything; pair program critical paths
Demo data is messy/incompleteMediumMediumPrepare 2–3 "golden" demo questions with known answers

---

11. Bounty Stacking Plan

BountyRequirementOwnerStatus
Track 1 Grand PrizeBest Enterprise AgentWhole TeamTarget
Special Bounty: Source SpecBuild 1+ custom sourceTBD100 + 50 charity
Special Bounty: Blog Guide2–3 page reproducible guideTBDKeychron keyboard
Early Bird SwagRegister + share on socialWhole TeamSwag box lottery
Social ShowcaseDiscord #show-and-tell + LinkedIn/XTBDClaude Max vouchers

---

12. Timeline (May 25–31)

DayFocusDeliverable
Day 1 (Sat)Setup & Coral installAll sources connected locally
Day 2 (Sun)Core SQL queries3 working cross-source JOINs
Day 3 (Mon)LLM integrationNatural language → SQL working
Day 4 (Tue)UI buildSearch bar + results page
Day 5 (Wed)Polish & citationsCited answer generation
Day 6 (Thu)Blog + source specSubmission prep + bonus bounties
Day 7 (Fri)Demo rehearsal2-minute pitch perfected

---

13. Appendix

A. Coral Installation

```bash
brew install withcoral/tap/coral
coral source add github
coral source add slack
coral source add linear
coral source add sentry
```

B. Resources
- [Coral Docs](https://docs.coral.io)
- [Coral GitHub](https://github.com/withcoral/coral)
- [Coral Discord](https://discord.gg/coral)
- [Hackathon Page](https://wemakedevs.org/events/hackathons/coral)

---

Document Version: 1.0
Last Updated: May 30, 2026
Authors: [Team Name TBD]
