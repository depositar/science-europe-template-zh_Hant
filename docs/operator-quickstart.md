# Operator Quickstart

Use this page when you are taking over day-to-day operation of the translation
repository. It tells you what to check first and where to go next.

## What This Repository Owns

This repository owns:

- `translation-config.yml`
- `translation/v*` version branches
- translator-facing `translation.md` files
- translated package and preview PDF releases
- migration PRs between supported versions
- manual import or public publication decisions

The parser, clean upstream scaffold artifacts, DSW runtime matrix, and demo
fixtures live in the tool repository declared by `translation-config.yml`.
The public template README shown by DSW is owned here at the path configured by
`public_readme.path`.

## Daily Health Check

Set the repository name once before copying commands:

```shell
TRANSLATION_REPO=owner/document-template-translation
TRANSLATION_OPERATIONS_BRANCH=$(awk '/control_branch:/ { print $2; exit }' translation-config.yml)
VERSION_BRANCH_PREFIX=$(awk '/version_branch_prefix:/ { print $2; exit }' translation-config.yml)
ACTIVE_TRANSLATION_VERSIONS=$(python - <<'PY'
from pathlib import Path

import yaml

config = yaml.safe_load(Path("translation-config.yml").read_text(encoding="utf-8"))
defaults = config.get("version_policy", {}).get("defaults", {})
overrides = config.get("version_policy", {}).get("overrides", {})

for version in config["template"]["supported_versions"]:
    policy = {**defaults, **overrides.get(version, {})}
    if policy.get("refresh") in {"auto", "manual"} or policy.get("publish_release") is True:
        print(version)
PY
)
```

`ACTIVE_TRANSLATION_VERSIONS` is derived from `translation-config.yml`, so daily
checks follow the versions currently opted into branch refresh or release asset
publishing. When in doubt, run the validation step in the operations workflow or
the tool repo's `validate_translation_config.py`; its version lifecycle table
shows which versions are active and which are scaffold-only.

1. Check the operations workflow:

   ```shell
   gh run list \
     --repo "$TRANSLATION_REPO" \
     --workflow document_template_translation_sync.yml \
     --limit 10
   ```

2. Check each active translation version branch has a recent green run:

   ```shell
   for version in $ACTIVE_TRANSLATION_VERSIONS; do
     gh run list \
       --repo "$TRANSLATION_REPO" \
       --branch "${VERSION_BRANCH_PREFIX}${version}" \
       --limit 3
   done
   ```

3. Confirm each active translated release has the expected assets:

   ```shell
   for version in $ACTIVE_TRANSLATION_VERSIONS; do
     gh release view "science-europe-zh-hant-$version" --repo "$TRANSLATION_REPO"
   done
   ```

Expected assets are listed in [QA Checklist](qa-checklist.md).

4. If public-facing package text changed, confirm the configured public README
   is present on each active version branch:

   ```shell
   PUBLIC_README_PATH=workspace/document-templates/public-readme/README.md
   git fetch origin

   for version in $ACTIVE_TRANSLATION_VERSIONS; do
     git show \
       "origin/${VERSION_BRANCH_PREFIX}${version}:$PUBLIC_README_PATH" \
       >/dev/null
   done
   ```

   The branch CI package should contain this README as package `README.md`.

If these checks pass for every active translation version, the translation
workflow is healthy for the versions currently opted into translation. New
upstream tags may be listed in `translation-config.yml` as scaffold-only until
the team opts them into `version_policy`.

## Manual Sync

Use a manual sync when the tool repo has refreshed clean scaffold artifacts or
you do not want to wait for the daily schedule:

```shell
gh workflow run document_template_translation_sync.yml \
  --repo "$TRANSLATION_REPO" \
  --ref "$TRANSLATION_OPERATIONS_BRANCH"
```

This runs the operations workflow on the configured control branch. It
validates `translation-config.yml`, downloads the latest clean scaffold
artifacts from the configured tool repository, records newly available scaffold
versions, refreshes policy-enabled `translation/v*` branches, and may open or
update migration PRs.

Manual syncs use the `manual` version-policy mode. The current configuration
keeps discovered future versions scaffold-only until maintainers opt them in,
while the currently active versions can refresh. See [Version Lifecycle
Policy](version-lifecycle-policy.md).

If you want migration PRs to fan out from a specific translated version, pass
`source_version`:

```shell
gh workflow run document_template_translation_sync.yml \
  --repo "$TRANSLATION_REPO" \
  --ref "$TRANSLATION_OPERATIONS_BRANCH" \
  -f source_version=vX.Y.Z
```

