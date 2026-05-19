---
type: class
id: CLS-MW-PARSER-DFD-DIAGRAM-PARSER
name: DfdDiagramParser
kind: class
package: model_weave.parser
tags:
  - Parser
  - DFD
---

# DfdDiagramParser

## Summary

`type: dfd_diagram` を持つファイルを解析します。
`Objects` セクションによるノード定義と、`Flows` セクションによるデータフロー定義を抽出します。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| parseDfdDiagramFile | markdown: string, path: string | ParseResult | public | Y | DFD 内部モデルを生成する |

## Relations

| id | to | kind | label | from_multiplicity | to_multiplicity | notes |
|---|---|---|---|---|---|---|
| REL-DFDP-USES-TABLE | [[CLS-MW-PARSER-MARKDOWN-TABLE-PARSER]] | dependency | uses | | | |