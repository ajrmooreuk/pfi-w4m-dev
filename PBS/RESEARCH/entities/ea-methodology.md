# EA Methodology & Strategy-as-Code

| Field | Value |
|-------|-------|
| **Type** | Entity |
| **Last Compiled** | 2026-04-06 |
| **Sources** | 8 docs |
| **Cross-Refs** | [PFC Wiki entities](https://github.com/ajrmooreuk/Azlan-EA-AAA/blob/main/PBS/RESEARCH/entities/) |

---

## Summary

W4M is the originator PFI of the PFC platform and defines its Enterprise Architecture methodology: a graph-based, ontology-driven approach where strategy, value engineering, execution, and governance are encoded as structured knowledge (Strategy-as-Code) rather than static documents. The methodology chains ontology-governed skill pipelines through a value cascade (VSOM to OKR to VP to EFS to Build), enabling agentic automation of traditionally manual consulting and delivery processes. W4M's niche differentiator is the combination of EA rigour, platform architecture ownership, and the Strategy-as-Code pattern that no other PFI instance replicates.

## Key Facts

- W4M is the PFC-owner root instance with full platform visibility, administrative access, and three sub-PFIs: W4M-RCS (Azure/mid-market), W4M-WWG (LSC logistics), and W4M-EOMS (sales-to-fulfilment) -- *Source: [CLAUDE.md](../../CLAUDE.md)*
- W4M's PFI class is `pfc-owner`, meaning it inherits all PFC ontologies and has cross-PFI visibility -- *Source: [CLAUDE.md](../../CLAUDE.md)*
- The strategic value chain follows a strict cascade: VSOM (Vision Strategy Objectives Measures) to OKR/KPI to VP (Value Proposition) to EFS (Epic-Feature-Story) to PFC-Core technical build -- *Source: [PFI-Instance-Integration-Architecture-v1.1.0.md](../../docs/PFI-Instance-Integration-Architecture-v1.1.0.md)*
- The value stack is governed by five ontology layers: VSOM v2.1.0, OKR v1.0.0, VP v1.2.0, EFS v3.0.0, and multiple PFC-Core ontologies -- *Source: [PFI-Instance-Integration-Architecture-v1.1.0.md](../../docs/PFI-Instance-Integration-Architecture-v1.1.0.md)*
- VP (Value Proposition) aligns to RRR (Roles, RACI, RBAC) via a standing convention: vp:Problem maps to rrr:Risk, vp:Solution maps to rrr:Requirement, vp:Benefit maps to rrr:Result (join pattern JP-VP-RRR-001) -- *Source: [PFI-Instance-Integration-Architecture-v1.1.0.md](../../docs/PFI-Instance-Integration-Architecture-v1.1.0.md)*
- Graphs enhance LLM performance through five mechanisms: structured context, precision retrieval (graph traversal vs vector search), bounded reasoning, multi-hop reasoning, and persistent evolving memory (episodic, semantic, procedural) -- *Source: [PFC-DSY-REF-Why-Graphs-Enhance-LLM-Performance-v1.0.0.md](../../docs/PFC-DSY-REF-Why-Graphs-Enhance-LLM-Performance-v1.0.0.md)*
- The URG (Unified Registry Graph) is the single gate for all platform resources, enforcing a 5-level cascade: PFC Platform to PFI Instance to Product/Service to Role (RBAC) to User, with definitive RLS at each level -- *Source: [PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md](../STRATEGY/PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md)*
- Ten Mandatory Security Rules (MSR-01 to MSR-10) are baked into the platform at every cascade level, including zero-default access, child-never-exceeds-parent scope, cross-PFI data invisibility, and TDD-mandatory role deployment -- *Source: [PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md](../STRATEGY/PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md)*
- PFI graph creation follows an 11-step pipeline: Load Registry, Create PFI Instance, Load Instance Data, Populate Scope Rules, Resolve Product Context, Evaluate Scope Rules, Compose Instance Graph, Resolve Entity Bindings, Freeze as Canonical Snapshot, Generate PFI Graph, and optionally Filter to Persona Workflow -- *Source: [OPERATING-GUIDE-PFI-Graph-Creation.md](../../docs/OPERATING-GUIDE-PFI-Graph-Creation.md)*
- EMC-ONT scope rules are declarative (include-data / exclude-data) with priority-based conflict resolution, allowing each PFI product to compose a tailored graph from the shared ontology library -- *Source: [OPERATING-GUIDE-PFI-Graph-Creation.md](../../docs/OPERATING-GUIDE-PFI-Graph-Creation.md)*
- The EFS GitHub integration pipeline spans three domains: EFS AC Updates (60% maturity), TDD Pre-Close-Out dual-layer (35% maturity), and Close-Out Pipeline 6-stage skill (55% maturity), governed by Epics 61, 76, and 49 respectively -- *Source: [PFC-ARCH-BRIEF-EFS-TDD-Acceptance-Close-Out-VE-Evaluation-v1.0.0.md](../STRATEGY/PFC-ARCH-BRIEF-EFS-TDD-Acceptance-Close-Out-VE-Evaluation-v1.0.0.md)*

## Relationships

- **PFC Platform (Azlan-EA-AAA):** W4M is the originator and PFC-owner instance; all ontologies, skills, and governance originate in PFC and cascade to W4M and all other PFI instances -- *Source: [CLAUDE.md](../../CLAUDE.md)*
- **Sub-PFIs (W4M-RCS, W4M-WWG, W4M-EOMS):** W4M is the parent; sub-PFIs inherit W4M scope but specialise by domain (Azure, logistics, sales-to-fulfilment) -- *Source: [CLAUDE.md](../../CLAUDE.md)*
- **AIRL PFI:** Separate PFI for AI readiness and governance; receives FairSlice-licensed skills from W4M-RCS (e.g. AZA products) -- *Source: [CLAUDE.md](../../CLAUDE.md)*
- **BAIV PFI:** Separate PFI for marketing strategy and competitive intelligence; consumes the same VE pipeline and graph composition architecture -- *Source: [OPERATING-GUIDE-PFI-Graph-Creation.md](../../docs/OPERATING-GUIDE-PFI-Graph-Creation.md)*
- **URG (Unified Registry Graph):** Governs all access control; W4M as PFC-owner has administrative access to define entitlement profiles for all PFI instances -- *Source: [PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md](../STRATEGY/PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md)*
- **52 Ontologies / 6 Series:** The ontology library is the knowledge substrate; W4M inherits all series (VE, PE, GRC, Foundation, Orchestration, EA) -- *Source: [CLAUDE.md](../../CLAUDE.md)*

## Architecture

### Strategy-as-Code Pattern

The core architectural principle is that strategic artefacts (vision, objectives, value propositions, execution plans, governance rules) are encoded as structured, machine-readable ontology instances rather than prose documents. This enables:

1. **Graph-governed skill chains** -- skills consume and produce ontology entities, creating traceable pipelines from strategy to delivery -- *Source: [PFI-Instance-Integration-Architecture-v1.1.0.md](../../docs/PFI-Instance-Integration-Architecture-v1.1.0.md)*
2. **Declarative scope composition** -- EMC-ONT scope rules compose per-PFI, per-product graphs from the shared ontology library without code changes -- *Source: [OPERATING-GUIDE-PFI-Graph-Creation.md](../../docs/OPERATING-GUIDE-PFI-Graph-Creation.md)*
3. **Bounded LLM reasoning** -- graph constraints define valid transitions and states, preventing hallucination by limiting the solution space to what the ontology permits -- *Source: [PFC-DSY-REF-Why-Graphs-Enhance-LLM-Performance-v1.0.0.md](../../docs/PFC-DSY-REF-Why-Graphs-Enhance-LLM-Performance-v1.0.0.md)*
4. **Cascading security** -- the URG 5-level entitlement chain ensures access narrows from PFC to PFI to Product to Role to User, never expanding -- *Source: [PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md](../STRATEGY/PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md)*

### Platform Architecture Layers

| Layer | Purpose | Key Component |
|-------|---------|---------------|
| Ontology Library | 52 ontologies across 6 series -- structured knowledge | ont-registry-index.json |
| EMC Composition | Declarative graph composition with scope rules | emc-composer.js |
| PFI Instance | Per-product graph with frozen canonical snapshots | PFI instance config + instance data |
| Skills Pipeline | Agentic skills consume/produce ontology entities | skills-register-index.json |
| URG Access Control | 5-level cascading entitlement with RLS enforcement | pfc_resources + pfi_entitlements |
| EFS Execution | Epic-Feature-Story hierarchy with TDD close-out | GitHub Issues + Projects + CI/CD |

*Sources: [CLAUDE.md](../../CLAUDE.md), [PFI-Instance-Integration-Architecture-v1.1.0.md](../../docs/PFI-Instance-Integration-Architecture-v1.1.0.md), [OPERATING-GUIDE-PFI-Graph-Creation.md](../../docs/OPERATING-GUIDE-PFI-Graph-Creation.md)*

### Niche Differentiator

W4M's niche is the intersection of EA/Strategy-as-Code, platform architecture, and ontology-driven modelling. No other PFI instance originates the platform itself. W4M defines the graph architecture, the cascade model, the skill registration pipeline, and the URG security model. Sub-PFIs (W4M-RCS, W4M-WWG, W4M-EOMS) specialise within domains but inherit the full platform capability. This makes W4M the only instance where the methodology IS the product. -- *Source: [CLAUDE.md](../../CLAUDE.md)*

## Current Status

- W4M is in active development as the PFC-owner root instance -- *Source: [CLAUDE.md](../../CLAUDE.md)*
- The VE pipeline (VSOM to OKR to VP to Kano to PMF) is fully operational with adopted skills at each stage -- *Source: [PFI-Instance-Integration-Architecture-v1.1.0.md](../../docs/PFI-Instance-Integration-Architecture-v1.1.0.md)*
- The PFI graph creation pipeline is documented and operational (EMC v5.1.0+) -- *Source: [OPERATING-GUIDE-PFI-Graph-Creation.md](../../docs/OPERATING-GUIDE-PFI-Graph-Creation.md)*
- The URG Access Scope Model is at "For Decision" status, pending approval as the foundational access architecture -- *Source: [PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md](../STRATEGY/PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md)*
- The EFS-TDD close-out integration is partially implemented (35-60% across three domains) -- *Source: [PFC-ARCH-BRIEF-EFS-TDD-Acceptance-Close-Out-VE-Evaluation-v1.0.0.md](../STRATEGY/PFC-ARCH-BRIEF-EFS-TDD-Acceptance-Close-Out-VE-Evaluation-v1.0.0.md)*
- Database architecture (Epic 59) is strategy-briefed but Supabase is not yet live; JSON/JSONLD + GitHub PM workflows are the current data layer -- *Source: [PFC-Security-Database-Integration-Review-v1.0.0.md](../STRATEGY/PFC-Platform-Graph/PFC-Security-Database-Integration-Review-v1.0.0.md)*

## Open Questions

- **URG approval status:** The URG Access Scope Model is "For Decision" -- approval would gate all subsequent database, security, and deployment work -- *Source: [PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md](../STRATEGY/PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md)*
- **Database deployment timing:** Epic 59 (Platform Database Architecture) depends on URG Phase 1 being proven first; Supabase is not yet deployed -- *Source: [PFC-Security-Database-Integration-Review-v1.0.0.md](../STRATEGY/PFC-Platform-Graph/PFC-Security-Database-Integration-Review-v1.0.0.md)*
- **TDD dual-layer maturity:** TDD2 regression tracker tests are Red stubs with no live regression risk tracking yet (35% maturity) -- *Source: [PFC-ARCH-BRIEF-EFS-TDD-Acceptance-Close-Out-VE-Evaluation-v1.0.0.md](../STRATEGY/PFC-ARCH-BRIEF-EFS-TDD-Acceptance-Close-Out-VE-Evaluation-v1.0.0.md)*
- **W4M instance data:** W4M-specific DS-ONT brand tokens exist but broader instance data population is not documented as complete -- *Source: [CLAUDE.md](../../CLAUDE.md)*

## Changelog

| Date | Action | Sources Ingested |
|------|--------|-----------------|
| 2026-04-06 | Initial compilation | CLAUDE.md, PFI-Instance-Integration-Architecture-v1.1.0.md, PFC-DSY-REF-Why-Graphs-Enhance-LLM-Performance-v1.0.0.md, PFC-ARCH-BRIEF-URG-Access-Scope-Model-v1.0.0.md, PFC-ARCH-EVAL-URG-Schema-Architecture-Review-v1.0.0.md, OPERATING-GUIDE-PFI-Graph-Creation.md, PFC-ARCH-BRIEF-EFS-TDD-Acceptance-Close-Out-VE-Evaluation-v1.0.0.md, PFC-Security-Database-Integration-Review-v1.0.0.md |
