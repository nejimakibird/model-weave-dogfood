---
id: GUIDE-MW-MARKDOWN-TABLE-SAFE-CELL
name: Markdownテーブルセル安全記述ガイド
tags:
  - Table
  - Guide
---

# Markdownテーブルセル安全記述ガイド

## Summary

この文書はModel Weave Dogfoodモデルの記述ガイドです。
Model Weave本体の正式な `rule` フォーマットではありません。
Markdownテーブルを安全に記述し、AI生成時の列崩れや不要な未解決参照を防ぐための運用ルールです。

## Conditions

- Markdownファイル内のあらゆるテーブル（Attributes, Methods, Fields, Mappings等）のセル記述に適用する。
- 属性型、メソッド引数、戻り値、参照リンクなどの入力時に考慮する。

## Recommended Notation

| 対象項目       | 避けるべき記述              | 推奨される代替記述                                    |
| ---------- | -------------------- | -------------------------------------------- |
| パイプ記号      | 未エスケープの `\|`         | 原則として使用を避け、`/` 等に置き換える                       |
| Wikilink   | `[[target\|label]]`  | `[[target]]` （エイリアスを使わない）                    |
| Union Type | `string \| null`     | `A / B` 等の単純表記を使う。任意性は required 列等で表現する |
| Array Type | `string[]`, `Array<string>` の裸書き | ERのdata_typeでは `string` を使い、notesに「複数値」「論理的には配列」と補足する。必要な場合のみ `string_array` 等の単純表記を使う |
| Optional Type | `string \| null`, `Optional<string>` の裸書き | required/not_null列で任意性を表現し、必要に応じて notes に補足 |
| Generic型表記 | `Array<T>`, `Optional<T>` の裸書き | Markdownテーブル内では原則避け、単純型 + notesで表現 |
| 複数選択/候補    | `A \| B \| C`        | `A / B / C`                                  |
| アセット参照を含む型 | `[[Array<DATA-ID>]]` | type列には `Array<DATA-ID>`、ref列に `[[DATA-ID]]` |
| 複雑な条件      | セル内へのロジック詰め込み        | `Notes` への追記、または別ファイル（Rule/Process）への切り出し    |

## Examples

### 1. メソッド定義
- **Risky:** `| getUsers | filter: string \| null | User[] |`
- **Safe:** `| getUsers | filter: string | User |` (任意性や複数値は notes 列や required 列で補足する)

### 2. 項目マッピング
- **Risky:** `| [[DATA-USER\|ユーザー]] | [[ENTITY-USER\|物理名]] |`
- **Safe:** `| [[DATA-USER]] | [[ENTITY-USER]] |` (表示名は定義側の名前に委ねる)

## Enforcement

### Validator Behavior
- セル内に未エスケープの `|` が存在し、列数がヘッダーと不一致を起こした場合は **Error**。
- 列数は一致していても、セル内に `[[...|...]]` や `...[]` の形式が検出された場合は、将来の解析互換性維持のため **Warning** を出す。

### Diagnostics Message
- `W-TAB-001: Wikilink alias is risky inside tables. Use [[target]] instead.`
- `W-TAB-002: TypeScript style array/union/generic types may break table parsing. Use simple scalar types and explain multiplicity or optionality in notes / required / not_null columns.`

## Notes

- Model Weaveのパーサー（`MarkdownTableParser`）はエスケープやWikilinkを考慮するように設計されていますが、Obsidianの標準レンダラーや他ツールとの互換性、および正規表現による簡易解析時の堅牢性を高めるために本ルールを設けています。
- エスケープ `\|` を使えば技術的には記述可能ですが、可読性が著しく低下するため推奨しません。
- 歴史的な経緯で `string_list` や `string / null` 等の表記が使われることがありますが、新規モデルでは上記推奨表記に寄せてください。
- 旧来の `Array<T>` / `Optional<T>` 表記は、MarkdownレンダリングやViewer表示で崩れる場合があるため、新規モデルでは原則使用しない。
- ERの `data_type` では単純型を優先し、配列性・任意性は notes / not_null で補足する。

