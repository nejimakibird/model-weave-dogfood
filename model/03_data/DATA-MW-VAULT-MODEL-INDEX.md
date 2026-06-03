---
type: data_object
id: DATA-MW-VAULT-MODEL-INDEX
name: Vaultモデルインデックス
kind: index
data_format: object
tags:
  - ModelWeave
  - Vault
  - Index
  - Core
---

# Vaultモデルインデックス

## Summary

Obsidian Vault内のMarkdownファイルを解析して構築される、Model Weaveの実行時インデックスデータ。
modelsByFilePath と各種ID別マップ、relation/member lookup、warningsByFilePath、構築済み状態を保持する。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| sourceFilesByPath | 入力ファイルマップ | object | | Y | sourceFilesByPath | [[DATA-MW-CORE-VAULT-FILE]] | path別のVaultFileInput |
| modelsByFilePath | 解析済みモデルマップ | object | | Y | modelsByFilePath | [[DATA-MW-CORE-PARSED-MODEL]] | path別のParsedFileModel |
| objectsById | Classモデルマップ | object | | N | objectsById | [[DATA-MW-CORE-PARSED-MODEL]] | object fileTypeのID別マップ |
| appProcessesById | Processモデルマップ | object | | N | appProcessesById | [[DATA-MW-CORE-PARSED-MODEL]] | app-process fileTypeのID別マップ |
| screensById | Screenモデルマップ | object | | N | screensById | [[DATA-MW-CORE-PARSED-MODEL]] | screen fileTypeのID別マップ |
| dataObjectsById | Data Objectマップ | object | | N | dataObjectsById | [[DATA-MW-CORE-PARSED-MODEL]] | data-object fileTypeのID別マップ |
| dfdObjectsById | DFD Objectマップ | object | | N | dfdObjectsById | [[DATA-MW-CORE-PARSED-MODEL]] | dfd-object fileTypeのID別マップ |
| erEntitiesById | ER Entityマップ | object | | N | erEntitiesById | [[DATA-MW-CORE-PARSED-MODEL]] | er-entity fileTypeのID別マップ |
| relationsFilesById | Relationsファイルマップ | object | | N | relationsFilesById | [[DATA-MW-CORE-PARSED-MODEL]] | relations fileTypeのID別マップ |
| diagramsById | Diagramマップ | object | | N | diagramsById | [[DATA-MW-CORE-PARSED-MODEL]] | diagram / dfd-diagramのID別マップ |
| relationsById | Relationマップ | object | | N | relationsById | | relation id別のRelationModel |
| relationsByObjectId | Object別Relation lookup | object | | N | relationsByObjectId | | object id別のRelationModel list |
| membersByOwnerId | Owner別Member lookup | object | | N | membersByOwnerId | | owner id別のQualifiedMemberCandidate list |
| membersByOwnerPath | Path別Member lookup | object | | N | membersByOwnerPath | | owner path別のQualifiedMemberCandidate list |
| warningsByFilePath | 診断マップ | object | | N | warningsByFilePath | [[DATA-MW-CORE-DIAGNOSTIC]] | file path別のValidationWarning list |
| relationLookupsBuilt | Relation lookup構築済み | boolean | | Y | state.relationLookupsBuilt | | ensureRelationLookups の状態 |
| memberLookupsBuilt | Member lookup構築済み | boolean | | Y | state.memberLookupsBuilt | | ensureMemberLookups の状態 |
| vaultValidationBuilt | Vault検証済み | boolean | | Y | state.vaultValidationBuilt | | ensureVaultValidation の状態 |
| fullParsedFilePaths | full parse済みファイル | object | | N | state.fullParsedFilePaths | | full parse済みpathのマップ |

## Notes

- 本データはVault内Markdownファイルから生成される派生インデックスであり、Markdownモデルの正本ではない。
- 現行実装には汎用の modelAssets / modelReferences マップはなく、fileType別のIDマップと modelsByFilePath を保持する。
- 現行実装には専用の sourceLinks index はなく、SourceLink は各 ParsedFileModel の sourceLinks に保持される。
- relationsByObjectId は ensureRelationLookups で再構築される。
- membersByOwnerId / membersByOwnerPath は ensureMemberLookups で再構築される。
- warningsByFilePath は parser warning、validator warning、重複ID warningを保持する。
- Markdownテーブル安全性のため、mapやlistの詳細型は notes で表現する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | ModelingVaultIndex | interface | Vault内モデル資産の索引 |
| src/core/vault-index.ts | buildVaultIndex | function | Indexの新規構築 |
| src/core/vault-index.ts | indexSingleFile | function | 個別ファイルの解析と登録 |
| src/core/vault-index.ts | ensureRelationLookups | function | relation lookup構築 |
| src/core/vault-index.ts | ensureMemberLookups | function | member lookup構築 |
| src/core/vault-index.ts | ensureVaultValidation | function | Vault検証 |
