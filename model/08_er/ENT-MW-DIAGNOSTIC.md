---
type: er_entity
id: ENT-MW-DIAGNOSTIC
logical_name: 診断結果
physical_name: mw_diagnostic
kind: diagnostic
tags:
  - ModelWeave
  - ER
  - Repository
---

# 診断結果 / mw_diagnostic

## Overview

Model Weaveが検出した警告・エラー・情報。解析・検証結果として生成される派生情報。

## Columns

| logical_name | physical_name | data_type | length | scale | not_null | pk | encrypted | default_value | notes |
|---|---|---|---:|---:|---|---|---|---|---|
| 診断ID | diagnosticId | string | | | Y | Y | N | | 診断単位の識別子 |
| ファイルパス | filePath | string | | | Y | N | N | | 発生元ファイル |
| アセットID | assetId | string | | | N | N | N | | 発生元ModelAsset |
| 重大度 | severity | string | | | Y | N | N | | error / warning / info / note |
| 診断コード | code | string | | | Y | N | N | | unresolved-reference 等 |
| メッセージ | message | string | | | Y | N | N | | ユーザー向け説明 |
| セクション | section | string | | | N | N | N | | 発生セクション |
| 行番号 | row | number | | | N | N | N | | 発生行 |
| 対象参照 | targetRef | string | | | N | N | N | | 未解決参照などの対象 |
| 判定ルール | resolvedByRule | string | | | N | N | N | | severity判定や検証ルール |

## Indexes

| index_name | index_type | unique | columns | notes |
|---|---|---|---|---|
| pk_mw_diagnostic | PRIMARY | Y | diagnosticId | 主キー |

## Relations

N/A (inbound only)

## Notes

- Markdownファイルが正本であり、ERは理解補助の派生ビューである。
- DiagnosticはMarkdownモデルの正本情報ではなく、解析・検証結果として生成される派生情報である。
- 実装データ構造の詳細は `[[DATA-MW-CORE-DIAGNOSTIC]]`、重大度判定ルールは `[[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]]` を参照。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/models/core/diagnostics.ts | Diagnostic | type | 診断情報の内部表現 (未確認) |
