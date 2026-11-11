---
name: generate-question-batch
description: Generate and verify a batch of AZ-104 practice questions for one or more objectives. Runs question-writer then question-verifier, then rebuilds. Use when asked to add, write or expand practice questions. Checkpointed so it resumes cleanly across sessions.
---

# Generate a question batch

Writer → verifier → rebuild. Checkpointed, so a batch started weeks ago picks up where it stopped.

## Batch size

**2–3 objectives, 5–8 questions each, per invocation.** About a 25-minute sprint. Resist doing more —
long generation runs produce repetitive questions and the verifier pass gets skipped.

Bank target is ~250 questions total: **D1 ≈50, D2 ≈110, D3 ≈90**, including ~8–10 case studies and
~15 problem/solution sets.

## 1. Pick the objectives

Read `content/questions/.batch-checkpoint.json`. If it does not exist, create it:

```json
{
  "target_total": 250,
  "objectives": {
    "D1.1.1": { "target": 4, "current": 0, "covered_angles": [], "last_batch": null }
  }
}
```

Per-objective targets scale with planned hours in `domains.toml` — a 3-hour high-risk objective earns
6–8 questions, a 0.2-hour `strong` objective earns 2.

Pick in **study order** (by domain) unless the user names an objective, or `weak-area-coach` has
flagged one as under-covered. Write questions for the fastest-moving product surfaces last on purpose:
preview and newly-shipped features change monthly, so questions written closest to the exam are the
most accurate. (TODO: identify those surfaces once the blueprint is captured.)

## 2. Write

Spawn `question-writer` with: the objective IDs, the exact blueprint wording from `domains.toml`, the
per-objective target counts, and the `covered_angles` already used.

Require a spread of formats. A batch that is all multiple choice is a failed batch — the real exam uses
eight formats and the all-or-nothing multiple-response rule alone costs marks if it is never practised.

## 3. Verify

**Always run `question-verifier` after the writer. Never skip it.** The writer never certifies its own
work, so unverified questions are exactly that — unverified.

If more than a quarter of the batch fails verification, stop and report it. That means the source docs
or the writer's brief need fixing, not just this batch.

## 4. Rebuild and record

```bash
python3 build.py
```

Fix every lint error. Pay attention to duplicate-similarity warnings — they mean the writer repeated a
scenario angle, and those questions should be rewritten, not shipped.

Update `.batch-checkpoint.json`: new `current` counts, the `covered_angles` used (short phrases like
`"group-based licence conflict on overlapping service plans"`), and `last_batch`.

## 5. Report

Objectives covered, questions written, verified vs rejected, the running bank total against 250, and
what the next batch should pick up.
