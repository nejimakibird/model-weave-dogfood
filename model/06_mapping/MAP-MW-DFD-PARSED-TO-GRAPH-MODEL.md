---
type: mapping
id: MAP-MW-DFD-PARSED-TO-GRAPH-MODEL
name: DFD論理モデルから描画用グラフモデルへの変換
kind: data_to_data
source: "[[DATA-MW-DFD-PARSED-DIAGRAM]]"
target: "[[DATA-MW-RENDERER-GRAPH-MODEL]]"
tags:
  - Renderer
  - DFD
  - Mapping
---

# DFD論理モデルから描画用グラフモデルへの変換

## Summary

解析済みのDFD専用論理モデル（DATA-MW-DFD-PARSED-DIAGRAM）から、抽象グラフモデル（DATA-MW-RENDERER-GRAPH-MODEL）への項目マッピングを定義します。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-DFD-PARSED-DIAGRAM]] | DFDとしてパースされた論理モデル |
| target | [[DATA-MW-RENDERER-GRAPH-MODEL]] | 描画エンジンの入力となるグラフ構造 |

## Mappings

| target_ref | source_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| nodes | objectEntries | DFDオブジェクトをGraphNodeへ変換 |  | Y | dfd_diagram.Objects由来 |
| edges | flows | DFDフローをGraphEdgeへ変換 |  | Y | dfd_diagram.Flows由来 |
| labels | flows | DATA-MW-DFD-FLOW-ENTRY.data をエッジラベルへ採用 |  | N | data列がある場合。source_refは親フィールドflowsを指定する |
| shape | level | DFD階層を表示制御ヒントへ変換 |  | N | Level 0/1等 |
| sourceRefs | id | 原典DFD IDとして保持 |  | Y |  |
| diagnostics | diagnostics | 診断情報を統合 |  | Y |  |



## Source Links

| path | notes |
|---|---|
| src/core/relation-resolver.ts | symbol: resolveDfdDiagramRelations; kind: function; DFDモデルの接続関係解決ロジック |
