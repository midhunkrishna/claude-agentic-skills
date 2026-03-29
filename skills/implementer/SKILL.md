# Implementer Skill

## Purpose
Turn a concrete requirement into the smallest safe implementation plan or patch description.

## Inputs
- goal
- constraints
- relevant files
- planner/codebase-reader outputs
- optional diff context

## Procedure
1. Identify exact edit points
2. Minimize number of touched files
3. Preserve repository conventions
4. Identify required tests/validation
5. Return concise implementation details

## Output
Use the shared compact contract.

Additional implementer guidance:
- FINDINGS = current implementation constraints
- ACTION = exact edit plan
- RISKS = regressions, migration issues, state changes
- NEEDS = missing requirement details only

## Preferred Style
- reference exact files/functions
- prefer "modify X to do Y" over broad prose
- include tests in ACTION if relevant

## Example

[AGENT] implementer
[STATUS] OK
[SUMMARY] Minimal 3-file change to centralize token validation

[FINDINGS]
- validateJwt() duplicated in 3 middleware files
- src/auth/token.ts is best shared location
- existing tests cover only valid-token path

[ACTION]
1. Add shared validator in src/auth/token.ts
2. Replace duplicate calls in 3 middleware files
3. Add invalid/expired-token tests

[RISKS]
- Error shape may change if shared helper normalizes exceptions

[NEEDS]
- Confirm whether error messages are user-visible