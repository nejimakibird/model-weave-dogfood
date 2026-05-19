---
type: rule
id: RULE-MW-PARSER-FRONTMATTER-SCHEMA
name: フロントマター検証ルール
tags:
  - Parser
  - Core
  - Validation
---

# フロントマター検証ルール

## Summary

Model Weave の各Markdownモデルファイルにおけるフロントマター（YAML領域）の必須項目および値を検証します。本ルールはパーサーの初期段階で適用され、検証結果は `DATA-MW-CORE-DIAGNOSTIC` として集約されます。

## Conditions

- フロントマター（`---` で囲まれた領域）を持つすべての Model Weave 管理対象ファイルに適用する。
- `type` フィールドが指定されている場合、その値は `[[CODE-MW-CORE-MODEL-TYPE]]` に含まれる必要がある。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| fm | Record<string, any> | FrontmatterParser | Y | 解析済みのYAMLオブジェクト |
| types | [[CODE-MW-CORE-MODEL-TYPE]] | CodeSet | Y | 有効なモデル種別リスト |

## Required Frontmatter Policy

各フィールドの必須性（Required）およびバリデーション方針を以下に定義します。

| field | required | validation | notes |
|---|---|---|---|
| type | Y | CODE-MW-CORE-MODEL-TYPE と一致すること | アセット種別の特定に必須 |
| id | Y | 文字列であること | システム内のユニーク識別子 |
| name | Y | 文字列であること | 表示名称。将来的に技術ファイルで緩和の可能性あり |
| tags | N | Array<string> | 任意。分類用タグ |
| render_mode | N | auto / custom / mermaid | 対応フォーマットでのみ有効 |
| kind | N | 文字列 | フォーマットごとの詳細分類（enum, status 等） |
| package | N | 文字列 | class 等で使用される論理階層 |
| screen_type | screenのみ | 文字列 | screen モデルの場合は必須 |

## Diagnostics Mapping

不備検出時に生成される診断コードと深刻度の対応です。

| condition | code | severity |
|---|---|---|
| フロントマターの構文エラー（YAML解析失敗） | `frontmatter-parse-error` | error |
| `type` フィールドが存在しない | `missing-required-frontmatter` | error |
| `type` の値が定義済みリストに存在しない | `unknown-schema` | error |
| `id` フィールドが存在しない | `missing-required-frontmatter` | error |
| `name` フィールドが存在しない | `missing-name` | error |
| 非対応フォーマットで `render_mode` が指定された | `unsupported-render-mode-fallback` | warning |

## Notes

- 本ルールによる検証を通過しないファイルは、インデックス構築プロセスにおいて「不明なファイル（markdown）」として扱われる場合があります。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/parsers/frontmatter-parser.ts | parseYamlLikeFrontmatter | function | YAML形式の解析 |
| source/model-weave-repo/src/core/schema-detector.ts | detectFileType | function | 種別判定ロジック |
| source/model-weave-repo/src/core/vault-index.ts | parseVaultFile | function | 検証とパーサーの振り分け |