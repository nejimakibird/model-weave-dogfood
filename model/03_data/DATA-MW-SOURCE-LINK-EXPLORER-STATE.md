---
type: data_object
id: DATA-MW-SOURCE-LINK-EXPLORER-STATE
name: Source Links Explorer表示状態
kind: state
tags:
  - ModelWeave
  - SourceLinks
  - Explorer
  - State
---

# Source Links Explorer表示状態

## Summary
Source Links Explorerで表示するモデル資産と実装ソースの対応関係、検索条件、選択状態、関連診断を保持する表示用状態データ。
[[DATA-MW-VAULT-MODEL-INDEX]] から導出される派生状態であり、Markdownモデルの正本ではない。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| explorerId | Explorer ID | string | | Y | explorerId | | Source Links Explorer状態の識別子 |
| sourceIndexId | 元インデックスID | string | | N | sourceIndexId | [[DATA-MW-VAULT-MODEL-INDEX]] | 参照元となるVaultモデルインデックス |
| sourceLinkCount | Source Link数 | number | | N | summary.sourceLinkCount | [[ENT-MW-SOURCE-LINK]] | Source Links総数 |
| linkedAssetCount | Source Link付き資産数 | number | | N | summary.linkedAssetCount | [[ENT-MW-MODEL-ASSET]] | Source Linksを持つModelAsset数 |
| missingSourceLinkCount | Source Link未設定資産数 | number | | N | summary.missingSourceLinkCount | [[ENT-MW-MODEL-ASSET]] | Source Linksを持たないModelAsset数 |
| filteredSourceLinkCount | 表示Source Link数 | number | | N | summary.filteredSourceLinkCount | [[ENT-MW-SOURCE-LINK]] | フィルタ後に表示するSource Link数 |
| selectedSourceLinkId | 選択Source Link ID | string | | N | selectedSourceLinkId | [[ENT-MW-SOURCE-LINK]] | 現在選択中のSource Link |
| selectedAssetId | 選択アセットID | string | | N | selectedAssetId | [[ENT-MW-MODEL-ASSET]] | 選択Source Linkの対応ModelAsset |
| sourcePathFilter | ソースパスフィルタ | string | | N | filters.sourcePathFilter | | path検索条件 |
| symbolFilter | シンボルフィルタ | string | | N | filters.symbolFilter | | symbol検索条件 |
| sourceKindFilter | Source Link種別フィルタ | string | | N | filters.sourceKindFilter | | function / class / type / constant 等 |
| modelTypeFilter | モデル種別フィルタ | string | | N | filters.modelTypeFilter | [[ENT-MW-MODEL-ASSET]] | 対応するModelAssetの種別 |
| searchText | 検索テキスト | string | | N | filters.searchText | | path / symbol / notes / assetId の検索 |
| visibleSourceLinks | 表示Source Link一覧 | object | | N | visibleSourceLinks | [[ENT-MW-SOURCE-LINK]] | 複数のSource Linkを保持するリスト |
| relatedModelAssets | 関連モデル資産一覧 | object | | N | relatedModelAssets | [[ENT-MW-MODEL-ASSET]] | Source Linkに紐づくModelAssetを保持するリスト |
| relatedDiagnostics | 関連診断一覧 | object | | N | relatedDiagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | Source Links不足や未確認リンクなどに紐づく診断のリスト |
| explorerStatus | Explorer状態 | string | | N | explorerStatus | | ready / empty / filtered / error など |

## Notes
- 本データはSource Links Explorer表示用の派生状態であり、Markdownモデル本文やfrontmatterの正本ではない。
- visibleSourceLinks / relatedModelAssets / relatedDiagnostics は論理的には複数要素を保持するが、Markdownテーブル安全性のため type は object とし、複数性はnotesで表現する。
- Source Linksはモデル資産から実装ソースへ辿るためのナビゲーション情報であり、実装ソースそのものを正本として保存するものではない。
- path表示は [[RULE-MW-PATH-SHORTENER]] によって短縮してよいが、ファイルジャンプやソース探索には完全なpathを使用する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/core/vault-index.ts | VaultIndex | class | Source Links情報の索引元 |
| source/model-weave-repo/src/core/schema-detector.ts | detectModelType | function | モデル資産種別の検出 |
| source/model-weave-repo/src/main.ts | openDiagnosticLocation | function | ファイルジャンプ処理の参考 |