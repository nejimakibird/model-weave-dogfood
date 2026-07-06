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

Markdownソースから解析された、Model Weaveの各種モデルに共通するファイルモデル表現。
実装上は BaseFileModel を共通形とし、ParsedFileModel は各 fileType 固有モデルのunionとして扱われる。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| fileType | ファイル種別 | string | | Y | fileType | [[CODE-MW-CORE-MODEL-TYPE]] | ParsedFileModelの判別値 |
| path | ファイルパス | string | | Y | path | | Vault内Markdown path |
| title | 表示タイトル | string | | N | title | | 任意の表示名 |
| frontmatter | frontmatter | object | | Y | frontmatter | | GenericFrontmatter |
| sections | セクション群 | object | | Y | sections | | SectionMap |
| sourceLinks | Source Links | object | | Y | sourceLinks | [[DATA-MW-SOURCE-LINK]] | SourceLink list |
| id | モデルID | string | | N | id | | fileType固有モデルで保持 |
| name | モデル名称 | string | | N | name | | fileType固有モデルで保持 |
| content | Markdown本文 | string | | N | content | | markdown fileTypeのみ |
| parseFile | 解析結果file | object | | N | file | | ParseResult.file |
| warnings | 解析warning | object | | N | warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | ParseResult.warnings |

## Notes

- BaseFileModel の共通フィールドは fileType / path / title / frontmatter / sections / sourceLinks である。
- ParsedFileModel は object / data-object / app-process / screen / codeset / message / rule / mapping / relations / diagram / dfd-object / dfd-diagram / er-entity / markdown のunionである。
- id / name / kind などは fileType 固有モデルで保持されるため、共通必須とは扱わない。
- ParseResult は file と warnings を返す。warnings は ParsedFileModel の直接フィールドではない。
- 旧generic項目の type / tables / diagnostics は現行共通フィールドとしては扱わない。
- Markdownテーブル安全性のため、mapやlistの詳細型は notes で表現する。

## Source Links

| path | notes |
|---|---|
| src/types/models.ts | symbol: BaseFileModel; kind: interface; 共通ファイルモデル形 |
| src/types/models.ts | symbol: ParsedFileModel; kind: type; 解析済みモデルのunion |
| src/types/models.ts | symbol: ParseResult; kind: interface; parser出力 |
| src/types/models.ts | symbol: SourceLink; kind: interface; sourceLinks要素 |
