---
type: data_object
id: DATA-MW-DIAGNOSTIC-QUICK-FIX
name: Diagnostic Quick Fix Candidate
kind: ui_action
data_format: object
tags:
  - ModelWeave
  - Diagnostic
  - QuickFix
---

# Diagnostic Quick Fix Candidate

## Summary

診断カードから派生するQuick Fix候補を表す。
これはMarkdown正本に保存されるデータではなく、Viewer上でユーザーが明示的に実行できる一時的なUI actionである。

現行MVPでは missing frontmatter `id` / `name` だけを対象にし、missing `type`、invalid table header、invalid table row、unresolved reference、任意Markdown本文の自動修正は対象外である。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| diagnosticCode | Diagnostic code | string | | Y | code | [[DATA-MW-CORE-DIAGNOSTIC]].code | 候補生成元の診断コード |
| targetField | Target field | string | | Y | field | [[DATA-MW-CORE-DIAGNOSTIC]].field | frontmatter id または name |
| derivedValue | Derived value | string | | N | derivedValue | | ファイルbasenameから安全に導出できる場合だけ保持 |
| actionLabel | Action label | string | | Y | actionLabel | | Viewerに表示するQuick Fix操作名 |
| status | Status | string | | Y | status | | available / unavailable / applied / skipped / failed |
| notes | Notes | string | | N | notes | | missing typeやtable診断は対象外など |

## Notes

- Quick Fix候補は `ValidationWarning` のmessage / contextから導出される。
- `targetField` が `type` の場合は候補を作らない。
- `derivedValue` は現在ファイルのbasenameから導出される。basenameが空の場合は候補を作らない。
- 適用時はMarkdownを再読込し、既存frontmatter値を上書きしない形で不足fieldを追加する。
- Copy Expected HeaderやCopy Frontmatter Exampleはコピー支援であり、このQuick Fix候補とは別のUI actionである。
- 汎用Markdown本文自動修正や複雑なQuick Fixはfutureとして扱う。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | getDiagnosticQuickFixActions が診断カード用Quick Fix候補を集める |
| src/views/modeling-preview-view.ts | createFrontmatterQuickFixAction が missing frontmatter id / name 用候補を生成する |
| src/views/modeling-preview-view.ts | getSafeFrontmatterQuickFixValues がbasename由来の安全な値を作る |
| src/views/modeling-preview-view.ts | applyFrontmatterQuickFix がユーザー操作後にMarkdownへ適用する |
