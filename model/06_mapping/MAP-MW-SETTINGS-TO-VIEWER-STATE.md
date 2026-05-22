---
type: mapping
id: MAP-MW-SETTINGS-TO-VIEWER-STATE
name: Settings to Viewer State Mapping
kind: data_to_data
source: "[[DATA-MW-PLUGIN-SETTINGS]]"
target: "[[DATA-MW-VIEWER-STATE]]"
tags:
  - ModelWeave
  - Viewer
  - Settings
  - Mapping
---

# Settings to Viewer State Mapping

## Summary

Model Weaveプラグイン設定を、Viewer / Renderer / Viewport が使用する実行時状態へ反映するためのマッピングを定義する。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-PLUGIN-SETTINGS]] | Obsidianプラグイン設定 |
| target | [[DATA-MW-VIEWER-STATE]] | Viewer実行時状態 |
| target | [[DATA-MW-VIEWPORT-STATE]] | Viewport表示状態 |
| target | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | Rendererモード解決入力 |
| rule | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | 設定値の実効解決 |
| rule | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | render mode解決 |
| rule | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | auto fit制御 |
| rule | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | viewport状態保持 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[DATA-MW-PLUGIN-SETTINGS]].defaultRenderMode | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]].defaultRenderMode | 未設定/不正値を補正して反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | 実効値は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] で決定 |
| [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | [[DATA-MW-VIEWPORT-STATE]].defaultScale | fit または 100% を初期値へ変換 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | fitの場合はauto fit制御へ委譲 |
| [[DATA-MW-PLUGIN-SETTINGS]].autoFitOnOpen | [[DATA-MW-VIEWER-STATE]].autoFitEnabled | boolean値をViewer状態へ反映 | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | N | 初回表示時のfitToView |
| [[DATA-MW-PLUGIN-SETTINGS]].preserveViewportState | [[DATA-MW-VIEWER-STATE]].preserveViewportState | boolean値をViewer状態へ反映 | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | N | ファイル単位のviewport状態保持 |
| [[DATA-MW-PLUGIN-SETTINGS]].diagnosticsVisible | [[DATA-MW-VIEWER-STATE]].diagnosticsVisible | boolean値をViewer状態へ反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | 診断生成ではなく表示制御 |
| [[DATA-MW-PLUGIN-SETTINGS]].statusBarVisible | [[DATA-MW-VIEWER-STATE]].statusBarVisible | boolean値をViewer状態へ反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | ステータスバー表示制御 |
| [[DATA-MW-PLUGIN-SETTINGS]].viewerFontScale | [[DATA-MW-VIEWER-STATE]].viewerFontScale | small / normal / large 等を反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | CSSまたはView側で解釈 |
| [[DATA-MW-PLUGIN-SETTINGS]].mermaidThemeMode | [[DATA-MW-VIEWER-STATE]].mermaidThemeMode | Mermaid描画テーマ方針として保持 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | Obsidianテーマ追従など |
| [[DATA-MW-PLUGIN-SETTINGS]].enableSourceLinks | [[DATA-MW-VIEWER-STATE]].sourceLinksEnabled | boolean値をViewer状態へ反映 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | Source Links表示や追跡 |

## Rules

- 設定値の正規化・フォールバックは [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] に従う。
- render mode の最終的な実効値は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に委ねる。
- defaultZoom=fit の場合、`viewportState.defaultScale` へ直接数値を入れるのではなく、auto fit制御へ委譲する。
- diagnosticsVisible=false の場合でも診断生成は継続し、UI表示（パネル）のみ抑制する。
- statusBarVisible=false の場合でもViewer状態は維持し、UI表示のみ抑制する。
- 設定反映はViewerの一時状態へ行い、Markdown本文やfrontmatterは変更しない。

## Notes

- 本mappingはObsidianプラグイン設定とViewer実行時状態の橋渡しを表す。
- 実際の設定保存はObsidianプラグイン設定に委ねる。
- 設定項目が実装上の名称と完全一致しない場合は、Source Links確認後に補正する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| source/model-weave-repo/src/settings.ts | DEFAULT_SETTINGS | constant | 既定設定値 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer状態の反映 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | prepareFileViewportState | method | viewport初期化 |