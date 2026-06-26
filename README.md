# DSW Document Template Translation Control

This repository maintains the Traditional Chinese translation workflow for the
Science Europe DSW document template. It is the working area for translation
branches and automation, not the final public template repository.

The companion tooling repository,
`ThreeMonth03/DSW-document-template-tool`, owns the parser, scaffold generation,
validation checks, demo project fixture, and packaging scripts. The published
template source is copied manually to
`depositar/science-europe-template-zh_Hant` only after the generated artifacts
have been reviewed.

## Branch Model

`master` is a control branch. It contains configuration, GitHub Actions, and
documentation only; it should not carry generated workspaces, template packages,
or demo PDFs.

Each supported upstream template tag has its own translation branch:

- `translation/v1.29.1`
- `translation/v1.30.0`
- `translation/v1.30.1`

Translation changes should target the matching `translation/v*` branch. The
generated template package and preview PDF are produced by CI as artifacts for
that branch, not committed back to `master`.

## Daily Translation Flow

For ordinary translation work, edit only the `Translation (zh_Hant)` blocks in
the relevant `translation.md` files on a version branch. CI refreshes the
generated output, checks Jinja placeholders and template structure, packages the
document template, and renders a preview PDF with the shared demo project from
the tooling repository.

If CI repairs a translation file, commit the repaired file back to the same
branch before continuing. Auto-repair restores missing metadata and broken block
structure; it does not replace human review of the translated text.

## Version Updates And Migration

Supported upstream versions are listed in `translation-config.yml`. When a new
template tag is added, the tooling repository first generates a clean scaffold
for that tag. This repository then creates or refreshes the corresponding
`translation/v*` branch from that scaffold.

Cross-version migration is intentionally conservative. Automation copies a
translation only when the source hash and executable placeholders match exactly.
If the upstream sentence changed, the target translation block stays empty and is
marked for human review. This avoids silently carrying a stale translation into
a changed template.

After a version branch passes, the control workflow may open migration PRs for
other configured versions. These PRs preserve existing translations first, fill
only exact-safe blank units, and include a migration report explaining what was
copied and what still needs review.

## Automation Credentials

Most checks run with the default `github.token`. Add extra repository secrets
only when the workflow needs cross-repository access that the default token
cannot provide:

- `TRANSLATION_AUTOMATION_TOKEN`: optional token for pushing repaired commits,
  refreshing version branches, and opening migration PRs. If it is absent, the
  workflow falls back to `github.token`.
- `TOOLING_ARTIFACT_TOKEN`: optional token for reading clean scaffold artifacts
  from `ThreeMonth03/DSW-document-template-tool`. Use it only if the default
  token cannot download artifacts across repositories.

Do not add `DOCUMENT_TEMPLATE_PUBLISH_TOKEN` unless we explicitly re-enable
automatic publishing. Public template publishing is currently manual by design.

## Manual Publishing

Publishing to `depositar/science-europe-template-zh_Hant` is manual for now. We
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

The current default in `translation-config.yml` is `publish.enabled: false`.

## Configuration

`translation-config.yml` is the single source of truth for the upstream template
repository, supported version tags, language metadata, branch naming, migration
policy, and manual publish target. If those values change, update the config
first and regenerate the version branches through the tooling workflow instead
of editing generated paths by hand.
