---
type: app_process
id: PROC-MW-SETTINGS-SAVE
name: 設定保存
process_type: ui_action
tags:
  - ModelWeave
  - Settings
  - Process
---

# 設定保存

## Summary

Model Weave設定タブで変更されたUI値を、[[DATA-MW-PLUGIN-SETTINGS]] として正規化し、Obsidianプラグイン設定へ保存する処理。
設定保存後、Viewer実行時状態へ反映するため [[MAP-MW-SETTINGS-TO-VIEWER-STATE]] を通じて再解決される。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| settingsUI | [[SCR-MW-SETTINGS-TAB]] | UI | Y | 設定タブ上のフィールド値 |
| pluginSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | Y | 保存対象の論理設定データ |
| uiMapping | [[MAP-MW-SETTINGS-UI-TO-PLUGIN-SETTINGS]] | Mapping | Y | UIからデータへの反映定義 |
| resolutionRule | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | Rule | Y | 設定値の正規化・補正ルール |

## Outputs

| id | data | target | notes |  
|---|---|---|---|  
| updatedSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | Obsidianプラグイン設定 |  
| viewerState | [[DATA-MW-VIEWER-STATE]] | Viewer | Viewer実行時状態 |  
| viewportState | [[DATA-MW-VIEWPORT-STATE]] | Viewer | Viewport表示状態 |

## Steps

| no | description | notes |
|---|---|---|
| 1 | 設定タブ上の変更対象フィールドを受け取る | changeイベントの発生 |
| 2 | [[MAP-MW-SETTINGS-UI-TO-PLUGIN-SETTINGS]] に従い、UI値を [[DATA-MW-PLUGIN-SETTINGS]] の対応項目へ反映する | データの同期 |
| 3 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] に従い、不正値や未設定値を補正する | 安全なフォールバック |
| 4 | Obsidianプラグイン設定（data.json等）として保存する | 設定の永続化 |
| 5 | [[MAP-MW-SETTINGS-TO-VIEWER-STATE]] に従い、Viewer実行時状態へ反映する | 実行時設定の再解決 |
| 6 | 必要に応じてViewerを再描画する | 変更内容の即時反映 |

## Messages

| id | text | severity | notes |
|---|---|---|---|
| settingsSaved | [[SCR-MW-SETTINGS-TAB]].settingsSaved | info | 設定保存完了 |
| invalidSettingFallback | [[SCR-MW-SETTINGS-TAB]].invalidSettingFallback | warning | 不正値の自動補正発生 |

## Notes

- Markdownモデル本文やfrontmatterは変更しない。
- 設定の正本はObsidianプラグイン設定であり、[[DATA-MW-PLUGIN-SETTINGS]] は論理モデルである。
- checkboxは実装上 Setting.addToggle に対応する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/settings-tab.ts | saveSettings | function | 設定保存処理 |
| source/model-weave-repo/src/settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| source/model-weave-repo/src/settings.ts | DEFAULT_SETTINGS | constant | 既定設定値 |