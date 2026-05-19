---
type: class_diagram
id: CLASSD-MW-PARSER-STRUCTURE
name: Model Weave Parser Structure
tags:
  - Class
  - Parser
---

# Model Weave Parser Structure

## Summary

Model Weave におけるパーサーパッケージの責務構造を定義します。
Vault 全体のファイルを一括して扱う `VaultFileParser` を起点に、フロントマターやテーブル解析などの共通部品と、フォーマット別の具象パーサーの依存関係を示します。

## Objects

| ref | notes |
|---|---|
| [[CLS-MW-PARSER-VAULT-FILE-PARSER]] | Vault 内の全ファイルを走査・解析する統括クラス |
| [[CLS-MW-PARSER-FRONTMATTER-PARSER]] | YAML 形式のフロントマター抽出を担当 |
| [[CLS-MW-PARSER-MODEL-TYPE-DETECTOR]] | フロントマターからフォーマット種別を判定 |
| [[CLS-MW-PARSER-MARKDOWN-TABLE-PARSER]] | Markdown 内のテーブル構造の解析 |
| [[CLS-MW-PARSER-DFD-DIAGRAM-PARSER]] | DFD 固有のセクション・テーブル解析 |
| [[CLS-MW-PARSER-CLASS-PARSER]] | クラス・インターフェース定義の解析 |

## Relations

| id  | from                                 | to                                      | kind        | label        | from_multiplicity | to_multiplicity | notes |
| --- | ------------------------------------ | --------------------------------------- | ----------- | ------------ | ----------------- | --------------- | ----- |
| R01 | [[CLS-MW-PARSER-VAULT-FILE-PARSER]]  | [[CLS-MW-PARSER-FRONTMATTER-PARSER]]    | dependency  | uses         |                   |                 |       |
| R02 | [[CLS-MW-PARSER-VAULT-FILE-PARSER]]  | [[CLS-MW-PARSER-MODEL-TYPE-DETECTOR]]   | dependency  | uses         |                   |                 |       |
| R03 | [[CLS-MW-PARSER-VAULT-FILE-PARSER]]  | [[CLS-MW-PARSER-DFD-DIAGRAM-PARSER]]    | association | delegates to |                   |                 |       |
| R04 | [[CLS-MW-PARSER-VAULT-FILE-PARSER]]  | [[CLS-MW-PARSER-CLASS-PARSER]]          | association | delegates to |                   |                 |       |
| R05 | [[CLS-MW-PARSER-DFD-DIAGRAM-PARSER]] | [[CLS-MW-PARSER-MARKDOWN-TABLE-PARSER]] | dependency  | uses         |                   |                 |       |
| R06 | [[CLS-MW-PARSER-CLASS-PARSER]]       | [[CLS-MW-PARSER-MARKDOWN-TABLE-PARSER]] | dependency  | uses         |                   |                 |       |

## Notes

- 各クラスの詳細は個別のクラス定義ファイルを参照してください。
- 解析結果は最終的に `VaultIndex` へ集約されます。