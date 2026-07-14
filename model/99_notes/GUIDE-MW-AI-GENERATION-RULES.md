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

Model Weave 0.1.20 時点では、app_process Business Flow、Flow Diagram Detail / Screen view、Flow Diagram Domain Sources、Mapping target-first canonical header、Domain Sources、color_scheme、Source Links section support、Impact / Relationship View、Weave Map が実装側に進んでいます。AI生成では、実装済みの表示機能と dogfood 先行のExplorer構想を分けて扱ってください。

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
| `data_object` / `Fields` | `\| name \| label \| type \| length \| required \| path \| ref \| notes \|` | - |
| `app_process` / `Inputs` | `\| id \| data \| source \| required \| notes \|` | `\| id \| data \| source \| required \| ref \| notes \|` <br> (**`ref` 列は不要**) |
| `app_process` / `Outputs`| `\| id \| data \| target \| notes \|` | `\| id \| data \| target \| ref \| notes \|` <br> (**`ref` 列は不要**) |
| `app_process` / `Steps` | `\| id \| domain \| label \| kind \| input \| output \| rule \| invoke \| screen \| notes \|` | `\| id \| label \| kind \| input \| output \| notes \|` <br> (**Business Flow 用の現行列を欠落させない**) |
| `app_process` / `Flows` | `\| from \| to \| condition \| label \| notes \|` | `\| id \| from \| to \| data \| notes \|` <br> (**DFD Flows と混同しない**) |
| `app_process` / `Transitions` | `\| id \| event \| to \| condition \| notes \|` | `\| from \| to \| label \| notes \|` <br> (**画面・プロセスへの出口はTransitionsに書く**) |
| `mapping` / `Mappings` | `\| target_ref \| source_ref \| transform \| rule \| required \| notes \|` | `\| source_ref \| target_ref \| transform \| rule \| required \| notes \|` <br> (**source-firstはlegacy互換。現行生成ではtarget-firstを使う**) |
| `dfd_diagram` / `Objects` | `\| id \| label \| kind \| ref \| domain \| notes \|` | `\| id \| label \| kind \| ref \| notes \|` <br> (**0.1.17ではdomain列を保持する**) |
| `dfd_diagram` / `Flows` | `\| id \| from \| to \| data \| notes \|` | `\| from \| to \| label \| data \| notes \|` <br> (**label列はDFD Flowの現行列ではない**) |
| `Domain Sources` | `\| ref \| notes \|` または `\| ref \|` | `\| id \| ref \| notes \|` <br> (**Domain Source自体にid列を追加しない**) |
| `Source Links` | `\| path \| notes \|` | `\| path \| symbol \| kind \| notes \|` <br> (**現行dogfoodではpath / notesを基本にする**) |
| `rule` / `Inputs` | `\| id \| data \| source \| required \| notes \|` | - |

## 5. 型表記の安全な運用

Markdownテーブル内の `type` / `data_type` 列では、表示崩れやパースエラーを防ぐために以下の表記を遵守してください。

- **山括弧・角括弧の回避**: `string[]`、`Array<string>`、`Optional<string>` のような表記は原則使用しません。`Array<string>` はHTMLタグとして誤認される可能性があり、`string[]` はパーサーが警告を出す場合があります。
- **ER (Entity) での単純型優先**: `data_type` には `string`, `number`, `boolean`, `datetime` などの単純型を記述します。
- **属性情報の外部化**: 複数値（配列性）や任意性は、`notes` 列（例: 「複数値項目」）や、FORMATが提供する `required` / `not_null` 列で表現してください。

## 6. 実装状態の意識

Dogfoodモデルには「将来構想（先行設計）」が含まれます。生成・提案時は以下の点に注意してください。

