---
type: rule
id: RULE-MW-RENDERER-DFD-LAYOUT-POLICY
name: DFDレイアウト方針
kind: renderer_rule
tags:
  - DFD
  - Renderer
  - Layout
---

# DFDレイアウト方針

## Summary

DFDをMermaid flowchartとして表示する際のレイアウト方針を定義します。
Mermaid sourceは解析済みのモデルから生成される派生出力であり、Markdownモデル（source of truth）の内容を視覚化するためのレンダリング規則として機能します。

## Conditions

- 対象モデルの `type` が `dfd_diagram` であること。
- 実効描画モード（effective render mode）が `mermaid` であること。
- `[[RULE-MW-DFD-MERMAID-ONLY]]` の方針に従うこと。

## Layout Policy

DFDのレンダリングにおいて、以下の表示方針を適用します。

| category | policy | ref | notes |
|---|---|---|---|
| 方向 | 原則として `flowchart LR` (左から右) を使用する | | 外部から内部、出力への流れを直感的にする |
| ノード | ノード種別に応じた形状とスタイルを適用する | [[RULE-MW-RENDERER-DFD-NODE-STYLING]] | |
| 接続 | `Objects` で定義された ID または参照に基づき解決する | [[MAP-MW-DFD-FLOW-TO-GRAPH-EDGE]] | |
| ラベル | フローデータの名称をエッジラベルとして表示する | [[DATA-MW-DFD-FLOW-ENTRY]] | dataが空の場合はラベルなしを許容 |
| サニタイズ | Mermaid構文に影響を与える特殊文字を置換・除去する | | パイプ記号、括弧、改行など |
| 耐障害性 | 未解決の参照があっても可能な範囲で描画を継続する | | 不備のあるノードのみ警告を表示 |

## Diagnostics

| condition | code | severity | notes |
|---|---|---|---|
| 始点・終点の参照が解決できない | unresolved-reference | warning | 接続線が生成されない |
| サポートされないレイアウト方向の指定 | unsupported-render-mode-fallback | warning | LRへフォールバック |
| Mermaidソースの生成自体に失敗した | mermaid-source-generation-error | error | プレビュー全体を中断 |

## Notes

- 本ルールは厳密な座標指定や経路制御（Subgraphs, clusters等）を目的としたものではなく、Mermaidの自動レイアウトに委ねるための「生成仕様」を表します。
- 座標調整が必要な場合は、将来的にカスタムレンダラーまたはレイアウト情報の属性導入を検討します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/dfd-mermaid.ts | buildDfdMermaidSource | function | DFD Mermaidソース生成の起点 |
| src/renderers/dfd-mermaid.ts | sanitizeMermaidEdgeLabel | function | ラベルのサニタイズ実装 |
| src/core/relation-resolver.ts | resolveDfdDiagramRelations | function | 接続関係の解決ロジック |
