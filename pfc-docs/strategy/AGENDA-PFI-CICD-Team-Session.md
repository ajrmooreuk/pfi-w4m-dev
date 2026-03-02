# PFI CI/CD Team Session — Agenda

**Duration:** 60 minutes
**Audience:** PFI instance team leads + developers (BAIV, AIRL, W4M, VHF)
**Pre-read:** [TRAINING-PFI-Release-and-Promotion-Guide.md](TRAINING-PFI-Release-and-Promotion-Guide.md)
**Date:** TBD

---

## Part 1: Concepts (15 min)

### 1.1 Hub-and-spoke architecture (5 min)

- PF-Core (Azlan-EA-AAA) is the **single source of truth** for ontologies, registry, and core platform content
- Each PFI has a **triad**: dev / test / prod — three private repos
- Content flows **one direction only**: Hub → dev → test → prod
- PFI teams own `instance-data/` — PF-Core owns `pfc-core/`

### 1.2 What you receive and what you own (5 min)

| You receive (read-only)              | You own (read-write)                        |
|--------------------------------------|---------------------------------------------|
| `pfc-core/ontology-registry.json`    | `instance-data/ontologies/` (VP, RRR, etc.) |
| `pfc-core/pfc-version`               | `instance-data/config/`                     |
| `pfc-core/` operating docs           | `supabase/` schema and seeds                |
| Core workflows                       | `tools/` instance utilities                 |

- Series subscriptions control what you receive (BAIV = all 5 series, AIRL = 3, VHF = 3)
- `guard-core.yml` + CODEOWNERS enforce the boundary — **demo the live failure from PR #42**

### 1.3 Why this matters before Supabase (5 min)

- Git-native pipeline proves the plumbing **without database dependency**
- Every artifact, every promotion, every approval is tracked in Git history
- When Supabase arrives, the subscription model stays the same — only the storage layer changes
- We need confidence the triad flow works before layering on multiple workstreams

---

## Part 2: Operating Guide Walkthrough (10 min)

### 2.1 PF-Core release lands on your dev repo (3 min)

Walk through the live example:

