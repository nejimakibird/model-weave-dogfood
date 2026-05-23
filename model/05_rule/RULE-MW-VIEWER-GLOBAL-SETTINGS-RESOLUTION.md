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

- `defaultRenderMode` は src/settings/model-weave-settings.ts に実装済みの設定であり、未設定の場合は `auto` を使用する。
- `defaultRenderMode` が対象モデルで利用不可の場合は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に従って実効値を解決する。render mode 解決の詳細は本ルールでは扱わない。
- DFDでは `[[RULE-MW-DFD-MERMAID-ONLY]]` により `mermaid` 固定を優先する。
- `defaultZoom` は src/settings/model-weave-settings.ts に実装済みの設定であり、`fit` の場合は初回表示時に `fitToView` を実行する。
- `defaultZoom` が `100` の場合、Viewport側の `zoom=1.0` / `scale=1.0` 相当を初期値とする。
- `defaultZoom` が不正値の場合、安全側として `fit` または `1.0` にフォールバックする。
- `fontSize` は src/settings/model-weave-settings.ts に実装済みの設定であり、Viewer表示サイズに影響する。具体的な反映は ModelingPreviewView の viewerPreferences と [[DATA-MW-VIEWER-STATE]] に委ねる。
- `nodeDensity` は src/settings/model-weave-settings.ts に実装済みの設定であり、ノード密度・表示間隔に影響する。具体的な反映は ModelingPreviewView の viewerPreferences と [[DATA-MW-VIEWER-STATE]] に委ねる。
- `autoFitOnOpen` が `true` の場合、[[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] に従う。ただし、この設定項目は現行ModelWeaveSettingsには未実装のplanned項目として扱う。
- `preserveViewportState` が `true` の場合、[[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] に従い、ファイル単位のviewport状態を復元してよい。ただし、この設定項目は現行ModelWeaveSettingsには未実装のplanned / partial項目として扱う。
- `diagnosticsVisible` が `false` の場合でも、内部診断の生成自体は継続してよい。表示（UIパネル）のみ抑制する。ただし、この設定項目は現行ModelWeaveSettingsには未実装のplanned項目として扱う。
- `statusBarVisible` が `false` の場合、ステータスバーUIのみ非表示にし、Viewer状態（renderStatus等）自体は維持する。ただし、この設定項目は現行ModelWeaveSettingsには未実装のplanned項目として扱う。
- `enableSourceLinks` は Source Links Explorer / Source Links索引化が future のため、現行実装済み設定として扱わない。
- 設定解決はViewerの一時状態へ反映され、Markdown本文やfrontmatterは変更しない。

## Parameters

| name | value | notes |
|---|---|---|
| fallbackRenderMode | auto | defaultRenderMode未設定時 |
| fallbackZoomMode | fit | defaultZoom不正時 |
| defaultScale | 1.0 | 100%表示 |
| fontSizeDefault | normal | implemented。DEFAULT_MODEL_WEAVE_SETTINGS.fontSize の既定値 |
| nodeDensityDefault | normal | implemented。DEFAULT_MODEL_WEAVE_SETTINGS.nodeDensity の既定値 |
| preserveViewportDefault | true | planned / partial。現行ModelWeaveSettingsには未実装の先行設計値 |
| diagnosticsVisibleDefault | true | planned。現行ModelWeaveSettingsには未実装の先行設計値 |
| statusBarVisibleDefault | true | planned。現行ModelWeaveSettingsには未実装の先行設計値 |

## Messages

| condition | message | severity | notes |
|---|---|---|---|
| invalid render mode setting | Invalid render mode setting. Fallback applied. | warning | defaultRenderModeが不正 |
| invalid zoom setting | Invalid zoom setting. Fallback applied. | warning | defaultZoomが不正 |
| settings fallback applied | Viewer settings fallback applied. | info | 既定値を使用 |

## Notes

- 本ルールはObsidianプラグイン設定からViewer実行時の実効値を解決する。
- 現行実装側の設定項目は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- この rule は現行実装済み設定とdogfood先行設計項目を含むため、全体としては partial と扱う。
- dogfood側のみの設定項目は、現時点でModel Weave本体に実装済みとは断定しない。
- 実際の設定保存はObsidianプラグイン設定に委ねる。
- Renderer選択の詳細は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に委ねる。
- render mode の詳細解決は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に委ねる。
- zoom / pan / fit の表示状態は [[DATA-MW-VIEWPORT-STATE]] に委ねる。
- Viewport初期化と状態復元は [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] および [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] に委ねる。ただし autoFitOnOpen / preserveViewportState は現行ModelWeaveSettingsには未実装の先行設計項目である。
- fontSize / nodeDensity は現行実装済み設定だが、Viewer State / Renderer側の反映モデルは後続で整理する場合がある。
- mermaidThemeMode / viewerFontScale / diagnosticsVisible / statusBarVisible はplanned項目として扱い、現行実装済みとは断定しない。
- enableSourceLinks は Source Links Explorer / Source Links索引化が future のため、現行実装済みとは扱わない。
- 本ルールはMarkdownモデルの内容を変更しない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| src/settings/model-weave-settings.ts | DEFAULT_MODEL_WEAVE_SETTINGS | constant | 既定設定値 |
| src/settings/model-weave-settings.ts | normalizeModelWeaveSettings | function | 設定値の正規化 |
| src/core/render-mode.ts | resolveRenderMode | function | render mode実効値の解決 |
| src/views/modeling-preview-view.ts | applyViewerSettings | method | fontSize / nodeDensity などViewer表示設定の反映 |
| src/views/modeling-preview-view.ts | prepareFileViewportState | method | zoom / viewport初期状態の解決 |
| src/renderers/graph-view-shared.ts | GraphViewportState | interface | zoom / pan / fit 状態 |
