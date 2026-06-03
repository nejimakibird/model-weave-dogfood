---
type: dfd_object
id: DFD-MW-OBJ-RENDERER
name: Renderer
kind: process
tags:
  - DFD
  - Renderer
---

# Renderer

## Summary

解決済みモデルやBusiness Flow入力を受け取り、Model Weave Viewerに表示するPreview DOM / Mermaid source / Mermaid描画を生成する描画境界。
関係解決そのものは Resolver 境界に分離し、Rendererは受け取った ResolvedDiagram や app_process Business Flow を描画する。

## Details

| key | value | notes |
|---|---|---|
| owner | Renderer Package | renderers 配下 |
| diagram_rendering | renderDiagramModel | diagram kindとrenderModeに応じた表示 |
| mermaid_source | [[DATA-MW-RENDERER-MERMAID-SOURCE]] | DFD / Class / ER Mermaid出力 |
| business_flow | renderAppProcessBusinessFlow | app_process Steps / Flows表示 |
| preview | modeling-preview-view.ts | Viewer内でrendererを呼び出す |
| related_process | [[PROC-MW-RENDERER-BUILD-GRAPH-MODEL]] | グラフモデル構築プロセス |
| related_process | [[PROC-MW-RENDERER-GENERATE-MERMAID-SOURCE]] | Mermaidソース生成プロセス |

## Source Links

| path | notes |
|---|---|
| src/renderers/diagram-renderer.ts | diagram renderer dispatch |
| src/renderers/dfd-mermaid.ts | DFD Mermaid source / preview |
| src/renderers/class-er-mermaid.ts | Class / ER Mermaid rendering |
| src/renderers/app-process-business-flow.ts | Business Flow Mermaid rendering |
| src/views/modeling-preview-view.ts | Preview view renderer usage |
