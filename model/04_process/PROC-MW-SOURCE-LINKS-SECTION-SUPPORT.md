---
type: app_process
id: PROC-MW-SOURCE-LINKS-SECTION-SUPPORT
name: Source Links Section Support
process_type: preview_support
kind: renderer
tags:
  - ModelWeave
  - SourceLinks
  - Process
---

# Source Links Section Support

## Summary

Supports implemented `## Source Links` sections in Model Weave preview.
The process parses Source Links from model Markdown, renders them in the preview, resolves relative paths with `localSourceRoot`, provides preview Copy Path / Open actions, and includes related Source Links in relationship / impact summaries.

This process does not model Source Links Explorer. Explorer screens, filtering, selection, indexing, and Explorer-specific copy/open workflows remain future/planned and out of scope.

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| sourceLinksSection | Markdown section lines | Model file `## Source Links` | N | Table rows or simple line entries parsed from the model body |
| localSourceRoot | string | Plugin settings / viewer preferences | N | Used when a Source Link path is relative |
| parsedModel | Parsed model | Preview / relationship analysis | Y | Model that may contain parsed Source Links |
| vaultIndex | Modeling vault index | Relationship / impact analysis | N | Used when collecting related Source Links |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| sourceLinks | SourceLink[] | Parsed model state | Parsed Source Links with path and optional label / notes |
| previewSection | HTMLElement | Modeling preview | Source Links table with path status, resolved path, notes, and actions |
| copiedPath | string | Clipboard | Resolved path copied by the Copy Path action |
| openRequest | resolved path | OS/default app | Open action request for an openable resolved path |
| relatedSourceLinks | ImpactSourceLink[] | Relationship / impact summary | Source Links from the current model and related inbound/outbound models |

## Steps

1. Parse the optional `## Source Links` section into Source Link entries.
2. Accept either Markdown table rows or simple line entries.
3. Normalize supported source path forms, including table headers such as `path`, `source`, `source_path`, or `file`.
4. Keep optional label / notes values when present.
5. Render a Source Links preview section only when at least one parsed Source Link has a non-empty path.
6. Resolve each path for preview status display. Relative paths use `localSourceRoot` when it is configured as a supported absolute source root.
7. Show the source path, status, resolved path, notes, and actions in the preview table.
8. Copy Path writes the resolved path to the clipboard.
9. Open requests the OS/default app to open the resolved path when the path is openable.
10. Relationship / impact summary collection includes Source Links from the current model, resolved outbound target models, and inbound source models.

## Notes

- Source Links section support is implemented in preview and relationship / impact summary behavior.
- `localSourceRoot` is the implemented setting for resolving relative Source Link paths.
- File URI paths are treated as unsupported for direct open behavior.
- Missing paths may still be displayed with status and remain copyable/openable according to renderer behavior.
- Source Links Explorer is not implemented by this process.
- This process does not define a screen, Explorer state, Explorer filtering, or Explorer selection behavior.

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/parsers/source-links-parser.ts | parseSourceLinks | function | Parses `## Source Links` table and line entries |
| src/renderers/source-links-renderer.ts | renderSourceLinks | function | Renders preview table, path status, Copy Path, and Open actions |
| src/renderers/source-links-renderer.ts | resolveSourceLinkPath | function | Resolves relative Source Link paths using `localSourceRoot` |
| src/core/impact-analyzer.ts | collectRelatedSourceLinks | function | Collects Source Links for relationship / impact summary |
| src/core/impact-analyzer.ts | formatImpactSummaryAsMarkdown | function | Emits Related Source Links in impact summary Markdown |
| src/views/modeling-preview-view.ts | renderSourceLinks | usage | Adds Source Links sections to preview output |
| src/settings/model-weave-settings.ts | ModelWeaveSettings.localSourceRoot | setting | Stores the local source root used for relative path resolution |
