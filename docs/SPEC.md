# Drawn — Product & Technical Spec

_Status: v0 spec, 2026-08-13. Distilled from initial ideation._

Drawn is a standalone, local-first desktop app for authoring custom icon
sets with AI agents. It connects to Honeybee/hive for agent execution and
will later ship an embeddable pane for Apiary. Its core bet: **icon-set
coherence is an engineering problem, not a prompting problem** — a style
is a typed spec with a deterministic linter and an adversarial critic, not
a paragraph of adjectives.

---

## 1. Positioning & naming

- **Name:** Drawn, as in *drawn comb* — the built-out foundation bees fill
  with work — and as in hand-*drawn*.
- **Vocabulary map** (use heavily in the data model, sparingly in UI copy):
  - a Set may be surfaced as a **Comb**
  - canvas grid slots are **cells**
  - the style spec is the **Foundation** — the stamped template comb is
    drawn on. (Naming caveat: an unrelated `foundation` MCP server exists
    in the local tool ecosystem; the term is fine inside Drawn's domain
    but avoid it in MCP-facing surface names.)
- **Peers/inspiration:** paper.design, magicpath — AI-native design tools
  with strong opinions and delightful in-progress states.

## 2. Core concepts

| Concept | Definition |
|---|---|
| **Set** | The unit of work. A collection of icons sharing one StyleSpec. Contains Sections. |
| **Section** | A named group inside a Set ("Home", "Money"). Purely organizational; icons can move between sections. |
| **Icon** | A named identity: slug, description, tags, section, and a **head pointer** into its version DAG. |
| **IconVersion** | Immutable, content-addressed (hash of normalized SVG). Records parent version(s), the StyleSpec version it was generated against, producing model, prompt, lint/critic scores. Never mutated, never deleted. |
| **StyleSpec** | The typed style document (§3). Versioned like everything else; icons record which spec version they target. |
| **Release** | A frozen snapshot mapping every icon in a set to one specific IconVersion. **All exports build from releases**, making font/PNG builds reproducible forever. |
| **Tag** | Global, cross-set. Powers the tag-based browse view (§7). |

## 3. The style system

A "style" is four artifacts working together. This is the heart of the
product.

### 3.1 StyleSpec — the typed document

Split into **hard constraints** (machine-checkable) and **soft
attributes** (prompt guidance). All fields editable at creation time and
after (edits create a new spec version).

**Geometry (hard):**
- grid size (e.g. 24px), live area / padding
- stroke weight(s), cap style, join style
- corner radius token(s)
- key shapes: the circle/square/rect templates icons must optically match
- minimum gap / feature size (e.g. nothing under 2px)
- max anchor budget per icon

**Construction (hard):**
- outline vs filled vs duotone vs filled+cutout
- stroke-as-attribute vs expanded paths — **font export requires
  expansion; this is a spec-time capability flag, not an export-time
  surprise**
