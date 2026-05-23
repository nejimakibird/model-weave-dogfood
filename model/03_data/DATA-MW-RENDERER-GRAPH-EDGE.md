---
type: data_object
id: DATA-MW-RENDERER-GRAPH-EDGE
name: 抽象グラフエッジ
tags:
  - Renderer
  - Data
---

# 抽象グラフエッジ

## Summary

描画ライブラリに依存しない、グラフ構造におけるノード間の接続（エッジ）を表します。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| id | エッジID | Optional<string> | | N | | | |
| source | 始点ノードID | string | | Y | | | |
| target | 終点ノードID | string | | Y | | | |
| kind | 種別 | string | | Y | | | flow / association / dependency 等 |
| label | 表示ラベル | Optional<string> | | N | | | |
| metadata | メタデータ | Record<string, any> | | N | | | 濃度（Cardinality）等の詳細情報 |

## Notes

- `DATA-MW-RENDERER-GRAPH-MODEL` の構成要素です。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/relation-resolver.ts | DiagramEdge | type | グラフ構造における接続情報の内部型 |