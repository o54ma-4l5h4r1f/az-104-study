# AZ-104 Study System

Offline study system for **Microsoft AZ-104: Microsoft Azure Administrator**
→ cert *Microsoft Certified: Azure Administrator Associate*.

> **⚠ MACHINE BUILT — content authored on demand.** Doctrine, build engine and the agent fleet are in
> place. Progress so far: blueprint **captured** (`content/domains.toml`: 5 domains, 15 subdomains, 81
> bullets, "Skills measured as of October 26, 2023" version, verbatim via the Microsoft Learn MCP);
> THE LEARNER **filled** (below); study plan **set** (`progress/study-plan.md`, 4 weeks). Still TODO:
> curriculum (`content/learn-map.toml` is a seed), then the objective pages, questions and labs —
> generated on demand with the `/study` and `generate-question-batch` skills.

Exam sit target: **~end of November 2026** (≈4 weeks from 2026-11-01, not yet booked). Runway / hours
per week: **~13–14 h/week, ~54 h total** (recorded in `progress/study-plan.md`).

---

## THE LEARNER  — for AZ-104

The learner is an MSSP security engineer. For AZ-104 their baseline is lopsided: **strong
on security and networking, newer to the rest of Azure admin**. Captured 2026-11-01. These drive
`prior_cert` on each objective:

- **Holds / proven:** networking (TCP/IP, routing, subnets, DNS, load balancing, firewalls), network
  security (NSGs/ACLs, segmentation, zero-trust thinking, bastion/jump-host patterns), general IT ops,
  virtualization, and comfort on a CLI / PowerShell. Security is the day job at an MSSP.
- **Assume fluent, never re-explain:** IP addressing and subnetting, routing and NAT, firewall rule
  logic (allow/deny, priority, stateful), DNS resolution, TLS/certs basics, identity concepts (RBAC,
  least privilege, SSO), and reading a packet/flow. Teach the *Azure-specific shape* of these on top —
  how Azure expresses a firewall rule, not what a firewall is.
- **Assume zero knowledge (teach from scratch):** the Azure portal and resource model, **ARM templates
  & Bicep**, storage accounts (redundancy, tiers, replication, lifecycle, SAS internals), **VMs /
  disks / availability zones & sets / VM Scale Sets**, containers (ACR, ACI, Container Apps),
  **App Service**, Microsoft Entra ID administration specifics (users/groups/licenses/external/SSPR),
  **Azure Policy / management groups / locks / tags / cost management**, **Azure Monitor / Log
  Analytics / KQL / Insights**, and **Azure Backup / Recovery Services vault / Site Recovery**.
- **Lean on his strengths:** for D4 (virtual networking) and the security-adjacent storage/identity
  bullets, teach the *delta* only — map the Azure control onto the networking/security primitive he
  already owns, then spend the words on Azure's quirks, limits and portal paths. Do not re-teach NSGs
  as if he has never seen an ACL.
- **Lab access:** Azure subscription available and **cost is funded** — labs run live against real
  Azure resources. Still keep test data small and tear down (quota and region limits are real). No
  objective is licensing-blocked for him.

---

## TEACHING DOCTRINE — read this before writing any content

The learner has explicitly said **Microsoft Learn is hard to follow and buries the point in
irrelevant detail**. This system exists to fix that. Learn is a *citation source*, not a teacher.
Never tell the learner to "go read the module" as an explanation.

### Every objective page follows this shape, in this order

1. **In one sentence** — what this thing actually is. Plain words. No Microsoft marketing phrasing.
2. **Why it exists** — the problem it solves. Anchor to something the learner already knows (see
   THE LEARNER baseline) wherever an honest analogy exists — relate the new concept to a feature
   they have already mastered (an NSG rule *is* a firewall rule; a UDR *is* a static route).
3. **Mental model** — the one picture/analogy that makes it click. Text diagram is fine.
4. **What actually matters** — the 3–6 things worth knowing. Not the 40 settings the docs list.
5. **Where it lives** — exact portal path (`Azure portal > Virtual machines > Create`) and the `az`
   CLI / Azure PowerShell call if there is one. Precise, because the in-exam Learn pane rewards
   knowing where to look.
