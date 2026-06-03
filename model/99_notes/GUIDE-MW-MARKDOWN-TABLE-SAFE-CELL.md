---

id: GUIDE-MW-MARKDOWN-TABLE-SAFE-CELL  
name: Markdownテーブルセル安全記述ガイド  
tags:
- Table
- Guide
---

# Markdownテーブルセル安全記述ガイド

## Summary

この文書は Model Weave Dogfood モデルの記述ガイドです。  
Model Weave 本体の正式な `rule` フォーマットではありません。  
Markdown テーブルを安全に記述し、AI 生成時の列崩れ、不要な未解決参照、Viewer / 診断表示での誤判定を防ぐための運用ルールです。

## Conditions

- Markdown ファイル内のあらゆるテーブルのセル記述に適用する。
    - Attributes
    - Methods
    - Fields
    - Inputs
    - Outputs
    - Steps
    - Flows
    - Mappings
    - Relations
    - Source Links
    - その他の構造化テーブル
- 属性型、メソッド引数、戻り値、入出力データ型、参照リンク、条件式、補足説明などの入力時に考慮する。
- 特に `app_process` の `Inputs.data` / `Outputs.data`、`data_object` の `Fields.type`、ER の `Columns.data_type` では、複雑な型表記を避ける。
- Markdown として一見正しく見える記述でも、Model Weave のテーブル診断、Obsidian 表示、正規表現ベースの簡易解析で崩れる可能性があるため、安全側の表記を優先する。

## Recommended Notation

|対象項目|避けるべき記述|推奨される代替記述|
|---|---|---|
|パイプ記号|未エスケープのパイプ記号|原則として使用を避け、`/` 等に置き換える|
|Wikilink|`[[target\|label]]`|`[[target]]` （エイリアスを使わない）|
|Union Type|`string \| null`|`A / B` 等の単純表記を使う。任意性は `required` / `not_null` 列等で表現する|
|Array Type|`string[]`, `SourceLink[]`, `ImpactSourceLink[]`, `Array<string>`, `Array<SourceLink>` の裸書き|Markdown テーブル内では `string list`, `SourceLink list`, `ImpactSourceLink list`, `list of SourceLink` などの単純表記を使う。ER の `data_type` では `string` などの単純型を使い、notes に「複数値」「論理的には配列」と補足する|
|Optional Type|`string \| null`, `Optional<string>` の裸書き|`required` / `not_null` 列で任意性を表現し、必要に応じて notes に補足する|
|Generic 型表記|`Array<T>`, `Optional<T>`, `Map<K,V>` の裸書き|Markdown テーブル内では原則避け、単純型 + notes で表現する|
|複数選択 / 候補|`A \| B \| C`|`A / B / C`|
|アセット参照を含む型|`[[Array<DATA-ID>]]`|type 列には `DATA-ID list` などの単純表記、ref 列に `[[DATA-ID]]`|
|複雑な条件|セル内へのロジック詰め込み|notes への追記、または別ファイル（Rule / Process）への切り出し|
|長文説明|1セル内に長い文章や複数条件を詰め込む|テーブルセルは短くし、詳細は `## Notes` や関連 Rule / Process に分離する|

## Examples

### 1. メソッド定義

- **Risky:** `| getUsers | filter: string \| null | User[] |`
- **Safe:** `| getUsers | filter: string | User list |`

任意性は `required` 列、複数性は notes 列で補足する。

### 2. app_process の Outputs

- **Risky:** `| sourceLinks | SourceLink[] | Parsed model state | 解析済み Source Links |`
- **Safe:** `| sourceLinks | SourceLink list | Parsed model state | 解析済み Source Links |`

`SourceLink[]` のような TypeScript 風の配列表記は、Markdown テーブルとしては自然に見えても、Viewer / 診断側で列数判定や簡易解析の誤判定を誘発する可能性がある。

### 3. 項目マッピング

- **Risky:** `| [[DATA-USER\|ユーザー]] | [[ENTITY-USER\|物理名]] |`
- **Safe:** `| [[DATA-USER]] | [[ENTITY-USER]] |`

表示名は定義側の `name` や `label` に委ねる。

### 4. Union / Optional 表記

- **Risky:** `| status | string \| null | N | 現在状態 |`
- **Safe:** `| status | string | N | 未設定の場合あり |`

任意性は `required` / `not_null` / notes で表現する。

## Enforcement

### Validator Behavior

- セル内に未エスケープのパイプ記号が存在し、列数がヘッダーと不一致を起こした場合は **Error**。
- 列数は一致していても、セル内に `[[...|...]]`、`...[]`、`Array<T>`、`A | B` のような形式が検出された場合は、将来の解析互換性維持のため **Warning** を出す。
- `app_process` の `Inputs` / `Outputs` / `Steps` / `Flows` では、型情報よりも図としての安定性を優先し、複雑な型表記は notes に逃がす。

### Diagnostics Message

- `W-TAB-001: Wikilink alias is risky inside tables. Use [[target]] instead.`
- `W-TAB-002: TypeScript style array/union/generic types may break table parsing. Use simple scalar types and explain multiplicity or optionality in notes / required / not_null columns.`
- `W-TAB-003: Complex table cell content may reduce parser compatibility. Keep table cells short and move details to Notes, Rule, or Process models.`

## Notes

- Model Weave のパーサー（`MarkdownTableParser`）はエスケープや Wikilink を考慮するように設計されていますが、Obsidian の標準レンダラー、他ツールとの互換性、および正規表現による簡易解析時の堅牢性を高めるために本ルールを設けています。
- エスケープを使えば技術的には記述可能な場合でも、可読性が著しく低下するため推奨しません。
- 歴史的な経緯で `string_list` や `string / null` 等の表記が使われることがありますが、新規モデルでは上記推奨表記に寄せてください。
- 旧来の `Array<T>` / `Optional<T>` 表記は、Markdown レンダリングや Viewer 表示で崩れる場合があるため、新規モデルでは原則使用しない。
- ER の `data_type` では単純型を優先し、配列性・任意性は notes / not_null で補足する。
- `SourceLink[]`、`ImpactSourceLink[]`、`RelationModel[]` など、独自型名に `[]` を付けた表記も避ける。Markdown テーブル内では `SourceLink list`、`ImpactSourceLink list`、`RelationModel list` のように書く。
- AI による dogfood 生成では、テーブルセルを短く保ち、詳細な説明や条件分岐は `## Notes`、`## Rules`、`## Steps`、別の Rule / Process モデルに分離する。