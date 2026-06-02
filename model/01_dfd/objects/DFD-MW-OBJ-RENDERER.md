---
type: dfd_object
id: DFD-MW-OBJ-RENDERER
name: Renderer
kind: process
tags:
  - DFD
  - Renderer
---

# Renderer

## Summary

解析済みモデル（Vault Index）を元に、Mermaidなどの図面定義ソースの生成、およびカスタムビュー用の抽象グラフモデルを構築する描画プロセスオブジェクト。

## Details

| key | value | notes |
|---|---|---|
| owner | Renderer Package | 責務パッケージ |
| related_process | [[PROC-MW-RENDERER-BUILD-GRAPH-MODEL]] | グラフモデル構築プロセス |
| related_process | [[PROC-MW-RENDERER-GENERATE-MERMAID-SOURCE]] | Mermaidソース生成プロセス |
