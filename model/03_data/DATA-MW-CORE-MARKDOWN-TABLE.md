---
type: data_object
id: DATA-MW-CORE-MARKDOWN-TABLE
name: 解析済みMarkdownテーブル
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
| headers | ヘッダー列 | Array<string> | | Y | | | |
| rows | 行データ群 | Array<Record<string, string>> | | Y | | | セル値を保持する連想配列のリスト |
| startLine | 開始行番号 | number | | Y | | | |
| endLine | 終了行番号 | number | | Y | | | |

## Notes

- パーサー内部で共通的に使用されるテーブル表現です。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/parsers/markdown-table.ts | getMarkdownTableCellRanges | function | パース結果の行・セル情報に対応 |