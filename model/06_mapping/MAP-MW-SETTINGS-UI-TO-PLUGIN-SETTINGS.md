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
| [[SCR-MW-SETTINGS-TAB]].defaultRenderModeSelect | [[DATA-MW-PLUGIN-SETTINGS]].defaultRenderMode | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | implemented寄り。現行ModelWeaveSettingsに実装済み。auto / custom / mermaid 等 |
| [[SCR-MW-SETTINGS-TAB]].mermaidThemeModeSelect | [[DATA-MW-PLUGIN-SETTINGS]].mermaidThemeMode | 選択値を保存 | | N | planned。現行ModelWeaveSettingsには未実装 |
| [[SCR-MW-SETTINGS-TAB]].defaultZoomSelect | [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | 選択値を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented寄り。現行ModelWeaveSettingsに実装済み。fit / 100 等 |
| [[SCR-MW-SETTINGS-TAB]].autoFitOnOpenToggle | [[DATA-MW-PLUGIN-SETTINGS]].autoFitOnOpen | checkbox値をbooleanとして保存 | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | N | planned。現行ModelWeaveSettingsには未実装 |
| [[SCR-MW-SETTINGS-TAB]].preserveViewportStateToggle | [[DATA-MW-PLUGIN-SETTINGS]].preserveViewportState | checkbox値をbooleanとして保存 | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | N | planned / partial。現行ModelWeaveSettingsには未実装 |
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
- `resetSettingsButton` は個別項目への通常マッピングではなく、`resetToDefaultSettings` 論理プロセスにより [[DATA-MW-PLUGIN-SETTINGS]] 全体を既定値へ戻す。
- 設定変更はObsidianプラグイン設定へ保存され、Markdownモデル本文やfrontmatterは変更しない。

## Notes

- 現行実装側の設定項目は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- この mapping は現行実装済み設定とdogfood先行設計項目を含むため、全体として partial / planned と扱う。
- defaultRenderMode / defaultZoom は現行ModelWeaveSettingsに実装済みのUI→設定マッピングとして扱う。
- fontSize / nodeDensity は現行実装済み設定だが、SCR-MW-SETTINGS-TAB 側に対応Fieldがないため、Mapping追加は行わず後続課題とする。
- autoFitOnOpen / preserveViewportState / mermaidThemeMode / viewerFontScale / diagnosticsVisible / statusBarVisible / settingsVersion はdogfood先行設計項目であり、現行ModelWeaveSettingsには未実装である。
- enableSourceLinks は Source Links Explorer / Source Links索引化がfutureのため、現行実装済みとは扱わない。
- dogfood側のみの設定項目は、現時点でModel Weave本体に実装済みとは断定しない。
- `saveSettings` / `resetToDefaultSettings` は [[PROC-MW-SETTINGS-SAVE]] / [[PROC-MW-SETTINGS-RESET-TO-DEFAULTS]] と同様に logical / planned process として扱う。
- checkboxはUI種別として扱い、planned項目を含む場合は実装済みSetting.addToggleとは断定しない。
- 本mappingは設定画面UIとプラグイン設定データの対応関係を表す。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/main.ts | ModelWeaveSettingTab | class | Obsidian設定タブ |
| src/main.ts | ModelWeavePlugin.updateSettings | method | 現行設定保存経路 |
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| src/settings/model-weave-settings.ts | DEFAULT_MODEL_WEAVE_SETTINGS | constant | 既定設定値 |
| src/settings/model-weave-settings.ts | normalizeModelWeaveSettings | function | 現行設定正規化 |
