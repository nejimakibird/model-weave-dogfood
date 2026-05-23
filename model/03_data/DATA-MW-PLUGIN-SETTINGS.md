---
type: data_object
id: DATA-MW-PLUGIN-SETTINGS
name: Model Weaveプラグイン設定
kind: settings
data_format: object
tags:
  - ModelWeave
  - Settings
  - Viewer
---

# Model Weaveプラグイン設定

## Summary

Model Weaveプラグイン全体の設定値を表す論理データ構造。
Viewer表示、Renderer選択、Viewport初期化、Mermaid描画、UI表示などに影響する設定値を保持する。
設定はMarkdownモデルの正本ではなく、Obsidianプラグイン設定として保存される実行時構成である。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| defaultRenderMode | 既定レンダリングモード | string | | N | defaultRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS に実装済み。auto / custom / mermaid 等 |
| defaultZoom | 既定ズーム | string | | N | defaultZoom | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | implemented。src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS に実装済み。fit / 100 等 |
| fontSize | Viewer文字サイズ | string | | N | fontSize | | implemented。src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS に実装済み。small / normal / large |
| nodeDensity | ノード密度 | string | | N | nodeDensity | | implemented。src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS に実装済み。compact / normal / relaxed |
| autoFitOnOpen | オープン時自動フィット | boolean | | N | autoFitOnOpen | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | planned。Auto Fit挙動の将来設定。現行ModelWeaveSettingsには未実装 |
| preserveViewportState | Viewport状態保持 | boolean | | N | preserveViewportState | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | planned。Viewport状態保持の将来設定。現行ModelWeaveSettingsには未実装 |
| mermaidThemeMode | Mermaidテーマモード | string | | N | mermaidThemeMode | | planned。Mermaidテーマ制御の将来設定。現行ModelWeaveSettingsには未実装 |
| viewerFontScale | Viewer文字サイズ倍率 | string | | N | viewerFontScale | | planned。Viewer表示調整用の将来設定。現行ModelWeaveSettingsには未実装 |
| diagnosticsVisible | 診断パネル表示 | boolean | | N | diagnosticsVisible | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | planned。診断パネル表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| statusBarVisible | ステータスバー表示 | boolean | | N | statusBarVisible | [[SCR-MW-VIEWER-STATUS-BAR]] | planned。ステータスバー表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| enableSourceLinks | Source Links有効化 | boolean | | N | enableSourceLinks | | future。Source Links Explorer / Source Links索引化がfutureのため現行実装済みではない |
| settingsVersion | 設定バージョン | string | | N | settingsVersion | | planned。将来の設定移行用。現行src/settings/model-weave-settings.tsには未実装 |

## Notes

- 本データはMarkdownモデルの正本ではなく、Obsidianプラグイン設定として保持される実行時設定を表す。
- 現行実装側の設定項目は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- この data_object は現行実装済み設定とV0.7以降の先行設計項目を含むため、ファイル全体としては partial と扱う。
- dogfood側のみの項目は、現時点でModel Weave本体に実装済みとは断定しない。
- enableSourceLinks は Source Links関連機能が future であるため、現時点では将来構想として扱う。
- defaultRenderMode の実効値解決は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に委ねる。
- defaultZoom / fontSize / nodeDensity は Viewer表示設定に影響する。
- autoFitOnOpen / preserveViewportState は Viewer / Viewport の将来設定として扱う。
- 実装上の設定項目名と完全一致しない場合は、Source Linksで確認して後続タスクで補正する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| src/settings/model-weave-settings.ts | DEFAULT_MODEL_WEAVE_SETTINGS | constant | 既定設定値 |
| src/core/render-mode.ts | resolveRenderMode | function | 既定レンダリングモードの解決 |
| src/views/modeling-preview-view.ts | prepareFileViewportState | method | defaultZoom / viewport状態の反映 |
