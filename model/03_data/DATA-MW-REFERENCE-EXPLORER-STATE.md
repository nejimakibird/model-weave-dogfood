---
type: data_object
id: DATA-MW-REFERENCE-EXPLORER-STATE
name: Reference Explorer表示状態
kind: state
tags:
  - ModelWeave
  - Reference
  - Explorer
  - State
---

# Reference Explorer表示状態

## Summary
Reference Explorerで表示する参照一覧、未解決参照、参照元/参照先、診断状態、フィルタ条件を保持する表示用状態データ。
[[DATA-MW-VAULT-MODEL-INDEX]] から導出される派生状態であり、Markdownモデルの正本ではない。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| explorerId | Explorer ID | string | | Y | explorerId | | Reference Explorer状態の識別子 |
| sourceIndexId | 元インデックスID | string | | N | sourceIndexId | [[DATA-MW-VAULT-MODEL-INDEX]] | 参照元となるVaultモデルインデックス |
| referenceCount | 参照数 | number | | N | summary.referenceCount | [[ENT-MW-MODEL-REFERENCE]] | 参照総数 |
| unresolvedReferenceCount | 未解決参照数 | number | | N | summary.unresolvedReferenceCount | [[ENT-MW-MODEL-REFERENCE]] | resolved=false の参照数 |
| filteredReferenceCount | 表示参照数 | number | | N | summary.filteredReferenceCount | [[ENT-MW-MODEL-REFERENCE]] | フィルタ後に表示する参照数 |
| selectedReferenceId | 選択参照ID | string | | N | selectedReferenceId | [[ENT-MW-MODEL-REFERENCE]] | 現在選択中の参照 |
| selectedSourceAssetId | 選択参照元アセットID | string | | N | selectedSourceAssetId | [[ENT-MW-MODEL-ASSET]] | 選択参照の参照元 |
| selectedTargetAssetId | 選択参照先アセットID | string | | N | selectedTargetAssetId | [[ENT-MW-MODEL-ASSET]] | 解決済みの場合の参照先 |
| referenceFilterMode | 参照フィルタモード | string | | N | filters.referenceFilterMode | | all / unresolved / resolved など |
| referenceKindFilter | 参照種別フィルタ | string | | N | filters.referenceKindFilter | | wikilink / id / source_link / rule 等 |
| sourceAssetFilter | 参照元フィルタ | string | | N | filters.sourceAssetFilter | [[ENT-MW-MODEL-ASSET]] | 参照元Assetで絞り込む |
| targetAssetFilter | 参照先フィルタ | string | | N | filters.targetAssetFilter | [[ENT-MW-MODEL-ASSET]] | 参照先Assetで絞り込む |
| searchText | 検索テキスト | string | | N | filters.searchText | | referenceText / assetId / filePath の検索 |
| visibleReferences | 表示参照一覧 | object | | N | visibleReferences | [[ENT-MW-MODEL-REFERENCE]] | 複数のModelReferenceを表示用に保持 |
| relatedDiagnostics | 関連診断一覧 | object | | N | relatedDiagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | 未解決参照などに紐づく診断 |
| explorerStatus | Explorer状態 | string | | N | explorerStatus | | ready / empty / filtered / error など |

## Notes
- 本データはReference Explorer表示用の派生状態であり、Markdownモデル本文やfrontmatterの正本ではない。
- visibleReferences / relatedDiagnostics は論理的には複数要素を保持するが、Markdownテーブル安全性のため type は object とし、複数性はnotesで表現する。
- 未解決参照は [[ENT-MW-MODEL-REFERENCE]] の resolved=false および [[DATA-MW-CORE-DIAGNOSTIC]] の診断情報として扱う。
- パス表示は [[RULE-MW-PATH-SHORTENER]] によって短縮してよいが、ファイルジャンプや参照解決には完全なfilePathを使用する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | VaultIndex | class | 参照情報の索引元 |
| src/core/relation-resolver.ts | resolveDiagramRelations | function | 参照解決処理 |
| src/core/current-file-diagnostics.ts | buildCurrentObjectDiagnostics | function | 未解決参照などの診断生成 |
| src/main.ts | openDiagnosticLocation | function | 診断箇所ジャンプの参考 |