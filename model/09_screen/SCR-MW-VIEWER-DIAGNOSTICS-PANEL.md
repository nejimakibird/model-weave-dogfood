---
type: screen
id: SCR-MW-VIEWER-DIAGNOSTICS-PANEL
name: Viewer診断パネル
screen_type: other
tags:
  - ModelWeave
  - Screen
  - Viewer
  - Diagnostic
---

# Viewer診断パネル

## Summary
プレビュー画面の下部に表示される、モデルの診断情報（エラー・警告・情報）の一覧表示パネル。
フィルタ機能により、特定の深刻度やコードに絞り込んで確認することができる。

## Layout

| id | label | kind | purpose | notes |
|---|---|---|---|---|
| panel_root | 診断パネルルート | container | パネル全体のコンテナ | |
| filter_bar | フィルタバー | toolbar | 深刻度やコードによる絞り込み操作領域 | |
| diagnostic_list_area | 診断一覧領域 | content-pane | 診断情報のリスト表示領域 | |

## Fields

| id | label | kind | layout | data_type | required | ref | rule | notes |
|---|---|---|---|---|---|---|---|---|
| severityFilter | 深刻度フィルタ | select | filter_bar | string | N | | | error / warning / info / note でフィルタ |
| codeFilter | コードフィルタ | input | filter_bar | string | N | | | 診断コードでフィルタ |
| applyFilterButton | フィルタ適用 | button | filter_bar |  | N | | | フィルタ条件を適用 |
| clearFilterButton | フィルタクリア | button | filter_bar |  | N | | | フィルタ条件をリセット |
| diagnosticList | 診断一覧 | list | diagnostic_list_area | Array&lt;DATA-MW-CORE-DIAGNOSTIC&gt; | Y | [[DATA-MW-CORE-DIAGNOSTIC]] | | 診断情報のリスト表示 |
| diagnosticSeverity | 深刻度アイコン | label | diagnostic_list_area | string | Y | | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | severityに応じたアイコン/ラベル |
| diagnosticCode | 診断コード | label | diagnostic_list_area | string | Y | | | 例: invalid-table-column |
| diagnosticMessage | メッセージ | label | diagnostic_list_area | string | Y | | | ユーザー向けの説明文 |
| diagnosticSourceFile | 対象ファイル | label | diagnostic_list_area | string | N | | | 問題が検出されたファイルのパス |
| diagnosticSection | 対象セクション | label | diagnostic_list_area | string | N | | | 問題が検出されたセクション名 |
| diagnosticRow | 対象行番号 | label | diagnostic_list_area | number | N | | | 問題が検出された行番号 |
| diagnosticTargetRef | 参照ターゲット | label | diagnostic_list_area | string | N | | | 未解決参照等の対象識別子 |
| jumpToSourceButton | ソースへジャンプ | button | diagnostic_list_area |  | N | | | 診断行クリックで該当箇所へ移動 |
| emptyStateMessage | 空状態メッセージ | label | diagnostic_list_area | string | Y | | | 診断がない場合の表示 |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| filterBySeverity | 深刻度フィルタ変更 | field_event | severityFilter | change | | | | 深刻度条件を変更してリスト更新 |
| filterByCode | コードフィルタ変更 | field_event | codeFilter | change | | | | 診断コード条件を変更してリスト更新 |
| applyFilter | フィルタ適用 | ui_action | applyFilterButton | click | | | | フィルタ条件を適用してリスト更新 |
| selectDiagnostic | 診断選択 | ui_action | diagnosticList | select | | | | 選択された診断の詳細表示 |
| jumpToSource | ソースへジャンプ | ui_action | jumpToSourceButton | click | | | | 選択された診断のファイル・行へ移動 |
| clearFilter | フィルタクリア | ui_action | clearFilterButton | click | | | | フィルタ条件をリセット |

## Notes
- 深刻度アイコンは `RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP` に基づき、テーマに合わせた色とアイコンで表示される。
- 診断行をクリック（`jumpToSourceButton`）することで、エディタ内の該当ファイル・該当行へ即座にジャンプする。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderDiagnostics | function | 診断情報の表示 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderDiagnosticSection | function | 診断セクションの描画 |
| source/model-weave-repo/src/main.ts | openDiagnosticLocation | function | 診断対象へのジャンプ |