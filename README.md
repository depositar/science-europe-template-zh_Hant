# DSW Document Template Translation Control

This repository translates the Science Europe DSW document template to
Traditional Chinese.

The `master` branch is intentionally version-neutral. It keeps only the control
plane for translation maintenance:

- translation policy and supported upstream versions
- CI workflows
- shared fixture projects and knowledge models
- documentation for branch layout

Actual translation work lives on version branches.

## Version Branches

Each supported upstream template tag has a dedicated branch:

- `translation/v1.30.0`
- `translation/v1.30.1`

Open translation PRs against the matching `translation/v*` branch, not against
`master`.

## Branch Responsibilities

- `master`: control plane only; no checked-in document template workspace or
  generated output.
- `translation/v*`: translator-facing `translation.md` files for one upstream
  version.
- `archive/*`: safety snapshots of historical repository layouts.

Generated document template packages and demo renders should be published as
GitHub Actions artifacts or release assets, not committed to `master`.

## Migration Policy

Version upgrades use exact-only migration:

- If a translation unit has the same source hash and executable placeholders as
  the previous version, the translation may be copied automatically.
- Otherwise the translation block stays empty and must be reviewed by a human.

This keeps cross-version reuse safe without silently applying stale text to a
changed upstream template.

## Automated Migration PRs

After a `translation/v*` branch passes its sync workflow, the `master` control
workflow creates or updates migration PRs for the other configured versions.

The bot workflow:

- refreshes the target version from its own upstream workspace
- preserves target-branch translations first
- fills only blank target units from the source version using exact-safe matches
- leaves changed or unsafe units empty
- writes a `migration-reports/vX-to-vY.md` summary into the PR

Humans should review and merge those migration PRs instead of manually copying
translation files between version branches.

## Current Configuration

See `translation-config.yml` for the source template, supported upstream tags,
language, branch naming, and tooling repository.
