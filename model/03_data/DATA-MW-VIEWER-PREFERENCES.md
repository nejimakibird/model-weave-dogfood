---
type: data_object
id: DATA-MW-VIEWER-PREFERENCES
name: Viewer表示設定
kind: settings
data_format: object
tags:
  - ModelWeave
  - Viewer
  - Settings
---

# Viewer表示設定

## Summary

ModelWeaveViewerPreferences として ModelingPreviewView に渡される、Viewer表示用の実装済み設定を表す。
ModelWeaveSettings から表示に必要な項目だけを選択した派生データであり、Markdownモデルの正本ではない。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| defaultZoom | 既定ズーム | string | | N | defaultZoom | [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | fit / 100 |
| fontSize | Viewer文字サイズ | string | | N | fontSize | [[DATA-MW-PLUGIN-SETTINGS]].fontSize | small / normal / large |
| nodeDensity | ノード密度 | string | | N | nodeDensity | [[DATA-MW-PLUGIN-SETTINGS]].nodeDensity | compact / normal / relaxed |
| localSourceRoot | ローカルソースルート | string | | N | localSourceRoot | [[DATA-MW-PLUGIN-SETTINGS]].localSourceRoot | Source Linksのrelative path解決に使う |
| uiLanguage | UI言語 | string | | N | uiLanguage | [[DATA-MW-PLUGIN-SETTINGS]].uiLanguage | auto / en / ja |
| showMermaidRenderDebug | Mermaid描画デバッグ | boolean | | N | showMermaidRenderDebug | [[DATA-MW-PLUGIN-SETTINGS]].showMermaidRenderDebug | Mermaid描画診断表示に使う |

## Notes

- 現行実装では ModelWeavePlugin.getViewerPreferences が ModelWeaveSettings からこの設定群を選択する。
- ModelingPreviewView.applyViewerSettings は viewerPreferences を受け取り、表示と言語設定に反映する。
- enableRelationshipView は ViewerPreferences には含まれず、impact summary / Relationship View の生成ゲートとして別に扱われる。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/settings/model-weave-settings.ts | ModelWeaveViewerPreferences | type | Viewerへ渡す設定型 |
| src/main.ts | getViewerPreferences | method | ModelWeaveSettingsからViewer設定を選択 |
| src/views/modeling-preview-view.ts | applyViewerSettings | method | Viewer表示設定を反映 |
