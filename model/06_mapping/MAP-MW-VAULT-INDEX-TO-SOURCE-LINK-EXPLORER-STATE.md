---
type: mapping
id: MAP-MW-VAULT-INDEX-TO-SOURCE-LINK-EXPLORER-STATE
name: Vault Index to Source Links Explorer State Mapping
kind: data_to_data
source: "[[DATA-MW-VAULT-MODEL-INDEX]]"
target: "[[DATA-MW-SOURCE-LINK-EXPLORER-STATE]]"
tags:
  - ModelWeave
  - SourceLinks
  - Explorer
  - Mapping
---

# Vault Index to Source Links Explorer State Mapping

## Summary

[[DATA-MW-VAULT-MODEL-INDEX]] が保持するモデル資産とSource Links情報を、Source Links Explorerで表示・検索しやすい [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] へ変換するマッピングを定義する。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-VAULT-MODEL-INDEX]] | Vault内モデル資産の派生インデックス |
| source | [[ENT-MW-SOURCE-LINK]] | Source Links行 |
| source | [[ENT-MW-MODEL-ASSET]] | Source Linkを持つモデル資産 |
| source | [[DATA-MW-CORE-DIAGNOSTIC]] | Source Links関連診断 |
| target | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] | Source Links Explorer表示状態 |
| rule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | 診断severity分類 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[DATA-MW-VAULT-MODEL-INDEX]] | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].sourceIndexId | 表示用IDを派生 | | N | 実装IndexにindexId fieldはない |
| [[ENT-MW-SOURCE-LINK]].sourceLinkId | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedSourceLinkId | 初期状態では空 | | N | ユーザー選択後に設定 |
| [[ENT-MW-SOURCE-LINK]].assetId | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedAssetId | 初期状態では空 | | N | 選択時に設定 |
| [[DATA-MW-VAULT-MODEL-INDEX]].modelsByFilePath | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].sourceLinkCount | 各ParsedFileModelのsourceLinks件数を集計 | | N | 現行Indexに専用Source Links mapはない |
| [[DATA-MW-VAULT-MODEL-INDEX]].modelsByFilePath | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].linkedAssetCount | sourceLinksを持つモデル件数を集計 | | N | Source Links付き資産数 |
| [[DATA-MW-VAULT-MODEL-INDEX]].modelsByFilePath | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].missingSourceLinkCount | sourceLinksを持たないモデル件数を集計 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | future Explorer用の派生値 |
| [[DATA-MW-VAULT-MODEL-INDEX]].modelsByFilePath | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].visibleSourceLinks | 各モデルのsourceLinksを展開 | | Y | 複数のSource Linkを表示用に保持 |
| [[DATA-MW-VAULT-MODEL-INDEX]].modelsByFilePath | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedModelAssets | Source Link所有モデルを抽出 | | N | リンクに紐づく資産一覧 |
| [[DATA-MW-VAULT-MODEL-INDEX]].warningsByFilePath | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedDiagnostics | Source Links関連診断を抽出 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 設定漏れ等の診断 |
| | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].sourcePathFilter | 空文字を設定 | | N | path検索条件 |
| | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].symbolFilter | 空文字を設定 | | N | symbol検索条件 |
| | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].sourceKindFilter | all を設定 | | N | kindフィルタ |
| | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].modelTypeFilter | all を設定 | | N | model typeフィルタ |
| | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].searchText | 空文字を設定 | | N | 全文検索 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].visibleSourceLinks | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].filteredSourceLinkCount | リストの件数を計算 | | N | 初期表示件数 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].visibleSourceLinks | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].explorerStatus | 件数や診断から決定 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | ready / empty / error |

## Rules

- 初期表示では modelsByFilePath 内の各ParsedFileModel.sourceLinksを展開し、`visibleSourceLinks` に反映する。
- `sourceLinkCount` は modelsByFilePath 内の sourceLinks 件数から算出する。
- `linkedAssetCount` はSource Linkを1件以上持つモデル数として算出する。
- `missingSourceLinkCount` は対象モデルのうちSource Linkを持たない件数として算出する。
- `relatedModelAssets` は Source Link.assetId に紐づくModelAssetを集約して保持する。
- `relatedDiagnostics` はSource Links不足や未確認リンクなど、Source Linksに関連する診断コードを持つ [[DATA-MW-CORE-DIAGNOSTIC]] を抽出する。
- `sourcePathFilter` / `symbolFilter` / `searchText` は初期状態では空文字にする。
- `sourceKindFilter` / `modelTypeFilter` は初期状態では `all` とする。
- `filteredSourceLinkCount` は `visibleSourceLinks` の現在の件数から計算する。
- `explorerStatus` は `visibleSourceLinks` が空なら `empty`、error診断があれば `error`、それ以外は `ready` とする。
- 本mappingは表示用の派生状態の生成であり、Markdown本文やfrontmatterは変更しない。

## Notes

- [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] は [[DATA-MW-VAULT-MODEL-INDEX]] から導出される表示用状態である。
- Source Links Explorer はfuture/plannedであり、現行実装のSource Linksは各ParsedFileModel.sourceLinksとして保持される。
- Source Linksはモデル資産から実装ソースへ辿るためのナビゲーション情報であり、実装ソースそのものを保存するものではない。
- 選択Source Linkやフィルタ条件はSource Links Explorerの一時状態であり、Markdownモデルには保存しない。
- ファイルパスの表示短縮はUIマッピング側で [[RULE-MW-PATH-SHORTENER]] に委ねる。
- [[ENT-MW-SOURCE-LINK]] の物理名は assetId / path / symbol / kind / notes を使用する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | VaultIndex | class | Source Links情報の索引元 |
| src/core/schema-detector.ts | detectModelType | function | モデル資産種別の検出 |
| src/core/current-file-diagnostics.ts | buildCurrentObjectDiagnostics | function | Source Links関連診断の生成 |
