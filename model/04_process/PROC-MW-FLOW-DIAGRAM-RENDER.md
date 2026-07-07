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

解析済みFlow Diagramを `ResolvedDiagram` に解決し、MermaidベースのInternal Detail Viewとして表示する処理。
現行実装では `schema: flow_diagram` を `renderDiagramModel` が検出し、`renderDfdMermaidDiagram` 経由でFlow Diagram専用Mermaid sourceを生成する。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| parsedFlowDiagram | [[DATA-MW-FLOW-DIAGRAM-PARSED-DIAGRAM]] | [[PROC-MW-FLOW-DIAGRAM-PARSE]] | Y | FlowDiagramModel |
| vaultIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | [[DFD-MW-OBJ-VAULT-INDEX]] | Y | refとFlows.data Wikilink解決に使う |
| colorScheme | [[DATA-MW-VIEWER-STATE]] | Viewer | N | resolvedColorScheme |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| resolvedDiagram | [[DATA-MW-RENDERER-GRAPH-MODEL]] | [[DFD-MW-OBJ-RENDERER]] | source上のResolvedDiagram |
| mermaidSource | [[DATA-MW-RENDERER-MERMAID-SOURCE]] | Viewer | Flow Diagram Mermaid source |
| warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | Diagnostics Panel | endpoint / data reference診断 |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | renderer_area | Flow Diagram描画を開始する | start | parsedFlowDiagram | | | | | schemaを確認する |
| resolveRelations | relation_resolution | endpointとdata参照を解決する | process | parsedFlowDiagram, vaultIndex | resolvedDiagram, warnings | | [[PROC-MW-RESOLVER-RESOLVE-DIAGRAM]] | | from / toはローカルObjects.id |
| selectRenderer | renderer_area | Flow Diagram rendererへ分岐する | decision | resolvedDiagram | | [[RULE-MW-DFD-MERMAID-ONLY]] | | | schema: flow_diagramでDFD Mermaid系へ渡す |
| buildMermaid | renderer_area | Mermaid sourceを生成する | process | resolvedDiagram, colorScheme | mermaidSource | | | | Flow Diagram専用source生成 |
| attachInteractions | viewer_ui | hover / click targetを付与する | process | resolvedDiagram | | | | [[SCR-MW-VIEWER-MAIN-FRAME]] | nodeとedge labelにmetadataを持たせる |
| renderPreview | viewer_ui | Previewへ表示する | screen | mermaidSource | | | | [[SCR-MW-VIEWER-MAIN-FRAME]] | Internal Detail View |
| end | viewer_ui | 描画を終了する | end | mermaidSource | | | | | |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | resolveRelations | | 解決 | |
| resolveRelations | selectRenderer | | renderer選択 | |
| selectRenderer | buildMermaid | `schema === "flow_diagram"` | Flow Diagram | |
| buildMermaid | attachInteractions | | interactions | |
| attachInteractions | renderPreview | | Preview表示 | |
| renderPreview | end | | 完了 | |

## Notes

- Flow Diagramは現行MVPでInternal Detail Viewのみを描画する。
- node groupには `Objects.domain` が使われ、Color Schemeは主に `target=domain` として反映される。
- Surface View、Communication View、folding、projection、transition coverage生成はfutureであり、このprocessでは実装済み扱いにしない。
- `src/renderers/flow-renderer.ts` は別系統の汎用Diagram kind用であり、v0.1.19の `type: flow_diagram` MVPの主要経路ではない。

## Source Links

| path | notes |
|---|---|
| src/renderers/diagram-renderer.ts | Steps: selectRenderer. schema: flow_diagram dispatch |
| src/core/relation-resolver.ts | Steps: resolveRelations. Flow Diagram endpoint / data reference解決 |
| src/renderers/dfd-mermaid.ts | Steps: buildMermaid, attachInteractions. buildFlowDiagramMermaidSourceとhover metadata |
| src/views/modeling-preview-view.ts | Steps: renderPreview. Preview表示とApplied Color Scheme連携 |
