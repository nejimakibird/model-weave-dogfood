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

解決済みの DFD グラフモデルから Mermaid.js 形式のソーステキスト（flowchart LR 等）を生成します。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| buildDfdMermaidSource | diagram: ResolvedDiagram | string | public | Y | Mermaidソースの組み立て |

## Source Links

| path | notes |
|---|---|
| src/renderers/dfd-mermaid.ts | symbol: buildDfdMermaidSource; kind: function; DFD Mermaidソース生成 |
