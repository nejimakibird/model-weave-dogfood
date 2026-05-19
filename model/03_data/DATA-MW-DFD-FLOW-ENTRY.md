---
type: data_object
id: DATA-MW-DFD-FLOW-ENTRY
name: DFDフロー解析行
tags:
  - DFD
  - Data
---

# DFDフロー解析行

## Summary

`dfd_diagram` の `Flows` テーブルから解析された1行分のデータフロー定義です。データの始点・終点と流れるデータを保持し、後続のグラフエッジ変換プロセスで使用されます。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| id | フローID | string | | Y | | | dfd_diagram.Flows.id |
| from | 始点参照 | string | | Y | | | Objects.id または解決可能な外部参照 |
| to | 終点参照 | string | | Y | | | Objects.id または解決可能な外部参照 |
| data | データ名称 | Optional<string> | | N | | | フロー上のデータ名。エッジラベルに使用 |
| notes | 補足説明 | Optional<string> | | N | | | 1行分のメモ |

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/types/models.ts | DfdFlowModel | interface | 解析済みのフロー行を保持する型 |