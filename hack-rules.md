# Hackathon Team Rules & Guidelines
## Pirates of the Coral-bean | WeMakeDevs x Coral

---

## 1. Team Charter

**Project:** The Knowledge Archaeologist  
**Track:** Enterprise Agent (Track 1)  
**Hackathon Dates:** May 25 – May 31, 2026  
**Team Size:** Up to 4 members  
**Goal:** Win Track 1 Grand Prize + Stack Special Bounties

---

## 2. Team Roles

### Role Definitions

| Role | Responsibilities | Ideal Skills |
|------|------------------|--------------|
| **Captain (Tech Lead)** | Architecture decisions, SQL design, Coral integration, unblocks team | SQL, backend, systems thinking |
| **First Mate (Frontend)** | UI/UX, search interface, results dashboard, demo polish | React/Next.js, Tailwind, design |
| **Navigator (LLM/AI)** | Natural language → SQL translation, prompt engineering, answer generation | LLM APIs, Python, prompt design |
| **Quartermaster (Ops/Content)** | Source spec building, blog writing, social posts, submission packaging | Writing, DevRel, documentation |

### Rules for Roles
1. **Primary ownership:** Each person owns their role but must document their work so others can cover if needed.
2. **No silos:** Every role must understand the full project enough to demo it.
3. **Rotation allowed:** If someone is stuck for >2 hours, swap tasks or pair program.

---

## 3. Communication Rules

### Channels
- **Discord (Coral server):** Public questions, source spec discussions, community help
- **Team Group Chat:** Daily standups, blockers, quick decisions
- **Voice/Video:** Deep technical discussions, pair programming, demo rehearsal

### Daily Standup (Async, Text-Only)
Post in group chat every morning by 10 AM:
```
Yesterday: What I completed
Today: What I'm working on
Blockers: What is stopping me
```

### Response Time Expectations
| Urgency | Response Time | Channel |
|---------|---------------|---------|
| Critical (demo broken, blocker) | < 15 minutes | Group chat + call |
| High (PR review needed) | < 2 hours | Group chat |
| Normal (questions, updates) | < 6 hours | Group chat |
| Low (ideas, future work) | < 24 hours | Any |

---

## 4. Development Workflow

### Branching Strategy
```
main          → Production-ready, demo branch
├── feat/sql-core          → Captain
├── feat/ui-search         → First Mate
├── feat/llm-router        → Navigator
├── feat/source-specs      → Quartermaster
└── docs/blog-guide        → Quartermaster
```

### Commit Rules
- **Format:** `type(scope): description`
- **Examples:**
  - `feat(sql): add temporal join for github + slack`
  - `fix(ui): citation links not opening in new tab`
  - `docs(prd): update data model section`
- **No direct pushes to main.** All code goes through PR.

### PR Rules
1. **Self-review first:** Run the code before requesting review.
2. **PR description template:**
   ```
   What changed
   Why
   How to test
   Screenshots (if UI)
   ```
3. **Review turnaround:** 2 hours max during hackathon hours.
4. **Merge criteria:** 1 approval + CI passes (if any) + no merge conflicts.

---

## 5. Code Standards

### SQL Standards (Coral Queries)
- **Use CTEs** for readability on complex multi-source JOINs
- **Always use `LEFT JOIN`** when a source might not have matching data
- **Comment temporal logic:** Explain why `BETWEEN` or `>=` is used
- **Limit results:** Always add `LIMIT` to prevent massive result sets during dev
- **Template queries:** Store working query patterns in `/queries/templates/`

### Frontend Standards
- **Mobile-first:** Demo might be on a laptop, but UI must work on any screen
- **Loading states:** Every async action needs a skeleton or spinner
- **Error states:** If Coral fails, show a friendly error with retry button
- **Accessibility:** Minimum WCAG 2.1 AA (proper contrast, keyboard navigation)

### Python/LLM Standards
- **Type hints:** All functions must have type annotations
- **Docstrings:** Every function needs a 1-line description
- **No hardcoded secrets:** Use `.env` files; never commit tokens
- **Prompt versioning:** Save prompt templates as files, not inline strings

---

## 6. Time Management Rules

### Core Hours (Mandatory Availability)
| Day | Hours | Activity |
|-----|-------|----------|
| All days | 10:00 – 14:00 | Focused work blocks (no meetings) |
| All days | 14:00 – 15:00 | Lunch + async standup |
| All days | 15:00 – 19:00 | Collaboration, PR reviews, pairing |
| All days | 19:00 – 20:00 | Demo check-in (show what works) |

### The "Shutdown Rule"
- **No new features after Day 5 (May 29, 19:00).**
- Day 6–7 is for polish, bug fixes, blog writing, and demo rehearsal only.
- Breaking this rule risks an unstable demo.

### Sleep Rule
- **Minimum 6 hours sleep per night.** No all-nighters after Day 3.
- A tired team makes bad decisions and bad demos.

---

## 7. Decision Making

### Decision Matrix
| Type | Who Decides | How | Time Limit |
|------|-------------|-----|------------|
| Architecture | Captain | Consult team, final call | 30 minutes |
| UI/UX | First Mate | Captain consults | 15 minutes |
| LLM approach | Navigator | Team input | 15 minutes |
| Blog/content | Quartermaster | Team reviews | 20 minutes |
| Scope cuts | Captain + Team | Vote if split | 10 minutes |
| Demo script | Whole team | Collaborative | 1 hour |

