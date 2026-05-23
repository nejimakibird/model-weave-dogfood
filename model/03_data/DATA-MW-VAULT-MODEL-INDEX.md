---
type: data_object
id: DATA-MW-VAULT-MODEL-INDEX
name: Vaultモデルインデックス
kind: index
tags:
  - ModelWeave
  - Vault
  - Index
  - Core
---

# Vaultモデルインデックス

## Summary

Obsidian Vault内で検出されたModel Weaveモデル資産の一覧、ファイル情報、参照解決状態、診断状態を保持する論理インデックスデータ。
Markdownファイル群をViewerや診断、参照解決で扱いやすくするための派生データであり、Markdownモデルの正本ではない。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| indexId | インデックスID | string | | Y | indexId | | Vaultインデックスの識別子 |
| vaultPath | Vaultパス | string | | N | vaultPath | | 対象Vaultのルートまたは論理名 |
| modelFileCount | モデルファイル数 | number | | N | summary.modelFileCount | [[ENT-MW-MODEL-FILE]] | 検出されたMarkdownモデルファイル数 |
| modelAssetCount | モデル資産数 | number | | N | summary.modelAssetCount | [[ENT-MW-MODEL-ASSET]] | frontmatter等から認識されたModelAsset数 |
| referenceCount | 参照数 | number | | N | summary.referenceCount | [[ENT-MW-MODEL-REFERENCE]] | 検出されたモデル間参照数 |
| unresolvedReferenceCount | 未解決参照数 | number | | N | summary.unresolvedReferenceCount | [[ENT-MW-MODEL-REFERENCE]] | resolved=false の参照数 |
| diagnosticCount | 診断件数 | number | | N | summary.diagnosticCount | [[DATA-MW-CORE-DIAGNOSTIC]] | error / warning / info / note の合計 |
| errorCount | エラー件数 | number | | N | summary.errorCount | [[DATA-MW-CORE-DIAGNOSTIC]] | severity=error の件数 |
| warningCount | 警告件数 | number | | N | summary.warningCount | [[DATA-MW-CORE-DIAGNOSTIC]] | severity=warning の件数 |
| modelFiles | モデルファイル一覧 | object | | N | modelFiles | [[ENT-MW-MODEL-FILE]] | 複数のModelFileを保持するマップまたはリスト |
| modelAssets | モデル資産一覧 | object | | N | modelAssets | [[ENT-MW-MODEL-ASSET]] | 複数のModelAssetを保持するマップまたはリスト |
| modelReferences | モデル参照一覧 | object | | N | modelReferences | [[ENT-MW-MODEL-REFERENCE]] | 複数のModelReferenceを保持するマップまたはリスト |
| diagnostics | 診断一覧 | object | | N | diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | 複数のDiagnosticを保持するマップまたはリスト |
| lastIndexedFilePath | 最終解析ファイル | string | | N | lastIndexedFilePath | | 増分更新時に最後に処理したファイル |
| indexStatus | インデックス状態 | string | | N | indexStatus | | ready / indexing / error / stale など |

## Notes

- 本データはVault内Markdownファイルから生成される派生インデックスであり、Markdownモデルの正本ではない。
- modelFiles / modelAssets / modelReferences / diagnostics は論理的には複数要素を保持するが、Markdownテーブル安全性のため type は object とし、複数性はnotesで表現する。
- 参照解決や診断結果は、ファイル更新やVault再読込により再生成される。
- [[ERD-MW-MODEL-REPOSITORY]] はこのインデックスの論理構造をERとして表したものである。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | VaultIndex | class | Vault内モデル資産の索引 |
| src/core/schema-detector.ts | detectModelType | function | Markdownファイル種別の検出 |
| src/core/relation-resolver.ts | resolveDiagramRelations | function | モデル間参照の解決 |
| src/core/current-file-diagnostics.ts | buildCurrentObjectDiagnostics | function | 診断情報の生成 |