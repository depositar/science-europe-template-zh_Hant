# DSW Document Template Translation

This repository is the working area for the Traditional Chinese
translation of the Science Europe DSW document template. It coordinates
translation branches, migration automation, CI checks, and reviewed artifacts.

It is not the public template source. After review, maintainers hand off
generated source to the configured public template repository as a reviewable
`sync/v*` branch; the public repository's default branch and DSW/depositar import
remain manual decisions.

## Repository Roles

- `master` is the operations branch. It contains configuration, GitHub Actions, and
  documentation only.
- `translation/v*` branches contain one upstream template version each.
- `weblate/v*` branches are Weblate write-back buffers. CI promotes their XLIFF
  edits into the matching `translation/v*` branch after validation.
- Generated packages and preview PDFs are CI artifacts, not committed files.

The shared parser, scaffold builder, demo project fixture, and render tooling
live in the tool repository declared by `translation-config.yml`.

## Supported Versions

Supported versions are declared in `translation-config.yml` and mirrored as
`translation/v*` branches. The operations workflow can update that list from clean
tool-repo artifacts when upstream publishes a new supported tag.

Open translation PRs against the matching `translation/v*` branch. Do not open
translation-content PRs against `master`.

Weblate integrations should push to the matching `weblate/v*` branch instead of
writing directly to `translation/v*`. The generated promotion workflow copies
only the XLIFF file from `weblate/v*`, imports it into the Markdown translation
tree, audits the result, and then updates `translation/v*`.

## Daily Translation Flow

1. Check out the target `translation/v*` branch.
2. Edit only the `Translation (zh_Hant)` block inside `translation.md` files.
3. Keep every placeholder shown in the source sentence, such as `{name}`.
4. Push the branch and inspect the CI artifact preview PDF.
5. If CI auto-repairs generated translation inputs, include that repair commit.

See [Translator Guide](docs/translator-guide.md) for the detailed workflow and
[QA Checklist](docs/qa-checklist.md) before treating artifacts as ready.

## Maintainer Flow

Maintainers update `translation-config.yml`, synchronize supported version
branches from clean tool-repo artifacts, review migration PRs, and hand off
reviewed template source to the configured public repository.

See [Maintainer Guide](docs/maintainer-guide.md) for version upgrades and
migration automation. See [Security and Publishing](docs/security-and-publishing.md)
for release assets, credentials, and manual publishing.

## Start Here

| Role or task | Read |
| --- | --- |
| Taking over operations | [Operator Quickstart](docs/operator-quickstart.md) |
| Editing translations | [Translator Guide](docs/translator-guide.md) |
| Maintaining version branches and migration | [Maintainer Guide](docs/maintainer-guide.md) |
| Reviewing packages and preview PDFs | [QA Checklist](docs/qa-checklist.md) |
| Checking publishing policy or tokens | [Security and Publishing](docs/security-and-publishing.md) |

The complete document map is in [docs/README.md](docs/README.md).

## Configuration

`translation-config.yml` is the single source of truth for:

- upstream template repository and supported version tags
- language and translated template IDs
- version branch naming
- the public README shown by DSW for generated template packages
- migration policy
- manual publish target

Update the config first when the version policy changes. Let CI regenerate
version branches instead of editing generated paths by hand.

## Generated Files

The operations branch intentionally keeps generated build products out of git:

- `outputs/`
- `.cache/`

Do not commit generated version workspaces such as compact, expanded, or
translation trees to `master`. The only intentional `workspace/document-templates/`
file on `master` is the configured public template README:
`workspace/document-templates/public-readme/README.md`.

That README is copied into active `translation/v*` branches during scaffold
refreshes and becomes the package `README.md` shown by DSW. Public package
README text links to the corresponding upstream GitHub README instead of
shipping transform-only workspace metadata.

Build products belong in GitHub Actions artifacts.
