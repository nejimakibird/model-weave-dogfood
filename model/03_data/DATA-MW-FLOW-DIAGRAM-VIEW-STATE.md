---
type: data_object
id: DATA-MW-FLOW-DIAGRAM-VIEW-STATE
name: Flow Diagram表示状態
kind: viewer_state
data_format: object
tags:
  - FlowDiagram
  - Viewer
  - State
---

# Flow Diagram表示状態

## Summary

Flow DiagramのDetail / Screen viewを、Viewer内でファイル単位に保持する実行時状態です。
`flow_view` frontmatterと `defaultFlowDiagramViewMode` は初期化入力であり、toolbar切替後の実効modeはMarkdown本文へ保存されません。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| filePath | 対象ファイル | string | | Y | filePath | | Flow Diagram MarkdownのVault path |
| mode | 実効Flow view | string | | Y | mode | | detail / screen |
| initializationKey | 初期化キー | string | | Y | initializationKey | | frontmatterまたはsettings由来の再初期化判定キー |
| frontmatterFlowView | frontmatter flow_view | string | | N | diagram.flowView | [[DATA-MW-FLOW-DIAGRAM-PARSED-DIAGRAM]] | 有効なfrontmatter指定がある場合の初期mode |
| flowViewSpecified | flow_view指定有無 | boolean | | Y | diagram.flowViewSpecified | [[DATA-MW-FLOW-DIAGRAM-PARSED-DIAGRAM]] | 有効なfrontmatter `flow_view` が指定されたか |
| flowViewRaw | flow_view raw値 | string | | N | diagram.flowViewRaw | [[DATA-MW-FLOW-DIAGRAM-PARSED-DIAGRAM]] | 不正値や空値を含む元入力 |
| defaultFlowDiagramViewMode | 既定Flow view | string | | Y | viewerPreferences.defaultFlowDiagramViewMode | [[DATA-MW-PLUGIN-SETTINGS]] | frontmatter未指定時の初期mode |
| initializationChanged | 初期化変更 | boolean | | N | synchronize.result | | key変化によりmodeを再初期化したか |

## Notes

- `mode` はtoolbarとrendererへ渡される実効値であり、rendererはfrontmatterを再評価しない。
- 有効なfrontmatter `flow_view` があるファイルは、Settingsの既定値変更で上書きしない。
- frontmatter未指定ファイルは、`defaultFlowDiagramViewMode` の変更で再初期化される。
- 不正な `flow_view` はwarning対象になり、Settings既定値へfallbackする。
- initializationKeyが変わった場合だけ再初期化し、通常の再描画やindex refreshではtoolbar選択を維持する。
- 再初期化やtoolbar切替時のviewport reset / FitはViewer操作結果であり、このDATAには保存しない。
- toolbar切替、Settings変更、renderer mode同期はMarkdown正本を書き換えない。

## Source Links

| path | notes |
|---|---|
| src/core/flow-diagram-view-mode.ts | symbol: FlowDiagramViewModeState; kind: class; ファイル単位のmodeとinitializationKeyを保持 |
| src/core/flow-diagram-view-mode.ts | symbol: buildFlowDiagramViewModeInitializationKey; kind: function; frontmatter / settingsから初期化キーを生成 |
| src/core/flow-diagram-view-mode.ts | symbol: resolveInitialFlowDiagramViewMode; kind: function; flow_view / default / detail fallbackを解決 |
| src/views/modeling-preview-view.ts | symbol: prepareFlowDiagramViewMode; kind: method; Preview表示前にview mode stateを同期 |
| src/views/modeling-preview-view.ts | symbol: setFlowDiagramViewMode; kind: method; toolbar変更時にViewer stateを更新 |
| src/settings/model-weave-settings.ts | symbol: defaultFlowDiagramViewMode; kind: field; Settings側の既定Flow view |
