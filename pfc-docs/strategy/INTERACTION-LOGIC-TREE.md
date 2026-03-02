# Componentised Interaction Logic Tree — Ontology Visualiser v5.1.0

**Version:** 1.0.0
**Date:** 2026-02-18
**Scope:** 27 ES modules, 40 interaction components across 5 layers
**Purpose:** Map every graph interaction as a named, reusable component to enable harmonisation and inform PFC→PFI strategy graph architecture

---

## Architecture Overview

The visualiser's interaction surface spans 7 graph view types, 4 auxiliary views, and 2 authoring modes. Every user action on a graph node or edge is categorised into one of 5 component layers:

```
┌─────────────────────────────────────────────────────────────┐
│ Layer 5: Authoring Components (create/edit/delete/undo)     │
├─────────────────────────────────────────────────────────────┤
│ Layer 4: Context Components (PFI/brand/instance awareness)  │
├─────────────────────────────────────────────────────────────┤
│ Layer 3: Filter Components (composition/layer/bridge/legend)│
├─────────────────────────────────────────────────────────────┤
│ Layer 2: Selection Components (click/focus/highlight)       │
├─────────────────────────────────────────────────────────────┤
│ Layer 1: Navigation Components (drill/zoom/breadcrumb)      │
└─────────────────────────────────────────────────────────────┘
```

## Graph View Types

| View | Tier | Render Function | File |
|---|---|---|---|
| Series Rollup | Tier 0 | `renderTier0()` | `graph-renderer.js:792` |
| Series Drill-Down | Tier 1 | `renderTier1()` | `graph-renderer.js:971` |
| Sub-Series Drill-In | Tier 1b | `renderTier1SubSeries()` | `graph-renderer.js:1308` |
| Single Ontology Entity Graph | Tier 2 | `renderGraph()` | `graph-renderer.js:168` |
| All Ontologies Flat | Multi | `renderMultiGraph()` | `graph-renderer.js:536` |
| Connection Map | ConnMap | `renderConnectionMap()` | `graph-renderer.js:1557` |
| DS Token Cascade | DS | `_renderDSCascadeGraph()` | `app.js:3920` |
| Library Dependency Graph | Lib | `_renderLibDepGraph()` | `app.js:1280` |
| Mindmap Canvas | Mindmap | `initMindmapCanvas()` | `mindmap-canvas.js:1` |

---

## Layer 1: Navigation Components

Components that move the user between views or graph locations.

### NAV-001: DrillToSeries

| Property | Value |
|---|---|
| **Trigger** | `doubleClick` on series node |
| **Views** | Tier 0 |
| **Handler** | `graph-renderer.js:945` → `window.drillToSeries(seriesKey)` |
| **Node data** | `params.nodes[0]` (seriesKey string), `seriesData[seriesKey]` lookup |
| **Effect** | Sets `state.currentTier = 1`, `state.currentSeries = seriesKey`, renders Tier 1 |
| **Related epics** | Core navigation (completed) |

### NAV-002: DrillToSubSeries

