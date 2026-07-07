---
type: mapping
id: MAP-MW-SETTINGS-UI-TO-PLUGIN-SETTINGS
name: Settings UI to Plugin Settings Mapping
kind: screen_to_data
source: "[[SCR-MW-SETTINGS-TAB]]"
target: "[[DATA-MW-PLUGIN-SETTINGS]]"
tags:
  - ModelWeave
  - Settings
  - Mapping
  - UI
---

# Settings UI to Plugin Settings Mapping

## Summary

Model Weave設定タブのUIフィールドを、プラグイン設定データ [[DATA-MW-PLUGIN-SETTINGS]] の各項目へ保存するためのマッピングを定義する。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[SCR-MW-SETTINGS-TAB]] | Obsidian設定タブUI。現行実装済み設定UIとplanned UI項目が混在する |
| target | [[DATA-MW-PLUGIN-SETTINGS]] | プラグイン設定データ。現行実装済み設定とdogfood先行設計項目を含む |
| rule | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | 設定値の正規化・フォールバック |
| rule | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | render modeの妥当性 |
| rule | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | planned。Auto Fit設定は現行ModelWeaveSettingsには未実装 |
| rule | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | planned / partial。Viewport状態保持設定は現行ModelWeaveSettingsには未実装 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[SCR-MW-SETTINGS-TAB]].defaultClassRenderModeSelect | [[DATA-MW-PLUGIN-SETTINGS]].defaultClassRenderMode | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。Default Class render mode。custom / mermaid / mermaid-detail |
| [[SCR-MW-SETTINGS-TAB]].defaultErRenderModeSelect | [[DATA-MW-PLUGIN-SETTINGS]].defaultErRenderMode | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。Default ER render mode。custom / mermaid / mermaid-detail |
| [[SCR-MW-SETTINGS-TAB]].defaultDfdRenderModeSelect | [[DATA-MW-PLUGIN-SETTINGS]].defaultDfdRenderMode | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。Default DFD render mode。mermaid |
| [[SCR-MW-SETTINGS-TAB]].defaultProcessRenderModeSelect | [[DATA-MW-PLUGIN-SETTINGS]].defaultProcessRenderMode | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。Default Process render mode。custom |
| [[SCR-MW-SETTINGS-TAB]].defaultScreenRenderModeSelect | [[DATA-MW-PLUGIN-SETTINGS]].defaultScreenRenderMode | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。Default Screen render mode。custom |
| [[SCR-MW-SETTINGS-TAB]].defaultRenderModeSelect | [[DATA-MW-PLUGIN-SETTINGS]].defaultRenderMode | 旧設定値として保存する場合のみ使用 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | legacy / not current UI。現行Settings Tabには単一defaultRenderMode selectはない。normalizeModelWeaveSettingsの移行補助用 |
| [[SCR-MW-SETTINGS-TAB]].mermaidThemeModeSelect | [[DATA-MW-PLUGIN-SETTINGS]].mermaidThemeMode | 選択値を保存 | | N | planned。現行ModelWeaveSettingsには未実装 |
| [[SCR-MW-SETTINGS-TAB]].defaultZoomSelect | [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | 選択値を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。fit / 100 |
| [[SCR-MW-SETTINGS-TAB]].autoFitOnOpenToggle | [[DATA-MW-PLUGIN-SETTINGS]].autoFitOnOpen | checkbox値をbooleanとして保存 | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | N | planned。現行ModelWeaveSettingsには未実装 |
| [[SCR-MW-SETTINGS-TAB]].preserveViewportStateToggle | [[DATA-MW-PLUGIN-SETTINGS]].preserveViewportState | checkbox値をbooleanとして保存 | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | N | planned / partial。現行ModelWeaveSettingsには未実装 |
| [[SCR-MW-SETTINGS-TAB]].fontSizeSelect | [[DATA-MW-PLUGIN-SETTINGS]].fontSize | 選択値を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。small / normal / large |
| [[SCR-MW-SETTINGS-TAB]].nodeDensitySelect | [[DATA-MW-PLUGIN-SETTINGS]].nodeDensity | 選択値を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。compact / normal / relaxed |
| [[SCR-MW-SETTINGS-TAB]].enableRelationshipViewToggle | [[DATA-MW-PLUGIN-SETTINGS]].enableRelationshipView | toggle値をbooleanとして保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。Relationship View toggle |
| [[SCR-MW-SETTINGS-TAB]].showMermaidRenderDebugToggle | [[DATA-MW-PLUGIN-SETTINGS]].showMermaidRenderDebug | toggle値をbooleanとして保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。Show Mermaid Render Debug toggle |
| [[SCR-MW-SETTINGS-TAB]].uiLanguageSelect | [[DATA-MW-PLUGIN-SETTINGS]].uiLanguage | 選択値を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。auto / en / ja |
| [[SCR-MW-SETTINGS-TAB]].localSourceRootText | [[DATA-MW-PLUGIN-SETTINGS]].localSourceRoot | 入力文字列を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。relative Source Links outside vault の解決基準 |
| [[SCR-MW-SETTINGS-TAB]].viewerFontScaleSelect | [[DATA-MW-PLUGIN-SETTINGS]].viewerFontScale | 選択値を保存 | | N | planned。現行ModelWeaveSettingsには未実装。fontSizeとの関係整理が必要 |
| [[SCR-MW-SETTINGS-TAB]].diagnosticsVisibleToggle | [[DATA-MW-PLUGIN-SETTINGS]].diagnosticsVisible | checkbox値をbooleanとして保存 | | N | planned。現行ModelWeaveSettingsには未実装 |
| [[SCR-MW-SETTINGS-TAB]].statusBarVisibleToggle | [[DATA-MW-PLUGIN-SETTINGS]].statusBarVisible | checkbox値をbooleanとして保存 | | N | planned。現行ModelWeaveSettingsには未実装 |
| [[SCR-MW-SETTINGS-TAB]].enableSourceLinksToggle | [[DATA-MW-PLUGIN-SETTINGS]].enableSourceLinks | checkbox値をbooleanとして保存 | | N | future。Source Links Explorer / Source Links索引化がfutureのため現行実装済みではない |

## Rules

- 各UIフィールドの値は [[DATA-MW-PLUGIN-SETTINGS]] の対応項目へ保存する。
- select項目は許可された値の範囲に正規化してから保存する。
- checkbox項目はboolean値として保存する。
- 不正値または未設定値は [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] に従ってフォールバックする。
- 現行実装側の正規化対象は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS とする。
- 現行Settings Tabは単一のdefaultRenderModeではなく、形式別のdefaultClassRenderMode / defaultErRenderMode / defaultDfdRenderMode / defaultProcessRenderMode / defaultScreenRenderModeを保存する。
- defaultRenderModeは現行UIの主項目ではなく、normalizeModelWeaveSettingsのlegacyDefaultRenderMode用にのみ扱う。
- `resetSettingsButton` は個別項目への通常マッピングではなく、`resetToDefaultSettings` 論理プロセスにより [[DATA-MW-PLUGIN-SETTINGS]] 全体を既定値へ戻す。
- `refreshOpenViewsButton` は設定保存ではなく、開いているViewerを現在設定で再描画する操作である。
- 設定変更はObsidianプラグイン設定へ保存され、Markdownモデル本文やfrontmatterは変更しない。

## Notes

- 現行実装側の設定項目は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- この mapping は現行実装済み設定とdogfood先行設計項目を含むため、全体として partial / planned と扱う。
- defaultClassRenderMode / defaultErRenderMode / defaultDfdRenderMode / defaultProcessRenderMode / defaultScreenRenderMode / defaultZoom / fontSize / nodeDensity / enableRelationshipView / showMermaidRenderDebug / uiLanguage / localSourceRoot は現行ModelWeaveSettingsに実装済みのUI→設定マッピングとして扱う。
- defaultRenderMode は旧設定名であり、現行Settings Tabの主UI項目ではない。
- autoFitOnOpen / preserveViewportState / mermaidThemeMode / viewerFontScale / diagnosticsVisible / statusBarVisible / settingsVersion はdogfood先行設計項目であり、現行ModelWeaveSettingsには未実装である。
- enableSourceLinks は Source Links Explorer / Source Links索引化がfutureのため、現行実装済みとは扱わない。
- dogfood側のみの設定項目は、現時点でModel Weave本体に実装済みとは断定しない。
- `saveSettings` / `resetToDefaultSettings` は [[PROC-MW-SETTINGS-SAVE]] / [[PROC-MW-SETTINGS-RESET-TO-DEFAULTS]] と同様に logical / planned process として扱う。
- checkboxはUI種別として扱い、planned項目を含む場合は実装済みSetting.addToggleとは断定しない。
- 本mappingは設定画面UIとプラグイン設定データの対応関係を表す。

## Source Links

| path | notes |
|---|---|
| src/main.ts | symbol: ModelWeaveSettingTab.display; kind: method; Obsidian設定タブで公開される設定項目 |
| src/main.ts | symbol: ModelWeavePlugin.updateSettings; kind: method; 現行設定保存経路 |
| src/settings/model-weave-settings.ts | symbol: ModelWeaveSettings; kind: type; プラグイン設定構造 |
| src/settings/model-weave-settings.ts | symbol: DEFAULT_MODEL_WEAVE_SETTINGS; kind: constant; 既定設定値 |
| src/settings/model-weave-settings.ts | symbol: normalizeModelWeaveSettings; kind: function; 現行設定正規化 |
