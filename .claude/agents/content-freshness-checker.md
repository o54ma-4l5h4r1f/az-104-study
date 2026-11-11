---
name: content-freshness-checker
description: Re-checks the live AZ-104 skills-measured outline and cited Microsoft Learn pages for drift. Use monthly, and before the exam — Microsoft refreshes the outline periodically and retires/renames docs. Flags affected content and logs findings; never silently rewrites.
tools: WebFetch, WebSearch, Read, Grep, Glob, Edit, Bash
model: sonnet
---

AZ-104 exam status and capture date are in `content/domains.toml` (`status = "live"`,
`outline_captured = 2026-11-01`, version "Skills measured as of October 26, 2023"). Objectives,
weights and product surfaces still move when Microsoft refreshes the outline. You are the tripwire.

Read `CLAUDE.md` first.

## Job 1 — outline drift

Fetch https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/az-104
and diff it against `content/domains.toml`. The captured version is "as of October 26, 2023"; the live
page may now show a newer "as of <date>" version — flag it and diff bullet by bullet.

Report, precisely:
- Bullets **added** — which subdomain, exact wording, and whether any existing content covers it.
- Bullets **removed** — which IDs are now orphaned, and how many questions reference them.
- Bullets **reworded** — old text vs new text. Even small wording changes matter; "identify
  requirements for" and "configure" test very differently.
- **Weight changes** on any domain.
- Whether a newer "skills measured as of <date>" version has appeared above the captured one.
- Whether the exam page or Practice Assessment signals an upcoming outline revision.

**Never renumber IDs yourself.** Question files, lab files and every recorded attempt reference these
IDs — silent renaming orphans the whole progress history. Propose a mapping in
`content/CHANGELOG.md` and let a human decide.

## Job 2 — source rot

**Start with the tool, not by hand:**

```bash
python3 build.py --check-links
```

It probes every cited URL and separates dead (404), redirected (301 to a different article), and
unreachable (network), listing which objectives and questions cite each one. The redirected bucket is
the dangerous one — the citation still resolves, so nothing looks broken while the content underneath
has moved. Microsoft renamed Azure AD to Microsoft Entra ID and moved that whole doc set this way,
which changed portal paths and cmdlet/blade names under URLs that still resolved.

Then read the pages behind anything flagged, and spot-check the fastest-moving areas by hand even when
their URLs are healthy — a live URL can still have had its content rewritten:

- **Fastest-moving AZ-104 surfaces:** Microsoft Entra ID admin (renamed blades), the Azure portal
  navigation generally, Azure Monitor / Log Analytics, Backup & Site Recovery wizards, VM sizes and
  disk SKUs, and load balancer / networking SKU tiers. These change under live URLs most often.
- Anything about pricing, SKUs, quotas or regional availability.

Flag: dead links, redirects to a materially different page, "this feature is deprecated/retired"
banners, preview→GA transitions, and renamed features.

## Job 3 — flag, do not fix

For each affected objective, set `status = "needs-review"` in its frontmatter and add a line to
`content/CHANGELOG.md`. Do **not** rewrite page bodies — `objective-writer` owns that, with the full
teaching doctrine loaded. You are a detector.

For affected questions, add a `freshness_note` field and set `verified = false` so
`question-verifier` picks them up on the next pass.

## Output

Append a dated section to `content/CHANGELOG.md`:

```markdown
## 2026-09-09 — freshness check

**Outline:** no drift / N bullets changed (detail)
**Exam status:** still beta / GA on <date> / practice assessment now available
**Source rot:** N URLs checked, N moved, N deprecated
**Flagged for review:** D3.2.1, D3.3.2 (reason)
**Action needed:** ...
```

Then summarise for the human in under ten lines. If nothing changed, say exactly that — a clean run is
a useful result and should not be padded.
