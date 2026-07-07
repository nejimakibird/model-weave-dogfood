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
| target | [[DATA-MW-VIEWER-PREFERENCES]] | Viewerへ渡される表示設定 |
| target | [[DATA-MW-VIEWPORT-STATE]] | Viewport表示状態 |
| target | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | Rendererモード解決入力 |
| rule | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | 設定値の実効解決 |
| rule | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | render mode解決 |
| rule | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | auto fit制御 |
| rule | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | viewport状態保持 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[DATA-MW-PLUGIN-SETTINGS]] | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]].settingsDefaultRenderMode | formatType / modelKind に応じて形式別既定値を1つ選択 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。getDefaultRenderModeForFormat が defaultClassRenderMode / defaultErRenderMode / defaultDfdRenderMode / defaultProcessRenderMode / defaultScreenRenderMode から選ぶ。defaultRenderMode はlegacy補助入力 |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultBusinessFlowDirection | [[DATA-MW-VIEWER-PREFERENCES]].defaultBusinessFlowDirection | ViewerPreferencesへ選択 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。app_process Business Flow方向解決のsettings default |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultBusinessFlowDirection | [[DATA-MW-VIEWER-STATE]].businessFlowDirection | toolbar override / frontmatter / settings defaultから実効方向を解決 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented / derived。Markdown frontmatterは書き換えない |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultDomainsViewMode | [[DATA-MW-VIEWER-PREFERENCES]].defaultDomainsViewMode | ViewerPreferencesへ選択 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。domains表示の既定モード |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultDomainDiagramViewMode | [[DATA-MW-VIEWER-PREFERENCES]].defaultDomainDiagramViewMode | ViewerPreferencesへ選択 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。domain_diagram表示の既定モード |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | [[DATA-MW-VIEWPORT-STATE]].defaultScale | fit または 100 を初期値へ変換 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented / partial。src/settings/model-weave-settings.ts に実装済み。実装上は viewerPreferences.defaultZoom から GraphViewportState.zoom / viewMode / auto fit へ反映され、dogfood側の defaultScale とは完全一致しない |
| [[DATA-MW-PLUGIN-SETTINGS]].fontSize | [[DATA-MW-VIEWER-STATE]].fontSize | Viewer表示文字サイズとして反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。src/settings/model-weave-settings.ts に実装済み設定。実装上は viewerPreferences.fontSize からCSS class / CSS variablesへ反映 |
| [[DATA-MW-PLUGIN-SETTINGS]].nodeDensity | [[DATA-MW-VIEWER-STATE]].nodeDensity | Viewer表示密度として反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。src/settings/model-weave-settings.ts に実装済み設定。実装上は viewerPreferences.nodeDensity からCSS class / spacingへ反映 |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | [[DATA-MW-VIEWER-PREFERENCES]].defaultZoom | ViewerPreferencesへ選択 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。getViewerPreferencesでViewerへ渡される |
| [[DATA-MW-PLUGIN-SETTINGS]].fontSize | [[DATA-MW-VIEWER-PREFERENCES]].fontSize | ViewerPreferencesへ選択 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。applyViewerSettingsで表示へ反映 |
| [[DATA-MW-PLUGIN-SETTINGS]].nodeDensity | [[DATA-MW-VIEWER-PREFERENCES]].nodeDensity | ViewerPreferencesへ選択 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。applyViewerSettingsで表示へ反映 |
| [[DATA-MW-PLUGIN-SETTINGS]].localSourceRoot | [[DATA-MW-VIEWER-PREFERENCES]].localSourceRoot | ViewerPreferencesへ選択 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。Source Links表示時のrelative path解決root |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultColorSchemeRef | [[DATA-MW-VIEWER-STATE]].resolvedColorScheme | vault refまたはpathをcolor_schemeへ解決 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented / derived。描画時に resolveDefaultColorScheme で解決し、未設定または解決不能時は組み込み配色を使う |
| [[DATA-MW-PLUGIN-SETTINGS]].showMermaidRenderDebug | [[DATA-MW-VIEWER-PREFERENCES]].showMermaidRenderDebug | ViewerPreferencesへ選択 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。Mermaid描画診断表示オプション |
| [[DATA-MW-PLUGIN-SETTINGS]].uiLanguage | [[DATA-MW-VIEWER-PREFERENCES]].uiLanguage | ViewerPreferencesへ選択 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。createModelWeaveTranslatorへ渡される |
| [[DATA-MW-PLUGIN-SETTINGS]].enableRelationshipView | [[DATA-MW-VIEWER-STATE]].relationshipViewEnabled | falseの場合はimpact summary生成とRelationship View表示準備を抑制 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。ensureImpactIndexReady / buildImpactPreviewProps のゲート |
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
- 現行実装のrender mode既定値は形式別設定から1つ選択され、DATA-MW-RENDERER-RENDER-MODE-INPUT の settingsDefaultRenderMode へ渡される。
- defaultRenderMode は旧設定名であり、normalizeModelWeaveSettingsのlegacyDefaultRenderModeとしてのみ扱う。
- toolbar overrideとfrontmatter render_modeは保存設定ではなく、render mode解決時の優先入力として扱う。
- defaultBusinessFlowDirectionはBusiness Flow方向解決のsettings defaultであり、toolbar overrideとfrontmatter flow_directionより低い優先度で使う。
- Settings Tabの各onChangeは updateSettings を呼び出し、saveData後に refreshOpenModelWeaveViews により開いているPreviewへ設定を再反映する。
- App Process Flow Connect Modeとref-aware Business Flow step hover / click targetは app_process Business Flow 限定のViewer一時状態であり、プラグイン設定からViewer Stateへ単純転記しない。
- DFD / Class / ER の接続編集や汎用ノードエディタとしては扱わない。
- defaultZoom=fit の場合、`viewportState.defaultScale` へ直接数値を入れるのではなく、auto fit制御へ委譲する。
- fontSize / nodeDensity は DATA-MW-VIEWER-STATE の現行Fieldへ接続し、ModelingPreviewView の viewerPreferences 経由で表示へ反映する。
- localSourceRoot / showMermaidRenderDebug / uiLanguage は [[DATA-MW-VIEWER-PREFERENCES]] としてViewerへ渡される。
- defaultColorSchemeRef は現行 getViewerPreferences には含まれず、描画処理で settings から color_scheme 解決へ渡される。
- enableRelationshipView はRelationship / impact summary の生成と表示準備を制御するゲートとして扱い、ImpactSummary自体は現在のmodelとVault Indexから派生生成される。
- Weave MapはImpactSummaryから生成されるRelationship View内の派生表示であり、設定値として保存しない。
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
- defaultZoom / fontSize / nodeDensity / localSourceRoot / showMermaidRenderDebug / uiLanguage は [[DATA-MW-VIEWER-PREFERENCES]] として getViewerPreferences / applyViewerSettings 経由でViewerへ反映される。
- defaultColorSchemeRef は描画時の color_scheme 解決入力であり、ViewerPreferencesへの単純転記とは扱わない。
- Relationship View内のWeave Map layer coloringは、defaultColorSchemeRefから解決されたColor Schemeを描画時に渡して反映する。
- defaultBusinessFlowDirection / defaultDomainsViewMode / defaultDomainDiagramViewMode も [[DATA-MW-VIEWER-PREFERENCES]] としてViewerへ渡される。
- viewerFontScale はdogfood先行項目であり、fontSizeとは別のplanned設定として扱う。

