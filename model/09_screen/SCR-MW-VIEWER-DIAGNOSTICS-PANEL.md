---
type: screen
id: SCR-MW-VIEWER-DIAGNOSTICS-PANEL
name: 診断パネル
screen_type: panel
tags:
  - Viewer
  - UI
  - Diagnostic
---

# 診断パネル

## Summary

Viewerの下部に表示され、現在のファイルのバリデーション結果やエラー情報を一覧表示するパネル。

## Layout

| id | label | kind | purpose | notes |
|---|---|---|---|---|
| diagnostic_panel_root | 診断パネルルート | container | パネル全体のコンテナ | |
| diagnostic_header_area | 診断ヘッダー領域 | container | 診断件数サマリーと操作ボタンの配置 | |
| diagnostic_list_area | 診断リスト領域 | list | 個別診断項目のスクロールエリア | |

## Fields

| id | label | kind | layout | data_type | required | ref | rule | notes |
|---|---|---|---|---|---|---|---|---|
| diagnosticsSummaryLabel | 診断サマリー | label | diagnostic_header_area | string | N | [[DATA-MW-CORE-DIAGNOSTIC]] | | [[MAP-MW-DIAGNOSTIC-TO-SUMMARY-TEXT]] に従う |
| closeButton | 閉じるボタン | button | diagnostic_header_area | action | N | | | 診断パネルを閉じる |
| diagnosticList | 診断リスト | list | diagnostic_list_area | Array<DATA-MW-CORE-DIAGNOSTIC> | Y | [[DATA-MW-CORE-DIAGNOSTIC]] | | 診断項目の反復表示 |
| diagnosticSeverity | 重大度 | icon | diagnostic_list_area | string | Y | | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | |
| diagnosticCode | 診断コード | label | diagnostic_list_area | string | Y | | | |
| diagnosticMessage | 診断メッセージ | text | diagnostic_list_area | string | Y | | | |
| diagnosticSourceFile | ソースファイル | link | diagnostic_list_area | string | N | | [[RULE-MW-PATH-SHORTENER]] | |
| diagnosticSection | セクション | label | diagnostic_list_area | string | N | | | |
| diagnosticRow | 行番号 | label | diagnostic_list_area | number | N | | | |
| diagnosticTargetRef | 参照対象 | label | diagnostic_list_area | string | N | | | |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| jumpToSource | ソースへジャンプ | ui_action | diagnosticList | click | openDiagnosticLocation | | [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] | エディタの該当箇所へ移動 |
| closePanel | パネルを閉じる | ui_action | closeButton | click | | | | パネルを非表示にする |

## Messages

| id | text | severity | timing | notes |
|---|---|---|---|---|
| diagnosticsSummaryEmpty | No diagnostics. | info | when_no_diagnostics | 診断がない場合のサマリー表示 |

## Notes

- diagnosticsSummaryLabel は [[MAP-MW-DIAGNOSTIC-TO-SUMMARY-TEXT]] に従い、severityごとの件数を短い文字列として表示する。
- 個別診断行の表示は diagnosticList / diagnosticMessage / diagnosticSourceFile などに委ねる。
- diagnosticSourceFile の表示短縮は [[RULE-MW-PATH-SHORTENER]] に従う。
- jumpToSource では短縮表示ではなく、診断情報が保持する完全な filePath と row を使用する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/views/modeling-preview-view.ts | renderDiagnostics | function | 診断情報の表示 |
| src/views/modeling-preview-view.ts | renderDiagnosticSection | function | 診断セクションの描画 |
| src/main.ts | openDiagnosticLocation | function | 診断対象へのジャンプ |