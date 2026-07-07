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
現行実装では、完全に空のデータ行は編集中ノイズとして無視され、部分入力行は通常どおり診断対象として残ります。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| sectionName | セクション名 | string | | Y | | | |
| headers | ヘッダー列 | string | | Y | | | 論理的には複数値。Markdownテーブルのヘッダー列を保持 |
| rows | 行データ群 | object | | Y | | | 論理的には複数要素。各行はセル値を保持する連想オブジェクト |
| skippedEmptyRows | 無視された空行 | object | | N | | | 全セルが空または空白のみのデータ行。編集ノイズとして行データにしない |
| diagnosticRows | 診断対象行 | object | | N | | | 部分入力行や列数不一致行。required-fieldやinvalid-table-rowの対象になり得る |
| startLine | 開始行番号 | number | | Y | | | |
| endLine | 終了行番号 | number | | Y | | | |

## Notes

- パーサー内部で共通的に使用されるテーブル表現です。
- ヘッダー行と区切り行は、empty row handlingの対象ではありません。
- fully empty data row は、全セルが空または空白のみの場合だけ該当します。
- fully empty data row は required-field diagnostics の対象にせず、行データも生成しません。
- partially filled row は無視しません。空セル、余分なpipe、列数不一致、必須値不足があれば診断対象として扱います。
- empty row handling はFORMATの期待ヘッダーを変更せず、unsupported columnを許容するものでもありません。
- Markdownテーブル安全性のため、type列は単純型で表現し、複数性や行オブジェクト構造は notes で表現します。

## Source Links

| path | notes |
|---|---|
| src/parsers/markdown-table.ts | symbol: parseMarkdownTable; kind: function; ヘッダー検証、row分割、empty data row除外 |
| src/parsers/markdown-table.ts | symbol: isEmptyMarkdownTableDataRow; kind: function; 全セル空白のデータ行を判定 |
| src/parsers/markdown-table.ts | symbol: getMarkdownTableCellRanges; kind: function; パース結果の行・セル情報に対応 |
