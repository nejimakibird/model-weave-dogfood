---
type: data_object
id: DATA-MW-DIAGNOSTIC-COPY-ACTION
name: Diagnostic Copy Action
kind: ui_action
data_format: object
tags:
  - ModelWeave
  - Diagnostic
  - Clipboard
---

# Diagnostic Copy Action

## Summary

Diagnostics Panelから派生するcopy actionを表す。
これはMarkdown正本に保存されるデータではなく、表示中の診断をMarkdown文字列へ整形してClipboardへ渡すための一時的なUI actionである。

bulk copyは all diagnostics / errors / warnings / notes を対象にする。
`notes` はViewer上の分類名であり、`ValidationWarning.severity` の保存値は `info` である。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| scope | Copy scope | string | | Y | scope | | all / errors / warnings / notes / single |
| sourceDiagnostics | Source diagnostics | object | | Y | diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | 論理的には複数値 |
| markdownText | Markdown text | string | | Y | markdownText | | Clipboardへ渡す整形済みMarkdown |
| actionLabel | Action label | string | | Y | actionLabel | | UIに表示するcopy action名 |
| enabled | Enabled | boolean | | Y | enabled | | 対象diagnosticsがある場合に有効 |
| notes | Notes | string | | N | notes | | Quick Fixとは別操作 |

## Notes

- all diagnostics は errors / warnings / notes をまとめてMarkdown化する。
- errors、warnings、notes は対象カテゴリに診断がある場合だけ表示される。
- notes は `info` severityをViewer上で表示する分類名であり、保存値としての `note` severityは使わない。
- bulk copyはClipboardへ文字列を渡すだけで、Markdown本文を変更しない。
- 個別診断カードのCopy Message / Copy Markdown / Copy Reference / Copy Expected Header / Copy Frontmatter Exampleとは別のaction groupである。
- Quick Fix MVPや任意Markdown本文修正とは別操作である。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | renderDiagnosticsBulkActions がbulk copy actionを組み立てる |
| src/views/modeling-preview-view.ts | renderDiagnosticsBulkCopyButton がClipboardへMarkdownを渡すbuttonを作る |
| src/views/modeling-preview-view.ts | formatDiagnosticsBulkMarkdown が診断一覧をMarkdown文字列に整形する |
| src/views/modeling-preview-view.ts | normalizeDiagnosticSeverityForViewer が info / warning / error をUI分類へ寄せる |
