---
type: mapping
id: MAP-MW-TOOLBAR-ACTION-TO-VIEWPORT-COMMAND
name: Toolbar Action to Viewport Command Mapping
kind: other
source: "[[SCR-MW-VIEWER-MAIN-FRAME]]"
target: "[[DATA-MW-VIEWPORT-COMMAND]]"
tags:
  - ModelWeave
  - Mapping
  - Viewer
---

# Toolbar Action to Viewport Command Mapping

## Summary

`[[SCR-MW-VIEWER-MAIN-FRAME]]` のツールバーで行われるUI操作を、プレビュー領域を制御する内部コマンド `DATA-MW-VIEWPORT-COMMAND` へマッピングします。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[SCR-MW-VIEWER-MAIN-FRAME]] | 操作元となるViewerメイン画面 |
| target | [[DATA-MW-VIEWPORT-COMMAND]] | 操作対象となるViewportコマンド構造 |
| rule | [[RULE-MW-VIEWER-TOOLBAR-VISIBILITY]] | ツールバー要素の有効/無効ルール |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| fitToView | commandType | toolbar action id に応じて commandType を決定する | [[RULE-MW-VIEWER-TOOLBAR-VISIBILITY]] | Y | fitToView=fit / zoomIn=zoom_in / zoomOut=zoom_out / resetZoom=reset_zoom |
| previewCanvas | targetViewport | use active preview viewport | | Y | 操作を適用する対象領域 |

## Rules

- `fitToView` は `commandType = fit` の ViewportCommand を生成する。
- `zoomIn` は `commandType = zoom_in` の ViewportCommand を生成する。
- `zoomOut` は `commandType = zoom_out` の ViewportCommand を生成する。
- `resetZoom` は `commandType = reset_zoom` の ViewportCommand を生成する。
- commandType の許容値は `[[CODE-MW-VIEWER-COMMAND-TYPE]]` に従う。
- ズーム倍率の境界は `[[RULE-MW-VIEWER-ZOOM-LIMITS]]` に従う。
- `fitToView` は、プレビュー領域のサイズ（viewportBounds）と描画内容のサイズ（contentBounds）を元に、最適な scale と pan を算出します。
- `resetZoom` は、プラグイン設定の `defaultZoom` または 100% に表示倍率をリセットします。
- ツールバーからの操作は、一時的な表示状態の変更であり、Markdownモデル（正本）の内容は変更しません。
## Notes

- 操作対象は常に現在アクティブな `previewCanvas` です。

## Source Links

| path | notes |
|---|---|
| src/renderers/graph-view-shared.ts | symbol: attachGraphViewportInteractions; kind: function; UI操作とViewport処理のバインド |
| src/renderers/zoom-toolbar.ts | symbol: createZoomToolbar; kind: function; ツールバーボタンの生成 |
