# Content changelog

Freshness checks, outline drift, and source rot. Append newest at the top.

`content-freshness-checker` runs monthly and logs here. It never renames objective IDs — question
files, lab files and every recorded attempt reference them, so a rename orphans the history. Remaps
are proposed here and applied by hand.

---

## 2026-11-11 — GitHub Pages web app + privacy scrub for public repo

Published the site as a GitHub Pages web app (dashboard = landing page). Added
`.github/workflows/pages.yml` (Actions → Pages: `python3 build.py --online`, deploy `site/`), a new
`build.py --online` flag (uncached remote images load straight from Microsoft's CDN with no "needs
network" chip — so a hosted build needs no committed images), and scoped `notes.js` server-mode to
localhost only (Pages is static, so it falls back to localStorage there).

**Privacy scrub before going public:** removed personal data from tracked files — the learner's name and
employer (CLAUDE.md "THE LEARNER", `tutor.md`, `objective-writer.md` → genericized to "an MSSP security
engineer / the learner") and the hardcoded `~/AZ-104` username path across agent/skill docs. Untracked
`assets/img/cache` (205 Microsoft Learn images) and gitignored it — kept local for offline file:// use,
not republished. Rewrote the single commit's author to a GitHub noreply address. Audit found no secrets.

## 2026-11-10 — Notes auto-save to disk (serve.py) + Reviews section

- **`serve.py`** (new, stdlib-only, localhost): serves `site/` at `http://localhost:8000` and persists
  on-page notes to **`progress/notes.json`** via GET/POST `/__notes` (atomic write, 127.0.0.1 only, no
  internet). `notes.js` now detects http vs `file://`: over http it hydrates from and writes back to the
  server (debounced POST); on `file://` it keeps the localStorage + Export/Import fallback. So notes save
  to a real file **by default** when the site is served. Seeded `progress/notes.json` with the learner's
  exported RBAC-poster notes (8 notes on `visuals/rbac-complete.html`). Verified end-to-end (GET returns
  seed, POST writes disk, page served with `notes.js`).
- **Reviews section** — new top-nav entry **Reviews** (`site/reviews.html`, next to Reference), auto-
  indexing the self-contained full-topic reference pages in `assets/visuals/*.html` (title read from each
  file). Doctrine: these reviews **may go beyond the AZ-104 blueprint** (standing learner-approved
  exception); objective pages stay blueprint-scoped. CLAUDE.md updated (layout + working rules).

## 2026-11-10 — Page annotations (sticky notes on study pages)

Added `assets/notes.js` + CSS, injected in the page shell (`build.py` `page()`, next to `app.js`) so it
loads on **every built page** (objectives, domains, diagrams, cheatsheets, reference, dashboard…). Click
the floating 📝 button → "add mode" → click anywhere on the page to drop a note pin; click a pin to
edit/delete. Notes persist in `localStorage` keyed per page, and **Export/Import** buttons write/read an
`az104-notes.json` file (same durability pattern as progress, since `file://` localStorage is unreliable).
Vanilla classic script, offline, theme-aware, hidden in print. Also wired into the self-contained
`assets/visuals/rbac-complete.html` and `assets/interactive/rbac-roles.html` (annotation CSS inlined +
`<script src="../notes.js">`), so notes work on **every** page — built or standalone. `notes.js` has no
`<main>` dependency (falls back to `document.body`), and the per-page key is the last two path segments,
so a page's notes are the same whether opened from `site/` or the `assets/` source.

## 2026-11-10 — RBAC complete-view poster (static, docs-complete)

Added a **static complete-view poster** format (`assets/visuals/*.html` → `site/visuals/`, same
self-contained/offline mechanics as the interactive pages, shipped by the asset loop). Doctrine in
CLAUDE.md "Static complete-view posters".

**Wrote `assets/visuals/rbac-complete.html`** — "Azure RBAC — complete reference view": one dense,
printable page covering the whole of RBAC with comparison tables — key terms, the two planes, Owner-
can't-read-blobs, scope hierarchy + who-can-assign, the Actions−NotActions equation + the ARM
evaluation order (deny assignment first → roles → conditions), the full roles table (incl. storage
data roles), built-in vs custom + custom-role rules, a **limits** panel, deny-assignment vs role-
assignment, ABAC conditions, the "role"-adjacent look-alikes (Entra role / classic admin / PIM /
access review), and traps + where-to-inspect. **Grounded in a live Microsoft Learn pass** (RBAC
overview, role-definitions, custom-roles, deny-assignments, conditions-overview, service-limits) —
corrected the role-assignment limits to **5,000 per subscription / 500 per management group** (fixed),
5,000 custom roles/tenant, 2,000 assignable scopes, 8 KB condition, 2,000 system deny assignments/sub;
noted deny assignments are Azure-managed (Deployment Stacks / legacy Blueprints retiring 2027-01-31).
Linked from D1.2.1, D1.2.2, D1.2.3 and `diagrams/D1`. Build + lint clean; offline; 0 stray hex.

**Expanded same day** from an RBAC-only page into a broad **Azure access & authorization** map (17 cards,
5 bands), after a second Microsoft Learn pass: added **Azure RBAC vs Microsoft Entra roles** (governs /
scope / tooling / action-namespace tell + the Owner≠Global-Admin and UAA≠User-Admin name traps + elevate-
access); **service access models** — Key Vault's two authorization models (control-plane RBAC + data-plane
Access policy *legacy* vs RBAC, KV data roles, "Contributor can self-grant" risk) and Storage's three data-
auth methods (Entra RBAC+ABAC / Shared Key / SAS) with the **three SAS types** (user-delegation/service/
account); **identities** (user/group/SP/managed identity — system- vs user-assigned); and **the other gates**
(deny assignment, Azure Policy Deny, resource locks, network rules, Conditional Access) plus a note that
Entra/Exchange/Intune/Synapse/AKS/SQL each have their own RBAC. Classic-admin retirement dates corrected
(fully retired May 2026). Same file/links.

## 2026-11-03 — Interactive walkthroughs + D1.2 access story

Added a lightweight **interactive walkthrough** format: self-contained HTML under
`assets/interactive/`, shipped to `site/interactive/` by the existing asset-copy loop (no build.py
change; never passes through the markdown renderer, so the no-`<script>` guard on content pages is
untouched). Each page embeds its own theme tokens (offline, light/dark), uses inline classic JS, and
walks **one scenario** that spotlights a persistent diagram step by step. New doctrine in CLAUDE.md
"Interactive walkthroughs".

**Wrote `assets/interactive/rbac-roles.html`** — "Azure access — the whole story": a 15-step single
thread (Alex must read a blob in `sa-files`) that surfaces every roles/permissions concept in order —
role = assignment (principal/role/scope), scope + inheritance, control vs data plane, the
Owner-can't-read-blobs trap, the fix, additive model, Actions−NotActions, built-in vs custom — plus the
four **look-alikes** that get confused with a role: Entra roles (≠ Azure RBAC), deny assignments,
classic administrators, and PIM eligible-vs-active. Grounded in the D1.2.1 page. Linked from D1.2.1 and
`diagrams/D1`. Build + lint clean; JS syntax-checked; offline.

## 2026-11-03 — Diagrams: new per-domain content type + D1 diagram page

Added a **diagrams** content type to the build: `content/diagrams/D{n}.md` → `site/diagram/<Dn>.html`,
linked in the top nav next to Cheatsheets. Where a cheatsheet is dense text, a diagram page is a
**curated set of big cross-page SVGs** that connect a domain's concepts + a map of Azure constructs
that look alike / do the same job. Inline SVG only → offline by construction, follows the theme. New
doctrine in CLAUDE.md "DIAGRAMS SECTION" (one per domain, curate don't dump, out-of-domain cousins as
dashed `box-fwd` forward-links, don't duplicate a page-local SVG — cross-link instead).

Engine (`build.py`): `DiagramPage` dataclass, `load_diagrams`, `build_diagram_index` +
`build_diagram_page`, nav entry, `emit` + `main` wiring — all mirroring the cheatsheet type. CSS
(`assets/style.css`): a `.diagrams` block (larger SVGs) and a new `.diagram svg .box-fwd` class
(dashed-muted forward-link node).

**Wrote `content/diagrams/D1.md`** — four diagrams: (1) the governance scope spine (what inherits down;
tags as the non-inheriting exception), (2) the identity→access bridge (Entra plane ↔ Azure plane via
the role assignment; the GA ≠ Owner two-planes trap), (3) the four-gates denial recap (cross-linked
both ways with D1.3.1, its canonical home) with an NSG forward-link, (4) a "same job, different tool"
confusables map (scope containers / ways-to-block / inherit-down pattern, D4 cousins dashed). Build +
lint clean (0/0), 0 hard-coded hex in the SVGs, every SVG has viewBox+role+aria-label, 0 literal `==`
leaks, offline, AI-source leak 0.

## 2026-11-03 — Cheatsheets: new per-domain content type + D1 sheet

Added a **cheatsheet** content type to the build: `content/cheatsheets/D{n}.md` → `site/cheatsheet/<Dn>.html`,
linked in the top nav, print-first (one–two pages, Ctrl/Cmd + P). Unlike reference pages, a cheatsheet
**is exam content** — the whole domain condensed to what survives into the exam room. New standing
doctrine in CLAUDE.md "CHEATSHEETS" (one per domain, grounded in that domain's objective pages,
dense/tabular, a **mandatory permissions table** summarizing who-can-do-what across all sections, and a
traps→truth list). Logged as a standing deliverable in `progress/study-plan.md`.

Engine changes (`build.py`): `Cheatsheet` dataclass, `load_cheatsheets`, `build_cheatsheet_index` +
`build_cheatsheet_page`, nav entry, `emit` wiring. CSS: a print-first `.cheatsheet` block + `.no-print`
and `@page` rules in `assets/style.css`.

**Wrote `content/cheatsheets/D1.md`** — Identities & governance: Entra users/groups, licensing, SSPR,
RBAC (the four confusable roles), governance (Policy/locks/tags/RGs/subs/mgmt-groups/cost), a
cross-cutting permissions table, and 8 top traps→truth. Every number grounded in the D1 objective pages.
Build + lint clean (0/0), renders offline, 0 literal `==` leaks.

## 2026-11-02 — Domain 1 reviewed + enhancement pass + inline highlight spans

Ran a 4-lens review of all 15 Domain 1 pages (structure/doctrine, voice/language, technical accuracy vs
live Microsoft Learn, pedagogical calibration). Verdict: accurate (0 exam-impacting errors found),
doctrine-compliant, on-voice. Then applied an enhancement pass for single-pass study speed:

- **New engine feature — inline highlight spans.** `build.py` now renders `==text==` (attention,
  highlighter-yellow), `==@term==` (accent), `==!trap==` (red), `==+correct==` (green) as themed
  `<mark>` spans. Keyed to the existing theme tokens (new `--hl`/`--hl-ink`), legible light + dark;
  code spans are protected and the sigils never fire in normal prose. Documented in `CLAUDE.md`
  (markdown subset + a new "Inline highlight spans" subsection). 204 spans now in use across Domain 1.
- **Comparison tables** added where contrasts were prose/bullets (lock levels, the four RBAC scopes,
  MG/sub/RG containment, Budget-vs-Alert-vs-Advisor, property categories, management-group limits).
- **New cross-page decision diagram** on D1.3.1 ("an operation was denied — which of the four
  independent gates: RBAC gap / deny assignment / resource lock / Policy Deny?"), cross-linked from
  D1.2.3. D1.3.1 now carries 3 SVGs.
- **Structure:** folded three stray `##` sections (Policy-vs-RBAC on D1.3.1, Managed apps on D1.3.2,
  the image gallery on D1.3.6) into the canonical 7-section template — all 15 pages now h2 == 7.
- **Scannability:** exam-core recap boxes on the densest pages (D1.1.1, D1.3.1, D1.3.6); fused
  multi-fact bullets split into tables (D1.3.6, D1.3.7); click-walkthrough screenshots regrouped under
  "Portal walkthrough — reference, not study" (D1.2.2, D1.3.4, D1.3.6) — all images kept.
- **Fixes:** D1.1.2 de-duplicated against D1.1.1; hollow SVG arrowheads fixed on 7 pages (marker
  `<path>` class `line`→`muted`, `line-accent`→`label-accent`); `unlocks`→`adds` (D1.1.3); two gerund
  openers made declarative (D1.1.2, D1.2.3); firewall analogy varied (tags now asset-label); D1.3.2
  given a 2nd source; D1.2.3 audit-retention qualified (7d Free / 30d P1-P2); D1.3.4 trailing comma;
  `domains.toml` D1.3.3 `prior_cert` partial→none.

No image dropped (per-page counts == baseline), all 15 still render offline (0 "needs network"), build
clean (0 warnings / 0 errors). Pages remain `status = draft`.

## 2026-11-01 — Domain 1 authored (15 pages) + curriculum captured

Captured the Domain 1 curriculum into `content/learn-map.toml` via the Microsoft Learn MCP: path P1
("Manage identities and governance"), 6 modules, 51 units mapped to D1 objective IDs. The official
path leans on two Fundamentals modules (core architectural components, policy initiatives), so D1.1.4
(external users), D1.3.2 (locks), D1.3.3 (tags) and D1.3.6 (cost) have no training unit and were
written from product docs.

Authored all **15 Domain 1 objective pages** (D1.1 ×5, D1.2 ×3, D1.3 ×7) with the `objective-writer`
fleet, `status = draft`. Build is clean (0 warnings, 0 errors). **217 Microsoft Learn images** were
harvested, each URL verified to resolve, and cached offline under `assets/img/cache/` — `--fetch-images`
reported 0 failed, and a sweep found 0 placeholder shims (<2 KB); every D1 page renders with no
"needs network" image. 19 inline SVG diagrams drawn for non-photographable concepts (RBAC scope
inheritance, allow-union/deny-override, Policy-vs-RBAC, management-group hierarchy, etc.). Fixed a
markdown gotcha: `<placeholder>` tokens in CLI examples tripped build.py's raw-HTML check, so they were
converted to `{placeholder}` form (SVG fences untouched). Pages remain `draft` pending a verifier pass.

## 2026-11-01 — System stood up; blueprint captured

Built the AZ-104 study system: stdlib-only `build.py`, the `assets/` front end, and the `.claude/`
authoring fleet (objective/question/lab writers, verifier, tutor, coach + the study/quiz/mock skills).
Captured the AZ-104 "Microsoft Azure Administrator" skills-measured outline ("Skills measured as of
October 26, 2023") into `content/domains.toml` via the Microsoft Learn MCP (`microsoft_docs_fetch` of
the live study guide): **5 domains, 15 subdomains, 81 leaf bullets**, bullet text verbatim. Set THE
LEARNER (strong security + networking, newer to the rest of Azure admin) and the 4-week study plan,
with per-objective `prior_cert` / `week` / `hours` allocated by gap size × weight.

`content/learn-map.toml` is **seeded** (the five Azure Administrator learning paths + module stubs) so
`--check-learn-map` runs clean; module units get mapped to objective IDs as each domain's pages are
authored. No objective pages, questions or labs written yet — those are generated on demand.
