---
type: mapping
id: MAP-MW-VIEWPORT-STATE-TO-RENDERER-INPUT
name: Viewport State to Renderer Input Mapping
kind: data_to_data
source: "[[DATA-MW-VIEWPORT-STATE]]"
target: "[[DATA-MW-RENDERER-VIEWPORT-INPUT]]"
tags:
  - Viewer
  - Renderer
  - Mapping
---

# Viewport State to Renderer Input Mapping

## Summary

`[[DATA-MW-VIEWPORT-STATE]]` が保持する現在のズーム倍率やパン位置を、Renderer（DOM/CSS）が解釈可能な `DATA-MW-RENDERER-VIEWPORT-INPUT` へマッピングします。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-VIEWPORT-STATE]] | Viewerが保持する現在の表示状態 |
| target | [[DATA-MW-RENDERER-VIEWPORT-INPUT]] | Rendererへ渡される表示制御入力 |
| rule | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | scaleの境界条件 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| scale | cssScale | CSS transform scale または CSS変数へ反映 | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | Y | 表示倍率 |
| panX | cssTranslateX | CSS transform translateX または CSS変数へ反映 | | Y | 横方向の表示位置 |
| panY | cssTranslateY | CSS transform translateY または CSS変数へ反映 | | Y | 縦方向の表示位置 |
| viewportWidth | viewportWidth | Renderer側の表示領域幅として渡す | | N | fit計算や再描画で使用 |
| viewportHeight | viewportHeight | Renderer側の表示領域高さとして渡す | | N | fit計算や再描画で使用 |
| contentWidth | contentWidth | 描画済みコンテンツ幅として渡す | | N | fit計算で使用 |
| contentHeight | contentHeight | 描画済みコンテンツ高さとして渡す | | N | fit計算で使用 |
| lastCommandType | lastCommandType | デバッグ/再描画判定用に保持 | [[CODE-MW-VIEWER-COMMAND-TYPE]] | N | 最後に適用された操作 |

## Rules

- `scale` は renderer に渡す前に `[[RULE-MW-VIEWER-ZOOM-LIMITS]]` の範囲内であることを前提とする。
- `panX` / `panY` は `previewCanvas` 内の表示位置を制御する。
- CSS transform を使う場合は `translate(panX, panY) scale(scale)` 相当の表現になる。
- CSS variables を使う場合は `--mw-zoom-scale`, `--mw-pan-x`, `--mw-pan-y` のような変数へ反映する。
- Renderer入力への反映はViewer表示のみを変更し、Markdown本文やfrontmatterは変更しない。

## Notes

- DATA-MW-RENDERER-VIEWPORT-INPUT は Renderer へ渡す表示制御入力として定義済みである。
- 実際のCSS変数名やDOM要素名は実装に委ねる。
- Mermaid / custom renderer のどちらでも、Viewer shell 側のviewport制御として扱う。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/renderers/graph-view-shared.ts | attachGraphViewportInteractions | function | zoom / pan 状態をDOMへ反映する処理 |
| source/model-weave-repo/src/renderers/zoom-toolbar.ts | createZoomToolbar | function | toolbar操作の発生元 |