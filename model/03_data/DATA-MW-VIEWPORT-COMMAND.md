---
type: data_object
id: DATA-MW-VIEWPORT-COMMAND
name: Viewport操作コマンド
kind: command
data_format: object
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
| commandType | コマンド種別 | string | | Y | commandType | [[CODE-MW-VIEWER-COMMAND-TYPE]] | コード値。fit / zoom_in / zoom_out / reset_zoom / pan / wheel_zoom |
| targetViewport | 対象Viewport | string | | Y | targetViewport | [[SCR-MW-VIEWER-MAIN-FRAME]] | 通常は previewCanvas |
| currentScale | 現在倍率 | number | | N | currentScale | | 任意項目。zoom操作時の現在倍率 |
| targetScale | 目標倍率 | number | | N | targetScale | | 任意項目。resetZoomやfit後の倍率 |
| scaleStep | ズームステップ | number | | N | scaleStep | | 任意項目。zoomIn / zoomOut時の倍率変化幅 |
| viewportBounds | Viewport領域 | object | | N | viewportBounds | | 任意項目。表示領域の幅・高さ |
| contentBounds | コンテンツ領域 | object | | N | contentBounds | | 任意項目。描画済みコンテンツの幅・高さ |
| panX | 横方向移動量 | number | | N | panX | | 任意項目。fit / pan後のX位置 |
| panY | 縦方向移動量 | number | | N | panY | | 任意項目。fit / pan後のY位置 |
| reason | 発生理由 | string | | N | reason | | 任意項目。toolbar / wheel / init など |

## Notes

- 本データはMarkdownモデルの正本情報ではなく、Viewer実行時の一時的な操作コマンドを表します。
- `commandType` は後続で codeset 化が可能です。
- ズーム下限・上限は `[[RULE-MW-VIEWER-ZOOM-LIMITS]]` で定義予定です。
- Markdownテーブル安全性のため、type列は単純型で表現し、任意性は required / notes / ref で表現します。

## Source Links

| path | notes |
|---|---|
| src/renderers/graph-view-shared.ts | symbol: attachGraphViewportInteractions; kind: function; Viewport操作のイベント処理 |
| src/renderers/zoom-toolbar.ts | symbol: createZoomToolbar; kind: function; ツールバー操作の発生元 |