## Source Links

| path | notes |
|---|---|
| src/settings/model-weave-settings.ts | symbol: ModelWeaveSettings; kind: type; プラグイン設定構造 |
| src/settings/model-weave-settings.ts | symbol: DEFAULT_MODEL_WEAVE_SETTINGS; kind: constant; 既定設定値 |
| src/core/render-mode.ts | symbol: ResolveRenderModeInput; kind: interface; settingsDefaultRenderMode の入力先 |
| src/core/render-mode.ts | symbol: resolveRenderMode; kind: function; render mode実効値の解決 |
| src/core/app-process-business-flow-direction.ts | symbol: resolveAppProcessBusinessFlowDirection; kind: function; Business Flow directionの解決 |
| src/core/color-scheme.ts | symbol: resolveDefaultColorScheme; kind: function; defaultColorSchemeRef の解決 |
| src/settings/model-weave-settings.ts | symbol: ModelWeaveViewerPreferences; kind: type; Viewerへ渡される表示設定 |
| src/main.ts | symbol: ModelWeavePlugin.updateSettings; kind: method; 設定変更の保存とPreview更新 |
| src/main.ts | symbol: ModelWeavePlugin.refreshOpenModelWeaveViews; kind: method; 開いているPreviewへViewerPreferencesを再反映 |
| src/main.ts | symbol: ModelWeavePlugin.getViewerPreferences; kind: method; Viewerへ渡す設定の選択 |
| src/main.ts | symbol: ModelWeavePlugin.getDefaultRenderModeForFormat; kind: method; 形式別render mode既定値の選択 |
| src/views/modeling-preview-view.ts | symbol: applyViewerSettings; kind: method; viewerPreferences の反映 |
| src/views/modeling-preview-view.ts | symbol: renderCurrentState; kind: method; Viewer状態の反映 |
| src/views/modeling-preview-view.ts | symbol: prepareFileViewportState; kind: method; viewport初期化 |