- **実装済みと断定しない**: `Source Links Explorer`、`Reference Explorer`、`Model Index View` は、現時点では Dogfood モデル上で定義されている将来機能です。独立Explorer画面として本体に実装済みであることを前提にしないでください。
- **Source Links section support は実装済み**: 各モデル内の `## Source Links` セクションは、パーサーで抽出され、Preview内で表示され、Copy / Open 操作や `localSourceRoot` による相対パス解決に使われます。Relationship View / Impact Summary でも関連 Source Links として集約されます。
- **Source Links Explorer は別物**: Source Links Explorer の一覧画面、Explorer専用フィルタ、選択状態管理、Explorer索引は future として扱います。Source Links section support と混同しないでください。
- **Business Flow は実装済み**: app_process の表形式 `Steps` / `Flows` による Business Flow 表示、`flow_direction`、LR / TD 方向切替、`defaultBusinessFlowDirection`、Step kind拡張、Flow Connect Mode は 0.1.17 時点で実装済みです。
- **Mappingはtarget-firstが現行canonical**: `## Mappings` は `target_ref / source_ref / transform / rule / required / notes` を使ってください。legacy `source_ref / target_ref` は警告なしで受理されますが、新規生成の推奨形式ではありません。
- **Flow Diagram Screen view は実装済み**: `type: flow_diagram` はDetail / Screen viewを持ち、Screen viewではvisible nodeへのprojectionを使います。任意projection定義や汎用foldingはfutureとして残してください。
- **flow_viewは初期表示**: frontmatter `flow_view` は初期mode入力です。toolbarでのFlow view切替はViewer stateを更新するだけで、Markdown本文やfrontmatterを書き換えません。
- **Flow DiagramのDomain Sources**: Flow Diagramでも `## Domain Sources` とlocal `## Domains` を使えます。Domain nameはgroup label、parentはnested group、kindは `target=domain` のColor Scheme keyとして扱います。
- **Flow endpoint の扱い**: `Flows.from` / `Flows.to` はローカル `Steps.id` を参照します。外部モデル、画面、別プロセスへの出口は `Transitions.to` に記述してください。
- **Step kind の扱い**: `start`, `process`, `decision`, `input`, `screen`, `subflow`, `end`, `event`, `api`, `batch`, `message`, `data`, `store`, `wait`, `error`, `connector`, `external` は描画上の意味を持ちます。未確認の独自kindを増やす場合は、表示崩れしないか確認してください。
- **Business Flow化の対象選定**: proseの app_process を表形式へ変換する場合は、source-backedな順序、分岐、入出力が明確な処理だけを対象にしてください。future Explorer系や logical / planned process は、実装済み機能と混同しないよう prose のまま維持して構いません。
- **不明点の扱い**: source / docs / dogfood の差分で判断に迷う場合は、無理に実装済み扱いにせず [[TASKS-MW-DOGFOOD-COVERAGE]] に調査タスクとして残してください。根拠、判断、次アクションを分けることで、後続のAI生成やレビューで再利用できます。
- **docs-onlyの扱い**: docsやdesign noteに書かれていても、srcで確認できないものはimplementedにしないでください。古いcommands docs、design noteの将来項目、サンプル由来の説明は `docs_only`、`planned`、`future`、`note_only` のいずれかに逃がしてください。
- **Domain Sources と color_scheme**: `domains`、`domain_diagram`、`Domain Sources`、`color_scheme` は実装済みです。Business Flow の `Steps.domain`、DFD の `Objects.domain`、Domain group、DFD object、Business Flow step の配色に使えます。
- **Weave Map の扱い**: Weave Map は Relationship View の派生表示として実装済みです。ただし `type: weave_map` という正本Markdown形式や専用パーサーはありません。Weave Map用の新規モデル形式を発明しないでください。
- **NotebookLM用バンドル**: NotebookLM向けにファイルをまとめる場合は、dogfood運用上の派生成果物として扱います。本体の実装済み機能や新しいModel Weave形式として記述しないでください。
- **maturity区分の参照**: フォルダ構成や実装状態の詳細は `model/README.md` を参照してください。

## 7. 表現の統一

- 不自然な和英混在を避けます。画面や図に出す場合は「描画する」「表示する」、Mermaid source や派生ファイルを作る場合は「生成する」を使ってください。
- `render_mode`、`Business Flow`、`Source Links`、`Domain Sources`、`Weave Map` など、FORMAT名・設定名・UI名として定着している語はそのまま使って構いません。
- 「実装済み」「部分実装」「将来構想」を本文で明記し、読者が本体実装とdogfood先行設計を取り違えないようにしてください。
- Markdown が正本です。Mermaid source、PNG、NotebookLM用バンドル、コピー用Markdownは派生出力として扱ってください。
- 仕様書不備のある業務システムを扱う場合は、既存ドキュメントや生成済みモデルを正と見なさず、src / docs / model の差分をAIで検出し、source-backedなものから段階的に反映してください。

## 運用上の注意

- このガイドは運用上の制約であり、正式な `rule` モデルではありません。
- FORMATファイル自体に変更があった場合は、最新のFORMATを常に優先してください。
