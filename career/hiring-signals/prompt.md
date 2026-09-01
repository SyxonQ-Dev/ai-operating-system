# HiringSignals — Workflow Prompt

## Purpose
Find hiring managers currently attached to an open req, rank them, and produce a short outreach list — while keeping the judgment calls with a human.

## Core Functionality

### Input
- A job-title facet (the platform's coarse standardized-title list)
- Target metros, read from the shared geography config
- The applications pipeline and company watchlist, for dedupe and tier lookup

### Processing
1. Build a geography- and network-scoped search URL
2. Page the results in a live browser session, accumulating rows client-side
3. Gate out recruiters, junior roles, off-function titles and out-of-scope geographies
4. Dedupe against the pipeline; demote companies at or over the application cap
5. Score on target tier, seniority signal, metro, role lane and shared connections
6. **Hand off ~30 profiles for a human to read**
7. Merge the human's reads back in, score, and archive an immutable report

### Output
- A ranked shortlist with the reasoning behind each score
- A signals report with drafted openers for the qualifying contacts
- An append-only run ledger, so no sweep can be lost or overwritten

## Triggers
- "Run signals"
- "Who's hiring PMs this week?"
- "Check for hiring managers"

## Constraints
- **Never runs headless or on a schedule.** Silent failure is indistinguishable from an empty result.
- **Never trusts the hiring badge.** Roughly 40% of flagged profiles have no post, or a post for a different role.
- **Never writes to the pipeline and never sends.** Every message is drafted for review.
- Weekly cadence. The roster turns over slowly; post freshness is what decays.