After dispatching, inspect the Actions run and any migration PRs before asking
translators to continue.

If no migration PR appears and you need to prove the migration queue is empty,
run the tool repo status helper against the same clean scaffold artifacts:

```shell
TOOL_REPO_DIR=/path/to/document-template-tool
TRANSLATION_REPO_DIR=/path/to/this-repository

"$TOOL_REPO_DIR/.venv/bin/python" "$TOOL_REPO_DIR/scripts/ci/check_translation_migration_status.py" \
  --repo "$TRANSLATION_REPO_DIR" \
  --tooling-root "$TOOL_REPO_DIR" \
  --clean-artifact-root /tmp/clean-scaffolds
```

`OK` means exact-only migration has nothing new to carry between active version
branches. `PENDING` means a migration PR should be created or reviewed before
translation work continues.

Manual sync does not create, update, or delete workflow files on
`translation/v*` branches. That is intentional: GitHub's default token cannot
push workflow-file changes across branches, and routine scaffold refreshes
should not require elevated workflow permission. If the branch workflow template
itself changed, a maintainer must run the tool repo branch-sync helper with
`--sync-workflows` and push the explicit workflow maintenance commits.

## What Version Branch CI Does

When a maintainer pushes to a `translation/v*` branch, or a translation PR runs
against one, the branch workflow audits the translation tree, syncs the
translated template, renders the demo preview, uploads Actions artifacts, and
refreshes the versioned GitHub Release assets.

For normal translation-content pushes, a successful branch run also dispatches
the operations workflow on the configured control branch. That operations run
refreshes supported policy-enabled version branches and may open migration PRs
so exact-safe changes can fan out to other active migration targets. Scaffold
refresh commits with messages starting `chore: refresh ` intentionally skip
this dispatch to avoid migration loops.

Scheduled operations runs use the stricter `auto` version-policy mode. With the
current policy, only explicitly active versions refresh. Scaffold-only versions
remain recorded but do not get branches, migrations, or release refreshes until
their policy changes.

## Optional External Translation Tools

The default workflow is direct Markdown editing on `translation/v*` branches.
External translation platforms are optional and currently disabled. If the team
enables one later, import validated exchange output back into `translation.md`
before review, packaging, or release.

## When Reviewing a Translation PR

1. Confirm the PR targets the matching `translation/v*` branch, not the
   operations branch.
   External translation tool output, if any, should already be imported into
   `translation.md`.
2. Confirm CI is green.
3. Download the preview PDF artifact.
4. Review glossary/i10n wording, English fallback, punctuation, placeholders,
   and PDF readability.
5. If CI creates an auto-repair commit, include it before merge.

Use [Translator Guide](translator-guide.md) for edit rules and
[QA Checklist](qa-checklist.md) before treating a branch as release-ready.

## When a New Upstream Tag Appears

1. Confirm the tool repo published a clean scaffold release for the tag. If the
   tool repo opened a DSW compatibility probe PR instead, wait for that PR to be
   reviewed and merged first.
2. Let this repo's operations workflow sync `translation-config.yml`. By
   default, the new tag is recorded as scaffold-only.
3. If the team wants to translate that tag, add a `version_policy` override or
   rule that enables `refresh`, `migrate_into`, and `publish_release`, then run
   the operations workflow again to create or refresh the matching
   `translation/v*` branch according to
   [Version Lifecycle Policy](version-lifecycle-policy.md).
4. Review any migration PRs.
5. Ask translators to fill units left empty by exact-only migration.
6. Confirm the translated release assets are refreshed.

The tool repo proves that the upstream template can be transformed and
packaged. This repo proves that the translated version exists, passes QA, and
can be imported manually.

## Before Public Source Handoff or Manual Import

1. Download the versioned release zip.
2. Verify `SHA256SUMS`.
3. Import into a test DSW environment when possible.
4. Render the demo project or a representative real project.
5. If source must be handed to the public template repository, push or refresh
   the configured `sync/v*` branch and review that branch.
6. Only then merge/import into the intended target environment.

Do not import from local `outputs/` unless that output was intentionally built,
reviewed, and checksummed for the same version.

## Do Not

- Do not put translation content on the operations branch.
- Do not commit generated `outputs/` to any branch.
- Do not manually edit generated translated output to fix wording.
- Do not add a publication token unless the team explicitly decides to automate
  public publishing and updates [Security and Publishing](security-and-publishing.md).
