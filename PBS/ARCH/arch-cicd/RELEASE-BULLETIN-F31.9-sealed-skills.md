# Release Bulletin: F31.9 — Sealed Skill Distribution

**Date:** 2026-03-01
**Version:** Epic 31, Feature F31.9
**Issue:** #819 (Closed)
**Epic:** #394

---

## What Changed

F31.9 introduces **sealed skill distribution** — a mechanism for distributing Claude Code skills from PFC-Core (Azlan-EA-AAA) to PFI triad dev repos as executable-but-not-editable artefacts.

### New Files

| File | Repository | Purpose |
|------|-----------|---------|
| `.github/workflows/guard-core.yml` | Azlan-EA-AAA, AZLAN-CI-CD | CI gate blocking human edits to `pfc-core/` |
| `azlan-github-workflow/sealed-skills-manifest.json` | Azlan-EA-AAA | Declares which skills are sealed with source/target paths |
| `pfc-core/.claude-plugin/plugin.json` | Azlan-EA-AAA, AZLAN-CI-CD | Plugin manifest for Claude Code discovery via `--plugin-dir ./pfc-core` |

### Modified Files

| File | Change |
|------|--------|
| `.github/workflows/pfc-release.yml` | Added `sealed-skills` component, manifest reading, skill + guard-core distribution |
| `PBS/ARCHITECTURE/scripts/bootstrap-triad.sh` | Added guard-core.yml to templates, sealed structure scaffolding (plugin.json + .gitkeep) |
| `PBS/ARCHITECTURE/arch-cicd/01-hub-and-spoke-proposal.md` | Added section 4.5 (Sealed Skill Distribution), updated section 4.4 repo structure |
| `PBS/ARCHITECTURE/arch-cicd/04-operating-guide.md` | Added section 11 (Sealed Skill Distribution), fixed section numbering, updated sections 2 and 6 |

### AZLAN-CI-CD Updates

Commit `a95b3c1` pushed `guard-core.yml` and `pfc-core/.claude-plugin/plugin.json` to `ajrmooreuk/AZLAN-CI-CD` via GitHub API.

---

## How It Works

**Two-phase delivery model:**

1. **Bootstrap (day 0):** `bootstrap-triad.sh` downloads scaffolding from AZLAN-CI-CD — `guard-core.yml`, `plugin.json`, and `pfc-core/skills/.gitkeep` placeholder.

2. **PFC Release (day 1+):** `pfc-release.yml` reads `sealed-skills-manifest.json`, copies each SKILL.md from its source path to its target path in registered PFI dev repos, along with the manifest, plugin.json, and guard-core.yml.

**Protection:** `guard-core.yml` CI gate fails any PR touching `pfc-core/` unless authored by `github-actions[bot]` or labelled `pfc-core-release`.

**Claude Code launch:** PFI dev repos use dual plugin directories:
```
claude --plugin-dir ./azlan-github-workflow --plugin-dir ./pfc-core
```

---

## Impact

- **PFI delivery teams:** Can execute sealed skills (e.g. close-out pipeline) but cannot modify them
- **Hub administrators:** Add new sealed skills by editing the manifest and tagging a PFC release
- **Existing workflows:** No breaking changes — `pfc-release.yml` defaults to `components: all` which includes sealed skills alongside ontology-registry
- **AZLAN-CI-CD:** Now contains guard-core.yml and plugin.json for bootstrap downloads

---

## First Sealed Skill

**close-out** — the 6-stage post-change housekeeping pipeline (issue close-out, architecture docs, operating guides, test plans, bulletins, deployment requirements). This is the skill currently executing this bulletin.

---

## Action Required

None for existing PFI triads — sealed skills will arrive on the next `pfc-v*` release tag. New PFI triads bootstrapped after this date will receive the scaffolding automatically.
