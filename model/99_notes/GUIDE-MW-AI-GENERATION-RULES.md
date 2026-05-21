---
id: GUIDE-MW-AI-GENERATION-RULES
name: Model Weave AI生成運用ガイド
tags:
  - Guide
  - AI
---

# Model Weave AI生成運用ガイド

## 概要

この文書は、AIがModel WeaveのMarkdownファイルを生成または更新する際の構造的整合性を保つための運用ルールです。
特に、テーブルヘッダーの不一致によるパースエラーの再発を防止することを目的としています。

## 1. テーブルヘッダーの厳格な遵守

AIは、Markdownテーブルを生成する前に、必ず対象となるモデルの `FORMAT-*.md` 仕様（またはパーサーの実装）における期待ヘッダーを確認してください。

- **列の順序・名称・数の固定**: セクションごとに定義されたヘッダー構造を完全に再現してください。
- **勝手な列の追加禁止**: 「便利そうだから」「他のモデルにあるから」という理由で、定義されていない列（例：`ref`, `rule`, `notes`, `description`）を絶対に追加しないでください。
- **パースエラーへの影響**: パーサーは特定の列数を前提としてセルの値を読み取ります。1列でも過不足があると、テーブル全体の解析に失敗します。

## 2. 定義外情報の記録方法

FORMATに存在しない情報をAIが記録する必要があると判断した場合は、以下のルールに従ってください。

- **既存の notes 列の活用**: 対象のテーブルに `notes` 列がある場合は、そこにテキスト情報を集約してください。
- **任意セクションの利用**: 物理的な定義（AttributesやInputsなど）の外側に、`## Notes` や `## Source Links` などの任意セクションを作成して記述してください。
- **TODOへの記録**: `TODO-MISSING-REFERENCES.md` などの管理用ファイルに課題として記述してください。

## 3. フロントマター記述の安全性

AIがフロントマター（YAML領域）を生成・更新する際は以下のルールを徹底してください。

- **Wikilinkのクォート**: frontmatter内で `[[...]]` を記述する場合は、必ずダブルクォートで囲んでください（例: `source: "[[DATA-ID]]"`）。未クォートのWikilinkはYAMLとして不正な構文になる場合があります。
- **typeフィールドの適切な使用**:
  - 正式なModel Weaveモデル（`rule`, `data_object`, `mapping`等）にのみ `type` を付与してください。
  - 補助的なノート、ガイド、TODOファイルには `type` フィールドを含めないでください（`id`, `name`, `tags` は含めて構いません）。
  - 補助ファイルに `type: note` や `type: todo` を付けると、パーサーが不正なスキーマとして検出し、診断エラーの原因となります。

## 4. 具体的な比較例

モデル種別によって、一見似ていても期待される列構成が異なります。

| モデル種別 / セクション | 期待されるヘッダー構造 (OK) | 誤りの例 (NG) |
|---|---|---|
| `data_object` / `Fields` | `| name | label | type | length | required | path | ref | notes |` | - |
| `app_process` / `Inputs` | `| id | data | source | required | notes |` | `| id | data | source | required | ref | notes |` <br> (**`ref` 列は不要**) |
| `app_process` / `Outputs`| `| id | data | target | notes |` | `| id | data | target | ref | notes |` <br> (**`ref` 列は不要**) |
| `rule` / `Inputs` | `| id | data | source | required | notes |` | - |

## 5. 型表記の安全な運用

Markdownテーブル内の `type` / `data_type` 列では、表示崩れやパースエラーを防ぐために以下の表記を遵守してください。

- **山括弧・角括弧の回避**: `string[]`、`Array<string>`、`Optional<string>` のような表記は原則使用しません。`Array<string>` はHTMLタグとして誤認される可能性があり、`string[]` はパーサーが警告を出す場合があります。
- **ER (Entity) での単純型優先**: `data_type` には `string`, `number`, `boolean`, `datetime` などの単純型を記述します。
- **属性情報の外部化**: 複数値（配列性）や任意性は、`notes` 列（例: 「複数値項目」）や、FORMATが提供する `required` / `not_null` 列で表現してください。

## 運用上の注意

- このガイドは運用上の制約であり、正式な `rule` モデルではありません。
- FORMATファイル自体に変更があった場合は、最新のFORMATを常に優先してください。