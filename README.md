# DSW-document-template-translation

Third-party translation workspace for the Science Europe DSW document template.

This repository intentionally contains only the template assets, translation
tree, generated output, and GitHub Actions workflow needed to consume
`ThreeMonth03/DSW-document-template-tool` as external tooling.

## Layout

- `.github/workflows/document_template_translation_sync.yml`: CI workflow copied from the tooling repo.
- `workspace/document-templates/compact/`: upstream DSW document template source.
- `workspace/document-templates/expanded/`: generated Jinja workspace used for translation extraction.
- `workspace/document-templates/translation/`: translator-facing `translation.md` files.
- `workspace/knowledge-models/`: KM bundle used by the sample project render.
- `workspace/projects/`: replayable sample project fixture.
- `outputs/document-templates/translated-expanded/`: generated translated template output.
- `outputs/project-render/`: sample rendered PDF output.

## CI Behavior

The workflow is intentionally not triggered by every branch push. It runs on:

- pull requests targeting `main`
- manual `workflow_dispatch`
- the daily scheduled check

This keeps feature branches quiet while still letting PRs and scheduled checks
validate that translations can sync into a document template, package, and render
the sample project preview.
