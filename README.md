# Science Europe DMP Template (zh-Hant) v1.30.0

This branch contains the Traditional Chinese translation workspace for
`dsw:science-europe:1.30.0`.

## Translate

Edit only translator-facing files under:

```text
workspace/document-templates/translation/dsw-science-europe-1.30.0/tree/
```

Keep source placeholders intact and open translation PRs against `translation/v1.30.0`.
If you translate through Weblate, it should edit:

```text
weblate/dsw-science-europe.zh_Hant.xlf
```

Weblate changes are promoted by the branch's Weblate promotion workflow. Normal
branch sync treats `translation.md` as the source of truth and exports a
refreshed XLIFF file for the next Weblate edit.

## Generated Outputs

CI generates the translated document-template package and preview PDF during
pull requests and branch pushes. Those files are uploaded as GitHub Actions
artifacts or release assets; they are not committed to this branch.

Repository operations, supported-version policy, and migration automation live
on `master`.
