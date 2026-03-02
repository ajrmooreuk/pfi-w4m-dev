# Strategy: Cloud Vendor, Data Sovereignty & Multi-Platform Options

## Azure vs Multi-Vendor vs Open Source — AWS, Google, Microsoft & Beyond

| Field | Value |
|---|---|
| **Version** | 1.0.0 |
| **Date** | 2026-02-26 |
| **Status** | PROPOSAL |
| **Classification** | CONFIDENTIAL -- Strategic Planning Asset |
| **Aligned to** | Epic 34 (S1 Graph-First, S4 Instance Customisation, S6 Integration & EA) |
| **Ontology Compliance** | VSOM-ONT v3.0.0, GRC-FW-ONT v3.0.0, EA-MSFT-ONT, EFS-ONT v2.0.0, PPM-ONT v4.0.0 |
| **ADR References** | ADR-001 (Supabase-First), ARCH-CICD-001 (Hub-and-Spoke) |

---

## 1. Executive Summary

PF-Core's current architecture is **100% GitHub-native** for governance (Actions, Issues, Projects, Releases) and **100% Claude-native** for AI reasoning (Claude Code CLI, skills, DELTA pipeline). This serves the solo-operator and startup PFI model well but creates three strategic risks as the platform scales to enterprise PFI instances:

1. **Vendor lock-in** -- enterprise clients may mandate Azure DevOps, AWS CodePipeline, or GitLab
2. **Data sovereignty gaps** -- all current providers (GitHub/Microsoft, Anthropic, Supabase) are US-headquartered and CLOUD Act-exposed
3. **AI model dependency** -- 7 DELTA skills + VE chain are Claude-specific; no fallback if Anthropic is unavailable or contractually excluded

This strategy document evaluates **five platform configurations** across four cloud vendors (Microsoft Azure, AWS, Google Cloud, self-hosted open source) and recommends a **tiered approach** where PFI instances select their platform stack based on client regulatory profile, existing enterprise agreements, and data sovereignty requirements.

**Key finding:** The ontology layer (VSOM, EFS, PPM, Unified Registry) is already platform-agnostic by design. The migration cost is in the **workflow plumbing and AI skill porting**, not the intellectual property.

---

## 2. VSOM Framework -- Strategic Context

### 2.1 Vision (from Epic 34)

> "Become the definitive graph-native agentic platform that transforms how mid-market enterprises architect, deploy, and continuously optimise AI-driven strategy, processes, and competitive intelligence."

**Multi-platform extension:** The platform must be deployable wherever the client's enterprise mandates, without sacrificing ontology governance or analytical quality.

### 2.2 Strategies Impacted

| Epic 34 Strategy | Multi-Platform Impact |
|---|---|
| **S1: Graph-First Architecture** | JSONB pattern works on any PostgreSQL (Supabase, Azure, AWS RDS, self-hosted) |
| **S4: Instance & Client Customisation** | PFI instances must choose their DevOps platform, cloud provider, and AI vendor independently |
| **S5: UI/UX Design-to-Production** | Figma + Pencil pipeline is cloud-agnostic; deployment target varies (Vercel, Azure Static Web Apps, AWS Amplify) |
| **S6: Integration & Enterprise Architecture** | API-first, MCP-native design must work across Azure, AWS, GCP, and on-premise |

### 2.3 Strategic Objectives

| ID | Objective | BSC Perspective | Target |
|---|---|---|---|
| OBJ-MP-1 | Enable PFI deployment on any major cloud platform | Customer | 3 platforms supported by Q3 2026 |
| OBJ-MP-2 | Achieve UK/EU data sovereignty option for regulated PFIs | Stakeholder | Sovereign stack available by Q4 2026 |
| OBJ-MP-3 | Maintain Claude as primary AI engine while enabling fallback | Internal | OpenAI + OSS adapters by Q4 2026 |
| OBJ-MP-4 | Keep platform migration cost below 2 weeks per PFI instance | Financial | Measured per bootstrap-triad |
| OBJ-MP-5 | Ensure ontology governance remains platform-agnostic | Learning | Zero ontology changes for platform switch |

---

## 3. Current State: Pure GitHub + Claude Architecture

### 3.1 Platform Dependency Map

```
GOVERNANCE LAYER (GitHub-locked)
  GitHub Issues           Epic/Feature/Story hierarchy (EFS-ONT mapping)
  GitHub Projects         Kanban boards (PPM-ONT mapping)
  GitHub Actions          7 enforcement workflows + pfc-release.yml
  GitHub Releases         Hub-and-spoke distribution to PFI triads
  GitHub Pages            Ontology library hosting
  gh CLI                  All automation scripts (bootstrap-triad.sh, setup-*.sh)

AI REASONING LAYER (Claude-locked)
  Claude Code CLI         Skill orchestration engine
  Claude Opus 4.6         DELTA pipeline (5-phase, 7 skills)
  Claude Sonnet 4.6       EFS hierarchy, template generation
  Claude tool_use         All 42+ ontology parsing functions
  Anthropic API           Direct API, no Azure/AWS/GCP dependency

DATA LAYER (Supabase-locked)
  Supabase PostgreSQL     JSONB graph storage (5 MVP tables)
  Supabase Auth           JWT + RLS, 4 roles
  Supabase Edge Functions Token-efficient API layer
  Supabase Realtime       Future: live collaboration

PLATFORM-AGNOSTIC LAYER (no lock-in)
  Ontology Library        42 JSON-LD ontologies (pure files)
  Unified Registry        ont-registry-index.json (pure JSON)
  Visualiser              Zero-build browser app (vanilla JS)
  Claude Code Skills      Markdown + tool schemas (portable prompts)
  .pen Design Files       Git-native design prototypes
```

### 3.2 Lock-In Risk Assessment (GRC-FW-ONT Aligned)

| Component | Lock-In Level | Migration Effort | Business Impact if Vendor Unavailable |
|---|---|---|---|
| GitHub Issues/Projects | MEDIUM | 2-3 weeks (to Azure Boards or GitLab) | Workflow disruption, no data loss |
| GitHub Actions | MEDIUM | 1-2 weeks (YAML largely portable) | CI/CD pipeline halted |
| GitHub Releases (hub-spoke) | HIGH | 3-4 weeks (custom distribution) | PFI sync broken |
| Claude Code CLI | HIGH | 6-12 weeks (rewrite orchestrator) | All AI skills offline |
| Claude tool_use schemas | HIGH | 3-4 weeks per skill (prompt rewrite) | DELTA pipeline offline |
| Supabase | MEDIUM | 2-3 weeks (standard PostgreSQL) | Auth + RLS needs reimplementation |
| Ontology Library (JSON-LD) | **NONE** | 0 (pure files) | No impact |
| Unified Registry (JSON) | **NONE** | 0 (pure JSON) | No impact |
| Visualiser (vanilla JS) | **NONE** | 0 (any static host) | No impact |

