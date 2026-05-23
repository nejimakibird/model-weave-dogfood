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

1. resetSettingsButton のclickイベントを受け取る。
   - このリセット操作は logical / planned process として扱い、現行実装済みUI挙動とは断定しない。
2. DEFAULT_MODEL_WEAVE_SETTINGS を取得する。
   - 現行実装済み設定として defaultRenderMode / defaultZoom / fontSize / nodeDensity を既定値へ戻す対象に含める。
   - fontSize / nodeDensity は現行ModelWeaveSettingsに実装済みだが、Settings Tab UIモデル側の表示項目整理は後続課題を含む。
3. [[DATA-MW-PLUGIN-SETTINGS]] 全体を既定値へ置き換える。
   - autoFitOnOpen / preserveViewportState / mermaidThemeMode / viewerFontScale / diagnosticsVisible / statusBarVisible / settingsVersion はplannedリセット項目として扱う。
   - enableSourceLinks は Source Links Explorer / Source Links索引化がfutureのため、futureリセット項目として扱う。
4. [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] に従い、実効設定を再解決する。
   - 現行実装では src/settings/model-weave-settings.ts の normalizeModelWeaveSettings が defaultRenderMode / defaultZoom / fontSize / nodeDensity を正規化する。
   - dogfood側のみの設定項目については、現行ModelWeaveSettingsに実装済みとは断定しない。
5. Obsidianプラグイン設定として保存する。
   - 現行実装の保存経路は ModelWeavePlugin.updateSettings / saveData を参照する。
   - resetToDefaultSettings という専用処理の実装済みまでは断定しない。
6. [[MAP-MW-SETTINGS-TO-VIEWER-STATE]] に従い、Viewer実行時状態へ反映する。
   - 現行実装済み設定とdogfood先行設計項目の反映範囲は区別して扱う。
7. 必要に応じて設定タブUIとViewerを再描画する。
   - planned項目の表示制御やfuture機能の反映まで現行実装済みとは断定しない。

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

| path | symbol | kind | notes |
|---|---|---|---|
| src/main.ts | ModelWeavePlugin.updateSettings | method | 現行設定保存経路 |
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| src/settings/model-weave-settings.ts | DEFAULT_MODEL_WEAVE_SETTINGS | constant | 既定設定値 |
| src/settings/model-weave-settings.ts | normalizeModelWeaveSettings | function | 現行設定正規化 |
