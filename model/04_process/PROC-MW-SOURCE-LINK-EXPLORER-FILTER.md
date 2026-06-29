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

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Source Links Explorer | 開始 | start | explorerState | | | | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]] | futureのSource Links Explorer向け |
| readLinks | Source Links Explorer | Source Link集合取得 | data | explorerState | visibleSourceLinks | | | | フィルタ対象を取得する |
| readFilters | Source Links Explorer | フィルタ条件取得 | input | sourcePathFilter, symbolFilter, sourceKindFilter, modelTypeFilter, sourceLinkSearchText | | | | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]] | UI上の一時条件 |
| hasPathFilter | Source Links Explorer | pathフィルタ判定 | decision | sourcePathFilter | | | | | 空でない場合だけ適用 |
| applyPathFilter | Source Links Explorer | pathで絞り込み | process | visibleSourceLinks | visibleSourceLinks | | | | Source Link pathを対象にする |
| hasSymbolFilter | Source Links Explorer | symbolフィルタ判定 | decision | symbolFilter | | | | | 空でない場合だけ適用 |
| applySymbolFilter | Source Links Explorer | symbolで絞り込み | process | visibleSourceLinks | visibleSourceLinks | | | | Source Link symbolを対象にする |
| hasKindFilter | Source Links Explorer | kindフィルタ判定 | decision | sourceKindFilter | | | | | all以外の場合だけ適用 |
| applyKindFilter | Source Links Explorer | kindで絞り込み | process | visibleSourceLinks | visibleSourceLinks | | | | Source Link kindを対象にする |
| hasModelTypeFilter | Source Links Explorer | model typeフィルタ判定 | decision | modelTypeFilter | | | | | all以外の場合だけ適用 |
| applyModelTypeFilter | Source Links Explorer | model typeで絞り込み | process | visibleSourceLinks | visibleSourceLinks | | | | assetIdからModelAssetを解決する |
| hasSearchText | Source Links Explorer | 検索文字列判定 | decision | sourceLinkSearchText | | | | | 空でない場合だけ適用 |
| applySearchText | Source Links Explorer | Source Link検索 | process | visibleSourceLinks | visibleSourceLinks | | | | path / symbol / notes / assetIdを対象にする |
| updateCount | Source Links Explorer | 件数更新 | process | visibleSourceLinks | filteredSourceLinkCount | | | | 絞り込み後の件数を反映する |
| collectAssets | Source Links Explorer | 関連ModelAsset集約 | data | visibleSourceLinks | relatedModelAssets | | | | 表示対象Source Linkに対応するAssetを集約する |
| collectDiagnostics | Source Links Explorer | 関連診断抽出 | process | visibleSourceLinks | relatedDiagnostics | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | | | Source Links不足などの診断を反映する |
| decideStatus | Source Links Explorer | 状態判定 | decision | filteredSourceLinkCount | explorerStatus | | | | empty / filtered / readyを判定する |
| updateUi | Source Links Explorer | UI反映用状態にする | screen | explorerState | explorerStatus | | | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]] | [[MAP-MW-SOURCE-LINK-EXPLORER-STATE-TO-UI]] で反映できる状態 |
| end | Source Links Explorer | 終了 | end | | | | | | 処理終了 |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | readLinks | | リンク取得 | |
| readLinks | readFilters | | 条件取得 | |
| readFilters | hasPathFilter | | path確認 | |
| hasPathFilter | applyPathFilter | sourcePathFilter exists | pathあり | |
| hasPathFilter | hasSymbolFilter | sourcePathFilter empty | pathなし | |
| applyPathFilter | hasSymbolFilter | | symbol確認 | |
| hasSymbolFilter | applySymbolFilter | symbolFilter exists | symbolあり | |
| hasSymbolFilter | hasKindFilter | symbolFilter empty | symbolなし | |
| applySymbolFilter | hasKindFilter | | kind確認 | |
| hasKindFilter | applyKindFilter | sourceKindFilter is not all | kindあり | |
| hasKindFilter | hasModelTypeFilter | sourceKindFilter is all | kindなし | |
| applyKindFilter | hasModelTypeFilter | | model type確認 | |
| hasModelTypeFilter | applyModelTypeFilter | modelTypeFilter is not all | model typeあり | |
| hasModelTypeFilter | hasSearchText | modelTypeFilter is all | model typeなし | |
| applyModelTypeFilter | hasSearchText | | 検索確認 | |
| hasSearchText | applySearchText | sourceLinkSearchText exists | 検索あり | |
| hasSearchText | updateCount | sourceLinkSearchText empty | 検索なし | |
| applySearchText | updateCount | | 件数更新 | |
| updateCount | collectAssets | | Asset集約 | |
| collectAssets | collectDiagnostics | | 診断抽出 | |
| collectDiagnostics | decideStatus | | 状態判定 | |
| decideStatus | updateUi | | UI反映 | |
| updateUi | end | | 完了 | |

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
