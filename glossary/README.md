# zh-Hant Glossary

[`zh-Hant.csv`](zh-Hant.csv) is the reviewed terminology reference for this
Science Europe document-template translation. It belongs on the `operations`
branch so every active version can use one shared vocabulary without adding
operations files to clean `sync/v*` branches.

Apply terms in this order:

1. Shared DSW terms follow the established Traditional Chinese locale in
   [`depositar/dsw-root-locales-zh_Hant`](https://github.com/depositar/dsw-root-locales-zh_Hant).
2. Science Europe and research-data-management terms follow `zh-Hant.csv`.
3. Existing reviewed wording in the newest active `sync/v*` branch is the
   cross-version reference.
4. Context wins over mechanical replacement. Record an explanatory note when
   the same English term needs different Chinese wording.

The CSV contains approved terms, not a search-and-replace program. Translators
must still read the complete sentence and inspect conditional punctuation in a
rendered PDF.
