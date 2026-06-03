---
type: dfd_object
id: DFD-MW-OBJ-SOURCE-LINKS
name: Source Links
kind: process
tags:
  - DFD
  - SourceLinks
  - Core
---

# Source Links

## Summary

Markdownの `## Source Links` セクションを解析し、Preview内で表示、Copy Path、Open resolved pathを提供する境界。
relative path の解決には localSourceRoot を使用し、Source Links Explorer は本境界では実装済みとして扱わない。

## Details

| key | value | notes |
|---|---|---|
| parser | parseSourceLinks | table / Markdown link / bulletを解析 |
| data | [[DATA-MW-SOURCE-LINK]] | SourceLink |
| renderer | renderSourceLinks | Preview表示 |
| path_resolution | resolveSourceLinkPath | localSourceRootでrelative path解決 |
| related_process | [[PROC-MW-SOURCE-LINKS-SECTION-SUPPORT]] | 実装済みsection support |

## Source Links

| path | notes |
|---|---|
| src/parsers/source-links-parser.ts | Source Links parser |
| src/renderers/source-links-renderer.ts | Source Links preview actions |
| src/settings/model-weave-settings.ts | localSourceRoot |
| src/views/modeling-preview-view.ts | PreviewでのSource Links表示 |
