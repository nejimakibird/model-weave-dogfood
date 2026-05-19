---
type: mapping
id: MAP-MW-VIEWER-STATE-TO-STATUS-BAR-UI
name: Viewer State to Status Bar UI Mapping
kind: data_to_screen
source: "[[DATA-MW-VIEWER-STATE]]"
target: "[[SCR-MW-VIEWER-STATUS-BAR]]"
tags:
  - Viewer
  - UI
  - Mapping
---

# Viewer State to Status Bar UI Mapping

## Summary

`[[DATA-MW-VIEWER-STATE]]`、`[[DATA-MW-VIEWPORT-STATE]]`、および `[[DATA-MW-CORE-DIAGNOSTIC]]` の情報を、`[[SCR-MW-VIEWER-STATUS-BAR]]` の各表示ラベルへ反映するための項目対応と変換ルールを定義します。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-VIEWER-STATE]] | Viewer全体の状態 |
| source | [[DATA-MW-VIEWPORT-STATE]] | Viewport表示状態 |
| source | [[DATA-MW-CORE-DIAGNOSTIC]] | 診断情報 |
| target | [[SCR-MW-VIEWER-STATUS-BAR]] | ステータスバーUI |
| rule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | 診断severity表示 |
| rule | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | 手動viewport状態の表示 |
| rule | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | Auto Fit状態の表示 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| scale | zoomPercentLabel | scaleを百分率文字列へ変換 | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | N | 例: 1.25 -> 125% |
| lastCommandType | manualViewportLabel | 手動操作由来なら Manual Zoom Active を表示 | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | N | pan / wheel_zoom 由来時 |
| renderStatus | autoFitStatusLabel | Auto Fit / Preserved Viewport / Error などに変換 | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | N | |
| diagnostics | diagnosticsSummaryLabel | error / warning 件数を集計して表示 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 例: 2 Errors / 3 Warnings |
| activeFilePath | currentFilePathLabel | Vault相対パスまたはファイル名へ短縮表示 | | N | |
| renderStatus | renderStatusLabel | ready / error / empty / unsupported を表示 | | N | |

## Rules

- **zoomPercentLabel**: `[[DATA-MW-VIEWPORT-STATE]]`.scale を 100倍し、整数または小数1桁に丸めて `%` を付与する。
- **manualViewportLabel**: ユーザー操作により viewportState が保存・復元されている場合に "Manual Zoom Active" または "Restored Viewport" を表示する。
- **autoFitStatusLabel**: Auto Fitが適用された場合 "Auto Fit Applied"、手動状態維持でスキップされた場合 "Preserved Viewport" を表示する。
- **diagnosticsSummaryLabel**: `[[DATA-MW-CORE-DIAGNOSTIC]]` の集合から severity ごとの件数を集計し、"N Errors / M Warnings" 形式で表示する。
- **currentFilePathLabel**: パスが長い場合、末尾のファイル名を優先して表示し、ディレクトリ階層を省略表示する。
- **renderStatusLabel**: Viewerの renderStatus を短い識別子（ready, error等）としてそのまま表示する。
- ステータスバーへの表示反映はViewerの一時的な表示状態であり、Markdown本文やフロントマターの内容は変更しない。

## Notes

- `diagnostics` は `[[DATA-MW-VIEWER-STATE]]` 内に保持される `[[DATA-MW-CORE-DIAGNOSTIC]]` のリストを指す。
- 詳細な診断一覧の表示については `[[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]]` が責務を持つ。
- 非対応ファイル表示時、ステータスバーの一部項目を空または非活性（disabled）にすることを許容する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer状態に応じたUI更新 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | prepareFileViewportState | method | viewport状態復元/初期化 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderDiagnostics | function | 診断情報の表示・集計元 |