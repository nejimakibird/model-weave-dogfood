---
type: codeset
id: CODE-MW-VIEWER-COMMAND-TYPE
name: Viewerコマンド種別
kind: enum
tags:
  - Viewer
  - Viewport
  - CodeSet
---

# Viewerコマンド種別

## Summary
ViewerのViewport操作で使用するコマンド種別を定義する。
[[DATA-MW-VIEWPORT-COMMAND]].commandType の許容値として使用する。

## Values

| code | label | sort_order | active | notes |
|---|---|---|---|---|
| fit | Fit to View | 10 | Y | 図全体が表示領域に収まるように倍率と位置を調整する |
| zoom_in | Zoom In | 20 | Y | 現在倍率から1段階拡大する |
| zoom_out | Zoom Out | 30 | Y | 現在倍率から1段階縮小する |
| reset_zoom | Reset Zoom | 40 | Y | 100%またはdefaultZoomへ戻す |
| pan | Pan | 50 | Y | 表示位置を移動する。ドラッグ操作などで使用 |
| wheel_zoom | Wheel Zoom | 60 | Y | マウスホイールやトラックパッドによるズーム操作 |

## Notes
- 初期のツールバー操作では fit / zoom_in / zoom_out / reset_zoom を主に使用する。
- pan / wheel_zoom はツールバー操作ではなく、Viewport直接操作で使用する。
- commandTypeの値は [[DATA-MW-VIEWPORT-COMMAND]].commandType で参照される。
- ズーム倍率の境界値は [[RULE-MW-VIEWER-ZOOM-LIMITS]] に従う。