---

## 4. Platform Options: Five Configurations

### 4.1 Option A: All-GitHub + Claude (Current State)

**Profile:** Solo operator, startups, agencies, open-source-friendly PFIs

```
Git/CI:     GitHub (Actions, Issues, Projects, Releases, Pages)
AI:         Claude (Anthropic Direct API)
Database:   Supabase (PostgreSQL + Auth + Edge Functions)
Hosting:    Vercel / Netlify / GitHub Pages
Auth:       Supabase Auth (email + OAuth)
```

**Strengths:**
- Proven, operational, 1,527 tests passing
- Lowest complexity (single vendor per layer)
- Best developer experience (gh CLI, Claude Code, Supabase dashboard)
- GitHub Pages free for ontology hosting
- 50,000 free Actions minutes on Enterprise tier

**Weaknesses:**
- All three vendors (Microsoft/GitHub, Anthropic, Supabase Inc.) are US-headquartered
- Full CLOUD Act exposure on every layer
- No native work item hierarchy (convention-enforced via workflows)
- No native test management
- Enterprise clients may refuse GitHub for DevOps

**Cost (3 PFI instances, 14 devs, 10 stakeholders):**

| Line Item | USD/yr | GBP/yr |
|---|---|---|
| GitHub Team (14 x $4/mo) | $672 | £531 |
| GitHub Actions (within free tier) | $0 | £0 |
| Supabase Pro (3 instances) | $900 | £711 |
| Claude API (moderate usage) | $2,400 | £1,896 |
| **Total** | **$3,972** | **£3,138** |

---

### 4.2 Option B: All-Azure DevOps + Azure OpenAI

**Profile:** Enterprise clients with Microsoft EA, M365 deployed, Azure-first mandate

```
Git/CI:     Azure DevOps (Repos, Pipelines, Boards, Artifacts, Test Plans)
AI:         Azure OpenAI (GPT-4o, GPT-4.1) + Claude via Azure Foundry
Database:   Azure PostgreSQL Flexible Server (JSONB) + Azure AD Auth
Hosting:    Azure Static Web Apps / Azure App Service
Auth:       Microsoft Entra ID (Azure AD) + RBAC
```

**Strengths:**
- Native Epic/Feature/Story hierarchy in Azure Boards (eliminates 3 enforcement workflows)
- Native environments with approval gates (replaces promote.yml simulation)
- UK South region available (data residency, requires allow-listing)
- MACC-eligible (Claude + OpenAI costs count against Azure EA committed spend)
- Unlimited free Stakeholder access (critical for PFI client visibility)
- Native Test Plans ($52/user/mo but no third-party needed)
- Microsoft compliance certifications (ISO 27001, SOC 2, FedRAMP, UK G-Cloud)
- Entra ID replaces Supabase Auth with enterprise SSO (SAML, OIDC)

**Weaknesses:**
- CLOUD Act exposure (Microsoft = US company; UK South does not guarantee sovereignty)
- Azure OpenAI UK South requires manual allow-listing (submit form + support ticket)
- Claude via Azure Foundry limited to Sweden Central (not in EU Data Boundary)
- Azure Foundry Agent Service unstable for multi-agent chains (your DELTA pipeline)
- Higher complexity than GitHub (Azure DevOps has steeper learning curve)
- No equivalent to GitHub Pages free hosting (Azure Static Web Apps Standard = $9/mo)
- Claude Code skills need rewriting for Azure OpenAI function calling schema (if using OpenAI models)

**Cost (3 PFI instances, 14 devs, 10 stakeholders):**

| Line Item | USD/yr | GBP/yr |
|---|---|---|
| Azure DevOps (5 free + 9 x $6/mo) | $648 | £512 |
| Azure Stakeholders (10 x free) | $0 | £0 |
| Azure Pipelines (1 free + 1 extra) | $480 | £379 |
| Azure Static Web Apps (Standard) | $108 | £85 |
| Azure PostgreSQL Flex (3 x B1ms) | $468 | £370 |
| Claude via Azure Foundry | $1,200 | £948 |
| Azure OpenAI (GPT-4o for M365 glue) | $1,200 | £948 |
| **Total** | **$4,104** | **£3,242** |

---

### 4.3 Option C: AWS-Native + Claude Bedrock

**Profile:** AWS-first enterprises, serverless-native teams, multi-region requirements

```
Git/CI:     AWS CodeCommit (deprecated) → GitHub / GitLab + AWS CodePipeline
AI:         Claude via AWS Bedrock (Frankfurt eu-central-1 for EU)
Database:   AWS RDS PostgreSQL (JSONB) or AWS Aurora Serverless v2
Hosting:    AWS Amplify / S3 + CloudFront
Auth:       AWS Cognito + IAM
```

**Strengths:**
- Claude on Bedrock is the most mature cloud integration (launched before Foundry)
- Frankfurt (eu-central-1) offers EU cross-region inference for Claude
- Aurora Serverless v2 auto-scales and auto-pauses (cost-efficient for variable PFI loads)
- AWS Cognito provides enterprise-grade auth (SAML, OIDC, social)
- Broadest global region coverage (25+ regions)
- Reserved instances and savings plans for predictable workloads

**Weaknesses:**
- AWS has no native DevOps work item tracker (need GitHub Issues, Jira, or Linear alongside)
- CodeCommit deprecated January 2025 -- no AWS-native Git hosting going forward
- CodePipeline is less developer-friendly than GitHub Actions or Azure Pipelines
- CLOUD Act exposure (Amazon = US company)
- Claude on Bedrock Frankfurt uses cross-region inference (data may route outside EU for processing)
- Higher operational complexity than Supabase (RDS requires more config)
- No Stakeholder free tier equivalent

**Cost (3 PFI instances, 14 devs, 10 stakeholders):**

| Line Item | USD/yr | GBP/yr |
|---|---|---|
| GitHub Team (14 x $4/mo -- still needed for Git) | $672 | £531 |
| AWS CodePipeline (3 pipelines x $1/mo) | $36 | £28 |
| AWS RDS PostgreSQL (3 x db.t4g.micro) | $540 | £427 |
| AWS Amplify Hosting (3 apps) | $180 | £142 |
| Claude via Bedrock | $2,400 | £1,896 |
| AWS Cognito (14 MAUs in free tier) | $0 | £0 |
| **Total** | **$3,828** | **£3,024** |

