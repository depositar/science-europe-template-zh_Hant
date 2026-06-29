# Maintainer Guide

This guide is for keeping the translation workflow maintainable across upstream
template versions.

## Control Branch

`master` is version-neutral. Keep it focused on:

- `translation-config.yml`
- `.github/workflows/document_template_translation_sync.yml`
- repository documentation
- small workspace notes

Do not commit generated workspaces, packages, preview PDFs, or completed public
template source to `master`.

## Version Branches

Version branches are named with the configured prefix:

```text
translation/v1.30.1
```

Each branch carries the compact, expanded, and translator-facing workspace for
one upstream template tag. The workflow refreshes these branches from clean
scaffold artifacts produced by `ThreeMonth03/DSW-document-template-tool`.

The tool repo can prove that a clean upstream scaffold can be transformed and
packaged. This repo still owns the translated branch, migration result, QA, and
versioned release assets.

## Updating Supported Versions

1. Confirm the tool repo can build clean artifacts for the upstream tag.
2. Let the control-plane workflow synchronize `translation-config.yml` and
   missing or changed `translation/v*` branches from the downloaded artifacts.
3. Review the config/branch sync commit if the supported version list changed.
4. Review any migration PRs created by automation.
5. Ask translators to finish units left empty by exact-only migration.

Do not manually invent generated paths. The config and tool-repo artifact layout
should derive those paths.

The normal path is artifact-driven: tool CI packages every upstream tag covered
by its artifact ref policy, and this repo unions those artifact versions into
`translation-config.yml`. Manual config edits are only needed when intentionally
changing the support policy or removing a version.

If upstream publishes a tag that still uses a configured DSW metamodel/runtime,
the daily tool CI should build its clean scaffold artifact automatically. The
daily control workflow can then add the version to `translation-config.yml`,
create or refresh the matching branch, and open migration PRs.

If upstream introduces a new `metamodelVersion`, the tool repo must first gain a
new `config/dsw-compat.yml` runtime row. That step is intentionally manual
because DSW server, TDK, and API behavior need a real smoke test.

For each supported version, verify all three layers:

- clean scaffold release exists in the tool repo
- `translation/v*` branch exists and has migrated or empty review blocks
- translated package/PDF release exists in this repo

## Migration Policy

Migration is conservative:

- Exact source hash and executable placeholders: copy the translation.
- Anything else: leave the target translation empty for human review.

The migration job may open or update PRs between version branches. It preserves
existing target translations first and only fills exact-safe blank units. This
keeps useful reuse without silently carrying stale text into changed upstream
sentences.

## Release and Publishing

Version branches publish review/download assets after successful non-PR CI
runs. Manual public publishing and optional token policy are documented in
[Security and Publishing](security-and-publishing.md).

Before import or public publishing, follow [QA Checklist](qa-checklist.md).

## Maintenance Checks

Before changing infra, verify:

```bash
make -C ../DSW-document-template-tool format-check
make -C ../DSW-document-template-tool lint
make -C ../DSW-document-template-tool test
```

For control-plane changes, also run:

```bash
../DSW-document-template-tool/.venv/bin/python \
  ../DSW-document-template-tool/scripts/ci/validate_translation_config.py \
  --config translation-config.yml
```

Then inspect a real Actions run. A healthy run validates config, downloads clean
tool artifacts, refreshes version branches, creates migration PRs when needed,
and leaves generated build products as artifacts.

## Workflow Synchronization

The workflow template in the tool repo is only a template. Existing
`translation/v*` branches carry their own workflow files. When a workflow fix is
needed, apply it to every supported version branch and confirm the branch CI
refreshes its release assets.
