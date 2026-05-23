---
type: data_object
id: DATA-MW-VIEWPORT-STATE
name: Viewport表示状態
kind: state
tags:
  - Viewer
  - Viewport
  - State
---

# Viewport表示状態

## Summary
ViewerのpreviewCanvasにおける現在の zoom、pan位置、fit / manual 状態、表示領域、描画内容領域を保持する実行時状態を表す。
この状態はMarkdownモデルの正本ではなく、Viewer上の一時的な表示状態である。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| scale | 表示倍率 | number | | Y | scale | | implemented。dogfood上の論理名。実装上の zoom に対応する互換的な概念名 |
| zoom | ズーム倍率 | number | | Y | zoom | | implemented。GraphViewportState.zoom。zoom / pan 操作の中核状態 |
| panX | 横方向位置 | number | | Y | panX | | implemented。GraphViewportState.panX。graph viewport interaction で使用されるパン位置 |
| panY | 縦方向位置 | number | | Y | panY | | implemented。GraphViewportState.panY。graph viewport interaction で使用されるパン位置 |
| viewMode | 表示モード | string | | Y | viewMode | | implemented。GraphViewportState.viewMode。fit / manual の表示状態 |
| hasAutoFitted | Auto Fit済み | boolean | | Y | hasAutoFitted | | implemented。GraphViewportState.hasAutoFitted。auto fit 実行状態 |
| hasUserInteracted | ユーザー操作済み | boolean | | Y | hasUserInteracted | | implemented。GraphViewportState.hasUserInteracted。ユーザーの zoom / pan 操作有無 |
| minScale | 最小倍率 | number | | N | minScale | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | partial。実装上の minZoom 制約に対応する論理名。既定値は attachGraphViewportInteractions で扱う |
| maxScale | 最大倍率 | number | | N | maxScale | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | partial。実装上の maxZoom 制約に対応する論理名。既定値は attachGraphViewportInteractions で扱う |
| defaultScale | 既定倍率 | number | | N | defaultScale | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | partial。実装上の initialZoom / resetGraphViewportState に対応する論理名 |
| viewportWidth | Viewport幅 | number | | N | viewportBounds.width | | partial / derived。fitToView の計算で canvas.clientWidth から得る表示領域幅。未設定の場合あり |
| viewportHeight | Viewport高さ | number | | N | viewportBounds.height | | partial / derived。fitToView の計算で canvas.clientHeight から得る表示領域高さ。未設定の場合あり |
| contentWidth | コンテンツ幅 | number | | N | contentBounds.width | | partial / derived。fitToView の計算で scene.width として扱う描画領域幅。未設定の場合あり |
| contentHeight | コンテンツ高さ | number | | N | contentBounds.height | | partial / derived。fitToView の計算で scene.height として扱う描画領域高さ。未設定の場合あり |
| lastCommandType | 最終コマンド種別 | string | | N | lastCommandType | [[CODE-MW-VIEWER-COMMAND-TYPE]] | planned / helper。現行GraphViewportStateには未実装の操作履歴補助項目。未設定の場合あり |
| updatedAt | 更新タイミング | number | | N | updatedAt | | partial。CachedViewportState.updatedAt としてキャッシュ更新時刻に使用。GraphViewportStateの中核状態ではない |

## Notes
- Viewport状態はViewer実行時の一時状態であり、Markdown本文やfrontmatterには保存しない。
- 現行実装側のViewport状態は src/renderers/graph-view-shared.ts と src/views/modeling-preview-view.ts を正とする。
- この data_object は実装済みの zoom / pan 状態と、dogfood上の補助・派生項目を含むため、全体としては partial と扱う。
- `scale` はdogfood上の論理名であり、実装上の `zoom` に対応する。
- `zoom` / `panX` / `panY` / `viewMode` / `hasAutoFitted` / `hasUserInteracted` は GraphViewportState に実装済みの中核状態である。
- `minScale` / `maxScale` / `defaultScale` は実装上の minZoom / maxZoom / initialZoom に対応する制約・初期値の論理名であり、GraphViewportStateの保持フィールドそのものではない。
- `viewportWidth` / `viewportHeight` / `contentWidth` / `contentHeight` は fitToView 等の計算に使われる派生値として扱う。
- `lastCommandType` は操作履歴やデバッグ用途のplanned/helper項目であり、現行実装済みとは断定しない。
- `updatedAt` は ModelingPreviewView の CachedViewportState で使用されるキャッシュメタデータであり、GraphViewportStateの中核状態ではない。

## Source Links
| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/graph-view-shared.ts | GraphViewportState | interface | zoom / pan / fit 状態の実装側定義 |
| src/renderers/graph-view-shared.ts | resetGraphViewportState | function | viewport状態の初期化 |
| src/renderers/graph-view-shared.ts | attachGraphViewportInteractions | function | zoom / pan 状態の更新 |
| src/views/modeling-preview-view.ts | CachedViewportState | interface | file単位のviewportキャッシュ |
| src/views/modeling-preview-view.ts | prepareFileViewportState | method | file切替時のviewport復元 |
| src/views/modeling-preview-view.ts | rememberViewportState | method | viewport状態のキャッシュ保存 |
