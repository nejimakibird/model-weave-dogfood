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
| parseMarkdownTable | lines: string, headers: string | TableResult | public | Y | 行配列をパースして正規化された行オブジェクト群を返す。linesとheadersは論理的には複数値 |
| splitMarkdownTableRow | line: string | string | public | Y | 単一行をセパレータで分割する。戻り値は論理的には複数値 |

## Relations

| id | to | kind | label | from_multiplicity | to_multiplicity | notes |
|---|---|---|---|---|---|---|

## Notes

- Markdownテーブル安全性のため、Methodsの型表記は単純型で表現し、複数性はnotesで表現する。
