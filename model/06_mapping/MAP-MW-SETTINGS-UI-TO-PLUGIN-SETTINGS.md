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
| source | [[SCR-MW-SETTINGS-TAB]] | Obsidian設定タブUI |
| target | [[DATA-MW-PLUGIN-SETTINGS]] | プラグイン設定データ |
| rule | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | 設定値の正規化・フォールバック |
| rule | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | render modeの妥当性 |
| rule | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | Auto Fit設定 |
| rule | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | Viewport状態保持設定 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[SCR-MW-SETTINGS-TAB]].defaultRenderModeSelect | [[DATA-MW-PLUGIN-SETTINGS]].defaultRenderMode | 選択値を保存 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | auto / custom / mermaid 等 |
| [[SCR-MW-SETTINGS-TAB]].mermaidThemeModeSelect | [[DATA-MW-PLUGIN-SETTINGS]].mermaidThemeMode | 選択値を保存 | | N | Obsidianテーマ追従など |
| [[SCR-MW-SETTINGS-TAB]].defaultZoomSelect | [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | 選択値を保存 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | fit / 100% 等 |
| [[SCR-MW-SETTINGS-TAB]].autoFitOnOpenToggle | [[DATA-MW-PLUGIN-SETTINGS]].autoFitOnOpen | checkbox値をbooleanとして保存 | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | N | Setting.addToggleに対応 |
| [[SCR-MW-SETTINGS-TAB]].preserveViewportStateToggle | [[DATA-MW-PLUGIN-SETTINGS]].preserveViewportState | checkbox値をbooleanとして保存 | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | N | Setting.addToggleに対応 |
| [[SCR-MW-SETTINGS-TAB]].viewerFontScaleSelect | [[DATA-MW-PLUGIN-SETTINGS]].viewerFontScale | 選択値を保存 | | N | small / normal / large 等 |
| [[SCR-MW-SETTINGS-TAB]].diagnosticsVisibleToggle | [[DATA-MW-PLUGIN-SETTINGS]].diagnosticsVisible | checkbox値をbooleanとして保存 | | N | 診断パネル表示 |
| [[SCR-MW-SETTINGS-TAB]].statusBarVisibleToggle | [[DATA-MW-PLUGIN-SETTINGS]].statusBarVisible | checkbox値をbooleanとして保存 | | N | ステータスバー表示 |
| [[SCR-MW-SETTINGS-TAB]].enableSourceLinksToggle | [[DATA-MW-PLUGIN-SETTINGS]].enableSourceLinks | checkbox値をbooleanとして保存 | | N | Source Links表示や追跡 |

## Rules

- 各UIフィールドの値は [[DATA-MW-PLUGIN-SETTINGS]] の対応項目へ保存する。
- select項目は許可された値の範囲に正規化してから保存する。
- checkbox項目はboolean値として保存する。
- 不正値または未設定値は [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] に従ってフォールバックする。
- `resetSettingsButton` は個別項目への通常マッピングではなく、`resetToDefaultSettings` 論理プロセスにより [[DATA-MW-PLUGIN-SETTINGS]] 全体を既定値へ戻す。
- 設定変更はObsidianプラグイン設定へ保存され、Markdownモデル本文やfrontmatterは変更しない。

## Notes

- `saveSettings` / `resetToDefaultSettings` は現時点では論理的なプロセス名として扱う。
- 後続で `PROC-MW-SETTINGS-SAVE` / `PROC-MW-SETTINGS-RESET-TO-DEFAULTS` としてapp_process化できる。
- checkboxはUI種別として扱い、実装上はObsidian APIのSetting.addToggleに対応する。
- 本mappingは設定画面UIとプラグイン設定データの対応関係を表す。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/settings-tab.ts | ModelWeaveSettingTab | class | Obsidian設定タブ |
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| src/settings/model-weave-settings.ts | DEFAULT_SETTINGS | constant | 既定設定値 |