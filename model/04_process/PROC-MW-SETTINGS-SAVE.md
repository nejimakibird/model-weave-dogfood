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

ModelWeavePlugin.updateSettings が受け取った部分設定を現在の設定へ反映し、normalizeModelWeaveSettings で正規化して Obsidian プラグイン設定へ保存する処理。
保存後は options.refreshViews に応じて、開いている Model Weave preview view へ設定を反映する。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Settings保存とViewer反映に関わる実装領域 |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| partialSettings | [[DATA-MW-PLUGIN-SETTINGS]] | [[SCR-MW-SETTINGS-TAB]] | Y | updateSettings に渡される部分設定 |
| currentSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Plugin | Y | this.settings の現在値 |
| saveOptions | options | Plugin | N | refreshViews の制御 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| updatedSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Plugin data | 正規化済み ModelWeaveSettings |
| refreshedViews | Model Weave preview views | Viewer | refreshViews が false でない場合に反映 |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | settings_configuration | 設定保存開始 | start | partialSettings |  |  |  |  | updateSettings が呼び出される |
| receivePartial | settings_configuration | 部分設定を受け取る | input | partialSettings | partialSettings |  |  | [[SCR-MW-SETTINGS-TAB]] | Settings Tab から呼び出される |
| mergeSettings | settings_configuration | 現在設定へ反映する | process | currentSettings | mergedSettings |  |  |  | this.settings と partial をマージする |
| normalizeSettings | settings_configuration | 設定を正規化する | process | mergedSettings | updatedSettings | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] |  |  | normalizeModelWeaveSettings を使う |
| saveSettings | settings_configuration | 設定を保存する | process | updatedSettings | updatedSettings |  |  |  | saveData に渡す |
| shouldRefresh | settings_configuration | View更新要否を判定する | decision | saveOptions |  |  |  |  | refreshViews false でなければ更新する |
| skipRefresh | settings_configuration | View更新を省略する | end | updatedSettings |  |  |  |  | refreshViews false の場合 |
| getOpenViews | viewer_ui | 開いているViewを取得する | process | updatedSettings | openViews |  |  |  | getLeavesOfType を使う |
| applyPreferences | viewer_ui | Viewer設定を反映する | process | openViews | refreshedViews |  |  |  | applyViewerSettings を呼ぶ |
| canRerenderFile | viewer_ui | 表示ファイルを再描画できるか判定する | decision | openViews |  |  |  |  | currentFilePath と TFile を確認する |
| rerenderFile | viewer_ui | 対象ファイルを再描画する | process | openViews | refreshedViews |  |  |  | showPreviewForFile を rerender で呼ぶ |
| refreshView | viewer_ui | Viewのみ更新する | process | openViews | refreshedViews |  |  |  | refreshForSettingsChange を呼ぶ |
| endSaved | settings_configuration | 設定保存完了 | end | updatedSettings |  |  |  |  | 保存結果を保持する |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| shouldRefresh | skipRefresh | `options?.refreshViews === false` | refresh skip | View更新せず終了する |
| shouldRefresh | getOpenViews | `options?.refreshViews !== false` | refresh | 開いているViewへ反映する |
| skipRefresh | endSaved |  | saved | 保存のみで終了する |
| canRerenderFile | rerenderFile | `target instanceof TFile` | rerender | 表示中ファイルを再描画する |
| canRerenderFile | refreshView |  | fallback refresh | View設定のみ更新する |
| rerenderFile | endSaved |  | refreshed | 再描画後に終了する |
| refreshView | endSaved |  | refreshed | View更新後に終了する |

## Messages

| id | text | severity | notes |
|---|---|---|---|
| settingsSaved | [[SCR-MW-SETTINGS-TAB]].settingsSaved | info | 設定保存完了 |
| invalidSettingFallback | [[SCR-MW-SETTINGS-TAB]].invalidSettingFallback | warning | 正規化によるfallback |

## Notes

- Markdownモデル本文やfrontmatterは変更しない。
- 設定の正本はObsidianプラグイン設定であり、[[DATA-MW-PLUGIN-SETTINGS]] は論理モデルである。
- 現行実装側の設定項目は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- updateSettings は partial を this.settings に重ねた後、normalizeModelWeaveSettings に渡す。
- normalizeModelWeaveSettings は defaultClassRenderMode / defaultErRenderMode / defaultDfdRenderMode / defaultProcessRenderMode / defaultScreenRenderMode / defaultZoom / fontSize / nodeDensity / localSourceRoot / enableRelationshipView / showMermaidRenderDebug / uiLanguage を正規化する。
- defaultRenderMode は legacy fallback として読み取られるが、現在の主設定ではない。
- options.refreshViews が false の場合、保存後のView更新は行わない。
- refreshOpenModelWeaveViews は開いている Model Weave preview view に viewer preferences を適用する。
- 表示中ファイルが TFile として取得できる場合は showPreviewForFile で再描画し、それ以外は refreshForSettingsChange を呼ぶ。
- `Steps.domain` は [[DOMAINS-MW-ARCHITECTURE]] のDomain idを参照する。Flow Connect Modeの対象ではなく、設定保存後のViewer更新フローである。

## Source Links

| path | notes |
|---|---|
| src/main.ts | symbol: ModelWeavePlugin.updateSettings; kind: method; 現行設定保存処理 |
| src/main.ts | symbol: ModelWeavePlugin.refreshOpenModelWeaveViews; kind: method; 開いているPreview Viewへの反映 |
| src/main.ts | symbol: saveData; kind: method call; 正規化済み設定の保存 |
| src/settings/model-weave-settings.ts | symbol: ModelWeaveSettings; kind: type; プラグイン設定構造 |
| src/settings/model-weave-settings.ts | symbol: DEFAULT_MODEL_WEAVE_SETTINGS; kind: constant; 既定設定値 |
| src/settings/model-weave-settings.ts | symbol: normalizeModelWeaveSettings; kind: function; 現行設定正規化 |
