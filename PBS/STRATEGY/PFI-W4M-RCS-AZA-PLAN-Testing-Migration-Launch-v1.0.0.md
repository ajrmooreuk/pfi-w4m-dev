# PFI-W4M-RCS-AZA — Strategy Plan: Testing, Migration & Launch

| Field | Value |
|---|---|
| **Document Code** | PFI-W4M-RCS-AZA-PLAN-Testing-Migration-Launch-v1.0.0 |
| **Product** | PFI-W4M-RCS-AZA (Azure Landing Zone Assessment) |
| **Owner** | Amanda Moore |
| **Date** | 2026-03-27 |
| **Status** | For Review |
| **Purpose** | Strategy-led plan to migrate AZA-ALZ into W4M-RCS repo, stand up the EFS Product Board, move to Testing, and iterate to live |

---

## 1. Strategic Intent

AZA-ALZ-HCR is a **built product in test-data mode** — 27/27 skills active, 490 tests passing, 22/27 Epic 74 features closed. The product needs to move from the hub development posture into its **product home** (W4M-RCS) with its own governance, board, and promotion path.

This plan delivers three things:

1. **Migrate** — Move AZA-ALZ product management into pfi-w4m-dev with EFS-governed Product Board
2. **Test** — Promote to pfi-w4m-test with structured acceptance testing against test data
3. **Iterate** — Wire live Azure MCP (Epic 85), validate against real tenant, ship

### Principles

- **W4M-RCS is the product home** — AZA-ALZ-HCR is a W4M-RCS product, not an AIRL product. AIRL receives a FairSlice license separately
- **PFI issues are self-contained** — No references to Azlan-EA-AAA hub issue URLs in pfi-w4m-dev issues (hub may reference PFI, one-way only)
- **Source-of-truth stays in PFC** — Ontologies, skills register, sealed skills remain in Azlan-EA-AAA. W4M-RCS receives via cascade
- **Test data is scenario-driven** — 7 canonical test scenarios + TD-ALZ-001 Contoso Financial Services. These are real-world profiles, not mocks
- **No Supabase yet** — JSON/JSONLD + GitHub PM workflows is the data + automation layer

---

## 2. Current Position

| Asset | Location | Status |
|---|---|---|
| 27 sealed skills (SKL-086-112) | pfc-core (cascaded) | Active |
| HCR UI/UX (37 files, 490 tests) | Azlan-EA-AAA/PBS/TOOLS/pfc-web-app | Complete |
| 7 test scenarios (JSONLD) | Azlan-EA-AAA AZALZ-ONT instance-data | Complete |
| TD-ALZ-001 canonical test data | Azlan-EA-AAA pfc-web-app/src/lib | Complete |
| 14 ontologies (GRC + VE series) | Azlan-EA-AAA ontology-library | Live |
| 50+ strategy docs | Split across hub + pfi-w4m-dev | Need consolidation |
| GitHub Project #78 (PFI-RCS Product) | ajrmooreuk | **Empty — 0 items** |
| GitHub Project #77 (PFI-RCS Engineering) | ajrmooreuk | 8 items |
| EFS Product Backlog | Not formalised | **Gap** |
| pfi-w4m-test repo | ajrmooreuk/pfi-w4m-test | Exists, not promoted to |

### What's in pfi-w4m-dev today (AZA-related)

| Directory | Contents |
|---|---|
| PBS/Docs/ | 11 AZA docs (strategy briefs, refs, customer HCR template, master review) |
| PBS/STRATEGY/ | Strategy index, CI/CD roadmap, URG briefs |
| pfc-core/skills/ | 25 sealed skills (VE + DELTA + lifecycle) — AZA skills distributed via cascade |
| instance-data/ | PFI config, graph-scope, tokens — no AZA instance data |
| docs/ | EFS spec, operating guides, architecture decision tree |

### What's in the hub that needs to come across

