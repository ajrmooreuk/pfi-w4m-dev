# PFI Release & Promotion — Quick Training Guide

**Audience:** PFI instance team leads (BAIV, AIRL, W4M, VHF)
**Date:** 2026-02-20
**Duration:** ~15 min walkthrough

---

## Overview: How content flows from PF-Core to your PFI

```
  PF-Core Hub (Azlan-EA-AAA)
  ont-registry-index.json v9.4.0 — 46 ontologies
          │
          │  pfc-release.yml
          │  (filters by YOUR series subscription)
          │
    ┌─────┴──────────────┐
    │                    │
    ▼                    ▼
 pfi-baiv-dev         pfi-airl-dev
 45 ontologies        33 ontologies
 (all 5 series)       (RCSG+VE+Foundation)
    │                    │
    │  promote.yml       │  promote.yml
    │  dev → test        │  dev → test
    ▼                    ▼
 pfi-baiv-test        pfi-airl-test
    │                    │
    │  promote.yml       │  promote.yml
    │  test → prod       │  test → prod
    ▼                    ▼
 pfi-baiv-prod        pfi-airl-prod
```

Each PFI has a **triad** of repos (dev / test / prod). Content always flows **down**: Hub → dev → test → prod. Never sideways, never backwards.

---

## Step 1: PF-Core Release (Hub → Your Dev Repo)

**Who triggers this:** PF-Core team (not PFI teams)
**What it does:** Pushes a filtered ontology registry snapshot to your dev repo as a PR

### What just happened (live example)

We triggered a release targeting BAIV and AIRL:

