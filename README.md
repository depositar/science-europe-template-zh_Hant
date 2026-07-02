# Science Europe DMP Template (zh-Hant) v1.30.1

This branch contains the Traditional Chinese translation workspace for
`dsw:science-europe:1.30.1`.

## Translate

Edit only translator-facing files under:

```text
workspace/document-templates/translation/dsw-science-europe-1.30.1/tree/
```

Keep source placeholders intact and open translation PRs against `translation/v1.30.1`.
If you translate through Weblate, it should edit:

```text
weblate/dsw-science-europe.zh_Hant.xlf
```

CI imports that XLIFF back into the translation tree and exports a refreshed
XLIFF file after every sync.

## Generated Outputs

CI generates the translated document-template package and preview PDF during
pull requests and branch pushes. Those files are uploaded as GitHub Actions
artifacts or release assets; they are not committed to this branch.

Repository operations, supported-version policy, and migration automation live
on `master`.
