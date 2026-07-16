# Translation Terminology and Style

Use this guide together with the translator-facing `translation.md` files. It
defines how wording decisions are shared across supported template versions.

## Sources of Truth

Use these sources in order:

1. Shared DSW terminology from
   [`depositar/dsw-root-locales-zh_Hant`](https://github.com/depositar/dsw-root-locales-zh_Hant).
2. This project's reviewed [`zh-Hant glossary`](../glossary/zh-Hant.csv).
3. Existing reviewed wording in the newest active `sync/v*` branch.
4. Contextual judgment where a term cannot be translated mechanically.

If two sources disagree, prefer the meaning required by the complete sentence
and record the decision in the glossary notes. Do not silently introduce a
second translation for the same meaning.

## Writing Rules

- Write natural Traditional Chinese rather than preserving English word order.
- Keep product names, standards, identifiers, and established abbreviations in
  their official form unless the glossary specifies otherwise.
- Keep every placeholder shown in the source sentence. Reordering placeholders
  is allowed when Chinese grammar requires it.
- Do not add raw Jinja to a translation block.
- Check punctuation around optional placeholders and conditional fragments in
  the preview PDF.
- Use cross-version migration only for executable source structures accepted by
  the migration audit. Similar visible English is a review hint, not proof that
  two Jinja units are interchangeable.

## High-Risk Terms

These terms occur frequently and should remain consistent:

| English | zh-Hant |
| --- | --- |
| Data Management Plan (DMP) | 資料管理方案 |
| Data Steward | 資料託管員 |
| Data Provenance | 資料溯源 |
| Persistent Identifier (PID) | 持續識別碼 |
| Pseudonymisation / Pseudonymization | 擬匿名化 |

The complete list is in the glossary CSV. Terms such as “data collection” must
still be interpreted in context; in this project it means 「資料蒐集」 when it
describes the activity of collecting data.
