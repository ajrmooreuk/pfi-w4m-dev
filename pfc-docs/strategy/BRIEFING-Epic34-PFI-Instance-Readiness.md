# Epic 34 Companion: PFI Instance Readiness Assessment

## Platform Foundation Instances -- Current State & Platform Gap Analysis

| Field | Value |
|---|---|
| **Parent Epic** | 34: PF-Core Graph-Based Agentic Platform Strategy |
| **Date** | 2026-02-18 |
| **Total PFI Instances** | 5 (BAIV, W4M-WWG, W4M-EOMS, AIRL-CAF-AZA, VHF) |
| **Total GitHub Repos** | 15 (3 per instance: dev/test/prod triads) |
| **Total Project Boards** | 15 (1 per repo) |

---

## 1. Instance Readiness Matrix

| Instance | VP+RRR Data | RBAC | Competitive ONT | Setup Guide | Build Summary | Supabase | Promotion Pipeline |
|---|---|---|---|---|---|---|---|
| **PFI-BAIV** | Seeded (7 files) | 11 roles defined | AIV-Competitive (6 files) | Complete | (in guide) | eccoai (dev only) | Not tested |
| **PFI-W4M-WWG** | **NOT SEEDED** | Not defined | Not created | Complete | Complete | Not created | Not tested |
| **PFI-W4M-EOMS** | **NOT SEEDED** | Not defined | Not created | Complete | Complete | Not created | Not tested |
| **PFI-AIRL-CAF-AZA** | Seeded (dev) | Not defined | Not created | Complete | Complete | Not created | Not tested |
| **PFI-VHF** | Not seeded | Not defined | Not created | Complete | (in guide) | Not created | Not tested |

### Readiness Scores

```
BAIV         ████████░░  80%  (VP+RRR, RBAC, Competitive, Security Arch -- missing: Supabase test/prod, promotion)
AIRL-CAF-AZA ██████░░░░  60%  (VP+RRR seeded in dev -- missing: RBAC, competitive, Supabase, promotion)
W4M-WWG      ████░░░░░░  40%  (Config + docs -- missing: VP+RRR, RBAC, competitive, Supabase, promotion)
W4M-EOMS     ████░░░░░░  40%  (Config + docs -- missing: VP+RRR, RBAC, competitive, Supabase, promotion)
VHF          ███░░░░░░░  30%  (Setup guide only -- PoC instance, minimal data)
```

---

## 2. Instance Detail: PFI-BAIV (AI Visibility) -- Lead Instance

**Role in Platform:** First to market. 100 paying clients is the critical OBJ-F1 target.

### Current Assets

**Instance Ontology Data** (`PBS/ONTOLOGIES/pfi-BAIV-AIV-ONT/`):
- `baiv-ai-visibility-vp-instance-v1.0.0.jsonld` -- VP instance (28 KB)
- `baiv-ai-visibility-vp-rrr-enhanced-v1.1.0.jsonld` -- VP+RRR aligned (38 KB)
- `RRR-DATA-BAIV-AIV-roles-v1.0.0.jsonld` -- 11 RBAC roles (24 KB)
- `EFS-DATA-BAIV-AIV-security-v1.0.0.jsonld` -- EFS security (23 KB)
- `baiv-architecture-security-roles-v1.0.0.jsonld` -- Arch security (6 KB)
- `baiv-csuite-rrr-instances-v1.0.0.jsonld` -- C-Suite roles (47 KB)
- `BAIV_VSOM_OAA_COMPLIANT (2).json` -- OAA VSOM (31 KB)

**Competitive Intelligence** (`AIV-Competitive-ONT/`):
- Full competitive analysis ontology with registry entry, test data, glossary, implementation guide, validation report

**Security Architecture:**
- Full RRR-aligned security requirements document (28 KB)
- RBAC implementation plan with 11 roles across 4 tiers
- VP-RRR alignment: Problem->Risk, Solution->Requirement, Benefit->Result

