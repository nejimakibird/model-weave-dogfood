# Model Weave Dogfood Design Models

この vault は、Obsidian プラグイン **Model Weave** 自身を Model Weave 形式の Markdown モデルとして記述した dogfood 用の設計資料です。

Markdown ファイルを正本として、現行実装済みの機能、部分的に実装された機能、将来構想を区別して整理します。

## 読み方

まず Domain Diagram で機能領域を把握し、次に DFD や app_process で処理とデータの流れを確認します。app_process の `Steps` では、処理単位ごとに Domain、入力、出力、関連ルール、呼び出し先を辿れます。

代表的な入口:

- `model/00_domains/DOMAIN-DIAGRAM-MW-ARCHITECTURE.md`
- `model/01_dfd/diagrams/DFD-MW-CORE-L0.md`
- `model/04_process/PROC-MW-VAULT-INDEX-REBUILD.md`
- `model/04_process/PROC-MW-PREVIEW-RENDER-MODEL.md`
- `model/04_process/PROC-MW-PARSER-PARSE-VAULT-FILE.md`

## Source Links による実装追跡

この dogfood model では、`Source Links` を使ってモデル上の Step と実装ソースの対応を記録しています。

`app_process` では処理の流れを `Steps` として表現し、`Source Links` では各 Step に関係する実装ファイルを `notes` に記載しています。これにより、Domain や Process で全体像を把握したあと、該当 Step から実装箇所へ降りることができます。

おすすめの確認順序:

1. Domain Diagram で機能領域を把握する
2. DFD / app_process で処理やデータの流れを確認する
3. Step と Wikilink で関連モデルを辿る
4. Source Links notes から対応する実装ファイルを確認する

## ディレクトリ構成

| ディレクトリ | 内容 |
|---|---|
| `docs/` | Model Weave の形式仕様、使い方、ポリシー |
| `docs/ja/` | 日本語ドキュメント |
| `model/00_domains/` | Domain Sources と Domain Diagram |
| `model/01_dfd/` | DFD 関連モデル |
| `model/02_class/` | クラス構造、Parser、Renderer などのモデル |
| `model/03_data/` | Viewer 状態、設定、診断、Renderer 入力などのデータオブジェクト |
| `model/04_process/` | app_process 形式の処理定義 |
| `model/05_rule/` | ルール、判定条件、解決方針 |
| `model/06_mapping/` | データ間、UI 間のマッピング定義 |
| `model/07_codeset/` | コード値、区分値 |
| `model/08_er/` | ER Entity 定義 |
| `model/08_message/` | メッセージ定義 |
| `model/09_screen/` | 画面、パネル、ビュー定義 |
| `model/10_color_scheme/` | Color Scheme 定義 |
| `model/99_notes/` | README、運用ルール、AI 生成ガイド、実装カバレッジ情報 |

## 実装状態の扱い

この vault には、現行実装済みのモデルと将来構想のモデルが混在しています。`Source Links Explorer`、`Reference Explorer`、`Model Index View` は現時点では将来構想として扱います。

詳細は次を参照してください。

- `model/99_notes/README.md`
- `model/99_notes/STATUS-MW-IMPLEMENTATION-COVERAGE.md`
- `model/99_notes/GUIDE-MW-AI-GENERATION-RULES.md`

## 関連リポジトリ

Model Weave 本体:

- https://github.com/nejimakibird/model-weave
