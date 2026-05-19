---
id: GUIDE-MW-FRONTMATTER-SAFE-WIKILINK
name: frontmatter内Wikilink安全記述ガイド
tags:
  - Frontmatter
  - Wikilink
  - Guide
---

# frontmatter内Wikilink安全記述ガイド

## Summary

Model WeaveのMarkdownモデルにおいて、frontmatter内でWikilink（`[[...]]`）を記述する際の安全な記法について説明します。

## Rule

- **frontmatter内でWikilinkを記述する場合は、必ずダブルクォートで囲んでください。**
- 未クォートの `[[...]]` は、YAMLパーサーが配列として誤認したり、Obsidianのリンク解決が期待通りに機能しなかったりする原因となるため、使用禁止です。
- Markdown本文やテーブル内でのWikilinkは、従来通りクォートなしで記述可能です。
- リンク機能（クリック遷移やバックリンク）が不要な項目については、Wikilink形式を使わずID文字列のみを記述しても問題ありません。

## Examples

### Bad
```yaml
source: [[DATA-MW-DFD-FLOW-ENTRY]]
```

### Good
```yaml
source: "[[DATA-MW-DFD-FLOW-ENTRY]]"
```

### Also acceptable (リンク機能が不要な場合)
```yaml
source: DATA-MW-DFD-FLOW-ENTRY
```

## Review targets

以下のfrontmatter項目は他モデルへの参照によく使用されるため、重点的に確認してください。

- `source` / `target`
- `ref` / `related`
- `parent` / `owner`
- `rule`