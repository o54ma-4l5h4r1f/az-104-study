# AZ-104 Study System

Offline study system for **Microsoft AZ-104: Microsoft Azure Administrator**
→ cert *Microsoft Certified: Azure Administrator Associate*.

A single learner's exam-prep site. Builds to static HTML that opens from `file://` with zero network.
Study pages are written in a plain, point-first voice — the opposite of Microsoft Learn's padded
style — with every factual claim cited back to a Learn URL.

## What's here

- `build.py` — the stdlib-only build engine: parse TOML + markdown → render → lint → emit `site/`.
- `CLAUDE.md` — the teaching doctrine: page shape, voice rules, image policy, the learner baseline.
- `.claude/agents/`, `.claude/skills/` — the authoring fleet (objective/question/lab writers,
  verifiers, tutor, coach) and the study/quiz/mock workflows.
- `content/domains.toml` — the exam blueprint: 5 domains, 15 subdomains, 81 objective bullets.
- `content/learn-map.toml` — the official Microsoft Learn curriculum mapped to blueprint IDs (seeded).
- `content/{objectives,questions,labs,reference,snippets,notes}/` — the study material.
- `progress/` — study plan and attempt log. `assets/` — CSS/JS.

## Status

- ✅ Blueprint captured (`content/domains.toml`, 81 bullets, verbatim via Microsoft Learn MCP).
- ✅ Learner baseline set (`CLAUDE.md` → THE LEARNER: strong security + networking, newer to the rest).
- ✅ Study plan set (`progress/study-plan.md`, 4-week, per-objective `week`/`hours` in the blueprint).
- 🟡 Curriculum map seeded (`content/learn-map.toml`) — units filled per domain as pages are written.
- ⬜ Objective pages, questions and labs — generated on demand with the `/study` and
  `generate-question-batch` skills, starting with Domain 1.

## Build

```bash
python3 build.py              # emits site/
python3 build.py --lint       # schema + ref checks + near-duplicate question stems
python3 build.py --report     # coverage / progress stats as JSON
python3 build.py --check-learn-map   # curriculum coverage (no network)
python3 build.py --fetch-images      # cache remote images for offline use
python3 build.py --check-links       # probe cited URLs for rot (the only networked command)
```

Open `site/index.html` from `file://`. `site/` is disposable — delete it and rebuild.

## How to study

1. `python3 build.py && open site/index.html` — the dashboard shows readiness and weak areas.
2. Use the **/study** skill to pick what to study now and get it taught.
3. Use **generate-question-batch** to add verified practice questions for an objective.
4. Use **mock-exam-session** for a timed full-length mock once a domain's pages exist.
5. Export progress weekly (footer button) — `localStorage` on `file://` is not durable.
