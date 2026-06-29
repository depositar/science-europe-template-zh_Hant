# Operator Quickstart

Use this page when you are taking over day-to-day operation of the translation
control repository. It tells you what to check first and where to go next.

## What This Repository Owns

This repository owns:

- `translation-config.yml`
- `translation/v*` version branches
- translator-facing `translation.md` files
- translated package and preview PDF releases
- migration PRs between supported versions
- manual import or public publication decisions

The parser, clean upstream scaffold artifacts, DSW runtime matrix, and demo
fixtures live in `ThreeMonth03/DSW-document-template-tool`.

## Daily Health Check

1. Check the control workflow:

   ```shell
   gh run list \
     --repo ThreeMonth03/DSW-document-template-translation \
     --workflow document_template_translation_sync.yml \
     --limit 10
   ```

2. Check each supported version branch has a recent green run:

   ```shell
   gh run list \
     --repo ThreeMonth03/DSW-document-template-translation \
     --branch translation/v1.29.1 \
     --limit 3
   gh run list \
     --repo ThreeMonth03/DSW-document-template-translation \
     --branch translation/v1.30.0 \
     --limit 3
   gh run list \
     --repo ThreeMonth03/DSW-document-template-translation \
     --branch translation/v1.30.1 \
     --limit 3
   ```

3. Confirm each translated release has the expected assets:

   ```shell
   gh release view science-europe-zh-hant-v1.30.1 \
     --repo ThreeMonth03/DSW-document-template-translation
   ```

Expected assets are listed in [QA Checklist](qa-checklist.md).

If all three checks pass, the translation control plane is healthy.

## When Reviewing a Translation PR

1. Confirm the PR targets the matching `translation/v*` branch, not `master`.
2. Confirm CI is green.
3. Download the preview PDF artifact.
4. Review glossary/i10n wording, English fallback, punctuation, placeholders,
   and PDF readability.
5. If CI creates an auto-repair commit, include it before merge.

Use [Translator Guide](translator-guide.md) for edit rules and
[QA Checklist](qa-checklist.md) before treating a branch as release-ready.

## When a New Upstream Tag Appears

1. Confirm the tool repo published a clean scaffold release for the tag.
2. Let this repo's control workflow sync `translation-config.yml` and create or
   refresh the matching `translation/v*` branch.
3. Review any migration PRs.
4. Ask translators to fill units left empty by exact-only migration.
5. Confirm the translated release assets are refreshed.

The tool repo proves that the upstream template can be transformed and
packaged. This repo proves that the translated version exists, passes QA, and
can be imported manually.

## Before Manual Import

1. Download the versioned release zip.
2. Verify `SHA256SUMS`.
3. Import into a test DSW/depositar environment when possible.
4. Render the demo project or a representative real project.
5. Only then import into the intended target environment.

Do not import from local `outputs/` unless that output was intentionally built,
reviewed, and checksummed for the same version.

## Do Not

- Do not put translation content on `master`.
- Do not commit generated `outputs/` to any branch.
- Do not manually edit generated translated output to fix wording.
- Do not add a publication token unless the team explicitly decides to automate
  public publishing and updates [Security and Publishing](security-and-publishing.md).
