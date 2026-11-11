---
name: study
description: Start a study session. Works out what to study now from recorded progress, the week schedule and weak areas, then teaches it. Use when the user says "study", "what should I study", "let's do some AZ-104", or asks where to pick up.
---

# Start a study session

The daily entry point. Figure out what matters right now, then teach it — do not just list options.

## 1. Work out where they are

```bash
python3 build.py --report
```

Also check:
- `progress/study-plan.md` — did `weak-area-coach` already prescribe a session? If it is less than a
  week old and still unaddressed, follow it.
- `progress/attempts.jsonl` — recent activity, and how long since the last session.
- `content/domains.toml` — the `week` field on each objective gives the planned schedule.

Week 1 start date: **TODO — set once the study plan exists (`progress/study-plan.md`).** Compare
today's date to work out which week they are in, then check whether the content actually reflects that
— falling a week behind is normal and not worth commenting on unless it is several.

## 2. Pick the session

In priority order:

1. **An outstanding prescription** in `progress/study-plan.md`.
2. **Weak objectives** — rolling accuracy under 60% on anything already attempted.
3. **Overdue spaced repetition** — objectives past their interval.
4. **This week's scheduled objectives** that have no page written yet.
5. **This week's objectives** that have a page but no questions attempted.

Announce the pick in one line with the reason. Do not present a menu unless genuinely torn between two.

## 3. Run it

A session is roughly **90 minutes to 2 hours** — that is what 5–8 hrs/week buys.

- **If the page is not written:** spawn `objective-writer` for it, then teach from the result.
- **If the page exists:** spawn `tutor` to teach it, or teach directly if it is a short objective.
- **After teaching:** point them at `site/quiz.html?objective=<ID>` for practice, and at any lab that
  covers it in `content/labs/`.
- **If no questions exist yet** for the objective, spawn `question-writer` (then `question-verifier`)
  so the practice loop closes in the same session.

Rebuild at the end so the dashboard reflects the session:

```bash
python3 build.py
```

## 4. Close the loop

Tell them plainly: what was covered, what to do next, and whether anything is slipping against the
10-week plan. If they have quiz data from this session, spawn `weak-area-coach` to write the next
prescription.

## Rules

- **Teach, do not brief.** The learner has said Microsoft Learn buries the point. Neither should you.
- Respect the effort allocation in `domains.toml`. If they want to over-spend on something their
  baseline (CLAUDE.md "THE LEARNER") already covers, steer them to the real gaps instead.
- One session at a time. Do not re-plan the whole runway.
