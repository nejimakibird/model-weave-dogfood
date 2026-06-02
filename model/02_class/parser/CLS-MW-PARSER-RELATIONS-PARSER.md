---
type: class
id: CLS-MW-PARSER-RELATIONS-PARSER
name: RelationsParser
kind: class
package: model_weave.parser
tags:
  - Parser
  - Relations
---

# RelationsParser

## Summary

`schema: model_relations_v1` を持つ Relations ファイルを解析します。
Relations は schema-driven なファイル種別であり、通常の `frontmatter.type` 値として扱いません。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| parseRelationsFile | markdown: string, path: string | ParseResult | public | Y | RelationsFileModel を生成する。schema が `model_relations_v1` でない場合は warning を返す |

## Relations

| id | to | kind | label | from_multiplicity | to_multiplicity | notes |
|---|---|---|---|---|---|---|
| REL-RELSP-USES-FM | [[CLS-MW-PARSER-FRONTMATTER-PARSER]] | dependency | uses | | | frontmatter から schema を読む |
| REL-RELSP-USES-DETECTOR | [[CLS-MW-PARSER-MODEL-TYPE-DETECTOR]] | dependency | uses | | | `model_relations_v1` を fileType `relations` として判定する |

## Notes

- `schema: model_relations_v1` のみを Relations ファイルとして受け付ける。
- `## Relations` セクションの箇条書きレコードを解析する。
- レコードは `- id: ..., from: ..., to: ..., kind: ...` 形式を使用する。
- 必須キーは `id`, `from`, `to`, `kind` である。
- 任意キーとして `label`, `from_multiplicity`, `to_multiplicity` を RelationModel に反映する。
- kind は core / reserved relation kind を確認し、未知の kind は warning を出して `association` として扱う。
- `## Source Links` セクションも parseSourceLinks により解析される。
- 解析結果は RelationsFileModel であり、Vault index では relation id ごとに RelationModel が索引される。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/parsers/relations-parser.ts | parseRelationsFile | function | schema-driven Relations parser implementation |
