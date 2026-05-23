---
type: mapping
id: MAP-MW-SETTINGS-TO-VIEWER-STATE
name: Settings to Viewer State Mapping
kind: data_to_data
source: "[[DATA-MW-PLUGIN-SETTINGS]]"
target: "[[DATA-MW-VIEWER-STATE]]"
tags:
  - ModelWeave
  - Viewer
  - Settings
  - Mapping
---

# Settings to Viewer State Mapping

## Summary

Model Weaveプラグイン設定を、Viewer / Renderer / Viewport が使用する実行時状態へ反映するためのマッピングを定義する。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-PLUGIN-SETTINGS]] | Obsidianプラグイン設定 |
| target | [[DATA-MW-VIEWER-STATE]] | Viewer実行時状態 |
| target | [[DATA-MW-VIEWPORT-STATE]] | Viewport表示状態 |
| target | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | Rendererモード解決入力 |
| rule | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | 設定値の実効解決 |
| rule | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | render mode解決 |
| rule | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | auto fit制御 |
| rule | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | viewport状態保持 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[DATA-MW-PLUGIN-SETTINGS]].defaultRenderMode | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]].settingsDefaultRenderMode | 未設定/不正値を補正して反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。src/settings/model-weave-settings.ts に実装済み。実効値は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] で決定 |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | [[DATA-MW-VIEWPORT-STATE]].defaultScale | fit または 100 を初期値へ変換 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented / partial。src/settings/model-weave-settings.ts に実装済み。実装上は viewerPreferences.defaultZoom から GraphViewportState.zoom / viewMode / auto fit へ反映され、dogfood側の defaultScale とは完全一致しない |
| [[DATA-MW-PLUGIN-SETTINGS]].autoFitOnOpen | [[DATA-MW-VIEWER-STATE]].autoFitEnabled | boolean値をViewer状態へ反映 | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | N | planned。Auto Fit挙動の将来設定。現行ModelWeaveSettingsには未実装 |
| [[DATA-MW-PLUGIN-SETTINGS]].preserveViewportState | [[DATA-MW-VIEWER-STATE]].preserveViewportState | boolean値をViewer状態へ反映 | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | N | planned。Viewport状態保持の将来設定。現行ModelWeaveSettingsには未実装 |
| [[DATA-MW-PLUGIN-SETTINGS]].diagnosticsVisible | [[DATA-MW-VIEWER-STATE]].diagnosticsVisible | boolean値をViewer状態へ反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | planned。診断パネル表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| [[DATA-MW-PLUGIN-SETTINGS]].statusBarVisible | [[DATA-MW-VIEWER-STATE]].statusBarVisible | boolean値をViewer状態へ反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | planned。ステータスバー表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| [[DATA-MW-PLUGIN-SETTINGS]].viewerFontScale | [[DATA-MW-VIEWER-STATE]].viewerFontScale | small / normal / large 等を反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | planned。Viewer表示調整用の将来設定。現行ModelWeaveSettingsには未実装 |
| [[DATA-MW-PLUGIN-SETTINGS]].mermaidThemeMode | [[DATA-MW-VIEWER-STATE]].mermaidThemeMode | Mermaid描画テーマ方針として保持 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | planned。Mermaidテーマ制御の将来設定。現行ModelWeaveSettingsには未実装 |
| [[DATA-MW-PLUGIN-SETTINGS]].enableSourceLinks | [[DATA-MW-VIEWER-STATE]].sourceLinksEnabled | boolean値をViewer状態へ反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | future。Source Links Explorer / Source Links索引化がfutureのため現行実装済みではない |

## Rules

- 設定値の正規化・フォールバックは [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] に従う。
- render mode の最終的な実効値は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に委ねる。
- defaultRenderMode は DATA-MW-RENDERER-RENDER-MODE-INPUT の現行実装側フィールド名 settingsDefaultRenderMode へ接続する。
- defaultZoom=fit の場合、`viewportState.defaultScale` へ直接数値を入れるのではなく、auto fit制御へ委譲する。
- この mapping は現行実装済み設定とdogfood先行設計項目を含むため、全体として partial と扱う。
- 現行実装済み設定の正は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS とする。
- dogfood側のみの設定項目は、現時点でModel Weave本体に実装済みとは断定しない。
- fontSize / nodeDensity は現行実装済み設定だが、Viewer State側に直接対応する target field が未定義のため、このmappingでは未接続とする。実装上は ModelingPreviewView の viewerPreferences からCSS class / CSS variablesへ反映される。
- diagnosticsVisible=false の場合でも診断生成は継続し、UI表示（パネル）のみ抑制する。
- statusBarVisible=false の場合でもViewer状態は維持し、UI表示のみ抑制する。
- 設定反映はViewerの一時状態へ行い、Markdown本文やfrontmatterは変更しない。

## Notes

- 本mappingはObsidianプラグイン設定とViewer実行時状態の橋渡しを表す。
- 実際の設定保存はObsidianプラグイン設定に委ねる。
- 設定項目が実装上の名称と完全一致しない場合は、Source Links確認後に補正する。
- TODO: fontSize は現行実装済み設定だが、[[DATA-MW-VIEWER-STATE]].viewerFontScale はdogfood先行項目のため、直接mappingするかViewer State側に fontSize を追加するか後続で整理する。
- TODO: nodeDensity は現行実装済み設定だが、Viewer State / Viewport State 側に直接対応するtarget fieldがないため、表示密度状態の受け皿追加を後続で検討する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| src/settings/model-weave-settings.ts | DEFAULT_MODEL_WEAVE_SETTINGS | constant | 既定設定値 |
| src/core/render-mode.ts | ResolveRenderModeInput | interface | settingsDefaultRenderMode の入力先 |
| src/core/render-mode.ts | resolveRenderMode | function | render mode実効値の解決 |
| src/views/modeling-preview-view.ts | applyViewerSettings | method | viewerPreferences の反映 |
| src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer状態の反映 |
| src/views/modeling-preview-view.ts | prepareFileViewportState | method | viewport初期化 |
