---
id: NOTE-MW-README
name: Model Weave Dogfood Models README
tags:
  - README
  - Guide
---

# Model Weave Dogfood Models

本フォルダは、Model Weave 自身を Model Weave でモデリングした設計アセット（正本）を格納します。

この dogfood model は、Model Weave 0.1.17 時点の実装と docs を参照しながら、実装済みの機能、部分的に実装された機能、将来構想を分けて管理します。

## フォルダ構成

| フォルダ          | 内容                            | 備考  |
| ------------- | ----------------------------- | --- |
| `01_dfd/` | プラグイン全体や主要処理のデータフロー構造 | L0/L1図、DFD object |  
| `02_class/` | 内部ロジック、パーサー、Detector等のクラス定義 | AREA別に分割 |  
| `03_data/` | Viewer状態、インデックス、設定等の論理データ構造 | data_object |  
| `04_process/` | 保存、再構築、フィルタリング等のアプリケーションプロセス | app_process / Business Flow |  
| `05_rule/` | 設定解決、色表示、バリデーション等の論理ルール | rule |  
| `06_mapping/` | UIからデータ、データから状態への変換定義 | mapping |  
| `07_codeset/` | 列挙値、定数セット | codeset |  
| `08_er/` | モデル資産、参照、ソースリンク等の永続化/論理エンティティ | er_entity / er_diagram |  
| `08_message/` | ユーザーメッセージ、エラー定義 | 08_er と番号衝突している場合は後続で番号見直し |  
| `09_screen/` | 設定タブ、各Explorerの画面定義 | 実装済み画面と将来構想を混同しない |  
| `10_color_scheme/` | Domain、DFD、Business Flow などの配色定義 | color_scheme |  
| `99_notes/` | 記述ガイド、運用ルール、TODO管理 | 補助ファイル。typeは付けない |

## Dogfoodモデルの成熟度 / 実装状態

Dogfoodモデルには、実装済みの機能と将来の構想が混在しています。AI生成や設計レビューの際は以下の区分に注意してください。

| 区分 | 意味 | 対象例 |
|---|---|---|
| **implemented** | 現行の Model Weave 本体に実装済み。 | class, er_entity, data_object, dfd_object, app_process Business Flow, Source Links section, Relationship View, Weave Map |
| **partial** | 一部実装済みだが、dogfoodモデル全体とはまだ完全一致していない。 | Viewer細部、Diagnostics guidance、dogfood app_process coverage |
| **planned** | docsや設計方針として自然だが、現時点では限定的または未実装。 | dogfood 側の追加モデル化、手動レビュー用バンドル |
| **future** | 将来構想・dogfood先行設計。本体実装済みとは限らない。 | **Source Links Explorer**, **Reference Explorer**, Model Index View |
| **docs_only** | docsやdesign noteにはあるが、src実装としては未確認。 | 古いcommands docsの単一Default render mode説明、Weave Map設計メモの将来項目 |
| **note_only** | 補助メモ、ガイド、運用ルール。 | 99_notes 配下の各種ガイド |

0.1.17 時点では、`## Source Links` セクションの解析、Preview 内表示、パスコピー、ファイルを開く操作、Relationship View / Impact Summary への関連 Source Links 集約は実装済みとして扱います。一方で、Source Links Explorer としての独立画面、Explorer 専用フィルタ、選択状態管理、Explorer 索引は将来構想として扱います。

app_process では、表形式の `## Steps` / `## Flows` による Business Flow 表示、`flow_direction`、LR / TD 方向切替、Step kind 拡張、Connect Flow による `## Flows` 行追加支援が実装済みです。Markdown が正本であり、Connect Flow は Markdown の `## Flows` テーブルを更新する入力支援です。

Domain Sources、`domains`、`domain_diagram`、`color_scheme` は実装済み形式として扱います。Business Flow、DFD、Domain Diagram では、Domain group や Color Scheme による表示補助を実装済みとして扱います。

## 運用ルール

### 1. アセットID命名規則
原則として `<FORMAT>-MW-<AREA>-<NAME>` の形式を採用します。
- AREA: `CORE`, `PARSER`, `RENDERER`, `UI`, `EDITOR`, `SETTINGS`, `EXPORT`, `DIAGNOSTICS`
- フォーマット限定のAREA: `DFD`, `ER`, `CLASS`, `SCREEN`, `COMPLETION` 等

### 2. 表記・記述ルール
- **DFD**: `Objects` は現行 FORMAT の `id / label / kind / ref / domain / notes` 形式を維持し、`Flows` は `id / from / to / data / notes` 形式を使う。
- **Class**: 単体クラスの `Relations` には `from` 列を持たせない。クラス図では `from / to` を明記する。
- **Screen**: 画面遷移は `Actions.transition` に記述し、ターゲットは `Fields.id` を参照する。
- **Mapping**: 項目対応に特化し、複雑なロジックは `rule` または `app_process` へ切り出す。
- **app_process**: Business Flow 化する場合は現行 FORMAT の `Steps` / `Flows` / `Transitions` ヘッダーを維持する。`Flows.from` / `Flows.to` には外部モデルIDではなくローカル `Steps.id` を使う。
- **Source Links**: 現行の `## Source Links` テーブルは `path / notes` を基本とし、実装追跡では `notes` に関係する `Steps.id` や処理境界を記録する。
- **Domain Sources**: `Domain Sources.ref` は `[[DOMAINS-...]]` 形式を優先し、`Steps.domain` や `Objects.domain` には Domain id を記述する。

### 3. 設計の正本
- Markdown ファイルが常に設計の正本です。
- Mermaid ソース、プレビュー、PNG はすべて Markdown から生成される派生出力です。
- NotebookLM 用バンドルを作る場合も派生出力として扱い、dogfood model の正本や本体実装済み機能とは分けて管理します。

## 4. 将来機能の扱い
- `future` 区分モデルは、現時点では「理想的な設計」の定義であり、プラグイン本体のソースコードと 1:1 で一致していない場合があります。
- Source Links Explorer、Reference Explorer、Model Index View は、個別モデルが存在していても本体の独立画面として実装済みとは扱いません。

## 5. AI生成
- 新規モデルを生成する際は `99_notes` 配下のガイドを遵守してください。
- app_process を Business Flow 化する場合は、source-backed な分岐と入出力が明確な処理を優先し、future Explorer 系や logical / planned process を無理に表形式へ変換しないでください。
- 機能網羅性点検や不明点管理は [[TASKS-MW-DOGFOOD-COVERAGE]] を起点に進めてください。src / docs / dogfood の差分を一度で解消しようとせず、根拠、判断、次アクションを分けて段階的に反映します。

## TODO  
  
- `08_er/` と `08_message/` の番号衝突を後続で整理する。  
- messageフォルダを維持する場合、`10_message/` 等への移動を検討する。  
- 今回の作業ではフォルダリネームは行わない。
- 0.1.17 の Viewer細部、Review Panel、Applied Color Scheme表示、NotebookLM用派生バンドルの運用境界を、必要に応じて個別モデルへ反映する。
- 継続的な網羅性点検タスクは [[TASKS-MW-DOGFOOD-COVERAGE]] で管理する。
