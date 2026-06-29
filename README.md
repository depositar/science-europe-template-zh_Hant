# DSW Document Template Translation Control

This repository is the working control area for the Traditional Chinese
translation of the Science Europe DSW document template. It coordinates
translation branches, migration automation, CI checks, and reviewed artifacts.

It is not the public template source. Public template updates are copied to
`depositar/science-europe-template-zh_Hant` manually after review.

## Repository Roles

- `master` is the control branch. It contains configuration, GitHub Actions, and
  documentation only.
- `translation/v*` branches contain one upstream template version each.
- Generated packages and preview PDFs are CI artifacts, not committed files.

The shared parser, scaffold builder, demo project fixture, and render tooling
live in `ThreeMonth03/DSW-document-template-tool`.

## Supported Versions

Supported versions are declared in `translation-config.yml` and mirrored as
`translation/v*` branches. The control workflow can update that list from clean
tool-repo artifacts when upstream publishes a new supported tag.

Open translation PRs against the matching `translation/v*` branch. Do not open
translation-content PRs against `master`.

## Daily Translation Flow

1. Checkout the target `translation/v*` branch.
2. Edit only the `Translation (zh_Hant)` block inside `translation.md` files.
3. Keep every placeholder shown in the source sentence, such as `{name}`.
4. Push the branch and inspect the CI artifact preview PDF.
5. If CI auto-repairs generated translation inputs, include that repair commit.

See [Translator Guide](docs/translator-guide.md) for the detailed workflow and
[QA Checklist](docs/qa-checklist.md) before treating artifacts as ready.

## Maintainer Flow

Maintainers update `translation-config.yml`, synchronize supported version
branches from clean tool-repo artifacts, review migration PRs, and manually
publish reviewed template source.

See [Maintainer Guide](docs/maintainer-guide.md) for version upgrades and
migration automation. See [Security And Publishing](docs/security-and-publishing.md)
for release assets, credentials, and manual publishing.

## Documentation

Start with the [Documentation Index](docs/README.md).

## Configuration

`translation-config.yml` is the single source of truth for:

- upstream template repository and supported version tags
- language and translated template IDs
- version branch naming
- migration policy
- manual publish target

Update the config first when the version policy changes. Let CI regenerate
version branches instead of editing generated paths by hand.

## Generated Files

The control branch intentionally keeps generated content out of git:

- `outputs/`
- `.cache/`
- `workspace/document-templates/`

Version-specific workspaces belong on `translation/v*` branches. Build products
belong in GitHub Actions artifacts.
