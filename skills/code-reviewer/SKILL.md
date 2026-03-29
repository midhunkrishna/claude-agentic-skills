# Code Review Skill

## Purpose
Review code, diffs, APIs, and implementation plans for correctness, clarity, maintainability, and risk using industry-standard, language-specific best practices.

## Use When
- reviewing PRs, diffs, files, or functions
- validating implementation plans
- checking API correctness and contracts
- identifying defects, edge cases, and missing tests
- ensuring code is maintainable and understandable

## Inputs
- goal
- constraints
- code, diff, or plan
- optional outputs from other agents

## Procedure
1. Identify artifact:
   - code / diff / API / plan

2. Identify language/runtime

3. Review in order:
   - correctness
   - contracts / invariants
   - error handling
   - concurrency / async
   - data flow and mutation
   - readability / maintainability
   - tests
   - idiomatic usage

4. Filter:
   - remove style-only issues
   - keep only high-impact findings

---

## Output
Use shared compact contract.

Guidance:
- FINDINGS = defects, risks, gaps
- ACTION = precise fixes
- RISKS = real consequences
- NEEDS = missing context

---

## Global Rules
- max 5 findings unless critical
- prioritize must-fix issues
- avoid vague comments
- prefer concrete, actionable feedback
- keep under 180 tokens

---

# Language-Specific Review Lenses

## Go
- explicit error handling
- simple APIs
- correct nil/zero semantics
- no unnecessary abstraction
- clear control flow

Red flags:
- ignored errors
- confusing interfaces
- hidden side effects
- concurrency without ownership clarity

---

## Bash
- safe quoting
- error handling correctness
- robust command chaining
- ShellCheck-class issues

Red flags:
- unquoted vars
- silent failures
- unsafe pipelines

---

## Kotlin
- null safety
- coroutine correctness
- exception/cancellation handling
- clear data models

Red flags:
- unsafe `!!`
- hidden coroutine context issues
- mutable shared state

---

## Java
- clear APIs
- correct concurrency
- appropriate modern features (records, sealed, etc.)
- clean exception handling

Red flags:
- thread misuse
- over-complicated inheritance
- unclear contracts

---

## TypeScript
- sound types
- correct narrowing
- runtime/type alignment
- async correctness

Red flags:
- `any`
- unsafe casts
- mismatch between type and runtime

---

## JavaScript
- async correctness
- clear control flow
- module boundaries
- proper error handling

Red flags:
- unhandled promises
- confusing async flow
- hidden mutations

---

## What to Catch (Human Review)

- correctness bugs
- edge cases
- unclear contracts
- async/concurrency issues
- missing tests
- confusing logic

---

## What to Ignore (Tooling)

- formatting
- trivial lint issues
- import sorting
- stylistic nits

---

## Example

[AGENT] code-reviewer
[STATUS] OK
[SUMMARY] One correctness issue and one missing validation

[FINDINGS]
- Observed: error from external call ignored; failure path is lost
- Likely: input assumes non-null but not validated

[ACTION]
1. handle and propagate error properly
2. validate input before use

[RISKS]
- silent failure
- runtime crash

[NEEDS]
- None