**Agent Architecture (from VSOM notes):**
- 16 agents across 4 clusters:
  - Discovery: P0 Master Reasoning, P1 Ontology Mapper, P2 Baseline Auditor, P3 Voice Capture
  - Analysis: P4-P7 (Citation, Competitive, Gap analysis)
  - Generation: P8-P11 (Content architecture, Authority, Technical optimisation)
  - Optimisation: P12-P16 (Performance, Iteration, Continuous improvement)
- Key metric: AI Visibility Score (Discovery 25%, Authority 20%, Trust 30%, Technical 15%, Impact 10%)

### BAIV Platform Gaps

| Gap | Impact | Priority |
|---|---|---|
| Supabase test/prod projects | Cannot promote beyond dev | High |
| PROMOTION_PAT secret | Cannot run promotion workflow | High |
| Agent Manager not built | No agent orchestration | Critical (S3) |
| JSONB graph storage not implemented | No runtime graph queries | Critical (S1) |
| Figma Make pipeline not established | No UI/UX delivery path | High (S5) |
| Three-tier cost engine not built | Agent costs unsustainable | High (S3) |

---

## 3. Instance Detail: PFI-AIRL-CAF-AZA (AI Readiness)

**Role in Platform:** GRC-heavy instance. UK NCSC Cyber Assessment Framework delivery.

### Current Assets
- VP instance data seeded in dev repo
- RRR instance data seeded in dev repo
- Canonical VP source in ontology library: `VP-ONT/instance-data/vp-airl-instance-v1.0.0.jsonld`
- Setup guide (18.3 KB) + Build summary (5.1 KB)
- Aligns strongly to NCSC-CAF-ONT and GRC-FW-ONT

### AIRL Platform Gaps

| Gap | Impact | Priority |
|---|---|---|
| RBAC roles not defined | No access control model | High |
| No competitive ontology | No competitive landscape analysis | Medium |
| No instance VSOM | Strategic framework not formalised | Medium |
| Supabase projects not created | Cannot go beyond dev | High |

---

## 4. Instance Detail: PFI-W4M (Value Engineering)

**Note:** W4M has **two sub-instances**: WWG (World Wide Growth) and EOMS (Enterprise Operations Management Suite).

### W4M-WWG Current Assets
- pfi-config.json across 3 environments
- Setup guide + Build summary
- VSOM visual guide (`w4m_vsom_visual_guide_v1.0.md`)
- 6 Claude Code skills per repo (create-epic, create-feature, create-story, review-hierarchy, setup-repo, setup-project-board)

### W4M-EOMS Current Assets
- pfi-config.json across 3 environments
- Setup guide + Build summary
- Same 6 Claude Code skills per repo

### W4M Platform Gaps

| Gap | Impact | Priority |
|---|---|---|
| VP+RRR instance data not seeded | No value proposition defined | High |
| RBAC roles not defined | No access control model | Medium |
| No competitive ontology | No competitive landscape | Medium |
| EA-ONT placeholder empty | No EA integration for W4M | Medium |
| Supabase projects not created | Cannot go beyond dev | High |

---

## 5. Instance Detail: PFI-VHF (Virtual Health Foods -- PoC)

**Role:** Proof-of-concept instance. Validates PF-Core patterns.
- Setup guide (18.9 KB) -- most complete docs
- No instance data, no RBAC, no competitive ontology
- Lowest priority for platform readiness

---

## 6. Cross-Instance Infrastructure Status

### Hub-and-Spoke CI/CD (Epic 31)

