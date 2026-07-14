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
future / plannedの [[DATA-MW-REFERENCE-EXPLORER-STATE]] が保持する参照一覧、件数、選択状態、関連診断を [[SCR-MW-REFERENCE-EXPLORER-VIEW]] の各表示項目へ反映するマッピングを定義する。
Model Weave 0.1.17時点では、Impact Summary / Relationship View / Weave Mapは実装済みだが、このExplorer画面へのUI反映は本体srcで確認できない。

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

| target_ref | source_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceCountLabel | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceCount | 件数をそのまま表示 |  | N | 参照総数 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].unresolvedReferenceCountLabel | [[DATA-MW-REFERENCE-EXPLORER-STATE]].unresolvedReferenceCount | 件数をそのまま表示 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 0より大きい場合はwarning相当 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].filteredReferenceCountLabel | [[DATA-MW-REFERENCE-EXPLORER-STATE]].filteredReferenceCount | 件数をそのまま表示 |  | N | フィルタ後件数 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceFilterModeSelect | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceFilterMode | 現在値として表示 |  | N | all / unresolved / resolved |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceKindFilterSelect | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceKindFilter | 現在値として表示 |  | N | 参照種別 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceSearchInput | [[DATA-MW-REFERENCE-EXPLORER-STATE]].searchText | 現在値として表示 |  | N | 検索文字列 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceList | [[DATA-MW-REFERENCE-EXPLORER-STATE]].visibleReferences | 一覧として反復表示 |  | Y | 複数のModelReferenceを表示 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceTextLabel | [[ENT-MW-MODEL-REFERENCE]].referenceValue | そのまま表示 |  | N | 記述された参照値 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceKindLabel | [[ENT-MW-MODEL-REFERENCE]].referenceType | そのまま表示 |  | N | wikilink / id / source_link 等 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceResolvedBadge | [[ENT-MW-MODEL-REFERENCE]].resolved | resolved / unresolved のバッジへ変換 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 未解決はwarning相当 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].sourceAssetLabel | [[ENT-MW-MODEL-REFERENCE]].sourceAssetId | そのまま表示 |  | N | 参照元Asset |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].targetAssetLabel | [[ENT-MW-MODEL-REFERENCE]].targetAssetId | そのまま表示 |  | N | 解決済みの場合の参照先 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].sourceFilePathLabel | [[ENT-MW-MODEL-ASSET]].filePath | 短縮表示 | [[RULE-MW-PATH-SHORTENER]] | N | sourceAssetIdからModelAssetを解決して表示 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].selectedReferenceDetail | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedReferenceId | 選択参照の詳細を表示 |  | N | selectReference後に更新 |
| [[SCR-MW-REFERENCE-EXPLORER-VIEW]].relatedDiagnosticsList | [[DATA-MW-REFERENCE-EXPLORER-STATE]].relatedDiagnostics | 関連診断を一覧表示 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 未解決参照など |

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
- 本mappingはfuture / plannedのReference Explorer向けであり、現行Viewer内Relationship ViewやWeave Mapの表示mappingとは分けて扱う。
- 参照元/参照先ファイルパスは [[ENT-MW-MODEL-REFERENCE]] ではなく、`sourceAssetId` / `targetAssetId` から [[ENT-MW-MODEL-ASSET]] を解決して取得する。
- `filterReferenceExplorer` / `selectReference` / `openReferenceSource` / `openReferenceTarget` / `openDiagnosticLocation` は論理プロセス名であり、後続で `app_process` 化できる。
- 表示上のパス短縮はUI表示にのみ適用する。

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | symbol: VaultIndex; kind: class; 参照情報の索引元 |
| src/core/relation-resolver.ts | symbol: resolveDiagramRelations; kind: function; 参照解決処理 |
| src/main.ts | symbol: openDiagnosticLocation; kind: function; 診断箇所ジャンプ |
| src/views/modeling-preview-view.ts | symbol: renderCurrentState; kind: method; Viewer表示更新の参考 |
