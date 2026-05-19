---
type: class
id: CLS-MW-PARSER-FRONTMATTER-PARSER
name: FrontmatterParser
kind: class
package: model_weave.parser
tags:
  - Parser
---

# FrontmatterParser

## Summary

Markdown ファイルの先頭にある YAML 形式のフロントマターを抽出し、キー・バリュー形式のオブジェクトへ変換します。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| parseFrontmatter | markdown: string | FrontmatterResult | public | Y | ボディとフロントマターを分離して解析する |

## Relations

| id | to | kind | label | from_multiplicity | to_multiplicity | notes |
|---|---|---|---|---|---|---|