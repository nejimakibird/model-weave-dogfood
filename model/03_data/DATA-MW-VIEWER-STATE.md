---
type: data_object
id: DATA-MW-VIEWER-STATE
name: Viewer内部状態
kind: state
tags:
  - Viewer
  - Runtime
  - State
---

# Viewer内部状態

## Summary
Viewer の実行時における現在の表示内容やレンダリング状態を保持する内部データオブジェクト。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| currentModelName | 現在モデル名 | Optional<string> | | N | currentModel.name | | 表示中モデルのnameまたはファイル名 |
| supportedRenderModes | サポート描画モード | Array<string> | | N | renderModeInput.supportedModes | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | 現在のモデルで選択可能な描画モード |
| effectiveRenderMode | 実効描画モード | Optional<string> | | N | effectiveRenderMode | | resolveRenderMode の結果 |
| previewContent | プレビュー内容 | Optional<object> | | N | previewContent | | GraphModelまたはMermaidSourceなど描画対象 |
| diagnostics | 診断情報一覧 | Array<DATA-MW-CORE-DIAGNOSTIC> | | N | diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | Viewerに表示する診断情報 |
| renderStatus | 描画状態 | string | | Y | renderStatus | | ready / unsupported / error / empty など |
| renderMessage | 描画状態メッセージ | Optional<string> | | N | renderMessage | | previewCanvas上に表示する状態メッセージ |
| activeFilePath | アクティブファイルパス | Optional<string> | | N | activeFilePath | | 表示対象ファイル |

## Notes
- 実装上の `PreviewState` および `ModelingPreviewView.state` に対応する概念的な状態データ。
- GraphModel や MermaidSource などの具体的な描画データは、UIマッピング上は `previewContent` に集約して扱う。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/views/modeling-preview-view.ts | PreviewState | type | Viewer内部状態 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | ModelingPreviewView | class | stateを保持しUIへ反映するViewer |