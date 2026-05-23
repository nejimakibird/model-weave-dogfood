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
| SCHEMA_MAP | object | private | Y | schema 値から fileType へのマッピング。Record構造 |
| TYPE_MAP | object | private | Y | type 値から fileType へのマッピング。Record構造 |

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| detectFileType | frontmatter: any | FileType | public | Y | フォーマット識別子を返す |

## Relations

| id | to | kind | label | from_multiplicity | to_multiplicity | notes |
|---|---|---|---|---|---|---|

## Notes

- Markdownテーブル安全性のため、Attributesの型表記は単純型で表現し、Record構造はnotesで表現する。
