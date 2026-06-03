---
type: dfd_object
id: DFD-MW-OBJ-IMPACT-ANALYZER
name: Impact Analyzer
kind: process
tags:
  - DFD
  - Impact
  - Core
---

# Impact Analyzer

## Summary

現在のモデルとVault Indexから、参照元、参照先、未解決参照、関連 Source Links を集約する関係/影響分析境界。
buildImpactSummary が ImpactSummary を生成し、formatImpactSummaryAsMarkdown がViewer表示用Markdownへ整形する。

## Details

| key | value | notes |
|---|---|---|
| owner | Core Impact Analyzer | impact-analyzer.ts |
| input | [[DATA-MW-CORE-PARSED-MODEL]] | 対象モデル |
| index | [[DATA-MW-VAULT-MODEL-INDEX]] | 関連モデル探索 |
| output | [[DATA-MW-IMPACT-SUMMARY]] | ImpactSummary |
| source_links | collectRelatedSourceLinks | self / inbound / outbound集約 |
| markdown | formatImpactSummaryAsMarkdown | Viewer表示用Markdown |

## Source Links

| path | notes |
|---|---|
| src/core/impact-analyzer.ts | buildImpactSummary / collectRelatedSourceLinks |
