---
type: mapping
id: MAP-MW-APP-PROCESS-STEP-KIND-TO-MERMAID
name: App Process Step kind to Mermaid Mapping
kind: renderer_mapping
source: "[[DATA-MW-APP-PROCESS-STEP]]"
target: "[[DATA-MW-RENDERER-MERMAID-SOURCE]]"
tags:
  - ModelWeave
  - Mapping
  - BusinessFlow
  - Renderer
---

# App Process Step kind to Mermaid Mapping

## Summary
app_process `Steps.kind` をBusiness Flow Mermaid sourceのnode shapeとColor Scheme style行へ反映するマッピング。
保存MarkdownのStep行を、描画用の派生出力へ変換する境界を示す。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-APP-PROCESS-STEP]] | app_process `## Steps` の保存Markdown行 |
| target | [[DATA-MW-RENDERER-MERMAID-SOURCE]] | Business Flow用Mermaid source |
| rule | [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] | Step kindからshapeとColor Scheme適用を決める |
| context | [[DATA-MW-VIEWER-STATE]].resolvedColorScheme | 描画時に解決済みのColor Scheme |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[DATA-MW-APP-PROCESS-STEP]].id | [[DATA-MW-RENDERER-MERMAID-SOURCE]].sourceText | Mermaid node IDへ変換 | [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] | Y | sanitizeされたStep node ID |
| [[DATA-MW-APP-PROCESS-STEP]].label | [[DATA-MW-RENDERER-MERMAID-SOURCE]].sourceText | Mermaid labelへ変換 | [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] | N | labelが空の場合はidなどへfallback |
| [[DATA-MW-APP-PROCESS-STEP]].kind | [[DATA-MW-RENDERER-MERMAID-SOURCE]].sourceText | node shapeを選択 | [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] | N | recognized kindは固有shape、blank / unknownはprocess shape |
| [[DATA-MW-APP-PROCESS-STEP]].kind | [[DATA-MW-RENDERER-MERMAID-SOURCE]].colorStyleLines | target=app_process のstyleへ変換 | [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] | N | Color Schemeがある場合だけclassDef / class行を生成 |
| [[DATA-MW-APP-PROCESS-STEP]].domain | [[DATA-MW-RENDERER-MERMAID-SOURCE]].sourceText | Domain groupへ配置 | | N | Domain Sources / local Domains解決後のgroupに配置 |
| [[DATA-MW-APP-PROCESS-STEP]].input | Business Flow interaction target | ref-aware target解決 | | N | hover / click target用。shape選択とは別 |
| [[DATA-MW-APP-PROCESS-STEP]].output | Business Flow interaction target | ref-aware target解決 | | N | hover / click target用。shape選択とは別 |
| [[DATA-MW-APP-PROCESS-STEP]].rule | Business Flow interaction target | ref-aware target解決 | | N | hover / click target用。shape選択とは別 |
| [[DATA-MW-APP-PROCESS-STEP]].invoke | Business Flow interaction target | ref-aware target解決 | | N | hover / click target用。shape選択とは別 |
| [[DATA-MW-APP-PROCESS-STEP]].screen | Business Flow interaction target | ref-aware target解決 | | N | hover / click target用。shape選択とは別 |

## Notes
- このmappingはBusiness Flow描画用の派生出力への変換を表す。app_process Markdown本文を書き換えない。
- `Flows.from` / `Flows.to` は `Steps.id` を参照するため、Step kindからFlow endpointを生成しない。
- Flow Connect ModeはStepクリックから `Steps.id` を使って `## Flows` を更新する。Step kindは主に表示形状と配色の入力である。
- ref-aware hover / click targetはStepの参照列から解決され、Source Links ExplorerやReference Explorerの一覧状態とは別である。
- DFD / Class / ER / 汎用Mermaidのnode shapeや接続編集には適用しない。

## Source Links

| path | notes |
|---|---|
| src/renderers/app-process-business-flow.ts | getStepShapeKind / formatAppProcessStepNode がStep kindをMermaid node shapeへ変換する |
| src/renderers/app-process-business-flow.ts | addAppProcessStepStyle が `target=app_process` と `Steps.kind` でColor Schemeを適用する |
| src/core/app-process-step-interaction-target.ts | resolveAppProcessStepInteractionTarget がStep参照列からhover / click targetを解決する |
| src/core/color-scheme.ts | resolveColorStyle がtarget / kindに対応する色を解決する |
