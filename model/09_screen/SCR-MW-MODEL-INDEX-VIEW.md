---
type: screen
id: SCR-MW-MODEL-INDEX-VIEW
name: Model Index View
screen_type: viewer
tags:
  - ModelWeave
  - Vault
  - Index
  - UI
---

# Model Index View

## Summary

Vault内で検出されたModel Weaveモデル資産を一覧表示する画面。
モデル種別、ファイルパス、診断状態、未解決参照数などを確認し、対象モデルファイルへ移動できるようにする。

## Layout

| id | label | kind | purpose | notes |
|---|---|---|---|---|
| index_view_root | Model Index View Root | container | 画面全体のコンテナ | |
| index_summary_area | Index Summary Area | section | Vault全体の件数サマリー表示 | |
| index_filter_area | Index Filter Area | toolbar | 種別・診断状態・検索条件の指定 | |
| model_list_area | Model List Area | list | モデル資産一覧の表示 | |
| index_action_area | Index Action Area | section | 再解析や対象ファイルジャンプなどの操作 | |
| model_asset_row | Model Asset Row | row | モデル資産一覧の1行表示 | modelAssetList内で反復表示される行 |

## Fields

| id | label | kind | layout | data_type | required | ref | rule | notes |
|---|---|---|---|---|---|---|---|---|
| modelFileCountLabel | モデルファイル数 | label | index_summary_area | number | N | [[DATA-MW-VAULT-MODEL-INDEX]].modelFileCount | | 検出されたMarkdownモデルファイル数 |
| modelAssetCountLabel | モデル資産数 | label | index_summary_area | number | N | [[DATA-MW-VAULT-MODEL-INDEX]].modelAssetCount | | 認識されたModelAsset数 |
| unresolvedReferenceCountLabel | 未解決参照数 | label | index_summary_area | number | N | [[DATA-MW-VAULT-MODEL-INDEX]].unresolvedReferenceCount | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | unresolved reference数 |
| diagnosticSummaryLabel | 診断サマリー | label | index_summary_area | string | N | [[DATA-MW-VAULT-MODEL-INDEX]].diagnostics | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | error / warning件数 |
| modelTypeFilter | モデル種別フィルタ | select | index_filter_area | string | N | [[DATA-MW-VAULT-MODEL-INDEX]].modelAssets | | data_object / rule / mapping 等 |
| diagnosticFilter | 診断状態フィルタ | select | index_filter_area | string | N | [[DATA-MW-CORE-DIAGNOSTIC]] | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | all / error / warning / clean 等 |
| searchTextInput | 検索テキスト | input | index_filter_area | string | N | [[DATA-MW-VAULT-MODEL-INDEX]] | | id / name / path の検索 |
| modelAssetList | モデル資産一覧 | list | model_list_area | object | Y | [[DATA-MW-VAULT-MODEL-INDEX]].modelAssets | | 複数のModelAssetを一覧表示 |
| modelAssetIdLabel | アセットID | label | model_asset_row | string | N | [[ENT-MW-MODEL-ASSET]].assetId | | モデル資産ID |
| modelAssetTypeLabel | モデル種別 | label | model_asset_row | string | N | [[ENT-MW-MODEL-ASSET]].modelType | | モデル種別 |
| modelAssetNameLabel | 表示名 | label | model_asset_row | string | N | [[ENT-MW-MODEL-ASSET]].name | | frontmatter.name |
| modelAssetPathLabel | ファイルパス | label | model_asset_row | string | N | [[ENT-MW-MODEL-ASSET]].filePath | [[RULE-MW-PATH-SHORTENER]] | 表示上は短縮してよい |
| modelAssetDiagnosticBadge | 診断バッジ | label | model_asset_row | string | N | [[DATA-MW-CORE-DIAGNOSTIC]] | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | error / warning等 |
| refreshIndexButton | インデックス再解析 | button | index_action_area | action | N | [[DATA-MW-VAULT-MODEL-INDEX]] | | Vault indexを再構築 |
| openSelectedModelButton | 選択モデルを開く | button | index_action_area | action | N | [[ENT-MW-MODEL-ASSET]].filePath | | 選択ファイルを開く |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| changeModelTypeFilter | モデル種別フィルタ変更 | ui_action | modelTypeFilter | change | [[PROC-MW-MODEL-INDEX-FILTER]] | | | 一覧を種別で絞り込む |
| changeDiagnosticFilter | 診断状態フィルタ変更 | ui_action | diagnosticFilter | change | [[PROC-MW-MODEL-INDEX-FILTER]] | | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | 診断状態で絞り込む |
| searchModelIndex | モデル検索 | ui_action | searchTextInput | input | [[PROC-MW-MODEL-INDEX-FILTER]] | | | id / name / path検索 |
| selectModelAsset | モデル資産選択 | ui_action | modelAssetList | select | selectModelAsset | | | 対象資産を選択 |
| openSelectedModel | 選択モデルを開く | ui_action | openSelectedModelButton | click | [[PROC-MW-MODEL-FILE-OPEN]] | | | 選択したMarkdownファイルを開く |
| refreshModelIndex | インデックス再解析 | ui_action | refreshIndexButton | click | [[PROC-MW-VAULT-INDEX-REBUILD]] | | | Vault内モデルを再解析 |

## Messages

| id | text | severity | timing | notes |
|---|---|---|---|---|
| noModelsFound | No Model Weave models found. | info | when_empty | モデル資産が見つからない場合 |
| indexRebuilt | Model index rebuilt. | info | on_refresh | 再解析完了 |
| indexHasWarnings | Model index has warnings. | warning | when_warning_exists | warningがある場合 |
| indexHasErrors | Model index has errors. | error | when_error_exists | errorがある場合 |

## Notes

- このScreenはVaultモデルインデックスの表示用UIであり、Markdownモデルの正本を変更しない。
- 表示対象の正本はVault内Markdownファイルである。
- [[DATA-MW-VAULT-MODEL-INDEX]] は派生インデックスであり、ファイル更新や再解析により再生成される。
- ファイルパス表示は [[RULE-MW-PATH-SHORTENER]] に従って短縮してよい。
- filterModelIndex / rebuildVaultModelIndex / openModelFile は、それぞれ [[PROC-MW-MODEL-INDEX-FILTER]] / [[PROC-MW-VAULT-INDEX-REBUILD]] / [[PROC-MW-MODEL-FILE-OPEN]] として詳細化済みである。
- selectModelAsset は現時点では画面内の選択状態更新として扱い、必要に応じて後続で app_process 化する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/core/vault-index.ts | VaultIndex | class | Vault内モデル資産の索引 |
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer表示更新 |
| source/model-weave-repo/src/main.ts | openDiagnosticLocation | function | ファイルジャンプ処理の参考 |