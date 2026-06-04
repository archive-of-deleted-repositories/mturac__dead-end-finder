# /dead-exports

**Usage:** `/dead-exports [path]`

Find unused exported symbols in the codebase or a specific path.

## Arguments

- No arg: scan entire project
- Path arg: limit scan to a specific directory (e.g., `/dead-exports src/utils/`)

## What it does

Runs `dead-exports` skill:
- Finds all `export` declarations
- Checks for any import of each symbol outside the source file
- Classifies: UNUSED / INTERNAL-ONLY / TEST-ONLY
- Checks barrel index files before flagging
- Shows last modified date (git log)

## Example

```
/dead-exports src/utils/
```

```
DEAD EXPORTS REPORT — src/utils/
─────────────────────────────────────────────────────────────────
File                          Symbol           Type     Last Modified
src/utils/legacyFormat.ts:12  formatCurrency   named    2023-08-14
src/utils/legacyFormat.ts:28  parseDate        named    2023-08-14
src/utils/flags.ts:44         BETA_FLAG_OLD    named    2024-01-15

Total unused exports: 3
UNUSED: 2 | INTERNAL-ONLY: 0 | TEST-ONLY: 1
```

## Next step

Run `/dead-cleanup [symbol]` to remove a confirmed dead export.
