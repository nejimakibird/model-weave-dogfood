---
type: mapping
id: MAP-MW-REFERENCE-EXPLORER-STATE-TO-UI
name: Reference Explorer State to UI Mapping
kind: data_to_screen
source: "[[DATA-MW-REFERENCE-EXPLORER-STATE]]"
target: "[[SCR-MW-REFERENCE-EXPLORER-VIEW]]"
tags:
  - ModelWeave
  - Reference
  - Explorer
  - Mapping
  - UI
---

# Reference Explorer State to UI Mapping

## Summary
[[DATA-MW-REFERENCE-EXPLORER-STATE]] が保持する参照一覧、件数、選択状態、関連診断を [[SCR-MW-REFERENCE-EXPLORER-VIEW]] の各表示項目へ反映するマッピングを定義する。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-REFERENCE-EXPLORER-STATE]] | Reference Explorer表示状態 |
| source | [[ENT-MW-MODEL-REFERENCE]] | 表示対象の参照行 |
| source | [[ENT-MW-MODEL-ASSET]] | 参照元/参照先Asset |
| source | [[DATA-MW-CORE-DIAGNOSTIC]] | 関連診断 |
| target | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | Reference Explorer画面 |
| rule | [[RULE-MW-PATH-SHORTENER]] | ファイルパス短縮表示 |
| rule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | severity / resolved状態の表示 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceCount | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceCountLabel | 件数をそのまま表示 | | N | 参照総数 |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].unresolvedReferenceCount | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].unresolvedReferenceCountLabel | 件数をそのまま表示 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 0より大きい場合はwarning相当 |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].filteredReferenceCount | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].filteredReferenceCountLabel | 件数をそのまま表示 | | N | フィルタ後件数 |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceFilterMode | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceFilterModeSelect | 現在値として表示 | | N | all / unresolved / resolved |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceKindFilter | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceKindFilterSelect | 現在値として表示 | | N | 参照種別 |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].searchText | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceSearchInput | 現在値として表示 | | N | 検索文字列 |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].visibleReferences | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceList | 一覧として反復表示 | | Y | 複数のModelReferenceを表示 |
| [[ENT-MW-MODEL-REFERENCE]].referenceValue | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceTextLabel | そのまま表示 | | N | 記述された参照値 |
| [[ENT-MW-MODEL-REFERENCE]].referenceType | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceKindLabel | そのまま表示 | | N | wikilink / id / source_link 等 |
| [[ENT-MW-MODEL-REFERENCE]].resolved | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceResolvedBadge | resolved / unresolved のバッジへ変換 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 未解決はwarning相当 |
| [[ENT-MW-MODEL-REFERENCE]].sourceAssetId | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].sourceAssetLabel | そのまま表示 | | N | 参照元Asset |
| [[ENT-MW-MODEL-REFERENCE]].targetAssetId | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].targetAssetLabel | そのまま表示 | | N | 解決済みの場合の参照先 |
| [[ENT-MW-MODEL-ASSET]].filePath | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].sourceFilePathLabel | 短縮表示 | [[RULE-MW-PATH-SHORTENER]] | N | sourceAssetIdからModelAssetを解決して表示 |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedReferenceId | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].selectedReferenceDetail | 選択参照の詳細を表示 | | N | selectReference後に更新 |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].relatedDiagnostics | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].relatedDiagnosticsList | 関連診断を一覧表示 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 未解決参照など |

## Rules
- `referenceCount` / `unresolvedReferenceCount` / `filteredReferenceCount` はSummary領域へ表示する。
- `unresolvedReferenceCount` が1以上の場合、warning相当の表示を許容する。
- `visibleReferences` は `referenceList` に反復表示する。
- `referenceResolvedBadge` は `resolved=true` の場合 `resolved`、`false` の場合 `unresolved` と表示する。
- `unresolved` の表示は [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] に従い warning相当として扱ってよい。
- `sourceFilePathLabel` は `sourceAssetId` から [[ENT-MW-MODEL-ASSET]] を解決し、その `filePath` を表示する。
- 表示上の `filePath` は [[RULE-MW-PATH-SHORTENER]] に従って短縮してよい。
- ファイルジャンプや参照解決には短縮表示ではなく完全な `filePath` を使う。
- `relatedDiagnosticsList` は未解決参照など、選択参照に関連する診断を表示する。
- 本mappingは表示用の派生変換であり、Markdown本文やfrontmatterは変更しない。

## Notes
- [[DATA-MW-REFERENCE-EXPLORER-STATE]] は [[DATA-MW-VAULT-MODEL-INDEX]] から導出される派生状態である。
- 参照元/参照先ファイルパスは [[ENT-MW-MODEL-REFERENCE]] ではなく、`sourceAssetId` / `targetAssetId` から [[ENT-MW-MODEL-ASSET]] を解決して取得する。
- `filterReferenceExplorer` / `selectReference` / `openReferenceSource` / `openReferenceTarget` / `openDiagnosticLocation` は論理プロセス名であり、後続で `app_process` 化できる。
- 表示上のパス短縮はUI表示にのみ適用する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | VaultIndex | class | 参照情報の索引元 |
| src/core/relation-resolver.ts | resolveDiagramRelations | function | 参照解決処理 |
| src/main.ts | openDiagnosticLocation | function | 診断箇所ジャンプ |
| src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer表示更新の参考 |