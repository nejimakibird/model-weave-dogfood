---
type: mapping
id: MAP-MW-DFD-FLOW-TO-GRAPH-EDGE
name: DFDフロー行からグラフエッジへの変換
source: "[[DATA-MW-DFD-FLOW-ENTRY]]"
target: "[[DATA-MW-RENDERER-GRAPH-EDGE]]"
tags:
  - DFD
  - Renderer
  - Mapping
---

# DFDフロー行からグラフエッジへの変換

## Summary

DFDの `## Flows` テーブルから解析された1行分のデータ（[[DATA-MW-DFD-FLOW-ENTRY]]）を、レンダラー内部で使用する抽象的なグラフ接続情報（[[DATA-MW-RENDERER-GRAPH-EDGE]]）へ変換する詳細な項目マッピングを定義します。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-DFD-FLOW-ENTRY]] | DFD解析済みのフロー行 |
| target | [[DATA-MW-RENDERER-GRAPH-EDGE]] | 抽象グラフモデルのエッジ |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| id | id | そのまま設定。空の場合は from/to/data から生成 |  | N | フローの一意識別子 |
| from | source | Objects.id または ref を GraphNode.id へ解決 |  | Y | 始点ノードIDへの解決 |
| to | target | Objects.id または ref を GraphNode.id へ解決 |  | Y | 終点ノードIDへの解決 |
| data | label | Mermaid edge label として使える文字列へサニタイズ |  | N | 表示用データ名。空の場合はラベルなし |
| notes | metadata | 補足情報として保持 |  | N | 実装上は metadata.notes へ格納 |

## Diagnostics Mapping

| condition | code | severity | notes |
|---|---|---|---|
| `from` が `Objects` で定義された ID または参照に解決できない | unresolved-reference | warning | 接続先不明 |
| `to` が `Objects` で定義された ID または参照に解決できない | unresolved-reference | warning | 接続先不明 |

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/relation-resolver.ts | resolveDfdDiagramRelations | function | フロー端点の解決とエッジ生成の主導 |
| src/renderers/dfd-mermaid.ts | sanitizeMermaidEdgeLabel | function | ラベルのサニタイズ処理 |
| src/types/models.ts | DfdFlowModel | type | 入力データ構造 |
| src/types/models.ts | DiagramEdge | type | 出力データ構造 |