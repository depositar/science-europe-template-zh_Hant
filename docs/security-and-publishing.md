# Security and Publishing

This repository publishes reviewed artifacts for manual use. It does not
automatically update the public downstream template source.

## Current Policy

- GitHub Actions artifacts are run-scoped previews.
- GitHub Release assets are versioned review/download buckets.
- Public DSW import is manual.
- Source branch handoff is disabled by default and must be explicitly enabled
  before use.
- `DOCUMENT_TEMPLATE_PUBLISH_TOKEN` is intentionally not required.

This keeps intermediate translation work visible to maintainers without giving
CI permission to publish directly to the public template repository or DSW
instance.

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

Do not add a broad publication token unless the team decides to automate public
publishing. If that changes, restrict the token to the intended repository or
branch and never expose it to fork pull requests.

## Optional Source Handoff

After QA, a maintainer may enable and run the explicit handoff helper from the
tooling repo if a downstream process needs a reviewable source branch:

```bash
TOOLING_ROOT=/path/to/document-template-tool

make -C "$TOOLING_ROOT" publish-translated-template \
  TRANSLATION_REPO=$PWD \
  PUBLISH_VERSION=vX.Y.Z
```

The helper copies reviewed generated source to the configured target repository
and pushes the configured branch prefix. It does not modify the downstream
default branch and does not import anything into DSW. Review that staged branch
before merging, importing, or asking a downstream maintainer to take over.
