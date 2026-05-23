---
type: app_process
id: PROC-MW-RENDERER-BUILD-GRAPH-MODEL
name: 抽象グラフモデル構築
kind: renderer
tags:
  - Renderer
---

# 抽象グラフモデル構築

## Summary

解析済みの論理モデルから、特定の描画構文に依存しない `DATA-MW-RENDERER-GRAPH-MODEL` を構築します。Vault Indexを用いた他モデルへの参照解決（Reference Resolution）を本プロセスで行います。

## Triggers

| id | kind | source | event | notes |
|---|---|---|---|---|
| T1 | system | Viewer | プレビュー表示要求時 | |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| model | [[DATA-MW-CORE-PARSED-MODEL]] | Parser | Y | 解析済みモデル |
| index | Vault Index | Cache | Y | |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| graph | [[DATA-MW-RENDERER-GRAPH-MODEL]] | Renderer | 抽象グラフモデル |

## Transitions

| id | event | to | condition | notes |
|---|---|---|---|---|
| TR1 | 構築完了 | PROC-MW-RENDERER-GENERATE-MERMAID-SOURCE | render_mode が mermaid の場合 | |
| TR2 | 構築完了 | - | render_mode が custom の場合 | カスタム描画へ移行 |

## Steps

1. **ノード抽出**: `ParsedModel` 内の `objectRefs` や `objectEntries` を走査し、`DATA-MW-RENDERER-GRAPH-NODE` のリストを作成する。
2. **リレーション解決**: `ParsedModel` 内の関連定義（Relations / Flows）から接続情報を抽出する。
3. **参照解決**: `reference-resolver` を使用し、Wikilink や ID 指定を Vault 内の絶対パスおよびモデル ID に確定させる。
4. **属性付与**: ノードの `kind` に基づき、表示ラベル（`label`）やスタイル用のメタデータを付与する。
5. **整合性チェック**: 接続先が見つからないリレーションに対し、`DATA-MW-CORE-DIAGNOSTIC` (unresolved-reference) を生成する。

## Errors

- **E-BGD-001**: 循環参照の検出（必要時）
- **E-BGD-002**: 必須ノードの欠落

## Notes

- 特定の描画エンジン（Mermaid等）に変換する前の中間表現を生成するのが本プロセスの責務です。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/relation-resolver.ts | resolveDiagramRelations | function | 図面種別（ER/DFD等）に応じたノード・エッジの解決 |