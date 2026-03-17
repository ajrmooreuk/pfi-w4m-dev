# ARCH-CICD-004: Operating Guide — Hub-and-Spoke CI/CD Platform

**Version:** 1.3.1
**Date:** 2026-03-17 (cross-reference to ARCH-CICD-005 added)
**Status:** Draft (updated with PAT governance & drift detection — F61.5)
**Parent:** ARCH-CICD-001 (Hub-and-Spoke Proposal), ARCH-CICD-002 (Promotion Pipeline Detail)
**Epic:** Epic 61 (#947) → Epic 31 (#394) → S31.6.1 (#431)
**See also:** ARCH-CICD-005 (`05-github-pm-automation.md`) — GitHub Projects governance, instance hierarchy, auto-add routing, project creation rule

---

## 1. Overview

This guide covers the day-to-day operations of the PFC-Core hub-and-spoke CI/CD platform. It assumes the architecture described in ARCH-CICD-001/002 is deployed and the glossary in ARCH-CICD-003 is understood.

### Platform Components

| Component | Repository | Purpose |
|-----------|-----------|---------|
| PFC-Core Forge | `ajrmooreuk/Azlan-EA-AAA` | Ontologies, visualiser, agents, design system |
| Convention Dev | `ajrmooreuk/azlan-workflow-dev` | Convention development and testing |
| Convention Test | `ajrmooreuk/azlan-workflow-test` | SME validation of conventions |
| Convention Prod | `ajrmooreuk/azlan-workflow-prod` | Source of truth for all conventions |
| PFI Instance (per client) | `{org}/pfi-{name}-{dev\|test\|prod}` | Instance-specific delivery repos |

---

## 2. PFC-Core Release

### When to Release

Create a new PFC release when:
- Ontologies are added, updated, or restructured
- Visualiser receives new features or bug fixes
- Agent prompts are updated (OAA version bump)
- Design system tokens change

### How to Release

```bash
# 1. Ensure main is clean and up to date
cd Azlan-EA-AAA
git checkout main
git pull origin main

# 2. Verify ontology registry is current
npx vitest run --dir PBS/TOOLS/ontology-visualiser

# 3. Tag the release (triggers pfc-release.yml)
git tag -a pfc-v1.1.0 -m "PFC Release v1.1.0: <summary of changes>"
git push origin pfc-v1.1.0

# 4. Verify release
gh release view pfc-v1.1.0 --repo ajrmooreuk/Azlan-EA-AAA
```

### Release Versioning

| Change Type | Bump | Example |
|-------------|------|---------|
| Breaking ontology schema change | Major | `pfc-v1.0.0` → `pfc-v2.0.0` |
| New ontology, new visualiser feature | Minor | `pfc-v1.0.0` → `pfc-v1.1.0` |
| Bug fix, typo correction, metadata update | Patch | `pfc-v1.0.0` → `pfc-v1.0.1` |

### Post-Release

After tagging, the `pfc-release.yml` workflow:
1. Packages the release archive (`pfc-vN.N.N.tar.gz`)
2. Creates a GitHub Release with the archive attached
3. Deploys updated ontology library to GitHub Pages
4. Distributes sealed skills to PFI dev repos (reads `sealed-skills-manifest.json`, copies SKILL.md files into `pfc-core/skills/`, pushes `guard-core.yml` and `plugin.json`)
5. Triggers `sync-to-live.yml` in the convention prod repo (if configured)

---

## 3. Convention Promotion

### Promotion Flow: dev → test → prod

Conventions are managed in three separate repos for full isolation. Changes flow one direction: dev → test → prod.

### Step 1: Develop in Dev

```bash
# Clone or update convention dev repo
cd azlan-workflow-dev
git checkout main
git pull origin main

# Make changes (issue templates, workflows, labels, scripts, Claude plugin)
# Edit files listed in convention-manifest.json

# Commit and push
git add <files>
git commit -m "feat: add new story issue template field"
git push origin main
```

### Step 2: Promote Dev to Test

```bash
# From the dev repo, trigger promotion
gh workflow run promote.yml \
  --repo ajrmooreuk/azlan-workflow-dev \
  -f direction=dev-to-test \
  -f bump=minor

# This creates a PR in azlan-workflow-test
# Review the PR — verify changes are correct
gh pr list --repo ajrmooreuk/azlan-workflow-test
gh pr view <PR_NUMBER> --repo ajrmooreuk/azlan-workflow-test

# Merge after review
gh pr merge <PR_NUMBER> --repo ajrmooreuk/azlan-workflow-test --merge
```

### Step 3: Promote Test to Prod (Requires SME Approval)

```bash
# From the test repo, trigger promotion
gh workflow run promote.yml \
  --repo ajrmooreuk/azlan-workflow-test \
  -f direction=test-to-prod \
  -f bump=minor

# This creates a PR in azlan-workflow-prod with SME approval checklist
# SME must review and approve before merge
gh pr view <PR_NUMBER> --repo ajrmooreuk/azlan-workflow-prod

# After SME approval, merge
gh pr merge <PR_NUMBER> --repo ajrmooreuk/azlan-workflow-prod --merge
```

### Step 4: Auto-Tag and Sync

On merge to prod main:
1. `auto-tag.yml` calculates the next semver and creates a git tag + GitHub Release
2. `sync-to-live.yml` triggers and distributes conventions to all registered live repos

---

## 4. Live Repo Sync

### How Sync Works

When a new tag is pushed to convention prod, `sync-to-live.yml`:
1. Reads `live-repos.json` for the list of registered PFI repos
2. For each repo, reads `pfc-core/azlan-workflow-version`
3. If pin matches the tag (or pin is `latest`), creates a sync PR
4. If pin doesn't match, skips the repo (logged)

### Checking Sync Status

```bash
# List recent sync PRs across all live repos
gh pr list --repo clientorg/pfi-baiv-prod --label convention-sync
gh pr list --repo clientorg/pfi-az-air-prod --label convention-sync
gh pr list --repo clientorg/pfi-ea-services-prod --label convention-sync
```

### Manual Sync (Emergency)

If automatic sync fails or you need to force a sync:

```bash
# Trigger sync manually
gh workflow run sync-to-live.yml \
  --repo ajrmooreuk/azlan-workflow-prod \
  -f tag=v1.2.0

# Or manually copy convention files
# 1. Clone prod and target repo
# 2. Copy files listed in convention-manifest.json
# 3. Create PR in target repo
```

---

## 5. Version Pinning

### Checking Current Pin

```bash
# Read a PFI repo's current version pin
gh api repos/clientorg/pfi-baiv-prod/contents/pfc-core/azlan-workflow-version \
  --jq '.content' | base64 -d
```

### Updating a Pin

Version pins are updated manually by an admin:

```bash
# Clone the PFI prod repo
cd pfi-baiv-prod
git checkout main
git pull origin main

# Update the pin file
echo "pfc-v1.1.0" > pfc-core/azlan-workflow-version

# Or set to latest for auto-sync
echo "latest" > pfc-core/azlan-workflow-version

# Commit and push
git add pfc-core/azlan-workflow-version
git commit -m "chore: update PFC pin to v1.1.0"
git push origin main
```

### Pin States

| Pin Value | Behaviour |
|-----------|-----------|
| `pfc-v1.0.0` | Only accepts sync when incoming tag is `pfc-v1.0.0` — effectively frozen |
| `latest` | Accepts every sync — always up to date with prod |
| `pfc-v1.*` | (Future) Accepts any v1.x patch/minor — blocks major bumps |

---

## 6. Bootstrapping a New PFI Instance

### Prerequisites

- `gh` CLI authenticated with `PROMOTION_PAT` scopes (repo + workflow)
- Target GitHub org/account exists
- Supabase account with free-tier project slots available

### Bootstrap Command

```bash
# Bootstrap a new PFI triad
./bootstrap-new-repo.sh \
  --name baiv-aiv \
  --org clientorg \
  --pin pfc-v1.0.0 \
  --license pfc-standard \
  --ontologies "VE-Series,PE-Series,Foundation"
```

This creates:
1. `clientorg/pfi-baiv-aiv-dev` — developer workspace
2. `clientorg/pfi-baiv-aiv-test` — SME validation
3. `clientorg/pfi-baiv-aiv-prod` — production instance

Each repo gets:
- Convention files from `convention-manifest.json`
- Instance template structure (`instance-data/`, `supabase/`, `pfc-core/`, `tools/`, `docs/`)
- Sealed skill scaffolding (`pfc-core/.claude-plugin/plugin.json`, `pfc-core/skills/.gitkeep`)
- `guard-core.yml` CI gate (blocks human edits to `pfc-core/`)
- Labels and issue templates from setup scripts
- Branch protection per tier
- `pfc-core/azlan-workflow-version` set to the specified pin

> **Note:** Bootstrap creates the sealed skill *scaffolding* only. Actual SKILL.md content arrives on the first `pfc-release.yml` run (day 1+). See Section 11 for details.

### Post-Bootstrap

```bash
# 1. Add the new instance to live-repos.json in convention prod
# Edit live-repos.json to add the new entry

# 2. Seed instance data
cd pfi-baiv-aiv-dev
mkdir -p instance-data/ontologies instance-data/tokens instance-data/config
# Add VP instances, brand tokens, config files

# 3. First promotion test
gh workflow run promote.yml \
  --repo clientorg/pfi-baiv-aiv-dev \
  -f direction=dev-to-test \
  -f bump=minor

# 4. Configure Supabase
# Create new Supabase project for this instance
# Run migrations: supabase db push
# Add secrets to repo: SUPABASE_URL, SUPABASE_ANON_KEY, SUPABASE_SERVICE_KEY

# 5. Trigger first PFC release to deliver sealed skills
# (From Azlan-EA-AAA — tag a pfc-v* release if not already done)
```

---

## 7. PROMOTION_PAT Setup

### Creating the PAT

1. Go to **https://github.com/settings/tokens/new** (Classic token)
2. Set **Note:** `PROMOTION_PAT`
3. Set **Expiration:** 90 days
4. Select scopes: **`repo`** (full) + **`workflow`**
5. Click **Generate token** and copy the `ghp_...` value

> **Important:** Must be a Classic PAT (`ghp_` prefix). OAuth tokens (`gho_`) from `gh auth` do not work for cross-repo checkout in GitHub Actions.

### Setting the Secret on All PFI Repos (Automated)

Use the registry-driven rotation script to set `PROMOTION_PAT` across all PFI dev+test repos in one command. The script reads repos directly from `ont-registry-index.json` — new PFIs added to the registry are automatically included.

```bash
# Set PROMOTION_PAT on all registered PFI dev+test repos + hub
echo "ghp_YOUR_TOKEN" | .github/scripts/set-pat-all-triads.sh
```

The script:

- Reads PFI triad repos from `ont-registry-index.json` → `pfiInstances[].hubSpokeConfig.repos`
- Sets `PROMOTION_PAT` on **dev + test only** (prod excluded by design)
- Skips repos that don't exist yet (e.g. planned PFIs)
- Also updates the hub repo (`Azlan-EA-AAA`)

> **When to run:** After creating a new PAT, after adding a new PFI to the registry, or during quarterly PAT rotation.

### Setting the Secret Manually (Single Repo)

```bash
# Set on a single repo
gh secret set PROMOTION_PAT --repo "ajrmooreuk/pfi-baiv-aiv-dev"
# Paste the ghp_ token when prompted
```

### Verifying

```bash
# Should show PROMOTION_PAT with a timestamp
gh secret list --repo ajrmooreuk/pfi-baiv-aiv-dev
```

### PAT Drift Detection

The `pat-drift-detection.yml` workflow automatically validates that `PROMOTION_PAT` is set on every registered PFI dev+test repo. It runs:

- **Weekly:** Every Monday at 08:00 UTC
- **On registry change:** When `ont-registry-index.json` is pushed to main
- **Manual:** Via workflow dispatch

If any repo is missing the secret, the workflow fails with an actionable error pointing to `set-pat-all-triads.sh`.

```bash
# Trigger manually
gh workflow run pat-drift-detection.yml --repo ajrmooreuk/Azlan-EA-AAA

# Check results
gh run list --workflow pat-drift-detection.yml --repo ajrmooreuk/Azlan-EA-AAA --limit 1
```

---

## 8. Multi-Dev Review Process

### Branch Protection Tiers

| Tier | Mode | PRs Required | Reviews | Force Push | Use Case |
|------|------|:------------:|:-------:|:----------:|----------|
| **Dev** | Multi-dev | Yes | 0 (self-merge) | Blocked | 1-4 devs, PR workflow for visibility |
| **Test** | Team | Yes | 1 | Blocked | Pre-release validation, SME sign-off |
| **Prod** | Team | Yes | 1 + enforce admins | Blocked | Production, admin-only merge |

### Developer Workflow (Dev Repo)

```bash
# 1. Clone the PFI dev repo
git clone https://github.com/ajrmooreuk/pfi-baiv-aiv-dev.git
cd pfi-baiv-aiv-dev

# 2. Create a feature branch
git checkout -b feat/vsom-cascade

# 3. Make changes (instance data, configs, strategy docs)
# Edit instance-data/config/graph-scope.json
# Edit instance-data/strategy/baiv-vsom.md

# 4. Commit and push branch
git add instance-data/
git commit -m "feat: add BAIV graph scope config and VSOM cascade"
git push origin feat/vsom-cascade

# 5. Create PR (self-merge OK for solo dev, request review for team)
gh pr create --title "feat: BAIV graph scope + VSOM cascade" \
  --body "Closes S2.2.1, S2.1.1 from Epic 2"

# 6. Self-merge (0 reviews required) or wait for team review
gh pr merge --merge
```

### When to Bump Reviews from 0 to 1

Upgrade when a **second dev** joins the instance:

```bash
# Bump BAIV-dev from 0 to 1 required review
gh api repos/ajrmooreuk/pfi-baiv-aiv-dev/branches/main/protection -X PUT \
  --input - <<'JSON'
{
  "required_pull_request_reviews": {
    "required_approving_review_count": 1,
    "dismiss_stale_reviews": true
  },
  "enforce_admins": false,
  "restrictions": null,
  "required_status_checks": null,
  "allow_force_pushes": false
}
JSON
```

### Review Expectations by Tier

| Tier | Who Reviews | What to Check |
|------|-------------|---------------|
| **Dev** (self-merge) | Author or peer | PR description, commit message, no secrets committed |
| **Dev** (1 review) | Any team member | Code quality, ontology alignment, VP-RRR consistency |
| **Test** (1 review) | SME / tech lead | Functional correctness, regression check, promotion readiness |
| **Prod** (1 review + admin) | Instance owner | Release approval, customer impact assessment |

---

## 9. Instance Promotion (PFI dev → test → prod)

Instance repos follow the same promotion model as conventions. The `promote.yml` workflow copies instance files from one tier to the next and creates a PR.

### What Gets Promoted

Files defined in `promotion/instance-manifest.json`:
- `instance-data/` (config, ontologies, tokens, strategy)
- `pfc-core/` (registry snapshot, version pin)
- `supabase/` (migrations)
- `tools/` (custom tools)
- `promotion/` (manifest, env)

### Step-by-Step: Dev → Test

```bash
# 1. Ensure dev main is up to date with all changes merged
gh pr list --repo ajrmooreuk/pfi-baiv-aiv-dev
# All PRs should be merged or closed

# 2. Trigger promotion
gh workflow run "Promote Instance Files" \
  --repo ajrmooreuk/pfi-baiv-aiv-dev \
  -f direction=dev-to-test \
  -f bump=minor

# 3. Wait ~30s, then check workflow status
gh run list --repo ajrmooreuk/pfi-baiv-aiv-dev \
  --workflow "Promote Instance Files" --limit 1

# 4. Review and merge the PR in the test repo
gh pr list --repo ajrmooreuk/pfi-baiv-aiv-test
gh pr view <PR_NUMBER> --repo ajrmooreuk/pfi-baiv-aiv-test
gh pr merge <PR_NUMBER> --repo ajrmooreuk/pfi-baiv-aiv-test --merge
```

### Step-by-Step: Test → Prod (Requires SME Approval)

```bash
# 1. Trigger promotion from test
gh workflow run "Promote Instance Files" \
  --repo ajrmooreuk/pfi-baiv-aiv-test \
  -f direction=test-to-prod \
  -f bump=minor

# 2. PR created in prod repo with 'needs-sme-approval' label
gh pr list --repo ajrmooreuk/pfi-baiv-aiv-prod

# 3. SME reviews and approves, then merge
gh pr merge <PR_NUMBER> --repo ajrmooreuk/pfi-baiv-aiv-prod --merge
```

### Promotion Checklist

Before promoting dev → test:
- [ ] All PRs in dev merged
- [ ] Instance data validates (ontology snapshots load in visualiser)
- [ ] VP-RRR alignment checked
- [ ] No secrets in committed files

Before promoting test → prod:
- [ ] Integration testing passed in test environment
- [ ] SME sign-off on strategy content
- [ ] Version bump type agreed (major/minor/patch)

---

## 10. Core Content Protection

### Overview

PFI instance repos contain `pfc-core/` — core platform content that is **read-only**. Three protection layers prevent unauthorized modification or download.

### Layer 1: Private Repos

All PFI triad repos (dev/test/prod) MUST be private. This prevents anonymous access to core IP (ontologies, registry, version pins).

```bash
# Verify visibility
gh repo view ajrmooreuk/pfi-baiv-aiv-dev --json visibility --jq '.visibility'
# Should output: PRIVATE

# Fix if PUBLIC
gh repo edit ajrmooreuk/pfi-baiv-aiv-dev \
  --visibility private --accept-visibility-change-consequences
```

### Layer 2: guard-core.yml (CI Guard)

The `guard-core.yml` workflow blocks any human-authored PR that modifies `pfc-core/**`. Only `github-actions[bot]` (automated sync/promotion) can modify core content.

```bash
# Verify guard workflow exists
gh api repos/ajrmooreuk/pfi-baiv-aiv-dev/contents/.github/workflows/guard-core.yml \
  --jq '.name'
# Should output: guard-core.yml
```

If a developer needs to update core content, they must:
1. Make the change in `Azlan-EA-AAA` (the source of truth)
2. Tag a `pfc-vN.N.N` release
3. The PFC-Core release workflow creates an automated PR in the PFI repo

### Layer 3: CODEOWNERS

The `CODEOWNERS` file requires `@ajrmooreuk` review for any change to `/pfc-core/`. Branch protection must have "Require review from Code Owners" enabled.

```bash
# Verify CODEOWNERS review is enabled
gh api repos/ajrmooreuk/pfi-baiv-aiv-dev/branches/main/protection/required_pull_request_reviews \
  --jq '.require_code_owner_reviews'
# Should output: true
```

### Deploying Protection to a New PFI Triad

When bootstrapping a new PFI instance, apply all 3 layers:

```bash
ORG="ajrmooreuk"
NAME="new-instance"

for TIER in dev test prod; do
  REPO="$ORG/pfi-$NAME-$TIER"

  # Layer 1: Private
  gh repo edit "$REPO" --visibility private --accept-visibility-change-consequences

  # Layer 2: guard-core.yml (push directly to dev, PR to test/prod)
  # Use the template from pfi-baiv-aiv-dev/.github/workflows/guard-core.yml

  # Layer 3: CODEOWNERS
  # Push CODEOWNERS with: /pfc-core/ @ajrmooreuk

  # Enable CODEOWNERS review
  gh api "repos/$REPO/branches/main/protection/required_pull_request_reviews" \
    -X PATCH -F require_code_owner_reviews=true
done
```

### Verification Script

```bash
# Audit all PFI repos for core protection
for REPO in \
  ajrmooreuk/pfi-baiv-aiv-{dev,test,prod} \
  ajrmooreuk/pfi-airl-caf-aza-{dev,test,prod}; do
  VIS=$(gh repo view "$REPO" --json visibility --jq '.visibility')
  GUARD=$(gh api "repos/$REPO/contents/.github/workflows/guard-core.yml" --jq '.name' 2>/dev/null || echo "MISSING")
  OWNERS=$(gh api "repos/$REPO/contents/CODEOWNERS" --jq '.name' 2>/dev/null || echo "MISSING")
  CO_REQ=$(gh api "repos/$REPO/branches/main/protection/required_pull_request_reviews" --jq '.require_code_owner_reviews' 2>/dev/null || echo "false")
  printf "%-40s vis:%-7s guard:%-14s owners:%-10s co-review:%s\n" "$REPO" "$VIS" "$GUARD" "$OWNERS" "$CO_REQ"
done
```

---

## 11. Sealed Skill Distribution

### Overview

Sealed skills are Claude Code skills that are **executable but not editable** in PFI triad repos. They are authored in `Azlan-EA-AAA` (the hub) and distributed one-way to PFI dev repos via `pfc-release.yml`.

**Two-phase delivery:**

| Phase | Source | What arrives |
|-------|--------|-------------|
| Bootstrap (day 0) | `AZLAN-CI-CD` via `bootstrap-triad.sh` | `guard-core.yml`, `plugin.json`, `pfc-core/skills/.gitkeep` (scaffolding) |
| First PFC release (day 1+) | `Azlan-EA-AAA` via `pfc-release.yml` | Sealed SKILL.md files, `sealed-skills-manifest.json` |

### Launching Claude Code with Sealed Skills

PFI dev repos have two plugin directories — editable and sealed:

```bash
# In a PFI dev repo
claude --plugin-dir ./azlan-github-workflow --plugin-dir ./pfc-core
```

- `./azlan-github-workflow` — editable instance skills (convention-synced)
- `./pfc-core` — sealed core skills (protected by `guard-core.yml`)

### Adding a New Sealed Skill

1. Create the SKILL.md in `Azlan-EA-AAA`:

```bash
cd Azlan-EA-AAA
mkdir -p azlan-github-workflow/skills/{new-skill-name}
# Write the SKILL.md
```

2. Register in the manifest:

```bash
# Edit azlan-github-workflow/sealed-skills-manifest.json
# Add entry to sealedSkills array:
{
  "name": "new-skill-name",
  "sourcePath": "azlan-github-workflow/skills/new-skill-name/SKILL.md",
  "targetPath": "pfc-core/skills/new-skill-name/SKILL.md",
  "sealed": true,
  "scope": "core",
  "description": "What this skill does"
}
```

3. Tag a new PFC release:

```bash
git tag -a pfc-v1.2.0 -m "PFC Release v1.2.0: add new-skill-name sealed skill"
git push origin pfc-v1.2.0
# pfc-release.yml distributes automatically to all registered PFI repos
```

### Verifying Sealed Skills in a PFI Repo

```bash
# Check guard-core.yml is present
gh api repos/{org}/pfi-{name}-dev/contents/.github/workflows/guard-core.yml \
  --jq '.name'
# Should output: guard-core.yml

# Check plugin.json exists
gh api repos/{org}/pfi-{name}-dev/contents/pfc-core/.claude-plugin/plugin.json \
  --jq '.name'
# Should output: plugin.json

# Check sealed skills manifest
gh api repos/{org}/pfi-{name}-dev/contents/pfc-core/sealed-skills-manifest.json \
  --jq '.content' | base64 -d | jq '.sealedSkills[].name'
# Should list all sealed skill names

# Check actual SKILL.md files
gh api repos/{org}/pfi-{name}-dev/contents/pfc-core/skills/close-out/SKILL.md \
  --jq '.name'
# Should output: SKILL.md
```

### What Happens if a Developer Edits pfc-core/

The `guard-core.yml` CI gate will **fail the PR**. Only `github-actions[bot]` (via `pfc-release.yml`) or PRs with the `pfc-core-release` label can modify `pfc-core/`.

```
Error: pfc-core/ is sealed. Changes to core content must be made in
Azlan-EA-AAA and distributed via pfc-release.yml.
```

---

## 12. Drift Detection

### Scheduled Runs

Two drift-detection workflows run weekly:

**Convention drift** — `drift-detection.yml` runs every Monday at 9am UTC. It:
1. Clones each registered live repo
2. Diffs convention files against prod source of truth
3. Creates/updates GitHub issues labelled `drift-detection` if drift found

### Manual Run

```bash
# Trigger drift detection manually
gh workflow run drift-detection.yml \
  --repo ajrmooreuk/azlan-workflow-prod

# Check results
gh issue list --repo clientorg/pfi-baiv-prod --label drift-detection
```

**PAT drift** — `pat-drift-detection.yml` runs every Monday at 8am UTC + on registry changes. It:

1. Reads all PFI dev+test repos from `ont-registry-index.json`
2. Checks each repo for `PROMOTION_PAT` secret presence
3. Fails if any registered repo is missing the secret

```bash
# Trigger PAT drift detection manually
gh workflow run pat-drift-detection.yml --repo ajrmooreuk/Azlan-EA-AAA

# Fix: set PAT on all repos
echo "ghp_..." | .github/scripts/set-pat-all-triads.sh
```

### Resolving Drift

| Scenario | Action |
|----------|--------|
| Accidental edit of convention file | Re-sync via `sync-to-live.yml` manual trigger |
| Intentional local override | Document exception in issue, add to `.convention-overrides` |
| Missing convention file | Re-run bootstrap setup scripts on the affected repo |
| Outdated version pin | Update pin to current version, then re-sync |

---

## 13. Monitoring Checklist

### Daily

- [ ] Check for failed GitHub Actions runs across convention repos
- [ ] Review any open PRs from promotion or sync workflows

### Weekly

- [ ] Review convention drift detection results (Monday after 9am UTC)
- [ ] Review PAT drift detection results (Monday after 8am UTC)
- [ ] Check PFI instance promotion queues (PRs waiting for review)

### Monthly

- [ ] Review `live-repos.json` — remove decommissioned instances
- [ ] Audit PROMOTION_PAT scope and expiry
- [ ] Review Supabase usage per instance (approaching free-tier limits?)

### Quarterly

- [ ] Rotate PROMOTION_PAT
- [ ] Review and update convention-manifest.json (new convention files?)
- [ ] Plan next PFC major/minor release

---

## 14. Common Operations Quick Reference

| Operation | Command |
|-----------|---------|
| Tag PFC release | `git tag -a pfc-v1.1.0 -m "msg" && git push origin pfc-v1.1.0` |
| Promote conventions | `gh workflow run promote.yml -f direction=dev-to-test -f bump=minor` |
| Check live repo pin | `cat pfc-core/azlan-workflow-version` |
| Update pin | `echo "pfc-v1.1.0" > pfc-core/azlan-workflow-version` |
| Bootstrap new PFI | `./bootstrap-new-repo.sh --name X --org Y --pin pfc-v1.0.0` |
| Trigger manual sync | `gh workflow run sync-to-live.yml -f tag=v1.2.0` |
| Run convention drift detection | `gh workflow run drift-detection.yml` |
| Run PAT drift detection | `gh workflow run pat-drift-detection.yml` |
| Set PAT on all dev+test repos | `echo "ghp_..." \| .github/scripts/set-pat-all-triads.sh` |
| Check drift issues | `gh issue list --label drift-detection` |
| View PFC release | `gh release view pfc-v1.0.0` |
| List convention PRs | `gh pr list --label convention-sync` |
| Launch Claude with sealed skills | `claude --plugin-dir ./azlan-github-workflow --plugin-dir ./pfc-core` |
| Add sealed skill | Edit `sealed-skills-manifest.json`, tag new `pfc-v*` release |
| Verify sealed skills | `gh api repos/{repo}/contents/pfc-core/sealed-skills-manifest.json` |
| Audit core protection | See Section 10 verification script |
| Make repo private | `gh repo edit {repo} --visibility private --accept-visibility-change-consequences` |
| Check CODEOWNERS review | `gh api repos/{repo}/branches/main/protection/required_pull_request_reviews --jq '.require_code_owner_reviews'` |

---

## 15. Troubleshooting

### Promotion PR Fails to Create

**Symptom:** `promote.yml` runs but no PR appears in the target repo.
**Cause:** Usually a PAT permission issue.
**Fix:**
1. Check `PROMOTION_PAT` has `repo` and `workflow` scopes
2. Verify PAT has access to the target repo/org
3. Check Actions log for permission errors

### Sync Skips a Repo Unexpectedly

**Symptom:** Live repo doesn't receive sync after prod tag.
**Cause:** Version pin mismatch.
**Fix:**
1. Check the repo's `pfc-core/azlan-workflow-version`
2. If pinned to a specific version, update pin or re-tag with matching version
3. If pin is `latest`, check `live-repos.json` for correct repo name

### Drift Detected but Expected

**Symptom:** Drift issue created for an intentional local override.
**Fix:**
1. Document the override in the drift issue
2. Add the file to `.convention-overrides` in the live repo
3. Drift detection will exclude overridden files in future runs

### Bootstrap Fails Partway

**Symptom:** Only 1 or 2 of the 3 repos created.
**Fix:**
1. Check which repos were created: `gh repo list {org} --pattern "pfi-{name}*"`
2. Manually create missing repos or re-run bootstrap with `--skip-existing`
3. Run setup scripts on any repos that were created but not configured
