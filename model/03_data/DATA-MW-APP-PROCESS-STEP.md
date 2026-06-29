---
type: data_object
id: DATA-MW-APP-PROCESS-STEP
name: app_process Step行
kind: markdown_row
data_format: object
tags:
  - ModelWeave
  - AppProcess
  - BusinessFlow
---

# app_process Step行

## Summary
app_process の `## Steps` テーブル1行を表す保存Markdown上のデータ。
Business Flow previewでは、この行の `kind` がStepの表示意味、Mermaid node shape、Color Scheme `target=app_process` の適用キーとして使われる。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| id | Step ID | string | | Y | steps.id | | `Flows.from` / `Flows.to` から参照されるローカルID |
| domain | Domain ID | string | | N | steps.domain | | Business Flow group配置とDomain Sources解決に使う |
| lane | Lane | string | | N | steps.lane | | legacy-compatibleな配置情報。domainがある場合は主経路ではない |
| label | 表示ラベル | string | | N | steps.label | | Business Flow node label |
| kind | Step kind | string | | N | steps.kind | [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] | 自由記述。認識済み値はshapeとColor Scheme適用に使われる |
| input | 入力参照 | string | | N | steps.input | | Inputs.id、data ID、中間データ名、または参照値 |
| output | 出力参照 | string | | N | steps.output | | Outputs.id、data ID、中間データ名、または参照値 |
| rule | ルール参照 | string | | N | steps.rule | | rule IDまたはWikilink |
| invoke | 呼び出しprocess参照 | string | | N | steps.invoke | | app_process IDまたはWikilink |
| screen | 画面参照 | string | | N | steps.screen | | screen IDまたはWikilink |
| notes | 補足 | string | | N | steps.notes | | 表示やレビュー用の補足 |

## Notes
- `Steps.kind` はBusiness Flow previewの表示補助情報であり、プロセス制御そのものを完全に定義するものではない。
- `Steps.kind` は自由記述として保持される。認識済み値は固有shapeを持ち、空または未知の値は通常のprocess rectangleへfallbackする。
- `Flows.from` / `Flows.to` は `Steps.id` を参照し、`Steps.kind` は参照しない。
- Flow Connect ModeはBusiness Flow SVG上のStepクリックから `Steps.id` を使って `## Flows` を更新する。Step kindは接続可否の主キーではない。
- ref-aware hover / click targetは `input` / `output` / `rule` / `invoke` / `screen` と `Inputs` / `Outputs` の行から参照先を解決する。
- Color Schemeが有効な場合、Business Flow Stepは `target=app_process` と `kind=<Steps.kind>` で色を解決する。

## Source Links

| path | notes |
|---|---|
| src/types/models.ts | AppProcessStep が保存Markdown由来のStep行構造を定義する |
| src/parsers/app-process-parser.ts | parseAppProcessStepsTable が `## Steps` テーブルをAppProcessStepへ解析する |
| src/renderers/app-process-business-flow.ts | getStepShapeKind と formatAppProcessStepNode が `Steps.kind` からshapeを決める |
| src/renderers/app-process-business-flow.ts | addAppProcessStepStyle が `target=app_process` と `Steps.kind` でColor Schemeを適用する |
| src/core/app-process-step-interaction-target.ts | resolveAppProcessStepInteractionTarget がStep参照操作先を解決する |
