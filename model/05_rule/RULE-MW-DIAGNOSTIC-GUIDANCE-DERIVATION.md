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

Viewerの診断カードで表示されるmetadata、details、manual guidance、copy actionsを、`ValidationWarning` の `message` と `context` から導出するルール。
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
| G8 | missing frontmatter id / name and safe basename exists | info | Quick Fix候補を表示する | missing id / name のMVPだけimplemented |
| G9 | field is Domain Sources or Domain Sources.ref | info | Domain Sources確認を促す | ref解決、type: domains、空のDomainsを確認する |
| G10 | field is Steps.domain | info | Business Flow Domain確認を促す | stepId / domainId をdetailsやreference候補に使う |
| G11 | Domain duplicate or field conflict message | warning | Domain定義の競合確認を促す | source間のid重複、name / kind / parent差分を確認する |
| G12 | local Domain override message | warning | local Domainsの上書き確認を促す | app_processまたはDFDのlocal Domainsが外部Domainを上書きする場合 |
| G13 | table-header diagnostic with known fileType and section | info | schema-driven expected headerを表示する | diagnostic-section-guidanceからCopy Expected Headerを導出する |
| G14 | Flow Diagram endpoint diagnostic | warning | Flow Diagram endpoint確認を促す | context.referenceKindがlocal-object-idのときfrom / toはObjects.idとして扱う |
| G15 | DFD or Flow Diagram Flows.data Wikilink diagnostic | warning | flow data参照確認を促す | plain text data labelではなく未解決Wikilinkだけを参照診断にする |
| G16 | missing frontmatter type | info | Quick Fix候補にしない | Copy Frontmatter Exampleなどの手動支援に留める |
| G17 | invalid table or unresolved reference diagnostic | warning | Quick Fix候補にしない | expected headerやreferenceのcopy guidanceを使う |
| G18 | diagnostics grouped by severity | info | bulk Markdown copy actionsを表示する | all diagnosticsは常にまとめ、errors / warnings / notesは対象がある場合だけ表示 |
| G19 | diagnostic has manual guidance pattern | info | 手動修復ガイダンスを表示する | frontmatter / table header / table row / render_mode / reference系のdetails |
| G20 | unsupported section has section guidance | info | セクション移動または別model化を促す | diagnostic-section-guidanceのmanualFixを表示する |
| G21 | invalid table row diagnostic | warning | 行内容を手動確認する | 空行、余分なセル、途中入力行を確認する |
| G22 | unresolved reference diagnostic | warning | 参照先を手動確認する | target ID、Wikilink、参照先file、indexed model setを確認する |

## Notes

- `VALIDATION_WARNING_CODES` は診断種別の有効値を定義するが、Viewerのcopy guidanceはcode単独ではなくmessageとcontextを併用して導出される。
- bulk Markdown copyは表示中のdiagnosticsを errors / warnings / notes に分けてMarkdown化する。notesは `info` severityのUI分類であり、保存値ではない。
- `invalid-table-column` と `invalid-table-row` はexpected header guidanceと関係しやすい。
- expected header guidanceは、現行実装ではtable header系診断に限定して表示される。
- manual-edit guidanceは、何が起きているか、よくある原因、手動での直し方を診断detailsに表示する。
- invalid table headerでは期待ヘッダーと現在のヘッダーを見比べる。Copy Expected Headerは修復支援であり、ヘッダー行を自動更新しない。
- invalid table rowでは空行、余分なセル、途中入力行を手動で確認する。
- unresolved referenceでは対象ID、Wikilink、参照先fileの存在、indexed model set内にあるかを確認する。
- missing frontmatterでは不足keyを戻す。missing id / name以外はQuick Fix MVPの対象外である。
- `unresolved-reference`、`duplicate-mapping-target-member`、`class-relation-target-not-diagram-compatible` はreference guidanceと関係しやすい。
- `missing-name`、`missing-kind`、`missing-schema`、`invalid-structure` はfrontmatterや必須fieldのmessageを持つ場合にfrontmatter example guidanceと関係する。
- Domain Sources、Domain Diagram、app_process `Steps.domain` の診断は、`field`、`context.stepId`、`context.domainId`、`context.rowIndex` を使って通常のmetadata / details / copy actionsへ載せる。
- Domain診断のcopy guidanceは参照値や期待箇所を示す補助であり、Domain Sourcesやlocal Domainsを自動修正するものではない。
- Domain Sourcesのmanual guidanceは、参照先fileが `type: domains` であること、Domain IDが解決できることを確認するための表示である。
- Flow Diagramの `Flows.from` / `Flows.to` はローカル `Objects.id` であり、外部モデル参照としてCopy Referenceする対象ではない。
- DFD / Flow Diagramの `Flows.data` はWikilinkの場合だけ未解決参照診断を出す。plain text labelを未解決扱いしない。
- Source Linksのavailable / missing / open failureはSource Links UIのstatusであり、ここではdiagnostic code guidanceとして扱わない。
- Quick Fix MVPは missing frontmatter id / name に限定してimplementedである。file basenameから安全な値を導出できる場合だけ候補を出す。
- missing type、invalid table header、invalid table row、unresolved reference、任意Markdown本文の自動修正はQuick Fix MVPの対象外でありfutureとして扱う。
- Copy Expected Header / Copy Frontmatter Exampleはcopy guidanceであり、Quick Fixとは別操作である。
- bulk Markdown copyはQuick Fixとは別のcopy action groupであり、Markdown本文を変更しない。

## Source Links

| path | notes |
|---|---|
| src/types/warnings.ts | VALIDATION_WARNING_CODES と VALIDATION_WARNING_SEVERITIES |
| src/views/modeling-preview-view.ts | renderDiagnosticCard とcopy actions |
| src/views/modeling-preview-view.ts | renderDiagnosticsBulkActions |
| src/views/modeling-preview-view.ts | formatDiagnosticsBulkMarkdown |
| src/views/modeling-preview-view.ts | getDiagnosticMetadata とgetDiagnosticDetailEntries |
| src/views/modeling-preview-view.ts | getDiagnosticGuidanceEntries |
| src/views/modeling-preview-view.ts | getDiagnosticReferenceValue |
| src/views/modeling-preview-view.ts | getExpectedHeaderForDiagnostic |
| src/views/modeling-preview-view.ts | getFrontmatterExampleForDiagnostic |
| src/views/modeling-preview-view.ts | formatDiagnosticAsMarkdown |
| src/views/modeling-preview-view.ts | getDiagnosticQuickFixActions |
| src/views/modeling-preview-view.ts | createFrontmatterQuickFixAction |
| src/views/modeling-preview-view.ts | getSafeFrontmatterQuickFixValues |
| src/views/modeling-preview-view.ts | applyFrontmatterQuickFix |
| src/core/diagnostic-section-guidance.ts | schema-driven expected header guidance |
| src/core/relation-resolver.ts | Flow Diagram local endpoint diagnostics and Flows.data Wikilink diagnostics |
| src/core/domain-diagnostics.ts | Domain diagnostics message helpers |
| src/core/domain-diagram-resolver.ts | Domain Sources diagnostics |
| src/core/app-process-domain-resolver.ts | app_process Steps.domain diagnostics |
