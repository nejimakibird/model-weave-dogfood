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
| rule   | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]]    | details / copy actions の導出ルール |
| rule   | [[RULE-MW-PATH-SHORTENER]]                    | diagnosticSourceFile の短縮表示 |
| output | [[DATA-MW-DIAGNOSTIC-QUICK-FIX]]              | missing id / name 限定のQuick Fix候補 |
|        |                                               |                            |

## Mappings

| source_ref                            | target_ref                                               | transform                         | rule                                          | required | notes                         |
| ------------------------------------- | -------------------------------------------------------- | --------------------------------- | --------------------------------------------- | -------- | ----------------------------- |
| [[DATA-MW-CORE-DIAGNOSTIC]].severity  | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticSeverity   | severityに対応するアイコン/ラベルへ変換          | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Y        | error / warning / info |
| [[DATA-MW-CORE-DIAGNOSTIC]].code      | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticCode       | そのまま表示                            |                                               | Y        | 診断コード                         |
| [[DATA-MW-CORE-DIAGNOSTIC]].message   | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticMessage    | そのまま表示                            |                                               | Y        | ユーザー向け説明                      |
| [[DATA-MW-CORE-DIAGNOSTIC]].filePath  | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticSourceFile | RULE-MW-PATH-SHORTENER に従って表示用に短縮 | [[RULE-MW-PATH-SHORTENER]]                    | N        | 完全パスはジャンプ処理用に保持               |
| [[DATA-MW-CORE-DIAGNOSTIC]].section   | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticSection    | そのまま表示                            |                                               | N        | 対象セクション                       |
| [[DATA-MW-CORE-DIAGNOSTIC]].line      | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticRow        | 数値表示                              |                                               | N        | 対象行番号                         |
| [[DATA-MW-CORE-DIAGNOSTIC]].context   | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticTargetRef  | context内の関連情報を表示                     | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]]     | N        | 未解決参照等の補足情報                  |
| [[DATA-MW-CORE-DIAGNOSTIC]].context   | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticMetaList   | path / section / field / lineなどを抽出して表示 | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]]     | N        | getDiagnosticMetadata相当           |
| [[DATA-MW-CORE-DIAGNOSTIC]].context   | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].diagnosticDetailBox  | referenceやexpectedHeaderなどを抽出して表示       | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]]     | N        | getDiagnosticDetailEntries相当      |
| [[DATA-MW-CORE-DIAGNOSTIC]].message   | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].copyDiagnosticMessageButton | clipboardへコピーする文字列を作る             | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]]     | N        | Copy Message                       |
| [[DATA-MW-CORE-DIAGNOSTIC]]           | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].copyDiagnosticMarkdownButton | formatDiagnosticAsMarkdownでMarkdown化      | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]]     | N        | Copy Markdown                      |
| [[DATA-MW-CORE-DIAGNOSTIC]].context   | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].copyDiagnosticReferenceButton | reference値がある場合だけ表示                 | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]]     | N        | Copy Reference                     |
| [[DATA-MW-CORE-DIAGNOSTIC]].context   | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].copyExpectedHeaderButton | expectedHeaderがある場合だけ表示              | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]]     | N        | Copy Expected Header               |
| [[DATA-MW-CORE-DIAGNOSTIC]].field     | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].copyFrontmatterExampleButton | missing frontmatter keyから例を生成          | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]]     | N        | Copy Frontmatter Example           |
| [[DATA-MW-CORE-DIAGNOSTIC]]           | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]].quickFixButton | [[DATA-MW-DIAGNOSTIC-QUICK-FIX]] を生成       | [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]]     | N        | missing frontmatter id / name のみ |

## Rules

