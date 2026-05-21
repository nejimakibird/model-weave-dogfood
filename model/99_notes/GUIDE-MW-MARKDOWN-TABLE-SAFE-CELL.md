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
| Union Type | `string \| null`     | `Optional<string>` (第一候補), `Union<A, B>`     |
| Array Type | `string[]`, `Array<string>` の裸書き | `string_array`、または `string` + notesに「複数値」 |  
| Optional Type | `string \| null`, `Optional<string>` の裸書き | `Optional-string`、または required列/notesで任意性を表現 |  
| Generic型表記 | `Array<T>`, `Optional<T>` | Markdownテーブルでは原則避け、必要ならnotesに補足 || 複数選択/候補    | `A \| B \| C`        | `A / B / C`                                  |
| アセット参照を含む型 | `[[Array<DATA-ID>]]` | type列には `Array<DATA-ID>`、ref列に `[[DATA-ID]]` |
| 複雑な条件      | セル内へのロジック詰め込み        | `Notes` への追記、または別ファイル（Rule/Process）への切り出し    |

## Examples

### 1. メソッド定義
- **Risky:** `| getUsers | filter: string \| null | User[] |`
- **Safe:** `| getUsers | filter: Optional<string> | Array<User> |`

### 2. 項目マッピング
- **Risky:** `| [[DATA-USER\|ユーザー]] | [[ENTITY-USER\|物理名]] |`
- **Safe:** `| [[DATA-USER]] | [[ENTITY-USER]] |` (表示名は定義側の名前に委ねる)

## Enforcement

### Validator Behavior
- セル内に未エスケープの `|` が存在し、列数がヘッダーと不一致を起こした場合は **Error**。
- 列数は一致していても、セル内に `[[...|...]]` や `...[]` の形式が検出された場合は、将来の解析互換性維持のため **Warning** を出す。

### Diagnostics Message
- `W-TAB-001: Wikilink alias is risky inside tables. Use [[target]] instead.`
- `W-TAB-002: TypeScript style array/union types may break table parsing. Use Array<T> or A / B.`

## Notes

- Model Weaveのパーサー（`MarkdownTableParser`）はエスケープやWikilinkを考慮するように設計されていますが、Obsidianの標準レンダラーや他ツールとの互換性、および正規表現による簡易解析時の堅牢性を高めるために本ルールを設けています。
- エスケープ `\|` を使えば技術的には記述可能ですが、可読性が著しく低下するため推奨しません。
- 歴史的な経緯で `string_list` や `string / null` 等の表記が使われることがありますが、新規モデルでは上記推奨表記に寄せてください。

---
最終更新: 2026-05-19