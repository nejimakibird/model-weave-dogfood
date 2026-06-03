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

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| filePath | id | | | Y | getModelIdロジックによりIDを特定 |
| basename | name | | | Y | 拡張子を除いたファイル基本名 |
| content | type | detectFileType | | Y | フロントマターのtype/schemaから特定 |
| content | kind | parser-specific kind extraction | | N | モデル固有のサブタイプ（Enum/Process等） |
| content | sections | extractMarkdownSections | | Y | ヘッディングによる論理ブロック分割 |
| content | tables | parseMarkdownTable | | Y | セクション内の表構造を抽出 |
| content | diagnostics | parser warnings aggregation | | Y | 解析プロセスで発生した検証警告の集約 |

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | parseVaultFile | function | ファイル種別に応じたパース処理のディスパッチ |
