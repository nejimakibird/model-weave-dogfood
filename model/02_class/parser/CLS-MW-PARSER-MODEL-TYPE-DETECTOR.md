---
type: class
id: CLS-MW-PARSER-MODEL-TYPE-DETECTOR
name: ModelTypeDetector
kind: class
package: model_weave.parser
tags:
  - Parser
---

# ModelTypeDetector

## Summary

フロントマターの `type` または `schema` プロパティを参照し、そのファイルがどの設計フォーマット（DFD, Class, ER 等）に該当するかを判定します。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|
| SCHEMA_MAP | Record<string, string> | private | Y | schema 値から fileType へのマッピング |
| TYPE_MAP | Record<string, string> | private | Y | type 値から fileType へのマッピング |

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| detectFileType | frontmatter: any | FileType | public | Y | フォーマット識別子を返す |

## Relations

| id | to | kind | label | from_multiplicity | to_multiplicity | notes |
|---|---|---|---|---|---|---|