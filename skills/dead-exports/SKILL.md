# dead-exports

Find all exported symbols that are never imported anywhere — the silent dead weight.

## Trigger

Use when: user runs `/dead-exports [path]` or `/dead-ends`.

## Steps

1. **Collect all exports** — scan for exported symbols:
   ```bash
   grep -rn "^export " src/ --include="*.ts" --include="*.tsx" --include="*.js"
   grep -rn "^export default " src/ --include="*.ts" --include="*.tsx"
   grep -rn "module\.exports" src/ --include="*.js"
   ```
   Extract: file path, line number, symbol name, export type (named / default / re-export).

2. **For each export, search for imports** (exclude the file itself):
   ```bash
   grep -rn "import.*{<SYMBOL>}" . --include="*.ts" --include="*.tsx" --include="*.js" | grep -v "<SOURCE_FILE>"
   grep -rn "require.*<SYMBOL>" . --include="*.js" | grep -v "<SOURCE_FILE>"
   ```

3. **Classify each export:**
   - **UNUSED** — zero import hits anywhere
   - **INTERNAL-ONLY** — imported only within the same package directory
   - **TEST-ONLY** — imported only in `*.test.*` or `*.spec.*` files

4. **Check barrel files** — if a symbol is re-exported through `index.ts` or `lib/index.ts`, count it as used even if direct imports are absent:
   ```bash
   grep -n "<SYMBOL>" src/index.ts lib/index.ts 2>/dev/null
   ```

5. **Get last modified date** for each dead export file:
   ```bash
   git log -1 --format="%ai" -- <filepath>
   ```
   Use `stat` as fallback if not in a git repo.

6. **Output table:**
   ```
   DEAD EXPORTS REPORT
   ─────────────────────────────────────────────────────────────────
   File                              Symbol              Type       Last Modified
   src/utils/legacyFormat.ts:12      formatCurrency      named      2023-08-14
   src/utils/legacyFormat.ts:28      parseDate           named      2023-08-14
   src/services/OldAuthService.ts:1  OldAuthService      default    2023-11-02
   src/constants/flags.ts:44         BETA_FLAG_LEGACY    named      2024-01-15

   Total unused exports: 4
   UNUSED: 3 | INTERNAL-ONLY: 0 | TEST-ONLY: 1
   ```

7. **Guardrail** — never auto-remove. Output the table and stop. Removal is handled by `dead-cleanup` skill only after explicit user confirmation.
