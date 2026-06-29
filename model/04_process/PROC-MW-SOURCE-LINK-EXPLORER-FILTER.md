---
type: app_process
id: PROC-MW-SOURCE-LINK-EXPLORER-FILTER
name: Source Links Explorerフィルタ
process_type: ui_action
tags:
  - ModelWeave
  - SourceLinks
  - Explorer
  - Process
---

# Source Links Explorerフィルタ

## Summary
Source Links Explorer上のソースパス、シンボル、Source Link種別、モデル種別、検索文字列をもとに、[[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] の `visibleSourceLinks` を更新する処理。
モデル資産と実装ソースの対応関係を絞り込み、Source Links Explorer Viewへ表示する。
Model Weave 0.1.17時点では、各モデル内の `## Source Links` section解析・Preview表示・Copy/Openは実装済みだが、横断的なSource Links Explorer画面とフィルタはfutureとして扱う。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| explorerState | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] | State | Y | フィルタ対象となるSource Links Explorer状態 |
| sourcePathFilter | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourcePathFilterInput | UI | N | path検索条件 |
| symbolFilter | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].symbolFilterInput | UI | N | symbol検索条件 |
| sourceKindFilter | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceKindFilterSelect | UI | N | kindフィルタ |
| modelTypeFilter | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].modelTypeFilterSelect | UI | N | 対応ModelAssetの種別フィルタ |
| sourceLinkSearchText | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceLinkSearchInput | UI | N | path / symbol / notes / assetId を対象とする検索文字列 |
| sourceLinks | [[ENT-MW-SOURCE-LINK]] | Explorer State | Y | フィルタ対象のSource Link集合 |
| relatedAssets | [[ENT-MW-MODEL-ASSET]] | Vault Index | N | assetIdからModelAsset種別を解決するために使用 |
| severityRule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Rule | N | Source Links不足などの表示分類 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| visibleSourceLinks | [[ENT-MW-SOURCE-LINK]] | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].visibleSourceLinks | 絞り込み後に表示するSource Link集合 |
| filteredSourceLinkCount | number | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].filteredSourceLinkCount | 絞り込み後のSource Link件数 |
| relatedModelAssets | [[ENT-MW-MODEL-ASSET]] | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedModelAssets | 表示対象Source Linkに対応するModelAsset集合 |
| relatedDiagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedDiagnostics | 表示対象Source Linkや不足状態に関連する診断集合 |
| explorerStatus | string | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].explorerStatus | ready / empty / filtered / error 等 |

## Steps

1. [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] からフィルタ対象のSource Link集合を取得する。
2. `sourcePathFilter` が空でない場合、[[ENT-MW-SOURCE-LINK]].path を対象に部分一致検索する。
3. `symbolFilter` が空でない場合、[[ENT-MW-SOURCE-LINK]].symbol を対象に部分一致検索する。
4. `sourceKindFilter` が `all` 以外の場合、[[ENT-MW-SOURCE-LINK]].kind が一致するSource Linkだけを残す。
5. `modelTypeFilter` が `all` 以外の場合、`assetId` から [[ENT-MW-MODEL-ASSET]] を解決し、ModelAssetの `modelType` が一致するSource Linkだけを残す。
6. `sourceLinkSearchText` が空でない場合、`path` / `symbol` / `notes` / `assetId` を対象に部分一致検索する。
7. 絞り込み後のSource Link集合を `visibleSourceLinks` に反映する。
8. `visibleSourceLinks` の件数を `filteredSourceLinkCount` に反映する。
9. 表示対象Source Linkに対応するModelAssetを `relatedModelAssets` に反映する。
10. 表示対象Source LinkやSource Links不足に関連する診断を `relatedDiagnostics` に反映する。
11. `filteredSourceLinkCount` が 0 件なら `explorerStatus=empty`、条件適用中なら `filtered`、通常表示なら `ready` とする。
12. 結果を [[MAP-MW-SOURCE-LINK-EXPLORER-STATE-TO-UI]] により UI へ反映できる状態にする。

## Messages

| id | text | severity | notes |
|---|---|---|---|
| noSourceLinksFound | No Source Links found. | info | フィルタ結果が空の場合 |
| sourceLinkFilterApplied | Source Links filter applied. | info | フィルタ適用完了 |
| sourceLinksMissing | Some model assets have no Source Links. | warning | Link未設定資産が表示対象または診断に含まれる場合 |
| sourceLinkFilterFailed | Failed to filter Source Links. | error | フィルタ処理に失敗した場合 |

## Notes
- 本処理はfutureのSource Links Explorer向け設計であり、Model Weave 0.1.17時点の本体実装済み画面とは扱わない。
- 本処理はSource Links Explorer上の表示対象を絞り込むUI処理であり、Markdownモデル本文やfrontmatterは変更しない。
- [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] は派生状態であり、フィルタ結果も表示用の一時状態である。
- `sourcePathFilter` / `symbolFilter` / `sourceKindFilter` / `modelTypeFilter` / `searchText` はSource Links Explorerの一時的なUI状態として扱う。
- `modelTypeFilter` では [[ENT-MW-SOURCE-LINK]].assetId から [[ENT-MW-MODEL-ASSET]] を解決して `modelType` を参照する。
- パス検索やcopy/open処理では短縮表示ではなく完全な `path` を使用する。
- 表示上のパス短縮は [[RULE-MW-PATH-SHORTENER]] に委ねる。
- `filterSourceLinks` は [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]] から呼び出される論理プロセスであり、本定義はその詳細に相当する。
- 現行実装済みのSource Links section supportは各モデル内の `## Source Links` を解析し、Preview内で表示し、Copy Path / Openを提供する機能である。
- Relationship View / Impact SummaryのRelated Source Links集約は現行実装済みだが、Explorer専用の一覧・検索・選択状態とは別機能である。

## Source Links

| path | notes |
|---|---|
| src/parsers/source-links-parser.ts | parseSourceLinks function。現行Source Links section supportの解析 |
| src/renderers/source-links-renderer.ts | renderSourceLinks function。現行Preview内Source Links表示とCopy/Open |
| src/core/impact-analyzer.ts | collectRelatedSourceLinks function。現行Relationship / Impact Summaryへの関連Source Links集約 |
| src/views/modeling-preview-view.ts | renderCurrentState method。現行Viewer表示であり、独立Source Links Explorer実装ではない |
