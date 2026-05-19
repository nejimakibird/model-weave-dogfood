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
ViewerのpreviewCanvasにおける現在の表示倍率、pan位置、表示領域、描画内容領域を保持する実行時状態を表す。
この状態はMarkdownモデルの正本ではなく、Viewer上の一時的な表示状態である。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| scale | 表示倍率 | number | | Y | scale | | 現在のズーム倍率 |
| panX | 横方向位置 | number | | Y | panX | | X方向の表示位置 |
| panY | 縦方向位置 | number | | Y | panY | | Y方向の表示位置 |
| minScale | 最小倍率 | number | | N | minScale | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | 許容最小ズーム倍率 |
| maxScale | 最大倍率 | number | | N | maxScale | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | 許容最大ズーム倍率 |
| defaultScale | 既定倍率 | number | | N | defaultScale | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | resetZoom時の倍率 |
| viewportWidth | Viewport幅 | Optional<number> | | N | viewportBounds.width | | 表示領域の幅 |
| viewportHeight | Viewport高さ | Optional<number> | | N | viewportBounds.height | | 表示領域の高さ |
| contentWidth | コンテンツ幅 | Optional<number> | | N | contentBounds.width | | 描画済みコンテンツの幅 |
| contentHeight | コンテンツ高さ | Optional<number> | | N | contentBounds.height | | 描画済みコンテンツの高さ |
| lastCommandType | 最終コマンド種別 | Optional<CODE-MW-VIEWER-COMMAND-TYPE> | | N | lastCommandType | [[CODE-MW-VIEWER-COMMAND-TYPE]] | 最後に適用されたViewport操作 |
| updatedAt | 更新タイミング | Optional<string> | | N | updatedAt | | 実行時状態の更新タイミング。永続化対象ではない |

## Notes
- Viewport状態はViewer実行時の一時状態であり、Markdown本文やfrontmatterには保存しない。
- `scale` は [[RULE-MW-VIEWER-ZOOM-LIMITS]] の範囲に収まる。
- `panX` / `panY` は fit / pan / wheel_zoom 操作で変化する。
- `lastCommandType` は操作履歴やデバッグ用途に使用できる。

## Source Links
| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/renderers/graph-view-shared.ts | attachGraphViewportInteractions | function | zoom / pan 状態の更新 |
| source/model-weave-repo/src/renderers/zoom-toolbar.ts | createZoomToolbar | function | toolbar操作の発生元 |