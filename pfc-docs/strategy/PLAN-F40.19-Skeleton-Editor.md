# F40.19: Skeleton Editor — Reorder & Move Nav/Zone Mappings

**Date**: 2026-02-23
**Author**: AJR Moore / Claude Code
**Status**: DELIVERED (1508/1508 tests pass)
**Parent**: Epic 40 (Design System & Application Framework)
**Depends on**: F40.13 (Skeleton System), F40.18 (Skeleton Inspector Panel)

---

## Problem

The application skeleton (F40.13) defines nav item ordering and zone-component placements as static JSONLD. Changing the order of toolbar buttons, moving items between navigation layers, or reassigning components to different zones required manual editing of `pfc-app-skeleton-v1.0.0.jsonld`. There was no way to preview changes interactively or undo mistakes. Saving required downloading the file and manually replacing it in the ontology library.

---

## Solution

A built-in **Skeleton Editor** integrated into the Skeleton Inspector panel (Z22) with four mutation operations, snapshot-based undo/redo, and three persistence channels including direct save-to-library via the File System Access API.

### Mutation Operations

| Operation | Function | Effect |
|-----------|----------|--------|
| Reorder nav item | `reorderNavItem(itemId, direction)` | Swaps `ds:renderOrder` with adjacent sibling within the same layer |
| Move nav item | `moveNavItemToLayer(itemId, targetLayerId)` | Changes `ds:belongsToLayer` reference, appends at end of target layer |
| Reorder zone component | `reorderZoneComponent(placementId, direction)` | Swaps `ds:renderOrder` with adjacent sibling within the same zone |
| Move zone component | `moveZoneComponentToZone(placementId, targetZoneId)` | Changes `ds:placedInZone` reference, appends at end of target zone |

### Edit Controls (UI)

- **Edit/Done** toggle button to enter/exit edit mode
- **Up/Down arrows** on each nav item and zone-component row
- **Drag handles** (☰) for HTML5 drag-to-reorder (zero external dependencies)
- **Layer/Zone dropdowns** to move items between layers or zones
- **Undo/Redo** buttons (snapshot-based, follows `ontology-author.js` pattern)
- **Dirty dot** (amber) indicator when unsaved changes exist

### Persistence

| Channel | API | Availability |
|---------|-----|-------------|
| **Save to Library** | File System Access API (`showDirectoryPicker`) | Chrome/Edge — writes directly to `PE-Series/DS-ONT/instance-data/` |
| **Export** | Blob download | Any browser — downloads JSONLD for manual placement |
| **localStorage** | `oaa-viz-skeleton-edits` key | All browsers — auto-cached, auto-restored on next page load |

### Change Control

- `serializeSkeletonJsonld()` stamps `ds:dateModified` on the `ds:Application` entity
- Optional version bump changes both the entity version and the output filename
- `getSkeletonEditSummary()` provides a structured diff of all renderOrder and layer/zone changes

---

## Deliverables

| File | Change | Lines |
|------|--------|-------|
| `js/app-skeleton-editor.js` | **New module** — 15 exports, 3 internal helpers | ~465 |
| `js/app-skeleton-panel.js` | Rewritten — edit toolbar, drag handles, arrows, move selects | ~50 changed |
| `js/state.js` | +5 state properties (edit mode, dirty, undo/redo stacks, baseline) | 5 |
| `css/viewer.css` | +22 CSS rules (edit controls, drag, save button, dirty dot) | 22 |
| `browser-viewer.html` | +1 edit toolbar div | 1 |
| `js/app.js` | +13 imports, +11 window bindings, +2 drag handlers, +localStorage restore | 30 |
| `tests/app-skeleton-editor.test.js` | **New test file** — 42 tests in 14 groups | ~550 |

**Documentation updated:**
- `ARCHITECTURE.md` — module count 19→20, new Skeleton Editor section, dependency graph
- `OPERATING-GUIDE.md` — new Workflow 25: Skeleton Editor
- `APP-SKELETON-GUIDE.md` — Section 8.5 updated with interactive option, new Section 8.7, updated file/test references
- `DESIGN-SYSTEM-OVERVIEW.md` — test count 1466→1508, cross-reference map entry

---

## Key Design Decisions

1. **PFC-level editing only** — No cascade immutability checks needed since we're editing the source skeleton directly in the Azlan repo, not overriding from a higher tier
2. **Snapshot-based undo** — Full JSON.stringify/parse at each mutation point (same pattern as ontology-author.js). Simple, reliable, no inverse-operation complexity
3. **File System Access API for save** — Allows direct write to the ontology library without leaving the browser. Falls back gracefully to download for Firefox/Safari
4. **localStorage session resilience** — Edits survive page refresh. Auto-restored before registry fetch in `loadAppSkeleton()`. Cleared on explicit save or discard
5. **HTML5 Drag and Drop** — Zero external dependencies for drag-to-reorder. Uses `dataTransfer` with `nav:` and `cmp:` prefixes to distinguish item types
6. **In-memory apply** — Every mutation calls `buildSkeletonRegistries()` + `renderNavFromSkeleton()` + `renderSkeletonPanel()` for immediate visual feedback

---

## Test Coverage (42 tests)

| Group | Tests | Coverage |
|-------|-------|----------|
| Edit mode management | 5 | Enter/exit, baseline snapshot, discard restore |
| Reorder nav items | 6 | Up/down, boundary checks, non-existent items |
| Move nav items | 4 | Between layers, already-there guard, non-existent |
| Reorder zone components | 2 | Up/down swap |
| Move zone components | 3 | Between zones, already-there guard |
| Undo/redo | 5 | Single undo, redo, empty stack, multi-step |
| Export JSONLD | 4 | dateModified stamp, version bump, Blob creation |
| Serialize JSONLD | 3 | Graph structure, context, dateModified |
| Change summary | 3 | No changes, renderOrder diff, layer move diff |
| localStorage persistence | 4 | Save, restore, clear, timestamp |
| Pending edits check | 2 | Has/hasn't pending |
| Save to library | 1 | Fallback to download when API unavailable |

---

## Verification

1. `npx vitest run` — **1508/1508 tests pass** (42 new + 1466 existing)
2. Open workbench → Skeleton panel → Edit → reorder nav items with arrows
3. Drag items to reorder within layers
4. Move items between layers via dropdown → toolbar re-renders immediately
5. Undo/Redo preserves full state
6. Save to Library → File System Access API prompts for directory → writes to `PE-Series/DS-ONT/instance-data/`
7. Refresh page → localStorage auto-restores pending edits
8. Discard → baseline restored, dirty flag cleared

---

<!-- F40.19 Skeleton Editor — Feature Bulletin -->
