---
type: app_process
id: PROC-MW-FLOW-DIAGRAM-RENDER
name: Flow Diagram描画
process_type: renderer
tags:
  - ModelWeave
  - FlowDiagram
  - Renderer
  - BusinessFlow
---

# Flow Diagram描画

## Summary

解析済みFlow Diagramを `ResolvedDiagram` に解決し、MermaidベースのDetail / Screen viewとして表示する処理。
現行実装では `schema: flow_diagram` を `renderDiagramModel` が検出し、Viewerから渡されたeffective Flow view modeを使ってFlow Diagram専用Mermaid sourceを生成する。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| parsedFlowDiagram | [[DATA-MW-FLOW-DIAGRAM-PARSED-DIAGRAM]] | [[PROC-MW-FLOW-DIAGRAM-PARSE]] | Y | FlowDiagramModel |
| vaultIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | [[DFD-MW-OBJ-VAULT-INDEX]] | Y | refとFlows.data Wikilink解決に使う |
| colorScheme | [[DATA-MW-VIEWER-STATE]] | Viewer | N | resolvedColorScheme |
| flowViewState | [[DATA-MW-FLOW-DIAGRAM-VIEW-STATE]] | [[PROC-MW-FLOW-DIAGRAM-VIEW-SWITCH]] | Y | effective detail / screen |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| resolvedDiagram | [[DATA-MW-RENDERER-GRAPH-MODEL]] | [[DFD-MW-OBJ-RENDERER]] | source上のResolvedDiagram |
| mermaidSource | [[DATA-MW-RENDERER-MERMAID-SOURCE]] | Viewer | Flow Diagram Mermaid source |
| projectedDiagram | [[DATA-MW-RENDERER-GRAPH-MODEL]] | Viewer | Screen view時の派生ResolvedDiagram |
| warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | Diagnostics Panel | endpoint / data reference診断 |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | renderer_area | Flow Diagram描画を開始する | start | parsedFlowDiagram | | | | | schemaを確認する |
| resolveRelations | relation_resolution | endpointとdata参照を解決する | process | parsedFlowDiagram, vaultIndex | resolvedDiagram, warnings | | [[PROC-MW-RESOLVER-RESOLVE-DIAGRAM]] | | from / toはローカルObjects.id |
| selectRenderer | renderer_area | Flow Diagram rendererへ分岐する | decision | resolvedDiagram | | [[RULE-MW-DFD-MERMAID-ONLY]] | | | schema: flow_diagramでDFD Mermaid系へ渡す |
| selectViewMode | viewer_ui | effective view modeを受け取る | process | flowViewState | | [[RULE-MW-FLOW-DIAGRAM-VIEW-STATE]] | | [[SCR-MW-VIEWER-MAIN-FRAME]] | detail / screen |
| projectScreenFlow | renderer_area | Screen viewへ射影する | decision | resolvedDiagram, flowViewState | projectedDiagram | [[RULE-MW-FLOW-DIAGRAM-SCREEN-PROJECTION]] | | | screen時だけ派生graphを作る |
| buildMermaid | renderer_area | Mermaid sourceを生成する | process | projectedDiagram, colorScheme | mermaidSource | | | | Flow Diagram専用source生成 |
| attachInteractions | viewer_ui | hover / click targetを付与する | process | resolvedDiagram | | | | [[SCR-MW-VIEWER-MAIN-FRAME]] | nodeとedge labelにmetadataを持たせる |
| renderPreview | viewer_ui | Previewへ表示する | screen | mermaidSource | | | | [[SCR-MW-VIEWER-MAIN-FRAME]] | Detail / Screen view |
| end | viewer_ui | 描画を終了する | end | mermaidSource | | | | | |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | resolveRelations | | 解決 | |
| resolveRelations | selectRenderer | | renderer選択 | |
| selectRenderer | selectViewMode | `schema === "flow_diagram"` | Flow Diagram | |
| selectViewMode | projectScreenFlow | `mode === "screen"` | Screen | visible nodeへ射影 |
| selectViewMode | buildMermaid | `mode === "detail"` | Detail | 射影しない |
| projectScreenFlow | buildMermaid | | Mermaid | |
| buildMermaid | attachInteractions | | interactions | |
| attachInteractions | renderPreview | | Preview表示 | |
| renderPreview | end | | 完了 | |

## Notes

- Flow DiagramはDetail viewとScreen viewを描画する。
- Screen viewではscreen / external / actor / user / context / messageをvisible nodeとして残し、内部node経由の到達関係をprojected edgeにする。
- Domain Sourcesとlocal DomainsからDomain nameをgroup label、parentをnested group、kindを `target=domain` のColor Scheme keyとして使う。
- Domain定義がない場合はraw `Objects.domain` によるsynthetic groupingを維持する。
- arbitrary projection definitions、Communication view、generic folding、transition coverage生成はfutureであり、このprocessでは実装済み扱いにしない。
- `src/renderers/flow-renderer.ts` は別系統の汎用Diagram kind用であり、`type: flow_diagram` の主要経路ではない。

## Source Links

| path | notes |
|---|---|
| src/renderers/diagram-renderer.ts | Steps: selectRenderer. schema: flow_diagram dispatch |
| src/core/relation-resolver.ts | Steps: resolveRelations. Flow Diagram endpoint / data reference解決 |
| src/core/flow-diagram-view-mode.ts | Steps: selectViewMode. effective Flow view mode |
| src/renderers/dfd-mermaid.ts | Steps: projectScreenFlow, buildMermaid, attachInteractions. Screen projection and Mermaid source |
| src/views/modeling-preview-view.ts | Steps: selectViewMode, renderPreview. Flow view selectorとApplied Color Scheme連携 |
