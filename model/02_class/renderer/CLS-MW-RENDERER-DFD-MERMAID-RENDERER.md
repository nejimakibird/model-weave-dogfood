---
type: class
id: CLS-MW-RENDERER-DFD-MERMAID-RENDERER
name: DfdMermaidRenderer
kind: class
package: model_weave.renderer
tags:
  - Renderer
  - DFD
---

# DfdMermaidRenderer

## Summary

解決済みの DFD / Flow Diagram グラフモデルから Mermaid.js 形式のソーステキスト（flowchart LR 等）を生成します。
Flow DiagramではDetail / Screen view、Domain group、Color Scheme適用もこの境界で扱います。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| buildDfdMermaidSource | diagram: ResolvedDiagram | string | public | Y | Mermaidソースの組み立て |
| buildFlowDiagramMermaidSource | diagram: ResolvedDiagram, colorScheme: object, flowDiagramViewMode: string | string | public | Y | Flow DiagramのDetail / Screen view用Mermaidソース生成 |
| buildFlowDiagramScreenFlowProjection | diagram: ResolvedDiagram | ResolvedDiagram | public | Y | Screen view用の派生graph生成 |

## Source Links

| path | notes |
|---|---|
| src/renderers/dfd-mermaid.ts | symbol: buildDfdMermaidSource; kind: function; DFD Mermaidソース生成 |
| src/renderers/dfd-mermaid.ts | symbol: buildFlowDiagramMermaidSource; kind: function; Flow Diagram Mermaidソース生成 |
| src/renderers/dfd-mermaid.ts | symbol: buildFlowDiagramScreenFlowProjection; kind: function; Screen view projection |
