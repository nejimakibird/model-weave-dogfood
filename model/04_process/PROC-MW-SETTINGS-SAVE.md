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
| settingsUI | [[SCR-MW-SETTINGS-TAB]] | UI | Y | 設定タブ上のフィールド値。現行実装済み設定とplanned UI項目が混在する |
| pluginSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | Y | 保存対象の論理設定データ。現行実装済み設定とdogfood先行設計項目を含む |
| uiMapping | [[MAP-MW-SETTINGS-UI-TO-PLUGIN-SETTINGS]] | Mapping | Y | UIからデータへの反映定義。未実装設定UIはplanned / futureとして扱う |
| resolutionRule | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | Rule | Y | 設定値の正規化・補正ルール。現行実装側の正はModelWeaveSettings |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| updatedSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | Obsidianプラグイン設定。現行実装の正は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS |
| viewerState | [[DATA-MW-VIEWER-STATE]] | Viewer | Viewer実行時状態。設定反映は現行実装とdogfood設計が混在する |
| viewportState | [[DATA-MW-VIEWPORT-STATE]] | Viewer | Viewport表示状態 |

## Steps

1. 設定タブ上の変更対象フィールドを受け取る。
   - 現行実装済み設定として defaultRenderMode / defaultZoom / fontSize / nodeDensity を保存対象に含める。
   - fontSize / nodeDensity は現行ModelWeaveSettingsに実装済みだが、Settings Tab UIモデル側の表示項目整理は後続課題を含む。
2. [[MAP-MW-SETTINGS-UI-TO-PLUGIN-SETTINGS]] に従い、UI値を [[DATA-MW-PLUGIN-SETTINGS]] の対応項目へ反映する。
   - autoFitOnOpen / preserveViewportState / mermaidThemeMode / viewerFontScale / diagnosticsVisible / statusBarVisible / settingsVersion はplanned保存項目として扱う。
   - enableSourceLinks は Source Links Explorer / Source Links索引化がfutureのため、future保存項目として扱う。
3. [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] に従い、不正値や未設定値を補正する。
   - 現行実装では src/settings/model-weave-settings.ts の normalizeModelWeaveSettings が defaultRenderMode / defaultZoom / fontSize / nodeDensity を正規化する。
   - dogfood側のみの設定項目については、現行ModelWeaveSettingsに実装済みとは断定しない。
4. Obsidianプラグイン設定として保存する。
   - 現行実装では ModelWeavePlugin.updateSettings が正規化後の設定を saveData に渡す。
5. [[MAP-MW-SETTINGS-TO-VIEWER-STATE]] に従い、Viewer実行時状態へ反映する。
   - 現行実装済み設定とdogfood先行設計項目の反映範囲は区別して扱う。
6. 必要に応じてViewerを再描画する。
   - 設定保存後の即時反映は現行実装に存在するが、planned項目の表示制御まで実装済みとは断定しない。

## Messages

| id | text | severity | notes |
|---|---|---|---|
| settingsSaved | [[SCR-MW-SETTINGS-TAB]].settingsSaved | info | 設定保存完了。現行実装済み設定とplanned保存項目を含む論理メッセージ |
| invalidSettingFallback | [[SCR-MW-SETTINGS-TAB]].invalidSettingFallback | warning | 不正値の自動補正発生。現行実装に存在しない移行処理までは断定しない |

## Notes

- Markdownモデル本文やfrontmatterは変更しない。
- 設定の正本はObsidianプラグイン設定であり、[[DATA-MW-PLUGIN-SETTINGS]] は論理モデルである。
- 現行実装側の設定項目は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- この app_process は現行実装済み設定とdogfood先行設計項目を含むため、全体としては partial / planned と扱う。
- defaultRenderMode / defaultZoom / fontSize / nodeDensity は現行実装済み設定であり、保存対象として扱う。
- autoFitOnOpen / preserveViewportState / mermaidThemeMode / viewerFontScale / diagnosticsVisible / statusBarVisible / settingsVersion はdogfood先行設計項目であり、現行ModelWeaveSettingsには未実装である。
- enableSourceLinks は Source Links Explorer / Source Links索引化がfutureのため、現行実装済みとは扱わない。
- dogfood側のみの設定項目は、現時点でModel Weave本体に実装済みとは断定しない。
- checkboxはUI種別として扱い、planned項目を含む場合は実装済みSetting.addToggleとは断定しない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/main.ts | ModelWeavePlugin.updateSettings | method | 現行設定保存処理 |
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| src/settings/model-weave-settings.ts | DEFAULT_MODEL_WEAVE_SETTINGS | constant | 既定設定値 |
| src/settings/model-weave-settings.ts | normalizeModelWeaveSettings | function | 現行設定正規化 |
