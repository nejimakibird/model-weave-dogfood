---
id: NOTE-MW-README
name: Model Weave Dogfood Models README
tags:
  - README
  - Guide
---

# Model Weave Dogfood Models

本フォルダは、Model Weave 自身を Model Weave でモデリングした設計アセット（正本）を格納します。

## フォルダ構成

| フォルダ          | 内容                            | 備考  |
| ------------- | ----------------------------- | --- |
| `01_dfd/` | プラグイン全体や主要処理のデータフロー構造 | L0/L1図、DFD object |  
| `02_class/` | 内部ロジック、パーサー、Detector等のクラス定義 | AREA別に分割 |  
| `03_data/` | Viewer状態、インデックス、設定等の論理データ構造 | data_object |  
| `04_process/` | 保存、再構築、フィルタリング等のアプリケーションプロセス | app_process |  
| `05_rule/` | 設定解決、色表示、バリデーション等の論理ルール | rule |  
| `06_mapping/` | UIからデータ、データから状態への変換定義 | mapping |  
| `07_codeset/` | 列挙値、定数セット | codeset |  
| `08_er/` | モデル資産、参照、ソースリンク等の永続化/論理エンティティ | er_entity / er_diagram |  
| `08_message/` | ユーザーメッセージ、エラー定義 | 08_er と番号衝突している場合は後続で番号見直し |  
| `09_screen/` | 設定タブ、インデックスビュー、各Explorerの画面定義 | screen |  
| `99_notes/` | 記述ガイド、運用ルール、TODO管理 | 補助ファイル。typeは付けない |

## Dogfoodモデルの成熟度 / 実装状態

Dogfoodモデルには、実装済みの機能と将来の構想が混在しています。AI生成や設計レビューの際は以下の区分に注意してください。

| 区分 | 意味 | 対象例 |
|---|---|---|
| **implemented** | 現行の Model Weave 本体に実装済み。 | DFD, Class, ER, Viewer (Zoom/Pan/Fit), Diagnostics パネル |
| **near_term** | 近い将来の拡張として設計されており、実装と接続しやすい。 | プラグイン設定 UI, Vault インデックス再構築 (Rebuild) |
| **future** | 将来構想・dogfood先行設計。本体実装済みとは限らない。 | **Source Links Explorer**, **Reference Explorer**, Model Index View |
| **note_only** | 補助メモ、ガイド、運用ルール。 | 99_notes 配下の各種ガイド |

## 運用ルール

### 1. アセットID命名規則
原則として `<FORMAT>-MW-<AREA>-<NAME>` の形式を採用します。
- AREA: `CORE`, `PARSER`, `RENDERER`, `UI`, `EDITOR`, `SETTINGS`, `EXPORT`, `DIAGNOSTICS`
- フォーマット限定のAREA: `DFD`, `ER`, `CLASS`, `SCREEN`, `COMPLETION` 等

### 2. 表記・記述ルール
- **DFD**: `Objects` は `id / label / kind / ref / notes` 形式を維持し、`Flows` は `id` を参照する。
- **Class**: 単体クラスの `Relations` には `from` 列を持たせない。クラス図では `from / to` を明記する。
- **Screen**: 画面遷移は `Actions.transition` に記述し、ターゲットは `Fields.id` を参照する。
- **Mapping**: 項目対応に特化し、複雑なロジックは `rule` または `app_process` へ切り出す。

### 3. 設計の正本
- Markdown ファイルが常に設計の正本です。
- Mermaid ソース、プレビュー、PNG はすべて Markdown から生成される派生出力です。

## 4. 将来機能の扱い
- `future` 区分モデルは、現時点では「理想的な設計」の定義であり、プラグイン本体のソースコードと 1:1 で一致していない場合があります。

## 5. AI生成
- 新規モデルを生成する際は `99_notes` 配下のガイドを遵守してください。

## TODO  
  
- `08_er/` と `08_message/` の番号衝突を後続で整理する。  
- messageフォルダを維持する場合、`10_message/` 等への移動を検討する。  
- 今回の作業ではフォルダリネームは行わない。