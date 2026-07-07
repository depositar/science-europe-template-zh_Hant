# Workspace

The `operations` branch intentionally keeps this workspace nearly empty.

Demo project fixtures and matching Knowledge Model bundles are maintained in
the tool repository declared by `translation-config.yml`. They live under that
repository's `fixtures/knowledge-models/` and `fixtures/projects/` directories.
Keeping those fixtures in one place avoids stale preview PDFs when the sample
project changes.

Document template workspaces are version-specific and live on `sync/v*`
branches or in GitHub Actions artifacts. The only document-template file
intentionally kept on `operations` is the public README configured by
`translation-config.yml`.
