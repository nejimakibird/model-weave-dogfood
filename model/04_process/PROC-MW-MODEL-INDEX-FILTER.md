---
type: app_process
id: PROC-MW-MODEL-INDEX-FILTER
name: Model Indexフィルタ
process_type: ui_action
tags:
  - ModelWeave
  - Vault
  - Index
  - Process
---

# Model Indexフィルタ

## Summary

Model Index View 上のモデル種別フィルタ、診断状態フィルタ、検索テキストをもとに、[[DATA-MW-VAULT-MODEL-INDEX]] の modelAssets から表示対象のModelAsset一覧を絞り込む将来構想の処理。
Model Weave 0.1.17時点では、Vault Index rebuildやRelationship Viewで使う内部indexは実装済みだが、独立したModel Index Viewとしての一覧・検索・フィルタ画面はfutureとして扱う。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| vaultModelIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | Index | Y | フィルタ対象となるVaultモデルインデックス |
| modelTypeFilter | [[SCR-MW-MODEL-INDEX-VIEW]].modelTypeFilter | UI | N | モデル種別フィルタ |
| diagnosticFilter | [[SCR-MW-MODEL-INDEX-VIEW]].diagnosticFilter | UI | N | 診断状態フィルタ |
| searchTextInput | [[SCR-MW-MODEL-INDEX-VIEW]].searchTextInput | UI | N | id / name / path を対象とする検索文字列 |
| severityRule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Rule | N | 診断severity判定 |
| pathShortenerRule | [[RULE-MW-PATH-SHORTENER]] | Rule | N | 表示時のパス短縮 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| filteredModelAssets | [[ENT-MW-MODEL-ASSET]] | [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetList | 絞り込み後に表示するModelAsset集合（論理的には複数） |
| diagnosticBadgeState | [[DATA-MW-CORE-DIAGNOSTIC]] | [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetDiagnosticBadge | 各ModelAssetに対する診断バッジ状態（論理的には複数） |
| filteredCount | number | [[SCR-MW-MODEL-INDEX-VIEW]] | 絞り込み後の件数 |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Model Index | 開始 | start | vaultModelIndex | | | | [[SCR-MW-MODEL-INDEX-VIEW]] | futureのModel Index View向け |
| readIndex | Model Index | ModelAsset一覧取得 | data | vaultModelIndex | filteredModelAssets | | | | modelAssetsを初期候補にする |
| readFilters | Model Index | フィルタ条件取得 | input | modelTypeFilter, diagnosticFilter, searchTextInput | | | | [[SCR-MW-MODEL-INDEX-VIEW]] | UI上の一時条件 |
| hasModelTypeFilter | Model Index | 種別フィルタ判定 | decision | modelTypeFilter | | | | | 指定がある場合だけ適用 |
| applyModelTypeFilter | Model Index | 種別で絞り込み | process | filteredModelAssets | filteredModelAssets | | | | ModelAsset.modelTypeで絞り込む |
| hasDiagnosticFilter | Model Index | 診断フィルタ判定 | decision | diagnosticFilter | | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | | | 指定がある場合だけ適用 |
| applyDiagnosticFilter | Model Index | 診断状態で絞り込み | process | filteredModelAssets | filteredModelAssets | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | | | severityで絞り込む |
| hasSearchText | Model Index | 検索文字列判定 | decision | searchTextInput | | | | | 空でない場合だけ適用 |
| applySearchText | Model Index | 文字列検索 | process | filteredModelAssets | filteredModelAssets | | | | assetId / name / filePath を対象にする |
| buildBadges | Model Index | 診断バッジ作成 | process | filteredModelAssets | diagnosticBadgeState | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | | | 各ModelAssetの最大severityを求める |
| shortenPaths | Model Index | 表示path短縮 | process | filteredModelAssets | filteredModelAssets | [[RULE-MW-PATH-SHORTENER]] | | | 表示上のみ短縮する |
| updateList | Model Index | 一覧へ反映 | screen | filteredModelAssets | filteredCount | | | [[SCR-MW-MODEL-INDEX-VIEW]] | modelAssetListへ表示する |
| isEmpty | Model Index | 空結果判定 | decision | filteredCount | | | | | 0件かどうかを判定する |
| showEmpty | Model Index | 空結果メッセージ | message | filteredCount | | | | [[SCR-MW-MODEL-INDEX-VIEW]] | noModelsFoundを表示する |
| end | Model Index | 終了 | end | | | | | | 処理終了 |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | readIndex | | 一覧取得 | |
| readIndex | readFilters | | 条件取得 | |
| readFilters | hasModelTypeFilter | | 種別確認 | |
| hasModelTypeFilter | applyModelTypeFilter | modelTypeFilter exists | 種別あり | |
| hasModelTypeFilter | hasDiagnosticFilter | modelTypeFilter empty | 種別なし | |
| applyModelTypeFilter | hasDiagnosticFilter | | 診断確認 | |
| hasDiagnosticFilter | applyDiagnosticFilter | diagnosticFilter exists | 診断条件あり | |
| hasDiagnosticFilter | hasSearchText | diagnosticFilter empty | 診断条件なし | |
| applyDiagnosticFilter | hasSearchText | | 検索確認 | |
| hasSearchText | applySearchText | searchTextInput exists | 検索あり | |
| hasSearchText | buildBadges | searchTextInput empty | 検索なし | |
| applySearchText | buildBadges | | バッジ作成 | |
| buildBadges | shortenPaths | | path表示 | |
| shortenPaths | updateList | | 一覧更新 | |
| updateList | isEmpty | | 空判定 | |
| isEmpty | showEmpty | filteredCount is 0 | 0件 | |
| showEmpty | end | | 完了 | |
| isEmpty | end | filteredCount is not 0 | 表示完了 | |

## Messages

| id | text | severity | notes |
|---|---|---|---|
| noModelsFound | No Model Weave models found. | info | フィルタ結果が空の場合 |
| filterApplied | Model index filter applied. | info | フィルタ適用完了 |
| filterHasWarnings | Filtered models include warnings. | warning | warningを含むモデルがある場合 |
| filterHasErrors | Filtered models include errors. | error | errorを含むモデルがある場合 |

## Notes

- 本処理はfutureのModel Index View向け設計であり、Model Weave 0.1.17時点の本体実装済み画面とは扱わない。
- 本処理は表示対象の絞り込みであり、Markdownモデル本文やfrontmatterは変更しない。
- [[DATA-MW-VAULT-MODEL-INDEX]] は派生インデックスであり、フィルタ結果も表示用の派生データである。
- diagnosticFilter は [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] によるseverity分類に従う。
- パス短縮は表示上のみ適用し、ファイルジャンプには完全なfilePathを使用する。
- `filterModelIndex` は [[SCR-MW-MODEL-INDEX-VIEW]] から呼び出される論理プロセスであり、本定義はその実体となるアルゴリズムを示す。
- 現行実装済みのImpact Summary / Relationship View / Weave MapはViewer内の派生表示であり、このModel Index Viewとは別機能である。
- 現行実装済みのSource Links section supportは各モデル内の `## Source Links` の解析・表示・Copy/Openであり、この横断的なModel Indexフィルタとは別機能である。

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | VaultIndex class。future Model Index Viewが参照し得る内部索引 |
| src/core/current-file-diagnostics.ts | buildCurrentObjectDiagnostics function。現行の診断生成参考 |
| src/views/modeling-preview-view.ts | renderCurrentState method。現行Viewer表示であり、独立Model Index View実装ではない |
