# ARCH-CICD-005: GitHub PM Automation — Project Governance, Instance Hierarchy & Auto-Add Cascade

**Version:** 1.0.0
**Date:** 2026-03-17
**Status:** Active
**Parent:** ARCH-CICD-001 (Hub-and-Spoke Proposal)
**Epic:** Epic 61: PFC-ARCH-CICD [#947](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/947)
**Feature:** F61.29 [#1241](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1241)
**Brief:** [PFC-CICD-BRIEF-Workflow-Governance-Project-Automation-Cascade-v1.0.0](../../STRATEGY/PFC-CICD-BRIEF-Workflow-Governance-Project-Automation-Cascade-v1.0.0.md)

---

## 1. Purpose

This document defines the operational architecture for GitHub Projects-based programme management across the PFC-PFI cascade. It covers:

- The instance ownership hierarchy and how it shapes project structure
- The two-project model (Engineering + Product) applied at every tier
- The hard rule on project creation authority
- Label-driven auto-add routing
- Collaborator permission model
- Instance registry schema for hierarchy and backup metadata
- Operating procedures for setup and troubleshooting

It is a peer document to ARCH-CICD-002 (Promotion Pipeline) and ARCH-CICD-004 (Operating Guide). It does not replace the strategy decisions in the brief above — it operationalises them.

---

## 2. Instance Ownership Hierarchy

The platform supports four instance types arranged in an ownership chain. Each type gets the same two-project structure, same routing, same project creation rule. The ownership type governs permissions, promotion gates, and backup tier.

```
Hub (Azlan-EA-AAA)
│   Governance, workflow authorship, sole project creation authority
│
└── OWNER_INSTANCE  (e.g. W4M-RCS, AIRL, BAIV)
    │   Top-level PFI — owns its own triad + everything beneath it
    │   Engineering project: Hub admin = Admin
    │
    ├── PRODUCT  (e.g. AZA product under W4M-RCS)
    │   │   Separate product board within or alongside owner PFI
    │   │   Engineering project: Hub admin = Admin, Owner PFI lead = Read
    │   │
    │   └── CLIENT_SUB_INSTANCE  (private deployment of a product for a client)
    │           Lives under owner PFI brand or as named sub-instance
    │           Engineering: Hub admin + Owner PFI lead = Admin; all others Read
    │           Product: client collaborators = Write
    │
    └── CLIENT_PFI  (full PFI triad provisioned for a client, Partner-branded)
            Owner PFI holds admin; client team are collaborators
            Engineering: Hub admin + Owner PFI lead = Admin only
            Product: client team = Write
            Backup and promotion governed as OWNER_INSTANCE — T1 obligation
```

**Rules:**
- An Owner PFI **manages** client instances — it does not own the client's data
- Private client deployments are never referenced in public hub issues
- Client instances promote through the Owner PFI's prod gate (`promote.yml` references `parent_pfi`)
- `guard-core.yml` in client instances seals the PFC core derived from Owner PFI prod

---

## 3. The Two-Project Model

Every PFI dev repo at every hierarchy tier has exactly **two** GitHub Projects.

### 3.1 Project Definitions

| Project | Naming | Purpose |
|---|---|---|
| **Engineering** | `{Instance} Engineering` | CI/CD, scaffold, triad ops, devops, branch protection, EFS scaffolding |
| **Product** | `{Instance} Product` | Epics, features, stories, RAID logs, briefs, proposals, cross-refs |

The triad test/prod boards remain as promotion-stage views and are unchanged by this model.

### 3.2 Permission Matrix

| Role | Engineering project | Product project |
|---|---|---|
| Hub admin (Azlan/Amanda) | Admin | Admin |
| Owner PFI lead | Read | Write |
| Product collaborators | Read | Write |
| Client collaborators (CLIENT_PFI) | — | Write |
| All others | — | — |

### 3.3 Hard Rule: Hub Admins Create All Projects

**All GitHub Projects at any tier are created exclusively by Hub admins using `PROJECT_PAT`.**

This is not a guideline. It is enforced by:

- `PROJECT_PAT` is held only by Hub admins. On a personal GitHub account the account owner is the only entity that can create projects — GitHub enforces this natively.
- The `setup-project-board` skill must be invoked only by the Hub admin.
- No workflow automation creates projects. `auto-add-to-projects.yml` only adds issues to *existing* projects.

Non-negotiable rationale: project sprawl and orphaned boards create governance debt that compounds with scale. The two-project model works only if Engineering is correctly owned and Product is correctly scoped. Neither can be delegated.

---

## 4. Label-Driven Auto-Add Routing

Each PFI dev repo has a generated `auto-add-to-projects.yml` workflow that routes newly opened issues to the correct project by label.

### 4.1 Routing Logic

```
Issue opened in PFI dev repo
       │
       ├── type:engineering  OR  domain:devops
       │         → Engineering project
       │
       └── type:epic  OR  type:feature  OR  type:story
                 OR  type:raid  OR  type:brief  OR  type:proposal
                 → Product project
```

No label match → issue is opened without project assignment. Collaborator should add the correct type label.

### 4.2 How the Workflow Is Generated

The `setup-repo` skill generates `auto-add-to-projects.yml` at instance creation time with the Engineering and Product project node IDs (`PVT_...`) baked in. The file is committed to the PFI dev repo and never manually edited.

```yaml
# Generated — do not edit manually
# Regenerate via: /azlan-github-workflow:setup-repo {owner/repo} --regen-auto-add
name: Auto-Add Issues to Project Boards
on:
  issues:
    types: [opened]
permissions:
  issues: read
jobs:
  route:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/github-script@v7
        with:
          github-token: ${{ secrets.PROJECT_PAT }}
          script: |
            const ENGINEERING_ID = 'PVT_XXXX';  # baked in by setup-repo
            const PRODUCT_ID     = 'PVT_YYYY';  # baked in by setup-repo
            const labels = context.payload.issue.labels.map(l => l.name);
            const engLabels = ['type:engineering', 'domain:devops'];
            const prodLabels = [
              'type:epic','type:feature','type:story',
              'type:raid','type:brief','type:proposal'
            ];
            const target = labels.some(l => engLabels.includes(l)) ? ENGINEERING_ID
                         : labels.some(l => prodLabels.includes(l)) ? PRODUCT_ID
                         : null;
            if (!target) return;
            await github.graphql(`
              mutation($p:ID!,$c:ID!) {
                addProjectV2ItemById(input:{projectId:$p,contentId:$c}){item{id}}
              }`, { p: target, c: context.payload.issue.node_id });
```

### 4.3 Required Secrets

`PROJECT_PAT` must be set on each PFI dev repo with `project` scope. Only the Hub admin holds this PAT.

---

## 5. Instance Registry Schema

`pfc-instance-registry.json` (the source of truth for all automation consuming instance metadata) carries the following fields per entry. These are written by `setup-repo` at instance creation and consumed by backup tooling (F61.24), `pfc-release.yml`, and drift detection.

```json
{
  "instance_id": "PFI-AIRL-CAF-AZA",
  "instance_type": "OWNER_INSTANCE",
  "parent_pfi": null,
  "triad": {
    "dev": "ajrmooreuk/pfi-airl-caf-aza-dev",
    "test": "ajrmooreuk/pfi-airl-caf-aza-test",
    "prod": "ajrmooreuk/pfi-airl-caf-aza-prod"
  },
  "engineering_project_id": "PVT_...",
  "product_project_id": "PVT_...",
  "backup_tier": "T1"
}
```

### 5.1 `instance_type` Values

| Value | Description |
|---|---|
| `OWNER_INSTANCE` | Top-level PFI (W4M-RCS, AIRL, BAIV) |
| `PRODUCT` | Product within an owner PFI (e.g. AZA under W4M-RCS) |
| `CLIENT_SUB_INSTANCE` | Private client deployment of a product |
| `CLIENT_PFI` | Full client-specific PFI triad — Partner-branded, private |

### 5.2 `backup_tier` Derivation

Derived from `instance_type` at registration. Never set manually.

| Instance Type | Backup Tier |
|---|---|
| Hub (Azlan-EA-AAA) | T0 |
| PFC triad (pfc-dev/test/prod) | T0 |
| `OWNER_INSTANCE` | T1 |
| `PRODUCT` | T1 |
| `CLIENT_SUB_INSTANCE` | T1 |
| `CLIENT_PFI` | T1 |

All client-bearing types default to T1 regardless of size — client data is a T1 obligation.

### 5.3 Backup Auto-Enrolment (F61.24)

Once F61.24 is implemented, backup tooling (`gh-backup-bare.sh`, `cicd-audit.sh`, `resilience-sweep.yml`) reads the repo list from the registry. `setup-repo` writing a `backup_tier` to the registry entry is sufficient to enrol the new instance in the next backup cycle. No manual list update required.

---

## 6. Workflow Governance — Quality Workflow Stubs

PFI dev repos must not replicate hub quality workflows. They must call them via `workflow_call`.

### 6.1 Workflows Replaced with Stubs (per PFI dev repo)

| Workflow | Hub source |
|---|---|
| `validate-issue-naming.yml` | `ajrmooreuk/Azlan-EA-AAA/.github/workflows/validate-issue-naming.yml@main` |
| `enforce-registry-link.yml` | `ajrmooreuk/Azlan-EA-AAA/.github/workflows/enforce-registry-link.yml@main` |
| `validate-labels.yml` | `ajrmooreuk/Azlan-EA-AAA/.github/workflows/validate-labels.yml@main` |

Stub pattern:

```yaml
name: Validate Issue Naming
on:
  issues:
    types: [opened, edited]
jobs:
  validate:
    uses: ajrmooreuk/Azlan-EA-AAA/.github/workflows/validate-issue-naming.yml@main
    secrets: inherit
```

### 6.2 Workflows That Stay in PFI Repos

| Workflow | Reason |
|---|---|
| `promote.yml` | References PFI-specific repo names, secrets, and `parent_pfi` gate |
| `guard-core.yml` | PFI-specific sealed core paths |
| `auto-add-to-projects.yml` | Contains baked-in PFI-specific project node IDs |

---

## 7. Operating Procedures

### 7.1 Setting Up a New Instance

```
1. Hub admin creates Engineering project
   /azlan-github-workflow:setup-project-board "{Instance} Engineering" --owner ajrmooreuk
   → note the returned PVT_... node ID

2. Hub admin creates Product project
   /azlan-github-workflow:setup-project-board "{Instance} Product" --owner ajrmooreuk
   → note the returned PVT_... node ID

3. Hub admin provisions the repo
   /azlan-github-workflow:setup-repo {owner/repo} \
     --instance-type OWNER_INSTANCE \
     --parent-pfi null \
     --engineering-project-id PVT_XXXX \
     --product-project-id PVT_YYYY
   → generates auto-add-to-projects.yml, writes registry entry, sets labels + branch protection

4. Hub admin sets PROJECT_PAT secret on the dev repo
   gh secret set PROJECT_PAT --repo {owner/repo}

5. Invite collaborators
   - Repo: Write
   - Engineering project: Read
   - Product project: Write
```

### 7.2 Setting Up a Client Sub-Instance or Client PFI

Same steps as §7.1. Use `--instance-type CLIENT_SUB_INSTANCE` or `CLIENT_PFI` and set `--parent-pfi {owner-pfi-id}`. The `backup_tier` is automatically set to T1.

Note: client-specific PFIs are private. Do not reference their repos or project IDs in public hub issues.

### 7.3 Regenerating the Auto-Add Workflow

If project IDs change (project deleted and re-created):

```bash
# Re-run setup-repo with new IDs — overwrites auto-add-to-projects.yml
/azlan-github-workflow:setup-repo {owner/repo} --regen-auto-add \
  --engineering-project-id PVT_NEW1 \
  --product-project-id PVT_NEW2
```

### 7.4 Migrating an Existing PFI to the Two-Project Model

For repos already in operation (e.g. AIRL dev during F61.29):

```
1. Create both projects (§7.1 steps 1–2)
2. Manually add existing open issues to the correct project (one-time backfill)
   - type:engineering issues → Engineering project
   - type:epic/feature/story/raid → Product project
3. Run setup-repo with --regen-auto-add (step 3 above) to generate the workflow
4. Set PROJECT_PAT if not already present
5. Replace 3 replicated quality workflows with workflow_call stubs (§6.1)
```

---

## 8. Troubleshooting

### Issue opened but not appearing on any project board

1. Check the issue has a routing label (`type:epic`, `type:engineering`, etc.)
2. Check `auto-add-to-projects.yml` exists in the repo's `.github/workflows/`
3. Check `PROJECT_PAT` secret is set on the repo: `gh secret list --repo {owner/repo}`
4. Check the workflow ran: `gh run list --repo {owner/repo} --workflow auto-add-to-projects.yml`
5. If the PAT has expired: rotate and re-set. PAT drift detection (F61.5) will alert weekly.

### Issue appears on wrong board

Label mismatch — the issue has a label that matches the wrong routing rule. Check the label against §4.1. Relabel and manually move the item, or add the correct label and it will route on the next open.

### Project board missing (collaborator reports they can't see it)

Projects are private by default on personal accounts. The Hub admin must explicitly invite the collaborator to the project with the correct role (Read for Engineering, Write for Product).

### `auto-add-to-projects.yml` workflow not triggering

This workflow requires `PROJECT_PAT` with `project` scope. A standard `GITHUB_TOKEN` cannot write to Projects v2. Confirm the secret name matches exactly.

---

## 9. Related Documents

| Document | Location | Relevance |
|---|---|---|
| ARCH-CICD-001 | `arch-cicd/01-hub-and-spoke-proposal.md` | Hub-and-spoke architecture decision |
| ARCH-CICD-002 | `arch-cicd/02-promotion-pipeline-detail.md` | Promotion mechanics |
| ARCH-CICD-003 | `arch-cicd/03-glossary.md` | Term definitions |
| ARCH-CICD-004 | `arch-cicd/04-operating-guide.md` | Day-to-day CI/CD operations |
| PFC-CICD-BRIEF | `PBS/STRATEGY/PFC-CICD-BRIEF-Workflow-Governance-Project-Automation-Cascade-v1.0.0.md` | Strategy decisions (D1–D10) |
| F61.24 | [#1206](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1206) | URG-driven backup auto-enrolment |
| F61.29 | [#1241](https://github.com/ajrmooreuk/Azlan-EA-AAA/issues/1241) | Implementation work |
| `setup-project-board` skill | `azlan-github-workflow/skills/setup-project-board/SKILL.md` | Board creation skill |
| `setup-repo` skill | `azlan-github-workflow/skills/setup-repo/SKILL.md` | Repo provisioning skill |
