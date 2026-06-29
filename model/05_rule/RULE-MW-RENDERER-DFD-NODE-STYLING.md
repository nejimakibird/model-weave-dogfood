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

DFDオブジェクトの種別（kind）を、抽象グラフモデル（GraphNode）の種別、Mermaid node の形状、CSSクラス、Color Scheme targetへ変換するルールを定義します。本ルールは DFD レンダラーが Mermaid ソースを生成する際の表示方針の根拠となります。

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
| external | external | rectangle | dfdExternal | 外部システム・アクター。Color Schemeは target=dfd / kind=external |
| process | process | rounded rectangle | dfdProcess | 処理・機能。Color Schemeは target=dfd / kind=process |
| datastore | datastore | database | dfdDatastore | ファイル・DB・保存場所。Color Schemeは target=dfd / kind=datastore |
| other | other | rectangle | dfdOther | fallback。Color Schemeは target=dfd / kind=other |

## Diagnostics

| condition | code | severity |
|---|---|---|
| 定義外の `kind` が指定された | `unknown-dfd-object-kind` | warning |
| `kind` が未指定（空欄） | `missing-dfd-object-kind` | warning |

## Notes

- 本ルールは Mermaid ソース生成時の「設計方針」を記述するものであり、具体的な色は解決済みColor Schemeまたは組み込み配色に委ねられます。
- Mermaid における `database` 形状は `[("label")]` 構文で表現されます。
- DFD Domain subgraphの配色は object kindではなく、解決済みDomainの kind を `target=domain` として解決する。
- Color Scheme適用は描画結果の見た目を変えるだけで、DFD Markdown本文やObjects定義を書き換えない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/dfd-mermaid.ts | toMermaidNodeDeclaration | function | ノード形状とクラスの割当 |
| src/renderers/dfd-mermaid.ts | registerDfdColorClass | function | DFD object kindからColor Scheme classを割り当てる |
| src/core/color-scheme.ts | resolveColorStyle | function | target=dfd / target=domain の配色解決 |
| src/parsers/dfd-diagram-parser.ts | normalizeDfdDiagramObjectKind | function | 入力種別の正規化 |