6. **Exam traps** — what gets tested, what's confusable with what, what the distractors will be.
7. **Skip list** — name what Microsoft Learn spends time on that is **not** worth studying, and say
   why. This section is mandatory and is the single most valuable part of the page for this learner.

### Rules of voice

- **Lead with the point.** Never build up to it. The first sentence is the answer.
- **Short sentences.** If a sentence has two clauses joined by "and," consider two sentences.
- **No Microsoft phrasing.** Never write "helps ensure," "seamlessly," "empowers," "robust,"
  "leverage," "holistic," "end-to-end." If a docs sentence sounds like a brochure, rewrite it.
- **Concrete over abstract.** "Blocks the traffic and logs a deny" beats "enforces policy."
- **Say what is NOT true.** Misconceptions kill more marks than gaps. Call them out explicitly.
- **Numbers and names exact.** Cmdlets, portal paths, SKU names, limits, defaults — never approximate.
  If unsure, fetch the doc and cite it. Precision here is non-negotiable even while the prose is loose.
- **Length scales with the objective's planned hours**, not a flat cap. Budget roughly **350–450 words
  of prose per planned hour**:

  | Planned | Prose target |
  |---|---|
  | 0.2–0.5 h | 200–400 words |
  | 1–1.5 h | 500–800 words |
  | 2–2.5 h | 900–1,200 words |
  | 3 h, high-risk | up to 1,700 words |

  Tables, SVG and image captions do **not** count toward the budget — a comparison table is dense and
  directly testable, and squeezing one out to hit a word count makes the page worse. Prose that runs
  long *beyond* these numbers means "What actually matters" is not doing its job.
- **Every factual claim carries a source URL** in the page's `ms_learn_urls` frontmatter.

### The skip list is the product

Microsoft Learn optimizes for completeness. This system optimizes for **passing the exam in the
planned hours**. When a module spends 20 minutes on something that is one bullet on the blueprint, say so:

> **Skip:** the module walks every blob access tier transition rule and rehydrate priority. The exam
> tests Hot/Cool/Cold/Archive and that Archive is offline. Ignore the rehydrate-priority detail.

---

## HARD TECHNICAL CONSTRAINTS

The site must open from `file://` with **zero network**. No npm, no CDN, no build tooling beyond
one stdlib-only Python script.

| Constraint | Rule |
|---|---|
| Chrome blocks ES `import` on `file://` | No `type="module"`. Classic `<script src>` only. One `window.AZ104` global. |
| Chrome blocks `fetch()` of local files | Build emits **`.js`** files that assign `window.AZ104.x = {...}`. Never `.json` fetched at runtime. |
| Python stdlib has no YAML | All structured content is **TOML** (`tomllib`, stdlib since 3.11). |
| Python stdlib has no Markdown lib | `build.py` has a hand-written markdown-subset renderer. Stay inside the subset (below). |
| `localStorage` on `file://` is unreliable | Progress = localStorage + Export/Import JSON buttons + build-time baked fallback. Use **one browser, one path**, export weekly. |
| No third-party Python packages | `build.py` imports stdlib only. If you reach for a package, redesign. |

### Markdown subset supported by `build.py`

`#`–`####` headings · paragraphs · `-` and `1.` lists (one nest level) · fenced code blocks with
optional language · `**bold**` `*italic*` `` `code` `` · **`==highlight==` spans** · `[text](url)`
links · GFM pipe tables · `>` blockquotes · `---` horizontal rules · **inline SVG diagrams** ·
**images**.

**Not supported:** raw HTML, reference links, footnotes, nested blockquotes, task lists.
The renderer warns on unsupported syntax rather than mangling it silently.

#### Inline highlight spans — `==text==`

For drawing the eye to a word or phrase *inside* a paragraph, table cell or list item. A leading sigil
picks a semantic role; the colour comes from the theme tokens, so it stays legible in light **and**
dark. **Never reach for raw colour** — there is no mechanism for it and a hex would die in one theme.
Four roles only, mirroring the SVG class vocabulary:

| Write | Role — use it for | Renders as |
|---|---|---|
| `==text==` | plain attention: the one keyword in a sentence the eye should land on | highlighter-yellow marker |
| `==@text==` | an exact Azure term / value / cmdlet the exam rewards knowing | accent (teal) tint |
| `==!text==` | a **trap**, a blocker, a "this is NOT true" — the thing that costs a mark | red tint |
| `==+text==` | the **correct** choice / the right answer / the safe path | green tint |

Rules: one sigil, at the start, right after `==`. Use it **sparingly** — a paragraph lit up all over
highlights nothing. Reserve `==!…==` / `==+…==` for genuine trap-vs-correct contrasts (natural fit in
**Exam traps**); `==@…==` for the exact term a question hinges on; plain `==…==` for the single scan
anchor in a dense line. It nests with `**bold**` and `` `code` `` and works inside tables and bullets.
`==` never appears in normal prose, so it will not fire by accident; PowerShell uses `-eq`, not `==`.

---

## DIAGRAMS — the learner asked for these specifically

The learner has said plainly that **pictures help them understand more than prose**, and that they want real
images **sourced** rather than drawn wherever a good one exists. Follow that order.

### RULE: include every Microsoft Learn image. Do not skip any.

**This is a standing instruction from the learner and it overrides editorial judgement about which
images "earn their place".**

For every article in an objective's `ms_learn_urls`, and for every unit of the AZ-104 training module
covering that objective, **harvest every image and put it on the page.** Do not filter for relevance.
Do not drop one because a drawn SVG already covers the same ground. Do not drop one because it
overlaps another screenshot. If Microsoft published a picture of this topic, he wants to see it.

The only four reasons to leave an image out:

1. **It is broken or a placeholder** — HTTP error, non-image content-type, or under ~2 KB (Learn serves
   354-byte shims for some missing assets). Verify before use; a broken image helps nobody.
2. **It is pure furniture** — a tab icon, a bullet glyph, a spacer, a logo.
3. **It is already on this same page.** Do not place the identical URL twice.
4. **It failed to cache** with `--fetch-images`. An image that cannot be pulled local would break the
   offline promise.

Nothing else. Not "redundant", not "low value", not "the SVG says it better", not "the Skip section
says to skip this topic".

**When two Microsoft sources contradict each other, show both and caption the contradiction.** That is
more useful than picking a winner silently. Example that actually occurs: the training module says
Azure Backup takes a VM restore point on one cadence, the shipping wizard shows another. Show both, and
say plainly that the product UI wins and why.

### Drawn SVG still has a place

Draw an inline SVG **in addition to** the sourced images, for what no screenshot shows: comparisons
across products, precedence chains, decision trees, lifecycles spanning several portals. Microsoft
documents those in prose only.

Images and diagrams are not in competition. A page with six screenshots and two drawn diagrams is a
good page.

### Sourced images

```markdown
![Create a storage account in the Azure portal](https://learn.microsoft.com/en-us/azure/storage/common/media/storage-account-create/create-storage-account-basics.png){The Basics tab — redundancy is chosen here}
```

Renders with a caption and a `source` link. `{Caption}` is optional and overrides the alt text.

**Finding the URL:** fetch the Learn article, look for `![...](media/...)` or `media/` paths in the
markdown, and build the absolute URL as
`https://learn.microsoft.com/en-us/<article-path>/media/<...>`. Verify it resolves before committing
to it — a broken image is worse than no image.

**Making it work offline:**

```bash
python3 build.py --fetch-images
```

Downloads every referenced remote image into `assets/img/cache/` and the build then serves the local
copy automatically. **Run this after adding any image.** Until you do, the image renders with a
"needs network" chip and will not display offline. Authoring stays clean — you write the Learn URL,
the cache is invisible.

### Inline SVG — for what cannot be photographed