| Hub Location | Contents | Count |
|---|---|---|
| PBS/STRATEGY/PFI-W4M-RCS/ | Build plan, gap analysis, build status v1+v2, VE brief, strategy brief | 6 docs |
| PBS/STRATEGY/PFI-AIRL/ (AZA-specific) | ALZ architecture, skill chain readiness, DMAIC methodology, HCR deliverable, guides (architect/developer/user), test plan, document sync manifest | 10 docs |
| PBS/STRATEGY/PFC-ARCH/ | AZA distribution spec, GRC HLD, GRC decisions | 3 docs |
| PBS/TOOLS/pfc-web-app/src/components/hcr/ | 26 HCR components | 26 files |
| PBS/TOOLS/pfc-web-app/src/hooks/hcr/ | 5 HCR hooks | 5 files |
| PBS/TOOLS/pfc-web-app/src/lib/hcr-* | Types, test data, export, print | 4 files |
| AZALZ-ONT instance-data/ | 7 test scenarios (JSONLD) | 1 file |

---

## 3. Plan — Four Phases

```
Phase 1: MIGRATE          Phase 2: BOARD           Phase 3: TEST            Phase 4: ITERATE
(Docs + Structure)        (EFS + Issues)           (Promote + Validate)     (Live MCP + Ship)
─────────────────────     ──────────────────       ──────────────────       ──────────────────
Copy strategy docs        Create AZA-ALZ EFS       Promote dev → test       Epic 85 MCP wire
Copy HCR components       Populate Project #78     Run acceptance suite     Live tenant test
Copy test data            Create W4M-RCS issues    Validate 7 scenarios     E2E pipeline
Update instance-data      Link to Project #77      UX walkthrough           First customer
Update graph-scope        Automation workflow       Bug triage + fix         HCR v2.0 report
                          Standing issue set        Iterate                  Promote to prod
```

---

### Phase 1: MIGRATE — Docs, Components & Test Data into W4M-RCS

**Goal:** All AZA-ALZ-HCR product assets live in pfi-w4m-dev. Hub retains source-of-truth (ontologies, skills register). W4M-RCS has everything needed to build, test, and ship independently.

#### 1.1 Strategy Docs Migration

Copy hub strategy docs into `PBS/STRATEGY/PFI-W4M-RCS-AZA/` (new subdirectory):

| Source (Hub) | Target (W4M-RCS) | Notes |
|---|---|---|
| PFI-W4M-RCS/PFI-W4M-RCS-AZA-PLAN-AZA-ALZ-HCR-Skill-Chain-Build-Plan-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | Build plan |
| PFI-W4M-RCS/PFI-W4M-RCS-BRIEF-AZA-ALZ-HCR-Gap-Analysis-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | Gap analysis |
| PFI-W4M-RCS/PFI-W4M-RCS-RPT-AZA-ALZ-HCR-Build-Status-v2.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | Latest status (supersedes v1) |
| PFI-W4M-RCS/PFI-W4M-RCS-VE-BRIEF-AZA-ALZ-HCR-Idea-to-VP-Assessment-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | VE assessment |
| PFI-AIRL/PFI-AIRL-GRC-ARCH-Azure-Assessment-ALZ-Healthcheck-Architecture-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | Core architecture |
| PFI-AIRL/PFI-AIRL-GRC-ARCH-HCR-App-Skeleton-UI-UX-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | HCR UI/UX architecture |
| PFI-AIRL/PFI-AIRL-GRC-BRIEF-ALZ-Assessment-Skill-Chain-Readiness-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | Skill chain readiness |
| PFI-AIRL/PFI-AIRL-GRC-BRIEF-ALZ-Assessment-DMAIC-Backcasting-Methodology-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | DMAIC methodology |
| PFI-AIRL/PFI-AIRL-GRC-BRIEF-Health-Check-Report-Strategic-Deliverable-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | HCR deliverable brief |
| PFI-AIRL/PFI-AIRL-GRC-BRIEF-QVF-Cyber-Economics-Value-Quantification-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | QVF cyber economics |
| PFI-AIRL/PFI-AIRL-GRC-GUIDE-Azure-Assessment-Architect-Guide-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | Architect guide |
| PFI-AIRL/PFI-AIRL-GRC-GUIDE-Azure-Assessment-Developer-Guide-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | Developer guide |
| PFI-AIRL/PFI-AIRL-GRC-GUIDE-Azure-Assessment-User-Guide-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | User guide |
| PFI-AIRL/PFI-AIRL-GRC-TEST-Azure-Assessment-Test-Plan-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | Test plan |
| PFI-AIRL/PFI-AIRL-GRC-MANIFEST-Azure-Assessment-Document-Sync-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | Document sync manifest |
| PFC-ARCH/PFC-ARCH-SPEC-AZA-ALZ-HCR-Distribution-And-TDD-Simulated-Mode-v1.0.0.md | PBS/STRATEGY/PFI-W4M-RCS-AZA/ | Distribution spec |

