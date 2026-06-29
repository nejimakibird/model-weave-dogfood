---
type: rule
id: RULE-MW-BUSINESS-FLOW-CONNECT-FLOW
name: Business Flow Flow接続ルール
kind: interaction
tags:
  - ModelWeave
  - Rule
  - BusinessFlow
---

# Business Flow Flow接続ルール

## Summary
app_process Business FlowのFlow接続モードで、クリックしたStepを `## Flows` の `from` / `to` として追加できるかを判定するルール。
Business Flow限定のMarkdown更新支援であり、DFD、Class、ER、汎用Mermaidの接続編集ルールではない。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| appProcessSteps | Steps.id list | app_process Markdown | Y | 現在ファイルのBusiness Flow Step ID |
| existingFlows | Flows row list | app_process Markdown | N | 既存の明示Flow |
| interactionState | [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]] | Viewer | Y | 接続モードと接続元Stepの状態 |
| clickedStepId | Steps.id | Business Flow SVG | Y | 今回クリックされたStep |

## Conditions

| id | expression | severity | message | notes |
|---|---|---|---|---|
| COND-SCOPE | current file is app_process Business Flow | error | Flow接続モードはapp_process Business Flow限定です。 | DFD / Class / ER / 汎用Mermaidは対象外 |
| COND-ENDPOINTS | from and to come from clicked Steps.id | info | Flow endpoint is local Steps.id. | UI上のBusiness Flow Stepクリックから渡される。addAppProcessFlow自体は存在検証をしない |
| COND-SAME-STEP | from === clickedStepId | info | 接続元Stepの選択を解除します。 | 同じStepの再クリックはFlow追加ではなく解除 |
| COND-DUPLICATE | existing Flows contains same from and to | warning | Flowはすでに存在します。 | duplicateではMarkdownを更新しない |
| COND-MISSING-STEPS | Steps section is missing | error | Steps section is required. | `## Steps` がない場合はmissing-steps |
| COND-FLOWS-HEADER | generated Flows uses from / to / condition / label / notes | info | Flows table header is generated when missing. | `## Flows` がない場合は正しいheaderで追加する |
| COND-TRANSITIONS | external exits stay in Transitions | info | 外部遷移はTransitionsで扱います。 | screen / processへの外部遷移とFlowsを混同しない |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| flowAddResult | [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]].flowAddStatus | [[PROC-MW-BUSINESS-FLOW-CONNECT-FLOW]] | added / duplicate / missing-steps / invalid |
| updatedMarkdown | app_process Markdown | current file | addedの場合だけ `## Flows` に行を追加する |

## Notes
- `addAppProcessFlow` は `from` / `to` をtrimし、空の場合はinvalidとして扱う。Step IDの存在検証はBusiness Flow上のクリック対象から渡ることに依存する。
- 既存の `## Flows` に同じ `from` / `to` の行がある場合はduplicateとして扱い、同じ行を追加しない。
- `## Flows` が存在しない場合は、`## Steps` の後ろに正しいheaderのFlow表を作る。
- 明示FlowはBusiness Flow描画時に接続元Stepの暗黙の行順Flowより優先される。
- ref-aware hover / click targetはStepの関連モデル参照を開く支援であり、Flow接続モードの `from` / `to` 判定とは別である。

## Source Links

| path | notes |
|---|---|
| src/core/app-process-flow-editor.ts | addAppProcessFlow がFlow追加可否、duplicate、missing-steps、invalidを判定する |
| src/views/modeling-preview-view.ts | handleAppProcessFlowConnectStepClick が接続元選択、解除、Flow追加結果通知を扱う |
| src/renderers/app-process-business-flow.ts | Explicit Flowsと暗黙の行順Flowの描画関係 |