**Note:** AWS is cheapest on raw infrastructure but requires GitHub/GitLab for Git and a third-party tool for project management (Jira, Linear, Azure Boards), adding hidden cost and integration complexity.

---

### 4.4 Option D: Google Cloud + Claude Vertex (Best EU Sovereignty)

**Profile:** EU/UK regulated enterprises needing genuine in-region AI processing

```
Git/CI:     GitHub (unchanged) or GitLab (self-managed)
AI:         Claude via Google Vertex AI Frankfurt (europe-west3) -- genuine in-region
Database:   Cloud SQL PostgreSQL (JSONB) or AlloyDB
Hosting:    Firebase Hosting / Cloud Run
Auth:       Firebase Auth or Google Cloud Identity
```

**Strengths:**
- **Best Claude data residency:** Google Vertex AI Frankfurt is the only option offering genuine in-region processing for Claude in the EU (not cross-region inference)
- Cloud SQL PostgreSQL is fully managed with automatic backups, HA
- AlloyDB offers 4x throughput over standard PostgreSQL (future graph workloads)
- Firebase Hosting is free for basic sites (equivalent to GitHub Pages)
- Cloud Run provides serverless container hosting with scale-to-zero
- Google's EU data processing commitments are stronger than AWS cross-region inference

**Weaknesses:**
- CLOUD Act exposure (Google = US company, though mitigated by in-region processing + ZDR)
- Google Cloud has smallest enterprise DevOps tooling (no native Boards equivalent)
- Cloud SQL is more expensive than Supabase Pro for equivalent features
- Firebase Auth is less enterprise-grade than Entra ID or Cognito
- Smallest market share in UK enterprise (behind Azure and AWS)
- No native CI/CD work item integration (need GitHub/GitLab)

**Cost (3 PFI instances, 14 devs, 10 stakeholders):**

| Line Item | USD/yr | GBP/yr |
|---|---|---|
| GitHub Team (14 x $4/mo) | $672 | £531 |
| Cloud SQL PostgreSQL (3 x db-f1-micro) | $360 | £284 |
| Claude via Vertex AI Frankfurt | $2,400 | £1,896 |
| Firebase Hosting (free tier) | $0 | £0 |
| Cloud Run (minimal) | $120 | £95 |
| **Total** | **$3,552** | **£2,806** |

**Note:** Google is the cheapest option and offers the best EU data residency for Claude. The trade-off is weakest enterprise DevOps tooling -- you still need GitHub or GitLab for project management.

---

### 4.5 Option E: Fully Sovereign Open Source (Self-Hosted)

**Profile:** UK public sector (NHS, MOD, HMRC), critical national infrastructure, SCIF environments, clients requiring zero US vendor involvement

```
Git/CI:     Gitea (self-hosted, GH Actions-compatible) or GitLab CE
AI:         Llama 4 / Mistral Large / DeepSeek V3.2 via vLLM on UK GPU
Database:   PostgreSQL (self-managed on UK infrastructure)
Hosting:    Nginx / Caddy on UK bare metal or Hetzner Cloud (German)
Auth:       Keycloak (self-hosted OIDC/SAML)
```

**Strengths:**
- **Complete CLOUD Act immunity** (no US company in the stack)
- **Full data sovereignty** (UK data centre, UK-incorporated operators)
- Zero vendor lock-in (all components are open source or self-managed)
- Gitea supports GitHub Actions-compatible workflows (YAML syntax portable)
- Keycloak provides enterprise SSO (SAML 2.0, OIDC, LDAP, Kerberos)
- PostgreSQL JSONB pattern works identically (zero ontology changes)
- Model weights are local -- no API calls, no token metering, deterministic costs

**Weaknesses:**
- **9x cost multiplier** over SaaS options ($36K-66K/yr vs $3.5-4K/yr)
- AI model quality: open-source LLMs achieve 70-90% of Claude Opus quality
- DELTA pipeline's complex multi-agent reasoning chains will degrade
- Significant DevOps overhead (GPU management, model serving, infra monitoring)
- No Claude Code CLI equivalent (need custom LangChain/LangGraph orchestrator)
- Hiring: need GPU/ML infrastructure expertise (scarce, expensive)
- Update lag: open-source models release weeks-months behind frontier

**Cost (3 PFI instances, self-hosted on Hetzner):**

| Line Item | USD/yr | GBP/yr |
|---|---|---|
| Hetzner dedicated GPU (2x A100, DE) | $9,600 | £7,584 |
| Hetzner cloud servers (3 x CX41) | $1,080 | £853 |
| GitLab CE (self-hosted, free) | $0 | £0 |
| PostgreSQL (self-managed) | $0 (on same servers) | £0 |
| Keycloak (self-hosted, free) | $0 | £0 |
| DevOps engineering (10-20 hrs/mo x £75) | $22,800 | £18,000 |
| **Total** | **~$33,480** | **~£26,437** |

**Alternative: Azure sovereign (UK South GPU VMs):**

| Line Item | USD/yr | GBP/yr |
|---|---|---|
| Azure NC24ads A100 v4 (UK South) | $32,400 | £25,596 |
| Azure PostgreSQL Flex (UK South) | $468 | £370 |
| Azure Static Web Apps | $108 | £85 |
| Gitea on Azure VM | $360 | £284 |
| DevOps engineering | $22,800 | £18,000 |
| **Total** | **~$56,136** | **~£44,347** |

---

## 5. Cross-Platform Cost Comparison Matrix

### 5.1 Near-Term (3 PFI Instances, 14 Devs, 10 Stakeholders)

| Component | A: GitHub+Claude | B: Azure DevOps | C: AWS+Bedrock | D: GCP+Vertex | E: Sovereign OSS |
|---|---|---|---|---|---|
| **Git/DevOps** | $672 | $648 | $672 | $672 | $0 |
| **Stakeholder access** | (included) | **$0 (free)** | (need Jira ~$840) | (included) | $0 |
| **CI/CD** | $0 | $480 | $36 | $0 | $0 |
| **Hosting** | $0 | $108 | $180 | $0 | $1,080 |
| **Database** | $900 | $468 | $540 | $360 | $0 |
| **Auth** | (in Supabase) | (in Entra) | $0 | $0 | $0 |
| **AI (Claude/OpenAI)** | $2,400 | $2,400 | $2,400 | $2,400 | $0 |
| **GPU (self-hosted LLM)** | $0 | $0 | $0 | $0 | $9,600 |
| **DevOps overhead** | $0 | $0 | $0 | $0 | $22,800 |
| **Total (USD/yr)** | **$3,972** | **$4,104** | **$4,668** | **$3,552** | **$33,480** |
| **Total (GBP/yr)** | **£3,138** | **£3,242** | **£3,688** | **£2,806** | **£26,437** |

