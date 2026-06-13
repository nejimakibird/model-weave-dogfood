---
type: app_process
id: PROC-MW-PARSER-PARSE-VAULT-FILE
name: Vaultファイル解析
process_type: batch
kind: parser
tags:
  - Parser
  - Core
---

# Vaultファイル解析

## Summary

Obsidian Vault内の物理的なMarkdownファイルを読み込み、Model Weaveの各種モデル種別を判定した上で、構造化された論理モデルへとパースします。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Model Weave architecture domains |

## Triggers

| id | kind | source | event | notes |
|---|---|---|---|---|
| T1 | system | Vault Indexer | ファイル走査時 | |
| T2 | user | Obsidian | ファイル修正時 | |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| source | [[DATA-MW-CORE-VAULT-FILE]] | Obsidian | Y | Vaultファイル情報 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| result | [[DATA-MW-CORE-PARSED-MODEL]] | Vault Index | 解析済みモデル |
| diag | [[DATA-MW-CORE-DIAGNOSTIC]] | Viewer | 診断情報 |

## Transitions

| id  | event | to                                     | condition                 | notes              |
| --- | ----- | -------------------------------------- | ------------------------- | ------------------ |
| TR1 | 解析成功  | [[PROC-MW-RENDERER-BUILD-GRAPH-MODEL]] | fileType が diagram 系の場合   | インデックス登録後に描画構築へ進む  |
| TR2 | 解析対象外 | -                                      | parseResult.file が存在しない場合 | warnings を保持して終了する |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | parser_resolver | Vaultファイル解析開始 | start | source |  |  |  |  | indexSingleFile から parseVaultFile が呼び出される |
| receiveFile | parser_resolver | Vault Markdownファイルを受け取る | input | source |  |  |  |  | path と content または frontmatter を入力とする |
| chooseParseMode | parser_resolver | parseModeを判定する | decision | source |  |  |  |  | shallow / full を分岐する |
| parseShallowFrontmatter | parser_resolver | shallow用frontmatterを取得する | process | source | frontmatter |  |  |  | 既存frontmatterがなければ parseFrontmatter を使う |
| detectShallowType | parser_resolver | shallow fileTypeを検出する | process | frontmatter | fileType |  |  |  | detectFileType で schema / type を判定する |
| createShallowModel | parser_resolver | shallowモデルを作成する | process | fileType | result |  |  |  | createShallowModel で最小モデルを返す |
| readContent | parser_resolver | Markdown本文を読み込む | process | source | content |  |  |  | content が未設定の場合は空文字として扱う |
| parseFrontmatter | parser_resolver | frontmatterを解析する | process | content | frontmatter |  |  |  | parseFrontmatter で本文とfrontmatterを分離する |
| fastTypeDispatch | parser_resolver | 直接type分岐を判定する | decision | frontmatter |  |  |  |  | 一部の `type` は detectFileType の前に直接分岐される |
| runFastParser | parser_resolver | type対応パーサへ委譲する | subflow | content | result |  |  |  | data_object / app_process / screen 等の直接分岐 |
| detectFileType | parser_resolver | schema / type からfileTypeを検出する | process | frontmatter | fileType |  |  |  | schema があれば schema mapping を優先する |
| fileTypeDispatch | parser_resolver | 検出fileTypeで分岐する | decision | fileType |  |  |  |  | object / relations / diagram / markdown 等へ分岐する |
| runRelationsParser | parser_resolver | Relationsパーサへ委譲する | subflow | content | result |  |  |  | `schema: model_relations_v1` は parseRelationsFile へ渡す |
| runDetectedParser | parser_resolver | fileType対応パーサへ委譲する | subflow | content | result |  |  |  | object / dfd / er_entity 等のパーサへ渡す |
| createMarkdown | parser_resolver | markdownモデルを作成する | process | content | result |  |  |  | unsupported schema / type は markdown として扱う |
| collectResult | parser_resolver | parseResultをまとめる | process | result | result, diag |  |  |  | parser warnings を diagnostics相当として保持する |
| hasParsedModel | model_storage | parsed model があるか判定する | decision | result |  |  |  |  | null の場合は index 登録しない |
| indexResult | model_storage | 解析済みモデルをindexへ登録する | process | result | result |  |  |  | indexSingleFile が fileType ごとの索引へ追加する |
| endParsed | parser_resolver | 解析結果を返す | end | result, diag |  |  |  |  | Vault Index とViewer描画の入力になる |
| endNoModel | parser_resolver | warningsのみで終了する | end | diag |  |  |  |  | parseResult.file が null の場合 |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| chooseParseMode | parseShallowFrontmatter | `parseMode === "shallow"` | shallow | shallowモデル作成へ進む |
| chooseParseMode | readContent | `parseMode !== "shallow"` | full | full parseへ進む |
| createShallowModel | collectResult |  | shallow result | shallowモデルを結果へまとめる |
| fastTypeDispatch | runFastParser | `frontmatter.type === direct type` | direct type | data_object / app_process / screen 等 |
| fastTypeDispatch | detectFileType |  | schema/type検出 | 通常の detectFileType へ進む |
| runFastParser | collectResult |  | parser result | 直接分岐パーサの結果をまとめる |
| fileTypeDispatch | runRelationsParser | `fileType === "relations"` | relations | schema-driven Relations parserへ委譲する |
| fileTypeDispatch | createMarkdown | `fileType === "markdown"` | markdown fallback | markdownモデルとして扱う |
| fileTypeDispatch | runDetectedParser |  | detected parser | その他のfileType対応パーサへ委譲する |
| runRelationsParser | collectResult |  | relations result | Relations parserの結果をまとめる |
| runDetectedParser | collectResult |  | parser result | fileType対応パーサの結果をまとめる |
| createMarkdown | collectResult |  | markdown result | markdown fallback結果をまとめる |
| hasParsedModel | indexResult | `parseResult.file` | parsed | index登録へ進む |
| hasParsedModel | endNoModel | `!parseResult.file` | no model | warningsのみで終了する |
| indexResult | endParsed |  | indexed | 解析結果を返す |

## Errors

- **E-PRS-001**: フロントマターの構文エラー（閉じ忘れ等）
- **E-PRS-002**: unsupported schema / type は markdown fallback として扱われる場合がある

## Notes

- パース処理は、物理ファイルへの依存を最小限にするため、パス情報ではなくテキスト内容に基づいて行われます。
- shallow parse は frontmatter から最小モデルを作成し、本文セクションの詳細解析を行わない。
- full parse は一部 `frontmatter.type` の直接分岐を先に確認し、その後 `detectFileType` による schema / type detection を行う。
- `schema: model_relations_v1` は `relations` fileType として扱われ、parseRelationsFile に委譲される。
- unsupported schema / type は `markdown` fileType として createMarkdownModel へfallbackする。
- indexSingleFile は parseResult.file が存在する場合のみ fileType ごとの索引へ登録する。
- Markdown安全記法のため、複雑な型表現は山括弧表記ではなく自然文で説明します。

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | Steps: start, hasParsedModel, indexResult. parseVaultFile / indexSingleFile による解析とindex登録 |
| src/parsers/frontmatter-parser.ts | Steps: parseShallowFrontmatter, parseFrontmatter. YAML frontmatter抽出 |
| src/core/schema-detector.ts | Steps: detectShallowType, detectFileType. schema / type による fileType 判定 |
| src/parsers/relations-parser.ts | Steps: runRelationsParser. schema-driven Relations ファイル解析 |
