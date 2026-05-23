---
type: data_object
id: DATA-MW-RENDERER-GRAPH-MODEL
name: 抽象グラフモデル
kind: model
data_format: object
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
| nodes | ノードリスト | object | | Y | | [[DATA-MW-RENDERER-GRAPH-NODE]] | 論理的には複数要素。グラフを構成するノード |
| edges | エッジリスト | object | | Y | | [[DATA-MW-RENDERER-GRAPH-EDGE]] | 論理的には複数要素。ノード間の接続 |
| labels | ラベルリスト | object | | Y | | [[DATA-MW-RENDERER-GRAPH-LABEL]] | 論理的には複数要素。表示ラベル |
| shape | 全体形状 | string | | N | | | 描画レイアウトのヒント |
| sourceRefs | 元モデル参照 | string | | Y | | | 論理的には複数値。グラフを構成する原典モデルのID |
| diagnostics | 構築時診断 | object | | Y | | [[DATA-MW-CORE-DIAGNOSTIC]] | 論理的には複数要素。構築時の診断情報 |

## Notes

- レンダラーの前段（中間データ）として、図面の意味的な繋がりを保持します。
- Markdownテーブル安全性のため、type列は単純型で表現し、複数性は notes / ref で表現します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/relation-resolver.ts | resolveDiagramRelations | function | 関数の戻り値となるグラフモデル構造に対応 |
