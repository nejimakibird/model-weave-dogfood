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
| id | エッジID | string | | N | | | 任意項目。未設定の場合あり |
| source | 始点ノードID | string | | Y | | | |
| target | 終点ノードID | string | | Y | | | |
| kind | 種別 | string | | Y | | | flow / association / dependency 等 |
| label | 表示ラベル | string | | N | | | 任意項目。未設定の場合あり |
| metadata | メタデータ | object | | N | | | 任意項目。Record構造。濃度（Cardinality）等の詳細情報 |

## Notes

- `DATA-MW-RENDERER-GRAPH-MODEL` の構成要素です。
- Markdownテーブル安全性のため、type列は単純型で表現し、任意性やRecord構造は required / notes で表現します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/relation-resolver.ts | DiagramEdge | type | グラフ構造における接続情報の内部型 |
