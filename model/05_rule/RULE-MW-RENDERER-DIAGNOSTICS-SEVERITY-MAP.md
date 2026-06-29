---
type: rule
id: RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP
name: 診断情報深刻度判定ルール
kind: diagnostics_rule
tags:
  - Renderer
  - Core
  - Diagnostic
---

# 診断情報深刻度判定ルール

## Summary

Model Weave 内部で発生した `ValidationWarning` の `severity` を、Viewer上の Notes / Warnings / Errors 表示へ分類するためのルール。
現行実装では `info` / `warning` / `error` が有効値であり、`note` は保存値ではなくUI表示ラベルとして扱う。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| warning | [[DATA-MW-CORE-DIAGNOSTIC]] | DiagnosticsCollector | Y | 判定対象の診断情報 |

## Conditions

| id | expression | severity | message | notes |
|---|---|---|---|---|
| C1 | warning.severity が `info` | info | そのまま扱う | normalizeDiagnosticSeverity |
| C2 | warning.severity が `error` | error | そのまま扱う | normalizeDiagnosticSeverity |
| C3 | error対象コードまたは必須フィールド | error | errorへ正規化する | frontmatter / schema / table / 必須名 |
| C4 | その他のwarning | warning | warningとして表示する | fallback |

## Severity Mapping

以下のマッピングに基づき、深刻度を正規化します。

| code / condition | severity | condition / description |
|---|---|---|
| `frontmatter-parse-error` | error | frontmatterの構文が不正 |
| `unknown-schema` | error | `schema` または `type` が非対応 |
| `invalid-table-column` | error | テーブルヘッダーが期待値と不一致 |
| `invalid-table-row` | error | テーブル行の列数が不一致 |
| `missing-name` | error | 必須の `name` が存在しない |
| `missing-kind` | error | 必須の `kind` が存在しない |
| `invalid-structure` with required identity field | error | `type` / `id` / `name` / `logical_name` / `physical_name` / `kind` の不正 |
| `unresolved-reference` | warning | 参照先が見つからない |
| `class-relation-target-not-diagram-compatible` | warning | Class Diagram描画対象外の参照 |
| `duplicate-mapping-target-member` | warning | mapping target memberが重複 |
| other `ValidationWarningCode` | emitted severity | 生成元が指定したseverityを維持 |

## Notes

- 上記のテーブルは Model Weave の正式な `Logic` セクションではなく、実装における `normalizeDiagnosticSeverity` 関数の振る舞いを定義する補足表である。
- `ValidationWarning.severity` の有効値は `info` / `warning` / `error` である。Viewerでは `info` を Notes として表示する。
- Source Linksのavailable / missingなどの表示状態はSource Links UIのstatusであり、`ValidationWarningCode` ではない。
- Quick FixやMarkdown自動修正は現行srcで確認できないため、このルールではfuture扱いとする。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/current-file-diagnostics.ts | normalizeDiagnosticSeverity | function | 深刻度の正規化ロジック |
| src/types/models.ts | ValidationWarning | type | 診断情報の基本構造 |
| src/types/warnings.ts | VALIDATION_WARNING_SEVERITIES | constant | severityの有効値 |
| src/types/warnings.ts | VALIDATION_WARNING_CODES | constant | codeの有効値 |
