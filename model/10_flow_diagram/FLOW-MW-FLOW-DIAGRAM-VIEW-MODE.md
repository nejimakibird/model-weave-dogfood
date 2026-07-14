---
type: flow_diagram
id: FLOW-MW-FLOW-DIAGRAM-VIEW-MODE
name: Flow Diagram View Mode Flow
kind: screen_communication
flow_view: screen
tags:
  - ModelWeave
  - FlowDiagram
  - Viewer
---

# Flow Diagram View Mode Flow

## Summary

Flow DiagramのDetail / Screen view切替を示す代表Dogfoodサンプルです。
`flow_view: screen` は初期表示だけを指定し、toolbarでの切替やViewer stateはMarkdown本文へ保存されません。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Viewer、settings、renderer、parser領域をこのflowで使用する |

## Domains

| id | name | kind | parent | description |
|---|---|---|---|---|
| flow_view_runtime | Flow View Runtime | viewer | viewer_ui | この図内だけのlocal domain。Domain Sourcesを上書きせず補助groupを追加する |

## Objects

| id | label | kind | ref | domain | notes |
|---|---|---|---|---|---|
| user | User | external | | viewer_ui | toolbarを操作する利用者 |
| viewer | Viewer Main Frame | screen | [[SCR-MW-VIEWER-MAIN-FRAME]] | viewer_ui | Flow view selectorを表示する |
| settings | Settings | context | [[DATA-MW-PLUGIN-SETTINGS]] | settings_configuration | defaultFlowDiagramViewModeを渡す |
| parser | Flow Diagram Parser | process | [[PROC-MW-FLOW-DIAGRAM-PARSE]] | parser_resolver | flow_viewとDomain Sourcesを解析する |
| viewState | Flow Diagram View State | work_object | [[DATA-MW-FLOW-DIAGRAM-VIEW-STATE]] | flow_view_runtime | filePathごとのmodeとinitializationKey |
| renderer | Flow Diagram Renderer | process | [[PROC-MW-FLOW-DIAGRAM-RENDER]] | renderer_area | effective modeを受け取る |
| screenProjection | Screen Projection | process | [[RULE-MW-FLOW-DIAGRAM-SCREEN-PROJECTION]] | renderer_area | Screen view用の派生graphを作る |
| mermaid | Mermaid Source | store | [[DATA-MW-RENDERER-MERMAID-SOURCE]] | renderer_area | 描画用の派生出力 |

## Flows

| id | from | to | kind | trigger | data | condition | notes |
|---|---|---|---|---|---|---|---|
| F01 | settings | viewState | config | Open Flow Diagram | defaultFlowDiagramViewMode | no valid flow_view | frontmatter未指定時だけ初期modeに使う |
| F02 | parser | viewState | derive | Parse frontmatter | flow_view | valid flow_view | 有効なfrontmatter指定は初期modeに使う |
| F03 | viewer | viewState | change | Flow view selector changed | selected mode | user changes toolbar | Markdown本文は変更しない |
| F04 | viewState | renderer | render | Render diagram | effective mode | mode is detail or screen | rendererはfrontmatterを再評価しない |
| F05 | renderer | screenProjection | derive | Build source | resolved diagram | mode is screen | visible nodeへ射影する |
| F06 | screenProjection | mermaid | generate | Projection complete | projected graph | visible nodes exist | screen / external / actor / user / context / message を残す |
| F07 | renderer | mermaid | generate | Build detail source | detail graph | mode is detail | 内部nodeを含むDetail view |
| F08 | mermaid | viewer | refresh | Mermaid rendered | preview result | after render | viewportをresetしてFitする |
| F09 | user | viewer | click | Select Flow view | toolbar event | Flow Diagram displayed | selector値とrenderer modeを同期する |

## Notes

- Screen viewでは `viewer`、`settings`、`user` などのvisible nodeを残し、`parser`、`renderer`、`screenProjection` などの内部nodeは経路探索に使われる。
- Detail viewでは内部nodeも表示される。
- Domain Sourcesは外部Domain定義を読み込み、local `## Domains` はこのファイル内だけの補助Domainを追加する。
- Domain nameはgroup labelに、Domain parentはnested groupに、Domain kindは `target=domain` のColor Scheme keyに使われる。
- projection結果、toolbar state、viewport stateはMarkdown正本へ保存しない。
- arbitrary projection definitions、Communication view、state machine生成、transition coverage生成はfutureである。

## Source Links

| path | notes |
|---|---|
| src/core/flow-diagram-view-mode.ts | FlowDiagramViewModeState and initializationKey |
| src/views/modeling-preview-view.ts | Flow view selector, effective mode sync, viewport reset |
| src/renderers/dfd-mermaid.ts | buildFlowDiagramScreenFlowProjection and Flow Diagram Mermaid source |
| src/parsers/dfd-diagram-parser.ts | flow_view, Domain Sources, local Domains parsing |
