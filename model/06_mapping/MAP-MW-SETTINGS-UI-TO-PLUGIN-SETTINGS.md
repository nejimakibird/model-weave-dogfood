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

| target_ref | source_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[DATA-MW-PLUGIN-SETTINGS]].defaultClassRenderMode | [[SCR-MW-SETTINGS-TAB]].defaultClassRenderModeSelect | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。Default Class render mode。custom / mermaid / mermaid-detail |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultErRenderMode | [[SCR-MW-SETTINGS-TAB]].defaultErRenderModeSelect | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。Default ER render mode。custom / mermaid / mermaid-detail |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultDfdRenderMode | [[SCR-MW-SETTINGS-TAB]].defaultDfdRenderModeSelect | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。Default DFD render mode。mermaid |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultProcessRenderMode | [[SCR-MW-SETTINGS-TAB]].defaultProcessRenderModeSelect | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。Default Process render mode。custom |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultScreenRenderMode | [[SCR-MW-SETTINGS-TAB]].defaultScreenRenderModeSelect | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented。Default Screen render mode。custom |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultRenderMode | [[SCR-MW-SETTINGS-TAB]].defaultRenderModeSelect | 旧設定値として保存する場合のみ使用 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | legacy / not current UI。現行Settings Tabには単一defaultRenderMode selectはない。normalizeModelWeaveSettingsの移行補助用 |
| [[DATA-MW-PLUGIN-SETTINGS]].mermaidThemeMode | [[SCR-MW-SETTINGS-TAB]].mermaidThemeModeSelect | 選択値を保存 |  | N | planned。現行ModelWeaveSettingsには未実装 |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | [[SCR-MW-SETTINGS-TAB]].defaultZoomSelect | 選択値を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。fit / 100 |
| [[DATA-MW-PLUGIN-SETTINGS]].autoFitOnOpen | [[SCR-MW-SETTINGS-TAB]].autoFitOnOpenToggle | checkbox値をbooleanとして保存 | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | N | planned。現行ModelWeaveSettingsには未実装 |
| [[DATA-MW-PLUGIN-SETTINGS]].preserveViewportState | [[SCR-MW-SETTINGS-TAB]].preserveViewportStateToggle | checkbox値をbooleanとして保存 | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | N | planned / partial。現行ModelWeaveSettingsには未実装 |
| [[DATA-MW-PLUGIN-SETTINGS]].fontSize | [[SCR-MW-SETTINGS-TAB]].fontSizeSelect | 選択値を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。small / normal / large |
| [[DATA-MW-PLUGIN-SETTINGS]].nodeDensity | [[SCR-MW-SETTINGS-TAB]].nodeDensitySelect | 選択値を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。compact / normal / relaxed |
| [[DATA-MW-PLUGIN-SETTINGS]].enableRelationshipView | [[SCR-MW-SETTINGS-TAB]].enableRelationshipViewToggle | toggle値をbooleanとして保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。Relationship View toggle |
| [[DATA-MW-PLUGIN-SETTINGS]].showMermaidRenderDebug | [[SCR-MW-SETTINGS-TAB]].showMermaidRenderDebugToggle | toggle値をbooleanとして保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。Show Mermaid Render Debug toggle |
| [[DATA-MW-PLUGIN-SETTINGS]].uiLanguage | [[SCR-MW-SETTINGS-TAB]].uiLanguageSelect | 選択値を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。auto / en / ja |
| [[DATA-MW-PLUGIN-SETTINGS]].localSourceRoot | [[SCR-MW-SETTINGS-TAB]].localSourceRootText | 入力文字列を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。relative Source Links outside vault の解決基準 |
| [[DATA-MW-PLUGIN-SETTINGS]].viewerFontScale | [[SCR-MW-SETTINGS-TAB]].viewerFontScaleSelect | 選択値を保存 |  | N | planned。現行ModelWeaveSettingsには未実装。fontSizeとの関係整理が必要 |
| [[DATA-MW-PLUGIN-SETTINGS]].diagnosticsVisible | [[SCR-MW-SETTINGS-TAB]].diagnosticsVisibleToggle | checkbox値をbooleanとして保存 |  | N | planned。現行ModelWeaveSettingsには未実装 |
| [[DATA-MW-PLUGIN-SETTINGS]].statusBarVisible | [[SCR-MW-SETTINGS-TAB]].statusBarVisibleToggle | checkbox値をbooleanとして保存 |  | N | planned。現行ModelWeaveSettingsには未実装 |
| [[DATA-MW-PLUGIN-SETTINGS]].enableSourceLinks | [[SCR-MW-SETTINGS-TAB]].enableSourceLinksToggle | checkbox値をbooleanとして保存 |  | N | future。Source Links Explorer / Source Links索引化がfutureのため現行実装済みではない |

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
