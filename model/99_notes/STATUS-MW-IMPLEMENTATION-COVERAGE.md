---
id: STATUS-MW-IMPLEMENTATION-COVERAGE
name: Model Weave Dogfood Implementation Coverage
tags:
  - Status
  - Coverage
  - Guide
---

# Model Weave Dogfood Implementation Coverage

## Summary
Dogfoodモデルに含まれる設計資産を、現行Model Weave本体への実装状態に基づいて分類する。
この分類は、AI生成や設計レビュー時に「実装済み」と「将来構想」を混同しないための補助情報である。

## Status Categories

| status | meaning | notes |
|---|---|---|
| implemented | 現行Model Weave本体に実装済み、または実装にかなり近い | 実装済み機能のdogfood精度を優先して高める |
| partial | 一部実装済み。ただしdogfoodモデル全体とは未対応 | 実装差分の確認が必要 |
| planned | 近い将来の拡張として自然だが、現時点では未実装または限定的 | V0.7以降の候補 |
| future | 将来構想・dogfood先行設計 | 本体に実装済みと断定しない |
| note_only | ガイド、README、TODOなど実装対象ではない | 補助情報 |

## Coverage Summary

| status | target | notes |
|---|---|---|
| implemented | class / er_entity / data_object / dfd_object | stable/primary formats |
| implemented | Viewer zoom / pan / fit | Viewer基本操作 |
| implemented | diagnostics panel | 診断表示 |
| implemented | PNG export | 図の派生出力 |
| implemented | Source Links section parsing / preview rendering | `## Source Links` の抽出とPreview内表示 |
| implemented | Source Links Copy Path / Open resolved path | Preview内Source Links表の操作。Local source root によるrelative path解決を含む |
| implemented | Related Source Links in relationship / impact summary | Relationship summary内の関連Source Links集約 |
| partial | dfd_diagram | Mermaid方針とV0.7移行の確認が必要 |
| partial | class_diagram / er_diagram | diagram表示はあるがdogfood定義との完全一致確認が必要 |
| partial | render_mode | 解決ロジックとUI接続の確認が必要 |
| partial | settings / viewport state / status bar | 実装範囲とdogfood定義の照合が必要 |
| planned | settings tab | 近い将来機能 |
| planned | rule / mapping / app_process / codeset / message | formatとしての整理とViewer/diagnostics対応の確認が必要 |
| planned | Vault Index rebuild | 近い将来機能として扱う |
| future | Model Index View | dogfood先行設計 |
| future | Reference Explorer | dogfood先行設計 |
| future | Source Links Explorer | dogfood先行設計 |
| future | Source Links Explorer indexing / filtering / selection | Explorer画面としての一覧・検索・選択はdogfood先行設計 |
| note_only | model/README.md / 99_notes guides | 補助ドキュメント |

## Implemented First Policy

- 今後のdogfood整備では、implemented / partial のモデルを優先してソース実装と照合する。
- future のモデルは、将来機能の設計資産として扱い、現行実装済みと断定しない。
- Source Links Explorer / Reference Explorer / Model Index View は future として扱う。
- `## Source Links` セクションの解析、Preview内表示、Copy Path / Open、Relationship summary内のRelated Source Links集約は現行実装済みとして扱う。詳細は [[PROC-MW-SOURCE-LINKS-SECTION-SUPPORT]] を参照する。
- Source Links Explorerとしての一覧画面、Explorer専用のフィルタ、選択状態管理、Explorer索引は future として扱う。
- Source Linksのrelative path解決には現行実装済み設定 `localSourceRoot` を使用する。`enableSourceLinks` は現行ModelWeaveSettingsには存在しないため実装済み設定とは扱わない。

## TODO

- implemented / partial の境界をソースコード単位で再確認する。
- 必要に応じて、各モデルファイルのfrontmatterに maturity を追加する。
- Explorer系モデルに maturity: future を付与するか検討する。
- `08_er/` と `08_message/` の番号衝突を別タスクで整理する。
