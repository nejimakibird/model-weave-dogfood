---
type: data_object
id: DATA-MW-RENDERER-GRAPH-NODE
name: 抽象グラフノード
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
| sourceRef | 原典参照 | Optional<string> | | N | | | 元となるモデルID |
| metadata | メタデータ | Record<string, any> | | N | | | 座標やスタイル等のヒント |

## Notes

- `DATA-MW-RENDERER-GRAPH-MODEL` の構成要素です。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/core/relation-resolver.ts | DiagramNode | type | グラフ構造における頂点情報の内部型 |