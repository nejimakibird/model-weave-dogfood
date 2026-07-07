---
type: rule
id: RULE-MW-VIEWER-ZOOM-LIMITS
name: Viewerズーム倍率制限ルール
kind: constraint
tags:
  - Viewer
  - Zoom
  - Rule
---

# Viewerズーム倍率制限ルール

## Summary
Viewerのズーム倍率について、最小値、最大値、既定値、ステップ倍率の扱いを定義する。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| viewportCommand | [[DATA-MW-VIEWPORT-COMMAND]] | Viewer | Y | ズーム操作の入力コマンド |
| mainFrame | [[SCR-MW-VIEWER-MAIN-FRAME]] | Viewer UI | N | 操作元となるViewer画面 |
| toolbarVisibilityRule | [[RULE-MW-VIEWER-TOOLBAR-VISIBILITY]] | Rule | N | 操作ボタンの有効/無効条件 |

## Conditions

- `targetScale` は最小ズーム倍率より小さくしてはいけない。
- `targetScale` は最大ズーム倍率より大きくしてはいけない。
- `zoomIn` は現在倍率にズームステップを掛ける。
- `zoomOut` は現在倍率をズームステップで割る。
- `reset_zoom` は 100% または plugin設定の defaultZoom に戻す。
- `fit` は viewportBounds と contentBounds から計算した倍率を、最小/最大範囲内に丸める。
- ズーム操作はViewerの一時状態だけを変更し、Markdown本文やfrontmatterは変更しない。

## Parameters

| name | value | notes |
|---|---|---|
| minZoom | 0.45 | 最小倍率 |
| maxZoom | 2.4 | 最大倍率 |
| defaultZoom | 1.0 | 100%表示 |
| zoomStep | 1.12 | zoomIn / zoomOut の標準倍率変化 |

## Messages

| condition | message | severity | notes |
|---|---|---|---|
| zoom below minimum | Minimum zoom level reached. | info | 下限に丸める |
| zoom above maximum | Maximum zoom level reached. | info | 上限に丸める |
| invalid viewport bounds | Cannot fit preview because viewport size is unavailable. | warning | fitToView計算不可 |

## Notes

- 本ルールはズーム倍率の境界条件を定義する。
- 実際のscale/pan計算は実装側に委ねる。
- fitToViewは図全体を収めるための派生計算であり、Markdownモデルの内容を変更しない。

## Source Links

| path | notes |
|---|---|
| src/renderers/graph-view-shared.ts | symbol: attachGraphViewportInteractions; kind: function; zoom/pan操作の起点 |
| src/renderers/zoom-toolbar.ts | symbol: createZoomToolbar; kind: function; zoomボタン操作の起点 |
