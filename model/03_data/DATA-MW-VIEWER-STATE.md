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
| currentModelName | 現在モデル名 | string | | N | currentModel.name | | 表示中モデルのnameまたはファイル名 |
| supportedRenderModes | サポート描画モード | string | | N | renderModeInput.supportedModes | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | 現在のモデルで選択可能な描画モード。有効なモード名のリスト |
| effectiveRenderMode | 実効描画モード | string | | N | effectiveRenderMode | | resolveRenderMode の結果 |
| previewContent | プレビュー内容 | object | | N | previewContent | | GraphModelまたはMermaidSourceなど描画対象 |
| diagnostics | 診断情報一覧 | DATA-MW-CORE-DIAGNOSTIC | | N | diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | Viewerに表示する複数診断を保持 |
| renderStatus | 描画状態 | string | | Y | renderStatus | | ready / unsupported / error / empty など |
| renderMessage | 描画状態メッセージ | string | | N | renderMessage | | previewCanvas上に表示する状態メッセージ |
| activeFilePath | アクティブファイルパス | string | | N | activeFilePath | | 表示対象ファイル |
| autoFitEnabled | Auto Fit有効 | boolean | | N | autoFitEnabled | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | 初回表示やファイル切替時にAuto Fitを許可するか |
| preserveViewportState | Viewport状態保持 | boolean | | N | preserveViewportState | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | ファイル単位のzoom/pan状態を保持するか |
| diagnosticsVisible | 診断パネル表示 | boolean | | N | diagnosticsVisible | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | 診断パネルを表示するか。診断生成自体は継続する |
| statusBarVisible | ステータスバー表示 | boolean | | N | statusBarVisible | [[SCR-MW-VIEWER-STATUS-BAR]] | Viewerステータスバーを表示するか |
| viewerFontScale | Viewer文字サイズ倍率 | string | | N | viewerFontScale | | small / normal / large 等。実際のCSS解釈はViewerに委ねる |
| mermaidThemeMode | Mermaidテーマモード | string | | N | mermaidThemeMode | | Obsidianテーマ追従や固定テーマの方針 |
| sourceLinksEnabled | Source Links有効 | boolean | | N | sourceLinksEnabled | | Source Links表示や追跡を有効にするか |

## Notes
- 実装上の `PreviewState` および `ModelingPreviewView.state` に対応する概念的な状態データ。
- GraphModel や MermaidSource などの具体的な描画データは、UIマッピング上は `previewContent` に集約して扱う。
- これらの項目はPlugin Settingsから導出されるViewer実行時状態であり、Markdownモデルの正本ではない。
- diagnosticsVisible / statusBarVisible は表示制御であり、内部状態や診断生成そのものを停止するものではない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/views/modeling-preview-view.ts | PreviewState | type | Viewer内部状態 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | ModelingPreviewView | class | stateを保持しUIへ反映するViewer |