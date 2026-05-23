---
type: class_diagram
id: CLASSD-MW-RENDERER-STRUCTURE
name: レンダラーパッケージ構造
tags:
  - Renderer
  - Diagram
---

# レンダラーパッケージ構造

## Summary

解析済みのモデル（Parsed Model）から最終的な Mermaid ソースへ至る、レンダリングパイプラインの構成クラスを示します。

## Objects

| ref | notes |
|---|---|
| [[CLS-MW-RENDERER-RENDER-MODE-RESOLVER]] | 描画戦略の決定 |
| [[CLS-MW-RENDERER-GRAPH-MODEL-BUILDER]] | グラフ構築の主責務 |
| [[CLS-MW-RENDERER-DFD-MERMAID-RENDERER]] | DFD Mermaid変換 |
| [[CLS-MW-RENDERER-CLASS-ER-MERMAID-RENDERER]] | Class/ER Mermaid変換 |
| [[CLS-MW-RENDERER-DIAGNOSTICS-COLLECTOR]] | 診断情報の集約 |

## Relations

| id | from | to | kind | label | from_multiplicity | to_multiplicity | notes |
|---|---|---|---|---|---|---|---|
| REL-RS-RESOLVE-GRAPH | [[CLS-MW-RENDERER-RENDER-MODE-RESOLVER]] | [[CLS-MW-RENDERER-GRAPH-MODEL-BUILDER]] | dependency | resolves for | | | |
| REL-RS-BUILD-DIAG | [[CLS-MW-RENDERER-GRAPH-MODEL-BUILDER]] | [[CLS-MW-RENDERER-DIAGNOSTICS-COLLECTOR]] | dependency | collects via | | | |
| REL-RS-BUILD-DFD | [[CLS-MW-RENDERER-GRAPH-MODEL-BUILDER]] | [[CLS-MW-RENDERER-DFD-MERMAID-RENDERER]] | association | delegates to | | | |
| REL-RS-BUILD-CLER | [[CLS-MW-RENDERER-GRAPH-MODEL-BUILDER]] | [[CLS-MW-RENDERER-CLASS-ER-MERMAID-RENDERER]] | association | delegates to | | | |

## Notes

- 本構造は `PROC-MW-RENDERER-BUILD-GRAPH-MODEL` および `PROC-MW-RENDERER-GENERATE-MERMAID-SOURCE` の具体的な実装責務を表現しています。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/relation-resolver.ts | resolveDiagramRelations | function | グラフ構築プロセスの起点 |