---
type: data_object
id: DATA-MW-VIEWER-STATE
name: Viewer内部状態
kind: state
tags:
  - Viewer
  - Runtime
  - State
---

# Viewer内部状態

## Summary
Viewer の実行時における現在の表示内容、レンダリング状態、Viewer表示設定を保持する内部データオブジェクト。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| currentModelName | 現在モデル名 | string | | N | currentModel.name | | partial。PreviewState の model / title / filePath から表示名として導出される概念項目 |
| supportedRenderModes | サポート描画モード | string | | N | rendererSelection.supportedModes | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | implemented / derived。RendererSelectionState.supportedModes。getSupportedRenderModes 由来の論理的な複数値 |
| effectiveRenderMode | 実効描画モード | string | | N | rendererSelection.effectiveMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。src/core/render-mode.ts の resolveRenderMode により決定される実効描画モード |
| previewContent | プレビュー内容 | object | | N | state.model | | partial。PreviewState の mode ごとの model / diagram / context / summary情報をUIマッピング上で集約する概念項目 |
| diagnostics | 診断情報一覧 | DATA-MW-CORE-DIAGNOSTIC | | N | state.warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | implemented。PreviewState.warnings と renderDiagnostics で表示される診断情報 |
| renderStatus | 描画状態 | string | | Y | state.mode | | partial。PreviewState.mode に近い描画状態の概念項目。実装のmode名と完全一致とは断定しない |
| renderMessage | 描画状態メッセージ | string | | N | state.message | | implemented / partial。empty / error 等のPreviewState.messageに対応する状態メッセージ |
| activeFilePath | アクティブファイルパス | string | | N | getCurrentFilePath | | implemented。ModelingPreviewView.getCurrentFilePath で表示対象ファイルを取得 |
| autoFitEnabled | Auto Fit有効 | boolean | | N | autoFitEnabled | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | planned / partial。auto fit挙動は実装済みだが、現行ModelWeaveSettingsの設定項目としては未実装 |
| preserveViewportState | Viewport状態保持 | boolean | | N | preserveViewportState | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | planned / partial。viewportStateCache による保持挙動はあるが、現行ModelWeaveSettingsの設定項目としては未実装 |
| diagnosticsVisible | 診断パネル表示 | boolean | | N | diagnosticsVisible | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | planned。診断パネル表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| statusBarVisible | ステータスバー表示 | boolean | | N | statusBarVisible | [[SCR-MW-VIEWER-STATUS-BAR]] | planned。ステータスバー表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| fontSize | Viewer文字サイズ | string | | N | viewerPreferences.fontSize | | implemented。src/settings/model-weave-settings.ts に実装済み。ModelingPreviewView.viewerPreferences からCSS class / CSS variablesへ反映 |
| nodeDensity | ノード密度 | string | | N | viewerPreferences.nodeDensity | | implemented。src/settings/model-weave-settings.ts に実装済み。ModelingPreviewView.viewerPreferences から表示密度のCSS class / spacingへ反映 |
| viewerFontScale | Viewer文字サイズ倍率 | string | | N | viewerFontScale | | planned。dogfood先行項目。現行ModelWeaveSettingsには未実装。fontSize との関係整理が後続課題 |
| mermaidThemeMode | Mermaidテーマモード | string | | N | mermaidThemeMode | | planned。Mermaidテーマ制御の将来設定。現行ModelWeaveSettingsには未実装 |
| sourceLinksEnabled | Source Links有効 | boolean | | N | sourceLinksEnabled | | future。Source Links Explorer / Source Links索引化がfutureのため現行実装済みではない |

## Notes
- 現行実装側のViewer状態は src/views/modeling-preview-view.ts の PreviewState / ModelingPreviewView を正とする。
- この data_object は現行実装済みViewer状態とdogfood先行設計項目を含むため、全体としては partial と扱う。
- dogfood側のみの項目は、現時点でModel Weave本体に実装済みとは断定しない。
- GraphModel や MermaidSource などの具体的な描画データは、UIマッピング上は `previewContent` に集約して扱う。
- `effectiveRenderMode` は src/core/render-mode.ts の resolveRenderMode と [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] / [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に従う。
- `defaultZoom` 由来の表示状態は [[DATA-MW-VIEWPORT-STATE]] の zoom / scale / fit 状態として扱う。
- fontSize / nodeDensity は現行実装済み設定であり、ModelingPreviewView.viewerPreferences からViewerのCSS class / CSS variablesへ反映される。
- viewerFontScale はdogfood先行項目であり、fontSize との関係整理は後続課題とする。
- diagnosticsVisible / statusBarVisible はplannedの表示制御であり、内部状態や診断生成そのものを停止するものではない。
- sourceLinksEnabled は Source Links Explorer / Source Links索引化が future のため、現行実装済みとは扱わない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/views/modeling-preview-view.ts | PreviewState | type | Viewer内部状態 |
| src/views/modeling-preview-view.ts | RendererSelectionState | interface | supportedRenderModes / effectiveRenderMode の実装側状態 |
| src/views/modeling-preview-view.ts | ModelingPreviewView | class | stateとviewerPreferencesを保持しUIへ反映するViewer |
| src/views/modeling-preview-view.ts | applyViewerSettings | method | fontSize / nodeDensity などViewer表示設定の反映 |
| src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer状態の描画反映 |
| src/settings/model-weave-settings.ts | ModelWeaveViewerPreferences | type | defaultZoom / fontSize / nodeDensity のViewer設定 |
| src/core/render-mode.ts | resolveRenderMode | function | effectiveRenderMode の解決 |