- fill-rule, closed-paths-only, viewBox convention
- color: palette tokens + accent policy ("accent on the primary object
  only")

**Form language (soft):**
- curvature language: geometric vs organic/hand-drawn, tension
- corner personality: sharp / rounded / squircle
- perspective: flat frontal, isometric, slight 3/4
- detail density: minimal / medium / detailed

**Metaphor policy (soft, and the layer where sets visibly fall apart):**
- literal vs abstract dial
- conventions: badge placement/size ("plus badge always bottom-right,
  6px circle"), arrow style, how to depict people / documents / money
- prohibitions: no faces, no letterforms, etc.

### 3.2 Linter — deterministic, no AI

Derived from the hard constraints; runs on raw SVG: stroke width exact,
anchors snapped to grid, min-feature-size respected, live-area coverage in
range (e.g. 70–85%), path count within budget, paths closed, palette
compliance. Lint failures trigger **repair** (mechanical: snap, round,
re-weight) or **regeneration**.

### 3.3 Exemplars — few-shot canon

3–8 SVGs that *are* the style. Every generation prompt carries them.
"Use one icon as the baseline for a new set" (§7) = extract a StyleSpec
draft from it **+** promote it to exemplar #1. Early accepted icons get
promoted to exemplars so consistency compounds.

### 3.4 Critic — contrastive vision pass

Judges what the linter can't. Render the candidate in a 3×3 grid with 8
accepted siblings and ask: *"which one is the outlier, and why?"* The
contrastive frame is far more reliable than judging one icon in
isolation. Critic output feeds a per-set **style-drift scoreboard** so
cohesion degradation is visible before it's felt.

## 4. Generation pipeline

```
prompt(spec + exemplars + icon brief)
  → producer (agent)            — returns SVG + self-reported attributes
  → normalize (SVGO, grid snap) — producer output is UNTRUSTED input
  → lint                        — repair or regenerate on failure
  → critic (best-of-N pick)     — accept / retry
  → new IconVersion
```

- **Section-by-section, not all-at-once.** Generate the first section,
  curate, promote the best 2–3 to exemplars, then fan out the rest. A
  cold fan-out of 60 icons will drift.
- **Producers are pluggable.** Primary: LLM path authoring (heavy
  few-shot). Fallback for organic/hand-drawn styles where direct path
  authoring fails: image-gen → vectorize (trace) → simplify → snap.
- Best-of-N generation when the model is cheap; critic picks.

## 5. Agent integration (Honeybee / hive)

- Drawn talks to hive via the **honeybee SDK** directly (it does not
  depend on apiary's internal packages).
- A set generation is a fan-out swarm; each icon is one task. StyleSpec +
  exemplars ride in every prompt.
- Agents return **structured output** (SVG + attribute self-report); the
  app validates, normalizes, and lints before anything is accepted.
- Progress events stream over buz and drive the canvas loaders (§6).
- Model choice is part of the creation wizard (§8); Apiary integration
  later lets Apiary-managed agents do the work.
- Commercial angle: bring-your-own-model via MCP in the open-source app;
  hosted models in the cloud tier.

## 6. Canvas UX (set detail page)

- Semi-free-form: pan/zoom over **section bands**; auto-grid layout
  within a section; drag allowed only *between* sections. Constrained
  beats free-form — icon sets want order.
- **Loaders** (paper.design energy): shimmer/plasma placeholder in the
  cell while an agent works; when the SVG lands, animate the real paths
  drawing in via stroke-dashoffset. Cheap, delightful, and honestly
  communicates "an agent made this."
- **Click an icon → popover menu:**
  - regenerate
  - weight / fill variant
  - "more literal" / "more abstract"
  - compare-with-neighbors (shows the critic's 3×3 grid to the human)
  - version history (walk the DAG, move the head pointer)
  - freeform prompt box
  Every action is just a new IconVersion.

## 7. Cross-set features

- **Tags:** global; a tag view shows icons across all sets — a personal
  icon search engine (SQLite FTS5 over names/tags/prompts/descriptions).
- **Icon as seed:** any single icon can bootstrap a new set (spec
  extraction + exemplar promotion, §3.3).
- **Import-to-style:** drop N images/SVGs → vision model fills a
  StyleSpec *draft* → **user edits the draft before anything generates**
  → generate. Same pipeline as icon-as-seed; one feature, two entry
  points.

## 8. Creation flow (wizard)

1. **Style:** pick a preset (Lucide-like, SF-Symbols-like, hand-drawn,
   isometric duotone, …) or import-to-style; edit the StyleSpec in a
   friendly editor.
2. **Scope:** how many icons, which concepts — auto-suggested seed lists
   by domain, freely editable/extendable.
3. **Model:** which agent/model does the work (hive-backed; Apiary
   integration later).
4. **Canvas:** land on the set canvas and watch sections fill in.

## 9. Storage — SQLite, local-first

- **SQLite is the source of truth** (better-sqlite3 in the Electron main
  process for v0). SVGs are kilobyte-scale text — squarely where SQLite
  blobs beat the filesystem — and we get transactional release snapshots,
  real queries for tag views, and FTS5 for free.
- Sketch of the schema:
  - `icon_versions(hash PK, svg, created_at, spec_version, model,
    prompt, lint_score, critic_score)` — **append-only**
  - `icon_version_parents(version_hash, parent_hash)` — the DAG
  - `icons(id, set_id, section_id, slug, head_version_hash)`
  - `sets`, `sections`, `style_specs(id, set_id, version, json)`
  - `tags(name)`, `icon_tags(icon_id, tag)`
  - `releases(id, set_id, created_at)`,
    `release_entries(release_id, icon_id, version_hash)`
- **Local-first roadmap:** plain SQLite in v0; when cloud sync arrives,
  cr-sqlite (CRDT, real multi-writer) or libsql/Turso embedded replicas.
  The append-only content-addressed design is what makes this cheap:
  immutable hash-keyed rows sync trivially and never conflict — only
  head pointers and tag edits need conflict handling. **Keep that
  property sacred.**
- The git-diffable plain-directory format (`manifest.json`,
  `icons/<hash>.svg`, spec JSONs) survives as the **export/interchange
  format** — and is what cloud sync speaks at the edges.

## 10. Exports

All built from **releases** only:

1. SVG (canonical, normalized)
2. PNG at named sizes via resvg (deterministic; no headless browser)
3. Icon font via fantasticon — TTF/WOFF2 + CSS + codepoint map
   (requires the spec's `fontExportable` capability: monochrome,
   expanded strokes, single compound path)
4. React/Vue component codegen; sprite sheet
5. Later: Figma plugin, Tailwind plugin, SF Symbols template

## 11. Architecture & repo boundaries

- **Own project** (this repo), *not* part of the apiary monorepo. Lives
  under the honeybee pro area: `trmd:honeybee:drawn`.
- pnpm workspace:
  - `packages/core` — `@drawn/core`: domain model, StyleSpec schema
    (zod or similar), normalizer, lint rules, version DAG logic. Pure
    typed logic, no Electron, fully test-covered. **Host-agnostic by
    design** — this is what the Apiary pane later consumes.
  - `apps/desktop` — Electron shell: SQLite, hive/buz wiring, canvas UI.
- **The dependency points outward:** the future Apiary pane imports
  `@drawn/core` (+ an embeddable canvas component) as a dependency.
  Apiary stays clean; Drawn's domain stays host-agnostic — the same
  discipline the open-source/cloud split needs anyway.

## 12. Commercialization & license

- **License: FSL-1.1** (Functional Source License; commercial use
  reserved, converts to Apache-2.0 after two years). Preferred over
  AGPL+CLA: cleaner story, less contributor friction. To be confirmed
  before first public release.
- **Generated icons belong to the user, fully unencumbered.** Say it
  loudly; it's a trust differentiator against every AI design tool.
- **Free/open:** desktop app, bring-your-own-model via MCP.
- **Cloud (commercial):** hosted models, team sets, shared style
  libraries, public gallery (network effects), and **versioned CDN
  delivery of releases** (`cdn…/set@release/icon.svg`) — the sticky
  recurring-revenue piece.

## 13. Risks — honest ones

1. **LLM SVG quality** is the existential risk. Mitigations, in order:
   heavy few-shot exemplars → lint-repair loop → best-of-N + critic →
   trace-pipeline fallback. Producers are pluggable for exactly this
   reason.
2. **Set-level drift** — mitigated by section-by-section flow, exemplar
   promotion, contrastive critic, drift scoreboard.
3. **Font export constraints** discovered too late — mitigated by the
   spec-time capability flag.
4. **Scope creep toward a general design tool** — Drawn makes icon
   *sets*. The constraint is the product.

## 14. Roadmap

- **v0:** one set; StyleSpec wizard (presets + editing);
  section-by-section fan-out to a single hive-backed model; canvas with
  loaders; click-to-regenerate; lint loop; SVG export.
- **v1:** version-history UI; tags + cross-set view; critic loop;
  PNG/font export; import-to-style.
- **v2:** releases; Apiary pane (via `@drawn/core`); multi-model;
  cloud sync.

## 15. First code artifact

`@drawn/core`: StyleSpec schema + normalizer + lint rules + version-DAG
logic, fully unit-tested. Everything else depends on its shape.
