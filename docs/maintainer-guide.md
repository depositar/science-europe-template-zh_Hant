# Maintainer Guide

This guide is for keeping the translation workflow maintainable across upstream
template versions.

## Operations Branch

`operations` is version-neutral. Keep it focused on:

- `translation-config.yml`
- `.github/workflows/document_template_translation_sync.yml`
- repository documentation
- small workspace notes
- the canonical public template README configured by `public_readme.path`

Do not commit generated workspaces, packages, preview PDFs, or completed public
template source to the operations branch.

The public README is the user-facing README that generated DSW template
packages expose as `README.md`. Keep it concise and aligned with the official
Science Europe template README, but write it for Traditional Chinese users. Do
not put tool-operation runbooks there; those belong in `docs/`.

Keep `tooling.repository` and `tooling.ref` in `translation-config.yml` as
ordinary one-line YAML scalars. The operations workflow reads only these two
values to bootstrap the tool checkout; the checked-out tool immediately applies
the complete schema and duplicate-key validation to the whole config.

The README may use lightweight placeholders supported by the tool repo, such as
`{template_version}`. Use them when linking to version-specific upstream GitHub
content.

## Version Branches

Version branches are named with the configured prefix:

```text
sync/v1.30.1
```

Each branch carries the compact, expanded, and translator-facing workspace for
one upstream template tag. The workflow refreshes these branches from clean
scaffold artifacts produced by the tool repository declared by
`translation-config.yml`.

Active branch refreshes also copy the canonical public README from the
configured operations branch. Branches marked maintenance or archived by
`version_policy` may receive explicit workflow-control updates without
refreshing translation content or public README text.

The tool repo can prove that a clean upstream scaffold can be transformed and
packaged. This repo still owns the translated branch, synchronization result, QA, and
versioned release assets.

## Updating Supported Versions

1. Confirm the tool repo can build clean artifacts for the upstream tag.
2. Let the operations workflow synchronize `translation-config.yml` and
   missing or changed `sync/v*` branches from the downloaded artifacts.
3. Review the config/branch sync commit if the supported version list changed.
4. Review any synchronization PRs created by automation.
5. Ask translators to handle units whose source structure differs from the
   synchronization source.

Do not manually invent generated paths. The config and tool-repo artifact layout
should derive those paths.

The normal path is artifact-driven: tool CI packages every upstream tag covered
by its artifact ref policy, and this repo unions those artifact versions into
`translation-config.yml`. Manual config edits are only needed when intentionally
changing translation policy or removing a version.

If upstream publishes a tag that still uses a configured DSW metamodel/runtime,
the daily tool CI should build its clean scaffold artifact automatically. The
daily operations workflow can then add the version to `translation-config.yml`,
but it does not automatically create a translation branch unless
`version_policy` opts that version into refresh.

Version lifecycle is controlled by `version_policy` in `translation-config.yml`.
Use it to keep old versions available without letting scheduled automation
rewrite reviewed translation content. The current policy keeps newly discovered
future versions scaffold-only by default and opts currently translated versions
in explicitly. If the team wants to translate a newly discovered version, add an
active override or scoped rule. If the team later wants to slow down or freeze an
older version, use a maintenance rule or an archived override. See
[Version Lifecycle Policy](version-lifecycle-policy.md).

To refresh immediately instead of waiting for the schedule:

```bash
TRANSLATION_REPO=$(gh repo view --json nameWithOwner --jq .nameWithOwner)
TRANSLATION_OPERATIONS_BRANCH=$(awk '/control_branch:/ { print $2; exit }' translation-config.yml)

gh workflow run document_template_translation_sync.yml \
  --repo "$TRANSLATION_REPO" \
  --ref "$TRANSLATION_OPERATIONS_BRANCH"
```

Optionally pin the migration source:

```bash
gh workflow run document_template_translation_sync.yml \
  --repo "$TRANSLATION_REPO" \
  --ref "$TRANSLATION_OPERATIONS_BRANCH" \
  -f source_version=vX.Y.Z
```

