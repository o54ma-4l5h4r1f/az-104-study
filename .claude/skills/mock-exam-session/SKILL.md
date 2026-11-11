---
name: mock-exam-session
description: Run a timed AZ-104 mock exam and debrief it. Use when the user asks for a mock, practice exam, or full-length test. Checks readiness first, then auto-debriefs with weak-area-coach.
---

# Mock exam session

## 1. Check it is worth doing

```bash
python3 build.py --report
```

A mock needs a real pool. Under ~120 questions the sample is too thin to mean anything and it mostly
burns questions they will then have memorised. Say so plainly and suggest `generate-question-batch`
instead.

Also check `progress/attempts.jsonl` for the last `mock_exam` session. Cadence: roughly every **60–80
practice questions**, and both weeks 9 and 10 of the plan.

## 2. Set it up properly

Tell them, before they start:

- **100 minutes, no pausing.** The real exam does not pause and neither should this.
- **No notes, no docs, no second screen.** The real exam gives a Microsoft Learn pane — this does not,
  which makes the mock slightly harder than the real thing. That is deliberate.
- **Answer everything.** No negative marking. A blank is a guaranteed zero; a guess is not.
- Multiple-response questions are **all-or-nothing** — worth the extra thirty seconds.
- Drag-and-drop and build-list give **partial credit** — always fill every slot, even under time pressure.

Then: open `site/mock-exam.html` and press start.

## 3. Debrief immediately

Do this while it is fresh — that is where the value is, not in the score.

Spawn `weak-area-coach` to analyse the session and write `progress/study-plan.md`.

Then walk the missed questions with them. For each one, the question worth asking is not "what was the
right answer" but **"what made the wrong answer look right?"** That is the misconception, and it is the
thing that will cost them again.

## 4. Read the score honestly

The 1–1000 number is a **linear approximation**. Microsoft's real algorithm is undisclosed and almost
certainly weights by item difficulty. Useful as a trend across attempts, not as a prediction.

What actually matters in the results:
- **Per-domain breakdown.** D2 and D3 together are ~80% of the exam. Weakness there matters far more
  than weakness in D1.
- **Failure by format.** Consistently losing multiple-response questions is a technique problem, not a
  knowledge problem, and it is fixable in one conversation.
- **The trend line.** Three mocks trending 620 → 680 → 730 is a better signal than any single number.

If readiness looks genuinely low close to the exam date, say so directly. False reassurance ahead of a
real exam fee is not kindness.
