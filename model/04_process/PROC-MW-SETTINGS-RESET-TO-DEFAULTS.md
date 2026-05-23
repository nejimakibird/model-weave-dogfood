---
type: app_process
id: PROC-MW-SETTINGS-RESET-TO-DEFAULTS
name: 設定リセット
process_type: ui_action
tags:
  - ModelWeave
  - Settings
  - Process
---

# 設定リセット

## Summary

Model Weave設定タブのリセット操作により、[[DATA-MW-PLUGIN-SETTINGS]] を既定値へ戻し、Obsidianプラグイン設定へ保存する処理。
リセット後はViewer実行時状態を再解決し、必要に応じてViewerを再描画する。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| resetAction | [[SCR-MW-SETTINGS-TAB]].resetSettingsButton | UI | Y | リセットボタン操作 |
| pluginSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | Y | 現在の設定データ |
| resolutionRule | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | Rule | Y | 既定値解決ルール |

## Outputs

| id | data | target | notes |  
|---|---|---|---|  
| resetSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | 既定値に戻されたプラグイン設定 |  
| viewerState | [[DATA-MW-VIEWER-STATE]] | Viewer | Viewer実行時状態 |  
| viewportState | [[DATA-MW-VIEWPORT-STATE]] | Viewer | Viewport表示状態 |

## Steps

| no | description | notes |
|---|---|---|
| 1 | resetSettingsButton のclickイベントを受け取る | リセット操作の開始 |
| 2 | DEFAULT_SETTINGS相当の既定設定を取得する | 実装上の定数参照 |
| 3 | [[DATA-MW-PLUGIN-SETTINGS]] 全体を既定値へ置き換える | データリセット |
| 4 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] に従い、実効設定を再解決する | フォールバック処理 |
| 5 | Obsidianプラグイン設定として保存する | 既定値の永続化 |
| 6 | [[MAP-MW-SETTINGS-TO-VIEWER-STATE]] に従い、Viewer実行時状態へ反映する | 実行時解決 |
| 7 | 必要に応じて設定タブUIとViewerを再描画する | 全画面の最新化 |

## Messages

| id | text | severity | notes |
|---|---|---|---|
| settingsReset | [[SCR-MW-SETTINGS-TAB]].settingsReset | info | 設定リセット完了 |
| settingsFallbackApplied | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]].settings fallback applied | info | 既定値適用メッセージ |

## Notes

- 個別の1対1マッピングではなく、設定全体を既定値へ戻す処理である。
- Markdownモデル本文やfrontmatterは変更しない。
- 実際の既定値は DEFAULT_SETTINGS または同等の実装定数に委ねる。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/settings-tab.ts | resetToDefaultSettings | function | 設定リセット処理 |
| src/settings/model-weave-settings.ts | DEFAULT_SETTINGS | constant | 既定設定値 |
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |