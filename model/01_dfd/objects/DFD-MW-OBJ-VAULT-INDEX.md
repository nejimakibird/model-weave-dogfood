---
type: dfd_object
id: DFD-MW-OBJ-VAULT-INDEX
name: Vault Index
kind: datastore
tags:
  - DFD
  - Core
  - Vault
---

# Vault Index

## Summary

Vault内Markdownファイルの解析結果を保持する、Model Weave実行時の中心インデックス境界。
ParsedFileModel、fileType別IDマップ、relation/member lookup、warningsByFilePath、構築済み状態を保持する。

## Details

| key | value | notes |
|---|---|---|
| owner | Core Vault Index | vault-index.ts |
| primary_data | [[DATA-MW-VAULT-MODEL-INDEX]] | ModelingVaultIndex |
| parsed_models | [[DATA-MW-CORE-PARSED-MODEL]] | modelsByFilePath |
| warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | warningsByFilePath |
| relations | relationsById | relation id別index |
| lookups | relation / member lookup | ensureRelationLookups / ensureMemberLookups |

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | ModelingVaultIndex / buildVaultIndex |
