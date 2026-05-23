---
type: data_object
id: DATA-MW-RENDERER-GRAPH-NODE
name: 抽象グラフノード
kind: model
data_format: object
tags:
  - Renderer
  - Data
---

# 抽象グラフノード

## Summary

描画ライブラリに依存しない、グラフ構造における単一の頂点（ノード）を表します。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| id | ノードID | string | | Y | | | 一意の識別子 |
| label | 表示ラベル | string | | Y | | | |
| kind | 種別 | string | | Y | | | process / actor / table 等 |
| sourceRef | 原典参照 | string | | N | | | 任意項目。元となるモデルID |
| metadata | メタデータ | object | | N | | | 任意項目。Record構造。座標やスタイル等のヒント |

## Notes

- `DATA-MW-RENDERER-GRAPH-MODEL` の構成要素です。
- Markdownテーブル安全性のため、type列は単純型で表現し、任意性やRecord構造は required / notes で表現します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/relation-resolver.ts | DiagramNode | type | グラフ構造における頂点情報の内部型 |
