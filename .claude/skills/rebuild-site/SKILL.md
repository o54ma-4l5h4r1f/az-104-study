---
name: rebuild-site
description: Rebuild the AZ-104 study site from content, surface lint warnings, and report coverage. Use after editing any content, or when the site looks stale or the user asks to rebuild.
---

# Rebuild the site

```bash
python3 build.py
```

Idempotent and cheap. `site/` is wiped and regenerated every time — it holds nothing that is not
derived from `content/`, `assets/` and `progress/`.

## Read the output

```bash
python3 build.py --lint         # validate only, write nothing
python3 build.py --report       # coverage stats as JSON
python3 build.py --check-links  # probe cited URLs for rot (needs network)
```

`--check-links` is the only command that touches the network. Run it monthly. It separates **dead**
(404), **redirected** (301 to a materially different article — the dangerous case, because the
citation still resolves and looks fine), and **unreachable** (no network, which is not a content
problem). Every flagged URL is listed with the objectives and questions citing it.

**Errors** block correctness and must be fixed:
- unknown objective IDs in questions or labs — usually a typo, or a stale ID after an outline change
- answer key not matching the options marked `correct`
- `multiple_choice` with more than one correct option
- `correct_order` or `correct_item` referencing an item that does not exist
- duplicate question IDs

**Warnings** are quality signals:
- *"questions X and Y are N% similar"* — the writer repeated a scenario angle. Rewrite one.
- *"no source_urls"* — a question with no citation cannot be verified. Fix it.
- *"option has no explanation"* — the explanation is where the learning happens.
- *"raw HTML in markdown"* — outside the supported subset; rewrite in markdown.

## After building

Open `site/index.html` directly in the browser. **No server needed** — that is a design constraint, not
a convenience. If anything requires a server to work, it is a bug.

Known browser caveat: **Firefox is the safer choice**. Chrome's storage partitioning for `file://`
origins is inconsistent, so progress can fail to persist across pages. Always open from the same path.

## Report

Objectives written vs total, questions and how many are verified, labs, and — most usefully — which
objectives still have **no page** and which have **no questions**. Those two lists are the work queue.
