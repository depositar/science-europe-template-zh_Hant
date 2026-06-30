# Translation Tree

This folder is the translator-facing tree exported from the expanded
template workspace.

- Each translation unit has its own `translation.md` file.
- Each file starts with the source `Sentence (en)`, then the
  editable `Translation (zh_Hant)` block.
- Wrapper-level blocks from the expanded workspace are split into smaller
  translator-facing units whenever the source structure allows it.
- Keep every placeholder shown in the sentence, such as `{name}`. You
  may reorder placeholders for grammar; sync converts them back to Jinja
  variables.
- Machine metadata is collapsed at the bottom of each file and should not
  be edited manually.
- If a translation file is deleted or its markdown block is broken, run
  `make export-translation-tree` to rebuild the file skeleton.
- Run `make sync-translation-tree` to apply translator edits back into a
  generated template copy.
