---
type: mapping
id: MAP-MW-VAULT-INDEX-TO-MODEL-INDEX-UI
name: Vault Index to Model Index UI Mapping
kind: data_to_screen
source: "[[DATA-MW-VAULT-MODEL-INDEX]]"
target: "[[SCR-MW-MODEL-INDEX-VIEW]]"
tags:
  - ModelWeave
  - Vault
  - Index
  - Mapping
  - UI
---

# Vault Index to Model Index UI Mapping

## Summary

[[DATA-MW-VAULT-MODEL-INDEX]] が保持するVault内モデル資産の件数、診断状態、モデル一覧を、future / plannedの [[SCR-MW-MODEL-INDEX-VIEW]] の各UI表示項目へ反映するためのマッピングを定義する。
Model Weave 0.1.17時点では内部Vault Indexは実装済みだが、独立したModel Index ViewへのUI反映は本体srcで確認できない。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-VAULT-MODEL-INDEX]] | Vault内モデル資産の派生インデックス |
| source | [[ENT-MW-MODEL-ASSET]] | モデル資産の論理構造 |
| source | [[ENT-MW-MODEL-REFERENCE]] | モデル参照の論理構造 |
| source | [[DATA-MW-CORE-DIAGNOSTIC]] | 診断情報 |
| target | [[SCR-MW-MODEL-INDEX-VIEW]] | Model Index表示画面 |
| rule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | 診断severity表示 |
| rule | [[RULE-MW-PATH-SHORTENER]] | ファイルパス短縮表示 |
| mapping | [[MAP-MW-DIAGNOSTIC-TO-SUMMARY-TEXT]] | 診断サマリーテキスト生成 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[DATA-MW-VAULT-MODEL-INDEX]].modelsByFilePath | [[SCR-MW-MODEL-INDEX-VIEW]].modelFileCountLabel | collection件数を表示 | | N | Vault内の解析済みモデルファイル数 |
| [[DATA-MW-VAULT-MODEL-INDEX]].modelsByFilePath | [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetCountLabel | 表示対象モデル件数を算出 | | N | ModelAsset相当の表示件数 |
| [[DATA-MW-VAULT-MODEL-INDEX]].warningsByFilePath | [[SCR-MW-MODEL-INDEX-VIEW]].unresolvedReferenceCountLabel | unresolved-reference件数を集計 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 0より大きい場合はwarning相当 |
| [[DATA-MW-VAULT-MODEL-INDEX]].warningsByFilePath | [[SCR-MW-MODEL-INDEX-VIEW]].diagnosticSummaryLabel | severity別に集計 | [[MAP-MW-DIAGNOSTIC-TO-SUMMARY-TEXT]] | N | 例: 2 Errors / 3 Warnings |
| [[DATA-MW-VAULT-MODEL-INDEX]].modelsByFilePath | [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetList | フィルタ条件に従って一覧化 | | Y | ParsedFileModelをModelAsset相当の行として表示 |
| [[ENT-MW-MODEL-ASSET]].assetId | [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetIdLabel | そのまま表示 | | N | モデル資産ID |
| [[ENT-MW-MODEL-ASSET]].modelType | [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetTypeLabel | そのまま表示 | | N | モデル種別 |
| [[ENT-MW-MODEL-ASSET]].name | [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetNameLabel | そのまま表示 | | N | 表示名 |
| [[ENT-MW-MODEL-ASSET]].filePath | [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetPathLabel | 短縮表示 | [[RULE-MW-PATH-SHORTENER]] | N | 完全パスは内部的に保持 |
| [[DATA-MW-CORE-DIAGNOSTIC]].severity | [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetDiagnosticBadge | バッジ変換 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 対象ModelAssetに紐づく診断状態 |

## Rules

- 件数系フィールドは [[DATA-MW-VAULT-MODEL-INDEX]].modelsByFilePath / warningsByFilePath から派生計算する。
- 診断サマリーは [[DATA-MW-VAULT-MODEL-INDEX]].warningsByFilePath を [[MAP-MW-DIAGNOSTIC-TO-SUMMARY-TEXT]] に従って短い文字列へ変換する。
- `modelAssetList` は `modelTypeFilter` / `diagnosticFilter` / `searchTextInput` の条件により絞り込まれる。
- `modelAssetPathLabel` は [[RULE-MW-PATH-SHORTENER]] に従い、表示上のみ短縮する。
- ファイルジャンプや参照解決では、短縮表示ではなく完全な `filePath` を使用する。
- `modelAssetDiagnosticBadge` は対象ModelAssetに紐づく診断の最も高いseverityを表示する。
- 診断がないModelAssetは clean または空表示にしてよい。
- 本mappingは表示用の派生変換であり、Markdown本文やfrontmatterは変更しない。

## Notes

- [[DATA-MW-VAULT-MODEL-INDEX]] はVault内Markdownファイルから生成される派生データである。
- [[SCR-MW-MODEL-INDEX-VIEW]] はfuture / plannedのインデックス閲覧UIであり、正本のMarkdownモデルを直接変更しない。
- 現行実装済みのVault Index利用はPreview、Relationship View、診断、描画支援の内部処理であり、この独立画面への表示mappingとは別に扱う。
- `filterModelIndex` / `rebuildVaultModelIndex` / `openModelFile` は論理プロセス名であり、後続でapp_process化できる。
- `modelAssetList` 内の行表示は [[SCR-MW-MODEL-INDEX-VIEW]] の `model_asset_row` レイアウトに従う。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | VaultIndex | class | Vault内モデル資産の索引 |
| src/core/current-file-diagnostics.ts | buildCurrentObjectDiagnostics | function | 診断情報の生成 |
| src/main.ts | openDiagnosticLocation | function | ファイルジャンプ処理の参考 |
