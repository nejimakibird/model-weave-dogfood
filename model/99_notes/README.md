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

| フォルダ | 説明 | 
|---|---|
| `01_dfd/` | データフロー構造（L0/L1図、プロセス/ストア定義） |
| `02_class/` | 責務構造（クラス図、クラス定義）。AREA別に分割。 |
| `03_data/` | データ構造（永続化データ、メモリ内オブジェクト） |
| `04_process/` | 処理フロー（メインプロセス、サブプロセス） |
| `05_rule/` | ビジネスルール、変換仕様、判定ロジック |
| `06_mapping/` | 項目対応、データ変換定義 |
| `07_codeset/` | 列挙値、定数セット |
| `08_message/` | ユーザーメッセージ、エラー定義 |
| `09_screen/` | UI構成、イベント、画面遷移 |
| `99_notes/` | 補足メモ、TODO、運用ガイド |

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

## 代表的なモデル

- [[DFD-MW-CORE-L0]]: プラグイン全体のコンテキストデータフロー
- [[CLSD-MW-PARSER-STRUCTURE]]: パーサーパッケージの責務構造

---
最終更新: 2026-05-17