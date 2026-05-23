---
type: class
id: CLS-MW-RENDERER-CLASS-ER-MERMAID-RENDERER
name: ClassErMermaidRenderer
kind: class
package: model_weave.renderer
tags:
  - Renderer
  - Class
  - ER
---

# ClassErMermaidRenderer

## Summary

クラスダイアグラムおよび ER ダイアグラムのグラフ構造を Mermaid.js 形式のソーステキストに変換します。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| buildClassOverviewMermaidSource | diagram: ResolvedDiagram | string | public | Y | クラス図概要の生成 |
| buildErOverviewMermaidSource | diagram: ResolvedDiagram | string | public | Y | ER図概要の生成 |

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/class-er-mermaid.ts | buildClassOverviewMermaidSource | function | クラス図Mermaidソース生成 |