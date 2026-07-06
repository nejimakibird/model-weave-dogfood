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
| resetAction | [[SCR-MW-SETTINGS-TAB]].resetSettingsButton | UI | Y | リセットボタン操作。Settings Tab UI全体はpartial / plannedとして扱う |
| pluginSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | Y | 現在の設定データ。現行実装済み設定とdogfood先行設計項目を含む |
| defaultSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | Y | 現行実装済み設定の既定値は src/settings/model-weave-settings.ts の DEFAULT_MODEL_WEAVE_SETTINGS を正とする |
| resolutionRule | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | Rule | Y | 既定値解決ルール。現行実装側の正はModelWeaveSettings |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| resetSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | 既定値に戻されたプラグイン設定。現行実装の正は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS |
| viewerState | [[DATA-MW-VIEWER-STATE]] | Viewer | Viewer実行時状態。設定反映は現行実装とdogfood設計が混在する |
| viewportState | [[DATA-MW-VIEWPORT-STATE]] | Viewer | Viewport表示状態 |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Settings | 開始 | start | resetAction | | | | [[SCR-MW-SETTINGS-TAB]] | logical / planned processとして扱う |
| receiveClick | Settings | リセット操作を受け取る | screen | resetAction | | | | [[SCR-MW-SETTINGS-TAB]] | 現行実装済みUI挙動とは断定しない |
| loadDefaults | Settings | 既定値取得 | data | defaultSettings | resetSettings | | | | DEFAULT_MODEL_WEAVE_SETTINGSを参照する |
| replaceSettings | Settings | 設定全体を置き換える | process | resetSettings | resetSettings | | | | dogfood先行設計項目はimplemented扱いにしない |
| resolveEffective | Viewer | 実効設定を再解決 | process | resetSettings | viewerState | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | | | normalizeModelWeaveSettings相当の正規化を含む |
| saveSettings | Settings | プラグイン設定として保存 | process | resetSettings | resetSettings | | | | updateSettings / saveData が保存経路の参考 |
| mapViewerState | Viewer | Viewer状態へ反映 | process | resetSettings | viewerState | | | | [[MAP-MW-SETTINGS-TO-VIEWER-STATE]] に従う |
| refreshUi | Viewer | 設定タブとViewerを更新 | screen | viewerState | viewportState | | | [[SCR-MW-SETTINGS-TAB]] | 必要に応じてViewerを再描画する |
| end | Settings | 終了 | end | | | | | | 処理終了 |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | receiveClick | | 操作受付 | |
| receiveClick | loadDefaults | | 既定値取得 | |
| loadDefaults | replaceSettings | | 置き換え | |
| replaceSettings | resolveEffective | | 解決 | |
| resolveEffective | saveSettings | | 保存 | |
| saveSettings | mapViewerState | | Viewer反映 | |
| mapViewerState | refreshUi | | 画面更新 | |
| refreshUi | end | | 完了 | |

## Messages

| id | text | severity | notes |
|---|---|---|---|
| settingsReset | [[SCR-MW-SETTINGS-TAB]].settingsReset | info | 設定リセット完了。現行実装済み設定とplannedリセット項目を含む論理メッセージ |
| settingsFallbackApplied | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]].settings fallback applied | info | 既定値適用メッセージ。現行実装に存在しないマイグレーション処理までは断定しない |

## Notes

- 個別の1対1マッピングではなく、設定全体を既定値へ戻す処理である。
- Markdownモデル本文やfrontmatterは変更しない。
- 現行実装側の設定項目は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- この app_process は現行実装済み設定とdogfood先行設計項目を含むため、全体としては partial / planned と扱う。
- defaultRenderMode / defaultZoom / fontSize / nodeDensity は現行実装済み設定であり、DEFAULT_MODEL_WEAVE_SETTINGS へ戻す対象として扱う。
- autoFitOnOpen / preserveViewportState / mermaidThemeMode / viewerFontScale / diagnosticsVisible / statusBarVisible / settingsVersion はdogfood先行設計項目であり、現行ModelWeaveSettingsには未実装である。
- enableSourceLinks は Source Links Explorer / Source Links索引化がfutureのため、現行実装済みとは扱わない。
- dogfood側のみの設定項目は、現時点でModel Weave本体に実装済みとは断定しない。
- resetToDefaultSettings は現時点では logical / planned process として扱い、専用app_process実装済みとは断定しない。

## Source Links

| path | notes |
|---|---|
| src/main.ts | symbol: ModelWeavePlugin.updateSettings; kind: method; 現行設定保存経路 |
| src/settings/model-weave-settings.ts | symbol: ModelWeaveSettings; kind: type; プラグイン設定構造 |
| src/settings/model-weave-settings.ts | symbol: DEFAULT_MODEL_WEAVE_SETTINGS; kind: constant; 既定設定値 |
| src/settings/model-weave-settings.ts | symbol: normalizeModelWeaveSettings; kind: function; 現行設定正規化 |
