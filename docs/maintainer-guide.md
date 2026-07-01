# Maintainer Guide

This guide is for keeping the translation workflow maintainable across upstream
template versions.

## Operations Branch

`master` is version-neutral. Keep it focused on:

- `translation-config.yml`
- `.github/workflows/document_template_translation_sync.yml`
- repository documentation
- small workspace notes
- the canonical public template README configured by `public_readme.path`

Do not commit generated workspaces, packages, preview PDFs, or completed public
template source to `master`.

The public README is the user-facing README that generated DSW template
packages expose as `README.md`. Keep it concise and aligned with the official
Science Europe template README, but write it for Traditional Chinese users. Do
not put tool-operation runbooks there; those belong in `docs/`.

## Version Branches

Version branches are named with the configured prefix:

```text
translation/v1.30.1
```

Each branch carries the compact, expanded, and translator-facing workspace for
one upstream template tag. The workflow refreshes these branches from clean
scaffold artifacts produced by the tool repository declared by
`translation-config.yml`.

Active branch refreshes also copy the canonical public README from `master`.
Branches marked maintenance or archived by `version_policy` may receive safer
workflow controls without refreshing translation content or public README text.

The tool repo can prove that a clean upstream scaffold can be transformed and
packaged. This repo still owns the translated branch, migration result, QA, and
versioned release assets.

## Updating Supported Versions

1. Confirm the tool repo can build clean artifacts for the upstream tag.
2. Let the operations workflow synchronize `translation-config.yml` and
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
daily operations workflow can then add the version to `translation-config.yml`,
create or refresh the matching branch, and open migration PRs.

Version lifecycle is controlled by `version_policy` in `translation-config.yml`.
Use it to keep old versions available without letting scheduled automation
rewrite reviewed translation content. The current policy keeps every supported
version active, so scheduled automation may refresh each `translation/v*`
branch. If the team later wants to slow down or freeze an older version, use a
maintenance rule or an archived override. See
[Version Lifecycle Policy](version-lifecycle-policy.md).

To refresh immediately instead of waiting for the schedule:

```bash
TRANSLATION_REPO=owner/document-template-translation

gh workflow run document_template_translation_sync.yml \
  --repo "$TRANSLATION_REPO" \
  --ref master
```

Optionally pin the migration source:

```bash
gh workflow run document_template_translation_sync.yml \
  --repo "$TRANSLATION_REPO" \
  --ref master \
  -f source_version=vX.Y.Z
```

If upstream introduces a new `metamodelVersion`, the tool repo handles that
first. Scheduled/manual tool CI may open a DSW compatibility probe PR that adds
an optimistic runtime row and lets CI test whether the closest previous DSW/TDK
runtime still works. Do not sync this translation repo for that tag until the
tool repo probe PR is reviewed, merged, and the clean scaffold release exists.

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

Successful non-refresh pushes to `translation/v*` branches dispatch the
operations workflow so migration can fan out after the branch has passed
translation CI. Commits with messages starting `chore: refresh ` are generated
scaffold refreshes and intentionally do not dispatch migration; this keeps daily
or manual scaffold syncs from creating loops.

## Release and Publishing

Version branches publish review/download assets after successful non-PR CI
runs. Those assets are for review, import, and provenance. They do not update
the public template source repository by themselves.

Manual public source handoff is a separate operator action. Use the tooling
repo helper to copy reviewed generated source to the configured downstream
repository as a `sync/v*` branch. Do not push generated source directly to the
downstream default branch from this repository.

Manual public publishing and optional token policy are documented in
[Security and Publishing](security-and-publishing.md).

Before import or public publishing, follow [QA Checklist](qa-checklist.md).

## Maintenance Checks

Before changing infra, verify:

```bash
TOOLING_ROOT=/path/to/document-template-tool

make -C "$TOOLING_ROOT" format-check
make -C "$TOOLING_ROOT" lint
make -C "$TOOLING_ROOT" test
```

For operations workflow changes, also run:

```bash
"$TOOLING_ROOT/.venv/bin/python" \
  "$TOOLING_ROOT/scripts/ci/validate_translation_config.py" \
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

GitHub's default `GITHUB_TOKEN` cannot push commits that create or update
workflow files on another branch. If the operations workflow needs to refresh a
version branch workflow, either configure `TRANSLATION_AUTOMATION_TOKEN` with
workflow permission or have a maintainer run the branch sync locally and push the
workflow update once. After the branch workflows are current, normal scaffold
refreshes can run without touching workflow files.
