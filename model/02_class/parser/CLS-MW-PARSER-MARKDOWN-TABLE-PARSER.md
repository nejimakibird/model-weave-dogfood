---
type: class
id: CLS-MW-PARSER-MARKDOWN-TABLE-PARSER
name: MarkdownTableParser
kind: class
package: model_weave.parser
tags:
  - Parser
  - Utility
---

# MarkdownTableParser

## Summary

Markdown セクション内のテーブル構造を解析する共通ユーティリティです。
エスケープされたパイプ記号や Wikilink を考慮した列の分割処理を担当します。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| parseMarkdownTable | lines: Array<string>, headers: Array<string> | TableResult | public | Y | 行配列をパースして正規化された行オブジェクト群を返す |
| splitMarkdownTableRow | line: string | Array<string> | public | Y | 単一行をセパレータで分割する |

## Relations

| id | to | kind | label | from_multiplicity | to_multiplicity | notes |
|---|---|---|---|---|---|---|