| PFI | PR Created | Ontologies Received |
|-----|-----------|-------------------|
| **BAIV** | [pfi-baiv-aiv-dev #38](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/38) | 45 (VE + PE + RCSG + Foundation + Orchestration) |
| **AIRL** | [pfi-airl-caf-aza-dev #39](https://github.com/ajrmooreuk/pfi-airl-caf-aza-dev/pull/39) | 33 (RCSG + VE + Foundation only) |

Notice: AIRL gets fewer ontologies because it only subscribes to 3 series. The pipeline **automatically filters** based on your `hubSpokeConfig.ontologySeries` in the registry.

### Your action: Review and merge the PR on your dev repo

The PR updates two files in `pfc-core/`:
- `ontology-registry.json` — your filtered registry snapshot
- `pfc-version` — records which release this came from

> **Do not edit `pfc-core/` manually.** It will be overwritten on the next release.

---

## Step 2: Promote Dev → Test

**Who triggers this:** Your PFI team
**When:** After you've merged the PF-Core release PR and validated your dev environment

### How to trigger

From your **dev repo**, run:

```bash
gh workflow run promote.yml \
  --repo ajrmooreuk/pfi-YOUR-INSTANCE-dev \
  --field direction="dev-to-test"
```

**Example for BAIV:**
```bash
gh workflow run promote.yml \
  --repo ajrmooreuk/pfi-baiv-aiv-dev \
  --field direction="dev-to-test"
```

**Example for AIRL:**
```bash
gh workflow run promote.yml \
  --repo ajrmooreuk/pfi-airl-caf-aza-dev \
  --field direction="dev-to-test"
```

### What gets promoted

The workflow copies these directories from dev → test:

| Directory | Contents |
|-----------|----------|
| `pfc-core/` | Ontology registry snapshot + version pin |
| `instance-data/` | VP/RRR instance data, RBAC roles |
| `supabase/` | Database schema and seed data |
| `tools/` | Utility scripts |
| `promotion/` | Promotion config (for the next tier) |

### What happens

1. Workflow reads `promotion/promotion.env` (knows which test repo to target)
2. Checks out your dev repo and your test repo
3. Copies the directories listed above
4. If anything changed → creates a PR on your **test** repo
5. If nothing changed → skips (no empty PRs)

### Your action: Merge the PR on your test repo

Review the changes — this is your QA gate. Validate that:
- Registry snapshot looks correct
- Instance data is present and well-formed
- Supabase schema hasn't broken

---

## Step 3: Promote Test → Prod

**Who triggers this:** Your PFI team lead (requires SME approval)
**When:** After test environment validation is complete

### How to trigger

From your **dev repo** (same workflow, different direction):

```bash
gh workflow run promote.yml \
  --repo ajrmooreuk/pfi-YOUR-INSTANCE-dev \
  --field direction="test-to-prod" \
  --field bump="minor"
```

The `bump` field is required for prod promotions:
- `patch` — bug fixes, minor ontology corrections
- `minor` — new ontologies added, schema updates
- `major` — breaking changes, major restructuring

### What's different about prod promotion

- PR is labelled `needs-sme-approval` — must be reviewed by a subject matter expert
- The PR body includes a requirements checklist
- This is the **last gate** before content reaches production

---

## Quick Reference: The Commands

```bash
# ── See what's on your dev repo ──
gh api repos/ajrmooreuk/pfi-YOUR-INSTANCE-dev/contents/pfc-core/pfc-version \
  --jq '.content' | base64 -d

# ── Check for open PFC release PRs ──
gh pr list --repo ajrmooreuk/pfi-YOUR-INSTANCE-dev \
  --label pfc-core-release

# ── Promote dev → test ──
gh workflow run promote.yml \
  --repo ajrmooreuk/pfi-YOUR-INSTANCE-dev \
  --field direction="dev-to-test"

# ── Promote test → prod ──
gh workflow run promote.yml \
  --repo ajrmooreuk/pfi-YOUR-INSTANCE-dev \
  --field direction="test-to-prod" \
  --field bump="minor"

# ── Watch a running workflow ──
gh run list --repo ajrmooreuk/pfi-YOUR-INSTANCE-dev --limit 5
gh run watch <RUN_ID> --repo ajrmooreuk/pfi-YOUR-INSTANCE-dev
```

Replace `YOUR-INSTANCE` with your PFI slug:
- `baiv-aiv` (BAIV)
- `airl-caf-aza` (AIRL)
- `w4m-wwg` (W4M-WWG)
- `w4m-eoms` (W4M-EOMS)
- `vhf-nutrition-app` (VHF)

---

## What you receive vs. what others receive

Each PFI has a **series subscription** that controls what content flows to you:

| PFI | Series Subscribed | Approx. Ontologies |
|-----|-------------------|-------------------|
| **BAIV** | VE, PE, RCSG, Foundation, Orchestration | 45 (everything) |
| **AIRL** | RCSG, VE, Foundation | 33 |
| **W4M-WWG** | VE, PE, RCSG, Foundation, Orchestration | 45 |
| **W4M-EOMS** | VE, PE, RCSG, Foundation, Orchestration | 45 |
| **VHF** | VE, Foundation, Orchestration | ~22 |

This is defined in `ont-registry-index.json` under your PFI's `hubSpokeConfig.ontologySeries`. If your team needs access to an additional series, raise it with the PF-Core team — it's a one-line config change.

---

## Rules of the road

1. **Never edit `pfc-core/` directly** — it's overwritten by the release pipeline
2. **Your instance data lives in `instance-data/`** — this is your space to own
3. **Promotions always flow forward** — dev → test → prod, never backwards
4. **Prod requires SME approval** — the `needs-sme-approval` label is enforced
5. **Version pins exist** — if your PFI needs to stay on a specific PF-Core version, set `pfcPin` in the registry (default is `"latest"` = always receives new releases)

---

## Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| Promotion workflow fails | `PROMOTION_PAT` secret missing or expired | Ask PF-Core team to regenerate the PAT |
| PR shows "no changes" | Dev and test already in sync | Nothing to do — this is expected |
| Registry has fewer ontologies than expected | Series subscription doesn't include what you need | Request series addition in `ont-registry-index.json` |
| PR not appearing on test/prod repo | Workflow may still be running | Check `gh run list` on your dev repo |

---

## Live Proof: Full Triad Walkthrough (2026-02-20)

The following was executed live against the BAIV triad to prove the end-to-end flow.

### 1. PF-Core Release landed on dev

Two PRs created automatically by `pfc-release.yml`:

| PFI  | PR                                                                                      | Ontologies | Status |
|------|------------------------------------------------------------------------------------------|------------|--------|
| BAIV | [pfi-baiv-aiv-dev #38](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/38)          | 45         | Merged |
| AIRL | [pfi-airl-caf-aza-dev #39](https://github.com/ajrmooreuk/pfi-airl-caf-aza-dev/pull/39)  | 33         | Merged |

### 2. PFI team creates their own work (instance-data/)

Epic and story created on the BAIV dev repo — this is PFI-team-owned content:

- **Epic 3:** [BAIV Q1 MarTech Campaign — VP/RRR Instance Data Seeding](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/issues/39)
- **S3.1.1:** [Seed VP instance data for AI Visibility MarTech segment](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/issues/40)

The team created a branch, added a VP instance data file to `instance-data/ontologies/`, and opened a PR:

- **PR:** [pfi-baiv-aiv-dev #41](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/41) — CI passed, merged

The file `vp-baiv-martech-q1-2026.json` contains 3 Problems, 3 Solutions, 3 Benefits — all aligned to RRR-ONT via join pattern JP-VP-RRR-001. This is exactly the kind of work PFI teams do: **instance-specific data in `instance-data/`, while `pfc-core/` stays untouched**.

### 3. pfc-core/ is protected — proof

A test PR was created that modified `pfc-core/pfc-version`:

- **PR:** [pfi-baiv-aiv-dev #42](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/42) — **CI FAILED**

The `guard-core.yml` workflow blocked it:

```text
❌ BLOCKED: pfc-core/ is read-only in PFI repos.

Core content (ontologies, registry, visualiser) can only be
modified in Azlan-EA-AAA and distributed via pfc-release.

If you need to update core content:
  1. Make the change in Azlan-EA-AAA
  2. Tag a pfc-vN.N.N release
  3. The sync workflow will create a PR here automatically
```

The PR was closed without merging. **Only `github-actions[bot]` can modify `pfc-core/`.**

Three layers of protection enforce this:

1. **`guard-core.yml`** — CI check that rejects non-bot PRs touching `pfc-core/`
2. **`CODEOWNERS`** — requires `@ajrmooreuk` approval for `pfc-core/`
3. **Private repos** — PFI repos are private, limiting access

### 4. Promoted dev → test

```bash
gh workflow run promote.yml \
  --repo ajrmooreuk/pfi-baiv-aiv-dev \
  --field direction="dev-to-test"
```

Result: [pfi-baiv-aiv-test #2](https://github.com/ajrmooreuk/pfi-baiv-aiv-test/pull/2) — created automatically

The promotion PR contained **both**:

- `instance-data/ontologies/vp-baiv-martech-q1-2026.json` (team's VP data)
- `pfc-core/ontology-registry.json` (updated registry from PF-Core release)

This proves: PFI-team work and PF-Core content travel together through the triad, but only PF-Core automation can write to `pfc-core/`.

The test repo requires **1 approving review** before merge (branch protection). In production use, a team member reviews and approves.

### 5. Promoted test → prod

```bash
gh workflow run promote.yml \
  --repo ajrmooreuk/pfi-baiv-aiv-dev \
  --field direction="test-to-prod" \
  --field bump="minor"
```

Result: [pfi-baiv-aiv-prod #2](https://github.com/ajrmooreuk/pfi-baiv-aiv-prod/pull/2) — created with `needs-sme-approval` label

The prod PR includes all the same content. The `needs-sme-approval` label signals that an SME must review before merge. This is the final gate before production.

### Summary: What flowed through the triad

```text
Azlan-EA-AAA (hub)
  │
  │ pfc-release.yml (filtered 46→45 ontologies for BAIV)
  ▼
pfi-baiv-aiv-dev ← PFI team added VP instance data here
  │                  (pfc-core/ protected by guard-core.yml)
  │ promote.yml dev-to-test
  ▼
pfi-baiv-aiv-test ← Review gate (1 approving review required)
  │
  │ promote.yml test-to-prod
  ▼
pfi-baiv-aiv-prod ← SME approval gate (needs-sme-approval label)
```

| Artifact                               | Origin      | Flows through                                    |
|----------------------------------------|-------------|--------------------------------------------------|
| `pfc-core/ontology-registry.json`      | PF-Core hub | Hub → dev → test → prod (automated, protected)   |
| `instance-data/ontologies/vp-*.json`   | PFI team    | dev → test → prod (team-owned)                   |
| `pfc-core/pfc-version`                 | PF-Core hub | Hub → dev → test → prod (automated, protected)   |

---

## Next: Unified Registry (coming soon)

The current model pushes **filtered copies** of the ontology registry to each PFI. The next evolution is a **unified registry** where PFI environments query a shared Supabase registry by subscription — no copies, always current. The promotion pipeline will shift from "copy files" to "promote subscription config + instance overrides".

Your series subscriptions and the dev → test → prod flow will stay the same. Only the plumbing underneath changes.
