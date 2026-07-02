# Translator Guide

This guide is for editing Traditional Chinese translations on a
`translation/v*` branch.

## Pick the Right Branch

Each supported upstream Science Europe template version has a matching
`translation/v*` branch. Use the branch that matches the template version you
want to translate. If you are unsure, use the newest branch unless a maintainer
asks for a specific version.

To see available branches:

```bash
git fetch origin
git branch -r --list 'origin/translation/v*'
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

## Weblate Editing

Version branches contain a Weblate exchange file:

```text
weblate/dsw-science-europe.zh_Hant.xlf
```

Weblate should edit that XLIFF file only, and it should push edits to the
matching `weblate/v*` branch. For example, Weblate edits for
`translation/v1.30.1` should land on `weblate/v1.30.1`.

The Markdown translation tree remains the reviewable source in this repository.
When Weblate pushes to `weblate/v*`, the generated promotion workflow:

- checks out the matching `translation/v*` branch
- copies only `weblate/dsw-science-europe.zh_Hant.xlf` from `weblate/v*`
- imports XLIFF targets into `translation.md`
- audits placeholders, source hashes, and unsafe Jinja
- syncs the translated template output
- pushes the validated result back to `translation/v*`

After that, the normal version-branch workflow builds preview artifacts and
release assets from `translation/v*`.

Git users may still open PRs directly against `translation/v*`. The same
translation tree and CI checks apply.

Do not edit generated compact, expanded, translated output, release assets, or
public handoff branches in Weblate. Do not treat `weblate/v*` as a release or
review branch; it is only a write-back buffer for Weblate.

## What CI Checks

When you push to a `translation/v*` branch or open a PR into one, CI will:

- refresh generated translation inputs from the checked-in workspace
- export a refreshed Weblate XLIFF file
- repair missing metadata or broken translation block skeletons when safe
- audit placeholders and unsafe Jinja
- sync translations into a generated template
- verify translated output did not break executable template structure
- package the document template
- render the shared demo project as a preview PDF artifact

When Weblate pushes to a `weblate/v*` branch, the promotion workflow first
imports the XLIFF into `translation/v*`. If promotion succeeds, the normal
`translation/v*` workflow then performs the checks above. If promotion fails,
the XLIFF is probably stale, missing source hashes, or carrying unsafe
placeholder changes.

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
