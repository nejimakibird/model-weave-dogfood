---
type: rule
id: RULE-MW-VIEWPORT-TRANSFORM-CALCULATION
name: Viewport transform計算ルール
kind: calculation
tags:
  - Viewer
  - Viewport
  - Rule
---

# Viewport transform計算ルール

## Summary

Viewport状態からCSS transform値またはCSS variablesを生成する計算方針を定義する。
対象は scale / panX / panY / transformValue であり、Viewer表示のみを変更し、Markdownモデルは変更しない。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| viewportState | [[DATA-MW-VIEWPORT-STATE]] | Viewer | Y | scale / panX / panY を保持する表示状態 |
| rendererInput | [[DATA-MW-RENDERER-VIEWPORT-INPUT]] | Renderer | Y | transformValue / CSS変数へ反映される入力 |
| viewportCommand | [[DATA-MW-VIEWPORT-COMMAND]] | Viewer | N | fit / zoom / pan の操作種別 |
| zoomLimitRule | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | Rule | Y | scale の境界条件 |
| commandType | [[CODE-MW-VIEWER-COMMAND-TYPE]] | CodeSet | N | 操作種別 |

## Conditions

- `scale` は `[[RULE-MW-VIEWER-ZOOM-LIMITS]]` の minZoom / maxZoom 範囲内に丸める。
- CSS transform を使う場合、`transformValue` は `translate(panXpx, panYpx) scale(scale)` 相当とする。
- CSS variables を使う場合、`cssScale`, `cssTranslateX`, `cssTranslateY` をそれぞれ対応する変数へ反映する。
- `fit` の場合、viewportBounds と contentBounds から図全体が収まる scale を算出し、中央寄せの panX / panY を算出する。
- `zoom_in` の場合、currentScale に zoomStep を掛ける。
- `zoom_out` の場合、currentScale を zoomStep で割る。
- `reset_zoom` の場合、scale は defaultZoom または 1.0 に戻し、panX / panY は初期位置へ戻す。
- `pan` の場合、scale は維持し、panX / panY だけを更新する。
- `wheel_zoom` の場合、ポインタ位置を基準に、拡大縮小後も同じ図上の点がカーソル下に残るよう panX / panY を補正する。
- transform反映はViewer表示のみを変更し、Markdown本文やfrontmatterは変更しない。

## Parameters

| name | expression | notes |
|---|---|---|
| transformValue | translate(panXpx, panYpx) scale(scale) | CSS transform 形式 |
| clampedScale | clamp(scale, minZoom, maxZoom) | ズーム境界で丸めた倍率 |
| zoomInScale | currentScale * zoomStep | 拡大操作 |
| zoomOutScale | currentScale / zoomStep | 縮小操作 |

## Messages

| condition | message | severity | notes |
|---|---|---|---|
| invalid scale | Invalid viewport scale. | warning | scale が数値として扱えない場合 |
| invalid bounds | Cannot calculate fit transform because bounds are unavailable. | warning | fit計算に必要な領域情報がない |
| transform clamped | Viewport zoom was clamped to allowed range. | info | min/max境界に丸めた場合 |

## Notes

- 本ルールは計算方針を定義するもので、具体的なDOM更新手段は実装に委ねる。
- Mermaid / custom renderer のどちらでも、Viewer shell側のtransform制御として扱う。
- transformValue は `[[DATA-MW-RENDERER-VIEWPORT-INPUT]]` の cssTranslateX / cssTranslateY / cssScale から導出できる。

## Source Links

| path | notes |
|---|---|
| src/renderers/graph-view-shared.ts | symbol: attachGraphViewportInteractions; kind: function; transform計算と反映の起点 |
| src/renderers/graph-view-shared.ts | symbol: applyTransform; kind: function; CSS transformまたはCSS変数への反映処理 |
| src/renderers/zoom-toolbar.ts | symbol: createZoomToolbar; kind: function; zoom操作の発生元 |
