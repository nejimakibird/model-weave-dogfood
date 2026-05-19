---
type: class
id: CLS-MW-PARSER-CLASS-PARSER
name: ClassParser
kind: class
package: model_weave.parser
tags:
  - Parser
  - Class
---

# ClassParser

## Summary

`type: class` (または旧 `model_object_v1`) を解析します。
属性、メソッド、およびそのクラスから外部へ向かう関連 (Relations) を抽出します。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| parseObjectFile | markdown: string, path: string | ParseResult | public | Y | Class 内部モデルを生成する |

## Relations

| id | to | kind | label | from_multiplicity | to_multiplicity | notes |
|---|---|---|---|---|---|---|
| REL-CLSP-USES-TABLE | [[CLS-MW-PARSER-MARKDOWN-TABLE-PARSER]] | dependency | uses | | | |