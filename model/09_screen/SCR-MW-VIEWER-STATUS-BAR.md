---
type: screen
id: SCR-MW-VIEWER-STATUS-BAR
name: Viewerステータスバー
screen_type: panel
tags:
  - Viewer
  - UI
  - Status
---

# Viewerステータスバー

## Summary

Viewerの現在状態を短く表示するステータスバーUIを定義する。
ズーム倍率、Auto Fit状態、手動Viewport状態、診断件数、現在ファイルパスなどを表示する。

## Layout

| id | label | kind | purpose | notes |
|---|---|---|---|---|
| status_bar_root | ステータスバー | container | Viewer状態の要約表示 | |
| viewport_status_area | Viewport状態領域 | container | zoom / fit / manual state を表示 | |
| diagnostics_summary_area | 診断サマリー領域 | container | error / warning 件数を表示 | |
| file_status_area | ファイル状態領域 | container | 現在ファイルやrender状態を表示 | |

## Fields

| id | label | kind | layout | data_type | required | ref | rule | notes |
|---|---|---|---|---|---|---|---|---|
| zoomPercentLabel | ズーム率表示 | label | viewport_status_area | string | N | [[DATA-MW-VIEWPORT-STATE]] | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | 例: 100% / 125% |
| manualViewportLabel | 手動表示状態 | label | viewport_status_area | string | N | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | | Manual Zoom Active など |
| autoFitStatusLabel | Auto Fit状態 | label | viewport_status_area | string | N | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | | Auto Fit / Preserved Viewport など |
| diagnosticsSummaryLabel | 診断サマリー | label | diagnostics_summary_area | string | N | [[DATA-MW-CORE-DIAGNOSTIC]] | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Error / Warning 件数 |
| currentFilePathLabel | 現在ファイル | label | file_status_area | string | N | [[DATA-MW-VIEWER-STATE]] | | 表示対象ファイルパス |
| renderStatusLabel | 描画状態 | label | file_status_area | string | N | [[DATA-MW-VIEWER-STATE]] | | ready / error / empty / unsupported など |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| openDiagnosticsPanel | 診断パネルを開く | ui_action | diagnosticsSummaryLabel | click | | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | | 診断サマリークリックで詳細パネルへ誘導 |
| resetManualViewport | 表示状態をリセット | ui_action | manualViewportLabel | click | | | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | 手動viewport状態を解除しfitToViewへ戻す |

## Messages

| id | text | severity | timing | notes |
|---|---|---|---|---|
| manualZoomActive | Manual Zoom Active | info | when_manual_viewport | 手動pan/zoom状態が有効 |
| autoFitApplied | Auto Fit Applied | info | when_auto_fit | 自動フィット済み |
| diagnosticsAvailable | Diagnostics available. | warning | when_diagnostics_exist | error/warningがある場合 |
| noDiagnostics | No diagnostics. | info | when_no_diagnostics | 診断がない場合 |

## Notes

- 本screenはViewer状態の要約表示を目的とする。
- 詳細な診断一覧は [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] に委ねる。
- zoomPercentLabel は [[DATA-MW-VIEWPORT-STATE]].scale を百分率表示へ変換して表示する。
- manualViewportLabel は [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] の状態保持方針に基づく。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer状態に応じたUI更新 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | prepareFileViewportState | method | viewport状態復元/初期化 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderDiagnostics | function | 診断サマリーの元データ |