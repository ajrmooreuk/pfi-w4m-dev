# ARCH-CICD — CI/CD & Platform Automation Architecture

**Owner:** Azlan-EA-AAA | Epic 61: PFC-ARCH-CICD [#947](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947)
**Last updated:** 2026-03-17

This directory contains the full architectural specification, operating guides, and release artefacts for the PFC-PFI CI/CD pipeline, GitHub PM automation, and platform delivery governance.

---

## Core Architecture Set (ARCH-CICD-001 → 005)

Read in order for a complete picture. Each document has a distinct scope.

| # | Document | Version | Status | Scope |
| --- | --- | --- | --- | --- |
| 001 | [01-hub-and-spoke-proposal.md](01-hub-and-spoke-proposal.md) | 1.0.0 | Decision record — frozen | Architecture decision: hub-and-spoke model, options evaluated, recommendation |
| 002 | [02-promotion-pipeline-detail.md](02-promotion-pipeline-detail.md) | 1.2.0 | Implemented | Promotion mechanics: dev→test→prod, convention manifest, sync-to-live |
| 003 | [03-glossary.md](03-glossary.md) | 1.3.0 | Active | All terms: platform, CI/CD, instance hierarchy, GH PM automation, backup, PAT types |
| 004 | [04-operating-guide.md](04-operating-guide.md) | 1.4.0 | Active | Day-to-day operations: releases, PAT management (PROMOTION + PROJECT), drift detection, troubleshooting |
| 005 | [05-github-pm-automation.md](05-github-pm-automation.md) | 1.1.0 | Active | GitHub Projects governance: instance hierarchy, two-project model, auto-add routing, stub deployment status |

---

## Backup & Resilience Documents

Architecture, operations, and test plan for the PFC backup and resilience system (Epic 61 F61.15–F61.28).

| Document | Version | Status | Scope |
| --- | --- | --- | --- |
| [PFC-CICD-ARCH-Backup-Resilience-Architecture-v1.0.0.md](PFC-CICD-ARCH-Backup-Resilience-Architecture-v1.0.0.md) | 1.0.0 | Active — implementation in progress | Resilience goals, backup tiers, architecture layers, component inventory, recovery scenarios, RAID |
| [PFC-CICD-OPS-Backup-Resilience-v1.0.0.md](PFC-CICD-OPS-Backup-Resilience-v1.0.0.md) | 1.0.0 | Active — implementation in progress | Routine monitoring, failure response, recovery procedures, housekeeping |
| [PFC-CICD-TEST-Backup-Resilience-v1.0.0.md](PFC-CICD-TEST-Backup-Resilience-v1.0.0.md) | 1.0.0 | Active — tests pending execution | Acceptance criteria per layer, test cases T-L1 through T-L5, recovery drill procedure |

---

## Reference & Audit Documents

Supporting artefacts from specific feature deliveries.

| Document | Feature | Purpose |
| --- | --- | --- |
| [e2e-cicd-diagram.md](e2e-cicd-diagram.md) | — | End-to-end Mermaid diagram of the full pipeline |
| [PFC-Release-Audit-Log.md](PFC-Release-Audit-Log.md) | pfc-release.yml | Audit log of PFC-core releases to PFI triads |
| [DEPLOY-REQ-F31.9-sealed-skills.md](DEPLOY-REQ-F31.9-sealed-skills.md) | F31.9 [#819](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/819) | Deployment & config requirements: sealed skill distribution |
| [TEST-PLAN-F31.9-sealed-skills.md](TEST-PLAN-F31.9-sealed-skills.md) | F31.9 [#819](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/819) | Test plan: sealed skill distribution |
| [RELEASE-BULLETIN-F31.9-sealed-skills.md](RELEASE-BULLETIN-F31.9-sealed-skills.md) | F31.9 [#819](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/819) | Release bulletin: sealed skill distribution |
| [RELEASE-BULLETIN-F61.5-PAT-Governance.md](RELEASE-BULLETIN-F61.5-PAT-Governance.md) | F61.5 [#952](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/952) | Release bulletin: PAT governance & drift detection |
| [RELEASE-BULLETIN-F61.29-PFI-Project-Board-Automation.md](RELEASE-BULLETIN-F61.29-PFI-Project-Board-Automation.md) | F61.29 [#1241](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1241) | Release bulletin: PFI project board automation, workflow stubs, registry hierarchy |

---

## Strategy Briefings (PBS/STRATEGY/)

Decision documents that govern this architecture set. Implementation detail is in ARCH-CICD-001–005 above.

| Document | Epic | Status |
| --- | --- | --- |
| [PFC-CICD-BRIEF-PFC-Core-Triad-Separation-v1.0.0.md](../../STRATEGY/PFC-CICD-BRIEF-PFC-Core-Triad-Separation-v1.0.0.md) | Epic 58 [#837](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/837) | For Decision |
| [PFC-CICD-BRIEF-PFC-PFI-Skill-Separation-Promotion-v1.0.0.md](../../STRATEGY/PFC-CICD-BRIEF-PFC-PFI-Skill-Separation-Promotion-v1.0.0.md) | Epic 31 [#394](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/394) | Active |
| [PFC-CICD-BRIEF-Workflow-Governance-Project-Automation-Cascade-v1.0.0.md](../../STRATEGY/PFC-CICD-BRIEF-Workflow-Governance-Project-Automation-Cascade-v1.0.0.md) | Epic 61 [#947](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947) · F61.29 [#1241](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1241) | For Decision |
| [PFC-CICD-BRIEF-PBS-Architecture-DevSecOps-v1.0.0.md](../../STRATEGY/PFC-CICD-BRIEF-PBS-Architecture-DevSecOps-v1.0.0.md) | Epic 61 [#947](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947) | Active |

---

## Governing Epics

| Epic | Issue | PBS Sub-ID | Scope | Docs in this set |
| --- | --- | --- | --- | --- |
| **Epic 61: PFC-ARCH-CICD** ← parent | [#947](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947) | `PBS-PFC-ARCHITECTURE.CICD-DevSecOps` | Cross-cutting CI/CD, DB, security, change control, GH PM automation | All |
| Epic 31: PFC-ARCH-HUB | [#394](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/394) | `.CICD-Pipeline` | Hub-and-spoke architecture, convention promotion, sync-to-live, sealed skills | 001, 002, 004 |
| Epic 58: PFC-ARCH-TRIAD | [#837](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/837) | `.PFC-Triad` | PFC own dev/test/prod triad, IP protection, pfc-release.yml migration | 001, 002, 004 |
| Epic 59: PFC-ARCH-DBA | [#840](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/840) | `.DB-Cascade` | Per-stage DB promotion, promote-db.yml, schema cascade | 002, 004 |
| Epic 63: PFC-ARCH-PBS | [#961](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/961) | `.PBS-Governance` | Doc naming CI, skill registration enforcement, CI SOPs across all repos | 004 |
| Epic 10A: PFC-ARCH-SEC | [#127](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/127) | `.Security-MVP` | RLS, tenant isolation, RBAC, audit trail — blocks on Epic 46a | 002 |
| Epic 53: PFC-ARCH-CLOUD | [#775](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/775) | `.Sovereignty` | Cloud vendor sovereignty, Azure/Supabase multi-platform delivery | 001 |

### Key Features (active / in-flight)

| Feature | Issue | Scope | Docs |
| --- | --- | --- | --- |
| F61.29: PFI Project Board Automation | [#1241](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1241) | **COMPLETE** — auto-add + stubs deployed to all 4 active PFI repos; registry hierarchy fields set; guard-core follow-up [#1264](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1264) | 005, RELEASE-BULLETIN |
| F61.24: URG-Driven Backup Scope Registry | [#1206](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1206) | Backup auto-enrolment from instance registry — no hardcoded lists | 005 |
| F61.15: GitHub Resilience & Cross-Account Backup | [#1155](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1155) | GitLab mirror, launchd, PFI backup accounts, client instance DR | 005 |
| F61.18: CI/CD Process Audit & Control | [#1178](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1178) | Cross-repo workflow governance, promotion cascade tracking | 004 |
| F61.5: PAT Governance & Drift Detection | [#952](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/952) | PROMOTION_PAT health, weekly drift detection across 16 repos | 004 |
| F31.9: Sealed Skill Distribution | [#819](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/819) | pfc-release.yml sealed skill bundles to PFI triads | 002, DEPLOY-REQ, TEST-PLAN, RELEASE-BULLETIN |