#### 1.2 HCR Application Components

Copy HCR components into `src/components/hcr/` (new app directory):

| Source (Hub) | Target (W4M-RCS) |
|---|---|
| PBS/TOOLS/pfc-web-app/src/components/hcr/ (26 files) | src/components/hcr/ |
| PBS/TOOLS/pfc-web-app/src/hooks/hcr/ (5 files) | src/hooks/hcr/ |
| PBS/TOOLS/pfc-web-app/src/lib/hcr-*.ts (4 files) | src/lib/ |

#### 1.3 Test Data

Copy test scenarios into instance-data:

| Source (Hub) | Target (W4M-RCS) |
|---|---|
| AZALZ-ONT/instance-data/azalz-assessment-test-scenarios-v1.0.0.jsonld | instance-data/test-data/ |

#### 1.4 Graph Scope Update

Update `instance-data/config/graph-scope.json` to make AZA-related ontologies visible:

- Move from hidden → visible: `EFS-ONT`, `KANO-ONT`
- Add to visible: `AZALZ-ONT`, `MCSB-ONT`, `GRC-FW-ONT`, `QVF-ONT`

#### 1.5 Deliverables

- [ ] `PBS/STRATEGY/PFI-W4M-RCS-AZA/` populated with 16 strategy docs
- [ ] `src/` directory with HCR components, hooks, lib (35 files)
- [ ] `instance-data/test-data/` with 7 test scenarios
- [ ] `graph-scope.json` updated
- [ ] Commit and push

---

### Phase 2: BOARD — EFS Product Board & Issue Structure

**Goal:** AZA-ALZ-HCR product has a governed EFS backlog in GitHub Project #78 (PFI-RCS Product), with self-contained issues in pfi-w4m-dev.

#### 2.1 EFS Product Backlog — Issue Structure

Create issues in pfi-w4m-dev following the EFS pattern. These are **self-contained** — no hub issue cross-references.

**Epic:**

| Issue | Title | Labels |
|---|---|---|
| New | **Epic 3: AZA-ALZ-HCR — Azure Landing Zone Assessment Product** | `type:epic`, `domain:w4m` |

**Features (mapped from hub Epic 74 open + Epic 85):**

| Issue | Title | Phase | Labels |
|---|---|---|---|
| New | F3.1: AZA-ALZ Test Data Validation — 7 Scenarios End-to-End | Testing | `type:feature` |
| New | F3.2: HCR Dashboard UX Acceptance — Walkthrough & Feedback | Testing | `type:feature` |
| New | F3.3: HCR Report Generation — Compose, Analyse, Verify Pipeline | Testing | `type:feature` |
| New | F3.4: Assessment Scoring Validation — ALZ 8 Design Areas + WAF 5 Pillars | Testing | `type:feature` |
| New | F3.5: QVF Cyber Economics — ALE, FAIR, Breach Model Validation | Testing | `type:feature` |
| New | F3.6: DELTA Assessment Discovery Integration | Iterate | `type:feature` |
| New | F3.7: DMAIC Assessment Improvement Cycle | Iterate | `type:feature` |
| New | F3.8: Current-to-Future State Projection | Iterate | `type:feature` |
| New | F3.9: INS ALZ Snapshot Audit — Multi-Sector Pattern | Iterate | `type:feature` |
| New | F3.10: Azure MCP Server Connection & Authentication | Live | `type:feature` |
| New | F3.11: Extract Phase Adapter — MCP JSON to Pipeline Input | Live | `type:feature` |
| New | F3.12: Live Data Normaliser — Azure Data to Ontology Entities | Live | `type:feature` |
| New | F3.13: Credential & Identity Management — Service Principal | Live | `type:feature` |
| New | F3.14: E2E Integration Test — Live Azure to HCR Report | Live | `type:feature` |
| New | F3.15: First Live Customer Assessment | Live | `type:feature` |