### 5.2 At Scale (100 Clients, 50 Devs, 200 Stakeholders)

| Component | A: GitHub+Claude | B: Azure DevOps | C: AWS+Bedrock | D: GCP+Vertex | E: Sovereign OSS |
|---|---|---|---|---|---|
| **User licensing** | $22,680 | $3,240 | $22,680 | $22,680 | $0 |
| **Stakeholder access** | $10,080 | **$0** | ~$16,800 (Jira) | $10,080 | $0 |
| **CI/CD** | $1,632 | $2,880 | $1,200 | $1,632 | $0 |
| **Database** | $7,200 | $9,360 | $6,480 | $4,320 | $0 |
| **AI** | $24,000 | $24,000 | $24,000 | $24,000 | $0 |
| **GPU** | $0 | $0 | $0 | $0 | $57,600 |
| **DevOps overhead** | $0 | $0 | $0 | $0 | $45,600 |
| **Total (USD/yr)** | **$65,592** | **$39,480** | **$71,160** | **$62,712** | **$103,200** |
| **Total (GBP/yr)** | **£51,818** | **£31,189** | **£56,216** | **£49,543** | **£81,528** |

**At 100-client scale, Azure DevOps wins by 40-62%** over all other options, primarily due to unlimited free Stakeholder access. The 200 PFI client business users viewing Kanban boards cost $0 on Azure vs $10,080-$16,800 on GitHub/Jira.

---

## 6. Data Sovereignty Deep Dive

### 6.1 The CLOUD Act Problem

The US CLOUD Act (Clarifying Lawful Overseas Use of Data Act, 2018) compels US-headquartered companies to produce data to US law enforcement regardless of where that data is physically stored. This means:

| Provider | Headquartered | CLOUD Act Exposure | UK South Hosting Helps? |
|---|---|---|---|
| Microsoft (Azure, GitHub) | US (Redmond, WA) | **Yes** | No -- legal jurisdiction overrides physical location |
| Anthropic (Claude) | US (San Francisco, CA) | **Yes** | N/A (no UK region) |
| Amazon (AWS) | US (Seattle, WA) | **Yes** | No -- same CLOUD Act exposure |
| Google (GCP) | US (Mountain View, CA) | **Yes** | No -- same CLOUD Act exposure |
| OpenAI | US (San Francisco, CA) | **Yes** | N/A |
| Supabase Inc. | US (San Francisco, CA) | **Yes** | N/A |
| Hetzner | **Germany** | **No** | N/A -- EU/German jurisdiction |
| OVHcloud | **France** | **No** | N/A -- EU/French jurisdiction |
| Scaleway | **France** | **No** | N/A -- EU/French jurisdiction |

**Critical insight:** Hosting in UK South on Azure, London on AWS, or any UK region does **not** protect against CLOUD Act. The determining factor is the vendor's country of incorporation, not the data centre location.

### 6.2 AI Model Data Policies

| Policy | Claude (Anthropic) | Azure OpenAI | OpenAI Direct | Open Source (Self-Hosted) |
|---|---|---|---|---|
| **API data used for training** | **No** (commercial terms) | **No** (Microsoft guarantee) | **No** (API/Enterprise) | **N/A** (you control) |
| **Consumer data used for training** | Yes (opt-out from Sep 2025) | N/A | Yes (default, opt-out) | **N/A** |
| **Zero Data Retention (ZDR)** | **Yes** (optional addendum) | **Yes** (configurable) | **Yes** (Enterprise tier) | **Yes** (inherent) |
| **Data Processing Agreement** | Yes (GDPR DPA) | Yes (Microsoft DPA) | Yes (OpenAI Enterprise DPA) | **N/A** (you control) |
| **Support staff access** | Limited (safety review) | Global Microsoft staff may access | Limited | **None** (self-managed) |
| **Abuse monitoring retention** | 30 days (API), 0 under ZDR | Azure Content Safety | OpenAI moderation | **None** |

### 6.3 Claude Regional Availability

| Access Method | UK Region | EU Region | In-Region Processing? | CLOUD Act? |
|---|---|---|---|---|
| Anthropic Direct API | **No** | **No** (US servers) | No -- US processing | Yes |
| AWS Bedrock | **No** | Frankfurt (eu-central-1) | **Partial** -- cross-region inference may route outside EU | Yes |
| Google Vertex AI | **No** | **Frankfurt (europe-west3)** | **Yes -- genuine in-region** | Yes (Google = US) |
| Azure AI Foundry | **No** | Sweden Central only | **No** -- excluded from EU Data Boundary | Yes |

### 6.4 Azure OpenAI Regional Availability

| Region | Status | Data Residency | Notes |
|---|---|---|---|
| UK South | **Available (requires allow-listing)** | UK at-rest, but Global Standard may process elsewhere | Submit Azure form + support ticket |
| Sweden Central | Available | EU | Data Zone Standard (EUR) available |
| France Central | Available | EU | Limited model selection |
| West Europe (Netherlands) | Available | EU | Broadest EU model selection |
| US East / West | Default | US | Full model catalog |

**UK South allow-listing process:**
1. Submit Azure OpenAI access/update request form
2. Open Azure support ticket requesting UK South allow-listing
3. Cite UK-only data residency / public sector requirement
4. Approval timeline: 1-3 weeks

### 6.5 Sovereignty Tier Classification

| Tier | Description | CLOUD Act Risk | AI Quality | Cost Multiplier | Suitable For |
|---|---|---|---|---|---|
| **Tier 0: Standard SaaS** | US-hosted, no special controls | HIGH | 100% (frontier models) | 1x | Startups, agencies, non-regulated |
| **Tier 1: Regional Deployment** | EU/UK data centre, US vendor | MEDIUM (legal risk remains) | 100% | 1.1-1.3x | Most commercial enterprises |
| **Tier 2: Regional + ZDR** | EU/UK data centre, zero retention, DPA | LOW-MEDIUM | 100% | 1.2-1.5x | Financial services, healthcare |
| **Tier 3: EU Sovereign Infra** | EU-incorporated infra vendor, US AI via Vertex Frankfurt | LOW | 100% (Claude in-region) | 1.5-2x | Regulated UK/EU enterprises |
| **Tier 4: Fully Sovereign** | EU/UK infra, self-hosted OSS LLM, no US vendor | **NONE** | 70-90% | 8-10x | MOD, CNI, SCIF, public sector |

