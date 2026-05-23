---
type: data_object
id: DATA-MW-RENDERER-GRAPH-MODEL
name: 抽象グラフモデル
tags:
  - Renderer
  - Data
---

# 抽象グラフモデル

## Summary

特定の描画エンジン（Mermaid等）に依存しない、ノードとエッジから構成される図面データの論理構造です。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| nodes | ノードリスト | Array<DATA-MW-RENDERER-GRAPH-NODE> | | Y | | [[DATA-MW-RENDERER-GRAPH-NODE]] | |
| edges | エッジリスト | Array<DATA-MW-RENDERER-GRAPH-EDGE> | | Y | | [[DATA-MW-RENDERER-GRAPH-EDGE]] | |
| labels | ラベルリスト | Array<DATA-MW-RENDERER-GRAPH-LABEL> | | Y | | [[DATA-MW-RENDERER-GRAPH-LABEL]] | |
| shape | 全体形状 | string | | N | | | 描画レイアウトのヒント |
| sourceRefs | 元モデル参照 | Array<string> | | Y | | | グラフを構成する原典モデルのID |
| diagnostics | 構築時診断 | Array<DATA-MW-CORE-DIAGNOSTIC> | | Y | | [[DATA-MW-CORE-DIAGNOSTIC]] | |

## Notes

- レンダラーの前段（中間データ）として、図面の意味的な繋がりを保持します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/relation-resolver.ts | resolveDiagramRelations | function | 関数の戻り値となるグラフモデル構造に対応 |