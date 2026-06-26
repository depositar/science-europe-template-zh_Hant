# DSW Document Template Translation Control

This repository maintains the Traditional Chinese translation workflow for the
Science Europe DSW document template.  It is not the place where the final
published template source lives.  Instead, it keeps the translation files,
version branches, and automation that turn upstream Science Europe template tags
into validated translated artifacts.

The companion tooling repository,
`ThreeMonth03/DSW-document-template-tool`, owns the parser, scaffold generation,
validation checks, demo project fixture, and packaging scripts.  The published
template source is copied manually to
`depositar/science-europe-template-zh_Hant` only after the generated artifacts
have been reviewed.

## Branch Model

`master` is a control branch.  It contains configuration, GitHub Actions, and
documentation only; it should not carry generated workspaces, template packages,
or demo PDFs.

Each supported upstream template tag has its own translation branch:

- `translation/v1.29.1`
- `translation/v1.30.0`
- `translation/v1.30.1`

Translation changes should target the matching `translation/v*` branch.  The
generated template package and preview PDF are produced by CI as artifacts for
that branch, not committed back to `master`.

## Daily Translation Flow

For ordinary translation work, edit only the `Translation (zh_Hant)` blocks in
the relevant `translation.md` files on a version branch.  The CI workflow then
refreshes the template output, checks that Jinja placeholders and control
structure are still valid, packages the document template, and renders a preview
PDF with the shared demo project from the tooling repository.

If CI reports that a translation file was auto-repaired, commit the repaired
file back to the same branch before continuing.  Auto-repair is meant to restore
missing metadata and broken block structure; it is not a substitute for reviewing
the translated text.

## Version Updates And Migration

Supported upstream versions are listed in `translation-config.yml`.  When a new
template tag is added, the tooling repository first generates a clean scaffold
for that tag.  This repository then creates or refreshes the corresponding
`translation/v*` branch from that scaffold.

Cross-version migration is intentionally conservative.  Automation copies a
translation only when the source hash and executable placeholders match exactly.
If the upstream sentence changed, the target translation block stays empty and is
marked for human review.  This avoids silently carrying a stale translation into
a changed template.

After a version branch passes, the control workflow may open migration PRs for
other configured versions.  These PRs preserve existing translations first, fill
only exact-safe blank units, and include a migration report explaining what was
copied and what still needs review.

## Manual Publishing

Publishing to `depositar/science-europe-template-zh_Hant` is manual for now.  We
do this deliberately so the public repository receives only reviewed template
source, not intermediate translation work or debugging output.

After a version branch artifact has been reviewed, publish it with the tooling
helper:

```bash
make -C ../DSW-document-template-tool publish-translated-template \
  TRANSLATION_REPO=$PWD \
  PUBLISH_VERSION=v1.30.1
```

The helper reads this repository's `translation-config.yml`, checks out the
matching `translation/v*` branch in a temporary worktree, copies the generated
translated template source into the configured downstream repository, commits the
result, and pushes to `sync/v*`.

Do not add an automatic publishing token unless we intentionally decide to
re-enable public publishing from CI.  The current default in
`translation-config.yml` is `publish.enabled: false`.

## Configuration

`translation-config.yml` is the single source of truth for the upstream template
repository, supported version tags, language metadata, branch naming, migration
policy, and manual publish target.  If those values change, update the config
first and regenerate the version branches through the tooling workflow instead
of editing generated paths by hand.
