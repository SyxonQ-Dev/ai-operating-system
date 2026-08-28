# ApplicationBlitz — Workflow Prompt

## Purpose

Turn a job posting into application materials that argue for one impression and survive an interview — a tailored resume, an edit checklist for the canonical Google Doc, a cover letter, and an honest ATS coverage report.

## Triggers

- `"Generate materials for [job URL]"`
- `"Run Blitz for [company]"`
- `"Tailor my resume for this role"`

## Core Functionality

### Input

- A job posting (URL, a pasted description, or a pipeline entry)
- Company context from the Companies database — tier, archetype, warmth
- The bullet bank: every approved bullet, with themes, strength, protected facts, provenance

### Processing

**① Narrative — who should the reader see?**

Classify the role, then route to one of four stories: zero-to-one, growth, monetization, or AI-native. Set the layout profile in the same pass — founder requisitions and AI-native companies promote startup work out of Education so it leads the page.

Roles that classify as genuinely misaligned (platform / infrastructure) return no narrative and **abort**. State the reason; do not invent a story to fill the gap.

**② Composer — which evidence, in what order?**

1. Score every *approved* bullet against the narrative and the JD's terms. Deterministic, no model.
2. Hand the model a ranked shortlist per company. It picks and orders, with a stated reason per pick.
3. Enforce in code afterwards: drop unknown IDs and duplicates, force-include pinned credentials, apply per-company line bounds, hold the total page budget by evicting the lowest-scoring picks, and keep cross-company order reverse-chronological.

Any unusable response falls back to deterministic selection.

**③ Tailor — how does each sentence read?**

Tune each selected bullet to the posting's vocabulary inside its character ceiling. Pass coverage gaps in as explicit targets, computed *before* this step. Retry over-budget rewrites once, asking for exactly the needed cut.

### Output

Written to the applications folder, one directory per role:

- `resume_tailored.md` — the pasteable resume
- `edit_checklist.md` — Current/Proposed diffs for the canonical Google Doc
- `cover_letter.md`
- `analysis.md` — narrative routing, ATS coverage, declined terms, fit warnings
- `changes.md` — what changed and why, per bullet

## Non-negotiables

**Protected facts survive verbatim.** A rewrite that drops or alters a protected number is rejected and the approved text ships instead.

**Character ceilings hold.** Bullets keep their rendered line count at real font size and margins.

**Fixed slots are immutable.** Awards, credentials, and contact details are never reworded.

**Unapproved bullets are invisible.** Drafts cannot reach a resume.

**Any gate failure degrades to the approved baseline.** Never to damaged text.

**Terms that cannot be placed truthfully are declined, not forced.** Report them. Several *core* requirements declined is real evidence the role is a stretch — surface that before an application is spent on it.

**One number everywhere.** When sources disagree on a figure, settle it in the bank as a canonical fact and record what it supersedes, so the resume and the interview never contradict each other.

## Success Criteria

- The resume argues one impression, not a list of everything
- Every claim traces to an approved bullet
- The page fits its line budget at real formatting
- ATS coverage is measured before and after, with the delta reported
- Declined terms are surfaced, not hidden
- A hostile or failed model pass yields the human-approved baseline
