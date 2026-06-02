---
type: dfd_diagram
id: DFD-MW-CORE-L0
name: Model Weave Core Level 0
level: 0
tags:
  - DFD
  - Core
---

# Model Weave Core Level 0

## Summary

Model Weave プラグイン内部の主要なデータフローを示します。
Markdown ソースの読み込みからパース、内部モデルの構築、そして各種レンダラーを介した Viewer への表示とエクスポートの流れを定義します。

## Objects

| id | label | kind | ref | notes |
|---|---|---|---|---|
| MD_FILES | Markdown Model Files | external | | Obsidian Vault 内の設計アセット群 |
| PARSER | Core Parser | process | | 各フォーマットの解析とバリデーション |
| VAULT_INDEX | Parsed Model (Vault Index) | datastore | | メモリ内の解析済みモデル管理構造 |
| RENDERER | Renderer | process | | Mermaid または Custom レンダリングの生成 |
| VIEWER | Model Weave Viewer | external | | ユーザーが操作するプレビュー画面 |
| DIAGNOSTICS | Diagnostics Engine | process | | 設計不備の抽出 |
| PNG_EXPORT | PNG Export | process | | 図面の画像出力機能 |

## Flows

| from | to | label | data | notes |
|---|---|---|---|---|
| MD_FILES | PARSER | | Markdown Source | |
| PARSER | VAULT_INDEX | | Internal Model | |
| PARSER | DIAGNOSTICS | | Raw Warnings | |
| VAULT_INDEX | RENDERER | | Model Structure | |
| RENDERER | VIEWER | | Rendered Content | Mermaid SVG / HTML |
| DIAGNOSTICS | VIEWER | | Diagnostic List | Error/Warning/Info |
| VIEWER | PNG_EXPORT | | Rendered Surface | |

## Notes

- 本図はシステム全体の概要を示す Level 0 図です。
- 各プロセスの詳細は Level 1 以降または各オブジェクトの定義を参照してください。