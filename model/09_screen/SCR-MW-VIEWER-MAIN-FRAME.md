---
type: screen
id: SCR-MW-VIEWER-MAIN-FRAME
name: Model Weave Viewer Main Frame
screen_type: dashboard
tags:
  - ModelWeave
  - Screen
  - Viewer
---

# Model Weave Viewer Main Frame

## Summary
Model Weave Viewer全体のレイアウトを定義し、上部ツールバー、中央プレビュー領域、下部診断パネルの関係を明確にする。
ズームコントロール、レンダラー選択、診断表示などの主要なUI要素を配置する。

## Layout

| id | label | kind | purpose | notes |
|---|---|---|---|---|
| viewer_root | Model Weave Viewer Root Container | container | Viewer全体のルート要素 | |
| toolbar | Viewer Toolbar | toolbar | ズーム、レンダラー選択などの操作を含む上部ツールバー | |
| preview_area | Main Preview Area | content-pane | モデルのメイン表示領域（ダイアグラム、テーブル、テキストなど） | |
| diagnostics_panel_area | Diagnostics Panel Area | content-pane | 診断メッセージを表示する下部パネル領域 | |

## Fields

| id | label | kind | layout | data_type | required | ref | rule | notes |
|---|---|---|---|---|---|---|---|---|
| currentModelTitle | Current Model Title | label | toolbar | string | Y | | | 現在表示中のモデルのタイトル |
| fitButton | Fit to View Button | button | toolbar |  | N | | | プレビューをビューポートにフィットさせるボタン |
| zoomOutButton | Zoom Out Button | button | toolbar |  | N | | | プレビューを縮小するボタン |
| zoomInButton | Zoom In Button | button | toolbar |  | N | | | プレビューを拡大するボタン |
| resetZoomButton | Reset Zoom Button | button | toolbar |  | N | | | プレビューを100%にリセットするボタン |
| renderModeSelector | Renderer Mode Selector | select | toolbar | string | N | | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | レンダラーモードを切り替える。DFDでは非表示または無効化 |
| previewCanvas | Preview Canvas | panel | preview_area |  | Y | [[DATA-MW-RENDERER-GRAPH-MODEL]] | | 実際のモデルレンダリングが行われる領域 |
| diagnosticsPanel | Diagnostics Panel | panel | diagnostics_panel_area |  | N | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | 診断メッセージを表示する下部パネル |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| fitToView | Fit to View | ui_action | fitButton | click | | | | previewCanvasをビューポートにフィットさせる |
| zoomOut | Zoom Out | ui_action | zoomOutButton | click | | | | previewCanvasを縮小する |
| zoomIn | Zoom In | ui_action | zoomInButton | click | | | | previewCanvasを拡大する |
| resetZoom | Reset Zoom | ui_action | resetZoomButton | click | | | | previewCanvasを100%にリセットする |
| changeRenderMode | Change Renderer Mode | field_event | renderModeSelector | change | | | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | 選択されたレンダラーモードに切り替える |
| refreshPreview | Refresh Preview | system_event | previewCanvas | other | [[PROC-MW-RENDERER-BUILD-GRAPH-MODEL]] | | | プレビューを再描画する |
| openDiagnosticPanel | Open Diagnostic Panel | ui_action | diagnosticsPanel | click | | | | 診断パネルを開く/閉じる、または診断項目にジャンプする |

## Messages

| id | text | severity | timing | notes |
|---|---|---|---|---|
| previewUnavailable | Preview unavailable for this file type. | info | on_unsupported_file | サポートされていないファイルタイプの場合 |
| renderError | Failed to render the model. Check diagnostics for details. | error | on_render_error | レンダリング処理でエラーが発生した場合 |
| noActiveModel | No active model to display. Open a Model Weave file. | info | empty_state | アクティブなファイルがない場合 |
| dfdRendererFixedNotice | DFD diagrams are rendered using Mermaid only. | info | on_dfd_model | DFDでレンダラー選択が固定されている場合の通知 |

## Related Screens

- [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]]

## Related Data Objects

- [[DATA-MW-RENDERER-RENDER-MODE-INPUT]]
- [[DATA-MW-RENDERER-GRAPH-MODEL]]
- [[DATA-MW-RENDERER-MERMAID-SOURCE]]
- [[DATA-MW-CORE-DIAGNOSTIC]]

## Related Rules

- [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]]
- [[RULE-MW-DFD-MERMAID-ONLY]]
- [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]]

## Related Processes

- [[PROC-MW-RENDERER-BUILD-GRAPH-MODEL]]
- [[PROC-MW-RENDERER-GENERATE-MERMAID-SOURCE]]

## Notes
- `renderModeSelector` の表示/非表示、および有効/無効は、現在のモデルタイプと [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] および [[RULE-MW-DFD-MERMAID-ONLY]] に基づいて制御される。
- `diagnosticsPanel` は [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] のインスタンスとして `diagnostics_panel_area` に表示される。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/views/modeling-preview-view.ts | ModelingPreviewView | class | Viewerのメインビュークラス |
| src/views/modeling-preview-view.ts | renderCurrentState | method | 現在のプレビュー状態をレンダリングする |
| src/views/modeling-preview-view.ts | renderObjectState | method | オブジェクトモデルのプレビューをレンダリングする |
| src/views/modeling-preview-view.ts | renderSummaryState | method | サマリーモデルのプレビューをレンダリングする |
| src/views/modeling-preview-view.ts | renderDfdObjectState | method | DFDオブジェクトモデルのプレビューをレンダリングする |
| src/views/modeling-preview-view.ts | renderDiagramState | method | ダイアグラムモデルのプレビューをレンダリングする |
| src/views/modeling-preview-view.ts | createViewerSplitShell | method | 上下分割パネルのUIシェルを作成する |
| src/views/modeling-preview-view.ts | appendRendererSelection | method | レンダラー選択UIをツールバーに追加する |
| src/views/modeling-preview-view.ts | renderDiagnostics | function | 診断情報をレンダリングするユーティリティ関数 |
| src/core/render-mode.ts | resolveRenderMode | function | レンダラーモードを解決する |
| src/core/render-mode.ts | getSupportedRenderModes | function | サポートされているレンダラーモードを取得する |
| src/main.ts | syncPreviewToActiveFile | method | アクティブファイルとプレビューを同期する |
| src/main.ts | openDiagnosticLocation | method | 診断箇所にジャンプする |