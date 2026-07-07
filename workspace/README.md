# Version Workspace

This branch contains version-specific document-template workspaces for the
matching `sync/v*` branch.

Demo project fixtures and matching Knowledge Model bundles are maintained in
the tool repository declared by the operations branch configuration. They live
under that repository's `fixtures/knowledge-models/` and `fixtures/projects/`
directories.

Do not copy demo fixtures into this branch. CI reads them from the tool
repository so preview PDFs stay consistent across versions.
