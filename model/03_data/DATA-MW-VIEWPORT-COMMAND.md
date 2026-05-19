---
type: data_object
id: DATA-MW-VIEWPORT-COMMAND
name: Viewport操作コマンド
kind: command
tags:
  - Viewer
  - Viewport
  - Command
---

# Viewport操作コマンド

## Summary

Viewerツールバー操作やマウスホイール操作などから生成される、Viewport（プレビュー表示領域）の倍率や位置を制御するための内部操作コマンドのデータ構造です。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| commandType | コマンド種別 | CODE-MW-VIEWER-COMMAND-TYPE | | Y | commandType | [[CODE-MW-VIEWER-COMMAND-TYPE]] | fit / zoom_in / zoom_out / reset_zoom / pan / wheel_zoom |
| targetViewport | 対象Viewport | string | | Y | targetViewport | [[SCR-MW-VIEWER-MAIN-FRAME]] | 通常は previewCanvas |
| currentScale | 現在倍率 | Optional<number> | | N | currentScale | | zoom操作時の現在倍率 |
| targetScale | 目標倍率 | Optional<number> | | N | targetScale | | resetZoomやfit後の倍率 |
| scaleStep | ズームステップ | Optional<number> | | N | scaleStep | | zoomIn / zoomOut時の倍率変化幅 |
| viewportBounds | Viewport領域 | Optional<object> | | N | viewportBounds | | 表示領域の幅・高さ |
| contentBounds | コンテンツ領域 | Optional<object> | | N | contentBounds | | 描画済みコンテンツの幅・高さ |
| panX | 横方向移動量 | Optional<number> | | N | panX | | fit / pan後のX位置 |
| panY | 縦方向移動量 | Optional<number> | | N | panY | | fit / pan後のY位置 |
| reason | 発生理由 | Optional<string> | | N | reason | | toolbar / wheel / init など |

## Notes

- 本データはMarkdownモデルの正本情報ではなく、Viewer実行時の一時的な操作コマンドを表します。
- `commandType` は後続で codeset 化が可能です。
- ズーム下限・上限は `[[RULE-MW-VIEWER-ZOOM-LIMITS]]` で定義予定です。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/renderers/graph-view-shared.ts | attachGraphViewportInteractions | function | Viewport操作のイベント処理 |
| source/model-weave-repo/src/renderers/zoom-toolbar.ts | createZoomToolbar | function | ツールバー操作の発生元 |