# HiringSignals

**Type:** Human-in-the-loop Python Workflow (browser-driven)
**Skill Domain:** CareerOS
**Command:** `python main.py signals --people-search --step scan|rank|report`

## What It Does

Finds hiring managers who are *currently attached to an open req* and turns them into a short, ranked list of people worth writing to this week — then stops, and hands the judgment back to a human.

Most job search tooling optimizes for finding more roles. This one assumes discovery was never the bottleneck.

## Why a people search, not a post search

The obvious approach is to search for posts that say "I'm hiring." That was tried first and it failed: eight post-searches returned almost entirely offshore recruiter spam and **zero** qualifying local hiring managers.

The better entry point is LinkedIn's *"actively hiring for &lt;title&gt;"* people filter — a roster of individuals the platform has flagged as attached to an open req. Same effort, roughly **10x** the qualifying contacts.

## How It Works

Three steps, deliberately separated so a human sits in the middle.

1. **Scan** — builds a geography-scoped search URL and emits the extraction script. Filtering happens *server-side*: a quarter of one manual run was fetched and then discarded for being outside the target metros, so the geo and network-degree filters now live in the URL rather than in post-processing.
2. **Rank** — applies the deterministic gates (recruiter, junior, non-function, geography), dedupes against the applications pipeline with a cap-aware penalty, looks up target-company tier, scores, and returns roughly thirty profiles worth opening.
3. **Report** — merges the human's profile reads back in, scores them through the existing signal pipeline, and writes an immutable archived report.

## The design decision that matters: what is *not* automated

The platform's "hiring" badge is derived from internal job data, not from anything the person said. On the run this workflow was built from, of 32 profiles opened by hand:

- **13 had no hiring post at all**
- **5 were hiring for something else entirely** — a payments operations role, a head of marketing, and one where the post said the job had already been filled

No rule catches that. So reading the profile, and judging whether the role is actually a fit, stays human — permanently, by design. The workflow automates the mechanical filtering underneath and refuses to automate the part that requires reading.

The same principle governs the runtime: it drives a live, logged-in browser session and is never scheduled. A blocked request and an expired session are indistinguishable from "no results," and a job that fails silently is worse than no job at all.

## Honest Yield

From one full sweep:

| Stage | Count |
|---|---|
| People scanned | 149 |
| Passed the gates | 80 |
| Profiles opened by hand | 32 |
| Real signals | 11 |
| **Applications sent** | **2** |

Two applications per sweep is the honest number. Running it daily would deepen a backlog, not increase output — each application costs a tailored resume, a cover letter and a researched message. The cadence is weekly, timed to catch mid-week posts while they are still one to three days old.

## Design Decisions

- **The knowledge lives in the command, not a doc.** Hard-won browser gotchas print on screen the moment before a sweep starts. They had previously been written into a 1000-line learnings file — and were rediscovered the hard way anyway. Documentation nobody opens at the moment of need does not work.
- **Server-side filters over client-side gates**, wherever the platform allows it.
- **Cap-aware ranking.** A company already at the application limit is demoted out of contention rather than nudged down a place — a flat penalty was not enough to stop a saturated employer sorting first.
- **Match on the current employer, never the whole headline.** "Director of Product @ Cantina | Ex-Uber, Amazon, TikTok" contains four companies and only the first is current; naive matching credited the wrong one and dropped the best lead from first to eighth.
- **Review-first.** Nothing is written to the pipeline and nothing is ever sent automatically.

## Related

[JobHunter](../job-hunter/) sweeps roles from job boards; this sweeps *people*. [NetworkingScout](../networking-scout/) maps contacts at a company you have already chosen; this finds the companies by following the people. Output feeds [ApplicationBlitz](../application-blitz/).
