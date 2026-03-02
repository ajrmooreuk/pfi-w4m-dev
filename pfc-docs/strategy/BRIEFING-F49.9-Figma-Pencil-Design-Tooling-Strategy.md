# F49.9: Design Tooling Strategy — Figma vs Pencil Platform Impact Analysis

| Field | Value |
|---|---|
| **Feature** | F49.9 |
| **Parent Epic** | Epic 49 (#747) — VSOM Skilled Application Planner |
| **Version** | 1.0.0 |
| **Date** | 2026-02-26 |
| **Status** | PROPOSAL |
| **Classification** | Strategic / Internal |
| **Impacted Systems** | DS-ONT v3.0.0, App Skeleton, Token Map, Supabase Schema, SSO, EMC Composition Engine |
| **Related Epics** | Epic 40 (#577) Workbench, Epic 44 (#631) WWG DS Tokens, Epic 34 (#518) PF-Core Strategy S5 |

---

## 1. Executive Summary

PF-Core's UI/UX pipeline currently depends on **Figma** (Epic 34 S5: "UI/UX Figma Make") as the canonical design source, with MCP-based token extraction feeding DS-ONT instance data (Epic 44). A second MCP-integrated design tool — **Pencil** (`.pen` files) — offers fundamentally different capabilities: full **read/write design authoring** by Claude, versus Figma's **read-only + limited write** model.

This briefing analyses both tools against the six platform systems they touch: the DS-led design system, token map, zones/app skeleton, application framework, Supabase JSONB graph storage, and SSO/RBAC. The goal is to determine whether Pencil complements, replaces, or extends Figma in the PFC → PFI → Client cascade.

**Key Finding:** The tools are not competitors — they occupy different positions in the delivery pipeline. Figma is the **human design source of truth**; Pencil is the **agentic design execution engine**. Combined, they close Gap 5 (no zone CRUD) and accelerate the F49.5 zone allocator skill by enabling Claude to author skeleton-derived UI layouts directly.

---

## 2. Tool Capability Comparison

### 2.1 MCP Integration Matrix

| Capability | Figma MCP | Pencil MCP | Impact |
|---|:---:|:---:|---|
| **Read design structure** | Yes | Yes | Both can inspect layouts, components, tokens |
| **Read screenshots** | Yes | Yes | Both support visual verification |
| **Read design tokens/variables** | Yes (`get_variable_defs`) | Yes (`get_variables`) | Both feed DS-ONT pipeline |
| **Write/create designs** | No | **Yes** | Pencil enables agentic UI authoring |
| **Edit existing designs** | No | **Yes** | Pencil enables iterative refinement |
| **Insert components** | No | **Yes** (`I()` operation) | Pencil can compose from component libraries |
| **Layout engine** | No (read only) | **Yes** (auto-layout, fill, gap, padding) | Pencil computes layout, Figma reports it |
| **Component system** | Read only | **Full CRUD** (reusable, ref instances) | Pencil can build + instantiate design systems |
| **Design-to-code** | **Yes** (primary strength) | Via guidelines | Figma has richer code generation context |
| **Code Connect** | **Yes** (bidirectional mapping) | No | Figma maps design ↔ codebase components |
| **Style guide generation** | No | **Yes** (tags, themes) | Pencil generates design direction from prompts |
| **Theme/variable management** | Read only | **Read/Write** | Pencil can define and cascade brand tokens |
| **FigJam diagrams** | **Yes** (Mermaid → FigJam) | No | Figma for process/flow diagrams |
| **Multi-user collaboration** | **Yes** (native) | File-based | Figma is the collaboration platform |
| **Version control** | Figma-native | **Git-native** (.pen files) | Pencil integrates into PFI triad promotion pipeline |

### 2.2 Directional Summary

```
FIGMA                                    PENCIL
──────────────────────                   ──────────────────────
Human → Figma → Claude reads             Claude → Pencil → Claude writes
(Design source of truth)                 (Agentic design execution)

Strong at:                               Strong at:
  Brand design                             Programmatic UI composition
  Collaboration                            Skeleton-driven layout generation
  Design-to-code                           Token cascade authoring
  Component documentation                  Git-native version control
  FigJam diagramming                       Automated design validation

Weak at:                                 Weak at:
  Agentic authoring                        Human collaboration
  Programmatic layout                      Design-to-code maturity
  Git integration                          Ecosystem breadth
  Token write-back                         FigJam / diagramming
```

---

## 3. Impact on PFC Platform Systems

### 3.1 DS-Led Design System (DS-ONT v3.0.0)

**Current state:** DS-ONT defines 6 entity types (Application, AppZone, NavLayer, NavItem, Action, ZoneComponent) with a 3-tier token cascade (Primitive → Semantic → Component). Five PFI brand instances exist (BAIV, WWG, VHF, PAND, RCS). Token mutability rules (BR-DS-006) enforce PF-Core immutability on structural tokens with PFI-Instance override on brand tokens.

**Figma role (unchanged):**
- Remains the **brand design source** — human designers define palettes, typography, spacing in Figma
- `get_variable_defs` and `get_design_context` feed the 6-phase PE-DS-EXTRACT-001 v2.2.0 pipeline (Epic 44)
- Code Connect maps Figma components to codebase components (Shadcn, React, etc.)

**Pencil role (new):**
- **DS-ONT instance authoring** — Claude can compose PFI design system instances in `.pen` files, defining zone layouts, component placement, and token bindings programmatically
- **Prototype generation** — from an EFS PRD + skeleton spec, Claude can generate a `.pen` prototype that visualises the zone layout with correct brand tokens applied
- **Validation artefact** — `.pen` screenshots serve as visual acceptance criteria for stories in F49.5 (zone allocator) and F49.7 (spec composer)

**Cascade position:**

```
Figma (Human Design)
  → PE-DS-EXTRACT-001 v2.2.0 (Token Pipeline)
    → DS-ONT instance (JSONLD)
      → Pencil (Agentic Composition)
        → .pen prototype with tokens applied
          → Screenshot validation
            → Code generation (Tailwind/Shadcn)
```

**Impact:** Pencil extends the DS pipeline **downstream** of Figma. Figma is the token source; Pencil is the token consumer for agentic prototyping. No conflict — additive capability.

### 3.2 Token Map

**Current state:** Token Map panel (`js/design-token-tree.js`) renders tokens by-zone (Z1–Z20) and by-category (Surfaces, Text, Accent, Status, Archetypes, Edges, Typography, Spacing) with live swatches, HARDCODED badges, and OVERRIDE badges.

**Figma role:**
- Source of truth for primitive + semantic token extraction
- `get_variable_defs` provides W3C Design Tokens `$type`/`$value` aligned data
- Component-tier tokens extracted via `get_design_context` (F44.3 gap: WWG has 0 component tokens currently)

**Pencil role:**
- **Token application verification** — apply DS-ONT tokens to `.pen` layouts and validate visual correctness via `get_screenshot`
- **Token authoring for agentic scenarios** — when Claude creates new PFI zones (F49.4 zone CRUD), it needs to assign tokens to those zones. Pencil's `set_variables` enables Claude to define theme variants programmatically
- **Token map extension** — `.pen` files support variables with theme axes, enabling Claude to author light/dark theme variants that the Token Map panel can validate

**Impact:** Pencil becomes the **token verification and agentic authoring** layer. Figma remains the **token definition** layer. The Token Map panel can validate both sources.

### 3.3 Zones & App Skeleton

**Current state:** `pfc-app-skeleton-v1.0.0.jsonld` defines 22 zones (Z1–Z22), 5 nav layers, 26 nav items, 62 actions. Loader cascades PFC → PFI → Product → App. Zone CRUD is Gap 5 in the architecture doc — the skeleton editor cannot create, delete, or edit zones.

**Figma role:**
- Zone layout reference (screenshots of target zone composition)
- Component-to-zone mapping via Code Connect
- No ability to generate or modify skeleton JSONLD

**Pencil role (critical for Epic 49):**
- **Zone layout authoring** — Claude can compose zone layouts in `.pen` files, placing components in the correct zones with correct sizing and positioning
- **PFI zone generation** — F49.5 (zone allocator) maps EFS features to zones. Pencil can render the allocated layout as a visual `.pen` artefact, making the allocation tangible before code generation
- **Skeleton ↔ Pencil round-trip** — skeleton JSONLD zone definitions → Pencil layout → screenshot validation → code generation. This closes the loop on Gap 5: even though skeleton editor CRUD (F49.4) adds programmatic zone mutation, Pencil provides the **visual composition** layer

**Impact:** HIGH. Pencil is the missing visual authoring layer for the skeleton system. Without it, zone allocation (F49.5) produces JSONLD that no one can preview until code is written. With it, Claude generates a visual `.pen` preview from the allocation output.

### 3.4 Application Framework for UI/UX

**Current state:** The application framework spans three isolated systems (skeleton + EFS + PPM) that Epic 49 bridges into a unified Application Specification Model. The VE skill chain (Epic 48) automates from org-context through to VP. The gap is VP → PPM → Skeleton → Code.

**Figma in the framework:**

| Pipeline Stage | Figma Role |
|---|---|
| Brand definition | **Primary** — human designers define visual identity |
| Token extraction | **Primary** — MCP pipeline extracts tokens |
| Component library | **Primary** — design system components defined in Figma |
| Code Connect | **Primary** — maps components to React/Shadcn codebase |
| Zone layout | Reference only (screenshots) |
| Skeleton authoring | None |
| EFS → UI mapping | None |
| PPM → Release scoping | None |

**Pencil in the framework:**

| Pipeline Stage | Pencil Role |
|---|---|
| Brand definition | Downstream consumer — applies extracted tokens |
| Token extraction | None (reads from DS-ONT) |
| Component library | **Complementary** — builds from DS-ONT component specs |
| Code Connect | None |
| Zone layout | **Primary** — agentic layout composition |
| Skeleton authoring | **Primary** — generates zone prototypes from skeleton JSONLD |
| EFS → UI mapping | **Primary** — visualises feature → zone allocation |
| PPM → Release scoping | Complementary — renders phase/release views |

**Combined framework:**

```
VE Skill Chain (Epic 48)
  → pfc-efs (EFS PRD)
    → pfc-ppm-plan (F49.2)
      → pfc-zone-allocator (F49.5)
        ┌──────────────────────────────┐
        │ PENCIL: .pen zone prototype  │ ← NEW: visual verification
        │   - Zones from skeleton      │
        │   - Tokens from Figma/DS-ONT │
        │   - Components from DS-ONT   │
        └──────────────────────────────┘
        → pfc-spec-composer (F49.7)
          → Application Specification JSONLD
            ┌──────────────────────────────┐
            │ FIGMA: Code Connect mapping  │ ← Component → codebase
            │ PENCIL: Code generation      │ ← Layout → Tailwind/CSS
            └──────────────────────────────┘
            → GitHub Triad (F49.6)
```

### 3.5 Supabase Integration with JSONB for Ontologies

**Current state:** Supabase schema (Epic 10A, MVP Security VSOM v1.1.0) defines 5 tables: `pfi_instances`, `profiles`, `user_pfi_access`, `ontologies` (JSONB data), `audit_log` (append-only). The CONVERGENCE-FairSlice-JSONB-Graph-Patterns doc (Epic 50) defines a 3-phase migration from domain JSONB → graph abstraction → Neo4j sync. `resolve_cascaded_config()` merges Core → Instance → Scope tiers.

**Figma impact on Supabase:** Minimal. Figma operates upstream of the data layer. The only touchpoint is `brand_config JSONB` in `pfi_instances` (future S2), where DS token overrides per PFI could be sourced from Figma extractions.

**Pencil impact on Supabase:**

1. **Design artefact storage** — `.pen` files could be stored as JSONB in a new `design_artefacts` table or within the `ontologies` table as a DS-ONT instance attachment. This would make agentic-generated prototypes part of the auditable graph.

2. **Graph-driven design composition** — the JSONB graph pattern (graph_nodes + graph_edges) could represent skeleton zone relationships. Pencil can read these relationships and generate visual layouts from them. This creates a round-trip:
   ```
   Supabase JSONB graph → Skeleton JSONLD → Pencil .pen prototype → Screenshot → Stored back as JSONB metadata
   ```

3. **Cascaded config resolution for design** — `resolve_cascaded_config()` already merges Core → Instance → Scope for JSONB config. The same function could merge design token overrides:
   ```sql
   -- Existing: config cascade
   SELECT resolve_cascaded_config('BAIV-AIV', 'multipliers');

   -- Extended: design token cascade
   SELECT resolve_cascaded_config('BAIV-AIV', 'brand_tokens');
   ```
   Pencil's `set_variables` + theme axis support maps cleanly to this cascade model.

4. **Ontology-driven `.pen` templates** — DS-ONT zone definitions stored as JSONB could drive Pencil template generation. Claude queries Supabase for the PFI skeleton config, generates a `.pen` file, and stores the result back.

**Impact:** MEDIUM. Pencil creates a new artefact type (design prototypes) that fits the existing JSONB storage model. The cascade resolution pattern applies directly to design token overrides.

### 3.6 SSO & RBAC

**Current state:** Supabase Auth (email-based), 4 roles (pf-owner, pfi-admin, pfi-member, viewer), multi-PFI access via `user_pfi_access` junction table, RLS-enforced data isolation, append-only audit log.

**Figma SSO impact:**
- Figma has its own SSO (Enterprise plan) — separate from PFC auth
- No integration path between Figma SSO and Supabase Auth
- GitHub OAuth used for PAT-based content API access (`github-loader.js`)

**Pencil SSO impact:**
- `.pen` files are local/git-native — no separate auth layer
- Files can be scoped per PFI in the triad repo structure (`pfi-{instance}-{tier}/design/`)
- RLS applies at Supabase level when `.pen` artefacts are stored as JSONB
- **No additional SSO integration needed** — Pencil operates within the existing git + Supabase auth boundary

**RBAC implications:**

| Role | Figma Access | Pencil Access |
|---|---|---|
| pf-owner | Full Figma org (human) | All PFI `.pen` files via git |
| pfi-admin | PFI Figma project (human) | PFI triad repo `.pen` files |
| pfi-member | Read/comment Figma (human) | Read `.pen` via Supabase JSONB (RLS-filtered) |
| viewer | View Figma (human) | Read `.pen` screenshots via Supabase (RLS-filtered) |

**Impact:** LOW. Pencil inherits the existing auth model. No new SSO integration required. The key difference is that Figma requires human authentication at the Figma platform level, while Pencil operates within the Claude + git + Supabase boundary.

---

## 4. Strategic Positioning

### 4.1 Figma = Design Source of Truth

Figma retains its position as the canonical brand and component design system:
- Human designers define visual identity, component libraries, design tokens
- MCP extraction pipeline (PE-DS-EXTRACT-001 v2.2.0) feeds DS-ONT
- Code Connect maps design ↔ codebase
- Collaboration, review, handoff workflows remain in Figma

### 4.2 Pencil = Agentic Design Execution Engine

Pencil occupies the new position of programmatic, Claude-driven design composition:
- Consumes DS-ONT tokens and skeleton specs
- Generates zone layout prototypes from EFS → skeleton allocation
- Produces visual artefacts for verification and acceptance
- Git-native fits the PFI triad promotion pipeline
- Enables the F49.5 zone allocator to produce visual output, not just JSONLD

### 4.3 Neither Replaces the Other

```
Human creativity           ← FIGMA
  ↓ (tokens, components)
DS-ONT (shared truth)
  ↓ (specs, allocations)
Agentic composition        ← PENCIL
  ↓ (.pen prototypes)
Code generation            ← BOTH (Figma Code Connect + Pencil guidelines)
  ↓ (React/Tailwind)
Supabase (storage)         ← EXISTING
  ↓ (JSONB + RLS)
Deployed application       ← PFI TRIAD
```

---

## 5. Recommendations

### 5.1 Immediate (F49.9 Scope)

| # | Action | Priority |
|---|---|---|
| R1 | Define `.pen` template convention for PFI skeleton zones | HIGH |
| R2 | Create a DS-ONT → Pencil token bridge (map `ds:` tokens to `.pen` variables) | HIGH |
| R3 | Add `.pen` artefact type to PFI triad directory convention (`design/*.pen`) | MEDIUM |
| R4 | Extend Token Map panel to validate `.pen` token application | MEDIUM |

### 5.2 Near-Term (F49.5 + F49.7)

| # | Action | Priority |
|---|---|---|
| R5 | F49.5 zone allocator outputs both JSONLD and `.pen` prototype | HIGH |
| R6 | F49.7 spec composer includes `.pen` screenshot in Application Specification | MEDIUM |
| R7 | Supabase `design_artefacts` table for storing `.pen` metadata + screenshots | MEDIUM |

### 5.3 Future (Epic 34 S5 Evolution)

| # | Action | Priority |
|---|---|---|
| R8 | Figma → Pencil automated sync (extract from Figma, compose in Pencil) | LOW |
| R9 | Pencil → Code generation pipeline (`.pen` → Tailwind CSS via `get_guidelines`) | LOW |
| R10 | `.pen` files as JSONB in graph_nodes for full graph traversal | LOW |

---

## 6. Stories (Proposed)

| Story | Title | Description | Depends |
|---|---|---|---|
| S49.9.1 | Define `.pen` template convention for PFI zones | Convention doc: directory structure, naming, token binding rules for `.pen` files in PFI triads | — |
| S49.9.2 | DS-ONT → Pencil token bridge specification | Map DS-ONT 3-tier token cascade to Pencil variable/theme system. Define the translation schema. | S49.9.1 |
| S49.9.3 | Proof of concept — W4M-WWG zone layout in Pencil | Generate a `.pen` file for W4M-WWG zones using WWG brand tokens (from Epic 44) and skeleton spec | S49.9.2, F44.1 |
| S49.9.4 | Token Map panel `.pen` validation extension | Extend Token Map panel to import `.pen` variable definitions and compare against DS-ONT tokens | S49.9.2 |
| S49.9.5 | Supabase schema extension for design artefacts | Add `design_artefacts` table or extend `ontologies` JSONB to store `.pen` metadata | S49.9.3 |

---

## 7. Acceptance Criteria

- [ ] Briefing document reviewed and approved
- [ ] `.pen` template convention documented and linked to CONVENTION-PFI-Zone-ID.md
- [ ] DS-ONT → Pencil token bridge specification complete
- [ ] PoC: W4M-WWG zone layout rendered in `.pen` with correct brand tokens
- [ ] Token Map panel validates `.pen` token application
- [ ] Supabase schema extension designed (not necessarily implemented)

---

## 8. Risk Register

| Risk | Probability | Impact | Mitigation |
|---|---|---|---|
| Pencil MCP stability — tool is newer than Figma MCP | MEDIUM | HIGH | PoC with W4M-WWG first; don't migrate until proven |
| Token translation loss — DS-ONT → Pencil variable mapping loses fidelity | LOW | MEDIUM | Automated validation via Token Map panel comparison |
| Scope creep — Pencil adoption expands beyond prototyping into production design | MEDIUM | MEDIUM | Strict convention: Figma = design truth, Pencil = agentic prototyping |
| `.pen` format lock-in — proprietary format without export paths | MEDIUM | MEDIUM | Store screenshots + JSONB metadata alongside `.pen` files |

---

## Appendix A: MCP Tool Inventory

### Figma MCP Tools
- `get_design_context` — Primary design-to-code (returns code + screenshot + context)
- `get_screenshot` — Node screenshot
- `get_metadata` — XML structure overview
- `get_variable_defs` — Design token definitions
- `get_code_connect_map` / `add_code_connect_map` — Component ↔ code mapping
- `get_figjam` / `generate_diagram` — FigJam diagram creation
- `whoami` — Auth verification

### Pencil MCP Tools
- `batch_design` — Insert/Copy/Update/Replace/Move/Delete/Image operations
- `batch_get` — Read/search node patterns
- `get_editor_state` — Current selection and context
- `open_document` — Open/create `.pen` files
- `get_guidelines` — Design rules (landing-page, web-app, mobile-app, design-system, etc.)
- `get_style_guide_tags` / `get_style_guide` — Style inspiration
- `get_screenshot` — Visual verification
- `get_variables` / `set_variables` — Theme and token management
- `snapshot_layout` — Layout structure inspection
- `find_empty_space_on_canvas` — Canvas space detection
- `search_all_unique_properties` / `replace_all_matching_properties` — Bulk property operations

---

*This briefing is a living document. Update as F49.9 stories are refined and PoC results are available.*