### Disagreement Resolution
1. **State the options clearly** (2 minutes)
2. **Each person argues for their pick** (1 minute each)
3. **Captain makes the call** (1 minute)
4. **Disagree and commit:** Once decided, full support. No back-channel complaints.

---

## 8. Scope & Cutting Rules

### The "MVP First" Principle
We build the **smallest version that wins the demo**, then add polish.

### Cutting Priority (Cut from bottom up if behind schedule)
1. ✅ **Keep:** Core SQL query (GitHub + Slack + 1 more source)
2. ✅ **Keep:** Natural language → SQL
3. ✅ **Keep:** Cited answer generation
4. ✅ **Keep:** Basic search UI
5. ⚠️ **Cut if needed:** Notion source integration
6. ⚠️ **Cut if needed:** Custom source spec (unless bounty critical)
7. ⚠️ **Cut if needed:** Blog guide (can write post-hackathon)
8. ❌ **Cut if needed:** Advanced UI animations
9. ❌ **Cut if needed:** Additional sources beyond 4

### The "2-Minute Demo" Test
Every feature must answer: *"Does this make the 2-minute demo better?"* If no, it is a P2.

---

## 9. Demo & Submission Rules

### Demo Preparation
- **Rehearse 5 times minimum.** The 5th run should be flawless.
- **Prepare 3 "golden" questions** with known good answers. Never demo with unknown data.
- **Have a backup plan:** If live query fails, have screenshots/video of it working.
- **Time the demo:** Use a stopwatch. Cut content if over 2 minutes.

### Submission Checklist (Day 7)
- [ ] Project submitted on hackathon portal
- [ ] GitHub repo public with README
- [ ] README includes: What it does, How to run, Demo video/screenshots
- [ ] Coral SQL queries documented in `/queries/`
- [ ] Blog guide published (if pursuing bounty)
- [ ] Social post shared tagging @withcoral and @WeMakeDevs
- [ ] Discord #show-and-tell post with screenshots
- [ ] Team registration confirmed
- [ ] Demo video recorded (2 minutes, uploaded to YouTube or Loom)

### Judging Criteria Checklist
| Criteria | How We Hit It |
|----------|---------------|
| 🏴‍☠️ Potential Impact | Show 4-hour → 10-second knowledge retrieval |
| ⚓ Creativity & Originality | "Live archaeology" concept; self-reconstructing docs |
| 🗺️ Learning & Growth | Document our Coral learning curve in blog/demo |
| ⚔️ Technical Implementation | Show the SQL. Show the JOINs. Show it's local. |
| 🎨 Aesthetics & UX | Clean search bar, cited cards, provenance links |
| 🪸 Best Use of Coral | One query, 4+ sources, temporal JOINs, zero glue code |

---

## 10. Bounty Stacking Rules

### Bounty Ownership
| Bounty | Owner | Deadline | Deliverable |
|--------|-------|----------|-------------|
| Track 1 Grand Prize | Whole Team | Day 7 | Demo + submission |
| Source Spec Bounty | Quartermaster | Day 5 | PR to Coral repo |
| Blog Guide Bounty | Quartermaster | Day 6 | Published blog post |
| Social Showcase | First Mate | Day 6 | LinkedIn/X post + Discord |
| Early Bird Swag | Captain | Day 1 | Registration + share |

### Bounty Priority
1. **Main track prize is the goal.** Bounties are bonuses.
2. **Source spec is the highest-value bonus** — it also improves the main project.
3. **Blog can be written post-hackathon** if time runs out, but submit the link before deadline.

---

## 11. Conflict Resolution

### If Team Members Disagree
1. **Take it to text first.** Don't argue in voice while others are working.
2. **Frame it as problem + options.** Not "you're wrong."
3. **Use the decision matrix above.**
4. **If still stuck:** Sleep on it. 90% of hackathon arguments look silly in the morning.

### If Someone is Falling Behind
1. **Ask: "What do you need?"** not "Why aren't you done?"
2. **Pair program.** Two people on one task is better than one person stuck for 6 hours.
3. **Reassign scope.** Move their tasks to someone else or cut them.
4. **No blame.** Hackathons are hard. Support > shame.

### If We Want to Add a New Member
- **Max 4 people.** Adding a 5th disqualifies the team.
- **New member must read this doc before contributing.**
- **Rebalance roles immediately.** Don't have 2 people doing the same thing.

---

## 12. Emergency Contacts

| Resource | Where to Find |
|----------|---------------|
| Coral Help | Discord #help channel |
| Coral Docs | https://docs.coral.io |
| Coral GitHub Issues | https://github.com/withcoral/coral/issues |
| WeMakeDevs Contact | contact@wemakedevs.org |
| Hackathon FAQ | https://wemakedevs.org/events/hackathons/coral |

---

## 13. Team Signatures

By participating, every team member agrees to:
- [ ] Read and follow this document
- [ ] Communicate blockers within 2 hours
- [ ] Respect the shutdown rule (no new features after Day 5)
- [ ] Support teammates, especially when stressed
- [ ] Prioritize the 2-minute demo over perfect code
- [ ] Have fun and learn something new

| Name | Role | Signature (Type Name) | Date |
|------|------|----------------------|------|
| | Captain | | |
| | First Mate | | |
| | Navigator | | |
| | Quartermaster | | |

---

*Document Version: 1.0*  
*Last Updated: May 30, 2026*  
*"Hoist the Jolly Roger and set sail on the high seas of data!"*
