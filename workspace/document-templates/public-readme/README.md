# Science Europe DMP 文件模板（繁體中文）

本文件模板是 [Science Europe](https://www.scienceeurope.org/) 建議之資料管理計畫（DMP）範本的繁體中文化版本，供 [Data Stewardship Wizard](https://ds-wizard.org/) 使用。文件模板會根據問卷回答套用條件規則，並彙整相關問題的文字回答，產生 DMP 文件。

## 使用方式

在 DSW 中完成資料管理計畫問卷後，可選用此文件模板產生繁體中文的
Science Europe DMP。模板會依照專案回答帶入適用段落；若目前專案使用的
Knowledge Model 不相容，DSW 可能不會顯示此模板，或無法用它產生文件。

## 維護與匯入

正式產出的文件模板套件可從本儲存庫的 release assets 下載。公開部署或
匯入 depositar 前，請先確認對應版本的 PDF 預覽與 `SHA256SUMS`。

## 相容性

此繁體中文版本對應官方 `dsw:science-europe` template 的同版 upstream release。相容 Knowledge Model 以 `template.json` 的 `allowedPackages` 為準；目前主要支援 `dsw:root`、`dsw:lifesciences`，以及繁體中文在地化的 `dsw:root-zh-hant`。

## 來源與維護

原始範本由 [ds-wizard/science-europe-template](https://github.com/ds-wizard/science-europe-template)
維護。本儲存庫維護繁體中文化內容與產出套件。完整英文說明、貢獻者與
upstream changelog 請見
[對應版本的 upstream README](https://github.com/ds-wizard/science-europe-template/blob/v{template_version}/README.md)。

## 問題回報

若問題與原始 Science Europe template 行為有關，請參考 upstream 儲存庫。若問題與繁體中文翻譯、用語、字體、PDF 呈現或 depositar 匯入有關，請回報給繁體中文化維護者。

## 致謝

本繁體中文化版本基於 Science Europe DMP Template 與 Data Stewardship Wizard
生態系製作。原始貢獻者、授權與完整版本紀錄請見
[對應版本的 upstream README](https://github.com/ds-wizard/science-europe-template/blob/v{template_version}/README.md)。

## 版本紀錄

此 README 描述繁體中文化文件模板的使用方式。各版本實際 template ID、版本號與相容性請以套件中的 `template.json` 為準。
