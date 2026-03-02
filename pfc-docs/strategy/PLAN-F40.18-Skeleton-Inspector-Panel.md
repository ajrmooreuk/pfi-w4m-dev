# F40.18: Application Skeleton Inspector Panel (Z22)

**Date**: 2026-02-23
**Author**: AJR Moore / Claude Code
**Status**: DELIVERED (1466/1466 tests pass)
**Parent**: Epic 40 (Design System & Application Framework)
**Depends on**: F40.13 (Skeleton System), F40.17 (PFI Lifecycle Workbench)

---

## Problem

The application skeleton (F40.13) defines the workbench's 22 zones, 5 nav layers, 26 nav items, and 23 component placements — but there was no way to **inspect** this structure from within the workbench itself. The DS Panel (Z12) provides introspection for design tokens; there was no equivalent for the skeleton architecture. Developers and architects had to read the raw JSONLD or consult external documentation to understand zone layout, component placement, and navigation structure.

---

## Solution

A new **App Skeleton Inspector Panel** at zone Z22 — a left-side sliding panel (440px) with three tabs and a spatial wireframe diagram. The panel reads from the existing runtime registries (`state.zoneRegistry`, `state.navLayerRegistry`) that are already populated by `app-skeleton-loader.js`.

### Spatial Diagram (CSS Grid Mini Wireframe)

A colour-coded spatial map at the top of the panel showing all zones positioned as they appear in the workbench:
- **Teal** = Fixed zones (Z1 Header, Z2 Toolbar, Z6 Canvas)
- **Blue** = Sliding zones (Z9 Sidebar, Z12 DS Panel, Z22 Skeleton Inspector, etc.)
- **Amber** = Conditional zones (Z3 Context Bar, Z4 Authoring, Z5 Breadcrumb)
- **Purple** = Floating zones (Z7 Legend, Z8 Layer Panel)
- **Grey** = Overlay zones (Z18 Modal, Z19 Tooltip, Z20 Drop Zone)

Currently visible zones are opaque; hidden zones are dimmed with dashed borders. Clicking any zone block scrolls to its detail card in the list below.

### Three Tabs

| Tab | Content |
|-----|---------|
| **Zones** | All 22 zones with type, position, width, defaultVisible, visibilityCondition, z-index, cascade tier, component count |
| **Functions** | Zone-to-component mapping — which components are placed in which zones, with placementId, slot name, component reference, and cascade tier |
| **Nav Layers** | L1–L4 navigation layers with their items: label, itemType (Button/Toggle/Dropdown/Select/Chip/Separator), action binding, keyboard shortcut, visibility condition, cascade tier |

All entities display PFC/PFI cascade tier badges (green = PFC immutable, blue = PFI override).

---

## Deliverables

| File | Change | Lines |
|------|--------|-------|
| `js/state.js` | +2 state properties (`skeletonPanelOpen`, `skeletonPanelTab`) | 2 |
| `css/viewer.css` | +55 lines panel/tab/diagram/card CSS | 55 |
| `js/app-skeleton-panel.js` | **New module** — 7 exports, 5 internal helpers | ~260 |
| `browser-viewer.html` | +1 toolbar button, +1 panel div with 3 tabs | 15 |
| `js/app.js` | +1 import, +2 window bindings | 4 |
| `js/design-token-tree.js` | Z22 in FALLBACK_ZONE_TREE, ZONE_TO_TOKEN, ZONE_DOM_SELECTORS | 15 |
| `pfc-app-skeleton-v1.0.0.jsonld` | +1 zone (Z22), +1 nav item, +1 component | 40 |
| `tests/app-skeleton-panel.test.js` | **New test file** — 17 tests in 6 groups | ~280 |
| `tests/design-token-tree.test.js` | Updated 8 count assertions (21→22, 78→82) | 8 |

**Documentation updated:**
- `ARCHITECTURE.md` — module count 18→19, zone count 21→22, Z22 in DOM selectors
- `OPERATING-GUIDE.md` — new Workflow 24, toolbar reference, zone map
- `APP-SKELETON-GUIDE.md` — zone diagram, visibility table, cascade counts, file reference
- `DESIGN-SYSTEM-OVERVIEW.md` — zone count, zone diagram, cascade diagram, key numbers table

---

## Test Results

```
Test Files  44 passed (44)
     Tests  1466 passed (1466)
  Duration  1.08s
```

17 new tests across 6 describe groups:
1. `toggleSkeletonPanel` (3) — open/close/absent element
2. `switchSkeletonTab` (3) — functions/nav tab switching, state update
3. `renderSpatialDiagram` (3) — zone blocks, visible/hidden classes
4. `renderZonesTab` (3) — zone cards, metadata, empty state
5. `renderFunctionsTab` (2) — component mappings, empty zones
6. `renderNavTab` (3) — layer sections, item lists, metadata

---

## Usage

1. Click **Skeleton** in the toolbar (L3-admin layer)
2. Panel slides in from the left
3. Browse the spatial diagram — click zones to navigate
4. Switch between Zones, Functions, and Nav Layers tabs
5. Inspect cascade tier badges to distinguish PFC baseline from PFI overrides
