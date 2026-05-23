---
type: rule
id: RULE-MW-VIEWER-STATUS-BAR-COLOR-POLICY
name: Viewerステータスバー色表示ルール
kind: display_rule
tags:
  - Viewer
  - UI
  - Status
  - Rule
---

# Viewerステータスバー色表示ルール

## Summary

Viewerステータスバーに表示される診断サマリー、描画状態、Viewport状態に応じた色・強調表示の方針を定義する。
本ルールはUI表示専用であり、Markdownモデルの内容は変更しない。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| statusBar | [[SCR-MW-VIEWER-STATUS-BAR]] | UI | Y | ステータスバー表示対象 |
| diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | Diagnostics | N | error / warning 件数の元情報 |
| viewerState | [[DATA-MW-VIEWER-STATE]] | Viewer | N | renderStatus の元情報 |
| viewportState | [[DATA-MW-VIEWPORT-STATE]] | Viewer | N | manual viewport 状態の元情報 |
| severityRule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Rule | Y | 診断severityの分類 |
| summaryMapping | [[MAP-MW-DIAGNOSTIC-TO-SUMMARY-TEXT]] | Mapping | N | 診断サマリーテキスト生成 |

## References

| ref | usage | notes |
|---|---|---|
| [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | severity_definition | severityの正規化定義 |
| [[MAP-MW-DIAGNOSTIC-TO-SUMMARY-TEXT]] | text_generation | サマリーテキストの生成規則 |

## Conditions

- error が1件以上ある場合、diagnosticsSummaryLabel を error 強調表示にする。
- warning が1件以上あり error がない場合、diagnosticsSummaryLabel を warning 強調表示にする。
- error / warning がなく info / note のみの場合、通常またはinfo表示にする。
- 診断が0件の場合、diagnosticsSummaryLabel は neutral / success 相当の控えめな表示にする。
- renderStatus が error の場合、renderStatusLabel を error 強調表示にする。
- renderStatus が unsupported または empty の場合、renderStatusLabel は info / muted 表示にする。
- manualViewportLabel が Manual Zoom Active または Restored Viewport の場合、viewport_status_area を info または accent 表示にしてよい。
- 色はObsidianテーマに追従し、固定色よりCSS変数やテーマトークンを優先する。
- 本ルールは表示のみを変更し、Markdown本文やfrontmatterは変更しない。

## Parameters

| name | value | notes |
|---|---|---|
| errorTone | error | error件数またはrender error時 |
| warningTone | warning | warning件数あり |
| infoTone | info | info/noteまたはmanual viewport |
| neutralTone | neutral | 正常または診断なし |
| mutedTone | muted | empty / unsupported など |

## Messages

| condition | message | severity | notes |
|---|---|---|---|
| status has errors | Status bar shows error state. | info | error件数あり |
| status has warnings | Status bar shows warning state. | info | warning件数あり |
| status is normal | Status bar shows neutral state. | info | 診断なし |

## Notes

- 実際の色値はCSS実装またはObsidianテーマに委ねる。
- 本ルールでは色名・tone・強調方針のみを定義する。
- 詳細な診断一覧は [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] に委ねる。
- diagnosticsSummaryLabel の文字列生成は [[MAP-MW-DIAGNOSTIC-TO-SUMMARY-TEXT]] に委ねる。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer状態に応じたUI更新 |
| src/views/modeling-preview-view.ts | renderDiagnostics | function | 診断情報の表示・集計元 |
| src/styles.css | mw-preview | css | Viewer表示スタイル |