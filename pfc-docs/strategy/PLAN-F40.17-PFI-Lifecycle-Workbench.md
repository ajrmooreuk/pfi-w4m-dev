# F40.17: PFI Lifecycle Workbench Integration — Skeleton-Driven UI for the 10-Step Pipeline

**Date**: 2026-02-22
**Author**: AJR Moore / Claude Code
**Status**: DELIVERED (commit pending, 1380/1380 tests pass)
**Parent**: Epic 40 (Design System & Application Framework)
**Depends on**: Epic 19 (F19.1–F19.5 complete), F40.13 (Skeleton System)

---

## Problem

Epic 19 delivered the full PFI graph composition engine — 33 functions across emc-composer.js. But **only 5 have UI bindings**. The operating guide documents a 10-step pipeline from instance creation through canonical freeze to persona workflows, yet **8 of 10 steps are code-only** with no buttons, panels, or visibility.

| Step | Function | Has UI? |
|------|----------|---------|
| 1. Create PFI Instance | `createPFIInstance()` | No |
| 2. Load Instance Data | `loadPFIInstanceData()` | No |
| 3. Populate Scope Rules | `populateScopeRulesFromEMC()` | No |
| 4. Resolve Product Context | `resolveProductContext()` | No |
| 5. Evaluate Scope Rules | `evaluateScopeRules()` | Partial (breadcrumb) |
| 6. Compose Instance Graph | `composeInstanceGraph()` | No |
| 7. Resolve Entity Bindings | `resolveProductBindings()` etc. | Partial (detail panel badges) |
| 8. Freeze Snapshot | `freezeComposedGraph()` | No |
| 9. Generate PFI Graph | `generatePFIGraph()` | Yes (persona chips + breadcrumb) |
| 10. Filter to Persona | `generatePersonaWorkflow()` | Yes (persona selector) |

**Result**: The BAIV-AIV discovery build cannot be operated from the workbench — users must call functions from the console.

---

## Solution

Use the skeleton-driven pattern from F40.13 to surface all 10 steps:

1. **PFI Lifecycle Panel** (Zone Z21, sliding, left, 420px) — progress tracker with action buttons per step
2. **Snapshot Manager Modal** (Zone Z18) — freeze, version history, inherit, diff
3. **Binding Inspector** (new sidebar tab in Z9) — product/ICP binding table with filters

### Architecture

```
Skeleton JSONLD (data-first)
  ├── PFC base: Z21 zone + 2 L3-admin NavItems (Lifecycle, Snapshots)
  └── BAIV override: 2 L4 NavItems (Compose Graph, Freeze Snapshot)
         ↓
Skeleton Loader (existing F40.13 code)
  ├── Renders NavItems as buttons automatically
  ├── Evaluates ds:visibilityCondition (state.isPFIMode)
  └── Calls ds:action → window function
         ↓
Handler Functions (app.js)
  ├── togglePFILifecyclePanel()
  ├── showSnapshotManager()
  ├── composeBAIVGraph()
  └── freezeBAIVSnapshot()
         ↓
UI Panels (pfi-lifecycle-ui.js — NEW)
  ├── renderPFILifecyclePanel() — 10-step progress tracker
  ├── renderSnapshotManager() — freeze/history/diff modal
  ├── renderBindingInspector() — product/ICP bindings table
  └── updateLifecycleStep() — status badge updater
```

---

## 7 Stories

### S40.17.1: PFC Skeleton v1.1.0 — Z21 Zone + Lifecycle NavItems
**File**: `PE-Series/DS-ONT/instance-data/pfc-app-skeleton-v1.0.0.jsonld`

- Add Zone Z21 (PFI Lifecycle Panel, Sliding, left, 420px, zIndex 60)
- Add NavItem `nav-L3-pfi-lifecycle` (L3-admin, Button, "Lifecycle", action: togglePFILifecyclePanel, visible when isPFIMode)
- Add NavItem `nav-L3-snapshots` (L3-admin, Button, "Snapshots", action: showSnapshotManager, visible when isPFIMode)
- Add ZoneComponent `cmp-pfi-lifecycle` (Z21, renderOrder 1)
- ~55 lines of JSONLD additions

### S40.17.2: BAIV Skeleton v1.1.0 — Compose/Freeze L4 NavItems
**File**: `PE-Series/DS-ONT/instance-data/baiv-app-skeleton-v1.0.0.jsonld`

