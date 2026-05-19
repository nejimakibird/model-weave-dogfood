---
type: data_object
id: DATA-MW-DFD-OBJECT-ENTRY
name: DFDオブジェクト解析行
tags:
  - DFD
  - Data
---

# DFDオブジェクト解析行

## Summary

`dfd_diagram` の `Objects` テーブルから解析された1行分のオブジェクト定義です。ローカル定義のオブジェクト、または外部の `dfd_object` を参照しているオブジェクトの状態を保持します。Mermaidや描画エンジンに依存しない、DFD専用の論理データ構造です。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| id | オブジェクトID | string | | Y | | | dfd_diagram.Objects.id |
| label | 表示ラベル | string | | Y | | | 図面上の表示名称 |
| kind | オブジェクト種別 | string | | Y | | | external / process / datastore / other |
| ref | 原典参照 | Optional<string> | | N | | | 外部ファイル参照時のパスまたはID |
| notes | 補足説明 | Optional<string> | | N | | | 1行分のメモ |

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/types/models.ts | DfdDiagramObjectEntry | interface | 解析済みのオブジェクト行を保持する型 |