```
Azlan-EA-AAA (Hub)
    |-- ont-registry-index.json (source of truth)
    |-- ontology-library/* (canonical ontologies)
    |
    |-- pfi-baiv-aiv-{dev|test|prod}      (Spoke -- BAIV)
    |-- pfi-w4m-wwg-{dev|test|prod}       (Spoke -- W4M WWG)
    |-- pfi-w4m-eoms-{dev|test|prod}      (Spoke -- W4M EOMS)
    |-- pfi-airl-caf-aza-{dev|test|prod}  (Spoke -- AIRL)
    +-- pfi-vhf-nutrition-app-{dev|test|prod} (Spoke -- VHF PoC)
```

**Status:** Repos created, project boards created, configs seeded, workflows copied. **Not yet tested end-to-end.**

### Visualiser Integration

- `pfi-loader.js` (174 lines) -- async instance data loading from registry
- `pfi-brand-resolution.test.js` -- design system brand resolution per PFI
- Three-tier resolution: `designSystemConfig.brand` -> fallback -> `brands[]` array
- 938/939 tests passing

### Secrets Outstanding (All Instances)

| Secret | Status | Scope |
|---|---|---|
| PROMOTION_PAT | Not set | All 15 repos |
| SUPABASE_URL | Set for BAIV dev only | Needs all test/prod environments |
| SUPABASE_ANON_KEY | Set for BAIV dev only | Needs all test/prod environments |
| SUPABASE_SERVICE_KEY | Set for BAIV dev only | Needs all test/prod environments |

---

## 7. PFI VSOM Alignment Requirements

Each PFI needs its own VSOM that inherits from the PFC-Core VSOM (the parent document). The `vsem:alignsTo` relationship ensures bidirectional traceability.

### PFI-BAIV VSOM Scope
- **Strategies:** S2 (VE), S3 (Agents), S4 (Instance Custom), S5 (UI/UX), S6 (Integration)
- **Domain objectives:** AI mention rates, citation architecture, content authority, competitor visibility
- **Domain metrics:** AI Visibility Score, Authority Score, Trust Score, Mention Rate Improvement
- **Agent clusters:** 4 (Discovery, Analysis, Generation, Optimisation) with 16 agents

### PFI-W4M VSOM Scope
- **Strategies:** S1 (Graph), S2 (VE), S3 (Agents), S4 (Instance Custom)
- **Domain objectives:** Idea-to-MVP velocity, PMF validation, strategy execution, wellbeing outcomes
- **Domain metrics:** PMF Score, Time-to-MVP, Client Success Rate, Wellbeing Impact

### PFI-AIR VSOM Scope
- **Strategies:** S1 (Graph), S2 (VE), S3 (Agents), S6 (Integration)
- **Domain objectives:** AI maturity improvement, capability development, governance, CAF compliance
- **Domain metrics:** AI Readiness Score, Capability Maturity Level, Compliance Rate, CAF Delivery Time

---

## 8. Recommended Priority Order for Platform Readiness

```
Phase 1: Foundation (Months 1-3)
  1. PF-Core JSONB graph storage PoC (S1) .................. Blocks everything
  2. PFI-BAIV VP+RRR -> graph migration .................... Lead instance
  3. Agent Manager + Template v6.0.0 (S3) .................. Intelligence layer
  4. PFI-BAIV Supabase test/prod + promotion pipeline ...... DevOps path

Phase 2: Instance Expansion (Months 3-6)
  5. PFI-AIRL RBAC + competitive ontology .................. Second instance
  6. PFI-W4M-WWG VP+RRR seeding ............................ Third instance
  7. PFI-W4M-EOMS VP+RRR seeding ........................... Fourth instance
  8. Cross-instance intelligence patterns .................. Network effects

Phase 3: Platform Scale (Months 6-12)
  9. Figma Make -> Next.js pipeline (S5) ................... UI/UX
  10. Multi-tenant graph isolation .......................... Client deployment
  11. API Gateway + MCP (S6) ............................... Enterprise integration
  12. Fair Slice digital contracts .......................... Partner ecosystem
```

---

*Epic 34 Companion -- PFI Instance Readiness Assessment*
*Status: DRAFT -- Ready for analysis and feature planning*
