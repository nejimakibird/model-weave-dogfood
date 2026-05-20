---
type: mapping
id: MAP-MW-RENDERER-DIAGNOSTIC-TO-UI
name: Renderer Diagnostic to UI Mapping
kind: other
source: "[[DATA-MW-CORE-DIAGNOSTIC]]"
target: "[[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]]"
tags:
  - ModelWeave
  - Mapping
  - Diagnostic
---

# Renderer Diagnostic to UI Mapping

## Summary
内部的な診断情報オブジェクト `DATA-MW-CORE-DIAGNOSTIC` の各フィールドが、Viewerの診断パネル `SCR-MW-VIEWER-DIAGNOSTICS-PANEL` 上のどのUI要素（Field/Action）にマッピングされるかを定義する。

## Scope

| role   | ref                                           | notes                      |
| ------ | --------------------------------------------- | -------------------------- |
| source | [[DATA-MW-CORE-DIAGNOSTIC]]                   | 診断情報のデータ構造                 |
| target | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]]           | プレビュー下部の診断表示パネル            |
| rule   | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | 重大度のUI変換ルール                |
| rule   | [[RULE-MW-PATH-SHORTENER]]                    | diagnosticSourceFile の短縮表示 |
|        |                                               |                            |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[DATA-MW-CORE-DIAGNOSTIC]].severity | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticSeverity | severityに対応するアイコン/ラベルへ変換 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Y | error / warning / info / note |
| [[DATA-MW-CORE-DIAGNOSTIC]].code | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticCode | そのまま表示 |  | Y | 診断コード |
| [[DATA-MW-CORE-DIAGNOSTIC]].message | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticMessage | そのまま表示 |  | Y | ユーザー向け説明 |
| [[DATA-MW-CORE-DIAGNOSTIC]].filePath | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticSourceFile | RULE-MW-PATH-SHORTENER に従って表示用に短縮 | [[RULE-MW-PATH-SHORTENER]] | N | 完全パスはジャンプ処理用に保持 |
| [[DATA-MW-CORE-DIAGNOSTIC]].section | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticSection | そのまま表示 |  | N | 対象セクション |
| [[DATA-MW-CORE-DIAGNOSTIC]].row | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticRow | 数値表示 |  | N | 対象行番号 |
| [[DATA-MW-CORE-DIAGNOSTIC]].targetRef | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticTargetRef | そのまま表示 |  | N | 未解決参照等の対象 |

## Rules

- `jumpToSource` アクションは、選択中の診断行に紐づく `filePath` と `row` を使用して、エディタの該当箇所へフォーカスを移動させる。
- `filePath` は `diagnosticSourceFile` に表示され、`row` は `diagnosticRow` に表示される。
- `jumpToSource` は表示項目ではなくActionであるため、`Mappings` では個別のtarget_refとして重複定義しない。
- diagnosticSourceFile は [[RULE-MW-PATH-SHORTENER]] に従い、長い場合はファイル名を優先して短縮表示する。
- openDiagnosticLocation / jumpToSource では短縮表示ではなく、[[DATA-MW-CORE-DIAGNOSTIC]].filePath の完全パスを使用する。
- 短縮表示はUI上の見た目だけに適用し、診断箇所ジャンプや参照解決には影響させない。

## Notes
- プレビュー画面での診断リストの実装（`ModelingPreviewView.renderDiagnostics`）に準拠する。
- パス短縮の詳細方針は [[RULE-MW-PATH-SHORTENER]] に委ねる。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderDiagnostics | function | 診断情報の表示 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderDiagnosticSection | function | 診断セクションの描画 |
| source/model-weave-repo/src/main.ts | openDiagnosticLocation | function | 診断対象へのジャンプ |