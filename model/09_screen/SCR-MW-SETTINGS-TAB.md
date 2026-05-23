---
type: screen
id: SCR-MW-SETTINGS-TAB
name: Model Weave設定タブ
screen_type: settings
tags:
  - ModelWeave
  - Settings
  - UI
---

# Model Weave設定タブ

## Summary

Model Weaveプラグインの設定値を表示・変更するObsidian設定タブのUIを定義する。
Renderer、Viewer、Viewport、Diagnostics、Status Bar、Source Linksに関する設定項目を扱う。

## Layout

| id | label | kind | purpose | notes |
|---|---|---|---|---|
| settings_root | 設定タブルート | container | 設定画面全体のコンテナ | |
| renderer_settings_area | Renderer設定領域 | section | 描画モードやMermaidテーマの設定 | |
| viewport_settings_area | Viewport設定領域 | section | ズームやAuto Fit、状態保持の設定 | |
| viewer_ui_settings_area | Viewer UI設定領域 | section | 診断パネル、ステータスバー、フォント表示の設定 | |
| source_link_settings_area | Source Links設定領域 | section | Source Linksの表示・追跡設定 | |
| settings_action_area | 設定操作領域 | section | 保存、リセット等の操作 | |

## Fields

| id | label | kind | layout | data_type | required | ref | rule | notes |
|---|---|---|---|---|---|---|---|---|
| defaultRenderModeSelect | 既定レンダリングモード | select | renderer_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | auto / custom / mermaid 等 |
| mermaidThemeModeSelect | Mermaidテーマモード | select | renderer_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].mermaidThemeMode | | Obsidianテーマ追従など |
| defaultZoomSelect | 既定ズーム | select | viewport_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | fit / 100% 等 |
| autoFitOnOpenToggle | オープン時Auto Fit | checkbox | viewport_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].autoFitOnOpen | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | 初回表示時にfitToViewするか |
| preserveViewportStateToggle | Viewport状態保持 | checkbox | viewport_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].preserveViewportState | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | ファイル単位でzoom/panを保持するか |
| viewerFontScaleSelect | Viewer文字サイズ | select | viewer_ui_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].viewerFontScale | | small / normal / large 等 |
| diagnosticsVisibleToggle | 診断パネル表示 | checkbox | viewer_ui_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].diagnosticsVisible | | 診断パネルの表示制御 |
| statusBarVisibleToggle | ステータスバー表示 | checkbox | viewer_ui_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].statusBarVisible | | ステータスバーの表示制御 |
| enableSourceLinksToggle | Source Links有効化 | checkbox | source_link_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].enableSourceLinks | | Source Links表示や追跡を有効にする |
| resetSettingsButton | 設定リセット | button | settings_action_area | action | N | [[DATA-MW-PLUGIN-SETTINGS]] | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | 既定設定へ戻す |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| changeDefaultRenderMode | 既定レンダリングモード変更 | ui_action | defaultRenderModeSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | 設定変更を保存 |
| changeMermaidThemeMode | Mermaidテーマモード変更 | ui_action | mermaidThemeModeSelect | change | saveSettings | | | 設定変更を保存 |
| changeDefaultZoom | 既定ズーム変更 | ui_action | defaultZoomSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | 設定変更を保存 |
| toggleAutoFitOnOpen | Auto Fit切替 | ui_action | autoFitOnOpenToggle | change | saveSettings | | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | 設定変更を保存 |
| togglePreserveViewportState | Viewport状態保持切替 | ui_action | preserveViewportStateToggle | change | saveSettings | | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | 設定変更を保存 |
| changeViewerFontScale | Viewer文字サイズ変更 | ui_action | viewerFontScaleSelect | change | saveSettings | | | 設定変更を保存 |
| toggleDiagnosticsVisible | 診断パネル表示切替 | ui_action | diagnosticsVisibleToggle | change | saveSettings | | | 設定変更を保存 |
| toggleStatusBarVisible | ステータスバー表示切替 | ui_action | statusBarVisibleToggle | change | saveSettings | | | 設定変更を保存 |
| toggleSourceLinks | Source Links切替 | ui_action | enableSourceLinksToggle | change | saveSettings | | | 設定変更を保存 |
| resetSettings | 設定リセット | ui_action | resetSettingsButton | click | resetToDefaultSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | 既定設定へ戻す |

## Messages

| id | text | severity | timing | notes |
|---|---|---|---|---|
| settingsSaved | Settings saved. | info | on_change | 設定変更保存時 |
| settingsReset | Settings reset to defaults. | info | on_reset | 既定設定へ戻した時 |
| invalidSettingFallback | Invalid setting value was replaced by default. | warning | on_invalid_setting | 不正値フォールバック時 |

## Notes

- このScreenはObsidianプラグイン設定タブの論理UI定義である。
- 設定値の正本は [[DATA-MW-PLUGIN-SETTINGS]] で表す。
- 実効値の解決は [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] に委ねる。
- 設定変更はObsidianプラグイン設定へ保存され、Markdownモデル本文やfrontmatterは変更しない。
- 診断パネルやステータスバーの表示制御はUI表示のみを対象とし、内部診断生成やViewer状態の保持そのものを止めるものではない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/settings-tab.ts | ModelWeaveSettingTab | class | Obsidian設定タブ |
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| src/settings/model-weave-settings.ts | DEFAULT_SETTINGS | constant | 既定設定値 |