# dead-end-finder

Discover unreachable code, unused exports, and orphaned API endpoints that accumulate silently.

## Install

```bash
claude plugin install mturac/dead-end-finder
```

## Commands

| Command | What it does |
|---|---|
| `/dead-ends` | Run all dead code analysis (exports + routes) |
| `/dead-exports [path]` | Find unused exported symbols |
| `/dead-api` | Find orphaned API endpoints |
| `/dead-cleanup [symbol]` | Safely remove confirmed dead code |

## Example output

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
```

## Workflow

1. `/dead-ends` — get full picture of dead weight
2. `/dead-exports src/utils/` — drill into a specific path
3. `/dead-api` — check for orphaned routes
4. `/dead-cleanup formatCurrency` — remove confirmed dead export (requires YES)

## Skills

- `dead-exports` — finds unused named/default exports; classifies UNUSED / INTERNAL-ONLY / TEST-ONLY; respects barrel files
- `dead-routes` — detects Express/Fastify/Next.js routes; cross-checks client fetch calls and integration tests
- `dead-cleanup` — plan-first removal with explicit confirmation; syntax-checks modified files post-deletion
