---
type: rule
id: RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE
name: Viewer Viewport状態保持ルール
kind: state_rule
tags:
  - Viewer
  - Viewport
  - State
  - Rule
---

# Viewer Viewport状態保持ルール

## Summary

Viewerがファイル単位でViewport状態を保持・復元・破棄する条件を定義する。
Viewport状態はViewer上の一時状態であり、Markdown本文やfrontmatterには保存しない。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| viewerState | [[DATA-MW-VIEWER-STATE]] | Viewer | Y | active fileやrenderStatusを含むViewer状態 |
| viewportState | [[DATA-MW-VIEWPORT-STATE]] | Viewer | Y | scale / panX / panY を含む表示状態 |
| autoFitRule | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | Rule | Y | 初回表示や切替時のfit方針 |
| transformRule | [[RULE-MW-VIEWPORT-TRANSFORM-CALCULATION]] | Rule | N | scale/panの計算方針 |
| zoomLimitRule | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | Rule | N | scaleの境界条件 |

## Conditions

- Viewerは対象ファイルごとにViewport状態を保持してよい。
- ファイルを切り替えた場合、切替先ファイルにキャッシュ済みviewportStateがあれば復元してよい。
- 切替先ファイルにキャッシュがない場合、`[[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]]` に従い fitToView を実行する。
- ユーザーがpan/zoom操作を行った場合、そのviewportStateをキャッシュに保存する。
- 同じファイルの再描画では、ユーザーが手動操作済みなら現在のviewportStateを維持する。
- レンダラー切替時は描画サイズが変わる可能性があるため、キャッシュを無効化するかfitToViewを再実行してよい。
- ファイル内容が大きく変わった場合、既存キャッシュを破棄してfitToViewを再実行してよい。
- 非対応ファイルやエラー状態ではviewportStateを保存しない。
- Viewport状態キャッシュはViewerの一時状態であり、Markdown本文やfrontmatterへ保存しない。

## Parameters

| name | value | notes |
|---|---|---|
| cacheScope | filePath | ファイル単位でViewport状態を保持 |
| restoreOnFileReturn | true | 前に開いたファイルへ戻った時に状態復元 |
| persistToMarkdown | false | Markdownモデルには保存しない |
| resetOnRendererChange | true | レンダラー切替時は再fitを許容 |
| skipCacheOnError | true | エラー状態ではキャッシュ保存しない |
| cacheLimit | 50 | 保持するViewport状態キャッシュの最大件数 |

## Messages

| condition | message | severity | notes |
|---|---|---|---|
| viewport state restored | Restored previous viewport state. | info | キャッシュ済み状態を復元 |
| viewport state reset | Viewport state reset for new render. | info | 再fitまたは初期化 |
| viewport cache skipped | Viewport cache skipped due to render error. | info | エラー時は保存しない |

## Notes

- 本ルールはViewer内の一時的な状態保持方針を定義する。
- 永続的なモデル情報ではないため、Markdownファイルには書き込まない。
- Auto Fitの実行条件は `[[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]]` に従う。
- scale/panの具体計算は `[[RULE-MW-VIEWPORT-TRANSFORM-CALCULATION]]` に従う。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | symbol: prepareFileViewportState; kind: method; ファイル単位のviewport状態準備 |
| src/views/modeling-preview-view.ts | symbol: resetGraphViewportState; kind: method; viewport状態の初期化 |
| src/views/modeling-preview-view.ts | symbol: renderCurrentState; kind: method; active file / render state に応じた再描画 |
| src/views/modeling-preview-view.ts | symbol: rememberViewportState; kind: method; ユーザー操作やfit後のviewport状態をキャッシュへ保存 |
| src/views/modeling-preview-view.ts | symbol: pruneViewportStateCache; kind: method; 古いviewport状態キャッシュを破棄 |
| src/views/modeling-preview-view.ts | symbol: VIEWPORT_STATE_CACHE_LIMIT; kind: constant; Viewport状態キャッシュの最大保持件数 |
| src/renderers/graph-view-shared.ts | symbol: attachGraphViewportInteractions; kind: function; ユーザー操作によるviewport状態更新 |
