---
type: app_process
id: PROC-MW-DIAGNOSTIC-QUICK-FIX-APPLY
name: 診断Quick Fix適用
process_type: diagnostics
tags:
  - ModelWeave
  - Diagnostic
  - QuickFix
  - BusinessFlow
---

# 診断Quick Fix適用

## Summary

Viewerの診断カードで、missing frontmatter `id` / `name` に限定したQuick Fix MVPをユーザー操作で適用する処理。
Diagnostics表示だけでMarkdownを勝手に書き換えるものではなく、ユーザーがQuick Fix actionを実行した場合だけ対象ファイルのfrontmatterへ不足fieldを追加する。

missing `type`、invalid table header、invalid table row、unresolved reference、任意Markdown本文の自動修正は対象外である。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Diagnostics / Viewer / parser領域の境界 |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| diagnostic | [[DATA-MW-CORE-DIAGNOSTIC]] | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | Y | 診断カードの対象 |
| quickFixCandidate | [[DATA-MW-DIAGNOSTIC-QUICK-FIX]] | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | N | missing id / name の場合だけ生成される |
| currentFile | Markdown file | Viewer current file | Y | Quick Fix適用対象 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| updatedMarkdown | Markdown file | Obsidian vault | frontmatterへ不足fieldを追加した結果 |
| quickFixStatus | [[DATA-MW-DIAGNOSTIC-QUICK-FIX]].status | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | applied / skipped / failed |
| refreshedPreview | [[DATA-MW-VIEWER-STATE]] | [[SCR-MW-VIEWER-MAIN-FRAME]] | 保存後にPreview / Diagnosticsを再評価する |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | viewer_ui | 診断カードを表示する | start | diagnostic | | | | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | 診断カード上の操作候補を準備する |
| deriveCandidate | diagnostics | Quick Fix候補を導出する | process | diagnostic | quickFixCandidate | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | | | missing frontmatter id / name だけ候補化する |
| candidateAvailable | diagnostics | 候補が利用可能か判定する | decision | quickFixCandidate | | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | | | missing typeやtable診断は対象外 |
| waitUserAction | viewer_ui | ユーザー実行を待つ | wait | quickFixCandidate | | | | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | 表示だけではMarkdownを書き換えない |
| readCurrentFile | model_storage | 対象Markdownを再読込する | process | currentFile | Markdown content | | | | 適用直前の内容を読む |
| patchFrontmatter | parser_area | frontmatterへ不足fieldを追加する | process | Markdown content, quickFixCandidate | updatedMarkdown | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | | | 既存frontmatter値は上書きしない |
| saveFile | model_storage | Markdownを保存する | process | updatedMarkdown | quickFixStatus | | | | Obsidian vaultへ保存する |
| refreshPreview | preview_pipeline | Previewを再評価する | screen | quickFixStatus | refreshedPreview | | | [[SCR-MW-VIEWER-MAIN-FRAME]] | 保存後に診断状態を更新する |
| skipped | diagnostics | 対象外として終了する | end | diagnostic | quickFixStatus | | | | Quick Fix候補がない場合 |
| failed | diagnostics | 失敗として終了する | error | currentFile | quickFixStatus | | | | 安全に適用できない場合 |
| end | viewer_ui | Quick Fixを終了する | end | refreshedPreview | | | | | 再評価後の状態を表示する |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | deriveCandidate | | 候補導出 | 診断からQuick Fix候補を作る |
| deriveCandidate | candidateAvailable | | 判定 | 対象fieldとderived valueを確認する |
| candidateAvailable | waitUserAction | quickFixCandidate.status == available | 利用可能 | missing id / name かつ値を導出できる |
| candidateAvailable | skipped | | 対象外 | missing typeやtable診断など |
| waitUserAction | readCurrentFile | user clicks Quick Fix | 実行 | ユーザー操作でのみ進む |
| readCurrentFile | patchFrontmatter | | patch | frontmatterを更新する |
| patchFrontmatter | saveFile | patch succeeded | 保存 | 既存値を保ったまま追加する |
| patchFrontmatter | failed | | 失敗 | frontmatterが安全に更新できない |
| saveFile | refreshPreview | | 再評価 | 保存後にPreviewを更新する |
| refreshPreview | end | | 完了 | 診断表示を更新する |

## Notes

- この処理はQuick Fix MVPだけを扱う。Diagnostics全般の自動修正や任意Markdown本文修正ではない。
- `missingField === "type"` の場合は候補を返さない。
- `id` と `name` は、現在ファイルのbasenameから値を導出できる場合だけ候補化される。
- Copy Expected Header / Copy Frontmatter Exampleはclipboard支援であり、この処理を呼び出さない。
- table header診断、table row診断、unresolved reference診断は手動修復guidanceやcopy actionsの対象であり、Quick Fix MVPの対象ではない。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | getDiagnosticQuickFixActions が診断カード用Quick Fix候補を集める |
| src/views/modeling-preview-view.ts | createFrontmatterQuickFixAction が missing frontmatter id / name 用候補を生成する |
| src/views/modeling-preview-view.ts | getSafeFrontmatterQuickFixValues がbasenameから id / name を導出する |
| src/views/modeling-preview-view.ts | applyFrontmatterQuickFix がMarkdownを読み直して保存する |
| src/views/modeling-preview-view.ts | applyFrontmatterPatch が既存frontmatter値を上書きせず不足fieldを追加する |
