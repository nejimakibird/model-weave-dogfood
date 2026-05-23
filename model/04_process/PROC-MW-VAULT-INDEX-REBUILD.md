---
type: app_process
id: PROC-MW-VAULT-INDEX-REBUILD
name: Vaultモデルインデックス再構築
process_type: batch
tags:
  - ModelWeave
  - Vault
  - Index
  - Process
---

# Vaultモデルインデックス再構築

## Summary

Obsidian Vault内のMarkdownファイルを走査し、Model Weaveモデル資産、参照、診断情報を検出して [[DATA-MW-VAULT-MODEL-INDEX]] を再構築する処理。
再構築結果は Model Index View や Viewer診断、参照解決に利用される。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| vaultFiles | [[ENT-MW-MODEL-FILE]] | Obsidian Vault | Y | Vault内のMarkdownファイル群。論理的には複数ファイルを対象とする |
| existingIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | Index Cache | N | 既存インデックス。増分更新時に解析差分を確認するために参照 |
| modelRepositoryEr | [[ERD-MW-MODEL-REPOSITORY]] | Dogfood ER | N | 論理構造（Entity/Relation）の整合性参照 |
| diagnosticsRule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Rule | N | 検出された不備の診断severity分類ルール |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| rebuiltIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | Index | 再構築されたVaultモデルインデックスのルート |
| detectedAssets | [[ENT-MW-MODEL-ASSET]] | Index | 検出されたModelAssetの集合 |
| detectedReferences | [[ENT-MW-MODEL-REFERENCE]] | Index | 検出されたモデル間参照の集合 |
| diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | Diagnostics | 解析・参照解決・形式検証で生成された診断情報の集合 |

## Steps

1. Vault内のMarkdownファイルを列挙する。
2. 対象ファイルごとにfrontmatterと本文セクション（Heading構造）を解析する。
3. `detectModelType` を使用して、Model Weave対象ファイル（DFD, Class, Data Object等）かどうかを判定する。
4. 対象ファイルから [[ENT-MW-MODEL-FILE]] および [[ENT-MW-MODEL-ASSET]] を生成する。
5. 本文内のWikilink、`ref` 列によるID参照、Source Links、mapping定義等を抽出し、[[ENT-MW-MODEL-REFERENCE]] を生成する。
6. `resolveDiagramRelations` を実行して参照先ModelAssetを解決し、referenceの `resolved` 状態を更新する。
7. 解析プロセスで発生した未解決参照、テーブルヘッダー不一致、frontmatter不備、循環参照等を Diagnostic として集約する。
8. 各資産・参照・診断の件数サマリーを計算し、[[DATA-MW-VAULT-MODEL-INDEX]] を構築（または更新）する。
9. [[SCR-MW-MODEL-INDEX-VIEW]] に最新状態を表示できるよう、[[MAP-MW-VAULT-INDEX-TO-MODEL-INDEX-UI]] へ解決結果を通知する。

## Messages

| id | text | severity | notes |
|---|---|---|---|
| indexRebuilt | Model index rebuilt. | info | 再構築が正常に完了した際の通知 |
| indexHasWarnings | Model index has warnings. | warning | 再構築は完了したが、解析データに不備（warning）がある場合 |
| indexHasErrors | Model index has errors. | error | 解析データに重大な不備（error）がある場合 |
| indexRebuildFailed | Failed to rebuild model index. | error | 処理自体の例外やVaultアクセスエラーによる失敗 |

## Notes

- 本処理はVault内Markdownファイルから派生インデックスを生成するものであり、Markdownモデル（正本）の内容やフロントマターを書き換えることはない。
- [[DATA-MW-VAULT-MODEL-INDEX]] は永続的な正本ではなく、キャッシュやメモリ上で保持され、必要に応じて再生成される派生データである。
- Source LinksやWikilinkの解決失敗は、モデル自体のエラーではなく [[ENT-MW-MODEL-REFERENCE]] の未解決状態および Diagnostic として管理される。
- `rebuildVaultModelIndex` は [[SCR-MW-MODEL-INDEX-VIEW]] から呼び出される論理プロセスであり、本定義はその実体となるアルゴリズムを示す。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | VaultIndex | class | Vault内モデル資産の索引管理 |
| src/core/schema-detector.ts | detectModelType | function | Markdownファイル種別の自動検出 |
| src/core/relation-resolver.ts | resolveDiagramRelations | function | モデル間参照の解決ロジック |
| src/core/current-file-diagnostics.ts | buildCurrentObjectDiagnostics | function | 診断情報の解析と生成 |