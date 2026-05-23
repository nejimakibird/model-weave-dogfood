---
type: data_object
id: DATA-MW-RENDERER-VIEWPORT-INPUT
name: Renderer Viewport入力
kind: renderer_input
tags:
  - Renderer
  - Viewport
  - Viewer
---

# Renderer Viewport入力

## Summary

RendererまたはViewer shellがDOM/CSSへ反映するためのViewport表示制御入力を表す。
[[DATA-MW-VIEWPORT-STATE]] から導出され、実際の previewCanvas / diagram body の transform や CSS variables に反映される。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| cssScale | CSS倍率 | number | | Y | cssScale | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | CSS transform scale または CSS変数に反映する倍率 |
| cssTranslateX | CSS横移動 | number | | Y | cssTranslateX | | CSS transform translateX または CSS変数に反映するX位置 |
| cssTranslateY | CSS縦移動 | number | | Y | cssTranslateY | | CSS transform translateY または CSS変数に反映するY位置 |
| transformValue | CSS transform値 | Optional<string> | | N | transformValue | | translate と scale を合成したCSS値 |
| zoomScaleVariable | ズームCSS変数 | Optional<string> | | N | cssVariables.zoomScale | | 例: --mw-zoom-scale |
| panXVariable | 横移動CSS変数 | Optional<string> | | N | cssVariables.panX | | 例: --mw-pan-x |
| panYVariable | 縦移動CSS変数 | Optional<string> | | N | cssVariables.panY | | 例: --mw-pan-y |
| viewportWidth | Viewport幅 | Optional<number> | | N | viewportWidth | | Renderer側の表示領域幅 |
| viewportHeight | Viewport高さ | Optional<number> | | N | viewportHeight | | Renderer側の表示領域高さ |
| contentWidth | コンテンツ幅 | Optional<number> | | N | contentWidth | | 描画済みコンテンツ幅 |
| contentHeight | コンテンツ高さ | Optional<number> | | N | contentHeight | | 描画済みコンテンツ高さ |
| lastCommandType | 最終コマンド種別 | CODE-MW-VIEWER-COMMAND-TYPE | | N | lastCommandType | [[CODE-MW-VIEWER-COMMAND-TYPE]] | 最後に適用されたViewport操作 |

## Notes

- 本データはRendererへ渡す表示制御用の入力であり、Markdownモデルの正本ではない。
- Mermaid / custom renderer のどちらでも、Viewer shell側のviewport制御として扱う。
- 実際のCSS変数名やDOM属性名は実装に依存する。
- transformValue は cssTranslateX / cssTranslateY / cssScale から導出できる。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/graph-view-shared.ts | attachGraphViewportInteractions | function | zoom / pan 状態をDOMへ反映する処理 |
| src/renderers/graph-view-shared.ts | applyTransform | function | transform値またはCSS変数への反映処理 |
| src/renderers/zoom-toolbar.ts | createZoomToolbar | function | toolbar操作の発生元 |