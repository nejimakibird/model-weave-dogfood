---
type: rule
id: RULE-MW-PARSER-SECTION-STRUCTURE
name: セクション構造検証ルール
kind: parser_rule
tags:
  - Parser
  - Core
  - Validation
---

# セクション構造検証ルール

## Summary

Model Weave の各Markdownモデルファイルにおいて、`frontmatter.type` に応じて期待されるセクション構造と、そのセクション内のテーブルヘッダーを定義します。本ルールはパーサーの初期段階で適用され、`expected headers mismatch` などの診断情報の根拠となります。
v0.1.18以降は、完全に空のMarkdown table data rowを編集中ノイズとして無視し、部分入力行は通常の検証対象として扱います。

## Conditions

- `frontmatter.type` が `[[CODE-MW-CORE-MODEL-TYPE]]` に含まれる Model Weave 管理対象ファイルに適用する。
- 各 `type` に定義された必須セクションの存在と、テーブルセクションのヘッダーが期待値と一致するかを検証する。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| model | [[DATA-MW-CORE-PARSED-MODEL]] | Parser | Y | 解析対象のモデル |
| fileType | CODE-MW-CORE-MODEL-TYPE | Parser | Y | モデルの種別 |

## Section Policy

このテーブルは、各モデルタイプが持つべきセクションと、そのセクション内のテーブルが期待するヘッダーを定義します。これは `FORMAT-*.md` ファイルの補足情報であり、AI生成時のガイドラインとして使用されます。

### Type: data_object

| section | required / recommended | expected_headers | notes |
|---|---|---|---|
| Fields | Required | `name / label / type / length / required / path / ref / notes` | |

### Type: app_process

| section | required / recommended | expected_headers | notes |
|---|---|---|---|
| Triggers | Recommended | `id / kind / source / event / notes` | |
| Inputs | Recommended | `id / data / source / required / notes` | |
| Outputs | Recommended | `id / data / target / notes` | |
| Transitions | Recommended | `id / event / to / condition / notes` | |
| Steps | Recommended | (テキストセクション) | |
| Errors | Recommended | (テキストセクション) | |
| Notes | Recommended | (テキストセクション) | |

### Type: mapping

| section | required / recommended | expected_headers | notes |
|---|---|---|---|
| Scope | Required | `role / ref / notes` | |
| Mappings | Required | `source_ref / target_ref / transform / rule / required / notes` | |

### Type: class

| section | required / recommended | expected_headers | notes |
|---|---|---|---|
| Attributes | Recommended | `name / type / visibility / static / notes` | |
| Methods | Recommended | `name / parameters / returns / visibility / static / notes` | |
| Relations | Recommended | `id / to / kind / label / from_multiplicity / to_multiplicity / notes` | `class`単体では `from` 列は持たせない |

### Type: class_diagram

| section | required / recommended | expected_headers | notes |
|---|---|---|---|
| Objects | Required | `ref / notes` | |
| Relations | Required | `id / from / to / kind / label / from_multiplicity / to_multiplicity / notes` | |

### Type: dfd_diagram

| section | required / recommended | expected_headers | notes |
|---|---|---|---|
| Objects | Required | `id / label / kind / ref / notes` | |
| Flows | Required | `id / from / to / data / notes` | |

### Type: codeset

| section | required / recommended | expected_headers | notes |
|---|---|---|---|
| Values | Required | `code / label / sort_order / active / notes` | |

### Type: rule

| section | required / recommended | expected_headers | notes |
|---|---|---|---|
| Summary | Recommended | (テキストセクション) | |
| Inputs | Recommended | `id / data / source / required / notes` | |
| References | Recommended | `ref / usage / notes` | |
| Conditions | Recommended | (テキストセクション) | |
| Messages | Recommended | `condition / message / severity / notes` | |
| Notes | Recommended | (テキストセクション) | |

## Diagnostics Mapping

不備検出時に生成される診断コードと深刻度の対応です。

| condition | code | severity |
|---|---|---|
| 必須セクションが存在しない | `missing-required-section` | error |
| テーブルヘッダーが期待値と不一致 | `invalid-table-column` | error |
| テーブルの行とヘッダーの列数が不一致 | `invalid-table-row` | error |
| 完全に空のデータ行 | 診断なし | info |
| 部分入力行の必須値不足 | required-field系診断 | warning |

## Notes

- 本ルールは `FORMAT-*.md` ファイルの代替ではなく、Dogfood運用における設計・検証ガイドとして使用されます。
- AI生成時は、必ず各 `FORMAT-*.md` を確認し、その仕様に完全に準拠してください。
- `FORMAT-*.md` に定義されていない列をテーブルに勝手に追加してはいけません。
- `source_ref` や `target_ref` など、意味が近い列であっても、`FORMAT-*.md` で定義された列の順序を入れ替えてはいけません。
- `invalid-table-column` はヘッダー不一致の診断であり、empty data row handlingとは別です。
- `invalid-table-row` は、部分入力行や列数不一致では引き続き発生し得ます。
- fully empty data row は、ヘッダー行や区切り行ではなく、全セルが空または空白のみのデータ行です。
- fully empty data row は編集途中の空行ノイズとして無視され、required-field diagnosticsを発生させません。
- partially filled row は無視せず、空セル、余分なpipe、列数不一致、required field不足を通常どおり確認します。
- empty row handlingはテーブル診断全体を緩めるものではなく、FORMATヘッダー期待値やunsupported columnの扱いを変更しません。

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | symbol: parseVaultFile; kind: function; パーサーの振り分けと初期検証 |
| src/parsers/markdown-table.ts | symbol: parseMarkdownTable; kind: function; テーブルヘッダーの検証ロジック |
| src/parsers/markdown-table.ts | symbol: isEmptyMarkdownTableDataRow; kind: function; 完全空データ行を編集ノイズとして除外 |
| src/core/current-file-diagnostics.ts | symbol: buildCurrentObjectDiagnostics; kind: function; 診断情報の集約 |
