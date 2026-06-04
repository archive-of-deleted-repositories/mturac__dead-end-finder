# /dead-api

**Usage:** `/dead-api`

Find orphaned API endpoints — routes that exist in your server but are never called from client code.

## What it does

Runs `dead-routes` skill:
- Auto-detects framework (Express, Fastify, Next.js pages, Next.js app)
- Extracts all defined routes (method + path)
- Searches frontend/client code for matching fetch/axios/useQuery calls
- Cross-checks integration test files
- Outputs a table of potentially orphaned routes

## Example

```
/dead-api
```

```
ORPHANED API ROUTES
─────────────────────────────────────────────────────────────────────────────
Method  Path                    Defined In                  Client  Tests
GET     /api/users/legacy       src/routes/users.ts:88      NO      NO
POST    /api/export/csv         src/routes/export.ts:12     NO      NO
DELETE  /api/admin/purge-cache  src/routes/admin.ts:45      NO      YES
GET     /api/debug/state        src/routes/debug.ts:7       NO      NO

Total routes scanned: 24
Potentially orphaned: 4 (17%)
With test coverage only: 1

Note: Routes called via dynamic string interpolation may not be detected.
```

## Next step

Run `/dead-cleanup [route-path]` to remove a confirmed orphaned route.
