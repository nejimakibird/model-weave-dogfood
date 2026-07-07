---
type: data_object
id: DATA-MW-DIAGNOSTIC-MANUAL-GUIDANCE
name: Diagnostic Manual Guidance
kind: ui_derived
data_format: object
tags:
  - ModelWeave
  - Diagnostic
  - Guidance
---

# Diagnostic Manual Guidance

## Summary

Diagnostics Panelの診断カードから派生する手動修復ガイダンスを表す。
これはMarkdown正本に保存されるデータではなく、`ValidationWarning` のmessage / context / section guidanceから、何が起きているか、よくある原因、手動での直し方を表示するための一時的なUI派生情報である。

Quick Fix MVPやMarkdown本文の自動修正とは別であり、invalid table header、invalid table row、unresolved reference、missing typeなどは手動確認とcopy支援に留める。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| diagnosticCode | Diagnostic code | string | | Y | code | [[DATA-MW-CORE-DIAGNOSTIC]].code | ガイダンス生成元の診断コード |
| section | Section | string | | N | section | [[DATA-MW-CORE-DIAGNOSTIC]].section | 対象セクション |
| field | Field | string | | N | field | [[DATA-MW-CORE-DIAGNOSTIC]].field | 対象fieldまたはfrontmatter key |
| whatHappened | What happened | string | | N | whatHappened | | 何が起きているか |
| commonCause | Common cause | string | | N | commonCause | | よくある原因 |
| manualFix | Manual fix | string | | Y | manualFix | | 手動での直し方 |
| relatedCopyAction | Related copy action | string | | N | relatedCopyAction | [[DATA-MW-DIAGNOSTIC-COPY-ACTION]] | Copy Expected Headerなどの併用可能なcopy支援 |
| notes | Notes | string | | N | notes | | Quick Fixや自動修正ではない |

## Notes

- manual guidanceは診断カードのdetailsとして表示される派生情報である。
- invalid table headerでは期待ヘッダーと現在のヘッダーを見比べ、ヘッダー行を手動で直す。
- invalid table rowでは空行、余分なセル、途中まで入力された行を確認する。
- missing frontmatterでは不足keyを手動で戻す。missing id / nameだけは別途Quick Fix MVP候補になり得る。
- unresolved referenceでは対象ID、Wikilink、参照先ファイルの存在を確認する。
- Domain Sources関連では参照先が `type: domains` であること、domain IDが解決できることを確認する。
- Copy Expected Header、Copy Frontmatter Example、Copy Referenceは手動修復を助けるcopy actionであり、Markdown本文を自動修正しない。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | getDiagnosticDetailEntries が診断detailsとmanual guidanceを組み立てる |
| src/views/modeling-preview-view.ts | renderDiagnosticCard が診断カードへdetailsとcopy actionsを表示する |
| src/core/diagnostic-section-guidance.ts | resolveDiagnosticSectionGuidance がsectionごとの期待ヘッダーとmanual fix情報を返す |
| src/core/diagnostic-section-guidance.ts | getExpectedHeaderForDiagnostic がCopy Expected Header対象を導出する |
| src/views/modeling-preview-view.ts | getFrontmatterExampleForDiagnostic がfrontmatter例を生成する |
