# dead-routes

Find orphaned API endpoints — routes defined in the server but never called from client code.

## Trigger

Use when: user runs `/dead-api` or `/dead-ends`.

## Steps

1. **Detect router framework:**
   - Express: `grep -rn "app\.\(get\|post\|put\|patch\|delete\)" . --include="*.ts" --include="*.js"`
   - Fastify: `grep -rn "fastify\.\(get\|post\|put\|patch\|delete\)" . --include="*.ts" --include="*.js"`
   - Next.js pages: find files in `pages/api/` — each file is a route
   - Next.js app: find `route.ts` / `route.js` files in `app/` directory
   - Detect by checking which pattern yields hits first.

2. **Extract all defined routes** — for each router hit, capture:
   - HTTP method (GET/POST/PUT/PATCH/DELETE)
   - Path string (e.g., `/api/users/:id`)
   - File path and line number

3. **Search client/frontend code for calls to each route:**
   - `grep -rn "fetch\|axios\|useQuery\|useMutation\|api\." src/ --include="*.ts" --include="*.tsx"`
   - For each route path: strip dynamic segments (`:id` → match any string) and check if any client call includes that path string.
   - Match `/api/users` if route is `/api/users/:id`.

4. **Cross-check integration tests:**
   - `grep -rn "<ROUTE_PATH>" . --include="*.test.*" --include="*.spec.*" --include="*.e2e.*"`
   - Mark routes with test coverage even if no client call found.

5. **Build route table:**
   ```
   ORPHANED API ROUTES
   ─────────────────────────────────────────────────────────────────────────────
   Method  Path                        Defined In                  Client  Tests
   GET     /api/users/legacy           src/routes/users.ts:88      NO      NO
   POST    /api/export/csv             src/routes/export.ts:12     NO      NO
   DELETE  /api/admin/purge-cache      src/routes/admin.ts:45      NO      YES
   GET     /api/debug/state            src/routes/debug.ts:7       NO      NO

   Total routes scanned: 24
   Potentially orphaned: 4 (17%)
   With test coverage: 1
   ```

6. **Note caveat:** dynamic route matching via string search is heuristic — routes called programmatically (e.g., from a config object or string interpolation) may not be detected. Flag this in output.

7. **Guardrail** — this skill is read-only. Never delete route handlers. Removal via `dead-cleanup` skill.
