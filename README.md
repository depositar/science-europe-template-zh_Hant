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

- `translation/v1.29.1`
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

## Release Assets

CI publishes generated packages as GitHub Release assets on non-PR runs of each
version branch. These releases are review/download buckets, not the formal
public source of record.

For a branch such as `translation/v1.30.0`, download assets from the matching
release tag:

- `science-europe-zh-hant-v1.30.0`

Expected assets include:

- `dsw-science-europe-zh-hant-v1.30.0.zip`: DSW import package
- `test-project-v1.30.0.pdf`: demo render
- `test-project-v1.30.0.pdf.json`: render metadata
- `SHA256SUMS`: checksums

After review, import the zip manually into the target DSW/depositar environment.
Do not treat this repository's release asset as an automatic public publish.

## Migration Policy

Version upgrades use exact-only migration:

- If a translation unit has the same source hash and executable placeholders as
  the previous version, the translation may be copied automatically.
- Otherwise the translation block stays empty and must be reviewed by a human.

This keeps cross-version reuse safe without silently applying stale text to a
changed upstream template.

## Current Configuration

See `translation-config.yml` for the source template, supported upstream tags,
language, branch naming, and tooling repository.
