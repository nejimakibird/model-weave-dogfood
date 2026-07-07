---
type: data_object
id: DATA-MW-RENDERER-GRAPH-EDGE
name: 抽象グラフエッジ
kind: model
data_format: object
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
| id | エッジID | string | | N | id | | 任意項目。未設定の場合あり |
| source | 始点ノードID | string | | Y | source | | 始点のnode id |
| target | 終点ノードID | string | | Y | target | | 終点のnode id |
| kind | 種別 | string | | N | kind | | RelationKind |
| label | 表示ラベル | string | | N | label | | 任意項目。未設定の場合あり |
| metadata | メタデータ | object | | N | metadata | | CardinalityやDFD flow情報など |

## Notes

- `DATA-MW-RENDERER-GRAPH-MODEL` の構成要素です。
- Markdownテーブル安全性のため、type列は単純型で表現し、任意性やRecord構造は required / notes で表現します。

## Source Links

| path | notes |
|---|---|
| src/types/models.ts | symbol: DiagramEdge; kind: interface; グラフ構造における接続情報 |
| src/core/relation-resolver.ts | symbol: resolveDiagramRelations; kind: function; edge解決 |