| Property | Value |
|---|---|
| **Trigger** | `doubleClick` on sub-series grouping node |
| **Views** | Tier 1 |
| **Handler** | `graph-renderer.js:1268` → `window.drillToSubSeries(seriesKey, subSeriesKey)` |
| **Node data** | `nodeId` composite key (`"VE-Series::VSOM-SA"`), `state.subSeriesData[nodeId]` |
| **Guard** | `ssInfo.count > 0` (must contain ontologies) |
| **Effect** | Renders `renderTier1SubSeries(seriesKey, subSeriesKey, ...)` |
| **Related epics** | Epic 9C (#136, CLOSED) — Refine Ontology Series |

### NAV-003: DrillToOntology

| Property | Value |
|---|---|
| **Trigger** | `doubleClick` on ontology node |
| **Views** | Tier 1, Tier 1b, Connection Map |
| **Handlers** | `graph-renderer.js:1276` (Tier 1), `:1528` (Tier 1b), `:1745` (ConnMap) |
| **Node data** | `nodeId` = namespace string (e.g. `"vsom:"`), `record.isPlaceholder` |
| **Guard** | Record must exist and `!record.isPlaceholder` |
| **Effect** | Sets `state.currentTier = 2`, renders `renderGraph(record.parsed, { seriesKey, seriesColor })` |
| **Design rule** | DR-SERIES-003: series context border colour persists through drill-through |
| **Related epics** | Core navigation (completed) |

### NAV-004: CrossEdgeNavigate

| Property | Value |
|---|---|
| **Trigger** | `selectEdge` (click on cross-ontology edge) |
| **Views** | Tier 1, Tier 1b, Multi, Connection Map |
| **Handler** | `graph-renderer.js:143-158` (`attachEdgeClickHandler()`) |
| **Edge data** | `edge._crossOntologyTarget` (target namespace string) |
| **Guard** | Single edge selected, no nodes in params, target ontology loaded and not placeholder |
| **Effect** | Calls `window.drillToOntology(targetNs)` |
| **Gap** | Only works for cross-ontology edges; intra-ontology edges have no click handler |
| **Related epics** | Epic 54 (CLOSED) — Sub-Ontology Connections |

### NAV-005: NavigateUp

| Property | Value |
|---|---|
| **Trigger** | `Escape` or `Backspace` keydown |
| **Views** | Tier 1, Tier 2 (multi-ontology mode only) |
| **Handler** | `app.js:2083-2094` |
| **Effect** | Tier 2 → `drillToSeries(state.currentSeries)`; Tier 1 → `navigateToTier0()` |
| **Guard** | Must be in multi-ontology mode, `state.currentTier > 0` |

### NAV-006: NavigateHome

| Property | Value |
|---|---|
| **Trigger** | `H` keydown |
| **Views** | All multi-ontology tiers > 0 |
| **Handler** | `app.js:2105` → `navigateToTier0()` |
| **Effect** | Returns to Series Rollup (Tier 0) |

### NAV-007: SidebarNodeNavigate

| Property | Value |
|---|---|
| **Trigger** | Click on connection/inheritance item in sidebar |
| **Views** | Tier 2 sidebar |
| **Handlers** | `ui-panels.js:387` (outgoing), `:403` (incoming), `:432` (inheritsFrom), `:341` (Foundation ext) |
| **Effect** | `state.network.selectNodes([nodeId])`, `state.network.focus(nodeId, { scale: 1.5, animation: true })`, then `showNodeDetails(node)` |
| **Pattern** | Re-used `navigateToNode(id)` function across 4 contexts |

### NAV-008: AuditNodeFocus

| Property | Value |
|---|---|
| **Trigger** | Click on stub/isolated node in audit panel |
| **Views** | Tier 2 |
| **Handlers** | `ui-panels.js:174` (stubs), `:182` (isolated/silo) |
| **Effect** | `state.network.selectNodes([id])`, `state.network.focus(id, { scale: 1.5, animation: true })` |

### NAV-009: LibraryLoadOntology

| Property | Value |
|---|---|
| **Trigger** | `doubleClick` on node in library dependency graph |
| **Views** | Library panel |
| **Handler** | `app.js:1329` |
| **Effect** | `loadOntologyFromPanel(ns)` then `toggleLibrary()` — loads ontology and closes library |

### NAV-010: Tier0ViewToggle

| Property | Value |
|---|---|
| **Trigger** | Click on `#tier0-toggle` buttons (`data-view` attribute) |
| **Views** | Tier 0 |
| **Handler** | HTML button click handlers in `app.js` |
| **Modes** | "Series" → `navigateToTier0()`, "Ontologies" → `showAllOntologies()` (renderMultiGraph), "Connections" → `showConnectionMap()` (renderConnectionMap) |

---

## Layer 2: Selection Components

Components that select, inspect, or highlight nodes and edges without changing the view.

### SEL-001: ShowNodeDetails

| Property | Value |
|---|---|
| **Trigger** | `click` on node |
| **Views** | All tiers: Tier 0/1/1b/2/Multi/ConnMap |
| **Handler** | Each tier's click handler → `showNodeDetails(node._data)` |
| **Node data** | `node._data` (full parsed entity): `id`, `label`, `entityType`, `description`, `properties`, `series`, `ontologyId` |
| **Effect** | Opens right sidebar with entity detail panel |
| **Guard** | In Tier 2 with `state.selectionMode`, intercepted by SEL-006 instead |

### SEL-002: ShowConnectionsTab

| Property | Value |
|---|---|
| **Trigger** | `doubleClick` on node |
| **Views** | Tier 2, Multi |
| **Handlers** | `graph-renderer.js:288` (Tier 2), `:682` (Multi) |
| **Effect** | `showNodeDetails(node._data)` then `switchTab('connections')` — opens sidebar on Connections tab |
| **Guard** | In authoring mode, intercepted by SEL-007 instead |

### SEL-003: CloseSidebar

| Property | Value |
|---|---|
| **Trigger** | `click` on empty canvas |
| **Views** | All tiers |
| **Handler** | Each tier: `closeSidebar()` |
| **Effect** | Collapses right sidebar panel |

### SEL-004: DSTokenTrace

| Property | Value |
|---|---|
| **Trigger** | `click` on token node in DS cascade graph |
| **Views** | DS Cascade |
| **Handler** | `app.js:3960` → `traceTokenResolution(nodeId, parsed)` |
| **Effect** | Highlights traced nodes (opacity 1.0), dims others (opacity 0.15), updates stats bar with trace path |
| **Node data** | `nodeId`, `parsed` (DS instance), `trace.nodeIds`, `trace.path[].name`, `trace.path[].value` |

### SEL-005: DSTokenReset

| Property | Value |
|---|---|
| **Trigger** | `click` on empty canvas in DS cascade |
| **Views** | DS Cascade |
| **Handler** | `app.js:3985` |
| **Effect** | Resets all node opacity to 1.0, restores stats bar to default summary |

### SEL-006: AuthoringNodeSelect

| Property | Value |
|---|---|
| **Trigger** | `click` on node when `state.selectionMode` is active |
| **Views** | Tier 2 (authoring mode) |
| **Handler** | `graph-renderer.js:277` → `window.toggleNodeSelection(nodeId)` |
| **Effect** | Adds/removes node from `state.selectedNodeIds` Set, updates visual highlight |
| **Related epics** | Epic 7 (#79) — Ontology Authoring |

### SEL-007: AuthoringEntityEditor

| Property | Value |
|---|---|
| **Trigger** | `doubleClick` on node in authoring mode |
| **Views** | Tier 2 (authoring mode) |
| **Handler** | `graph-renderer.js:291` → `window.showEntityEditorUI(nodeId)` |
| **Guard** | `state.authoringMode && window.showEntityEditorUI` |
| **Effect** | Opens entity editor modal for inline editing |

### SEL-008: LegendHighlight

| Property | Value |
|---|---|
| **Trigger** | `mouseenter` / `mouseleave` on legend item |
| **Views** | All tiers (single and multi-ontology legends) |
| **Handler** | `graph-renderer.js:438-447` (`_attachLegendInteraction`) |
| **Effect** | After 100ms debounce, `state.network.selectNodes(matchingIds)` for highlight; `unselectAll()` on leave |
| **Guard** | Only fires when no `_legendActiveFilter` is set |

---

## Layer 3: Filter Components

Components that show/hide/dim subsets of the graph.

### FLT-001: CompositionFilter

| Property | Value |
|---|---|
| **Trigger** | PFI instance select / category chip toggle |
| **State** | `state.activeComposition`, `state.compositionFilterActive` |
| **Files** | `emc-composer.js`, `composition-filter.js` |
| **API** | `applyCompositionFilter(composition)` → `FilteredView`, `clearCompositionFilter()`, `getActiveFilteredView()` |
| **FilteredView** | `visibleNamespaces` (Set), `contextGhostNamespaces` (Set), `hiddenNamespaces` (Set), `activeSeriesSet` (Set), `filterLabel` (string) |
| **Effect** | All 5 render functions check `getActiveFilteredView()` at start; ghost nodes get 55% opacity + dashed borders, hidden nodes excluded |
| **Related epics** | Epic 9D (#138, CLOSED), Epic 9E (#139), Epic 9F (#140), Epic 19 (#196) |

### FLT-002: SemanticLayerFilter

| Property | Value |
|---|---|
| **Trigger** | Layer chip toggle, preset select, mode buttons, search input |
| **State** | `state.activeLayers`, `state.layerMode` ('and'/'or'), `state.layerFilterActive` |
| **File** | `layer-filter.js` |
| **API** | `computeLayerFilter(nodes, activeLayers, mode, crossRefNodeIds)` → `{ visible: Set, dimmed: Set }` |
| **Layers** | `strategic` (VE), `operational` (PE), `compliance` (RCSG), `foundation` (Foundation), `orchestration` (Orchestration), `crossRef` (overlay) |
| **Presets** | `complianceAudit`, `strategicOverview`, `fullMesh`, `crossRefOnly` |
| **URL serialisation** | `#layers=strategic,operational&mode=or&preset=strategicOverview` |
| **Related epics** | F8.7 (implemented) |

### FLT-003: BridgeNodeFilter

| Property | Value |
|---|---|
| **Trigger** | Bridge filter button |
| **State** | `state.bridgeFilterActive` |
| **Handler** | `app.js:1905-1919` (`toggleBridgeFilter()`) |
| **Detection** | `detectBridgeNodes(crossEdges, loadedOntologies, threshold=3)` → `Map<entityId, {refCount, referencingOntologies}>` |
| **Effect** | When active, only bridge nodes shown; 1.5x size, gold border `#eab839`, 4px, gold glow |

### FLT-004: CrossEdgeFilter

| Property | Value |
|---|---|
| **Trigger** | Cross-edge filter button |
| **State** | `state.crossEdgeFilterActive` |
| **Handler** | `app.js:1997` (`toggleCrossEdgeFilter()`) |
| **Effect** | When active, intra-ontology edges hidden, only cross-ontology edges shown |

### FLT-005: ComponentFilter

| Property | Value |
|---|---|
| **Trigger** | Component dropdown in audit panel |
| **State** | `state.componentColoringActive`, `state.componentFilter` |
| **Handlers** | `toggleComponentColoring()`, `filterComponent(value)` |
| **Data** | `audit.componentMap: Map<nodeId, componentIndex>`, `COMPONENT_COLORS[idx]` |
| **Views** | Tier 2 only (single ontology) |

### FLT-006: LegendClickFilter

| Property | Value |
|---|---|
| **Trigger** | `click` on legend item (toggle) |
| **State** | `_legendActiveFilter` (module-scoped, NOT in `state`) |
| **Handler** | `graph-renderer.js:462-466` (`_filterByLegend` / `_clearLegendFilter`) |
| **Effect** | Matching nodes opacity 1.0, non-matching 0.15; dims non-matching legend items |
| **Gap** | Ephemeral — not URL-serialisable, not persisted in state, unlike FLT-002 |

### FLT-007: SeriesHighlight

| Property | Value |
|---|---|
| **Trigger** | Series toggle button |
| **State** | `state.highlightedSeries` (Set, multi-select) |
| **Handler** | `app.js:1987-2024` (`toggleSeriesHighlight(seriesKey)`) |
| **Effect** | Highlighted series nodes get coloured glow ring, enlarged 1.15x; cross-ontology edges brightened with `SERIES_HIGHLIGHT_COLORS`; non-matching dimmed |
| **Lineage chains** | VE: `[VSOM, OKR, VP, PMF, EFS]`, PE: `[PPM, PE, EFS, EA]` — consecutive edges get chain-specific styling |
| **Convergence** | EFS appears in both chains → orange `#FF6B35`, 4px width |

### FLT-008: LayerSearchFilter

| Property | Value |
|---|---|
| **Trigger** | Search input within layer filter |
| **Handler** | `layerSearchUI(query)` in `app.js:4324-4596` |
| **Effect** | Searches node labels within layer visibility constraints; dims non-matching to 0.15 opacity |

---

## Layer 4: Context Components

Components that establish and communicate the active PFI/brand/instance context.

### CTX-001: PFIContextSwitch

| Property | Value |
|---|---|
| **Trigger** | PFI instance select in dropdown |
| **State** | `state.activePFI` |
| **Handler** | `app.js:2348-2419` (`_doSelectPFIInstance(instanceId)`) |
| **Effect** | 1) Sets `state.activePFI`, 2) Auto-resolves DS brand via CTX-002, 3) Applies CSS vars, 4) Updates context bar/glow/title, 5) Applies composition filter |
| **Persistence** | `localStorage.setItem('oaa-viz-pfi-instance', instanceId)` |
| **Note** | Auto-restore on page load was disabled to prevent silent filtering of RCSG/Orchestration series |
| **Related epics** | Epic 9D (#138), Epic 9E (#139), F8.8 (#145) |

### CTX-002: DSBrandResolution

| Property | Value |
|---|---|
| **Trigger** | Automatic from CTX-001 (PFI select) |
| **State** | `state.activeDSBrand`, `state.dsAppliedCSSVars`, `state.brandContext` |
| **Handler** | `resolveDSBrandForPFI(config)` in `ds-loader.js:745-767` |
| **Resolution tiers** | 1) `designSystemConfig.brand` → explicit, 2) `designSystemConfig.fallback`, 3) `brands[0].toLowerCase()` case-insensitive |
| **Effect** | `applyCSSVars(generateCSSVars(parsed))`, `refreshArchetypeCache()` (DR-SEMANTIC-005) |
| **Related epics** | Epic 8 (#80), Epic 8B (#85, CLOSED) |

### CTX-003: BrandGlowOverlay

| Property | Value |
|---|---|
| **Trigger** | Render-time when `state.brandContext` is set |
| **State** | `state.brandContext: { brand, tier, accentColor }` |
| **Effect** | Graph nodes get brand accent colour glow ring (DR-BRAND-001) |

### CTX-004: InstanceDataOverlay

| Property | Value |
|---|---|
| **Trigger** | Render-time when `state.activeInstanceId` has loaded data |
| **State** | `state.activeInstanceId`, `state.pfiInstanceData` |
| **Handler** | `graph-renderer.js:644-782` (`_mergeInstanceDataIntoGraph`) |
| **Effect** | Instance entities merged as `inst::` prefixed nodes with dashed brand-coloured borders, `[inst]` label prefix, `instanceOf` dashed edges to template counterparts |
| **Gap** | `state.activeInstanceId` is separate from `state.activePFI` — two overlapping state fields |

### CTX-005: ContextBar

| Property | Value |
|---|---|
| **Trigger** | PFI context switch |
| **Handler** | `updateContextBar()` in `app.js` |
| **Effect** | Persistent full-width bar showing current PFI context name and brand colour |
| **Status** | Partial implementation (F8.8 stories mostly unstarted) |

### CTX-006: GraphBorderGlow

| Property | Value |
|---|---|
| **Trigger** | PFI context switch |
| **Handler** | `updateGraphBorderGlow()` in `app.js` |
| **Effect** | Graph canvas border colour matches active PFI brand accent |
| **Status** | Partial implementation (F8.8.2) |

---

## Layer 5: Authoring & Mindmap Components

Components specific to content creation modes.

### AUTH-001: SelectionMode

| Property | Value |
|---|---|
| **Trigger** | Toggle button in authoring toolbar |
| **State** | `state.selectionMode`, `state.selectedNodeIds` (Set), `state.selectedEdgeIds` (Set) |
| **Handler** | `authoring-ui.js:557-673` |
| **Sub-actions** | Select All (`selectAllNodes`), Clear (`clearSelection`) |
| **Effect** | Intercepts SEL-001 click handler; enables multi-node selection for subgraph export |

### AUTH-002: UndoRedo

| Property | Value |
|---|---|
| **Trigger** | `Ctrl/Cmd+Z` (undo), `Ctrl/Cmd+Y` or `Ctrl/Cmd+Shift+Z` (redo), `Ctrl/Cmd+S` (save) |
| **Handler** | `app.js:4616-4622` |
| **Effect** | Undo/redo ontology edits; save with validation |
| **Guard** | Only active when `state.authoringMode` |

### AUTH-003: MindmapNodeSelect

| Property | Value |
|---|---|
| **Trigger** | `click` on node in mindmap |
| **State** | `state.mindmapSelectedNode` |
| **Handler** | `mindmap-canvas.js:44` |
| **Effect** | Sets selection, calls `_showNodeProperties(nodeId)` to populate properties panel |

### AUTH-004: MindmapEdgeCreate

| Property | Value |
|---|---|
| **Trigger** | Click source node → click target node (edge mode active) |
| **State** | `state.mindmapEdgeSource` |
| **Handler** | `mindmap-canvas.js:52-56` |
| **Effect** | First click sets source + highlights; second click calls `completeEdge(targetId, edgeType)` |
| **Edge type** | From `#mm-edge-type-select` dropdown value |

### AUTH-005: MindmapContextMenu

| Property | Value |
|---|---|
| **Trigger** | Right-click (`oncontext`) on node or empty canvas |
| **Handler** | `mindmap-canvas.js:98` → `_showContextMenu(x, y, canvasPos, nodeId)` |
| **Node actions** | "Edit Label" → inline prompt, "Connect From Here" → edge mode, "Delete Node" → remove + edges |
| **Canvas actions** | "+ Idea" → add ellipse node, "+ Action Card" → add box node |

### AUTH-006: MindmapInlineEdit

| Property | Value |
|---|---|
| **Trigger** | `doubleClick` on non-lane node |
| **Handler** | `mindmap-canvas.js:77` |
| **Guard** | `node._data.type !== 'lane'` |
| **Effect** | `prompt('Edit label:')` → `updateNodeLabel(nodeId, newLabel)` |

### AUTH-007: MindmapAddNode

| Property | Value |
|---|---|
| **Trigger** | `doubleClick` on empty canvas |
| **Handler** | `mindmap-canvas.js:88` |
| **Effect** | Converts DOM coords → canvas coords, calls `addIdeaNode(x, y)` |

### AUTH-008: MindmapDirtyTrack

| Property | Value |
|---|---|
| **Trigger** | `dragEnd` event |
| **Handler** | `mindmap-canvas.js:109` |
| **State** | `state.mindmapDirty = true` |
| **Effect** | Calls `_updateDirtyIndicator()`, triggers `_scheduleAutoSave()` |

---

## Global Interactions

### Keyboard Shortcuts

| Key | Context | Action | Handler |
|---|---|---|---|
| `Escape` | Modal open | Close active modal | `app.js:2058` |
| `Escape` | Tier 1/2 | Navigate up one tier | `app.js:2083` |
| `Backspace` | Tier 1/2 | Navigate up one tier | `app.js:2094` |
| `H` | Tier > 0 | Navigate to Tier 0 (home) | `app.js:2105` |
| `L` | Any | Toggle library panel | `app.js:2111` |
| `C` | Any | Toggle EMC context drawer | `app.js:2117` |
| `?` | Any | Show keyboard shortcut help | `app.js:2123` |
| `Ctrl+Z` | Authoring | Undo | `app.js:4616` |
| `Ctrl+Y` | Authoring | Redo | `app.js:4619` |
| `Ctrl+S` | Authoring | Save with validation | `app.js:4622` |
| `Delete` | Mindmap | Delete selected node | `app.js:4240` |
| `Escape` | Mindmap edge mode | Cancel edge creation | `app.js:4245` |

### vis-network Global Settings

All graph views share: `interaction: { hover: true, tooltipDelay: 200 }`. Hover info is delivered entirely via the native vis-network `title` property — no programmatic `hoverNode`/`hoverEdge` handlers exist.

### Auto-Fit

Every graph registers `.once('stabilizationIterationsDone')` → `state.network.fit({ animation: true })` for initial auto-fit after physics stabilisation.

---

## Interaction Matrix by View

| Component | Tier 0 | Tier 1 | Tier 1b | Tier 2 | Multi | ConnMap | DS | Lib | Mindmap |
|---|---|---|---|---|---|---|---|---|---|
| **NAV-001** DrillToSeries | **Y** | . | . | . | . | . | . | . | . |
| **NAV-002** DrillToSubSeries | . | **Y** | . | . | . | . | . | . | . |
| **NAV-003** DrillToOntology | . | **Y** | **Y** | . | . | **Y** | . | . | . |
| **NAV-004** CrossEdgeNav | . | **Y** | **Y** | . | **Y** | **Y** | . | . | . |
| **NAV-005** NavigateUp | . | **Y** | **Y** | **Y** | . | . | . | . | . |
| **NAV-006** NavigateHome | . | **Y** | **Y** | **Y** | **Y** | **Y** | . | . | . |
| **NAV-007** SidebarNav | . | . | . | **Y** | . | . | . | . | . |
| **NAV-008** AuditFocus | . | . | . | **Y** | . | . | . | . | . |
| **NAV-009** LibraryLoad | . | . | . | . | . | . | . | **Y** | . |
| **NAV-010** Tier0Toggle | **Y** | . | . | . | . | . | . | . | . |
| **SEL-001** ShowDetails | **Y** | **Y** | **Y** | **Y** | **Y** | **Y** | . | . | . |
| **SEL-002** ConnectionsTab | . | . | . | **Y** | **Y** | . | . | . | . |
| **SEL-003** CloseSidebar | **Y** | **Y** | **Y** | **Y** | **Y** | **Y** | . | . | . |
| **SEL-004** DSTokenTrace | . | . | . | . | . | . | **Y** | . | . |
| **SEL-005** DSTokenReset | . | . | . | . | . | . | **Y** | . | . |
| **SEL-006** AuthNodeSel | . | . | . | **Y**^a | . | . | . | . | . |
| **SEL-007** AuthEntityEd | . | . | . | **Y**^a | . | . | . | . | . |
| **SEL-008** LegendHover | **Y** | **Y** | **Y** | **Y** | **Y** | **Y** | . | . | . |
| **FLT-001** Composition | **Y** | **Y** | **Y** | . | **Y** | **Y** | . | . | . |
| **FLT-002** LayerFilter | **Y** | **Y** | **Y** | **Y** | **Y** | **Y** | . | . | . |
| **FLT-003** BridgeFilter | . | . | . | . | **Y** | . | . | . | . |
| **FLT-004** CrossEdgeFltr | . | . | . | . | **Y** | . | . | . | . |
| **FLT-005** ComponentFltr | . | . | . | **Y** | . | . | . | . | . |
| **FLT-006** LegendClick | **Y** | **Y** | **Y** | **Y** | **Y** | **Y** | . | . | . |
| **FLT-007** SeriesHighlt | . | **Y** | **Y** | . | **Y** | **Y** | . | . | . |
| **FLT-008** LayerSearch | . | . | . | **Y** | . | . | . | . | . |
| **CTX-001** PFISwitch | **Y** | **Y** | **Y** | . | **Y** | **Y** | . | . | . |
| **CTX-002** DSBrandRes | **Y** | **Y** | **Y** | **Y** | **Y** | **Y** | **Y** | . | . |
| **CTX-003** BrandGlow | . | . | . | **Y** | **Y** | . | . | . | . |
| **CTX-004** InstanceOverlay | . | . | . | . | **Y** | . | . | . | . |
| **AUTH-003..008** Mindmap | . | . | . | . | . | . | . | . | **Y** |

^a = authoring mode only

---

## Harmonisation Findings

### Inconsistency Analysis

| # | Issue | Current Behaviour | Impact | Proposed Resolution |
|---|---|---|---|---|
| H-001 | **doubleClick varies by tier** | Tier 0/1: drill down; Tier 2: show connections tab; Multi: show connections tab | Users learn different mental models per tier | **HR-001**: Unified contract — drill always navigates deeper; connections tab via sidebar click |
| H-002 | **No context menu in main graph** | Only Mindmap has right-click | Main graph misses discoverable actions (navigate, filter, cross-ref) | **HR-002**: Extend context menu to all views with view-appropriate actions |
| H-003 | **No multi-select in main graph** | Only Mindmap + authoring selection mode | Cannot batch-select for comparison, export, or analysis | Consider: extend selection mode beyond authoring |
| H-004 | **Legend filter is ephemeral** | `_legendActiveFilter` module-scoped, not in state | Resets on any re-render; not URL-shareable unlike layer filter | **HR-004**: Promote to `state.legendFilter`, add URL hash segment |
| H-005 | **Two PFI state fields** | `state.activePFI` (brand/composition) vs `state.activeInstanceId` (data overlay) | Confusing separation, potential desync | **HR-003**: Unify into `state.pfiContext = { instanceId, brand, composition, instanceData }` |
| H-006 | **Edge click limited** | `selectEdge` only for cross-ontology edges via `_crossOntologyTarget` | Intra-ontology edges in Tier 2 are not clickable | **HR-005**: Register edge detail handler in all tiers |
| H-007 | **No hover handlers** | All hover via vis-network native `title` tooltip | Cannot show cross-ref indicators, preview panels, or rich tooltips | **HR-006**: Add `hoverNode` for preview with cross-ref count badge |

### Proposed Harmonisation Rules

| Rule | Component Impact | Priority |
|---|---|---|
| **HR-001** Unified doubleClick contract | NAV-001/002/003, SEL-002 | Medium |
| **HR-002** Context menu everywhere | New component: CTX-MENU-001 | Low (post-MVP) |
| **HR-003** Merge PFI state | CTX-001, CTX-004 | High (prerequisite for Epic 19) |
| **HR-004** Legend filter parity | FLT-006 | Low |
| **HR-005** Edge click all tiers | NAV-004 extension | Medium |
| **HR-006** Hover intelligence | New component: SEL-009 | Low (post-MVP) |

---

## Related Documents

- [PFC-PFI-STRATEGY-GRAPH-MAPPING.md](_strategy/PFC-PFI-STRATEGY-GRAPH-MAPPING.md) — Strategy graph resolution pattern and epic cross-reference
- [BRIEFING-Epic34-PF-Core-VSOM-Platform-Strategy.md](_strategy/BRIEFING-Epic34-PF-Core-VSOM-Platform-Strategy.md) — Master VSOM
- [BRIEFING-Epic34-Graph-Series-Ontology-Mapping.md](_strategy/BRIEFING-Epic34-Graph-Series-Ontology-Mapping.md) — 8 graph series mapped to 42 ontologies
- [DESIGN-DIRECTOR-TOOLKIT-Graphing-Workbench-Decision-Tree.md](_strategy/DESIGN-DIRECTOR-TOOLKIT-Graphing-Workbench-Decision-Tree.md) — Workbench extensibility architecture
