---
type: data_object
id: DATA-MW-LOWER-PANEL-TAB-STATE
name: Lower Panel Tab State
kind: viewer_runtime_state
data_format: object
tags:
  - ModelWeave
  - Viewer
  - Runtime
---

# Lower Panel Tab State

## Summary

Viewer下部のReview情報を安定したtabとして表示するための実行時状態を表す。
これはMarkdown正本に保存されるデータではなく、`ModelingPreviewView` 内で現在のmode、表示可能なReview section、選択中tabを元に派生するViewer UI状態である。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| activeTab | Active tab | string | | N | activeLowerPanelTabId | | details / relationships / diagnostics / source-links / mermaid |
| availableTabs | Available tabs | object | | Y | lowerPanelTabs | | 論理的には複数値 |
| reviewContext | Review context | string | | Y | state.mode | [[DATA-MW-VIEWER-STATE]].renderStatus | graph / non-graph / object / diagramなど |
| rendererMode | Renderer mode | string | | N | rendererSelection.effectiveMode | [[DATA-MW-VIEWER-STATE]].effectiveRenderMode | renderer switching後もtab構造を維持する |
| businessFlowDirection | Business Flow direction | string | | N | resolvedBusinessFlowDirection | [[DATA-MW-VIEWER-STATE]].businessFlowDirection | direction変更後もtab構造を維持する |
| persistentWithinView | Persistent within view | boolean | | Y | activeLowerPanelTabId | | 同一file / mode内では選択tabを維持 |
| notes | Notes | string | | N | notes | | Markdown正本ではない |

## Notes

- lower panel tabsはViewer内のReview UIであり、Source Links Explorer / Reference Explorer / Model Index Viewとは別である。
- tab候補はDetails、Relationships、Diagnostics、Source Links、Mermaid Sourceを中心に、現在modeで内容があるsectionから構成される。
- graph model review viewsとnon-graph model review viewsの両方で、下部Review情報をtab化できる。
- renderer switchingやBusiness Flow direction変更で描画内容が再構成されても、同じfile / mode内ではtab構造とactive tabを安定維持する。
- file pathやPreviewState.modeが変わる場合は、active tabをリセットし得る。
- lower panel tabsはMarkdown本文やfrontmatterを変更しない。
- PNG Exportは描画済み図本体の派生出力であり、lower panel tabs、Diagnostics、Source Links、Mermaid SourceなどのReview UIは含めない。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | ViewerLowerPanelTabId がlower panel tab IDを定義する |
| src/views/modeling-preview-view.ts | activeLowerPanelTabId が選択中tabを保持する |
| src/views/modeling-preview-view.ts | applyLowerPanelTabsToPane が下部paneをtab構造へ変換する |
| src/views/modeling-preview-view.ts | resolveActiveLowerPanelTab が維持またはfallbackするactive tabを決める |
| src/views/modeling-preview-view.ts | createCollectionDiagramLowerPaneSlots がgraph review slotsを作る |
