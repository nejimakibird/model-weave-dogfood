---
type: app_process
id: PROC-MW-REFERENCE-EXPLORER-FILTER
name: Reference Explorerフィルタ
process_type: ui_action
tags:
  - ModelWeave
  - Reference
  - Explorer
  - Process
---

# Reference Explorerフィルタ

## Summary
Reference Explorer上の参照状態フィルタ、参照種別フィルタ、検索文字列をもとに、[[DATA-MW-REFERENCE-EXPLORER-STATE]] の `visibleReferences` を更新する処理。
未解決参照や特定種別の参照を絞り込み、Reference Explorer Viewへ表示する。
Model Weave 0.1.17時点では、Relationship View / Impact Summaryによる参照表示は実装済みだが、独立したReference Explorer画面と横断フィルタはfutureとして扱う。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| explorerState | [[DATA-MW-REFERENCE-EXPLORER-STATE]] | State | Y | フィルタ対象となるReference Explorer状態 |
| referenceFilterMode | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceFilterModeSelect | UI | N | all / unresolved / resolved |
| referenceKindFilter | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceKindFilterSelect | UI | N | wikilink / id / source_link / rule 等 |
| referenceSearchText | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceSearchInput | UI | N | referenceValue / assetId / filePath検索 |
| modelReferences | [[ENT-MW-MODEL-REFERENCE]] | Explorer State | Y | フィルタ対象の参照集合 |
| relatedAssets | [[ENT-MW-MODEL-ASSET]] | Vault Index | N | filePath検索や参照元/参照先表示に使用 |
| severityRule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Rule | N | 未解決参照や診断状態の表示分類 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| visibleReferences | [[ENT-MW-MODEL-REFERENCE]] | [[DATA-MW-REFERENCE-EXPLORER-STATE]].visibleReferences | 絞り込み後に表示する参照集合 |
| filteredReferenceCount | number | [[DATA-MW-REFERENCE-EXPLORER-STATE]].filteredReferenceCount | 絞り込み後の参照件数 |
| explorerStatus | string | [[DATA-MW-REFERENCE-EXPLORER-STATE]].explorerStatus | ready / empty / filtered / error 等 |
| relatedDiagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | [[DATA-MW-REFERENCE-EXPLORER-STATE]].relatedDiagnostics | 表示対象参照に関連する診断集合 |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Reference Explorer | 開始 | start | explorerState | | | | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | futureのReference Explorer向け |
| readReferences | Reference Explorer | 参照集合取得 | data | explorerState | visibleReferences | | | | フィルタ対象の参照集合を取得する |
| readFilters | Reference Explorer | フィルタ条件取得 | input | referenceFilterMode, referenceKindFilter, referenceSearchText | | | | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | UI上の一時条件 |
| decideResolvedFilter | Reference Explorer | 解決状態フィルタ判定 | decision | referenceFilterMode | | | | | resolved / unresolved / allを判定する |
| applyResolvedFilter | Reference Explorer | 解決状態で絞り込み | process | visibleReferences | visibleReferences | | | | resolved値で絞り込む |
| decideKindFilter | Reference Explorer | 参照種別フィルタ判定 | decision | referenceKindFilter | | | | | all以外の場合だけ適用 |
| applyKindFilter | Reference Explorer | 参照種別で絞り込み | process | visibleReferences | visibleReferences | | | | referenceTypeで絞り込む |
| decideSearchText | Reference Explorer | 検索文字列判定 | decision | referenceSearchText | | | | | 空でない場合だけ適用 |
| applySearchText | Reference Explorer | 参照検索 | process | visibleReferences | visibleReferences | | | | referenceValue / assetId / filePathを対象にする |
| updateCount | Reference Explorer | 件数更新 | process | visibleReferences | filteredReferenceCount | | | | 絞り込み後の件数を反映する |
| collectDiagnostics | Reference Explorer | 関連診断抽出 | process | visibleReferences | relatedDiagnostics | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | | | 未解決参照や関連診断を反映する |
| decideStatus | Reference Explorer | 状態判定 | decision | filteredReferenceCount | explorerStatus | | | | empty / filtered / readyを判定する |
| updateUi | Reference Explorer | UI反映用状態にする | screen | explorerState | explorerStatus | | | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | [[MAP-MW-REFERENCE-EXPLORER-STATE-TO-UI]] で反映できる状態 |
| end | Reference Explorer | 終了 | end | | | | | | 処理終了 |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | readReferences | | 参照取得 | |
| readReferences | readFilters | | 条件取得 | |
| readFilters | decideResolvedFilter | | 解決状態確認 | |
| decideResolvedFilter | applyResolvedFilter | referenceFilterMode is resolved or unresolved | 条件あり | |
| decideResolvedFilter | decideKindFilter | referenceFilterMode is all | 条件なし | |
| applyResolvedFilter | decideKindFilter | | 種別確認 | |
| decideKindFilter | applyKindFilter | referenceKindFilter is not all | 種別あり | |
| decideKindFilter | decideSearchText | referenceKindFilter is all | 種別なし | |
| applyKindFilter | decideSearchText | | 検索確認 | |
| decideSearchText | applySearchText | referenceSearchText exists | 検索あり | |
| decideSearchText | updateCount | referenceSearchText empty | 検索なし | |
| applySearchText | updateCount | | 件数更新 | |
| updateCount | collectDiagnostics | | 診断抽出 | |
| collectDiagnostics | decideStatus | | 状態判定 | |
| decideStatus | updateUi | | UI反映 | |
| updateUi | end | | 完了 | |

