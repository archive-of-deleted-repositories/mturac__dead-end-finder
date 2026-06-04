# dead-cleanup

Safely remove a confirmed dead export or orphaned route — with explicit confirmation required.

## Trigger

Use when: user runs `/dead-cleanup [symbol]` after reviewing dead-exports or dead-routes output.

## Steps

1. **Show the dead symbol or route** — confirm what is about to be removed:
   ```
   Removing: formatCurrency (named export)
   Defined in: src/utils/legacyFormat.ts:12
   Classification: UNUSED
   ```

2. **Show all files that would change:**
   - The source file (export declaration removed)
   - Any barrel files (`index.ts`, `lib/index.ts`) that re-export it
   - Import statements in any file (even INTERNAL-ONLY or TEST-ONLY usages)

3. **Generate removal plan — show before asking:**
   ```
   REMOVAL PLAN: formatCurrency
   ─────────────────────────────────────────
   1. src/utils/legacyFormat.ts:12
      DELETE: export function formatCurrency(value: number): string {

   2. src/index.ts:8
      DELETE: export { formatCurrency } from './utils/legacyFormat';
   ```

4. **Require explicit YES confirmation** — output:
   ```
   Ready to apply. Type YES to confirm removal of formatCurrency, or NO to cancel.
   ```
   Wait for user response. Do NOT proceed without YES.

5. **Apply removal only after YES:**
   - Remove the export declaration line(s) from the source file
   - Remove the barrel re-export line if present
   - Remove any import statements in other files that imported this symbol

6. **Post-removal: check for syntax errors** — scan the modified files for:
   - Trailing commas before closing braces in import lists: `import { , Bar }` 
   - Empty export blocks: `export { }` with nothing left
   - Dangling semicolons after removed lines
   - Report any found — do not auto-fix, show exact location.

7. **Completion report:**
   ```
   CLEANUP COMPLETE: formatCurrency
   ─────────────────────────────────
   Files modified: 2
   Lines removed: 3
   
   ✓ src/utils/legacyFormat.ts — export removed
   ✓ src/index.ts — barrel re-export removed
   
   Syntax check: no issues detected.
   ```

## Safety contract

- Never remove without showing the plan first
- Never proceed without YES
- Never touch files outside the removal scope
- Always run syntax check after removal
