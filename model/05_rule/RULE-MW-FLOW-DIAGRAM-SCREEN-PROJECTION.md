---
type: rule
id: RULE-MW-FLOW-DIAGRAM-SCREEN-PROJECTION
name: Flow Diagram Screen projection rule
kind: renderer_rule
tags:
  - FlowDiagram
  - Renderer
  - Rule
---

# Flow Diagram Screen projection rule

## Summary

Flow Diagramのeffective modeが `screen` のとき、Detail viewの内部nodeを経由した流れをScreen view向けに射影するルールです。
この射影はRenderer内の派生表示であり、Markdown正本を書き換えません。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| resolvedDiagram | [[DATA-MW-RENDERER-GRAPH-MODEL]] | Renderer | Y | Flow Diagramの解決済みgraph |
| effectiveMode | [[DATA-MW-FLOW-DIAGRAM-VIEW-STATE]] | Viewer | Y | detail / screen |

## Conditions

| id | condition | result | notes |
|---|---|---|---|
| C1 | effectiveMode is detail | Detail viewを使う | 射影しない |
| C2 | effectiveMode is screen | Screen projectionを試す | visible nodeを抽出する |
| C3 | visible node is empty | Detail viewへfallback | 図を空にしない |
| C4 | visible node exists | projected edgeを作る | 内部node経由の到達関係を集約する |

## Projection Rules

| rule | value | notes |
|---|---|---|
| visible kinds | screen, external, actor, user, context, message | source実装の isScreenFlowVisibleNode に対応 |
| hidden kinds | process, app_process, work_object, session, store, datastore, data, api, service, handler, unknown | 内部nodeとして経路探索に使う |
| edge dedupe | from / to pair | 同一ペアのprojected edgeをまとめる |
| label priority | condition, trigger, flowKind | 最初に存在する値をedge labelに使う |
| label merge | slash-separated text | 複数labelは重複を除いて結合する |
| max label length | 80 | 長いlabelは省略する |
| domain and color | preserve | Domain hierarchyとColor Scheme適用を維持する |

## Notes

- Screen projectionはv0.1.20で実装済みのFlow Diagram表示modeである。
- 任意のprojection定義言語、Communication view、state machine生成、transition coverage生成、汎用folding editorはfutureとして扱う。
- projected edgeはRendererの派生edgeであり、`## Flows` 行としてMarkdownへ保存しない。
- Flow Diagram node colorは `target=flow_diagram` と `Objects.kind` を使い、Domain group colorは `target=domain` とDomain kindを使う。

## Source Links

| path | notes |
|---|---|
| src/renderers/dfd-mermaid.ts | symbol: buildFlowDiagramScreenFlowProjection; kind: function; Screen projectionの本体 |
| src/renderers/dfd-mermaid.ts | symbol: isScreenFlowVisibleNode; kind: function; visible kind判定 |
| src/renderers/dfd-mermaid.ts | symbol: buildScreenFlowProjectionEdgeLabel; kind: function; edge label優先順 |
| src/renderers/dfd-mermaid.ts | symbol: mergeScreenFlowLabels; kind: function; edge label結合 |