If upstream introduces a new `metamodelVersion`, the tool repo handles that
first. Scheduled/manual tool CI may open a DSW compatibility probe PR that adds
an optimistic runtime row and lets CI test whether the closest previous DSW/TDK
runtime still works. Do not sync this translation repo for that tag until the
tool repo probe PR is reviewed, merged, and the clean scaffold release exists.

For each known scaffold version, verify the tool layer:

- clean scaffold release exists in the tool repo

For each active translation version, verify the downstream layers:

- `sync/v*` branch exists and has synchronized or version-specific review blocks
- translated package/PDF release exists in this repo

## Cross-Version Synchronization

Versions with `migrate_into: auto` receive translation changes from the source
version selected by the operations workflow. The source branch is authoritative
for that run:

- Exact source hash and executable placeholders: fill a blank translation or
  update an existing translation.
- Anything else: keep the target unit unchanged for version-specific review.

Artifact refresh and cross-version synchronization are separate. Refresh first
rebuilds the target scaffold while preserving that branch's translator edits;
the synchronization phase then updates only structurally identical units. The
operations workflow serializes fan-out runs so multiple active source branches
cannot update the same target concurrently.

After a sync or parser/tooling update, confirm synchronization has settled.
Either review and merge the generated synchronization PRs, or run the tool repo
status helper. The settled state is `OK` for every active source version; a missing PR
alone is not proof that migration was checked.

Successful non-refresh pushes to `sync/v*` branches dispatch the
operations workflow so translation changes can fan out after the branch has
passed translation CI. Commits with messages starting `chore: refresh ` are generated
scaffold refreshes and intentionally do not dispatch fan-out; this keeps daily
or manual scaffold syncs from creating loops.

## Release and Publishing

Version branches publish review/download assets after successful non-PR CI
runs. Those assets are for review, import, and provenance. They do not import
the template into DSW by themselves.

Release permissions and manual import policy are documented in
[Security and Publishing](security-and-publishing.md).

Before import, follow [QA Checklist](qa-checklist.md).

## Maintenance Checks

Before changing infra, verify:

```bash
TOOLING_ROOT=/path/to/document-template-tool

make -C "$TOOLING_ROOT" check
```

For operations workflow changes, also run:

```bash
"$TOOLING_ROOT/.venv/bin/python" \
  "$TOOLING_ROOT/scripts/ci/validate_translation_config.py" \
  --config translation-config.yml

make -C "$TOOLING_ROOT" check-translation-repository-docs \
  TRANSLATION_DOCS_REPO="$PWD"
```

Then inspect a real Actions run. A healthy run validates config, checks
operations documentation coverage, downloads clean tool artifacts, refreshes
version branches, creates synchronization PRs when needed, and leaves generated build
products as artifacts.

## Workflow Synchronization

The workflow templates in the tool repo are only templates. Existing
`sync/v*` branches carry generated copies. Routine operations sync leaves those
workflow files alone when it runs with the default GitHub Actions token, because
GitHub rejects workflow-file pushes unless the token has workflow scope.

If the tool repo workflow template changed and active version branches must
receive the fix, configure `TRANSLATION_AUTOMATION_TOKEN` with workflow scope and
rerun the operations workflow. With that token present, operations also syncs
generated branch workflow files while refreshing scaffold content, release
assets, and synchronization PRs for policy-enabled versions.

Use a repository-limited token when possible. A fine-grained PAT needs
`Contents: Read and write` and `Workflows: Read and write`; a classic PAT needs
`repo` and `workflow`. On a trusted maintainer machine, this is the shortest safe
setup path:

```shell
gh auth refresh -h github.com -s repo -s workflow
TRANSLATION_REPO=$(gh repo view --json nameWithOwner --jq .nameWithOwner)
gh auth token | gh secret set TRANSLATION_AUTOMATION_TOKEN \
  --repo "$TRANSLATION_REPO"
```