**Standing Operational Issues:**

| Issue | Title | Labels |
|---|---|---|
| New | AZA-ALZ Bug Triage — Testing Phase | `type:bug` |
| New | AZA-ALZ Documentation Maintenance | `type:docs` |

#### 2.2 Project Board Setup

1. Add all new issues to **GitHub Project #78 (PFI-RCS Product)**
2. Add engineering issues to **GitHub Project #77 (PFI-RCS Engineering)**
3. Configure board views:
   - **EFS Backlog** — all features by phase (Testing → Iterate → Live)
   - **Sprint Board** — Kanban (To Do / In Progress / Review / Done)
   - **Roadmap** — Timeline view by phase

#### 2.3 Automation

Extend `.github/workflows/auto-add-to-projects.yml` to route AZA-ALZ issues to Project #78:
- Issues with `domain:aza` label → Project #78
- Issues with `type:epic` + `domain:aza` → Project #78 + #77

#### 2.4 Deliverables

- [ ] Epic 3 + 15 features + 2 standing issues created in pfi-w4m-dev
- [ ] All issues added to Project #78 (Product) and #77 (Engineering)
- [ ] Board views configured (EFS Backlog, Sprint, Roadmap)
- [ ] Automation workflow updated
- [ ] `domain:aza` label created

---

### Phase 3: TEST — Promote to pfi-w4m-test & Validate

**Goal:** AZA-ALZ-HCR passes structured acceptance testing against all 7 test scenarios in the test environment.

#### 3.1 Pre-Promotion Checklist

Before promoting dev → test:

| Check | Criteria | Status |
|---|---|---|
| All Phase 1 migration complete | 16 docs + 35 components + test data in repo | Pending |
| All Phase 2 issues created | Epic 3 + features in Project #78 | Pending |
| HCR components build | `npm run build` succeeds | Verify |
| HCR tests pass | `npx vitest run` — 490/490 | Verify |
| Graph-scope updated | AZA ontologies visible | Pending |
| Instance manifest updated | AZA files included in promotion list | Pending |

#### 3.2 Promotion

Update `promotion/instance-manifest.json` to include AZA assets:

```json
{
  "files": [
    "instance-data/config/pfi-config.json",
    "instance-data/config/graph-scope.json",
    "instance-data/ontologies/**",
    "instance-data/test-data/**",
    "instance-data/tokens/**",
    "src/components/hcr/**",
    "src/hooks/hcr/**",
    "src/lib/hcr-*.ts",
    "pfc-core/**",
    "promotion/**",
    "supabase/migrations/**",
    "tools/custom/**"
  ]
}
```

Run promotion: `gh workflow run promote.yml` (dev → test)

#### 3.3 Acceptance Test Suite

Test against all 7 canonical scenarios:

| Scenario | Profile | Expected Score | Test Focus |
|---|---|---|---|
| **TD-GOLD-001** | Mature Enterprise (Financial Services) | 92 posture | Baseline — all domains score high |
| **TD-DRIFT-001** | Config Drift (Healthcare) | 58 posture | Drift detection, remediation recommendations |
| **TD-STARTUP-001** | Greenfield (Tech Startup) | Low | Gap identification, roadmap generation |
| **TD-HYBRID-001** | Hybrid Cloud (Manufacturing) | Mixed | Cross-environment scoring |
| **TD-MULTI-001** | Multi-Subscription (Enterprise) | Variable | Multi-tenant assessment aggregation |
| **TD-REGULATED-001** | Regulated Sector (Insurance) | Compliance-focused | MCSB + NCSC-CAF alignment |
| **TD-LEGACY-001** | Legacy Migration (Public Sector) | Low base, trajectory | Current→future state projection |

**Per-scenario test pass criteria:**

