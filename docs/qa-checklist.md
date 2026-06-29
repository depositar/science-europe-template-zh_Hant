# QA Checklist

Use this before importing a translated Science Europe document template into a
DSW/depositar environment or publishing reviewed source downstream.

## Branch and CI

For the target version:

- the target branch is the matching `translation/v*` branch
- CI is green on the branch or PR head
- auto-repair commits, if any, are included
- the generated package artifact exists
- the demo preview artifact exists

## Release Assets

On non-PR branch runs, confirm the versioned release exists:

```bash
gh release view science-europe-zh-hant-vX.Y.Z \
  --repo ThreeMonth03/DSW-document-template-translation
```

Expected assets:

- `dsw-science-europe-zh-hant-vX.Y.Z.zip`
- `test-project-vX.Y.Z.pdf`
- `test-project-vX.Y.Z.pdf.json`
- `SHA256SUMS`
- `release-notes.md`

Download the assets and verify the checksum before manual import.

## Translation Structure

Confirm CI or local checks covered:

- translation block format is valid
- placeholders such as `{name}` are preserved
- raw Jinja is not introduced in translation text
- translated output keeps the executable Jinja and HTML structure
- blank blocks are intentional and not accidental English fallback

## PDF Review

Open the preview PDF and inspect:

- cover page title, project name, and metadata
- table and list rendering
- representative conditional sections
- punctuation around optional sentences
- glossary and i10n wording
- obvious fallback English text
- heading/body font hierarchy and readability

Do not change generated template structure just to improve wording. If a
sentence is hard to translate because the translation unit is broken, fix the
tooling in `ThreeMonth03/DSW-document-template-tool` and regenerate.

## Manual Import

Before importing into DSW/depositar:

1. Download the versioned zip from the translation release.
2. Verify `SHA256SUMS`.
3. Import into a test DSW environment when possible.
4. Render the demo project or a representative real project.
5. Only then import into the intended target environment.

Do not import directly from local `outputs/` unless that output was intentionally
built, reviewed, and checksummed for the same version.
