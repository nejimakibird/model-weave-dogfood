---
type: screen
id: SCR-MW-VIEWER-DIAGNOSTICS-PANEL
name: 診断パネル
screen_type: panel
kind: diagnostics_panel
tags:
  - Viewer
  - UI
  - Diagnostic
---

# 診断パネル

## Summary

Viewerの下部Review Panelに表示され、現在のファイルのバリデーション結果やエラー情報を一覧表示するパネル。
現行実装では Notes / Warnings / Errors に分類し、診断カードごとにメタ情報、詳細、開く操作、コピー支援、missing frontmatter id / name 限定のQuick Fix MVPを表示する。

## Layout

| id | label | kind | purpose | notes |
|---|---|---|---|---|
| diagnostic_panel_root | 診断パネルルート | container | パネル全体のコンテナ | |
| diagnostic_header_area | 診断ヘッダー領域 | container | 診断件数サマリーの配置 | |
| diagnostic_list_area | 診断リスト領域 | list | 個別診断項目のスクロールエリア | |
| diagnostic_action_area | 診断操作領域 | container | 診断カードごとのopen / copy操作 | |

## Fields

| id | label | kind | layout | data_type | required | ref | rule | notes |
|---|---|---|---|---|---|---|---|---|
| diagnosticsSummaryLabel | 診断サマリー | label | diagnostic_header_area | string | N | [[DATA-MW-CORE-DIAGNOSTIC]] | | [[MAP-MW-DIAGNOSTIC-TO-SUMMARY-TEXT]] に従う |
| closeButton | 閉じるボタン | button | diagnostic_header_area | action | N | | | planned。現行renderDiagnosticsには専用close buttonはない |
| diagnosticList | 診断リスト | list | diagnostic_list_area | object | Y | [[DATA-MW-CORE-DIAGNOSTIC]] | | 論理的には複数のDiagnosticを保持し、診断項目を反復表示する |
| diagnosticSeverity | 重大度 | icon | diagnostic_list_area | string | Y | | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | |
| diagnosticCode | 診断コード | label | diagnostic_list_area | string | Y | | | |
| diagnosticMessage | 診断メッセージ | text | diagnostic_list_area | string | Y | | | |
| diagnosticSourceFile | ソースファイル | link | diagnostic_list_area | string | N | | [[RULE-MW-PATH-SHORTENER]] | |
| diagnosticSection | セクション | label | diagnostic_list_area | string | N | | | |
| diagnosticRow | 行番号 | label | diagnostic_list_area | number | N | | | |
| diagnosticTargetRef | 参照対象 | label | diagnostic_list_area | string | N | | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | |
| diagnosticMetaList | メタ情報 | list | diagnostic_list_area | object | N | [[DATA-MW-CORE-DIAGNOSTIC]].context | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | path / section / field / lineなどを表示 |
| diagnosticDetailBox | 詳細情報 | panel | diagnostic_list_area | object | N | [[DATA-MW-CORE-DIAGNOSTIC]].context | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | expectedHeaderやreferenceなどの補足情報 |
| copyDiagnosticMessageButton | メッセージをコピー | button | diagnostic_action_area | action | N | [[DATA-MW-CORE-DIAGNOSTIC]].message | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | implemented。診断メッセージをclipboardへコピー |
| copyDiagnosticMarkdownButton | Markdownをコピー | button | diagnostic_action_area | action | N | [[DATA-MW-CORE-DIAGNOSTIC]] | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | implemented。診断をMarkdown形式でclipboardへコピー |
| copyDiagnosticReferenceButton | 参照をコピー | button | diagnostic_action_area | action | N | [[DATA-MW-CORE-DIAGNOSTIC]].context | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | implemented。referenceが取得できる場合だけ表示 |
| copyExpectedHeaderButton | 期待ヘッダーをコピー | button | diagnostic_action_area | action | N | [[DATA-MW-CORE-DIAGNOSTIC]].context | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | implemented。expectedHeaderが取得できる場合だけ表示 |
| copyFrontmatterExampleButton | frontmatter例をコピー | button | diagnostic_action_area | action | N | [[DATA-MW-CORE-DIAGNOSTIC]].field | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | implemented。missing frontmatter keyが推定できる場合だけ表示 |
| quickFixButton | Quick Fix Button | button | diagnostic_action_area | action | N | [[DATA-MW-DIAGNOSTIC-QUICK-FIX]] | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | implemented。missing frontmatter id / name の場合だけ表示 |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| jumpToSource | ソースへジャンプ | ui_action | diagnosticList | click | openDiagnosticLocation | | [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] | エディタの該当箇所へ移動 |
| copyDiagnosticMessage | 診断メッセージをコピー | ui_action | copyDiagnosticMessageButton | click | navigator.clipboard.writeText | | [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] | 診断メッセージだけをコピー |
| copyDiagnosticMarkdown | 診断Markdownをコピー | ui_action | copyDiagnosticMarkdownButton | click | navigator.clipboard.writeText | | [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] | code / severity / messageなどをMarkdown化してコピー |
| copyDiagnosticReference | 診断参照をコピー | ui_action | copyDiagnosticReferenceButton | click | navigator.clipboard.writeText | | [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] | unresolved referenceなどの対象値をコピー |
| copyExpectedHeader | 期待ヘッダーをコピー | ui_action | copyExpectedHeaderButton | click | navigator.clipboard.writeText | | [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] | table header診断の期待ヘッダーをコピー |
| copyFrontmatterExample | frontmatter例をコピー | ui_action | copyFrontmatterExampleButton | click | navigator.clipboard.writeText | | [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] | frontmatter不足診断の例をコピー |
| applyQuickFix | Quick Fixを適用 | ui_action | quickFixButton | click | [[PROC-MW-DIAGNOSTIC-QUICK-FIX-APPLY]] | | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] | missing id / name の不足fieldをユーザー操作で追加する |
| closePanel | パネルを閉じる | ui_action | closeButton | click | | | | planned。現行実装では専用close buttonは未確認 |

