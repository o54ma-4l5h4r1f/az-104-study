# AZ-104 study plan

Exam: **AZ-104 — Microsoft Azure Administrator** → Microsoft Certified: Azure Administrator Associate.
Blueprint: 5 domains / 15 subdomains / 81 bullets (`content/domains.toml`, "Skills measured as of
October 26, 2023" version).

## Runway

- **Sit target:** ~end of November 2026 (≈4 weeks from 2026-11-01). Not booked yet.
- **Budget:** ~13–14 h/week → **~54 hours total** (`total_planned_hours` in the blueprint = 54.2 h).
  Aggressive; leans on the learner's networking/security strength to buy time for the new material.
- **Lab access:** Azure subscription, cost funded. Labs run live. Keep data small, tear down.

## Baseline (drives effort — see CLAUDE.md "THE LEARNER")

Strong on **security and networking**, newer to everything else in Azure admin. So hours are
allocated by **gap size × exam weight**, not weight alone:

- **Thin hours (strength):** D4 virtual networking — especially D4.2 secure access (NSGs, ASGs,
  Bastion, service/private endpoints) — plus RBAC (D1.2) and the security-adjacent storage-access
  bullets (SAS, access keys, storage firewalls). Teach the Azure *delta* only.
- **Heavy hours (new ground):** D1.3 governance (Policy, locks, tags, management groups, cost),
  all of D2.2/D2.3 (storage accounts, redundancy, replication, encryption, tiers, lifecycle),
  D3.1 ARM/Bicep, D3.3 containers, D3.4 App Service, and all of D5 (Monitor/KQL, Backup, Site Recovery).

## 4-week shape (per-objective `week`/`hours` live in `content/domains.toml`)

| Week | Focus | Domains | ~Hours |
|---|---|---|---|
| 1 | Identity + governance: Entra users/groups, RBAC (fast), Policy/locks/tags/mgmt-groups/cost (slow) | D1 | ~10 |
| 2 | Storage end-to-end + ARM/Bicep: access, accounts, redundancy, files/blob, templates | D2 + D3.1 | ~17 |
| 3 | Compute + networking: VMs/disks/availability/VMSS, containers, App Service, then D4 (fast, strength) | D3.2–D3.4 + D4 | ~17 |
| 4 | Monitor + backup + mocks: Azure Monitor/KQL/Insights, Backup/RSV/ASR, then full mocks + weak-area patch | D5 + review | ~10 |

Weeks 2–3 are front-loaded because the compute + storage gap is widest; D4 (networking) is parked late
and light on purpose — it is the learner's strongest area and clears fast.

## Planning-step status

1. ✅ Blueprint captured (`content/domains.toml`, 81 bullets, verbatim via Microsoft Learn MCP).
2. 🟡 Curriculum → `content/learn-map.toml` — **Domain 1 captured** (path P1, 6 modules, 51 units,
   mapped to D1 objective IDs via the Microsoft Learn MCP). D2–D5 still module-less seeds.
3. ✅ Learner baseline → CLAUDE.md "THE LEARNER".
4. ✅ Study plan → this file. Per-objective `week`/`hours` set across all 5 domains in the blueprint.
5. 🟡 Objective pages — **Domain 1 done (15/15)**, `status = draft`, built 0 warnings / 0 errors,
   217 Microsoft Learn images harvested + cached offline (0 broken, 0 placeholder shims) + 19 drawn
   SVGs. D2–D5 and all practice questions / labs still to author via `/study` and
   `generate-question-batch`.

## Domain 1 — done 2026-11-01

All 15 D1 objective pages authored (D1.1 identity ×5, D1.2 RBAC ×3, D1.3 governance ×7) and built
clean. Every page follows the 7-section doctrine with a Skip list and ≥1 SVG; 217 sourced Learn images
cached under `assets/img/cache/` so they render offline from `file://`. D1.1.4 (external users),
D1.3.2 (locks), D1.3.3 (tags), D1.3.6 (cost) were written from product docs (no training unit).
Still owed for D1: a `question-verifier`-style fact re-check to flip pages draft→verified, then
practice questions and labs.

**Enhancement pass (2026-11-02):** 4-lens review (structure / voice / accuracy / pedagogy) found the
set accurate (0 exam-impacting errors) and doctrine-compliant. Applied: inline **highlight spans**
(new `==text==` / `==@term==` / `==!trap==` / `==+correct==` engine feature, 204 in use), comparison
tables on contrast-heavy pages, a cross-page "what blocked this action?" decision diagram on D1.3.1,
exam-core recap boxes on the densest pages, three stray sections folded into the 7-section template
(all 15 now h2==7), and a batch of small fixes (arrowheads, de-dup, analogy variety, one 2nd source).
No image dropped; still 0 "needs network"; build clean. Pages stay `draft`.

## Per-domain cheatsheets (standing deliverable)

After each domain's objective pages are done, author a **one-page printable cheatsheet** at
`content/cheatsheets/D{n}.md` — the last-night single-sheet recap, exam content, dense/tabular, with a
mandatory **permissions table** ("who can do what" across all the domain's sections) and a
traps→truth list. Renders at `site/cheatsheet/<Dn>.html`, linked in the top nav, print with
Ctrl/Cmd + P. Doctrine in CLAUDE.md "CHEATSHEETS". **D1 done 2026-11-03.** D2–D5 owed as each domain
lands.

Also per domain: a **Diagrams** page (`content/diagrams/D{n}.md` → `site/diagram/<Dn>.html`, nav next
to Cheatsheets) — a curated set of big cross-page SVGs connecting the domain + a "same job, different
tool" confusables map, with out-of-domain cousins drawn as dashed forward-links. Inline SVG only
(offline/theme). Doctrine in CLAUDE.md "DIAGRAMS SECTION". **D1 done 2026-11-03** (4 diagrams: scope
spine, identity→access bridge, four-gates recap, confusables map). D2–D5 owed as each domain lands.

**Interactive walkthroughs** (`assets/interactive/*.html` → `site/interactive/`, self-contained +
offline): for concepts best learned as one walked scenario. First one **done 2026-11-03** —
`rbac-roles.html`, the D1.2 access story (15 steps, Alex reads a blob, folds in Entra-vs-RBAC, deny
assignments, classic admins, PIM). Linked from D1.2.1 + diagrams/D1. Add more only where a story beats
a static diagram. Doctrine in CLAUDE.md "Interactive walkthroughs".

**Static complete-view posters** (`assets/visuals/*.html` → `site/visuals/`): one dense printable page
with all of a topic + comparison tables. First one **done 2026-11-10** — `rbac-complete.html` (Azure
RBAC complete reference, docs-verified limits), linked from D1.2.1/2.2/2.3 + diagrams/D1. Doctrine in
CLAUDE.md "Static complete-view posters".

## Weekly loop

- Start each session with the **/study** skill — it reads progress + the week schedule + weak areas
  and picks the next objective, then teaches it.
- After a page is written: `python3 build.py --fetch-images` (cache images) then `python3 build.py`.
- End of each week: a `mock-exam-session` over the domains covered so far; feed results to
  `weak-area-coach`. **Export progress** (footer button) — do not rely on `file://` localStorage.
- Notes: the 📝 button (bottom-right of any study page) drops sticky notes anywhere on the page.
  **Export notes** (📝 menu → ⭳ → `az104-notes.json`) alongside progress — same reason, `file://`
  localStorage can be wiped. Re-import with ⭱.
