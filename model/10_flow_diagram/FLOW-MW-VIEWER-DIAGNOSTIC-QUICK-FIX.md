---
type: flow_diagram
id: FLOW-MW-VIEWER-DIAGNOSTIC-QUICK-FIX
name: Viewer Diagnostic Quick Fix Flow
kind: screen_communication
tags:
  - ModelWeave
  - FlowDiagram
  - Diagnostic
  - Viewer
---

# Viewer Diagnostic Quick Fix Flow

## Summary

Diagnostics Panelで missing frontmatter `id` / `name` 向けQuick Fix MVPを実行し、対象Markdownのfrontmatter patch、保存、Preview再評価へつなげる画面・処理間フローです。

この図は `type: flow_diagram` の代表Dogfoodサンプルであり、DFDではありません。Flow Diagram MVPでは、画面、UI action、処理境界、Markdownファイルの間で、trigger / data / condition がどのように流れるかを軽量に表現します。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Viewer、diagnostics、parser、vault領域をこのflowで使用する |

## Objects

| id | label | kind | ref | domain | notes |
|---|---|---|---|---|---|
| user | User | external |  | viewer_ui | Quick Fixを実行する利用者 |
| viewerMain | Viewer Main Frame | screen | [[SCR-MW-VIEWER-MAIN-FRAME]] | viewer_ui | Preview全体のViewer UI |
| diagnosticsPanel | Diagnostics Panel | screen | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | viewer_ui | 診断カード、copy actions、Quick Fix actionを表示する |
| quickFixCandidate | Quick Fix Candidate | work_object | [[DATA-MW-DIAGNOSTIC-QUICK-FIX]] | diagnostics | missing frontmatter id / name から派生する一時候補 |
| quickFixApply | Apply Quick Fix | process | [[PROC-MW-DIAGNOSTIC-QUICK-FIX-APPLY]] | diagnostics | ユーザー操作によりfrontmatter patchを適用する処理 |
| markdownFile | Target Markdown File | store | [[DATA-MW-CORE-VAULT-FILE]] | vault_content | frontmatterを持つ対象Markdown |
| parsedModel | Parsed Model | work_object | [[DATA-MW-CORE-PARSED-MODEL]] | parser_resolver | 保存後に再解析されるモデル |
| diagnostics | Diagnostics | work_object | [[DATA-MW-CORE-DIAGNOSTIC]] | diagnostics | 再評価後のValidationWarning群 |

## Flows

| id | from | to | kind | trigger | data | condition | notes |
|---|---|---|---|---|---|---|---|
| F01 | user | diagnosticsPanel | click | User opens diagnostic action | selected diagnostic card | diagnostic has Quick Fix candidate | ユーザーが診断カード内のQuick Fix操作を選ぶ |
| F02 | diagnosticsPanel | quickFixCandidate | derive | Render diagnostic actions | [[DATA-MW-DIAGNOSTIC-QUICK-FIX]] | missing frontmatter id or name | Quick Fix候補は診断から派生するUI actionでありMarkdown正本ではない |
| F03 | quickFixCandidate | quickFixApply | invoke | Quick Fix clicked | frontmatter patch request | candidate is available | missing typeやtable修正は対象外 |
| F04 | quickFixApply | markdownFile | update | Apply frontmatter patch | id / name patch | existing frontmatter value is not overwritten | 既存値を保持し、安全な場合だけ値を追加する |
| F05 | markdownFile | parsedModel | parse | File saved | Markdown content | after patch applied | 保存後に対象Markdownを再解析する |
| F06 | parsedModel | diagnostics | validate | Rebuild diagnostics | validation result | after parse | missing id / name の診断が再評価される |
| F07 | diagnostics | viewerMain | refresh | Update preview state | refreshed diagnostics | after validation | Viewerへ再評価結果を渡す |
| F08 | viewerMain | diagnosticsPanel | render | Render Diagnostics tab | diagnostic cards | updated diagnostics | Diagnostics Panelに更新結果を表示する |

## Notes

- `Flows.from` / `Flows.to` はこのFlow Diagram内の `Objects.id` を参照する。
- `Flows.data` がWikilinkの場合は参照診断対象になる。
- `Flows.data` がplain textの場合は表示ラベルとして扱う。
- Quick Fix MVPは missing frontmatter `id` / `name` に限定する。
- missing `type`、invalid table header、invalid table row、unresolved reference、任意Markdown本文の自動修正はfuture扱いであり、このFlow Diagramではimplementedとして扱わない。
- Copy Expected Header / Copy Frontmatter Example / bulk Markdown copy / manual-edit guidance はDiagnostics UIの支援機能だが、Quick Fix適用処理とは別操作である。
- この図はFlow Diagramの代表サンプルであり、DFDやSource Links Explorerの機能ではない。
- PNG Exportは描画結果の派生出力であり、Markdown正本を変更しない。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | createFrontmatterQuickFixAction / applyFrontmatterQuickFix / applyFrontmatterPatch |
| src/core/current-file-diagnostics.ts | missing frontmatter id / name diagnostics |
| src/parsers/frontmatter-parser.ts | frontmatter parse warning structure |
| docs/releases/v0.1.18.md | Quick Fix MVP release note |
