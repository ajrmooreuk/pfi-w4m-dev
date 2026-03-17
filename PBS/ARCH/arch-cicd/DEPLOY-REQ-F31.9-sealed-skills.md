# Deployment & Configuration Requirements: F31.9 — Sealed Skill Distribution

**Date:** 2026-03-01
**Feature:** F31.9 (#819)
**Epic:** Epic 31 (#394)

---

## Pre-Deployment Checklist

### Azlan-EA-AAA (Hub)

- [x] `.github/workflows/guard-core.yml` committed
- [x] `azlan-github-workflow/sealed-skills-manifest.json` committed
- [x] `pfc-core/.claude-plugin/plugin.json` committed
- [x] `.github/workflows/pfc-release.yml` updated with sealed-skills component
- [ ] Commit all F31.9 files to `main` branch
- [ ] Tag first PFC release (`pfc-v1.0.0`) to trigger distribution

### AZLAN-CI-CD (Template)

- [x] `.github/workflows/guard-core.yml` pushed (commit `a95b3c1`)
- [x] `pfc-core/.claude-plugin/plugin.json` pushed (commit `a95b3c1`)
- No further action required — bootstrap-triad.sh downloads from here.

### PFI Triad Repos (Downstream)

No manual deployment needed. Sealed skills arrive automatically via:
1. **New triads:** `bootstrap-triad.sh` creates scaffolding on day 0
2. **Existing triads:** `pfc-release.yml` distributes on next `pfc-v*` tag

---

## Configuration Requirements

### GitHub Actions Secrets

No new secrets required. The existing `PROMOTION_PAT` (with `repo` + `workflow` scopes) is sufficient for `pfc-release.yml` to push sealed skills to PFI repos.

### Branch Protection

PFI triad repos should have `guard-core.yml` as a **required status check** on the `main` branch to enforce sealed skill protection:

```bash
# Add guard-core as required check (per PFI dev repo)
gh api repos/{org}/pfi-{name}-dev/branches/main/protection -X PUT \
  --input - <<'JSON'
{
  "required_status_checks": {
    "strict": true,
    "contexts": ["guard"]
  },
  "required_pull_request_reviews": {
    "required_approving_review_count": 0
  },
  "enforce_admins": false,
  "restrictions": null,
  "allow_force_pushes": false
}
JSON
```

> **Note:** This is applied automatically by `bootstrap-triad.sh` for new triads. For existing triads, apply manually after the first `pfc-release.yml` run delivers `guard-core.yml`.

### Claude Code Configuration

PFI developers must launch Claude Code with dual plugin directories:

```bash
claude --plugin-dir ./azlan-github-workflow --plugin-dir ./pfc-core
```

- `./azlan-github-workflow` — editable instance skills (convention-synced)
- `./pfc-core` — sealed core skills (protected, read-only)

---

## Rollback Plan

If sealed skill distribution causes issues:

1. **Disable distribution:** Run `pfc-release.yml` with `components: ontology-registry` (skips sealed skills)
2. **Remove from a PFI repo:** Delete `pfc-core/skills/` contents (guard-core allows bot-authored PRs)
3. **Revert manifest:** Remove the skill entry from `sealed-skills-manifest.json` and re-tag

The `guard-core.yml` workflow and `plugin.json` are inert without SKILL.md files — they can remain in place safely.

---

## Dependencies

| Dependency | Status | Required for |
|-----------|--------|-------------|
| `pfc-release.yml` updated | Done | Sealed skill distribution |
| `bootstrap-triad.sh` updated | Done | New PFI triad provisioning |
| AZLAN-CI-CD template files | Done (commit `a95b3c1`) | Bootstrap downloads |
| First `pfc-v*` release tag | Pending (S31.1.3) | Triggering distribution to live repos |
| First PFI triad bootstrap | Pending (S31.4.4) | End-to-end validation |