## Messages

| id | text | severity | timing | notes |
|---|---|---|---|---|
| diagnosticsSummaryEmpty | No diagnostics. | info | when_no_diagnostics | 診断がない場合のサマリー表示 |

## Notes

- diagnosticsSummaryLabel は [[MAP-MW-DIAGNOSTIC-TO-SUMMARY-TEXT]] に従い、severityごとの件数を短い文字列として表示する。
- 個別診断行の表示は diagnosticList / diagnosticMessage / diagnosticSourceFile などに委ねる。
- diagnosticSourceFile の表示短縮は [[RULE-MW-PATH-SHORTENER]] に従う。
- jumpToSource では短縮表示ではなく、診断情報が保持する完全な filePath と line / fromLine / toLine / section / field / context を使用する。
- metadata、details、copy actionsは [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] に従い、code単独ではなくmessage / context から導出される。
- Domain Sources、Domain Diagram、app_process `Steps.domain` の診断も、専用パネルではなく通常の診断カードとして表示する。
- Domain診断のcopy actionsは参照値や期待箇所のコピー支援であり、Domain定義やMarkdown本文を自動更新しない。
- Quick Fix MVPは missing frontmatter id / name に限定した実装済み操作であり、Copy Expected Header / Copy Frontmatter Exampleとは別グループで表示される。
- Quick Fix MVPはユーザーが実行した場合だけMarkdownを更新し、表示だけで勝手に修正しない。
- missing type、invalid table header、invalid table row、unresolved reference、任意Markdown本文の自動修正はQuick Fix MVPの対象外でありfutureとして扱う。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | symbol: renderDiagnostics; kind: function; 診断情報の表示 |
| src/views/modeling-preview-view.ts | symbol: renderDiagnosticSection; kind: function; 診断セクションの描画 |
| src/views/modeling-preview-view.ts | symbol: renderDiagnosticCard; kind: function; 診断カードとcopy/open actionsの描画 |
| src/views/modeling-preview-view.ts | symbol: getDiagnosticMetadata; kind: function; 診断メタ情報の抽出 |
| src/views/modeling-preview-view.ts | symbol: getDiagnosticDetailEntries; kind: function; 診断詳細情報の抽出 |
| src/views/modeling-preview-view.ts | symbol: getDiagnosticReferenceValue; kind: function; 参照値コピーの対象を抽出 |
| src/views/modeling-preview-view.ts | symbol: getExpectedHeaderForDiagnostic; kind: function; 期待ヘッダーコピーの対象を抽出 |
| src/views/modeling-preview-view.ts | symbol: getFrontmatterExampleForDiagnostic; kind: function; frontmatter例を生成 |
| src/views/modeling-preview-view.ts | symbol: formatDiagnosticAsMarkdown; kind: function; 診断Markdownコピー用文字列 |
| src/views/modeling-preview-view.ts | symbol: getDiagnosticQuickFixActions; kind: function; 診断カード用Quick Fix候補を抽出 |
| src/views/modeling-preview-view.ts | symbol: createFrontmatterQuickFixAction; kind: function; missing frontmatter id / name のQuick Fix候補を生成 |
| src/views/modeling-preview-view.ts | symbol: applyFrontmatterQuickFix; kind: function; Quick Fix実行時にMarkdownを更新 |
| src/main.ts | symbol: openDiagnosticLocation; kind: function; 診断対象へのジャンプ |
| src/core/domain-diagnostics.ts | symbol: formatAppProcessUnknownDomainMessage; kind: function; Business Flow Domain診断メッセージ |
| src/core/domain-diagram-resolver.ts | symbol: resolveDomainSources; kind: function; Domain Sources診断の生成 |
| src/core/app-process-domain-resolver.ts | symbol: resolveAppProcessDomainPlacement; kind: function; Steps.domain診断の生成 |
