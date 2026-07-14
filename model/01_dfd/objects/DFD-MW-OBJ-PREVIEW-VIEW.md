---
type: dfd_object
id: DFD-MW-OBJ-PREVIEW-VIEW
name: Preview View
kind: external
tags:
  - DFD
  - Viewer
  - Preview
---

# Preview View

## Summary

Model WeaveのPreview表示境界。
ModelingPreviewView が表示対象ファイルのpreview stateを描画し、viewer preferencesを適用し、settings変更時に開いているPreviewを更新する。
Source Links Explorerなどの将来Explorer画面は本オブジェクトの範囲外である。

## Details

| key | value | notes |
|---|---|---|
| owner | views/modeling-preview-view.ts | ModelingPreviewView |
| preview | showPreviewForFile | file pathからpreview stateを表示 |
| settings | applyViewerSettings | viewerPreferencesを反映 |
| flow_view_state | FlowDiagramViewModeState | Flow Diagramのfile scoped Detail / Screen view状態 |
| refresh | refreshForSettingsChange | settings変更後の再描画 |
| state | [[DATA-MW-VIEWER-STATE]] | PreviewState / viewerPreferences |
| rendered_input | rendered preview content | Renderer / Source Links / Impact Summary から受け取る |

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | ModelingPreviewView |
| src/views/modeling-preview-view.ts | applyViewerSettings / refreshForSettingsChange |
| src/views/modeling-preview-view.ts | showPreviewForFile |
| src/core/flow-diagram-view-mode.ts | FlowDiagramViewModeState |
