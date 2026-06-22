# Shared Workspace Assets

The `master` branch keeps only shared assets that are not tied to one document
template version:

- `knowledge-models/` stores KM bundles used by CI render fixtures.
- `projects/` stores replayable sample project fixtures.

Document template workspaces are version-specific and live on
`translation/v*` branches or in GitHub Actions artifacts.
