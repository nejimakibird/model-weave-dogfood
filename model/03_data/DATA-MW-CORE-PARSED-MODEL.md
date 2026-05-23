---
type: data_object
id: DATA-MW-CORE-PARSED-MODEL
name: 解析済みモデル
kind: parsed_model
data_format: object
tags:
  - Core
  - Data
---

# 解析済みモデル

## Summary

Markdownソースから解析された、Model Weaveの各種モデル（Class, ER, DFD等）の共通抽象表現です。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| id | モデルID | string | | Y | | | frontmatter.id |
| name | モデル名称 | string | | Y | | | frontmatter.name |
| type | モデル種別 | CODE-MW-CORE-MODEL-TYPE | | Y | | [[CODE-MW-CORE-MODEL-TYPE]] | class / er_entity / dfd_diagram 等 |
| kind | 詳細分類 | string | | N | | | 任意項目。frontmatter.kind 等 |
| sections | セクション群 | object | | Y | | | Map構造。セクション名ごとに本文行の複数値を保持 |
| tables | テーブル群 | object | | Y | | [[DATA-MW-CORE-MARKDOWN-TABLE]] | 論理的には複数要素。セクション内から抽出されたテーブル |
| diagnostics | 診断情報 | object | | Y | | [[DATA-MW-CORE-DIAGNOSTIC]] | 論理的には複数要素。解析時の診断情報 |

## Notes

- 物理ファイルの状態に依存せず、プラグイン内部で「設計図面」として扱うための論理データです。
- Markdownテーブル安全性のため、type列は単純型で表現し、複数性・任意性・Map構造は required / notes / ref で表現します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/types/models.ts | ModelFile | type | 各種モデル（ObjectModel, ErEntity等）の総称型 |
