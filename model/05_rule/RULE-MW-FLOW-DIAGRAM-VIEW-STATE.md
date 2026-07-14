---
type: rule
id: RULE-MW-FLOW-DIAGRAM-VIEW-STATE
name: Flow Diagram表示状態解決ルール
kind: viewer_rule
tags:
  - FlowDiagram
  - Viewer
  - Rule
---

# Flow Diagram表示状態解決ルール

## Summary

Flow DiagramのDetail / Screen viewについて、frontmatter、Settings、Viewer toolbar stateを分離して実効modeを決めるルールです。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| parsedFlowDiagram | [[DATA-MW-FLOW-DIAGRAM-PARSED-DIAGRAM]] | Parser | Y | flowView / flowViewSpecified / flowViewRaw |
| settings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | Y | defaultFlowDiagramViewMode |
| viewerState | [[DATA-MW-FLOW-DIAGRAM-VIEW-STATE]] | Viewer | N | filePathごとのmodeとinitializationKey |

## Conditions

| id | expression | severity | message | notes |
|---|---|---|---|---|
| C1 | valid frontmatter.flow_view exists | info | frontmatter.flow_viewを初期modeに使う | detail / screen の指定をViewer初期化に反映する |
| C2 | frontmatter.flow_view is missing | info | defaultFlowDiagramViewModeを初期modeに使う | Markdownに指定がない場合は保存設定へfallbackする |
| C3 | frontmatter.flow_view is invalid | warning | invalid flow_viewは警告し保存設定へfallbackする | 未対応値は診断対象であり描画を止めない |
| C4 | defaultFlowDiagramViewMode unavailable | info | Settingsが使えない場合はdetailへfallbackする | 設定未読込や不正値の保護経路 |
| C5 | initializationKey unchanged | info | toolbar modeを維持する | 同じファイルと初期値ではユーザー選択を保持する |
| C6 | initializationKey changed | info | Flow Diagram view stateを再初期化する | ファイル変更やfrontmatter変更で初期modeを再解決する |
| C7 | toolbar selection changed | info | toolbar selectionはViewer stateだけを更新する | Markdown本文やfrontmatterは変更しない |
| C8 | renderer receives effective mode | info | rendererはViewerから渡されたmodeで描画する | renderer側でfrontmatterを再評価しない |
| C9 | state reinitialized | info | viewport cacheをリセットし再描画後にFitする | 初期化後の画面位置を安定させる |
| C10 | ordinary refresh with same key | info | 通常更新ではtoolbar selectionを維持する | Fitやindex refreshや同一key再描画ではmodeを戻さない |

## Rules

| id | rule | result | notes |
|---|---|---|---|
| R1 | valid frontmatter flow_view exists | initial mode is frontmatter value | Settings既定値で上書きしない |
| R2 | no valid frontmatter flow_view | initial mode is defaultFlowDiagramViewMode | 不正設定はdetailへfallback |
| R3 | no valid setting | initial mode is detail | 最終fallback |
| R4 | initializationKey unchanged | keep toolbar mode | 通常再描画やindex refreshでは維持する |
| R5 | initializationKey changed | reinitialize mode | frontmatterや関連settings変更を反映する |
| R6 | toolbar changed | update viewer state | Markdown本文とfrontmatterは変更しない |
| R7 | renderer called | use effective mode | rendererはfrontmatterを再評価しない |

## Notes

- `flow_view` は初期表示入力であり、固定表示指定ではない。
- toolbar selectorの表示値とrendererへ渡すmodeは同じeffective modeを使う。
- toolbar変更やSettings変更はMarkdown正本を書き換えない。
- reinitialize時はviewport cacheを破棄し、再描画後にFitする。
- 不正な `flow_view` はwarningを出し、Settings既定値へfallbackする。

## Source Links

| path | notes |
|---|---|
| src/core/flow-diagram-view-mode.ts | symbol: normalizeFlowDiagramViewMode; kind: function; detail / screen正規化 |
| src/core/flow-diagram-view-mode.ts | symbol: resolveInitialFlowDiagramViewMode; kind: function; 初期mode解決 |
| src/core/flow-diagram-view-mode.ts | symbol: buildFlowDiagramViewModeInitializationKey; kind: function; 再初期化key生成 |
| src/core/flow-diagram-view-mode.ts | symbol: FlowDiagramViewModeState; kind: class; file scoped state管理 |
| src/views/modeling-preview-view.ts | symbol: setFlowDiagramViewMode; kind: method; toolbar変更時のstate更新 |
