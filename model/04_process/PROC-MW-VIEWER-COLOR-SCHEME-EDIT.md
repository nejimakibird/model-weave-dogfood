---
type: app_process
id: PROC-MW-VIEWER-COLOR-SCHEME-EDIT
name: Color Schemeプレビュー編集
process_type: viewer_action
tags:
  - ModelWeave
  - ColorScheme
  - Viewer
  - BusinessFlow
---

# Color Schemeプレビュー編集

## Summary

Viewerの `color_scheme` 表示で、`## Colors` テーブルの `fill` / `stroke` / `text` セルを色入力から更新する操作。
現行実装では `updateColorSchemeColorCell` がhex colorを検証し、対象セルだけをMarkdownに反映する。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| markdown | [[DATA-MW-CORE-VAULT-FILE]] | Viewer | Y | color_scheme Markdown本文 |
| colorCell | [[DATA-MW-VIEWER-STATE]] | [[SCR-MW-VIEWER-MAIN-FRAME]] | Y | rowIndex / columnName / value |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| updatedMarkdown | [[DATA-MW-CORE-VAULT-FILE]] | Obsidian editor | changedの場合だけ更新されるMarkdown |
| editStatus | [[DATA-MW-CORE-DIAGNOSTIC]] | Viewer notice | invalid-colorなどのstatus |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | viewer_ui | 色セル編集を開始する | start | colorCell | | | | [[SCR-MW-VIEWER-MAIN-FRAME]] | color input change |
| normalizeColor | renderer_area | hex colorを正規化する | process | colorCell | | | | | `#rgb` は `#rrggbb` へ展開 |
| validateColor | renderer_area | 色値を検証する | decision | colorCell | editStatus | | | | `#rrggbb` だけを更新対象にする |
| locateCell | renderer_area | Colors tableセルを探す | process | markdown, colorCell | | | | | section / header / row / columnを確認 |
| updateMarkdown | viewer_ui | Markdownを更新する | process | markdown, colorCell | updatedMarkdown | | | | 対象セルのみ置換 |
| showStatus | viewer_ui | 結果を通知する | message | editStatus | | | | [[SCR-MW-VIEWER-MAIN-FRAME]] | invalid-colorや変更なしを扱う |
| end | viewer_ui | 編集処理を終了する | end | updatedMarkdown | | | | | |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | normalizeColor | | 開始 | |
| normalizeColor | validateColor | | 検証 | |
| validateColor | locateCell | `status === "updated"` | 有効 | |
| validateColor | showStatus | `status !== "updated"` | 更新不可 | invalid-color / table-missingなど |
| locateCell | updateMarkdown | | セル更新 | |
| updateMarkdown | end | | 完了 | |
| showStatus | end | | 終了 | |

## Notes

- この処理はColor Scheme編集専用であり、Diagnostics Quick FixやMarkdown本文の汎用自動修正ではない。
- 対象列は `fill`、`stroke`、`text` に限定される。
- 対象sectionは `## Colors` で、期待headerは `target / kind / fill / stroke / text / notes` である。

## Source Links

| path | notes |
|---|---|
| src/core/color-scheme-table-editor.ts | Steps: normalizeColor, validateColor, locateCell, updateMarkdown. updateColorSchemeColorCell |
| src/views/modeling-preview-view.ts | Steps: start, showStatus, end. color_scheme viewから編集処理を呼び出す |
