---
type: class
id: CLS-MW-PARSER-VAULT-FILE-PARSER
name: VaultFileParser
kind: class
package: model_weave.parser
tags:
  - Parser
  - Core
---

# VaultFileParser

## Summary

Obsidian Vault 内の全ファイルを走査し、Model Weave の内部インデックスを構築するための統括クラスです。
各ファイルの種別を判定し、適切な具象パーサーへ解析処理を委譲します。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|
| vaultIndex | ModelingVaultIndex | private | N | 解析済みモデルのメモリ内キャッシュ |

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| buildVaultIndex | files: object | ModelingVaultIndex | public | N | Vault 全体のインデックスを再構築する。filesは論理的には複数要素 |
| indexSingleFile | index: ModelingVaultIndex, file: SourceFile | void | private | N | 単一ファイルを解析してインデックスへ追加する |

## Relations

| id | to | kind | label | from_multiplicity | to_multiplicity | notes |
|---|---|---|---|---|---|---|
| REL-VFP-USES-FM | [[CLS-MW-PARSER-FRONTMATTER-PARSER]] | dependency | uses | | | |
| REL-VFP-USES-DETECTOR | [[CLS-MW-PARSER-MODEL-TYPE-DETECTOR]] | dependency | uses | | | |
| REL-VFP-DELEGATES-DFD | [[CLS-MW-PARSER-DFD-DIAGRAM-PARSER]] | association | delegates to | | | |
| REL-VFP-DELEGATES-CLASS | [[CLS-MW-PARSER-CLASS-PARSER]] | association | delegates to | | | |
| REL-VFP-DELEGATES-RELATIONS | [[CLS-MW-PARSER-RELATIONS-PARSER]] | association | delegates to | | | schema-driven Relations files are delegated to parseRelationsFile |

## Notes

- Markdownテーブル安全性のため、Methodsの型表記は単純型で表現し、複数性はnotesで表現する。
