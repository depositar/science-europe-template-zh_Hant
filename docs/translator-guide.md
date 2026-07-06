# Translator Guide

This guide is for editing Traditional Chinese translations on a
`sync/v*` branch.

## Pick the Right Branch

Each actively translated upstream Science Europe template version has a
matching `sync/v*` branch. Use the branch that matches the template
version you want to translate. If you are unsure, use the newest active branch
unless a maintainer asks for a specific version.

To see available branches:

```bash
git fetch origin
git branch -r --list 'origin/sync/v*'
```

## Edit Translation Files

Translation work happens in files named `translation.md` under:

```text
workspace/document-templates/translation/.../tree/
```

Each file is designed for humans:

- Read `Sentence (en)` to understand the source text.
- Edit only the `Translation (zh_Hant)` fenced block.
- Leave collapsed machine metadata alone.

Placeholder rules:

- Keep every placeholder shown in the source sentence, such as `{name}`.
- You may reorder placeholders to make Chinese grammar natural.
- Do not write raw Jinja such as `{{ ... }}` or `{% ... %}` in translations.

Blank translation blocks fall back to English in generated preview artifacts.
That is useful while a branch is incomplete, but it is not a finished
translation.

## Optional External Translation Tools

The default workflow is Git/Markdown based. Edit `translation.md` directly on a
`sync/v*` branch or through a pull request.

The tool repo has optional import/export helpers for external translation
platforms, but this repository does not enable them by default. If the team
turns on an external service later, use an explicit exchange file such as:

```text
xliff/dsw-science-europe.zh_Hant.xlf
```

External platforms should never edit generated compact, expanded, translated
output, release assets, or public handoff branches. Imported results must land
back in the Markdown translation tree before CI packaging or review. The
Markdown translation tree remains the reviewable source of truth in this
repository.

## What CI Checks

When you push to a `sync/v*` branch or open a PR into one, CI will:

- refresh generated translation inputs from the checked-in workspace
- repair missing metadata or broken translation block skeletons when safe
- audit placeholders and unsafe Jinja
- sync translations into a generated template
- verify translated output did not break executable template structure
- package the document template
- render the shared demo project as a preview PDF artifact

If CI pushes an auto-repair commit, include it in the branch before continuing.
Auto-repair only fixes structure; it does not decide wording.

## Review Artifacts

Download the preview artifacts from the GitHub Actions run. The important files
are:

- translated document template zip
- preview PDF
- render JSON or failure status file
- migration report, when the branch was created or refreshed by migration

Review the PDF for missing English fallback, broken placeholders, awkward word
order, and glossary consistency. Use [QA Checklist](qa-checklist.md) before
asking a maintainer to import or publish the package.

If something looks like a structural issue, fix the translation tree or ask a
maintainer before changing generated output by hand.

## Glossary and Style

Use the glossary and i10n wording prepared for this project. Prefer natural
Traditional Chinese over literal English order, but keep DSW terms consistent
across versions.

Good translation branches are boring: small wording commits, green CI, and a PDF
that looks predictable.