- Add NavItem `nav-L4-baiv-compose` (L4, Button, "Compose Graph", action: composeBAIVGraph, visible when activeInstanceId === PFI-BAIV)
- Add NavItem `nav-L4-baiv-freeze` (L4, Button, "Freeze Snapshot", action: freezeBAIVSnapshot, visible when composedPFIGraph exists)
- ~40 lines of JSONLD additions

### S40.17.3: PFI Lifecycle Panel — 10-Step Pipeline UI
**File**: `js/pfi-lifecycle-ui.js` (NEW, ~100 lines)

`renderPFILifecyclePanel(instanceId)`:
- Renders 10 steps as a vertical progress tracker inside `#pfi-lifecycle-content`
- Each step shows: step number, label, status badge (pending → ready → complete → error)
- Steps 1-2: auto-detect from state (instance exists + data loaded)
- Step 3: "Load Scope Rules" button → `populateScopeRulesFromEMC()`
- Steps 4-6: "Compose Graph" button → runs resolve→evaluate→compose pipeline
- Step 7: "Resolve Bindings" button → `resolveProductBindings()` + `resolveICPBindings()` + `inferProductBindings()`
- Step 8: "Freeze Snapshot" button → opens snapshot manager modal
- Step 9: Status line with graph stats (entities, edges, ontologies)
- Step 10: Persona status (active persona name or "All")
- Footer: scope rule log summary ("N rules evaluated, M fired")

`updateLifecycleStep(stepNumber, status, detail)` (~20 lines):
- Updates a single step's badge and optional detail text
- Called by handlers after each pipeline operation

### S40.17.4: Snapshot Manager Modal — Freeze/Inherit/Diff
**File**: `js/pfi-lifecycle-ui.js` (~80 lines added)

`renderSnapshotManager(instanceId)`:
- Renders inside `#snapshot-modal-body`
- Top section: current composed graph spec summary (ontology count, entity count, join count)
- Inputs: version (semver text field), admin ref (text field)
- "Freeze" button → `freezeComposedGraph(spec, version, adminRef)` → updates lifecycle step 8
- Version history table from `listSnapshotVersions(specId)`:
  - Columns: version, status badge (locked=green, superseded=grey), frozenAt, frozenBy
  - "Inherit" button per locked row → `inheritSnapshot(instanceId, snapshotId)`
  - Checkbox select for diff → "Compare" button → `diffSnapshots(oldId, newId)`
- Diff results: summary panel (nodes added/removed/modified, edges added/removed)

### S40.17.5: Binding Inspector Sidebar Tab
**Files**: `js/ui-panels.js` (~40 lines), `js/pfi-lifecycle-ui.js` (~60 lines)

`renderBindingInspector(instanceId)`:
- New "Bindings" tab in Z9 sidebar (after existing Data tab)
- Tab only visible when `state.isPFIMode && state.composedPFIGraph`
- Product bindings table: entity ID, product code, binding type (explicit/inferred), confidence %
- ICP bindings table: entity ID, ICP ref, seniority level, cascade source
- Filter buttons: Explicit Only | Inferred | All
- Row click → highlights node in graph (reuses existing `focusOnNode()`)

### S40.17.6: app.js Handler Wiring + Skeleton Action Bindings
**File**: `js/app.js` (~60 lines)

New imports from `pfi-lifecycle-ui.js` and additional emc-composer.js functions.

Handler functions:
- `togglePFILifecyclePanel()` — toggle Z21 panel visibility, render lifecycle panel
- `showSnapshotManager()` — open Z18 modal, render snapshot manager
- `closeSnapshotModal()` — close modal
- `composeBAIVGraph()` — shortcut: `generatePFIGraph('PFI-BAIV', latestSnapshotId)`, update steps 4-6-9
- `freezeBAIVSnapshot()` — shortcut: open snapshot manager pre-populated for BAIV

Extend `selectPFIInstance()` handler: after existing composition, call `updateLifecycleStep()` for steps 1-2.

All handlers exported to `window.*` for skeleton action binding.

### S40.17.7: HTML Structure + Tests
**File**: `browser-viewer.html` (~45 lines)

