---
type: rule
id: RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP
name: 診断情報深刻度判定ルール
tags:
  - Renderer
  - Core
  - Diagnostic
---

# 診断情報深刻度判定ルール

## Summary

Model Weave 内部で発生したバリデーション警告（ValidationWarning）の診断コードを、ビューアー上での表示種別（severity）に変換するためのマッピング基準を定義します。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| warning | [[DATA-MW-CORE-DIAGNOSTIC]] | DiagnosticsCollector | Y | 判定対象の診断情報 |

## Severity Mapping

以下のマッピングに基づき、深刻度を正規化します。

| code | severity | condition / description |
|---|---|---|
| `frontmatter-parse-error` | error | フロントマターの構文が不正で以降の解析が不能 |
| `unknown-schema` | error | `type` または `schema` が未定義または非対応 |
| `invalid-table-column` | error | セクション内のテーブルヘッダーが期待値と不一致 |
| `invalid-table-row` | error | 列数の不一致によりデータが読み取れない |
| `missing-name` | error | 必須の `name` フィールドが存在しない |
| `unresolved-reference` | warning | Wikilink または ID による参照先が見つからない |
| `unresolved-mapping-source-ref` | warning | マッピングのソースパスが Fields に存在しない |
| `unresolved-mapping-target-ref` | warning | マッピングのターゲットパスが Fields に存在しない |
| `unsupported-render-mode-fallback` | warning | 指定された描画モードが非対応で自動切替された |
| `dfd-custom-render-mode-fallback` | warning | DFD で `custom` が指定され `mermaid` へ強制された |
| `source-link-unverified` | warning | `Source Links` の対応関係が未確認 |
| `optional-section-ignored` | info | 定義外の任意セクションがスキップされた |
| `dogfood-review-note` | note | Dogfood 運用上のレビューコメントや設計メモ |

## Notes

- 上記のテーブルは Model Weave の正式な `Logic` セクションではなく、実装における `normalizeDiagnosticSeverity` 関数の振る舞いを定義する補足表です。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/current-file-diagnostics.ts | normalizeDiagnosticSeverity | function | 深刻度の正規化ロジック |
| src/types/models.ts | ValidationWarning | type | 診断情報の基本構造 |