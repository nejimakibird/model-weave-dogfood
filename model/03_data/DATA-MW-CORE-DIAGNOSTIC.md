---
type: data_object
id: DATA-MW-CORE-DIAGNOSTIC
name: モデル診断情報
kind: diagnostic
data_format: object
tags:
  - Core
  - Data
  - Diagnostic
---

# モデル診断情報

## Summary

モデルのパース、Vault検証、参照解決、Preview表示時の追加チェックで生成される `ValidationWarning` を表すデータオブジェクト。
Markdown正本として保存されるデータではなく、`warningsByFilePath` や `ParseResult.warnings`、Preview state の warnings として扱われる派生診断である。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| code | 診断コード | string | | Y | code | | ValidationWarningCode |
| message | メッセージ内容 | string | | Y | message | | 診断メッセージ |
| severity | 深刻度 | string | | Y | severity | | info / warning / error |
| path | 対象path | string | | N | path | | 主な対象ファイルpath |
| filePath | 対象filePath | string | | N | filePath | | 互換的な対象ファイルpath |
| line | 対象行 | number | | N | line | | 単一行の位置 |
| fromLine | 開始行 | number | | N | fromLine | | 範囲開始行 |
| toLine | 終了行 | number | | N | toLine | | 範囲終了行 |
| section | 対象セクション | string | | N | section | | セクション名 |
| field | 対象フィールド | string | | N | field | | テーブル列やfrontmatter field |
| context | 補足context | object | | N | context | | Record形式の補足情報 |

## Notes

- severity の有効値は info / warning / error である。
- Viewerでは info がNotesとして表示される。`note` は `ValidationWarning.severity` の保存値ではない。
- code の有効値は src/types/warnings.ts の VALIDATION_WARNING_CODES を正とする。
- row / targetRef は現行 ValidationWarning の直接フィールドではないため、必要な場合は context に保持される。
- warningsByFilePath では path がキーになり、warning自体にも path が補完される場合がある。
- Viewerのコピー支援は message / code / severity / context などから生成されるUI出力であり、このDATAモデルの別フィールドではない。導出ルールは [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] に分ける。
- Markdownテーブル安全性のため、Record構造は notes で表現する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/types/models.ts | ValidationWarning | type | 診断情報のデータ構造定義 |
| src/types/warnings.ts | VALIDATION_WARNING_SEVERITIES | constant | severityの有効値 |
| src/types/warnings.ts | VALIDATION_WARNING_CODES | constant | codeの有効値 |
| src/core/vault-index.ts | warningsByFilePath | field | file path別warning map |
| src/core/validator.ts | validateVaultIndex | function | Vault検証warning生成 |
| src/core/current-file-diagnostics.ts | buildCurrentObjectDiagnostics | function | 表示対象の診断生成 |
| src/core/current-file-diagnostics.ts | normalizeDiagnosticSeverity | function | severityの正規化 |
| src/views/modeling-preview-view.ts | renderDiagnosticCard | function | 診断カードとコピー支援の表示 |