---

## 7. AI Model Strategy: Claude-Primary with Fallback

### 7.1 Claude as Primary (Recommended Default)

Your PFC skill architecture (DELTA pipeline, VE chain, 42 ontology parsers) is Claude-native. Claude Opus 4.6 remains the best model for:

- Complex multi-step agentic reasoning (DELTA 5-phase pipeline)
- Faithful JSON-LD structured output (ontology parsing)
- Extended thinking for strategic analysis (VSOM-SA chain)
- Tool use orchestration (Claude Code CLI)

**Cost:** $5/$25 per 1M tokens (Opus), $3/$15 (Sonnet), $0.80/$4 (Haiku)
**Parity:** Identical pricing across Anthropic Direct, AWS Bedrock, Google Vertex, Azure Foundry

### 7.2 OpenAI as Secondary (Enterprise M365 Integration)

For PFI instances embedded in Microsoft 365 environments, OpenAI provides capabilities Claude cannot match:

| Capability | OpenAI (via Azure/Copilot) | Claude |
|---|---|---|
| Teams bot integration | **Native (Copilot Studio)** | None |
| SharePoint content generation | **Native** | Via API + custom integration |
| Outlook email drafting | **Native** | None |
| Power BI natural language queries | **Native** | None |
| Excel formula/analysis | **Native** | None |

**Recommended pattern:**

```
Strategic Analysis (DELTA, VSOM-SA, BSC)  →  Claude (best reasoning)
M365 Content Layer (Teams, SharePoint)    →  OpenAI (native integration)
Bulk/Template Generation (EFS, reports)   →  Sonnet or GPT-4o-mini (cost)
```

### 7.3 Open Source as Sovereign Fallback

For Tier 4 sovereign deployments where no US vendor is acceptable:

| Model | Parameters | Context | Strengths | Weaknesses (vs Claude Opus) |
|---|---|---|---|---|
| **Llama 4** (Meta) | 70B / 405B | 128K | Best general-purpose OSS | Weaker multi-step reasoning |
| **Mistral Large** | 123B | 128K | Strong EU-aligned (French company) | Smaller context, less tool use |
| **DeepSeek V3.2** | 671B MoE | 128K | Near-frontier reasoning | Chinese origin (sovereignty concern for some) |
| **Qwen3-235B** | 235B | 128K | Strong multilingual, coding | Chinese origin (Alibaba) |
| **Command R+** (Cohere) | 104B | 128K | Enterprise RAG optimised | Narrower than Claude/GPT |

**Realistic quality assessment for PFC workloads:**

| PFC Skill | Claude Opus 4.6 | Best OSS (Llama 4 405B) | Quality Gap |
|---|---|---|---|
| pfc-delta-pipeline (orchestrator) | Excellent | Adequate | 25-30% degradation |
| pfc-delta-evaluate (CGA engine) | Excellent | Good | 15-20% degradation |
| pfc-reason (MECE, hypothesis) | Excellent | Good | 15-20% degradation |
| pfc-delta-narrate (5-section arc) | Excellent | Good | 10-15% degradation |
| EFS hierarchy management | Very Good | Good | 5-10% degradation |
| Ontology JSON-LD parsing | Excellent | Adequate | 20-25% degradation |
| BSC dashboard generation | Very Good | Good | 10-15% degradation |

**Serving stack for self-hosted:**

```
vLLM (serving engine, 2-4x throughput via PagedAttention)
  → OpenAI-compatible API endpoint
    → LangChain/LangGraph (replaces Claude Code skill orchestration)
      → Same ontology JSON-LD as tool results
        → PostgreSQL JSONB (unchanged)
```

### 7.4 Model Router Architecture

For PFI instances needing multiple models:

```
PFC Skill Invocation
  │
  ├── skill.modelPreference = "claude" (default)
  │     → Claude API (Direct / Bedrock / Vertex / Foundry)
  │
  ├── skill.modelPreference = "openai" (M365 PFIs)
  │     → Azure OpenAI API (UK South / Data Zone EUR)
  │
  ├── skill.modelPreference = "sovereign" (Tier 4 PFIs)
  │     → Self-hosted vLLM (Llama 4 / Mistral Large)
  │
  └── skill.modelPreference = "auto" (cost-optimised)
        → Haiku/GPT-4o-mini for simple tasks
        → Sonnet/GPT-4o for medium tasks
        → Opus for DELTA pipeline only
```

**Implementation:** Add `modelPreference` field to PFI config (`pfi-config.json`):

```json
{
  "pfiId": "pfi-airl-caf-aza",
  "aiConfig": {
    "modelPreference": "claude",
    "primaryProvider": "google-vertex",
    "primaryRegion": "europe-west3",
    "fallbackProvider": "azure-openai",
    "fallbackRegion": "uksouth",
    "sovereignOverride": null,
    "maxTokenBudgetPerCycle": 500000,
    "costTier": "auto"
  }
}
```

---

## 8. DevOps Platform Translation Guide

### 8.1 GitHub → Azure DevOps

| GitHub Component | Azure DevOps Equivalent | Translation Notes |
|---|---|---|
| `gh issue create --label type:epic` | `az boards work-item create --type Epic` | Native hierarchy -- no label enforcement needed |
| `validate-issue-naming.yml` | **Not needed** -- native WIT hierarchy | Eliminates workflow entirely |
| `validate-labels.yml` | **Not needed** -- Area Paths + WIT types | Eliminates workflow entirely |
| `enforce-registry-link.yml` | Azure Pipeline gate + custom policy | Similar YAML, different trigger syntax |
| `promote.yml` (dev→test→prod) | Multi-stage Pipeline with environments | Native approval gates, cleaner than custom workflow |
| `pfc-release.yml` (hub→spoke) | Azure Artifacts universal packages + Pipeline trigger | Different distribution pattern, same outcome |
| `setup-gh-project.sh` | Azure DevOps Process Template customisation | One-time config, richer than GH Projects |
| `bootstrap-triad.sh` | `bootstrap-triad-azure.sh` (NEW) | Uses `az devops` CLI instead of `gh` CLI |
| GitHub Projects (Kanban) | Azure Boards (Kanban/Scrum/CMMI) | Azure Boards objectively richer |
| GitHub Milestones | Azure Iteration Paths | More granular in Azure |
| GitHub Labels (28 standard) | Area Paths + Tags + Custom Fields | Different taxonomy model |
| GitHub Pages | Azure Static Web Apps | Free tier available, Standard $9/mo |
| GitHub Releases | Azure Artifacts + Release Pipelines | Different model but equivalent |
| Claude Code Plugin (6 skills) | Claude Code Plugin (unchanged) | CLI-based, platform-agnostic |