## Messages

| id | text | severity | notes |
|---|---|---|---|
| noReferencesFound | No references found. | info | フィルタ結果が空の場合 |
| referenceFilterApplied | Reference filter applied. | info | フィルタ適用完了 |
| unresolvedReferencesFound | Unresolved references found. | warning | 未解決参照が表示対象に含まれる場合 |
| referenceFilterFailed | Failed to filter references. | error | フィルタ処理に失敗した場合 |

## Notes
- 本処理はfutureのReference Explorer向け設計であり、Model Weave 0.1.17時点の本体実装済み画面とは扱わない。
- 本処理はReference Explorer上の表示対象を絞り込むUI処理であり、Markdownモデル本文やfrontmatterは変更しない。
- [[DATA-MW-REFERENCE-EXPLORER-STATE]] は派生状態であり、フィルタ結果も表示用の一時状態である。
- `referenceFilterMode` / `referenceKindFilter` / `searchText` はReference Explorerの一時的なUI状態として扱う。
- `filePath` 検索では、[[ENT-MW-MODEL-REFERENCE]] 自身ではなく `sourceAssetId` / `targetAssetId` から [[ENT-MW-MODEL-ASSET]] を解決して `filePath` を参照する。
- 表示上のパス短縮は [[RULE-MW-PATH-SHORTENER]] に委ねるが、検索やジャンプには完全な `filePath` を使用する。
- `filterReferenceExplorer` は [[SCR-MW-REFERENCE-EXPLORER-VIEW]] から呼び出される論理プロセスであり、本定義はその詳細に相当する。
- 現行実装済みのImpact Summary / Relationship View / Weave MapはViewer内の派生表示であり、Reference Explorerの横断検索・選択状態とは別機能である。
- 現行実装済みのref-aware Business Flow step hover / click targetはapp_process Business Flow限定の参照操作支援であり、このExplorer画面ではない。

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | VaultIndex class。future Reference Explorerが参照し得る内部索引 |
| src/core/relation-resolver.ts | resolveDiagramRelations function。現行の参照解決処理 |
| src/core/impact-analyzer.ts | buildImpactSummary function。現行Relationship View向けのImpact Summary生成 |
| src/views/modeling-preview-view.ts | renderCurrentState method。現行Viewer表示であり、独立Reference Explorer実装ではない |
