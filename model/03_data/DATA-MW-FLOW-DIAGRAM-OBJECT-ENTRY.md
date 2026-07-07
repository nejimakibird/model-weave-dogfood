---
type: data_object
id: DATA-MW-FLOW-DIAGRAM-OBJECT-ENTRY
name: Flow Diagramオブジェクト解析行
kind: parsed_entry
data_format: object
tags:
  - FlowDiagram
  - Data
---

# Flow Diagramオブジェクト解析行

## Summary

`flow_diagram` の `## Objects` テーブルから解析された1行分のオブジェクト定義です。
Flow Diagramでは `id` が必須で、flow endpointはこのローカルIDを参照する。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| id | オブジェクトID | string | | Y | | | `Objects.id`。Flows.from / Flows.to のendpoint |
| label | 表示ラベル | string | | N | | | 図上の表示名称 |
| kind | オブジェクト種別 | string | | N | | | screen / process / app_process / context / work_object / session / store / datastore / external / unknown |
| ref | 原典参照 | string | | N | | | 任意。解決できる場合はhover preview / click targetに使われる |
| domain | Domain | string | | N | | | Domain groupとtarget=domain配色に使う |
| notes | 補足説明 | string | | N | | | 1行分のメモ |
| rowIndex | 行番号 | number | | Y | | | parser上の行位置 |

## Notes

- `kind` が未指定または未知の場合でも、描画を壊さないように `unknown` 相当のfallbackが使われる。
- Flow Diagramの `ref` はオブジェクト解決とPreview上の参照支援に使われるが、endpoint判定は `id` を使う。

## Source Links

| path | notes |
|---|---|
| src/types/models.ts | symbol: DfdDiagramObjectEntry; kind: interface; Flow Diagram Objects行にも使われる型 |
| src/types/models.ts | symbol: FlowDiagramObjectKind; kind: type; Flow Diagram object kind |
| src/parsers/dfd-diagram-parser.ts | symbol: parseFlowDiagramFile; kind: function; Objects table解析 |
| src/renderers/dfd-mermaid.ts | symbol: toFlowDiagramMermaidShape; kind: function; kindからMermaid shapeを選ぶ |
