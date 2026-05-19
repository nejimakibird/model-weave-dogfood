---
type: rule
id: RULE-MW-RENDERER-MERMAID-LABEL-ESCAPE
name: Mermaid Label Escape Rule
kind: transformation
---

# Mermaid Label Escape Rule

## Summary

Mermaid 構文において、構文エラーを引き起こさずに正しく表示・制御するためのエスケープおよび ID 安全化規則を定義します。

## Conditions

| no | condition | result | notes |
|---|---|---|---|
| 1 | ノード ID の生成 | 英数字とアンダースコアのみ（例: `mw_n_0`） | 表示ラベルやパスを直接 ID に使用しない。 |
| 2 | 特殊文字のエスケープ | `[` `]` `(` `)` `"` などの文字を HTML エンティティまたはエスケープ処理 | Mermaid の形状定義文字との衝突を避ける。 |
| 3 | ラベルのクオート | 常にダブルクォートで囲む | 文字列中のスペースや特殊記号を許容する。 |
| 4 | クリックナビゲーション | Mermaid 内部の callback を使用せず SVG 後処理用 ID を付与 | セキュリティ上の制約（loose 等）を回避するため。 |

## Notes

- Mermaid 固有の構文制約を吸収することで、`app_process` 側では単純な組み立てに集中できるようにします。
- 今後、特定のレンダラー（例: Mermaid sequence diagram）特有の制約が増えた場合は、このルールを分割または拡張します。

---