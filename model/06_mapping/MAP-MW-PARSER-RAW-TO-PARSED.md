---
type: mapping
id: MAP-MW-PARSER-RAW-TO-PARSED
name: 物理ファイルから論理モデルへの変換
kind: data_to_data
source: "[[DATA-MW-CORE-VAULT-FILE]]"
target: "[[DATA-MW-CORE-PARSED-MODEL]]"
tags:
  - Core
  - Parser
  - Mapping
---

# 物理ファイルから論理モデルへの変換

## Summary

Obsidian Vault上の物理ファイル（DATA-MW-CORE-VAULT-FILE）から、Model Weaveの各種モデルを表現する論理的な解析済みモデル（DATA-MW-CORE-PARSED-MODEL）への項目マッピングを定義します。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-CORE-VAULT-FILE]] | 入力となる物理ファイル実体とメタデータ |
| target | [[DATA-MW-CORE-PARSED-MODEL]] | 出力となる種別判定済みの論理モデル |

## Mappings

| target_ref | source_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| id | filePath |  |  | Y | getModelIdロジックによりIDを特定 |
| name | basename |  |  | Y | 拡張子を除いたファイル基本名 |
| type | content | detectFileType |  | Y | フロントマターのtype/schemaから特定 |
| kind | content | parser-specific kind extraction |  | N | モデル固有のサブタイプ（Enum/Process等） |
| sections | content | extractMarkdownSections |  | Y | ヘッディングによる論理ブロック分割 |
| tables | content | parseMarkdownTable |  | Y | セクション内の表構造を抽出 |
| diagnostics | content | parser warnings aggregation |  | Y | 解析プロセスで発生した検証警告の集約 |

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | symbol: parseVaultFile; kind: function; ファイル種別に応じたパース処理のディスパッチ |
