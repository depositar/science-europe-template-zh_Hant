# Operator Quickstart

Use this page when you are taking over day-to-day operation of the translation
repository. It tells you what to check first and where to go next.

## What This Repository Owns

This repository owns:

- `translation-config.yml`
- `translation/v*` version branches
- `weblate/v*` Weblate write-back buffer branches
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
VERSION_BRANCH_PREFIX=$(awk '/version_branch_prefix:/ { print $2; exit }' translation-config.yml)
SUPPORTED_VERSIONS=$(awk '
  /supported_versions:/ { in_versions=1; next }
  in_versions && /^    - / { print $2; next }
  in_versions && /^[^ ]/ { in_versions=0 }
' translation-config.yml)
```

1. Check the operations workflow:

   ```shell
   gh run list \
     --repo "$TRANSLATION_REPO" \
     --workflow document_template_translation_sync.yml \
     --limit 10
   ```

2. Check each supported version branch has a recent green run:

   ```shell
   for version in $SUPPORTED_VERSIONS; do
     gh run list \
       --repo "$TRANSLATION_REPO" \
       --branch "${VERSION_BRANCH_PREFIX}${version}" \
       --limit 3
   done
   ```

3. Confirm each translated release has the expected assets:

   ```shell
   for version in $SUPPORTED_VERSIONS; do
     gh release view "science-europe-zh-hant-$version" --repo "$TRANSLATION_REPO"
   done
   ```

Expected assets are listed in [QA Checklist](qa-checklist.md).

4. If public-facing package text changed, confirm the configured public README
   is present on each active version branch:

   ```shell
   PUBLIC_README_PATH=workspace/document-templates/public-readme/README.md
   git fetch origin

   for version in $SUPPORTED_VERSIONS; do
     git show \
       "origin/${VERSION_BRANCH_PREFIX}${version}:$PUBLIC_README_PATH" \
       >/dev/null
   done
   ```

   The branch CI package should contain this README as package `README.md`.

If these checks pass for every supported version, the translation workflow
is healthy for versions already listed in `translation-config.yml`. New upstream
tags still need the upgrade flow below.

## Manual Sync

Use a manual sync when the tool repo has refreshed clean scaffold artifacts, the
tooling workflow template changed, or you do not want to wait for the daily
schedule:

```shell
gh workflow run document_template_translation_sync.yml \
  --repo "$TRANSLATION_REPO" \
  --ref master
```

This runs the operations workflow on `master`. It validates
`translation-config.yml`, downloads the latest clean scaffold artifacts from
the configured tool repository, refreshes supported
`translation/v*` branches, and may open or update migration PRs.

Manual syncs use the `manual` version-policy mode. The current configuration
keeps every supported version active, so manual syncs may refresh all supported
version branches. If the team later marks a version as maintenance or archived,
that policy can limit refreshes or freeze translation content. See
[Version Lifecycle Policy](version-lifecycle-policy.md).

If you want migration PRs to fan out from a specific translated version, pass
`source_version`:

```shell
gh workflow run document_template_translation_sync.yml \
  --repo "$TRANSLATION_REPO" \
  --ref master \
  -f source_version=vX.Y.Z
```

After dispatching, inspect the Actions run and any migration PRs before asking
translators to continue.

## What Version Branch CI Does

When a maintainer pushes to a `translation/v*` branch, or a translation PR runs
against one, the branch workflow audits the translation tree, syncs the
translated template, renders the demo preview, uploads Actions artifacts, and
refreshes the versioned GitHub Release assets.

For normal translation-content pushes, a successful branch run also dispatches
the operations workflow on `master`. That operations run refreshes supported
version branches and may open migration PRs so exact-safe changes can fan out to
other supported versions. Scaffold refresh commits with messages starting
`chore: refresh ` intentionally skip this dispatch to avoid migration loops.

Scheduled operations runs use the stricter `auto` version-policy mode. With the
current policy, all supported versions are active and may refresh. If the team
later marks a version as maintenance or archived, scheduled runs respect that
policy.

## What Weblate Pushes Do

Weblate should push translation edits to a matching `weblate/v*` branch. That
branch is only a write-back buffer. Its promotion workflow copies the XLIFF file
into the matching `translation/v*` branch, imports it into `translation.md`,
audits the result, syncs translated output, and pushes the validated commit.

After that push lands on `translation/v*`, the normal version branch workflow
builds the package, preview PDF, release assets, and migration dispatch. If the
promotion workflow fails, inspect the run before asking translators to continue;
common causes are stale XLIFF source hashes, missing placeholders, or a version
branch whose generated promotion workflow has not been refreshed from the tool
repo template.

## When Reviewing a Translation PR

1. Confirm the PR targets the matching `translation/v*` branch, not `master`.
   Weblate-generated edits should arrive through `weblate/v*` promotion and then
   appear as a validated commit on `translation/v*`.
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
2. Let this repo's operations workflow sync `translation-config.yml` and create
   or refresh the matching `translation/v*` branch according to
   [Version Lifecycle Policy](version-lifecycle-policy.md).
3. Review any migration PRs.
4. Ask translators to fill units left empty by exact-only migration.
5. Confirm the translated release assets are refreshed.

The tool repo proves that the upstream template can be transformed and
packaged. This repo proves that the translated version exists, passes QA, and
can be imported manually.

## Before Public Source Handoff or Manual Import

1. Download the versioned release zip.
2. Verify `SHA256SUMS`.
3. Import into a test DSW/depositar environment when possible.
4. Render the demo project or a representative real project.
5. If source must be handed to the public template repository, push or refresh
   the configured `sync/v*` branch and review that branch.
6. Only then merge/import into the intended target environment.

Do not import from local `outputs/` unless that output was intentionally built,
reviewed, and checksummed for the same version.

## Do Not

- Do not put translation content on `master`.
- Do not commit generated `outputs/` to any branch.
- Do not manually edit generated translated output to fix wording.
- Do not add a publication token unless the team explicitly decides to automate
  public publishing and updates [Security and Publishing](security-and-publishing.md).
