---
type: data_object
id: DATA-MW-BUSINESS-FLOW-INTERACTION-STATE
name: Business Flow操作状態
kind: state
data_format: object
tags:
  - ModelWeave
  - Viewer
  - BusinessFlow
---

# Business Flow操作状態

## Summary
app_process Business Flow上のFlow接続モードと、Step hover / click用の参照操作先を保持するViewer実行時状態。
Markdownの保存設定ではなく、現在表示中のViewer操作に伴う一時状態として扱う。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| filePath | 対象ファイルパス | string | | Y | appProcessFlowConnectFilePath | | Flow接続モードの対象app_processファイル |
| connectModeEnabled | Flow接続モード有効 | boolean | | Y | appProcessFlowConnectModeEnabled | | app_process Business Flow限定の接続入力支援 |
| sourceStepId | 接続元Step ID | string | | N | appProcessFlowConnectSourceStepId | | 1回目クリックで保持し、同じStep再クリックで解除 |
| targetStepId | 接続先Step ID | string | | N | handleAppProcessFlowConnectStepClick.stepId | | 2回目クリックで渡される接続先 |
| flowAddStatus | Flow追加結果 | string | | N | AddAppProcessFlowResult.status | | added / duplicate / missing-steps / invalid |
| markdownChanged | Markdown変更有無 | boolean | | N | AddAppProcessFlowResult.changed | | trueの場合のみvault.modifyでMarkdownを更新 |
| interactionTargets | Step参照操作先 | GraphInteractionTarget list | | N | buildAppProcessBusinessFlowInteractionTargets | | ref-aware hover / click targetの派生リスト |

## Notes
- このデータはViewer実行時状態であり、app_processファイルの正本構造ではない。
- Flow接続モードはBusiness Flow SVG上で接続元Stepと接続先Stepを選び、現在ファイルの `## Flows` に行を追加する入力支援である。
- `Flows.from` と `Flows.to` は現在の `Steps.id` を参照する。外部screenや外部processへの遷移は `## Transitions` で扱う。
- 既存の同一 `from` / `to` のFlowがある場合はduplicateとして扱い、Markdownは更新しない。
- `interactionTargets` は `Steps.input` / `Steps.output` / `Steps.rule` / `Steps.invoke` / `Steps.screen` と `Inputs` / `Outputs` の行を使って解決される。
- Flow接続モードとref-aware hover / click targetはapp_process Business Flow限定であり、DFD接続編集、Class / ER接続編集、汎用ノードエディタではない。
- Source Links ExplorerやReference Explorerの索引、検索、選択状態とは別のViewer内操作状態である。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | appProcessFlowConnectFilePath / appProcessFlowConnectModeEnabled / appProcessFlowConnectSourceStepId を保持する |
| src/views/modeling-preview-view.ts | handleAppProcessFlowConnectStepClick が接続元選択、解除、Flow追加結果を処理する |
| src/core/app-process-flow-editor.ts | addAppProcessFlow が `## Flows` 行の追加、duplicate、missing-steps、invalidを判定する |
| src/core/app-process-step-interaction-target.ts | resolveAppProcessStepInteractionTarget がStep参照操作先を解決する |
| src/renderers/app-process-business-flow.ts | buildAppProcessBusinessFlowInteractionTargets がBusiness Flow上のhover / click targetを作る |
| src/views/mermaid-node-interactions.ts | attachMermaidNodeInteractions がhover previewとclick targetをMermaid SVGへ付与する |