### 8.2 GitHub → AWS

| GitHub Component | AWS Equivalent | Translation Notes |
|---|---|---|
| GitHub Repos | **GitHub (keep)** or GitLab | CodeCommit deprecated Jan 2025 |
| GitHub Actions | AWS CodePipeline + CodeBuild | Less developer-friendly, YAML not portable |
| GitHub Issues | **None native** -- need Jira, Linear, or GitHub | AWS has no work item tracker |
| GitHub Projects | **None native** | AWS has no project board |
| promote.yml | CodePipeline stages + manual approval | More configuration needed |
| pfc-release.yml | CodePipeline + S3 artifact distribution | Different but functional |
| GitHub Pages | S3 + CloudFront | More setup, more control |

### 8.3 GitHub → GitLab (Self-Hosted)

| GitHub Component | GitLab CE Equivalent | Translation Notes |
|---|---|---|
| GitHub Repos | GitLab Repos | Near-identical |
| GitHub Actions | GitLab CI/CD (`.gitlab-ci.yml`) | Different YAML syntax, same concepts |
| GitHub Issues | GitLab Issues | Near-identical, with native hierarchy (Epics) |
| GitHub Projects | GitLab Boards | Similar Kanban capability |
| promote.yml | GitLab Environments + manual jobs | Native environment concept |
| pfc-release.yml | GitLab Releases + Package Registry | Similar pattern |
| GitHub Pages | GitLab Pages | Equivalent |
| gh CLI | glab CLI | Different syntax, same operations |

### 8.4 GitHub → Gitea (Self-Hosted, GH-Compatible)

| GitHub Component | Gitea Equivalent | Translation Notes |
|---|---|---|
| GitHub Repos | Gitea Repos | Near-identical |
| GitHub Actions | Gitea Actions (GH Actions-compatible YAML) | **Direct port** -- same YAML syntax |
| GitHub Issues | Gitea Issues | Simpler but functional |
| GitHub Projects | Gitea Projects (basic Kanban) | Less feature-rich |
| gh CLI | tea CLI | Different syntax |

**Gitea advantage:** Actions YAML compatibility means your existing workflows (`validate-issue-naming.yml`, `enforce-registry-link.yml`, `promote.yml`) work with minimal changes. Lowest migration effort for sovereign deployments.

---

## 9. EFS-ONT Integration: Platform-Agnostic Work Item Model

### 9.1 EFS as the Interoperability Contract

Your EFS-ONT v2.0.0 already defines tool integration patterns:

| EFS Entity | GitHub | Azure Boards | Jira | GitLab |
|---|---|---|---|---|
| `efs:Epic` | Issue + `type:epic` label | Work Item Type: Epic | Epic | Epic |
| `efs:Feature` | Issue + `type:feature` | Feature | Story (mapped) | Feature |
| `efs:UserStory` | Issue + `type:story` | User Story | Story | Issue |
| `efs:Task` | Checklist / sub-issue | Task | Sub-task | Task |
| `efs:Release` | GitHub Release | Release Pipeline | Fix Version | Release |
| `efs:Sprint` | Milestone | Iteration Path | Sprint | Milestone |
| `efs:Hypothesis` | Issue body | Custom field | Custom field | Description |
| `efs:Outcome` | Acceptance criteria | Acceptance criteria | Acceptance criteria | Description |
| `efs:PBS` | `type:pbs` label | Custom WIT / Area Path | Component | Label |
| `efs:WBS` | `type:wbs` label | Custom WIT / Area Path | Component | Label |

### 9.2 Bidirectional Sync Pattern

```
EFS-ONT JSON-LD (canonical source of truth)
  │
  ├── GitHub adapter
  │     gh issue create → efs:BacklogItem
  │     gh issue edit   → efs:BacklogItem.update()
  │     gh issue close  → efs:BacklogItem.status = "done"
  │
  ├── Azure Boards adapter
  │     az boards work-item create → efs:BacklogItem
  │     az boards work-item update → efs:BacklogItem.update()
  │     az boards work-item close  → efs:BacklogItem.status = "done"
  │
  ├── Jira adapter (future)
  │     jira issue create → efs:BacklogItem
  │
  └── GitLab adapter (future)
        glab issue create → efs:BacklogItem
```

This pattern means PFI instances on different platforms can share the same EFS ontology-governed hierarchy. The adapter layer is thin -- ~200 lines of CLI wrapper per platform.

---

## 10. Recommended Strategy: Tiered Platform Cascade

### 10.1 The PFC → PFI Platform Independence Principle

**Core principle:** PFC-Core remains on GitHub. Each PFI instance independently selects its platform stack. The Unified Registry JSON-LD is the interoperability contract.

```
PFC-CORE (GitHub -- unchanged, canonical)
  │
  │  pfc-release.yml distributes to ALL platform types:
  │
  ├── GitHub PFI spoke (existing)
  │     PR with filtered registry snapshot
  │
  ├── Azure DevOps PFI spoke (NEW)
  │     Azure Artifacts universal package + work item creation
  │
  ├── AWS PFI spoke (NEW)
  │     S3 artifact + CodePipeline trigger
  │
  └── GitLab PFI spoke (NEW)
        GitLab Package Registry + merge request
```

### 10.2 PFI Client Decision Matrix

| PFI Client Profile | Recommended Stack | AI Primary | AI Secondary | Sovereignty Tier |
|---|---|---|---|---|
| **Startup / Agency** | A: GitHub + Claude + Supabase | Claude Direct | None | Tier 0 |
| **SME (UK, non-regulated)** | A: GitHub + Claude + Supabase | Claude Direct | None | Tier 0/1 |
| **Enterprise (Azure EA)** | B: Azure DevOps + Claude Foundry | Claude (Vertex) | Azure OpenAI (M365) | Tier 1/2 |
| **Enterprise (AWS)** | C: GitHub + Claude Bedrock + RDS | Claude (Bedrock) | None | Tier 1/2 |
| **Regulated UK/EU** | D: GitHub + Claude Vertex Frankfurt | Claude (Vertex) | None | Tier 2/3 |
| **Regulated + M365** | B+D: Azure DevOps + Vertex Frankfurt | Claude (Vertex) | Azure OpenAI (UK South) | Tier 2/3 |
| **UK Public Sector** | E: GitLab/Gitea + Vertex Frankfurt + Hetzner | Claude (Vertex) | Self-hosted Mistral | Tier 3 |
| **MOD / CNI / SCIF** | E: Gitea + vLLM + self-hosted PostgreSQL | Self-hosted Llama/Mistral | None | Tier 4 |

