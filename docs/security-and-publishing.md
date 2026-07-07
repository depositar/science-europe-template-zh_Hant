# Security and Publishing

This repository publishes reviewed artifacts for manual use. It does not
automatically import anything into DSW.

## Current Policy

- GitHub Actions artifacts are run-scoped previews.
- GitHub Release assets are versioned review/download buckets.
- Public DSW import is manual.
- Source branch handoff is disabled by default and must be explicitly enabled
  before use.
- `DOCUMENT_TEMPLATE_PUBLISH_TOKEN` is intentionally not required.

This keeps intermediate translation work reviewable without giving CI
permission to import directly into a DSW instance.

## Release Asset Permissions

Workflows that refresh release assets need:

```yaml
permissions:
  contents: write
```

Release assets are uploaded with `--clobber`, so branch updates refresh the same
versioned release bucket. The Git tag commit is not the generated asset source
of truth; use release notes, checksums, and workflow run metadata for
provenance.

If a version should no longer refresh release assets, set
`publish_release: false` for that exact version in `translation-config.yml`.
The version branch workflow will still run audits and preview checks, but the
release upload steps are skipped. See
[Version Lifecycle Policy](version-lifecycle-policy.md).

If GitHub immutable releases are enabled, `--clobber` will fail. Either disable
immutability for these review/download releases or switch to run-id-specific
release tags.

## Optional Secrets

Most workflows can use `github.token`.

Optional secrets:

- `TRANSLATION_AUTOMATION_TOKEN`: push repaired commits, refresh version
  branches, or open migration PRs when the default token is insufficient.
  If operations need to create or update files under `.github/workflows/` on
  `sync/v*` branches, this token must include workflow permission.
- `TOOLING_ARTIFACT_TOKEN`: download tool-repo clean scaffold artifacts when the
  default token cannot read cross-repository artifacts.

For workflow synchronization, create a PAT that is limited to this repository
when possible. A fine-grained token needs `Contents: Read and write` and
`Workflows: Read and write`; a classic token needs `repo` and `workflow`.
Set it as an Actions secret without printing the token:

```shell
gh auth refresh -h github.com -s repo -s workflow
gh auth token | gh secret set TRANSLATION_AUTOMATION_TOKEN \
  --repo depositar/science-europe-template-zh_Hant
```

Do not add a broad publication token unless the team decides to automate public
publishing. If that changes, restrict the token to the intended repository or
branch and never expose it to fork pull requests.

## Optional Source Handoff

The current policy uses versioned release assets for manual import. Source
branch handoff remains disabled in `translation-config.yml`:

```yaml
publish:
  enabled: false
```

Only enable source handoff if the team decides that a reviewable source branch
is needed in addition to release assets. When that policy changes, update this
document and the operator guide in the same commit so maintainers do not have
to infer the new publication path from CI logs.
