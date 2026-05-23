---
type: data_object
id: DATA-MW-RENDERER-GRAPH-LABEL
name: 抽象グラフ内テキスト
kind: model
data_format: object
tags:
  - Renderer
  - Data
---

# 抽象グラフ内テキスト

## Summary

グラフ内の特定の座標や要素に関連付けられた、補足的なテキスト情報を保持します。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| text | テキスト内容 | string | | Y | | | |
| position | 配置ヒント | string | | N | | | center / source / target 等 |
| ownerRef | 所有要素参照 | string | | Y | | | 関連するノードまたはエッジのID |

## Notes

- Mermaidのノード内テキストではなく、グラフモデル全体の注釈等として扱われます。
