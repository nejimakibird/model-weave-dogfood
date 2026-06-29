---
type: rule
id: RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING
name: Business Flow Step kind描画ルール
kind: renderer_rule
tags:
  - ModelWeave
  - Rule
  - BusinessFlow
  - Renderer
---

# Business Flow Step kind描画ルール

## Summary
app_process Business Flowの `Steps.kind` を、Mermaid node shapeとColor Scheme `target=app_process` の適用キーへ変換するルール。
Step kindは表示上の意味と形状を補助する値であり、Flow endpointや外部遷移の参照先ではない。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| stepKind | [[DATA-MW-APP-PROCESS-STEP]].kind | app_process `## Steps` | N | 自由記述のStep kind |
| colorScheme | [[DATA-MW-VIEWER-STATE]].resolvedColorScheme | Preview pipeline | N | 解決済みColor Scheme |

## Conditions

| id | expression | severity | message | notes |
|---|---|---|---|---|
| COND-FREE-TEXT | Steps.kind is free text | info | Step kind is free text. | 未知値でも解析は壊さない |
| COND-FALLBACK | kind is blank or unknown | info | Use process rectangle fallback. | 空または未知のkindは通常process shape |
| COND-COLOR-TARGET | target is app_process and kind is Steps.kind | info | Apply app_process color by kind. | Color Schemeがある場合だけ適用 |
| COND-FLOW-ENDPOINT | Flows use Steps.id, not Steps.kind | info | Flow endpoints are step IDs. | Flow Connect ModeでもStep kindは接続先IDではない |
| COND-TRANSITIONS | Transitions handle external exits | info | External exits stay in Transitions. | 外部screen / processへの出口とStep kindを混同しない |

## Calculations

| id | expression | result | notes |
|---|---|---|---|
| SHAPE-TERMINAL | start / end / event / error | terminal | Rounded / stadium node |
| SHAPE-PROCESS | process / blank / unknown | process | Rectangle |
| SHAPE-ROUNDED-PROCESS | api / batch / message / wait / external | rounded-process | Rounded rectangle |
| SHAPE-DECISION | decision | decision | Diamond |
| SHAPE-INPUT | input / screen | input | Parallelogram |
| SHAPE-SUBFLOW | subflow / flow | subflow | Subroutine / double-box |
| SHAPE-DATABASE | data / store | database | Database / cylinder |
| SHAPE-CONNECTOR | connector | connector | Circle |
| COLOR-STEP | target=app_process and kind=Steps.kind | Color Scheme style | Step node color |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| stepShape | Mermaid node shape | [[DATA-MW-RENDERER-MERMAID-SOURCE]].sourceText | Business Flow Step nodeのshape |
| stepColorClass | Mermaid class | [[DATA-MW-RENDERER-MERMAID-SOURCE]].colorStyleLines | Color Schemeがある場合のclassDef / class行 |

## Notes
- 実装では認識済みkindを小文字化して判定する。
- `flow` はsource実装上 `subflow` と同じshapeへfallbackする互換的な認識済み値として扱う。
- Step kindのshape選択はBusiness Flow preview専用であり、DFD / Class / ER / 汎用Mermaidの編集ルールではない。
- unknown kindは通常のprocess rectangleとして描画し、表示を壊さない。
- Color Scheme未設定、またはkindに対応する色がない場合は、Color Scheme側のfallbackに委ねる。
- ref-aware hover / click targetはStepの参照列から解決され、shape選択とは別の責務である。

## Source Links

| path | notes |
|---|---|
| src/renderers/app-process-business-flow.ts | normalizeAppProcessStepKind が認識済みStep kindを判定する |
| src/renderers/app-process-business-flow.ts | getStepShapeKind がStep kindからshape種別を選ぶ |
| src/renderers/app-process-business-flow.ts | formatAppProcessStepNode がMermaid node構文を生成する |
| src/renderers/app-process-business-flow.ts | addAppProcessStepStyle がStep kindをColor Scheme target=app_processへ渡す |
| src/core/color-scheme.ts | resolveColorStyle がtarget / kindから色を解決する |