### 10.3 Implementation Phases

#### Phase 1: Azure DevOps Support (Q2 2026) -- OBJ-MP-1

| Deliverable | Effort | Priority |
|---|---|---|
| `bootstrap-triad-azure.sh` -- one-command Azure DevOps PFI setup | 2 weeks | HIGH |
| `pfc-release-azure.yml` -- Azure Artifacts spoke in hub-and-spoke | 1 week | HIGH |
| Azure Boards work item templates (Epic/Feature/Story with EFS fields) | 3 days | HIGH |
| Azure Pipeline equivalents (promote, validate-naming, enforce-registry) | 1 week | HIGH |
| Azure Boards ↔ EFS-ONT adapter (bidirectional sync) | 1 week | MEDIUM |
| Documentation: PFI Azure onboarding guide | 2 days | HIGH |

#### Phase 2: Claude Multi-Provider (Q2-Q3 2026) -- OBJ-MP-3

| Deliverable | Effort | Priority |
|---|---|---|
| `aiConfig` field in PFI config schema | 1 day | HIGH |
| Claude Vertex Frankfurt deployment guide | 2 days | HIGH |
| Claude Bedrock Frankfurt deployment guide | 2 days | MEDIUM |
| Azure OpenAI adapter for PFC skills (function calling translation) | 3-4 weeks | MEDIUM |
| Model router (auto-select based on PFI config) | 1 week | MEDIUM |
| Token budget tracking per DELTA cycle | 3 days | MEDIUM |

#### Phase 3: Sovereign Stack (Q3-Q4 2026) -- OBJ-MP-2

| Deliverable | Effort | Priority |
|---|---|---|
| Gitea deployment playbook (Docker Compose + Actions runner) | 1 week | MEDIUM |
| vLLM + Llama 4/Mistral serving guide | 1 week | MEDIUM |
| LangChain/LangGraph skill adapter (replaces Claude Code for OSS models) | 4-6 weeks | LOW |
| Keycloak auth integration (replaces Supabase Auth) | 2 weeks | LOW |
| Sovereign PFI bootstrap script (`bootstrap-triad-sovereign.sh`) | 2 weeks | LOW |
| Quality benchmark: DELTA pipeline on OSS vs Claude | 1 week | MEDIUM |

#### Phase 4: Enterprise Value-Add (Q4 2026+) -- OBJ-MP-1

| Deliverable | Effort | Priority |
|---|---|---|
| Power BI BSC dashboard from VSOM KPI-ONT | 2 weeks | LOW |
| Viva Goals ↔ VSOM-ONT OKR sync | 2 weeks | LOW |
| Copilot Studio front-end for DELTA output consumption | 3 weeks | LOW |
| Jira adapter for EFS-ONT sync | 2 weeks | LOW |
| Multi-platform PFI migration tool (GH→Azure, GH→GitLab) | 3 weeks | LOW |

---

## 11. Risk Register (GRC-FW-ONT Aligned)

| ID | Risk | Probability | Impact | Mitigation | Owner |
|---|---|---|---|---|---|
| R-MP-001 | CLOUD Act data access on US-hosted PFI data | MEDIUM | HIGH | Tier 2+ for regulated PFIs; ZDR addendum; customer-managed encryption keys | PFC Platform |
| R-MP-002 | Claude unavailable (outage, contract dispute, region block) | LOW | CRITICAL | Model router + OpenAI fallback + OSS fallback in PFI config | PFC Platform |
| R-MP-003 | Skill quality degrades on non-Claude models | MEDIUM | HIGH | Quality benchmark suite; skill-level model requirements; fallback warnings | PFC Skills |
| R-MP-004 | Azure DevOps migration effort exceeds estimate | MEDIUM | MEDIUM | Phase 1 PoC with single PFI (AIRL) before committing | PFI-AIRL |
| R-MP-005 | Self-hosted GPU costs exceed budget | MEDIUM | MEDIUM | Start with Hetzner (€250-400/mo), not Azure GPU ($2,700/mo); use smallest viable model | PFC Platform |
| R-MP-006 | Multi-platform maintenance burden unsustainable for solo operator | HIGH | HIGH | Automate via bootstrap scripts; only add platforms when paying PFI demands it | PFC Platform |
| R-MP-007 | Anthropic consumer training policy erodes enterprise trust | LOW | MEDIUM | Communicate: API/Team/Enterprise exempt; ZDR available; commercial terms unchanged | PFC Sales |
| R-MP-008 | EU e-evidence regulation (Aug 2026) creates new compliance burden | MEDIUM | MEDIUM | Tier 3+ for EU PFIs; legal review of DPA terms with all vendors | PFC Legal |
| R-MP-009 | Ontology lock-in via Claude-specific prompt patterns | MEDIUM | HIGH | OBJ-MP-5: all ontologies remain pure JSON-LD; no Claude-specific fields | PFC Ontology |
| R-MP-010 | PFI clients demand platform not yet supported | MEDIUM | LOW | EFS-ONT adapter pattern keeps effort per new platform at 2-3 weeks | PFC Platform |

---

## 12. Metrics (VSOM KPI-ONT Aligned)

| KPI | Target | Measurement | Frequency |
|---|---|---|---|
| Platforms supported for PFI deployment | 3 (GitHub, Azure, GitLab) | Count of working bootstrap-triad scripts | Quarterly |
| PFI bootstrap time (any platform) | < 2 hours | Time from script invocation to first Epic created | Per instance |
| Ontology changes required for platform switch | 0 | Diff of ontology files across platform deployments | Per migration |
| AI model fallback activation rate | < 5% of requests | Model router logs | Monthly |
| DELTA pipeline quality on non-Claude (Opus baseline = 100%) | > 85% on Sonnet/GPT-4o, > 70% on OSS | Quality benchmark suite scores | Quarterly |
| Sovereignty tier compliance | 100% PFIs at or above client-mandated tier | Audit against PFI client contract | Quarterly |
| Platform maintenance hours | < 10 hrs/mo for multi-platform support | Engineering time tracking | Monthly |
| Cost per PFI instance (blended) | < $150/mo | Total platform cost / active PFI count | Monthly |