| Test | Pass Criteria |
|---|---|
| Pipeline ingestion | Test data loads, parses, maps to ontology entities |
| Scoring accuracy | Design area scores match expected ranges (±5%) |
| Findings generation | Correct number of findings per domain, severity classification matches |
| HCR Dashboard render | All 6 zones render, PostureGauge shows correct score, DomainHeatmap populated |
| HCR Report compose | Report chapters generated, evidence linked, recommendations prioritised |
| QVF Cyber Economics | ALE calculated, breach model populated, GRC ROI computed |
| Export | PDF/Markdown export produces valid output |

#### 3.4 UX Walkthrough

Structured UX review of HCR dashboard and report against TD-GOLD-001:

| Step | Validate |
|---|---|
| 1. Dashboard load | PostureGauge, DomainHeatmap, RiskRadar render correctly |
| 2. Domain drill-down | Click design area → findings list → finding detail with evidence |
| 3. Report view | Switch to Report tab → chapters render → section navigation works |
| 4. Graph view | Switch to Graph tab → ontology relationships visible |
| 5. Export | Export report as PDF and Markdown → verify formatting |
| 6. Detail panel | Slide-out inspector shows finding detail, evidence, recommendations |
| 7. Filtering | Filter by severity, domain, framework → correct results |

#### 3.5 Bug Triage Process

| Severity | Response | Target |
|---|---|---|
| Critical (blocks assessment) | Fix immediately, re-test | Before next scenario |
| High (incorrect scoring/data) | Fix in current sprint | Before promotion |
| Medium (UX/cosmetic) | Log to AZA-ALZ Bug Triage issue | Next iteration |
| Low (enhancement) | Log as feature request in EFS backlog | Backlog |

#### 3.6 Deliverables

- [ ] Promotion dev → test executed successfully
- [ ] 7/7 test scenarios pass acceptance criteria
- [ ] UX walkthrough completed with feedback logged
- [ ] Bug triage: 0 critical, 0 high remaining
- [ ] F3.1-F3.5 (Testing features) closeable

---

### Phase 4: ITERATE — Live MCP, Customer Pilot, Ship

**Goal:** Wire Azure MCP for live data, validate against real tenant, deliver first customer assessment, promote to prod.

#### 4.1 Azure MCP Integration (Epic 85 scope)

| Step | Action | Dependency |
|---|---|---|
| 4.1.1 | Register `microsoft/azure-skills` MCP Server in local config | None |
| 4.1.2 | Implement Extract Phase Adapter — MCP JSON → pipeline input format | 4.1.1 |
| 4.1.3 | Implement Live Data Normaliser — raw Azure → ontology entities | 4.1.2 |
| 4.1.4 | Service Principal provisioning — Reader + Security Reader RBAC | Customer engagement |
| 4.1.5 | Rate limiting & retry for Azure API throttling | 4.1.2 |
| 4.1.6 | E2E integration test: live Azure → scored findings → HCR v2.0 → UI | 4.1.3 + 4.1.4 |
| 4.1.7 | Assessment accuracy validation vs TD-ALZ-001 manual baseline | 4.1.6 |

#### 4.2 Remaining Epic 74 Features (5 open — parallel)

| Feature | What | When |
|---|---|---|
| F3.6 (DELTA Discovery) | Plug DELTA assessment discovery into pipeline Phase 1 | During MCP build |
| F3.7 (DMAIC Improvement) | DMAIC improvement cycle for post-assessment iteration | After first live run |
| F3.8 (Current→Future) | State projection methodology — gap→roadmap | After first live run |
| F3.9 (INS Snapshot Audit) | Insurance sector architect pattern | Customer-driven |

#### 4.3 QVF Maturation (Epic 67 — parallel)

| Priority | Feature | Action |
|---|---|---|
| 1 | F67.2 Cost & Resource Modelling (Tier 2a) | Build when testing complete |
| 2 | F67.3 Benefits Realisation (Tier 2b) | Post-first-customer |
| 3 | F67.4 Risk-Adjusted Value (Tier 3) | Post-first-customer |
| 4 | F67.5 QVF Pipeline Orchestrator | After Tier 2/3 |

