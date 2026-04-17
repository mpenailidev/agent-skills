# Formatting Spec

## Decision procedure

### Step 1: Detect house style

Inspect the input before touching layout.

Detect whether the block uses:

- leading commas
- leading `and`
- hard tabs after commas
- aligned closers
- assignment alignment
- row grouping by blank lines

If a style is already consistent, keep it.

### Step 2: Split into format units

A format unit is a sibling list that can be laid out on a grid without changing logic.

Examples:

- items inside `insert (...)`
- items inside `values (...)`
- items in a SQL `select`
- assignments in `set`
- entries inside `array(...)`
- repeated `define(...)` statements considered as one visual family

Do not merge unrelated units into one grid.

### Step 3: Estimate width

Use:

- monitor width = 1440 px
- usable width = 82 percent
- font size = 10 pt
- local indentation cost = current left padding of the unit
- right safety margin = 5 percent of usable width

Approximate widths consistently; exact font metrics are not required.

### Step 4: Choose column count

Try 5, 4, 3, 2 in that order.

For each candidate:

- compute the visual width of the widest item
- multiply by candidate count
- add inter-column padding
- add local indentation
- accept the first candidate that comfortably fits

If 2 does not fit, use a single-item vertical layout for that unit only.

### Step 5: Align tokens

#### Lists with leading commas

- first item may appear without leading comma if that is the surrounding style
- subsequent lines start with `,`
- after `,` place one real tab when the source style uses it
- align the first meaningful token of each item after the comma/tab prefix

#### Predicates with leading `and`

- continuation lines begin with `and`
- align predicate bodies after `and`
- nested conditions may break vertically if that is easier to scan

#### Assignments

- align left-hand names together
- align `=` vertically
- align right-hand expressions from a common start column when practical

#### Closers

- `)` or `);` align with the opening construct's indentation column
- nested closers staircase naturally with their owning blocks

## PHP-specific notes

### Arrays

Preferred shape:

- one logical entry per grid cell
- keys aligned
- `=>` aligned where practical
- values begin at a stable column
- closer aligned with the assignment start

### Defines

Treat repeated `define(...)` lines as a declaration family.

- align the constant argument block
- align the value argument block
- preserve spaces or tabs used to emphasize readability

## SQL-specific notes

### Insert and values calce

When an `insert (...)` block is followed by `values (...)` or `select ...` used as source values:

- make the row grouping visually correspond when possible
- do not force exact character-for-character matching if expressions differ a lot in width
- prioritize recognizable pairing over perfect geometry

### Merge blocks

In `merge`, prefer:

- `on` predicates vertical with leading `and`
- `set` assignments vertical
- `insert (...)` and `values (...)` in compatible grids
- `case` expressions vertically nested inside their assignment

## Final checklist

Before returning the result, verify:

- logic unchanged
- tokens unchanged
- comments preserved
- leading comma style preserved when applicable
- leading `and` style preserved when applicable
- tabs preserved where style depends on them
- closers aligned
- each local unit uses 2 to 5 columns when that fits the width heuristic
- exceptionally long items handled locally without destroying nearby alignment
