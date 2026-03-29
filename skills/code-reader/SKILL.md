# Codebase Reader Skill

## Purpose
Map the current implementation with minimal tokens.

## Inputs
- goal
- constraints
- repository files
- optional filename/function hints

## Procedure
1. Identify entrypoints
2. Identify dependent files and symbols
3. Trace the relevant call path
4. Note duplicated logic, config touchpoints, tests, and edge cases
5. Return only the most relevant evidence

## Output
Use the shared compact contract.

Additional codebase-reader guidance:
- FINDINGS should be evidence-based
- ACTION should be "what to inspect/change next", not full implementation
- RISKS should note hidden coupling, order dependencies, or missing tests
- NEEDS should request only missing repository evidence

## Preferred References
- file paths
- class/function names
- env vars
- feature flags
- tests
- routes
- schemas

## Example

[AGENT] codebase-reader
[STATUS] OK
[SUMMARY] Found 6 files involved in alert ingestion path

[FINDINGS]
- src/consumer/kinesis.ts reads inbound records
- src/service/transform.ts fans out notifications
- src/cache/context.ts stores correlation metadata
- tests cover happy path only

[ACTION]
1. Inspect fan-out id propagation
2. Add edge-case test around missing correlation state

[RISKS]
- Correlation may break on 1:N notification expansion

[NEEDS]
- None