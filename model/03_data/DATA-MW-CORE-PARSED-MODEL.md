---
type: data_object
id: DATA-MW-CORE-PARSED-MODEL
name: 解析済みモデル
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
| kind | 詳細分類 | Optional<string> | | N | | | |
| sections | セクション群 | Map<string, Array<string>> | | Y | | | セクション名と本文行のマップ |
| tables | テーブル群 | Array<DATA-MW-CORE-MARKDOWN-TABLE> | | Y | | [[DATA-MW-CORE-MARKDOWN-TABLE]] | セクション内から抽出されたテーブル |
| diagnostics | 診断情報 | Array<DATA-MW-CORE-DIAGNOSTIC> | | Y | | [[DATA-MW-CORE-DIAGNOSTIC]] | |

## Notes

- 物理ファイルの状態に依存せず、プラグイン内部で「設計図面」として扱うための論理データです。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/types/models.ts | ModelFile | type | 各種モデル（ObjectModel, ErEntity等）の総称型 |