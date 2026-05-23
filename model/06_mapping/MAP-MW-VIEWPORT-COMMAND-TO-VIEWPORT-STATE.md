---
type: mapping
id: MAP-MW-VIEWPORT-COMMAND-TO-VIEWPORT-STATE
name: Viewport Command to Viewport State Mapping
kind: data_to_data
source: "[[DATA-MW-VIEWPORT-COMMAND]]"
target: "[[DATA-MW-VIEWPORT-STATE]]"
tags:
  - Viewer
  - Viewport
  - Mapping
---

# Viewport Command to Viewport State Mapping

## Summary

`[[DATA-MW-VIEWPORT-COMMAND]]` によって要求された操作を、実行時の表示状態である `[[DATA-MW-VIEWPORT-STATE]]` へ反映させるための項目対応と変換ルールを定義します。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-VIEWPORT-COMMAND]] | Viewport操作要求 |
| target | [[DATA-MW-VIEWPORT-STATE]] | 操作後の表示状態 |
| rule | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | 倍率の境界条件 |
| reference | [[CODE-MW-VIEWER-COMMAND-TYPE]] | commandTypeの許容値 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| commandType | lastCommandType | そのまま保持 | [[CODE-MW-VIEWER-COMMAND-TYPE]] | N | 最後に適用された操作種別 |
| targetScale | scale | minZoom / maxZoom の範囲に丸めて反映 | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | Y | zoom / reset / fit の結果 |
| panX | panX | 計算済みX位置を反映 | | N | fit / pan 操作時 |
| panY | panY | 計算済みY位置を反映 | | N | fit / pan 操作時 |
| viewportBounds | viewportWidth | viewportBounds.width を反映 | | N | fit計算に使用 |
| viewportBounds | viewportHeight | viewportBounds.height を反映 | | N | fit計算に使用 |
| contentBounds | contentWidth | contentBounds.width を反映 | | N | fit計算に使用 |
| contentBounds | contentHeight | contentBounds.height を反映 | | N | fit計算に使用 |
|  | minScale | RULE-MW-VIEWER-ZOOM-LIMITS の minZoom を参照値として保持 | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | N | ズーム下限 |
|  | maxScale | RULE-MW-VIEWER-ZOOM-LIMITS の maxZoom を参照値として保持 | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | N | ズーム上限 |
|  | defaultScale | RULE-MW-VIEWER-ZOOM-LIMITS の defaultZoom を参照値として保持 | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | N | resetZoom時の既定倍率 |

## Rules

- `commandType = fit` の場合、viewportBounds と contentBounds から scale / panX / panY を計算する。
- `commandType = zoom_in` の場合、currentScale に scaleStep を掛け、`[[RULE-MW-VIEWER-ZOOM-LIMITS]]` の範囲に丸める。
- `commandType = zoom_out` の場合、currentScale を scaleStep で割り、`[[RULE-MW-VIEWER-ZOOM-LIMITS]]` の範囲に丸める。
- `commandType = reset_zoom` の場合、targetScale または defaultScale を scale に反映する。
- `commandType = pan` の場合、panX / panY を更新し、scale は維持する。
- `commandType = wheel_zoom` の場合、ポインタ位置を基準に scale と panX / panY を同時更新する。
- Viewport状態の変更はViewer実行時の一時状態であり、Markdown本文やfrontmatterは変更しません。

## Notes

- fit / zoom / pan の詳細計算は実装側に委ねる。
- 本mappingは、UI操作の結果としてどの状態項目が更新されるかを表す。
- minScale / maxScale / defaultScale は `[[RULE-MW-VIEWER-ZOOM-LIMITS]]` から導出される参照値である。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/graph-view-shared.ts | attachGraphViewportInteractions | function | Viewport状態更新の起点 |
| src/renderers/zoom-toolbar.ts | createZoomToolbar | function | toolbar操作の発生元 |