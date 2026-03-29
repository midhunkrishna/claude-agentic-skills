# Code Explainer

## Purpose
Read code deeply, understand what it actually does, and explain it to a human in a way that is clear, structured, and grounded in evidence from the code.

This skill is optimized for:
- reverse-engineering unfamiliar code
- explaining control flow and data flow
- clarifying abstractions, hidden coupling, and side effects
- turning dense code into a human-friendly narrative
- using ASCII diagrams to make execution and architecture intuitive

This skill should outperform generic code explanation by being:
- more evidence-based
- more structured
- more explicit about uncertainty
- better at tracing execution paths
- better at showing relationships visually

## Use When
Use this skill when the user asks to:
- explain what some code does
- understand a file, function, class, or module
- trace a request, event, or execution path
- explain a system in simple terms
- summarize code for onboarding, review, or documentation
- convert code into a human-friendly walkthrough
- produce an ASCII diagram of flow, architecture, or interactions

## Do Not Use When
Do not use this skill when the user primarily wants:
- code changes
- bug fixes
- refactoring
- tests
- performance tuning
- security review
- adversarial analysis

Those should route to more specialized agents/skills.

## Inputs
Expected inputs may include:
- one or more files
- code snippets
- function/class/module names
- diffs
- repository context
- a user question about behavior

## Core Operating Principles

### 1. Read before explaining
Do not jump into explanation from names alone.
Infer behavior from:
- control flow
- call sites
- data transformations
- side effects
- error handling
- configuration
- dependencies

### 2. Explain what is present, not what is imagined
Do not speculate beyond the code.
When inference is required, label it clearly:
- Observed
- Likely
- Unclear

### 3. Optimize for human understanding
Prefer:
- plain English
- short sections
- concrete terminology
- examples of “what happens when”
- execution order
- why the code exists
- ASCII diagrams for flow and structure

### 4. Show the mental model
A good explanation should answer:
- What is this?
- Why does it exist?
- How does it work?
- What are the important moving parts?
- What happens at runtime?
- What should a human pay attention to?

### 5. Respect levels of abstraction
Start broad, then zoom in:
1. big picture
2. component roles
3. execution flow
4. notable details / edge cases

## Required Output Style
Output must be human-friendly and structured.

Prefer this structure unless the user asks otherwise:

### 1. One-line summary
A concise plain-English explanation.

### 2. What this is
Explain the code artifact in simple terms.

### 3. Why it exists
State the likely purpose in the system.

### 4. How it works
Explain the actual runtime flow in sequence.

### 5. ASCII diagram
Include at least one ASCII diagram when useful.

### 6. Important details
Call out:
- side effects
- hidden assumptions
- dependencies
- surprising behavior
- non-obvious logic

### 7. If relevant: examples
Show “if X happens, then Y happens”.

## ASCII Diagram Requirements
Use ASCII art whenever it improves understanding.

Good use cases:
- request flow
- event flow
- class/module relationships
- pipeline stages
- branching logic
- cache lookup/write paths
- retry/error paths

Preferred diagram styles:

### Linear flow
[Input] -> [Transform] -> [Decision] -> [Output]

### Branching
[Start]
   |
   v
[Check condition]
   | yes                 | no
   v                     v
[Path A]              [Path B]

### Layered architecture
[Caller / API / UI]
        |
        v
[Service / Orchestrator]
        |
        v
[Repository / External System]

### Fan-out
          -> [Handler A]
[Event] -> -> [Handler B]
          -> [Handler C]

### Cache pattern
[Request]
   |
   v
[L1 Cache?] --hit--> [Return]
   |
  miss
   v
[L2/DB/Remote] -> [Populate Cache] -> [Return]

Rules:
- Keep diagrams compact
- Use stable labels from the code where useful
- Do not create giant diagrams
- Prefer one good diagram over many weak ones

## Explanation Heuristics

### For functions
Explain:
- inputs
- outputs
- major steps
- side effects
- failure modes
- what the caller gets

### For classes
Explain:
- responsibility
- key fields/dependencies
- lifecycle
- key methods
- collaboration with other classes

### For modules/files
Explain:
- purpose
- key exports
- role in the system
- dependencies
- how other parts use it

### For async/event systems
Explain:
- source of event/request
- transformation steps
- fan-out/fan-in
- retry behavior
- ordering assumptions
- state carried across stages

### For infra/config code
Explain:
- what resource is being created/configured
- what depends on what
- trust boundaries
- operational effect
- why each important block exists

## Human-Friendly Language Rules
Prefer:
- “This function takes...”
- “The code first checks...”
- “If that succeeds...”
- “This class acts like...”
- “In practice, this means...”

Avoid:
- dense jargon without explanation
- line-by-line narration unless requested
- vague phrases like “handles stuff”
- repeating identifiers without interpretation

When a technical term is necessary, explain it immediately.

Example:
“Idempotent here means repeating the same message should not create duplicate effects.”

## Evidence and Uncertainty Rules
Always distinguish:

### Observed
Directly supported by code.

### Likely
Strong inference from usage and structure.

### Unclear
Cannot be confirmed from the visible code alone.

If something is unclear, say so plainly.

Example:
- Observed: this method writes to DynamoDB and updates an in-memory cache.
- Likely: the cache is intended to reduce repeated reads on hot keys.
- Unclear: whether cache invalidation is handled elsewhere.

## Depth Control
Match explanation depth to the apparent user need.

### Shallow
Use for:
- “what does this do?”
- quick file summaries

### Medium
Use for:
- function/class/module walkthroughs
- code onboarding

### Deep
Use for:
- execution tracing
- distributed flow explanation
- hidden coupling / side-effect analysis

If unsure, choose Medium.

## Anti-Patterns to Avoid
Do not:
- merely paraphrase variable names
- explain only syntax
- skip runtime behavior
- ignore side effects
- hide uncertainty
- produce giant walls of text
- create ASCII art that is decorative but unhelpful
- claim understanding of files/functions not actually inspected

## Strong Default Response Template

Use this by default:

Summary:
<1-2 sentence explanation>

What this is:
- ...

Why it exists:
- ...

How it works:
1. ...
2. ...
3. ...

ASCII view:
```text
...