---
type: data_object
id: DATA-MW-FLOW-DIAGRAM-FLOW-ENTRY
name: Flow Diagramフロー解析行
kind: parsed_entry
data_format: object
tags:
  - FlowDiagram
  - Data
---

# Flow Diagramフロー解析行

## Summary

`flow_diagram` の `## Flows` テーブルから解析された1行分の通信・処理フロー定義です。
MVPでは `from` と `to` がローカル `Objects.id` を参照し、`trigger`、`kind`、`data` からedge labelを組み立てる。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| id | フローID | string | | N | | | `Flows.id` |
| from | 始点 | string | | Y | | | ローカル `Objects.id` |
| to | 終点 | string | | Y | | | ローカル `Objects.id` |
| kind | フロー種別 | string | | N | | | call / event / commandなどの意味ラベル |
| trigger | トリガー | string | | N | | | edge label候補 |
| data | データ | string | | N | | | plain textはラベル。Wikilinkは参照診断対象 |
| dataRef | データ参照 | ParsedReferenceValue | | N | | | `data` が参照として解析できる場合 |
| condition | 条件 | string | | N | | | guard条件 |
| notes | 補足説明 | string | | N | | | 1行分のメモ |
| rowIndex | 行番号 | number | | Y | | | parser上の行位置 |

## Notes

- `Flows.data` のplain textは解決を要求しない。未解決診断はWikilinkなど参照形式の場合に出る。
- `from` / `to` に外部モデルIDやWikilinkを入れる形式ではない。ローカル `Objects.id` と一致しない場合はFlow Diagram endpoint診断になる。

## Source Links

| path | notes |
|---|---|
| src/types/models.ts | symbol: DfdFlowModel; kind: interface; Flow Diagram Flows行にも使われる型 |
| src/parsers/dfd-diagram-parser.ts | symbol: FLOW_DIAGRAM_FLOW_HEADERS; kind: constant; `id / from / to / kind / trigger / data / condition / notes` |
| src/core/relation-resolver.ts | symbol: resolveDfdFlowDataReferenceWarnings; kind: function; Flows.data Wikilink診断 |
| src/core/relation-resolver.ts | symbol: buildFlowDiagramEdgeLabel; kind: function; edge label組み立て |
