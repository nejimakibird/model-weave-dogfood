---
type: data_object
id: DATA-MW-CORE-MARKDOWN-TABLE
name: 解析済みMarkdownテーブル
kind: parsed_table
data_format: object
tags:
  - Core
  - Data
---

# 解析済みMarkdownテーブル

## Summary

Markdownソースから抽出・解析された単一のテーブル構造を保持します。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| sectionName | セクション名 | string | | Y | | | |
| headers | ヘッダー列 | string | | Y | | | 論理的には複数値。Markdownテーブルのヘッダー列を保持 |
| rows | 行データ群 | object | | Y | | | 論理的には複数要素。各行はセル値を保持する連想オブジェクト |
| startLine | 開始行番号 | number | | Y | | | |
| endLine | 終了行番号 | number | | Y | | | |

## Notes

- パーサー内部で共通的に使用されるテーブル表現です。
- Markdownテーブル安全性のため、type列は単純型で表現し、複数性や行オブジェクト構造は notes で表現します。

## Source Links

| path | notes |
|---|---|
| src/parsers/markdown-table.ts | symbol: getMarkdownTableCellRanges; kind: function; パース結果の行・セル情報に対応 |
