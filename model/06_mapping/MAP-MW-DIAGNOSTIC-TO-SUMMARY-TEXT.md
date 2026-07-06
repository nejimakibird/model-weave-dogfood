---
type: mapping
id: MAP-MW-DIAGNOSTIC-TO-SUMMARY-TEXT
name: Diagnostic to Summary Text Mapping
kind: data_to_text
source: "[[DATA-MW-CORE-DIAGNOSTIC]]"
target: diagnosticsSummaryText
tags:
  - Viewer
  - Diagnostic
  - Mapping
---

# Diagnostic to Summary Text Mapping

## Summary

診断情報の集合から、ステータスバーや診断パネル見出しに表示する短いサマリー文字列を生成するためのマッピングを定義する。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-CORE-DIAGNOSTIC]] | 診断情報 |
| target | diagnosticsSummaryText | ステータスバー/診断パネル用の表示文字列 |
| rule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | severity正規化ルール |
| screen | [[SCR-MW-VIEWER-STATUS-BAR]] | diagnosticsSummaryLabel の表示先 |
| screen | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | 診断一覧の表示先 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| severity | errorCount | severity=error の件数を集計 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | error件数 |
| severity | warningCount | severity=warning の件数を集計 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | warning件数 |
| severity | infoCount | severity=info の件数を集計 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | UIではNotesとして表示 |
| severity | diagnosticsSummaryText | 件数を優先度順に結合して短い文字列を生成 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Y | 例: 2 Errors / 3 Warnings |

## Rules

- error が1件以上ある場合は、error件数を先頭に表示する。
- warning が1件以上ある場合は、warning件数を error の後に表示する。
- info は通常は省略してよいが、error / warning がない場合にNotesとして表示してよい。
- 診断が0件の場合は "No diagnostics" と表示する。
- 表示順は error > warning > info とする。
- severity は [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] によって正規化済みとして扱う。
- ステータスバーでは短く表示し、詳細は [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] に委ねる。

## Examples

| input | output | notes |
|---|---|---|
| 0 diagnostics | No diagnostics | 正常状態 |
| 2 error, 1 warning | 2 Errors / 1 Warning | error優先 |
| 0 error, 3 warning | 3 Warnings | warningのみ |
| 2 info | 2 Info | error/warningなしの場合 |

## Notes

- 本mappingは表示文字列生成の方針を定義する。
- 個別診断の表示は [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] に委ねる。
- ステータスバーの diagnosticsSummaryLabel から参照される想定。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | symbol: renderDiagnostics; kind: function; 診断情報表示の起点 |
| src/core/current-file-diagnostics.ts | symbol: normalizeDiagnosticSeverity; kind: function; severity正規化 |