- `jumpToSource` アクションは、選択中の診断行に紐づく `filePath` と `line` を使用して、エディタの該当箇所へフォーカスを移動させる。
- `line` がない場合、openDiagnosticLocation は `fromLine`、`toLine`、section / field / context 由来の位置を順に利用する。
- `filePath` は `diagnosticSourceFile` に表示され、`line` は `diagnosticRow` に表示される。
- `jumpToSource` は表示項目ではなくActionであるため、`Mappings` では個別のtarget_refとして重複定義しない。
- diagnosticSourceFile は [[RULE-MW-PATH-SHORTENER]] に従い、長い場合はファイル名を優先して短縮表示する。
- openDiagnosticLocation / jumpToSource では短縮表示ではなく、[[DATA-MW-CORE-DIAGNOSTIC]].filePath の完全パスを使用する。
- 短縮表示はUI上の見た目だけに適用し、診断箇所ジャンプや参照解決には影響させない。
- 診断カードのcopy actionsはclipboardへ文字列を渡すだけで、Markdown本文を自動修正しない。
- Quick Fix MVPは missing frontmatter id / name に限定してimplementedとして扱う。ユーザー操作時だけ [[PROC-MW-DIAGNOSTIC-QUICK-FIX-APPLY]] へ進む。
- missing typeはQuick Fix対象外であり、Copy Frontmatter Exampleなどの手動支援に留める。
- invalid table columnはCopy Expected Header、invalid table rowはmanual-edit guidance、unresolved referenceはCopy Referenceの対象であり、Quick Fix対象外である。
- code別の固定UIではなく、message / context から [[RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION]] に従ってmetadata、details、copy対象を導出する。
- Copy Expected Headerは、diagnostic-section-guidanceが対応するtable header診断にだけ表示される。
- Flow Diagramの `Flows.from` / `Flows.to` 診断はローカル `Objects.id` の確認として表示し、外部モデル参照の未解決とは区別する。
- DFD / Flow Diagramの `Flows.data` は、Wikilinkが解決できない場合だけ参照診断として表示する。plain text labelは診断対象にしない。
- Domain Sources / Domain Diagram診断では、fieldやrowIndexをdiagnosticMetaListに出し、refやsource種別の不整合はdiagnosticDetailBoxやCopy Referenceの候補として扱う。
- app_process `Steps.domain` 診断では、contextのstepId / domainIdをdiagnosticDetailBoxやCopy Referenceの候補として扱う。

## Notes
- プレビュー画面での診断リストの実装（`ModelingPreviewView.renderDiagnostics`）に準拠する。
- パス短縮の詳細方針は [[RULE-MW-PATH-SHORTENER]] に委ねる。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | symbol: renderDiagnostics; kind: function; 診断情報の表示 |
| src/views/modeling-preview-view.ts | symbol: renderDiagnosticSection; kind: function; 診断セクションの描画 |
| src/views/modeling-preview-view.ts | symbol: renderDiagnosticCard; kind: function; 診断カードとcopy/open actionsの描画 |
| src/views/modeling-preview-view.ts | symbol: getDiagnosticMetadata; kind: function; 診断メタ情報を抽出 |
| src/views/modeling-preview-view.ts | symbol: getDiagnosticDetailEntries; kind: function; 診断詳細情報を抽出 |
| src/views/modeling-preview-view.ts | symbol: getDiagnosticReferenceValue; kind: function; referenceコピー値を抽出 |
| src/views/modeling-preview-view.ts | symbol: getExpectedHeaderForDiagnostic; kind: function; expectedHeaderコピー値を抽出 |
| src/views/modeling-preview-view.ts | symbol: getFrontmatterExampleForDiagnostic; kind: function; frontmatter例を生成 |
| src/views/modeling-preview-view.ts | symbol: formatDiagnosticAsMarkdown; kind: function; Markdownコピー用文字列を生成 |
| src/views/modeling-preview-view.ts | symbol: getDiagnosticQuickFixActions; kind: function; Quick Fix候補を抽出 |
| src/views/modeling-preview-view.ts | symbol: createFrontmatterQuickFixAction; kind: function; missing frontmatter id / name のQuick Fix候補を生成 |
| src/views/modeling-preview-view.ts | symbol: getSafeFrontmatterQuickFixValues; kind: function; basenameから安全な値を導出 |
| src/core/diagnostic-section-guidance.ts | symbol: getExpectedHeaderForDiagnostic; kind: function; schema-driven expected headerを導出 |
| src/core/relation-resolver.ts | symbol: resolveDfdFlowDataReferenceWarnings; kind: function; DFD / Flow Diagram Flows.data Wikilink診断 |
| src/main.ts | symbol: openDiagnosticLocation; kind: function; 診断対象へのジャンプ |
| src/core/domain-diagnostics.ts | symbol: formatDomainDiagramUnresolvedSourceMessage; kind: function; Domain Sources診断メッセージ |
| src/core/domain-diagram-resolver.ts | symbol: resolveDomainSources; kind: function; Domain Sources解決とwarning生成 |
| src/core/app-process-domain-resolver.ts | symbol: resolveAppProcessDomainPlacement; kind: function; Steps.domain解決とwarning生成 |
