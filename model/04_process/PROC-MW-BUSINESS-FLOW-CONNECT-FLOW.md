---
type: app_process
id: PROC-MW-BUSINESS-FLOW-CONNECT-FLOW
name: Business Flow Flow接続更新
kind: viewer_process
tags:
  - ModelWeave
  - AppProcess
  - BusinessFlow
---

# Business Flow Flow接続更新

## Summary
Viewer上のapp_process Business FlowでFlow接続モードを使い、接続元Stepと接続先Stepのクリックから現在ファイルの `## Flows` 行を追加する。
対象はapp_process Business Flow限定であり、DFD、Class、ER、Mermaid全般の汎用接続編集ではない。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| currentProcessFile | app_process Markdown | [[SCR-MW-VIEWER-MAIN-FRAME]] | Y | 現在表示中のapp_processファイル |
| interactionState | [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]] | Viewer | Y | Flow接続モードの一時状態 |
| clickedStepId | Steps.id | Business Flow SVG | Y | ユーザーがクリックしたStep ID |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| updatedMarkdown | app_process Markdown | currentProcessFile | Flow追加成功時だけ `## Flows` を更新する |
| flowAddStatus | [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]].flowAddStatus | [[SCR-MW-VIEWER-MAIN-FRAME]] | added / duplicate / missing-steps / invalid |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Viewer | Flow接続モード開始 | start | currentProcessFile | interactionState |  |  | [[SCR-MW-VIEWER-MAIN-FRAME]] | ツールバーで対象ファイルの接続モードを開始する |
| waitStepClick | Viewer | Stepクリックを待つ | input | clickedStepId |  | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] |  | [[SCR-MW-VIEWER-MAIN-FRAME]] | Business Flow SVG上のStepクリックだけを扱う |
| hasSource | Viewer | 接続元選択済みか判定 | decision | interactionState |  | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] |  |  | sourceStepIdの有無で分岐する |
| selectSource | Viewer | 接続元Stepを保持 | process | clickedStepId | interactionState |  |  |  | 1回目クリックとしてsourceStepIdを保持する |
| sameSource | Viewer | 同じStepか判定 | decision | clickedStepId |  | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] |  |  | 同じStepなら接続元選択を解除する |
| clearSource | Viewer | 接続元選択を解除 | process | interactionState | interactionState |  |  |  | sourceStepIdを空にする |
| readMarkdown | Vault | Markdownを読む | process | currentProcessFile | app_process Markdown |  |  |  | 現在ファイルの本文を読む |
| addFlow | Parser | Flow行を追加 | process | app_process Markdown | updatedMarkdown | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] |  |  | addAppProcessFlowで `## Flows` を更新する |
| changed | Parser | 更新有無を判定 | decision | flowAddStatus |  | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] |  |  | changedとstatusで保存または通知に分岐する |
| saveMarkdown | Vault | Markdownを保存 | process | updatedMarkdown | currentProcessFile |  |  |  | vault.modifyで現在ファイルを更新する |
| showDuplicate | Viewer | 重複を通知 | process | flowAddStatus | interactionState |  |  | [[SCR-MW-VIEWER-MAIN-FRAME]] | 既存Flowと同じ場合はMarkdownを変更しない |
| showFailure | Viewer | 失敗を通知 | process | flowAddStatus | interactionState |  |  | [[SCR-MW-VIEWER-MAIN-FRAME]] | Steps不足または入力不正の場合 |
| end | Viewer | Flow接続処理終了 | end | flowAddStatus | interactionState |  |  | [[SCR-MW-VIEWER-MAIN-FRAME]] | 結果を通知し、必要に応じて再描画する |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | waitStepClick |  | 待機 |  |
| waitStepClick | hasSource |  | 判定 |  |
| hasSource | selectSource | sourceStepId is empty | 接続元選択 | 1回目クリック |
| selectSource | end |  | 選択完了 |  |
| hasSource | sameSource | sourceStepId is set | 接続先判定 | 2回目クリック |
| sameSource | clearSource | from === stepId | 選択解除 | 同じStepを再クリック |
| clearSource | end |  | 解除完了 |  |
| sameSource | readMarkdown | from !== stepId | Flow追加へ | 別Stepをクリック |
| readMarkdown | addFlow |  | 追加 |  |
| addFlow | changed |  | 結果判定 |  |
| changed | saveMarkdown | result.status === "added" | 保存 | Markdownを更新する |
| saveMarkdown | end |  | 完了 |  |
| changed | showDuplicate | result.status === "duplicate" | 重複 | Markdownは更新しない |
| showDuplicate | end |  | 通知完了 |  |
| changed | showFailure | result.status !== "added" | 失敗 | duplicate以外の未変更結果 |
| showFailure | end |  | 通知完了 |  |

## Notes
- `## Flows` が存在しない場合、実装は `Steps` セクション直後に `from / to / condition / label / notes` の表を追加する。
- 追加される行は `from` と `to` のみを持ち、condition / label / notes は空欄から始まる。
- `## Steps` が見つからない場合、missing-stepsとして失敗し、Markdownを更新しない。
- `Flows.from` と `Flows.to` は現在のBusiness Flow内の `Steps.id` であり、外部モデルIDではない。
- 接続元と接続先はBusiness Flow SVG上のStepクリックから渡される。Flow追加関数自体は空ID、`## Steps` 不在、重複を判定する。
- 明示FlowはBusiness Flow描画時に同じ接続元Stepの暗黙の行順Flowより優先される。
- 外部screenや外部processへの遷移は `## Transitions` で扱い、Flow Connect Modeの対象外とする。
- この処理はSource Links Explorer / Reference Explorerの機能ではない。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | Steps: start, waitStepClick, hasSource, selectSource, sameSource, clearSource, readMarkdown, saveMarkdown, end。Flow接続モードのViewer状態とクリック処理 |
| src/core/app-process-flow-editor.ts | Steps: addFlow, changed, showDuplicate, showFailure。addAppProcessFlowによる `## Flows` 更新と結果判定 |
| src/renderers/app-process-business-flow.ts | Business Flow SVG上のStep interaction targetを構築する |
| src/views/mermaid-node-interactions.ts | Business Flow SVGノードのhover / click操作を付与する |