#### 4.4 First Customer Assessment

| Step | Action |
|---|---|
| 1 | Identify pilot customer (internal W4M or external) |
| 2 | Onboard: Service Principal, RBAC, tenant scoping |
| 3 | Extract: Live azure-skills MCP → pipeline Phase 2 |
| 4 | Assess: Ontology-driven scoring across all 4 pillars |
| 5 | Report: HCR v2.0 generation — dashboard + PDF |
| 6 | Present: Customer debrief with findings + roadmap |
| 7 | Validate: Pipeline Automation KPI 0% → >70% |

#### 4.5 Promote to Prod

Once first customer assessment validates:

1. Fix any live-data issues discovered
2. Update instance-manifest.json for prod promotion
3. Run promotion: dev → test → prod
4. Update PFI-RCS Product board — close Testing features, advance Live features
5. Close Epic 3 Testing features, track Live features

#### 4.6 Deliverables

- [ ] Azure MCP connected and authenticated
- [ ] E2E pipeline: live Azure → HCR v2.0 report validated
- [ ] First live customer assessment delivered
- [ ] Pipeline Automation KPI >70%
- [ ] F3.10-F3.15 (Live features) closeable
- [ ] Promoted to pfi-w4m-prod

---

## 4. Migration Governance

### What Stays in the Hub (Azlan-EA-AAA)

| Asset | Why |
|---|---|
| Ontologies (AZALZ-ONT, MCSB-ONT, GRC-FW-ONT, QVF-ONT, etc.) | Source-of-truth — cascaded to PFI via pfc-core |
| Skills Register (skills-register-index.json) | Source-of-truth — PFC-governed |
| Sealed skills (pfc-core/skills/) | Distributed via pfc-release.yml |
| Epic 74, 85, 67, 90 issues | Hub-level programme tracking |
| PE-ONT process definitions | PFC platform asset |

### What Moves to W4M-RCS (pfi-w4m-dev)

| Asset | Why |
|---|---|
| AZA strategy docs (copies) | Product-level governance — W4M-RCS is product home |
| HCR components + hooks + lib | Product application code |
| Test data scenarios | Product testing |
| EFS backlog (new issues) | Product board — self-contained |
| Graph-scope config (updated) | Instance-specific visibility |

### Cross-Reference Rule

- **Hub → PFI:** Allowed (hub Epic 74 may reference pfi-w4m-dev issues)
- **PFI → Hub:** NEVER (pfi-w4m-dev issues must be self-contained)
- **Doc sync:** Document Sync Manifest tracks which docs are copied; hub version is authoritative if conflict

---

## 5. Execution Sequence

```
WEEK 1 ─────────────────────────────────────────────────────
  Phase 1.1  Create PBS/STRATEGY/PFI-W4M-RCS-AZA/ directory
  Phase 1.1  Copy 16 strategy docs from hub
  Phase 1.2  Copy HCR components/hooks/lib into src/
  Phase 1.3  Copy test scenarios into instance-data/test-data/
  Phase 1.4  Update graph-scope.json
  Phase 1.5  Commit + push

WEEK 1-2 ───────────────────────────────────────────────────
  Phase 2.1  Create domain:aza label
  Phase 2.1  Create Epic 3 + 15 features + 2 standing issues
  Phase 2.2  Add all to Project #78 (Product) + #77 (Engineering)
  Phase 2.3  Configure board views (EFS Backlog, Sprint, Roadmap)
  Phase 2.4  Update auto-add-to-projects workflow

WEEK 2-3 ───────────────────────────────────────────────────
  Phase 3.1  Pre-promotion checklist
  Phase 3.2  Update instance-manifest.json + promote dev → test
  Phase 3.3  Run acceptance tests (7 scenarios)
  Phase 3.4  UX walkthrough (TD-GOLD-001)
  Phase 3.5  Bug triage + fix
  Phase 3.3  Re-test after fixes
  Phase 3.6  Close F3.1-F3.5

WEEK 3+ ────────────────────────────────────────────────────
  Phase 4.1  Azure MCP connection (F3.10-F3.13)
  Phase 4.2  Parallel: remaining Epic 74 features (F3.6-F3.9)
  Phase 4.3  Parallel: QVF maturation (F67.2-F67.5)
  Phase 4.4  First live customer assessment (F3.15)
  Phase 4.5  Promote to prod
```

