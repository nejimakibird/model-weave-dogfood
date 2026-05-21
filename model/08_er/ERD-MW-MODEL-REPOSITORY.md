---
type: er_diagram
id: ERD-MW-MODEL-REPOSITORY
name: Model Weave Model Repository ER
tags:
  - ModelWeave
  - ER
  - Repository
---

# Model Weave Model Repository ER

## Summary

Model WeaveがObsidian Vault内のMarkdownファイルを、モデル資産・参照・診断・ソースリンクとして扱う論理構造を表すER図。
これは実DBスキーマではなく、Dogfoodモデルを理解するための論理ERである。

## Objects

| ref | notes |
|---|---|
| [[ENT-MW-MODEL-FILE]] | Vault内のMarkdownファイル |
| [[ENT-MW-MODEL-ASSET]] | Model Weaveが認識したモデル資産 |
| [[ENT-MW-MODEL-REFERENCE]] | モデル間参照 |
| [[ENT-MW-DIAGNOSTIC]] | 診断結果 |
| [[ENT-MW-SOURCE-LINK]] | 実装ソース対応 |

## Notes

- relationは各er_entityのRelationsから集約する。
- Markdownファイルが正本であり、ERはリポジトリ構造を理解するための派生ビューである。
- ModelAssetはfrontmatter type/id/nameを持つModel Weave管理対象を表す。
- ModelReferenceはWikilinkやID参照によるモデル間リンクを表す。
- Diagnosticは未解決参照、テーブル不備、frontmatter不備などの診断結果を表す。
- SourceLinkはDogfoodモデルと実装ソースの対応を表す。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/models/core/model-repository.ts | ModelRepository | class | リポジトリ管理の実装 (未確認) |
| source/model-weave-repo/src/models/core/file-parser.ts | parseModelFile | function | ファイルからアセットへの解析 (未確認) |