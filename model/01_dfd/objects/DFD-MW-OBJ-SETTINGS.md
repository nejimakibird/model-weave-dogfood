---
type: dfd_object
id: DFD-MW-OBJ-SETTINGS
name: Plugin Settings
kind: datastore
tags:
  - DFD
  - Settings
  - Configuration
---

# Plugin Settings

## Summary

Model Weaveプラグインの実行時設定境界。
ModelWeaveSettings は normalizeModelWeaveSettings で正規化され、getViewerPreferences を通じてPreview Viewへ渡される。
localSourceRoot、format別render mode default、Flow Diagram既定表示、relationship view、Mermaid debug、UI languageなどの実装済み設定を保持する。

## Details

| key | value | notes |
|---|---|---|
| owner | settings/model-weave-settings.ts | ModelWeaveSettings |
| normalized | normalizeModelWeaveSettings | 旧設定値も補助入力として扱う |
| saved_by | ModelWeavePlugin.updateSettings | saveData後にview更新 |
| viewer_preferences | ModelWeaveViewerPreferences | Preview Viewへ渡す表示設定 |
| flow_diagram_view | defaultFlowDiagramViewMode | frontmatter未指定Flow Diagramの初期表示 |
| related_data | [[DATA-MW-PLUGIN-SETTINGS]] | plugin settings model |
| viewer_state | [[DATA-MW-VIEWER-STATE]] | viewerPreferencesに反映 |
| related_process | [[PROC-MW-SETTINGS-SAVE]] | settings保存処理 |

## Source Links

| path | notes |
|---|---|
| src/settings/model-weave-settings.ts | ModelWeaveSettings / ModelWeaveViewerPreferences |
| src/settings/model-weave-settings.ts | normalizeModelWeaveSettings |
| src/core/flow-diagram-view-mode.ts | Flow Diagram view mode fallback |
| src/main.ts | updateSettings / getViewerPreferences |
| src/main.ts | refreshOpenModelWeaveViews |
