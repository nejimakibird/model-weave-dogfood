---
type: app_process
id: PROC-MW-FLOW-DIAGRAM-VIEW-SWITCH
name: Flow Diagram表示切替
process_type: viewer
tags:
  - ModelWeave
  - FlowDiagram
  - Viewer
  - BusinessFlow
---

# Flow Diagram表示切替

## Summary

Flow DiagramのDetail / Screen viewについて、frontmatterとSettingsから初期modeを解決し、toolbar選択をViewer stateとして保持してrendererへ渡す処理です。
toolbar切替はMarkdown本文を書き換えず、表示modeとviewportだけを更新します。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| parsedFlowDiagram | [[DATA-MW-FLOW-DIAGRAM-PARSED-DIAGRAM]] | [[PROC-MW-FLOW-DIAGRAM-PARSE]] | Y | flowView / flowViewRaw / flowViewSpecified |
| settings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | Y | defaultFlowDiagramViewMode |
| currentState | [[DATA-MW-FLOW-DIAGRAM-VIEW-STATE]] | Viewer | N | file scoped state |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| effectiveMode | [[DATA-MW-FLOW-DIAGRAM-VIEW-STATE]] | [[PROC-MW-FLOW-DIAGRAM-RENDER]] | rendererへ渡すdetail / screen |
| viewportReset | [[DATA-MW-VIEWPORT-STATE]] | Viewer | View切替または再初期化時に破棄してFitする |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | viewer_ui | Flow Diagram表示を開始する | start | parsedFlowDiagram | | | | | Viewerがdiagram stateを表示する |
| buildKey | viewer_ui | 初期化キーを作る | process | parsedFlowDiagram, settings | currentState | [[RULE-MW-FLOW-DIAGRAM-VIEW-STATE]] | | | frontmatterまたはsettings由来 |
| compareKey | viewer_ui | 既存stateと比較する | decision | currentState | | [[RULE-MW-FLOW-DIAGRAM-VIEW-STATE]] | | | keyが変わったか判定 |
| resolveInitialMode | viewer_ui | 初期modeを解決する | process | parsedFlowDiagram, settings | effectiveMode | [[RULE-MW-FLOW-DIAGRAM-VIEW-STATE]] | | | flow_view、settings、detail fallback |
| keepToolbarMode | viewer_ui | toolbar modeを維持する | process | currentState | effectiveMode | [[RULE-MW-FLOW-DIAGRAM-VIEW-STATE]] | | | 通常再描画では維持 |
| showSelector | viewer_ui | Flow view selectorを表示する | screen | effectiveMode | | | | [[SCR-MW-VIEWER-MAIN-FRAME]] | flow_diagram時だけ表示 |
| passToRenderer | renderer_area | rendererへmodeを渡す | process | effectiveMode | | [[RULE-MW-FLOW-DIAGRAM-SCREEN-PROJECTION]] | [[PROC-MW-FLOW-DIAGRAM-RENDER]] | | rendererはfrontmatterを再評価しない |
| toolbarChange | viewer_ui | toolbar変更を受ける | input | effectiveMode | currentState | [[RULE-MW-FLOW-DIAGRAM-VIEW-STATE]] | | [[SCR-MW-VIEWER-MAIN-FRAME]] | Markdown本文は変更しない |
| resetViewport | viewer_ui | viewportをresetしてFitする | process | currentState | viewportReset | | | | view切替または再初期化時 |
| end | viewer_ui | 表示modeを確定する | end | effectiveMode | | | | | |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | buildKey | | 初期化 | |
| buildKey | compareKey | | 比較 | |
| compareKey | resolveInitialMode | initializationKey changed | 再初期化 | frontmatterやsettings変更 |
| compareKey | keepToolbarMode | initializationKey unchanged | 維持 | 通常再描画 |
| resolveInitialMode | showSelector | | selector表示 | |
| keepToolbarMode | showSelector | | selector表示 | |
| showSelector | passToRenderer | | 描画 | effective modeを渡す |
| showSelector | toolbarChange | user changes selector | toolbar変更 | |
| toolbarChange | resetViewport | | viewport reset | |
| resetViewport | passToRenderer | | 再描画 | |
| passToRenderer | end | | 完了 | |

## Notes

- `flow_view` は初期表示入力であり、toolbar stateを固定しない。
- toolbar変更、Settings変更、renderer mode同期はMarkdown正本を書き換えない。
- Screen viewのprojectionは [[RULE-MW-FLOW-DIAGRAM-SCREEN-PROJECTION]] に従う。
- arbitrary projection definitions、Communication view、generic folding、transition coverage generationはfutureである。

## Source Links

| path | notes |
|---|---|
| src/core/flow-diagram-view-mode.ts | Steps: buildKey, compareKey, resolveInitialMode, keepToolbarMode. Flow Diagram view state |
| src/views/modeling-preview-view.ts | Steps: showSelector, toolbarChange, resetViewport. Flow view selector and viewport reset |
| src/renderers/dfd-mermaid.ts | Steps: passToRenderer. effective Flow Diagram view modeを受け取る |
