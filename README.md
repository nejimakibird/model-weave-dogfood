# Model Weave Dogfood Design Models

このリポジトリは、Obsidianプラグイン **Model Weave** 自身を、Model Weave形式のMarkdownモデルとして記述した dogfood 用の設計資料です。

現在公開中の Model Weave **0.1.2** 時点の実装内容を基準に、現行実装済みの機能、近い将来の計画、将来構想を区別して整理しています。

## 目的

このdogfoodモデルは、以下を目的としています。

- Model Weave自身の構造をModel Weave形式で可視化する
- Markdownを正本とした設計資産として管理する
- 現行実装と設計モデルの差分を確認しやすくする
- AIによる設計レビュー、影響調査、ドキュメント生成の材料にする
- 将来機能を、現行実装済み機能と混同しないように整理する

## Source Links による実装追跡

このdogfoodモデルでは、`Source Links` を使ってモデル上の処理Stepと実装ソースの対応を記録しています。

`app_process` では処理の流れを `Steps` として表現し、`Source Links` では各Stepに関係する実装ファイルを `notes` に記載しています。これにより、DomainやProcessで全体像を把握したあと、該当Stepから実装箇所へ降りることができます。

おすすめの確認順序:

1. Domain Diagram で機能領域を把握する
2. DFD / app_process で処理やデータの流れを確認する
3. Step と Wikilink で関連モデルを辿る
4. Source Links notes から対応する実装ファイルを確認する

代表例:

- `model/00_domains/DOMAIN-DIAGRAM-MW-ARCHITECTURE.md`
- `model/01_dfd/diagrams/DFD-MW-CORE-L0.md`
- `model/04_process/PROC-MW-VAULT-INDEX-REBUILD.md`
- `model/04_process/PROC-MW-PREVIEW-RENDER-MODEL.md`
- `model/04_process/PROC-MW-PARSER-PARSE-VAULT-FILE.md`

## ディレクトリ構成

| ディレクトリ | 内容 |
|---|---|
| `model/01_dfd/` | DFD関連モデル |
| `model/02_class/` | クラス構造、Parser、Rendererなどのモデル |
| `model/03_data/` | Viewer状態、設定、診断、Renderer入力などのデータオブジェクト |
| `model/04_process/` | app_process形式の処理定義 |
| `model/05_rule/` | ルール、判定条件、解決方針 |
| `model/06_mapping/` | データ間、UI間のマッピング定義 |
| `model/07_codeset/` | コード値、区分値 |
| `model/08_er/` | ER Entity定義 |
| `model/08_message/` | メッセージ定義 |
| `model/09_screen/` | 画面・パネル・ビュー定義 |
| `model/99_notes/` | README、運用ルール、AI生成ガイド、実装カバレッジ情報 |

## 実装状態の扱い

このリポジトリには、現行実装済みのモデルと、将来構想のモデルが混在しています。

主な区分は以下です。

| 区分 | 意味 |
|---|---|
| `implemented` | 現行のModel Weave本体に実装済み、または実装にかなり近いもの |
| `partial` | 一部実装済みだが、dogfoodモデル全体とはまだ完全一致していないもの |
| `planned` | 近い将来の拡張として設計しているもの |
| `future` | 将来構想・dogfood先行設計 |
| `note_only` | ガイド、README、TODOなどの補助情報 |

詳細は以下を参照してください。

- `model/99_notes/README.md`
- `model/99_notes/STATUS-MW-IMPLEMENTATION-COVERAGE.md`
- `model/99_notes/GUIDE-MW-AI-GENERATION-RULES.md`

## 注意事項

- Markdownファイルが設計の正本です。
- Mermaid、プレビュー、PNGなどはMarkdownから生成される派生出力です。
- `Source Links Explorer`、`Reference Explorer`、`Model Index View` は現時点では将来構想として扱います。
- `Source Links` セクションは、現時点では主に設計メモ・追跡用リンクです。
- 本資料はModel Weave 0.1.2時点の実装を基準にしています。今後のバージョンで実装との差分が発生する可能性があります。

## 関連リポジトリ

Model Weave本体:

- https://github.com/nejimakibird/model-weave