A ```` ```svg ```` fenced block is emitted raw into the page. It is self-contained, works offline,
scales, and follows the light/dark theme. **Prefer this over everything else.**

````
```svg
<svg viewBox="0 0 640 200" role="img" aria-label="Three Azure Storage redundancy options compared">
  <rect class="box-accent" x="20" y="40" width="180" height="110" rx="8"/>
  <text x="110" y="70" text-anchor="middle" class="label-accent">LRS</text>
  <text x="110" y="95" text-anchor="middle">3 copies, 1 datacenter</text>
  <text x="110" y="118" text-anchor="middle" class="muted">cheapest, no region HA</text>
  <rect class="box" x="230" y="40" width="180" height="110" rx="8"/>
  <text x="320" y="70" text-anchor="middle">ZRS</text>
  <text x="320" y="95" text-anchor="middle">3 zones, 1 region</text>
  <text x="320" y="118" text-anchor="middle" class="muted">zone failure survived</text>
  <rect class="box-ok" x="440" y="40" width="180" height="110" rx="8"/>
  <text x="530" y="70" text-anchor="middle">GRS</text>
  <text x="530" y="95" text-anchor="middle">6 copies, 2 regions</text>
  <text x="530" y="118" text-anchor="middle" class="muted">region failure survived</text>
