---
type: rule
id: RULE-MW-VIEWER-AUTO-FIT-ON-LOAD
name: Viewer読み込み時Auto Fit制御ルール
kind: display_rule
tags:
  - Viewer
  - Viewport
  - Rule
---

# Viewer読み込み時Auto Fit制御ルール

## Summary

Viewerでモデルを開いた時、再描画された時、レンダラーが切り替わった時に、fitToView を自動実行する条件を定義する。
Auto FitはViewer表示状態のみを変更し、Markdownモデルの内容は変更しない。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| viewerState | [[DATA-MW-VIEWER-STATE]] | Viewer | Y | 現在の表示対象と描画状態 |
| viewportState | [[DATA-MW-VIEWPORT-STATE]] | Viewer | Y | 現在のscale/pan状態 |
| viewportCommand | [[DATA-MW-VIEWPORT-COMMAND]] | Viewer | N | fit操作として生成されるコマンド |
| mainFrame | [[SCR-MW-VIEWER-MAIN-FRAME]] | Viewer UI | N | previewCanvasを含むViewer画面 |
| transformRule | [[RULE-MW-VIEWPORT-TRANSFORM-CALCULATION]] | Rule | Y | fit時のscale/pan計算 |
| zoomLimitRule | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | Rule | Y | scaleの境界条件 |

## Conditions

- 新しいModel Weave対象ファイルを開いた場合、初回表示として fitToView を実行する。
- active file が切り替わった場合、前のファイルのpan/zoom状態を引き継がず、新しいファイルに対して fitToView を実行する。
- 同じファイル内で再描画された場合、ユーザーが手動でズーム/パンしていれば原則として現在のviewportStateを維持する。
- renderModeSelectorで描画モードを切り替えた場合、描画結果の寸法が変わる可能性があるため fitToView を実行してよい。
- previewCanvas のサイズが大きく変わった場合、必要に応じて fitToView を再計算してよい。
- contentBounds が取得できない場合、fitToView は実行せず warning を出す。
- ユーザーが明示的に Fit ボタンを押した場合は、常に fitToView を実行する。
- Auto Fit は Markdown本文やfrontmatterを変更しない。

## Parameters

| name | value | notes |
|---|---|---|
| autoFitOnFirstOpen | true | 初回表示時にfitToViewを実行 |
| autoFitOnFileChange | true | 対象ファイル切替時にfitToViewを実行 |
| preserveManualViewport | true | 手動操作後の再描画では現在状態を維持 |
| autoFitOnRenderModeChange | true | レンダラー切替時は再fitを許容 |

## Messages

| condition | message | severity | notes |
|---|---|---|---|
| missing content bounds | Cannot auto-fit because rendered content bounds are unavailable. | warning | contentBounds未取得 |
| missing viewport bounds | Cannot auto-fit because viewport bounds are unavailable. | warning | viewportBounds未取得 |
| auto fit skipped | Auto fit skipped to preserve manual viewport. | info | 手動pan/zoomを維持した場合 |

## Notes

- Auto Fit は [[DATA-MW-VIEWPORT-COMMAND]].commandType = fit として扱える。
- 実際のscale/pan計算は [[RULE-MW-VIEWPORT-TRANSFORM-CALCULATION]] に従う。
- scaleの境界値は [[RULE-MW-VIEWER-ZOOM-LIMITS]] に従う。
- Auto Fit はViewerの一時表示状態を変更するだけで、Markdownモデルは変更しない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderCurrentState | method | active file / render state に応じた再描画 |
| source/model-weave-repo/src/renderers/graph-view-shared.ts | fitToView | function | 自動fitのscale/pan計算 |
| source/model-weave-repo/src/renderers/graph-view-shared.ts | attachGraphViewportInteractions | function | viewport状態と操作の管理 |