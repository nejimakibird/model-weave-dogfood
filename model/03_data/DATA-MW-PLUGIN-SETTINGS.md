---
type: data_object
id: DATA-MW-PLUGIN-SETTINGS
name: Model Weaveプラグイン設定
kind: settings
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
| defaultRenderMode | 既定レンダリングモード | string | | N | defaultRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | auto / custom / mermaid 等 |
| defaultZoom | 既定ズーム | string | | N | defaultZoom | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | fit / 100% 等。実装設定に合わせる |
| autoFitOnOpen | オープン時自動フィット | boolean | | N | autoFitOnOpen | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | ファイル表示時にfitToViewするか |
| preserveViewportState | Viewport状態保持 | boolean | | N | preserveViewportState | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | ファイル単位のzoom/pan状態を保持するか |
| mermaidThemeMode | Mermaidテーマモード | string | | N | mermaidThemeMode | | Obsidianテーマまたは固定テーマへの追従 |
| viewerFontScale | Viewer文字サイズ倍率 | string | | N | viewerFontScale | | small / normal / large 等 |
| diagnosticsVisible | 診断パネル表示 | boolean | | N | diagnosticsVisible | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | 診断パネルを表示するか |
| statusBarVisible | ステータスバー表示 | boolean | | N | statusBarVisible | [[SCR-MW-VIEWER-STATUS-BAR]] | Viewerステータスバーを表示するか |
| enableSourceLinks | Source Links有効化 | boolean | | N | enableSourceLinks | | Source Links表示や追跡を有効にするか |
| settingsVersion | 設定バージョン | string | | N | settingsVersion | | 将来の設定移行用 |

## Notes

- 本データはMarkdownモデルの正本ではなく、Obsidianプラグイン設定として保持される実行時設定を表す。
- defaultRenderMode の実効値解決は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に委ねる。
- defaultZoom / autoFitOnOpen / preserveViewportState は Viewer / Viewport の初期状態に影響する。
- 実装上の設定項目名と完全一致しない場合は、Source Linksで確認して後続タスクで補正する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| source/model-weave-repo/src/settings.ts | DEFAULT_SETTINGS | constant | 既定設定値 |
| source/model-weave-repo/src/core/render-mode.ts | resolveRenderMode | function | 既定レンダリングモードの解決 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | prepareFileViewportState | method | defaultZoom / viewport状態の反映 |