</svg>
```
````

**Use the provided CSS classes — never hard-code colours.** A hex fill will be invisible in one of the
two themes. Available classes:

| Class | Use |
|---|---|
| `.box` | neutral node |
| `.box-accent` | the thing being emphasised |
| `.box-risk` | a trap, a blocker, a failure state |
| `.box-ok` | a success or correct path |
| `.line` / `.line-accent` | connectors (add `marker-end` for arrows, define the marker inline) |
| `.divider` | a dashed boundary — a phase change, a trust boundary |
| `.muted` | secondary text |
| `.label-accent` / `.label-risk` | emphasised labels |

Rules: always set `viewBox` (never fixed `width`/`height`), always include `role="img"` and a real
`aria-label`, never include `<script>` (the build rejects it). Keep text at 13px or larger.

### When to draw

Draw when the idea is **structural and not photographable**: a comparison across products, a precedence
chain (NSG rule evaluation order), a decision tree (which redundancy / which load balancer SKU), a
"which of these four owns this" split, or a lifecycle spanning several portals. Microsoft documents
these in prose, so no screenshot exists.

Do not draw a list — a table is better.

**There is no upper limit on figures per page.** Every published Learn image goes in (see the rule
above), plus one or two drawn diagrams for what cannot be photographed. Eight figures on a meaty
objective is normal and correct.

---

## THE OFFICIAL CURRICULUM — `content/learn-map.toml`

**TODO: capture the AZ-104 curriculum.** Microsoft publishes a set of learning paths → modules →
units for AZ-104 (the "Azure Administrator" learning paths — identity/governance, storage, compute,
networking, monitoring/backup). `content/learn-map.toml` currently holds a **seed** (the five paths,
module stubs) so the build runs clean; fill in the units as each domain is authored, with each unit
mapped to the blueprint bullet it serves. `python3 build.py --check-learn-map` prints the coverage
report; the site renders it at `site/curriculum.html`, and every objective page lists the units behind
that bullet.

**Read the map before writing any objective page.** It answers three questions the study guide cannot:

1. **Which units teach this bullet.** Fetch those units for facts and images, not just the article set.
2. **Whether coverage is thin.** A bullet carried by one unit must be written from product
   documentation. The module will not be deep enough on its own.
3. **What to put in the Skip section.** Units with `off_blueprint = true` are material Microsoft
   teaches that the exam never asks for. Cite them by name and unit number, not "some of the module".

The blueprint and the curriculum are written by different teams and rarely line up one-to-one. When
you capture the map, record the mismatches explicitly — bullets with **no unit** (must come entirely
from docs), bullets resting on a **single unit** (thin), and **off-blueprint units** (Skip-section fuel).

When the map and a live page disagree, the live page wins — update the map and log it in
`content/CHANGELOG.md`. Never let the map drift silently; it is a captured snapshot, not truth.

---

## REFERENCE PAGES — `content/reference/*.md`

The learner works for an MSSP. Some things they need are useful at work and **never** on the exam: list
prices, SKU comparisons, region tables, partner-programme detail. Those go in `content/reference/`,
render at `site/reference/`, and are linked from the objectives they relate to.

**They do not go on objective pages.** An objective page is scarce exam budget; a price table
dilutes it and rots inside a quarter. If a reference page and an objective page cover the same product,
the objective page teaches the mechanics and links across.

Frontmatter: `slug`, `title`, `summary`, `captured`, `volatile`, `related_objectives`, `source_urls`.
Set `volatile = true` for anything that moves — it renders a "these numbers move, re-check before you
quote" banner, which is the honest thing to put in front of pricing. Cited URLs join `--check-links`,
so a restructured Microsoft pricing page shows up as a build signal rather than a silent lie.

---

## CHEATSHEETS — `content/cheatsheets/D{n}.md` (one per domain)

**Standing instruction from the learner: after each domain is authored, write a one-page cheatsheet
for it.** It is the last-night, single-sheet recap — the whole domain condensed to what survives into
the exam room. Unlike reference pages, a cheatsheet **IS exam content**. It renders at
`site/cheatsheet/<Dn>.html`, is print-first (Ctrl/Cmd + P → one or two pages), and is linked in the
top nav.

Rules:

- **One file per domain**, named `D{n}.md` (e.g. `D1.md`), frontmatter `domain`, `title`, `summary`,
  `captured`. `domain` must be a real domain id.
- **Condense, don't re-teach.** Every fact must already be grounded in that domain's objective pages —
  the cheatsheet never introduces a claim the pages don't carry. Ground exact numbers by reading the
  pages, not from memory.
- **Dense and tabular.** Lead with tables and one-liners; this is recall fuel, not prose. Use the
  `==!trap==` / `==+correct==` / `==@term==` highlight sigils for the trap-vs-truth contrasts.
- **A permissions table is mandatory.** The learner specifically wants "who can do what" summarized
  across *all* sections of the domain in one place — the exact role / right each action needs. Put it
  as its own section near the end (see `content/cheatsheets/D1.md` for the shape).
- **End with a "traps → truth" list** — the domain's highest-value distractors, one line each.
- Keep it to roughly two printed pages. If it runs longer, the objective pages are doing the teaching
  and the cheatsheet is over-reaching.

---

## DIAGRAMS SECTION — `content/diagrams/D{n}.md` (one per domain)

A sibling of the cheatsheet, rendered at `site/diagram/<Dn>.html` and linked in the top nav next to
Cheatsheets. Where the cheatsheet is dense text, this page is **a curated set of big pictures that
connect a domain's concepts** — the cross-page structure no single objective page owns, plus a map of
the Azure constructs that **look alike or do the same/close job** (a standing learner request: show the
family resemblance, even when a cousin lives in another domain).

Rules:

- **One file per domain**, `D{n}.md`, frontmatter `domain`, `title`, `summary`, `captured`.
- **Inline SVG only** — no sourced images here, so the page is offline by construction and follows the
  theme. Every `<svg>`: `viewBox`, `role="img"`, a real `aria-label`, text ≥13px, and **theme CSS
  classes only, never hard-coded hex** (see the DIAGRAMS drawing rules above).
- **Curate, don't dump.** A few large diagrams that each earn their place. The learner explicitly does
  **not** want every fact on one canvas — a diagram crammed with everything highlights nothing. If a
  picture gets crowded, split it or drop a node.
- **Forward-links for out-of-domain cousins.** When you pull in a construct from another domain to show
  a look-alike, draw it with the dashed-muted `box-fwd` class and label it by module (e.g. "NSG — D4").
  A legend states `dashed = another domain`. Anchor these to the learner's known baseline (an NSG rule
  *is* a firewall rule) and make **no detailed factual claims** about them here — they are pointers.
- **Don't silently duplicate a page-local SVG.** If an objective page already owns the canonical
  diagram (e.g. the four-gates picture on D1.3.1), show a recap here and **cross-link both ways**,
  rather than maintaining two copies that can drift.
- **Ground every in-domain claim** in that domain's objective pages (read them for exact numbers).

### Interactive walkthroughs — `assets/interactive/*.html`

For a concept that is best understood as **one scenario walked end to end** (rather than a static
picture), author a self-contained interactive HTML page under `assets/interactive/`. The asset loop
(`build.py`, the `ASSETS.iterdir()` copy) ships `assets/interactive/` to `site/interactive/`
automatically — **no engine change, and it never goes through the markdown renderer**, so the
no-raw-HTML / no-`<script>` guard on content pages is not relaxed. Rules:

- **Self-contained and offline.** Inline CSS + inline classic `<script>` (no `type="module"`, no CDN,
  no network). Embed the theme tokens (copy the `:root` / `:root[data-theme="dark"]` block from
  `assets/style.css`) so the page follows light/dark on its own and survives being moved.
- **One connected thread**, not a grid of separate panels — a persistent diagram that a step engine
  spotlights as a single narrative advances (Prev/Next + keyboard). Weave in the look-alike/confusable
  concepts on the same thread rather than listing them apart.
- **Ground every claim** in the relevant objective page(s); guard `localStorage` in try/catch
  (unreliable on `file://`).
- **Link it, don't orphan it:** add a one-line link from the objective page(s) it serves and from the
  domain's Diagrams page (e.g. `../interactive/<name>.html`). First one: `rbac-roles.html` (D1.2
  access story), linked from D1.2.1 and `diagrams/D1`.

### Static complete-view posters — `assets/visuals/*.html`

The non-interactive sibling: a **single dense page** that lays out *everything* about a topic at once —
all concepts plus comparison tables — like a printable infographic. Same mechanics as the interactive
pages (self-contained, embedded theme tokens, offline, shipped by the asset loop to `site/visuals/`,
never through the renderer). Use it when the learner wants the whole picture side by side rather than a
walked scenario. **Go wider than one objective** when the topic spans several (RBAC covers D1.2 +
touches D1.1/P2) and **ground every number in Microsoft Learn** (these posters carry exact limits, so
verify them live — don't trust memory). First one: `rbac-complete.html` (Azure RBAC complete reference),
linked from D1.2.1/2.2/2.3 and `diagrams/D1`.

---

## OBJECTIVE ID SCHEME

`D{domain}.{subdomain}.{bullet}` — e.g. `D2.3.5` = Domain 2, subdomain 3, bullet 5.

IDs are **stable and positional**. Question files, lab files and progress records all reference these
IDs — so if Microsoft reshuffles the outline, `content-freshness-checker` proposes a remap in
`content/CHANGELOG.md`. It **never** silently renames.

**Captured (2026-11-01):** **81 leaf bullets** across **5 domains / 15 subdomains**. Per-domain weights
are on each `[[domains]]` entry in `content/domains.toml` (`weight_min` / `weight_max` and the
`sample_weight` the mock-exam sampler uses; all five `sample_weight` sum to 1.0):

| Domain | Title | Weight | sample_weight |
|---|---|---|---|
| D1 | Manage Azure identities and governance | 20–25% | 0.2432 |
| D2 | Implement and manage storage | 15–20% | 0.1892 |
| D3 | Deploy and manage Azure compute resources | 20–25% | 0.2432 |
| D4 | Implement and manage virtual networking | 15–20% | 0.1892 |
| D5 | Monitor and maintain Azure resources | 10–15% | 0.1351 |

(All five `sample_weight` values sum to 1.0 — the mock-exam sampler depends on it.)

---

## NDA AND ETHICS — non-negotiable

Every practice question in this system is **original**, synthesized from public Microsoft Learn
documentation, and cites the URLs it was written from.

- Real exam content is under the Microsoft Certification Exam Candidate Agreement (NDA).
- Braindump material is fraudulently obtained; **using it for prep is itself cheating** and grounds
  for permanent ban from the Credentials Program.
- If asked to write questions "like the ones on the real exam" or "based on what I saw," refuse and
  explain. Write from documented product behavior instead — that is both legal and pedagogically better.
- Never scrape or reference certqueen, passcert, examtopics, or any dump site.

---

## LAYOUT

```
AZ-104/
├── CLAUDE.md              # this file
├── build.py               # stdlib-only build: parse → render → lint → index → emit site/
├── serve.py               # optional stdlib localhost server → page notes auto-save to progress/notes.json
├── content/
│   ├── domains.toml       # backbone: domains → subdomains → bullets
│   ├── learn-map.toml     # official curriculum: paths → modules → units → objective IDs
│   ├── objectives/*.md    # TOML frontmatter (+++) + markdown body, one per bullet
│   ├── questions/D{1..5}/*.toml
│   ├── questions/case-studies/*.toml
│   ├── labs/*.md
│   ├── reference/*.md     # NOT exam content — day-job material (pricing, SKUs, region detail)
│   ├── cheatsheets/D{n}.md # one-page printable domain recap — IS exam content, per-domain
│   ├── diagrams/D{n}.md   # curated cross-page big-picture SVGs — one per domain
│   ├── snippets/*.ps1
│   ├── notes/             # freeform, personal
│   └── CHANGELOG.md
├── progress/
│   ├── attempts.jsonl     # append-only attempt log
│   ├── notes.json         # on-page sticky notes (written by serve.py); export/import fallback on file://
│   ├── progress-export.json
│   └── study-plan.md
└── site/                  # 100% GENERATED — never hand-edit
```

`site/` is disposable. Delete it, run `python3 build.py`, get it back.

---

## SCORING RULES (mirror the real exam)

- Scaled 1–1000, pass 700. **No negative marking** — never leave a blank.
- `multiple_response` → **all-or-nothing**. Exact set match or zero.
- `drag_and_drop`, `build_list`, `hot_area`, `active_screen` → **partial credit per part**.
- `case_study` → each linked question scored by its own rule.
- `problem_solution_set` → the three proposals are scored **independently** and logged as three
  separate attempt records.
- The mock exam's 1–1000 number is a **linear approximation** and the UI must say so. Microsoft's
  real algorithm is undisclosed and near-certainly difficulty-weighted.

---

## WORKING RULES

- After any content change, run `python3 build.py`. It is idempotent and cheap.
- **Page notes & `serve.py`.** Every page carries an on-page sticky-note tool (`assets/notes.js`): the 📝
  button → click anywhere to drop a note. On `file://` notes live in `localStorage` with Export/Import.
  For real disk persistence, run `python3 serve.py` and open `http://localhost:8000` — notes then
  auto-save to `progress/notes.json` (GET/POST `/__notes`, localhost only, still offline). Never commit a
  real `progress/notes.json` as content; it is personal state like `progress/attempts.jsonl`.
- **Reviews section (`site/reviews.html`).** Full-topic, single-page "complete reference" reviews live as
  self-contained pages in `assets/visuals/*.html` and are auto-indexed into the **Reviews** nav entry
  (next to Reference) by `build_reviews_page` (reads each file's `<title>`). These are the one place
  content **may go beyond the AZ-104 blueprint** — a standing learner-approved exception; objective pages
  stay blueprint-scoped.
- `python3 build.py --lint` flags broken objective refs, schema errors, and near-duplicate question stems.
- `python3 build.py --check-learn-map` prints official-curriculum coverage: bullets with no unit,
  bullets carried by a single unit, and every off-blueprint unit. No network — reads the captured map.
- `python3 build.py --check-links` probes every cited URL for rot. **The only command here that uses
  the network.** Run it monthly with the freshness check. Microsoft consolidates and retires docs
  constantly — a citation that silently 301s to a *different* article is worse than a 404, because it
  still looks fine.
- New questions land with `verified = false`. Only `question-verifier` flips that flag, and only after
  independently re-deriving the answer from a fresh doc fetch. The writer never self-certifies.
- Lab guides run against a **real production-adjacent tenant**. Every lab scopes to test
  users/groups/resource-groups, carries an explicit Rollback section, and never instructs anything
  that could lock the admin out.
