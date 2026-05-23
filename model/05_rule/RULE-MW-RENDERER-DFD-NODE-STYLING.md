---
type: rule
id: RULE-MW-RENDERER-DFD-NODE-STYLING
name: DFDノードスタイリングルール
kind: renderer_rule
tags:
  - DFD
  - Renderer
  - Styling
---

# DFDノードスタイリングルール

## Summary

DFDオブジェクトの種別（kind）を、抽象グラフモデル（GraphNode）の種別および Mermaid node の形状・CSSクラスへ変換するルールを定義します。本ルールは DFD レンダラーが Mermaid ソースを生成する際の表示方針の根拠となります。

## Conditions

- 対象モデルの `type` が `dfd_diagram` であること。
- [[DATA-MW-DFD-OBJECT-ENTRY]] の `kind` 属性を入力とする。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| input_kind | string | [[DATA-MW-DFD-OBJECT-ENTRY]] | Y | 解析済みのオブジェクト種別 |

## Logic

以下のマッピング表に基づき、レンダリング時の表現を決定します。

| input_kind | graph_kind | mermaid_shape | mermaid_class | notes |
|---|---|---|---|---|
| external | external | rectangle | dfdExternal | 外部システム・アクター |
| process | process | rounded rectangle | dfdProcess | 処理・機能 |
| datastore | datastore | database | dfdDatastore | ファイル・DB・保存場所 |
| other | other | rectangle | dfdOther | fallback |

## Diagnostics

| condition | code | severity |
|---|---|---|
| 定義外の `kind` が指定された | `unknown-dfd-object-kind` | warning |
| `kind` が未指定（空欄） | `missing-dfd-object-kind` | warning |

## Notes

- 本ルールは Mermaid ソース生成時の「設計方針」を記述するものであり、具体的な `classDef` のカラーコードなどは renderer の実装（CSS）に委ねられます。
- Mermaid における `database` 形状は `[("label")]` 構文で表現されます。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/dfd-mermaid.ts | toMermaidNodeDeclaration | function | ノード形状とクラスの割当 |
| src/parsers/dfd-diagram-parser.ts | normalizeDfdDiagramObjectKind | function | 入力種別の正規化 |
