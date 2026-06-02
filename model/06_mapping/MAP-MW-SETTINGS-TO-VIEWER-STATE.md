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
| [[DATA-MW-PLUGIN-SETTINGS]].defaultClassRenderMode | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]].settingsDefaultRenderMode | formatType / modelKind が class または class_diagram の場合に反映 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。ModelWeavePlugin.getDefaultRenderModeForFormat で選択され、resolveRenderModeへ渡される |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultErRenderMode | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]].settingsDefaultRenderMode | formatType / modelKind が er_entity または er_diagram の場合に反映 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。ModelWeavePlugin.getDefaultRenderModeForFormat で選択され、resolveRenderModeへ渡される |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultDfdRenderMode | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]].settingsDefaultRenderMode | formatType が dfd_diagram の場合に反映 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。ModelWeavePlugin.getDefaultRenderModeForFormat で選択され、resolveRenderModeへ渡される |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultProcessRenderMode | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]].settingsDefaultRenderMode | formatType が app_process の場合に反映 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。ModelWeavePlugin.getDefaultRenderModeForFormat で選択され、resolveRenderModeへ渡される |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultScreenRenderMode | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]].settingsDefaultRenderMode | formatType が screen の場合に反映 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。ModelWeavePlugin.getDefaultRenderModeForFormat で選択され、resolveRenderModeへ渡される |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultRenderMode | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]].settingsDefaultRenderMode | 旧設定値をclass / ER既定値の補助入力として正規化する | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | legacy / migration fallback。現行の主設定ではない。normalizeModelWeaveSettingsがlegacyDefaultRenderModeとして読む |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | [[DATA-MW-VIEWPORT-STATE]].defaultScale | fit または 100 を初期値へ変換 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented / partial。src/settings/model-weave-settings.ts に実装済み。実装上は viewerPreferences.defaultZoom から GraphViewportState.zoom / viewMode / auto fit へ反映され、dogfood側の defaultScale とは完全一致しない |
| [[DATA-MW-PLUGIN-SETTINGS]].fontSize | [[DATA-MW-VIEWER-STATE]].fontSize | Viewer表示文字サイズとして反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。src/settings/model-weave-settings.ts に実装済み設定。実装上は viewerPreferences.fontSize からCSS class / CSS variablesへ反映 |
| [[DATA-MW-PLUGIN-SETTINGS]].nodeDensity | [[DATA-MW-VIEWER-STATE]].nodeDensity | Viewer表示密度として反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。src/settings/model-weave-settings.ts に実装済み設定。実装上は viewerPreferences.nodeDensity からCSS class / spacingへ反映 |
| [[DATA-MW-PLUGIN-SETTINGS]].localSourceRoot | ModelingPreviewView.viewerPreferences.localSourceRoot | Source Links表示時のrelative path解決rootとして反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。getViewerPreferences / applyViewerSettings 経由でViewerへ渡される。DATA-MW-VIEWER-STATEには専用Fieldなし |
| [[DATA-MW-PLUGIN-SETTINGS]].showMermaidRenderDebug | ModelingPreviewView.viewerPreferences.showMermaidRenderDebug | Mermaid描画診断表示オプションとして反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。getViewerPreferences / applyViewerSettings 経由でViewerへ渡され、Mermaid描画オプションに使われる。DATA-MW-VIEWER-STATEには専用Fieldなし |
| [[DATA-MW-PLUGIN-SETTINGS]].uiLanguage | ModelingPreviewView.viewerPreferences.uiLanguage | Viewer翻訳器の言語として反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。applyViewerSettingsでcreateModelWeaveTranslatorへ渡される。DATA-MW-VIEWER-STATEには専用Fieldなし |
| [[DATA-MW-PLUGIN-SETTINGS]].enableRelationshipView | Relationship / impact preview props | falseの場合はimpact summary生成とRelationship View表示準備を抑制 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。ensureImpactIndexReady / buildImpactPreviewProps のゲート。DATA-MW-VIEWER-STATEには専用Fieldなし |
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
- 現行実装のrender mode既定値は形式別設定から選択され、DATA-MW-RENDERER-RENDER-MODE-INPUT の settingsDefaultRenderMode へ渡される。
- defaultRenderMode は旧設定名であり、normalizeModelWeaveSettingsのlegacyDefaultRenderModeとしてのみ扱う。
- defaultZoom=fit の場合、`viewportState.defaultScale` へ直接数値を入れるのではなく、auto fit制御へ委譲する。
- fontSize / nodeDensity は DATA-MW-VIEWER-STATE の現行Fieldへ接続し、ModelingPreviewView の viewerPreferences 経由で表示へ反映する。
- localSourceRoot / showMermaidRenderDebug / uiLanguage は ModelWeaveViewerPreferences としてViewerへ渡されるが、現行DATA-MW-VIEWER-STATEには専用Fieldがないため、viewerPreferences上の設定として扱う。
- enableRelationshipView はViewerStateの単純な表示設定ではなく、Relationship / impact summary の生成と表示準備を制御するゲートとして扱う。
- この mapping は現行実装済み設定とdogfood先行設計項目を含むため、全体として partial と扱う。
- 現行実装済み設定の正は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS とする。
- dogfood側のみの設定項目は、現時点でModel Weave本体に実装済みとは断定しない。
- diagnosticsVisible=false の場合でも診断生成は継続し、UI表示（パネル）のみ抑制する。
- statusBarVisible=false の場合でもViewer状態は維持し、UI表示のみ抑制する。
- 設定反映はViewerの一時状態へ行い、Markdown本文やfrontmatterは変更しない。

## Notes

- 本mappingはObsidianプラグイン設定とViewer実行時状態の橋渡しを表す。
- 実際の設定保存はObsidianプラグイン設定に委ねる。
- 設定項目が実装上の名称と完全一致しない場合は、Source Links確認後に補正する。
- fontSize / nodeDensity は [[DATA-MW-VIEWER-STATE]] に対応Fieldがあるため、このmappingで接続済みである。
- defaultZoom / fontSize / nodeDensity / localSourceRoot / showMermaidRenderDebug / uiLanguage は getViewerPreferences / applyViewerSettings 経由でViewerへ反映される。
- viewerFontScale はdogfood先行項目であり、fontSizeとは別のplanned設定として扱う。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| src/settings/model-weave-settings.ts | DEFAULT_MODEL_WEAVE_SETTINGS | constant | 既定設定値 |
| src/core/render-mode.ts | ResolveRenderModeInput | interface | settingsDefaultRenderMode の入力先 |
| src/core/render-mode.ts | resolveRenderMode | function | render mode実効値の解決 |
| src/settings/model-weave-settings.ts | ModelWeaveViewerPreferences | type | defaultZoom / fontSize / nodeDensity のViewer設定 |
| src/main.ts | ModelWeavePlugin.getViewerPreferences | method | Viewerへ渡す設定の選択 |
| src/main.ts | ModelWeavePlugin.getDefaultRenderModeForFormat | method | 形式別render mode既定値の選択 |
| src/views/modeling-preview-view.ts | applyViewerSettings | method | viewerPreferences の反映 |
| src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer状態の反映 |
| src/views/modeling-preview-view.ts | prepareFileViewportState | method | viewport初期化 |
