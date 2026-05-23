---
type: screen
id: SCR-MW-SETTINGS-TAB
name: Model Weave設定タブ
screen_type: settings
kind: settings_screen
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
| defaultRenderModeSelect | 既定レンダリングモード | select | renderer_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented寄り。現行ModelWeaveSettingsに実装済み。auto / custom / mermaid 等 |
| mermaidThemeModeSelect | Mermaidテーマモード | select | renderer_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].mermaidThemeMode | | planned。現行ModelWeaveSettingsには未実装 |
| defaultZoomSelect | 既定ズーム | select | viewport_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | implemented寄り。現行ModelWeaveSettingsに実装済み。fit / 100 等 |
| autoFitOnOpenToggle | オープン時Auto Fit | checkbox | viewport_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].autoFitOnOpen | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | planned。Auto Fit設定UIの先行設計。現行ModelWeaveSettingsには未実装 |
| preserveViewportStateToggle | Viewport状態保持 | checkbox | viewport_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].preserveViewportState | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | planned / partial。viewport保持UIの先行設計。現行ModelWeaveSettingsには未実装 |
| viewerFontScaleSelect | Viewer文字サイズ | select | viewer_ui_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].viewerFontScale | | planned。現行ModelWeaveSettingsには未実装。fontSizeとの関係整理が必要 |
| diagnosticsVisibleToggle | 診断パネル表示 | checkbox | viewer_ui_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].diagnosticsVisible | | planned。診断パネル表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| statusBarVisibleToggle | ステータスバー表示 | checkbox | viewer_ui_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].statusBarVisible | | planned。ステータスバー表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| enableSourceLinksToggle | Source Links有効化 | checkbox | source_link_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].enableSourceLinks | | future。Source Links Explorer / Source Links索引化がfutureのため現行実装済みではない |
| resetSettingsButton | 設定リセット | button | settings_action_area | action | N | [[DATA-MW-PLUGIN-SETTINGS]] | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | planned process。既定設定へ戻す論理操作 |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| changeDefaultRenderMode | 既定レンダリングモード変更 | ui_action | defaultRenderModeSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | logical process。現行実装済み設定を保存する想定 |
| changeMermaidThemeMode | Mermaidテーマモード変更 | ui_action | mermaidThemeModeSelect | change | saveSettings | | | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| changeDefaultZoom | 既定ズーム変更 | ui_action | defaultZoomSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | logical process。現行実装済み設定を保存する想定 |
| toggleAutoFitOnOpen | Auto Fit切替 | ui_action | autoFitOnOpenToggle | change | saveSettings | | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| togglePreserveViewportState | Viewport状態保持切替 | ui_action | preserveViewportStateToggle | change | saveSettings | | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| changeViewerFontScale | Viewer文字サイズ変更 | ui_action | viewerFontScaleSelect | change | saveSettings | | | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| toggleDiagnosticsVisible | 診断パネル表示切替 | ui_action | diagnosticsVisibleToggle | change | saveSettings | | | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| toggleStatusBarVisible | ステータスバー表示切替 | ui_action | statusBarVisibleToggle | change | saveSettings | | | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| toggleSourceLinks | Source Links切替 | ui_action | enableSourceLinksToggle | change | saveSettings | | | future process。Source Links関連機能はfuture |
| resetSettings | 設定リセット | ui_action | resetSettingsButton | click | resetToDefaultSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | logical / planned process。既定設定へ戻す |

## Messages

| id | text | severity | timing | notes |
|---|---|---|---|---|
| settingsSaved | Settings saved. | info | on_change | 設定変更保存時 |
| settingsReset | Settings reset to defaults. | info | on_reset | 既定設定へ戻した時 |
| invalidSettingFallback | Invalid setting value was replaced by default. | warning | on_invalid_setting | 不正値フォールバック時 |

## Notes

- このScreenはObsidianプラグイン設定タブの論理UI定義である。
- 設定値の正本は [[DATA-MW-PLUGIN-SETTINGS]] で表す。
- 現行実装側の設定項目は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- この screen は現行実装済み設定とdogfood先行設計項目を含むため、全体としては partial / planned と扱う。
- defaultRenderMode / defaultZoom は現行ModelWeaveSettingsに実装済みの設定UI項目として扱う。
- autoFitOnOpen / preserveViewportState / mermaidThemeMode / viewerFontScale / diagnosticsVisible / statusBarVisible はdogfood先行設計項目であり、現行実装済みとは断定しない。
- enableSourceLinks は Source Links Explorer / Source Links索引化が future のため、現行実装済みとは扱わない。
- fontSize / nodeDensity は現行実装済み設定だが、Settings Tab UIモデル側の表示項目整理は後続課題とする。
- `saveSettings` / `resetToDefaultSettings` は現時点では logical process / planned process として扱い、app_process実装済みとは断定しない。
- 実効値の解決は [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] に委ねる。
- 設定変更はObsidianプラグイン設定へ保存され、Markdownモデル本文やfrontmatterは変更しない。
- 診断パネルやステータスバーの表示制御はUI表示のみを対象とし、内部診断生成やViewer状態の保持そのものを止めるものではない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/main.ts | ModelWeaveSettingTab | class | Obsidian設定タブ |
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| src/settings/model-weave-settings.ts | DEFAULT_MODEL_WEAVE_SETTINGS | constant | 既定設定値 |
