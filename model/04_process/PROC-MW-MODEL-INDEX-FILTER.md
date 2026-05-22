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

Model Index View 上のモデル種別フィルタ、診断状態フィルタ、検索テキストをもとに、[[DATA-MW-VAULT-MODEL-INDEX]] の modelAssets から表示対象のModelAsset一覧を絞り込む処理。

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

1. [[DATA-MW-VAULT-MODEL-INDEX]].modelAssets を取得する。
2. modelTypeFilter が指定されている場合、ModelAsset.modelType で絞り込む。
3. diagnosticFilter が指定されている場合、ModelAssetに紐づくDiagnosticのseverityで絞り込む。
4. searchTextInput が空でない場合、assetId / name / filePath を対象に部分一致検索する。
5. 対象ModelAssetごとに最も高いseverityを求め、diagnosticBadgeState を作成する。
6. filePath表示は必要に応じて [[RULE-MW-PATH-SHORTENER]] に委ねる。
7. 絞り込み結果を [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetList に反映する。
8. 結果が0件の場合は noModelsFound メッセージを表示する。

## Messages

| id | text | severity | notes |
|---|---|---|---|
| noModelsFound | No Model Weave models found. | info | フィルタ結果が空の場合 |
| filterApplied | Model index filter applied. | info | フィルタ適用完了 |
| filterHasWarnings | Filtered models include warnings. | warning | warningを含むモデルがある場合 |
| filterHasErrors | Filtered models include errors. | error | errorを含むモデルがある場合 |

## Notes

- 本処理は表示対象の絞り込みであり、Markdownモデル本文やfrontmatterは変更しない。
- [[DATA-MW-VAULT-MODEL-INDEX]] は派生インデックスであり、フィルタ結果も表示用の派生データである。
- diagnosticFilter は [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] によるseverity分類に従う。
- パス短縮は表示上のみ適用し、ファイルジャンプには完全なfilePathを使用する。
- `filterModelIndex` は [[SCR-MW-MODEL-INDEX-VIEW]] から呼び出される論理プロセスであり、本定義はその実体となるアルゴリズムを示す。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/core/vault-index.ts | VaultIndex | class | Vault内モデル資産の索引 |
| source/model-weave-repo/src/core/current-file-diagnostics.ts | buildCurrentObjectDiagnostics | function | 診断情報の生成 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer表示更新 |