Add Z21 panel div:
```html
<div id="pfi-lifecycle-panel" class="sidebar-panel left-panel" style="display:none;">
  <div class="panel-header">
    <h3>PFI Lifecycle</h3>
    <button onclick="togglePFILifecyclePanel()" title="Close">&times;</button>
  </div>
  <div id="pfi-lifecycle-content"></div>
</div>
```

Add snapshot modal (inside modal container):
```html
<div id="snapshot-modal" class="modal-overlay" style="display:none;">
  <div class="modal-content" style="max-width:600px;">
    <div class="modal-header">
      <h3>Snapshot Manager</h3>
      <button onclick="closeSnapshotModal()">&times;</button>
    </div>
    <div id="snapshot-modal-body"></div>
  </div>
</div>
```

Add bindings tab button to sidebar tab bar.

**File**: `tests/pfi-lifecycle-ui.test.js` (NEW, ~500 lines, ~40 tests)

7 describe blocks:
1. `renderPFILifecyclePanel` (~8 tests) — renders 10 steps, correct status badges, button handlers
2. `updateLifecycleStep` (~4 tests) — status transitions, error display
3. `renderSnapshotManager` (~8 tests) — version list, freeze button calls freezeComposedGraph, diff
4. `renderBindingInspector` (~6 tests) — product/ICP bindings rendered, filter toggle, confidence
5. `composeBAIVGraph` (~4 tests) — calls generatePFIGraph, updates lifecycle
6. `freezeBAIVSnapshot` (~4 tests) — opens modal, pre-populates BAIV
7. `Integration` (~6 tests) — full flow: select instance → lifecycle → compose → freeze → bindings

---

## Files Changed Summary

| File | Action | ~Lines |
|------|--------|--------|
| `pfc-app-skeleton-v1.0.0.jsonld` | Modify — add Z21 + 2 NavItems + ZoneComponent | +55 |
| `baiv-app-skeleton-v1.0.0.jsonld` | Modify — add 2 L4 NavItems | +40 |
| `js/pfi-lifecycle-ui.js` | **NEW** — lifecycle panel, snapshot manager, binding inspector | +350 |
| `js/app.js` | Modify — imports, 5 handler functions, window exports | +60 |
| `js/ui-panels.js` | Modify — Bindings tab button + handler | +40 |
| `browser-viewer.html` | Modify — Z21 div, snapshot modal, bindings tab | +45 |
| `tests/pfi-lifecycle-ui.test.js` | **NEW** — 7 describe blocks, ~40 tests | +500 |

**Total**: ~1090 lines new/modified across 7 files

---

## Patterns Reused

| Pattern | Source | Used For |
|---------|--------|----------|
| Skeleton NavItem → action → window function | F40.13 | All button bindings |
| Sliding panel (Z10/Z11/Z12/Z13) | Existing panels | Z21 lifecycle panel |
| Modal overlay (Z18) | GitHub settings, diff modal | Snapshot manager |
| Sidebar tab (Details/Connections/Schema/Data) | ui-panels.js | Bindings tab |
| `ds:visibilityCondition` | Skeleton system | PFI-mode gating |
| `vi.mock('../js/state.js')` | All test files | Test infrastructure |

---

## Execution Order

1. S40.17.1 — PFC skeleton JSONLD
2. S40.17.2 — BAIV skeleton JSONLD
3. S40.17.3 — Lifecycle panel (pfi-lifecycle-ui.js)
4. S40.17.4 — Snapshot manager modal
5. S40.17.5 — Binding inspector tab
6. S40.17.6 — app.js handler wiring
7. S40.17.7 — HTML + tests
8. Run tests → fix → create issues → close

---

## Verification

```bash
cd PBS/TOOLS/ontology-visualiser && npx vitest run
```

Target: ~1390 tests passing (1347 current + ~40 new), zero regressions.
Backward compatible: Z21 hidden when `isPFIMode === false`. Skeleton loader gracefully ignores unknown zones.

---

## Strategy Alignment

| Strategy | How This Feature Serves It |
|----------|---------------------------|
| S1 Graph-First | Surfaces graph composition + freeze pipeline in UI |
| S2 VE-Driven | Bindings inspector shows which entities carry product value |
| S4 Instance Custom | Skeleton cascade: PFC adds lifecycle zone, BAIV extends with compose/freeze shortcuts |
| S5 UI/UX | Skeleton-driven UI construction — data-first, zero-hardcoded buttons |
