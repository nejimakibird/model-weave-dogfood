---
type: rule
id: RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION
name: Viewerグローバル設定解決ルール
kind: settings_rule
tags:
  - ModelWeave
  - Viewer
  - Settings
  - Rule
---

# Viewerグローバル設定解決ルール

## Summary

Model Weaveプラグイン設定から、Viewer / Renderer / Viewport が実際に使用する実効設定値を解決するルールを定義する。
未設定または不正値がある場合は、安全な既定値へフォールバックする。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| pluginSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | Y | Obsidianプラグイン設定 |
| viewerState | [[DATA-MW-VIEWER-STATE]] | Viewer | N | Viewer実行時状態 |
| viewportState | [[DATA-MW-VIEWPORT-STATE]] | Viewer | N | Viewport表示状態 |
| renderModeInput | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | Renderer | N | レンダリングモード解決入力 |
| renderModeRule | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | Rule | Y | render modeの解決方針 |
| dfdMermaidOnlyRule | [[RULE-MW-DFD-MERMAID-ONLY]] | Rule | N | DFDのMermaid固定方針 |
| zoomLimitRule | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | Rule | Y | zoom値の制約 |
| autoFitRule | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | Rule | N | auto fit方針 |
| viewportPersistenceRule | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | Rule | N | viewport状態保持方針 |

## Conditions

- `defaultRenderMode` が未設定の場合は `auto` を使用する。
- `defaultRenderMode` が対象モデルで利用不可の場合は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に従って実効値を解決する。
- DFDでは `[[RULE-MW-DFD-MERMAID-ONLY]]` により `mermaid` 固定を優先する。
- `defaultZoom` が `fit` の場合、初回表示時に `fitToView` を実行する。
- `defaultZoom` が `100%` の場合、`scale=1.0` を初期値とする。
- `defaultZoom` が不正値の場合、安全側として `fit` または `1.0` にフォールバックする。
- `autoFitOnOpen` が `true` の場合、[[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] に従う。
- `preserveViewportState` が `true` の場合、[[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] に従い、ファイル単位のviewport状態を復元してよい。
- `diagnosticsVisible` が `false` の場合でも、内部診断の生成自体は継続してよい。表示（UIパネル）のみ抑制する。
- `statusBarVisible` が `false` の場合、ステータスバーUIのみ非表示にし、Viewer状態（renderStatus等）自体は維持する。
- 設定解決はViewerの一時状態へ反映され、Markdown本文やfrontmatterは変更しない。

## Parameters

| name | value | notes |
|---|---|---|
| fallbackRenderMode | auto | defaultRenderMode未設定時 |
| fallbackZoomMode | fit | defaultZoom不正時 |
| defaultScale | 1.0 | 100%表示 |
| preserveViewportDefault | true | Viewport状態保持の既定 |
| diagnosticsVisibleDefault | true | 診断パネル表示の既定 |
| statusBarVisibleDefault | true | ステータスバー表示の既定 |

## Messages

| condition | message | severity | notes |
|---|---|---|---|
| invalid render mode setting | Invalid render mode setting. Fallback applied. | warning | defaultRenderModeが不正 |
| invalid zoom setting | Invalid zoom setting. Fallback applied. | warning | defaultZoomが不正 |
| settings fallback applied | Viewer settings fallback applied. | info | 既定値を使用 |

## Notes

- 本ルールはObsidianプラグイン設定からViewer実行時の実効値を解決する。
- 実際の設定保存はObsidianプラグイン設定に委ねる。
- Renderer選択の詳細は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に委ねる。
- Viewport初期化と状態復元は [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] および [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] に委ねる。
- 本ルールはMarkdownモデルの内容を変更しない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| source/model-weave-repo/src/settings.ts | DEFAULT_SETTINGS | constant | 既定設定値 |
| source/model-weave-repo/src/core/render-mode.ts | resolveRenderMode | function | render mode実効値の解決 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | prepareFileViewportState | method | zoom / viewport初期状態の解決 |