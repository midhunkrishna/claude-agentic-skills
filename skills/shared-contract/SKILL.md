# Shared Compact Agent Contract

## Purpose
Provide a common response schema for all subagents.

## Use When
- any subagent returns results to the orchestrator
- token efficiency matters
- outputs must be dense and easy to merge

## Required Output Format

[AGENT] <name>
[STATUS] OK | BLOCKED | NEEDS_INPUT
[SUMMARY] <<= 20 words>

[FINDINGS]
- <fact>
- <fact>

[ACTION]
1. <next step>
2. <next step>

[RISKS]
- <risk>

[NEEDS]
- <missing item>

## Rules
- No markdown headers
- No JSON unless explicitly requested
- No introductory or closing sentences
- Use bullets, not paragraphs
- Prefer concrete references:
  - filenames
  - functions
  - symbols
  - endpoints
  - configs
  - tests
- Do not restate the request
- Keep under 180 tokens unless STATUS is BLOCKED

## Good Example

[AGENT] codebase-reader
[STATUS] OK
[SUMMARY] Found auth flow entrypoints and token refresh path

[FINDINGS]
- src/auth/index.ts initializes session handling
- src/middleware/refresh.ts runs before auth guard
- validateJwt() duplicated in 3 files

[ACTION]
1. Extract shared token validator
2. Reorder refresh/auth middleware review

[RISKS]
- Middleware reorder may alter refresh semantics

[NEEDS]
- Confirm backward compatibility requirement

## Bad Example
- long prose
- repeated task summary
- vague findings like "the code looks okay"
- unnecessary explanation without concrete refs