---

## 6. Success Criteria

| Milestone | Criteria |
|---|---|
| **Migration Complete** | 16 docs + 35 components + test data in pfi-w4m-dev, committed |
| **Board Live** | Epic 3 + 15 features in Project #78, views configured |
| **Testing Passed** | 7/7 scenarios pass, UX walkthrough done, 0 critical/high bugs |
| **First Live Assessment** | Real Azure tenant → scored HCR v2.0 → customer delivered |
| **Product Shipped** | Promoted to pfi-w4m-prod, Pipeline Automation KPI >70% |

---

## 7. Risks

| Risk | Severity | Mitigation |
|---|---|---|
| HCR components have hub-specific imports | Medium | Audit imports during Phase 1.2, create adapter layer if needed |
| Test data format differs from live Azure data | Medium | Normaliser validated against test data first (Phase 3), then live (Phase 4) |
| No customer Azure tenant access for Phase 4 | High | Use internal W4M Azure tenant as pilot; parallel customer engagement |
| Graph-scope changes break visualiser | Low | Test in dev before promoting; graph-scope is instance-specific |
| EFS backlog scope creep | Medium | Phase 3 features are Testing-only; Phase 4 features gated on Phase 3 completion |

---

## 8. Document References

| Doc | Link |
|---|---|
| Master Status Review | [W4M](https://github.com/ajrmooreuk/pfi-w4m-dev/blob/main/PBS/DOCS/PFI-W4M-RCS-AZA-OVW-Master-Status-Review-v1.0.0.md) |
| Build Status v2.0.0 | [Hub](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFI-W4M-RCS/PFI-W4M-RCS-RPT-AZA-ALZ-HCR-Build-Status-v2.0.0.md) |
| Build Plan v1.0.0 | [Hub](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFI-W4M-RCS/PFI-W4M-RCS-AZA-PLAN-AZA-ALZ-HCR-Skill-Chain-Build-Plan-v1.0.0.md) |
| Gap Analysis | [Hub](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFI-W4M-RCS/PFI-W4M-RCS-BRIEF-AZA-ALZ-HCR-Gap-Analysis-v1.0.0.md) |
| VE Assessment | [Hub](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFI-W4M-RCS/PFI-W4M-RCS-VE-BRIEF-AZA-ALZ-HCR-Idea-to-VP-Assessment-v1.0.0.md) |
| ALZ Architecture | [Hub](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFI-AIRL/PFI-AIRL-GRC-ARCH-Azure-Assessment-ALZ-Healthcheck-Architecture-v1.0.0.md) |
| HCR UI/UX Architecture | [Hub](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFI-AIRL/PFI-AIRL-GRC-ARCH-HCR-App-Skeleton-UI-UX-v1.0.0.md) |
| Test Plan | [Hub](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFI-AIRL/PFI-AIRL-GRC-TEST-Azure-Assessment-Test-Plan-v1.0.0.md) |
| Distribution Spec | [Hub](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFC-ARCH/PFC-ARCH-SPEC-AZA-ALZ-HCR-Distribution-And-TDD-Simulated-Mode-v1.0.0.md) |
| QVF Cyber Economics | [Hub](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/STRATEGY/PFI-AIRL/PFI-AIRL-GRC-BRIEF-QVF-Cyber-Economics-Value-Quantification-v1.0.0.md) |
| Epic 74 (Hub) | [#1074](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1074) |
| Epic 85 (Hub) | [#1290](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1290) |
| Epic 67 QVF (Hub) | [#986](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/986) |
| Project #78 PFI-RCS Product | [Board](https://github.com/users/ajrmooreuk/projects/78) |
| Project #77 PFI-RCS Engineering | [Board](https://github.com/users/ajrmooreuk/projects/77) |

---

*PFI-W4M-RCS-AZA — Testing, Migration & Launch Plan v1.0.0 — For Review*
