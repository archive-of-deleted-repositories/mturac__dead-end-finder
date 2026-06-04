# /dead-ends

**Usage:** `/dead-ends`

Run all dead code analysis in one shot — unused exports AND orphaned routes.

## What it does

Runs `dead-exports` and `dead-routes` skills sequentially and combines output:
1. Scan all exports for unused symbols
2. Scan all API route definitions for missing client calls
3. Output combined summary table

## Example

```
DEAD END REPORT
════════════════════════════════════════

UNUSED EXPORTS (3 found)
─────────────────────────────────────────────────────────────────
File                              Symbol              Last Modified
src/utils/legacyFormat.ts:12      formatCurrency      2023-08-14
src/utils/legacyFormat.ts:28      parseDate           2023-08-14
src/services/OldAuthService.ts:1  OldAuthService      2023-11-02

ORPHANED ROUTES (2 found)
─────────────────────────────────────────────────────────────────
Method  Path                   Defined In               Client  Tests
GET     /api/users/legacy      src/routes/users.ts:88   NO      NO
POST    /api/export/csv        src/routes/export.ts:12  NO      NO

────────────────────────────────────────
Total dead weight found: 5 items
Run /dead-cleanup [symbol] to remove confirmed items.
```
