# /dead-cleanup

**Usage:** `/dead-cleanup [symbol]`

Safely remove a confirmed dead export or orphaned route — with plan preview and explicit confirmation.

## Arguments

- Symbol name: `formatCurrency`
- Route path: `/api/users/legacy`

## What it does

Runs `dead-cleanup` skill:
1. Shows what will be removed and from which files
2. Generates a removal plan (file + line)
3. Asks for YES confirmation before touching anything
4. Applies removal only after confirmed
5. Checks for syntax errors post-removal (dangling commas, empty export blocks)

## Example

```
/dead-cleanup formatCurrency
```

```
Removing: formatCurrency (named export)
Defined in: src/utils/legacyFormat.ts:12
Classification: UNUSED

REMOVAL PLAN
────────────────────────────────────────
1. src/utils/legacyFormat.ts:12
   DELETE: export function formatCurrency(value: number): string {

2. src/index.ts:8
   DELETE: export { formatCurrency } from './utils/legacyFormat';

Ready to apply. Type YES to confirm removal of formatCurrency, or NO to cancel.
```

After YES:

```
CLEANUP COMPLETE: formatCurrency
─────────────────────────────────
Files modified: 2 | Lines removed: 3

✓ src/utils/legacyFormat.ts — export removed
✓ src/index.ts — barrel re-export removed

Syntax check: no issues detected.
```

## Safety

Never removes without showing the plan. Never proceeds without YES.
