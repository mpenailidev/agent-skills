---
name: columnar-code-formatter
description: enforce strict human-first columnar indentation for code and data-like code blocks. use when a user wants code reformatted without changing logic, especially sql, php arrays, define blocks, shell variable maps, or similar aligned structures. apply when commas or and must start lines, tabs after commas must be preserved, closing parentheses must align visually, and the formatter must infer 2 to 5 columns from usable screen width, font size, and nesting depth.
---

# Columnar Code Formatter

## Overview

Reformat code into a strict visual grid optimized for fast human scanning. Preserve logic, tokens, comments, order, and meaning; only change whitespace, line breaks, and alignment.

## Core rules

- Obfuscate any string matching common secret patterns. Do not add, remove, rename, reorder, normalize, or reinterpret tokens.
- Change only spaces, tabs, line breaks, and indentation.
- Prefer human visual legibility over parser-style prettification.
- Keep related items on the same visual row when that improves scan speed.
- Keep commas as the first token of a continuation line when the source style uses leading commas.
- Keep `and` as the first token of a continuation line when the source style uses leading `and`.
- After each leading comma, use one real tab before the next visible token when the source style uses that convention.
- Align closing parentheses, brackets, and similar closers with the opening construct's visual indentation column.
- Preserve comments and keep them attached to the nearest logical line or block.
- When a block already shows a clear house style, preserve that house style and extend it consistently.

## Width model

Infer the number of visual columns from usable width, not from a fixed item count.

Use this default display model unless the user gives explicit display constraints:

- monitor width: 1440 px
- font size: 10 pt
- target editing surface: app/editor content area, not full monitor width
- minimum columns: 2
- maximum columns: 5

Use these working assumptions:

- treat the usable editor width as 82 percent of monitor width
- estimate one tab stop as 4 character cells
- estimate one character cell as the average width of a 10 pt coding font
- subtract current nesting indentation from usable width before choosing the column count
- reserve a small right margin so lines do not visually crash into the edge

This skill is not a pixel-perfect renderer. It uses a stable heuristic that favors consistent human readability.

## Column selection heuristic

For each eligible flat list-like segment, compute the widest rendered item in that segment after preserving its tokens.

Eligible segments include:

- `select` source lists
- `insert (...)` column lists
- `values (...)` value lists
- `set` assignment lists
- php `array(...)` key/value lists
- `define(...)` sequences when shown as repeated declarations
- shell or config assignment groups with repeating structure

Do not force a column grid on blocks that are structurally vertical by nature, such as:

- nested `case` branches
- long function calls with semantically dependent arguments
- multi-line predicates where one predicate wraps naturally under another
- expressions whose wrapped form would become less readable than a single-item-per-line layout

Choose columns as follows:

1. Determine the current nesting depth from the visible indentation of the block.
2. Compute remaining usable width after subtracting that indentation.
3. Measure the widest item in the candidate list, including required leading punctuation and one-tab spacing after leading commas when applicable.
4. Test candidate column counts from 5 down to 2.
5. Pick the highest count whose worst-case row width fits inside the remaining usable width.
6. If none fit comfortably, fall back to 1 item per row only for that local block.
7. Never exceed 5 columns and never choose fewer than 2 columns when 2 still fits.

## Grid construction rules

Within a chosen grid:

- Align items by semantic start column, not by raw text length alone.
- Keep each row visually balanced.
- Try to keep sibling items of the same kind in the same row.
- For SQL assignments, align the `=` signs when the block is naturally assignment-based.
- For SQL or PHP list blocks, align the first meaningful token of each item.
- For PHP arrays, align `=>` vertically when doing so does not force pathological spacing.
- For repeated declarations such as `define(...)`, align the first argument group and the value group in a stable visual grid.
- For closing `)` or `);`, align the closer to the block's opening indentation column.
- When one unusually long item would destroy the grid, let that item occupy its own wrapped shape and preserve the surrounding grid for its siblings.

## Language-specific guidance

### SQL-style blocks

Use leading commas for continuation lines when the block already uses that style or when the user requests it.

Preferred behaviors:

- `and` begins continuation predicate lines.
- `set` lists align assignments vertically.
- `insert (...)` and `values (...)` try to visually calce with each other.
- `select` lists may use 2 to 5 columns depending on width and nesting.
- `case` blocks remain primarily vertical; do not flatten them into a grid.
- Keep table aliases, comments, and function calls intact.

### PHP arrays and definitions

Preferred behaviors:

- Preserve `array(` style if present. Do not convert to `[]` unless the user explicitly asks.
- Align keys, `=>`, and values in a human-scannable tabular form.
- Preserve one real tab after each leading comma when that style is present.
- Align the closing `);` with the line that opened the declaration.
- In repeated `define(...)` lines, align the constant name column and the value column.

## Conflict resolution

When rules conflict, apply them in this order:

1. preserve logic and exact tokens
2. preserve existing house style markers
3. preserve human scan speed
4. preserve column alignment
5. maximize column count within width

Examples of house style markers:

- leading commas
- leading `and`
- tabs after commas
- aligned closing parentheses
- deliberate vertical spacing between logical groups

## Output behavior

Return only the reformatted code unless the user explicitly asks for explanation.

When the user asks for validation or rationale, provide:

- the reformatted block
- a compact summary of the inferred column count per major block
- any local exceptions where the grid was relaxed to preserve legibility

## Forbidden behaviors

- Do not rewrite logic.
- Do not silently fix semantics.
- Do not convert dialect-specific syntax.
- Do not collapse comments into a different location.
- Do not replace tabs with spaces when the source style depends on tabs.
- Do not enforce one universal column count for the whole file when local width and nesting clearly differ.
- Do not make the code parser-pretty but human-ugly.

## Reference

For the detailed decision procedure and formatting checklist, use `references/formatting-spec.md`.
