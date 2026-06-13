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

Obsidian Vault内のMarkdownファイルを走査し、buildVaultIndex で [[DATA-MW-VAULT-MODEL-INDEX]] を再構築する処理。
parseMode に応じて shallow / full の入力を作り、full parse では関係ルックアップ、メンバー索引、Vault検証も構築する。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Model Weave architecture domains |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| vaultFiles | [[ENT-MW-MODEL-FILE]] | Obsidian Vault | Y | Markdownファイル群 |
| parseMode | option | Plugin | N | shallow / full |
| buildOptions | option | Plugin | N | relations / members / validation |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| rebuiltIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | Index | 再構築されたVaultモデルインデックスのルート |
| detectedAssets | [[ENT-MW-MODEL-ASSET]] | Index | 検出されたModelAssetの集合 |
| detectedReferences | [[ENT-MW-MODEL-REFERENCE]] | Index | 検出されたモデル間参照の集合 |
| diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | Diagnostics | warningsByFilePath に保持される診断 |

## Steps

| id                   | domain              | label                  | kind     | input         | output        | rule | invoke                          | screen | notes                                |
| -------------------- | ------------------- | ---------------------- | -------- | ------------- | ------------- | ---- | ------------------------------- | ------ | ------------------------------------ |
| start                | editor_integration  | Index再構築開始             | start    | vaultFiles    |               |      |                                 |        | rebuildIndex から開始する                  |
| collectFiles         | vault_content       | Markdownファイルを列挙する      | input    | vaultFiles    | markdownFiles |      |                                 |        | getMarkdownFiles を使う                 |
| chooseMode           | editor_integration  | parseModeを判定する         | decision | parseMode     |               |      |                                 |        | 既定は shallow                          |
| readFullContent      | vault_content       | 本文を読み込む                | process  | markdownFiles | fullFiles     |      |                                 |        | full parse では cachedRead を使う         |
| readFrontmatterCache | vault_content       | frontmatterを取得する       | process  | markdownFiles | shallowFiles  |      |                                 |        | shallow parse では cache を使う           |
| buildIndex           | model_storage       | Vault Indexを構築する       | subflow  | fileInput     | rebuiltIndex  |      | [[PROC-MW-PARSER-PARSE-VAULT-FILE]] |        | buildVaultIndex が各ファイルを解析する          |
| markFullParsed       | model_storage       | full解析済みを記録する          | process  | rebuiltIndex  | rebuiltIndex  |      |                                 |        | full parse のファイルパスを保持する              |
| duplicateDiagnostics | diagnostics         | 重複ID診断を再計算する           | process  | rebuiltIndex  | diagnostics   |      |                                 |        | recomputeDuplicateModelIdDiagnostics |
| shouldResolve        | model_storage       | relation lookup要否を判定する | decision | buildOptions  |               |      |                                 |        | resolveRelations option              |
| relationLookups      | relation_resolution | relation lookupを構築する   | process  | rebuiltIndex  | rebuiltIndex  |      |                                 |        | ensureRelationLookups                |
| shouldIndexMembers   | model_storage       | member lookup要否を判定する   | decision | buildOptions  |               |      |                                 |        | indexMembers option                  |
| memberLookups        | model_storage       | member lookupを構築する     | process  | rebuiltIndex  | rebuiltIndex  |      |                                 |        | ensureMemberLookups                  |
| shouldValidate       | model_storage       | Vault検証要否を判定する         | decision | buildOptions  |               |      |                                 |        | validate option                      |
| vaultValidation      | diagnostics         | Vault検証を実行する           | process  | rebuiltIndex  | diagnostics   |      |                                 |        | ensureVaultValidation                |
| end                  | model_storage       | Index再構築完了             | end      | rebuiltIndex  |               |      |                                 |        | this.index に保持される                    |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| chooseMode | readFullContent | `parseMode === "full"` | full | 本文を読む |
| chooseMode | readFrontmatterCache | `parseMode !== "full"` | shallow | cacheを使う |
| readFullContent | buildIndex |  | build | fullFiles を渡す |
| readFrontmatterCache | buildIndex |  | build | shallowFiles を渡す |
| buildIndex | markFullParsed | `parseMode === "full"` | full parsed | full解析済みを記録する |
| buildIndex | duplicateDiagnostics | `parseMode !== "full"` | shallow parsed | 診断再計算へ進む |
| markFullParsed | duplicateDiagnostics |  | diagnostics | 重複ID診断へ進む |
| shouldResolve | relationLookups | `resolveRelations === true` | resolve | relation lookupを構築する |
| shouldResolve | shouldIndexMembers | `resolveRelations !== true` | skip | member判定へ進む |
| relationLookups | shouldIndexMembers |  | next | member判定へ進む |
| shouldIndexMembers | memberLookups | `indexMembers === true` | index | member lookupを構築する |
| shouldIndexMembers | shouldValidate | `indexMembers !== true` | skip | validation判定へ進む |
| memberLookups | shouldValidate |  | next | validation判定へ進む |
| shouldValidate | vaultValidation | `validate === true` | validate | Vault検証を行う |
| shouldValidate | end | `validate !== true` | done | 検証せず完了する |
| vaultValidation | end |  | done | 検証後に完了する |

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
- rebuildIndex は shallow を既定とし、明示された場合に full parse を行う。
- buildVaultIndex は options が未指定の場合、relations / members / validation を有効として扱う。
- main.ts の rebuildIndex は full parse のときだけ relations / members / validation を有効にしている。
- indexSingleFile の詳細なパース分岐は [[PROC-MW-PARSER-PARSE-VAULT-FILE]] を参照する。

## Source Links

| path | notes |
|---|---|
| src/main.ts | Steps: start, collectFiles, readFullContent, readFrontmatterCache. ModelWeavePlugin.rebuildIndex がVaultファイル入力を作る |
| src/core/vault-index.ts | Steps: buildIndex, markFullParsed. buildVaultIndex / indexSingleFile が解析とindex登録を行う |
| src/core/vault-index.ts | Steps: shouldResolve, relationLookups. ensureRelationLookups がrelation lookupを構築する |
| src/core/vault-index.ts | Steps: shouldIndexMembers, memberLookups. ensureMemberLookups がmember lookupを構築する |
| src/core/vault-index.ts | Steps: duplicateDiagnostics, shouldValidate, vaultValidation. duplicate diagnostics / ensureVaultValidation が診断を構築する |
