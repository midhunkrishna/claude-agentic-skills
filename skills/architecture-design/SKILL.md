# Architecture Design Skill

## Purpose
Research design options broadly and produce a well-documented, well-reasoned technical design with explicit tradeoffs, assumptions, and recommendations.

## Use When
- designing a new system, feature, or subsystem
- comparing architecture or technology options
- turning requirements into a technical design
- evaluating storage, messaging, API, workflow, or deployment approaches
- documenting a recommended design for implementation
- reviewing whether an architecture proposal is sufficiently reasoned

## Inputs
- goal
- constraints
- requirements or product context
- architecture notes, APIs, implementation plan, or codebase context
- optional outputs from planner, solution-fit-analyst, reviewer, or codebase-reader

## Procedure
1. Identify the real problem
2. Extract explicit and implicit constraints:
   - scale
   - latency
   - reliability
   - consistency
   - cost
   - team familiarity
   - operational complexity
   - rollout/migration constraints
3. Define evaluation criteria
4. Identify 2-4 credible solution options
5. Compare options across:
   - fitness to requirements
   - complexity
   - operability
   - implementation cost
   - future flexibility
6. Recommend one option
7. Explain why it wins and why others lose
8. Document major risks, assumptions, and open questions
9. Return only the highest-signal findings

## Output
Use the shared compact contract.

Additional architecture-design guidance:
- FINDINGS = constraints, options, tradeoffs, design reasoning
- ACTION = recommended architecture/design next steps
- RISKS = design, migration, operability, or future-evolution risks
- NEEDS = missing constraints needed for stronger recommendation

## Required Thinking Lenses
Apply only relevant lenses:
- requirement fit
- simplicity vs extensibility
- consistency vs availability
- sync vs async
- event-driven vs request-response
- state ownership
- storage/access pattern fit
- scaling bottlenecks
- operability
- migration/adoption cost
- vendor/tooling maturity
- build vs buy
- rollback and failure containment
- team cognitive load

## Design Principles
- solve the real problem, not the imagined future one
- explore multiple credible options before choosing
- prefer explicit tradeoffs over hidden assumptions
- prefer practical, operable designs over elegant but fragile ones
- do not over-engineer “future flexibility” unless justified
- recommend the simplest design that satisfies the constraints

## Rules
- do not recommend more than 4 serious options
- do not list more than 5 findings unless the design space is genuinely complex
- clearly distinguish:
  - observed constraint
  - option
  - recommendation
  - open tradeoff
- avoid vague advice like "use a scalable architecture"
- name concrete design patterns, tools, or structures when relevant
- keep output under 180 tokens unless STATUS is BLOCKED

## Good Example

[AGENT] architect
[STATUS] OK
[SUMMARY] Async event-driven design is justified, but current plan overuses custom coordination components

[FINDINGS]
- Observed: core requirement is reliability under intermittent downstream failures, not ultra-low latency
- Option: direct synchronous fan-out is simpler but weak under dependency instability
- Option: queue-backed async processing fits retry isolation and operational visibility better
- Recommendation: keep async queue-based design, but replace custom retry coordinator with managed DLQ/redrive pattern

[ACTION]
1. Adopt queue + worker + DLQ as primary flow
2. Keep correlation IDs and idempotency at message boundaries
3. Avoid introducing a separate custom orchestration store unless required by business recovery needs

[RISKS]
- Custom coordination layer adds state and failure modes without clear requirement support
- Missing idempotency rules may still undermine async reliability

[NEEDS]
- Confirm target throughput and acceptable end-to-end delay

## Bad Example
- "Use microservices"
- "Design for scale"
- "Kafka may be better"
- abstract recommendations without requirement-fit reasoning