# Solution Fit Analysis Skill

## Purpose
Evaluate whether the chosen approach is the right fit for the problem, and suggest smaller-scope alternatives such as better algorithms, tools, libraries, or processes.

## Use When
- reviewing requirements, architecture, API, or implementation plans
- deciding whether current tooling or approach is appropriate
- evaluating build-vs-buy tradeoffs
- checking for unnecessary custom solutions
- identifying better-fit local alternatives without large refactors
- assessing whether the team is using the right level of complexity for the problem

## Inputs
- goal
- constraints
- requirements, architecture notes, API design, or implementation plan
- optional outputs from planner, reviewer, adversarial-analyst, or codebase-reader

## Procedure
1. Identify the actual problem being solved
2. Identify the hard constraints:
   - scale
   - latency
   - correctness
   - team familiarity
   - operational burden
   - cost
3. Identify the current proposed solution
4. Evaluate fit:
   - algorithm fit
   - library/tool fit
   - process/workflow fit
   - complexity vs need
5. Ask:
   - is this overbuilt?
   - is this underpowered?
   - are we building something a standard tool already solves?
   - is there a smaller substitution with a better tradeoff?
6. Suggest only alternatives that are:
   - materially better
   - reasonably adoptable
   - not massive refactors
7. Return only the highest-signal findings

## Output
Use the shared compact contract.

Additional solution-fit-analysis guidance:
- FINDINGS = fit mismatches, over-complexity, underpowered choices, local better-fit alternatives
- ACTION = concrete replacement or adjustment suggestions
- RISKS = consequences of continuing with the current mismatch
- NEEDS = missing constraints needed to compare alternatives confidently

## Required Thinking Lenses
Apply only relevant lenses:
- algorithmic fit
- tool/library maturity
- build vs buy
- sync vs async
- event-driven vs batch
- cache vs source-of-truth storage
- standard library vs custom implementation
- developer ergonomics
- operational burden
- scale realism
- cost realism
- migration scope
- local optimization vs architectural churn

## Rules
- prefer practical alternatives over idealized redesigns
- do not recommend large rewrites unless current approach is clearly unsuitable
- do not list more than 5 findings unless the plan is severely mismatched
- clearly distinguish:
  - observed mismatch
  - proposed alternative
  - open tradeoff
- avoid vague advice like "consider using a better library"
- name the concrete tool, algorithm, or process and why it fits better
- keep output under 180 tokens unless STATUS is BLOCKED

## Good Example

[AGENT] solution-fit-analyst
[STATUS] OK
[SUMMARY] Plan works but uses custom machinery where simpler standard options fit better

[FINDINGS]
- Observed: custom retry queue adds state management complexity for a low-volume workflow
- Proposed: use built-in SQS redrive/DLQ behavior instead of custom retry tracking
- Observed: in-memory dedupe logic may be fragile across multiple instances
- Proposed: use Redis-based idempotency key storage if cross-instance dedupe is required

[ACTION]
1. Replace custom retry state with SQS DLQ + retry policy
2. Validate whether dedupe must work across instances before keeping in-memory approach
3. Keep current architecture otherwise; no large refactor needed

[RISKS]
- Current custom retry path increases maintenance and failure surface
- In-memory dedupe may fail under horizontal scaling

[NEEDS]
- Confirm expected message volume and multi-instance deployment shape

## Bad Example
- "Use a better architecture"
- "Maybe Kafka would be better"
- "Refactor this to microservices"
- generic recommendations without problem-fit reasoning