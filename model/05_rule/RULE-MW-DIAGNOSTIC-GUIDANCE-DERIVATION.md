---
type: rule
id: RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION
name: 診断ガイダンス導出ルール
kind: diagnostics_rule
tags:
  - ModelWeave
  - Diagnostic
  - Viewer
---

# 診断ガイダンス導出ルール

## Summary

Viewerの診断カードで表示されるmetadata、details、copy actionsを、`ValidationWarning` の `message` と `context` から導出するルール。
現行実装ではcodeごとの個別UIではなく、`context` のキーやmessage patternから汎用的にガイダンスを組み立てる。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| diagnostic | [[DATA-MW-CORE-DIAGNOSTIC]] | [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] | Y | Viewerへ渡されたValidationWarning |

## Conditions

| id | expression | severity | message | notes |
|---|---|---|---|---|
| G1 | diagnostic.message exists | info | Copy Messageを表示する | 診断メッセージをそのままコピー対象にする |
| G2 | diagnostic exists | info | Copy Markdownを表示する | severity / code / message / metadata / detailsをMarkdown化する |
| G3 | context has reference-like key or message has quoted reference | info | Copy Referenceを表示する | reference / ref / target / targetRef / sourceRef / valueを参照する |
| G4 | context has expected header or table-column message | info | Copy Expected Headerを表示する | expectedHeader / expectedHeaders / expectedまたはsection名から導出する |
| G5 | message mentions required frontmatter key | info | Copy Frontmatter Exampleを表示する | 引用されたfrontmatter keyから例を生成する |
| G6 | path / section / line / rowIndex exists | info | metadataとして表示する | filePath / path / section / field / line / fromLine / rowIndexを使う |
| G7 | message mentions render_mode | info | render_mode詳細を表示する | requested render modeとformat default説明を表示する |
| G8 | Quick Fix requested | info | futureとして扱う | 現行srcはMarkdown本文を自動修正しない |
| G9 | field is Domain Sources or Domain Sources.ref | info | Domain Sources確認を促す | ref解決、type: domains、空のDomainsを確認する |
| G10 | field is Steps.domain | info | Business Flow Domain確認を促す | stepId / domainId をdetailsやreference候補に使う |
| G11 | Domain duplicate or field conflict message | warning | Domain定義の競合確認を促す | source間のid重複、name / kind / parent差分を確認する |
| G12 | local Domain override message | warning | local Domainsの上書き確認を促す | app_processまたはDFDのlocal Domainsが外部Domainを上書きする場合 |

## Notes

- `VALIDATION_WARNING_CODES` は診断種別の有効値を定義するが、Viewerのcopy guidanceはcode単独ではなくmessageとcontextを併用して導出される。
- `invalid-table-column` と `invalid-table-row` はexpected header guidanceと関係しやすい。
- `unresolved-reference`、`duplicate-mapping-target-member`、`class-relation-target-not-diagram-compatible` はreference guidanceと関係しやすい。
- `missing-name`、`missing-kind`、`missing-schema`、`invalid-structure` はfrontmatterや必須fieldのmessageを持つ場合にfrontmatter example guidanceと関係する。
- Domain Sources、Domain Diagram、app_process `Steps.domain` の診断は、`field`、`context.stepId`、`context.domainId`、`context.rowIndex` を使って通常のmetadata / details / copy actionsへ載せる。
- Domain診断のcopy guidanceは参照値や期待箇所を示す補助であり、Domain Sourcesやlocal Domainsを自動修正するものではない。
- Source Linksのavailable / missing / open failureはSource Links UIのstatusであり、ここではdiagnostic code guidanceとして扱わない。
- Quick FixやMarkdown自動修正はfutureであり、copy guidanceと混同しない。

## Source Links

| path | notes |
|---|---|
| src/types/warnings.ts | VALIDATION_WARNING_CODES と VALIDATION_WARNING_SEVERITIES |
| src/views/modeling-preview-view.ts | renderDiagnosticCard とcopy actions |
| src/views/modeling-preview-view.ts | getDiagnosticMetadata とgetDiagnosticDetailEntries |
| src/views/modeling-preview-view.ts | getDiagnosticReferenceValue |
| src/views/modeling-preview-view.ts | getExpectedHeaderForDiagnostic |
| src/views/modeling-preview-view.ts | getFrontmatterExampleForDiagnostic |
| src/views/modeling-preview-view.ts | formatDiagnosticAsMarkdown |
| src/core/domain-diagnostics.ts | Domain diagnostics message helpers |
| src/core/domain-diagram-resolver.ts | Domain Sources diagnostics |
| src/core/app-process-domain-resolver.ts | app_process Steps.domain diagnostics |