---

## 13. Decision Record

### 13.1 Key Decisions Made

| Decision | Rationale |
|---|---|
| **PFC-Core stays on GitHub** | Proven, operational, 1,527 tests, no migration benefit. PFC is the hub; hubs don't move. |
| **PFI instances choose their platform** | S4 (Instance Customisation) requires it. Enterprise clients will mandate their stack. |
| **Claude remains primary AI** | Best model for multi-agent reasoning. Skill rewrite cost ($50K+ effort) not justified. |
| **OpenAI is secondary, not replacement** | M365 integration value is real. But analytical engine stays Claude. |
| **Sovereign stack is an option, not default** | 9x cost multiplier means only Tier 4 clients justify it. Don't over-engineer for edge cases. |
| **EFS-ONT is the platform interop contract** | Work item hierarchy must be consistent across GitHub/Azure/Jira/GitLab. EFS-ONT governs the schema. |
| **Google Vertex Frankfurt is the EU Claude gateway** | Only genuine in-region Claude processing. Recommended for all Tier 2+ PFIs. |

### 13.2 Decisions Deferred

| Decision | Deferred Until | Trigger |
|---|---|---|
| Jira adapter | First PFI client on Atlassian stack | Client contract signed |
| AWS-native DevOps (CodePipeline) | First PFI client on AWS-only | Client contract signed |
| Neo4j graph database | JSONB pattern hits performance ceiling | > 1M nodes per PFI graph |
| Copilot Studio front-end | First enterprise PFI with M365 Copilot deployed | Client request |
| Self-hosted LLM investment | First Tier 4 PFI client (MOD/CNI) | Contract + budget confirmed |

---

## 14. Appendix A: Bootstrap Script Comparison

### `bootstrap-triad.sh` (GitHub -- existing)

```bash
./bootstrap-triad.sh \
  --name baiv-ai-visibility \
  --org ajrmooreuk \
  --pin pfc-v1.0.0 \
  --license pfc-enterprise \
  --ontologies "VE-Series,PE-Series,Foundation" \
  --project-prefix "PFI-BAIV"
```

### `bootstrap-triad-azure.sh` (Azure -- proposed)

```bash
./bootstrap-triad-azure.sh \
  --name airl-caf-aza \
  --org "https://dev.azure.com/pfc-airl" \
  --project "PFI-AIRL" \
  --pin pfc-v1.0.0 \
  --license pfc-enterprise \
  --ontologies "VE-Series,PE-Series,Foundation,RCSG-Series" \
  --process Agile \
  --ai-provider "google-vertex" \
  --ai-region "europe-west3" \
  --sovereignty-tier 2
```

### `bootstrap-triad-sovereign.sh` (Self-hosted -- proposed)

```bash
./bootstrap-triad-sovereign.sh \
  --name mod-secure-instance \
  --gitea-url "https://gitea.sovereign.mod.uk" \
  --pin pfc-v1.0.0 \
  --license pfc-sovereign \
  --ontologies "VE-Series,PE-Series,Foundation,RCSG-Series" \
  --ai-engine "vllm" \
  --ai-model "mistral-large-2" \
  --ai-gpu-host "gpu01.sovereign.mod.uk" \
  --db-host "pg01.sovereign.mod.uk" \
  --sovereignty-tier 4
```

---

## 15. Appendix B: Vendor Contact & Procurement

| Vendor | Product | Procurement Route | UK Entity | Notes |
|---|---|---|---|---|
| **Anthropic** | Claude API (Direct) | Self-service or Enterprise sales | N/A (US only) | ZDR addendum requires Enterprise contract |
| **Anthropic via Google** | Claude on Vertex AI | Google Cloud Marketplace | Google Cloud UK Ltd | MACC-equivalent via Google CUD |
| **Anthropic via AWS** | Claude on Bedrock | AWS Marketplace | Amazon Web Services UK Ltd | EDP-eligible |
| **Anthropic via Microsoft** | Claude on Azure Foundry | Azure Marketplace | Microsoft Ltd (UK) | MACC-eligible |
| **Microsoft** | Azure DevOps / OpenAI | Microsoft EA / CSP | Microsoft Ltd (UK) | Volume discounts at 50+ seats |
| **GitHub** | Team / Enterprise | GitHub Sales or Microsoft EA | Microsoft (via GitHub Inc.) | 15-35% discount on multi-year |
| **Supabase** | Pro / Team | Self-service | Supabase Inc. (US) | No UK entity |
| **Hetzner** | Dedicated / Cloud | Self-service | Hetzner Online GmbH (DE) | EU-sovereign, no CLOUD Act |
| **OVHcloud** | Public Cloud | Self-service or Enterprise | OVH Ltd (UK subsidiary) | EU-sovereign, UK presence |
| **Scaleway** | GPU Instances | Self-service | Scaleway SAS (FR) | EU-sovereign, competitive GPU pricing |

---

## 16. Appendix C: Regulatory Reference

| Regulation | Jurisdiction | Relevance | Impact on Platform Choice |
|---|---|---|---|
| **UK GDPR** (2018/retained EU law) | UK | All PFI instances processing UK personal data | Requires DPA with all vendors; lawful basis for AI processing |
| **UK Data Protection Act 2018** | UK | Supplements UK GDPR | ICO is supervisory authority |
| **EU GDPR** (2016/679) | EU | PFI instances processing EU personal data | Requires SCCs or adequacy decision for non-EU transfers |
| **US CLOUD Act** (2018) | US (extraterritorial) | All US-headquartered vendors | Legal basis for US government data access regardless of storage location |
| **EU e-evidence Regulation** | EU (from 17 Aug 2026) | PFI instances in EU | New cross-border evidence framework; may conflict with CLOUD Act |
| **UK-US Data Access Agreement** | UK-US bilateral | UK PFIs using US vendors | Streamlines lawful access requests; does not eliminate CLOUD Act risk |
| **NIS2 Directive** (EU) | EU (from Oct 2024) | Critical infrastructure PFIs | Mandates risk management, incident reporting for essential services |
| **DORA** (EU) | EU (from Jan 2025) | Financial services PFIs | ICT risk management, third-party provider oversight |
| **UK Cyber Security Bill** (proposed) | UK | All PFI instances | Expanding NIS requirements to more sectors |

---

*This strategy document is a living artefact. Update as PFI client mandates emerge and platform capabilities evolve. Next review: Q2 2026 after Phase 1 Azure DevOps PoC with PFI-AIRL.*
