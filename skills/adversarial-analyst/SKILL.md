# Adversarial Analysis Skill

## Purpose
Stress-test a plan, design, or implementation by identifying how it could fail, be misused, or break under real-world conditions.

## Use When
- a plan looks plausible but may hide assumptions
- an implementation needs hardening
- a rollout, migration, auth flow, distributed workflow, or stateful system is involved
- the cost of missing edge cases is high
- the team wants pre-mortem style analysis

## Inputs
- goal
- constraints
- relevant files, plan, diff, or design notes
- optional outputs from planner, implementer, or reviewer

## Procedure
1. Identify the intended success path
2. List the assumptions required for that success path
3. Attack each assumption:
   - bad input
   - missing dependency
   - timing issue
   - scale issue
   - stale state
   - duplicate event
   - partial failure
   - unauthorized actor
4. Identify what fails loudly vs silently
5. Identify what is hardest to detect or recover from
6. Prioritize by severity and likelihood
7. Return only the highest-signal findings

## Output
Use the shared compact contract.

Additional adversarial-analysis guidance:
- FINDINGS = assumptions, break points, failure modes
- ACTION = hardening steps, validations, tests, rollout protections
- RISKS = highest-severity issues only
- NEEDS = missing evidence required to confirm a suspected failure mode

## Required Thinking Lenses
Apply only the relevant lenses:
- hostile input
- malformed data
- race condition
- duplicate delivery
- replay
- out-of-order events
- timeout / retry storm
- partial write
- stale cache
- version skew
- auth bypass
- tenant boundary leak
- rollout mismatch
- missing observability
- no rollback path
- human/operator error

## Rules
- prioritize concrete failure over abstract concern
- do not produce more than 5 findings unless the situation is genuinely risky
- clearly distinguish:
  - observed issue
  - plausible risk
  - open question
- avoid style-only feedback
- keep output under 180 tokens unless STATUS is BLOCKED

## Good Example

[AGENT] adversarial-analyst
[STATUS] OK
[SUMMARY] Plan is viable but fragile under duplicate delivery and partial failure

[FINDINGS]
- Observed: no idempotency guard for repeated event consumption
- Plausible: retry after downstream timeout may create duplicate side effects
- Plausible: cache miss during fan-out may drop correlation context silently
- Open: identified open problems ( could be from a prior work )

[ACTION]
1. Add idempotency key handling for event processing
2. Define timeout/retry side-effect policy
3. Add test for duplicate and out-of-order delivery

[RISKS]
- Silent duplicate notifications
- Hard-to-debug correlation loss under partial failure

[NEEDS]
- Confirm whether downstream writes are idempotent

## Bad Example
- "This could have edge cases"
- "Make sure security is good"
- long prose without concrete breakage
- repeating reviewer comments without adversarial pressure