- Show [BAIV dev PR #38](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/38) (45 ontologies)
- Show [AIRL dev PR #39](https://github.com/ajrmooreuk/pfi-airl-caf-aza-dev/pull/39) (33 ontologies)
- Explain the jq filter: how `ontologySeries` subscription determines what each PFI gets
- **Action for teams:** review the PR, check the registry snapshot, merge

### 2.2 Your daily workflow: instance-data/ (4 min)

Walk through Epic 3 on BAIV dev:

- [Issue #39](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/issues/39) — Epic created
- [Issue #40](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/issues/40) — Story created
- [PR #41](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/41) — VP instance data added, CI passes, merged
- Note: `enforce-registry-link.yml` requires `Registry Artifact:` field in PR body

### 2.3 Promotion commands (3 min)

Live demo of the `gh` commands:

```bash
# Dev → test
gh workflow run promote.yml --repo ajrmooreuk/pfi-YOUR-INSTANCE-dev --field direction="dev-to-test"

# Test → prod
gh workflow run promote.yml --repo ajrmooreuk/pfi-YOUR-INSTANCE-dev --field direction="test-to-prod" --field bump="minor"
```

Show [BAIV test PR #2](https://github.com/ajrmooreuk/pfi-baiv-aiv-test/pull/2) and [BAIV prod PR #2](https://github.com/ajrmooreuk/pfi-baiv-aiv-prod/pull/2) as proof.

---

## Part 3: Training Scenarios (25 min)

These scenarios validate that the pipeline holds under real-world conditions. **Each PFI team runs their scenario in parallel.**

### Scenario A: First release + promotion (each PFI team, 10 min)

**Goal:** Each team proves their own triad works end-to-end.

1. Check your dev repo for the latest PF-Core release PR (should already be there)
2. Review and merge the PF-Core release PR
3. Create an epic on your dev repo: `Epic 1: [PFI-NAME] Initial Instance Data Seeding`
4. Create a story: `S1.1.1: Seed VP instance data for [your segment]`
5. Branch from main, add a VP JSON file to `instance-data/ontologies/`
6. Open PR (include `Registry Artifact:` field), get it reviewed, merge
7. Trigger promotion: `dev-to-test`
8. Verify the PR appears on your test repo

**Success:** PR on test contains both your VP data and pfc-core/ content.

### Scenario B: Prove pfc-core protection (one volunteer team, 5 min)

**Goal:** Confirm that human edits to `pfc-core/` are blocked.

1. Branch from main on your dev repo
2. Edit `pfc-core/pfc-version` (add any text)
3. Open a PR
4. Watch `guard-core.yml` fail the CI check
5. Close the PR without merging

**Success:** CI shows "BLOCKED: pfc-core/ is read-only in PFI repos."

### Scenario C: Concurrent workstreams — no collision (pair exercise, 10 min)

**Goal:** Two team members work on the same PFI dev repo simultaneously without breaking each other.

**Team Member 1:**

1. Create branch `feature/vp-segment-alpha`
2. Add `instance-data/ontologies/vp-alpha.json`
3. Open PR, merge

**Team Member 2 (in parallel):**

1. Create branch `feature/rrr-alignment-beta`
2. Add `instance-data/ontologies/rrr-beta.json`
3. Open PR, merge

**Then together:**

4. Trigger one promotion `dev-to-test`
5. Verify the test PR contains **both** alpha and beta files plus pfc-core/

**Success:** Both workstreams promoted cleanly in a single promotion. No merge conflicts. `pfc-core/` unchanged.

---

## Part 4: Guardrails Checklist + Q&A (10 min)

### 4.1 What to validate before we add more workstreams (5 min)

Run through this checklist with all teams:

| Check                                             | BAIV | AIRL | W4M-WWG | W4M-EOMS | VHF |
|---------------------------------------------------|------|------|---------|----------|-----|
| PF-Core release PR received on dev                |      |      |         |          |     |
| PF-Core release PR merged on dev                  |      |      |         |          |     |
| Instance data PR created and merged on dev        |      |      |         |          |     |
| `pfc-core/` edit blocked by guard-core.yml        |      |      |         |          |     |
| Promotion dev → test succeeded                    |      |      |         |          |     |
| Test PR contains instance-data + pfc-core         |      |      |         |          |     |
| Promotion test → prod PR created with SME label   |      |      |         |          |     |
| `PROMOTION_PAT` secret working                    |      |      |         |          |     |
| Branch protection enforced on test (review req'd) |      |      |         |          |     |
| No unintended files in promotion diff             |      |      |         |          |     |

### 4.2 Known issues to be aware of (2 min)

- **`needs-sme-approval` label** must exist on test/prod repos before first test-to-prod promotion (now created on all repos)
- **Self-approval blocked** — test/prod PRs require a *different* team member to approve (this is by design)
- **promote.yml lives on the dev repo** — both dev-to-test and test-to-prod are triggered from the dev repo
- **Version pin** — if your PFI needs to stay on a specific PF-Core version, set `pfcPin` in the registry to e.g. `"pfc-v1.0.0"` instead of `"latest"`

### 4.3 Open Q&A (3 min)

Likely questions:

- "What happens if PF-Core releases while we have uncommitted work on dev?" → No conflict. The release creates a PR to `pfc-core/` only. Your `instance-data/` is untouched.
- "Can we roll back a promotion?" → Yes, revert the merge commit on test/prod. Or re-promote from a known-good dev state.
- "When does Supabase change this?" → The subscription model stays the same. Supabase replaces the JSON snapshot with a live query. Promotion shifts from "copy files" to "promote config + overrides".

---

## Key artifacts for the session

| Resource | Location |
|----------|----------|
| Training guide | [TRAINING-PFI-Release-and-Promotion-Guide.md](TRAINING-PFI-Release-and-Promotion-Guide.md) |
| Operating guide | Available in each PFI dev repo at `pfc-core/04-operating-guide.md` |
| CICD ontology | [PE-Series/CICD-ONT/](../../ONTOLOGIES/ontology-library/PE-Series/CICD-ONT/) |
| Live proof PRs | BAIV dev [#38](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/38), [#41](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/41), [#42](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/42) |
| Guard failure | BAIV dev [#42](https://github.com/ajrmooreuk/pfi-baiv-aiv-dev/pull/42) (closed, CI failed) |
| Promotion proof | BAIV test [#2](https://github.com/ajrmooreuk/pfi-baiv-aiv-test/pull/2), BAIV prod [#2](https://github.com/ajrmooreuk/pfi-baiv-aiv-prod/pull/2) |
| Registry index | [ont-registry-index.json](../../ONTOLOGIES/ontology-library/ont-registry-index.json) (v9.4.0, 46 ontologies) |

---

## Post-session actions

1. **Each PFI team** completes Scenario A on their own triad within 48 hours
2. **PF-Core team** triggers a fresh release to all 5 PFIs to confirm the full matrix
3. **Update the checklist** above — all boxes should be ticked before adding a second workstream
4. **Next session:** Supabase integration planning — how the unified registry replaces JSON snapshots
