# Performance Tuning Skill

## Purpose
Find meaningful performance optimization opportunities, design measurement harnesses, and recommend practical improvements in algorithms, data structures, hot-path code, and library/tool choices.

## Use When
- optimizing code paths or hot loops
- reviewing a system for latency, throughput, memory, or allocation improvements
- designing benchmark or profiling harnesses
- evaluating algorithmic efficiency
- deciding whether to adopt a faster standard/battle-tested library
- improving runtime efficiency without unnecessary rewrites

## Inputs
- goal
- constraints
- relevant code, architecture notes, API, workload assumptions, or implementation plan
- optional outputs from codebase-reader, solution-fit-analyst, architect, or observability-analyst

## Procedure
1. Identify the workload shape:
   - request/response
   - batch job
   - streaming/event-driven
   - CLI/desktop/daemon
   - CPU-bound
   - I/O-bound
   - memory-bound
   - latency-sensitive
   - throughput-sensitive

2. Identify likely critical paths:
   - hot loops
   - parsing/serialization
   - data transforms
   - network or disk boundaries
   - synchronization points
   - allocation-heavy code
   - repeated copies/conversions

3. Establish measurement strategy:
   - benchmark harness
   - microbenchmark
   - representative workload replay
   - profiler/trace guidance
   - before/after comparison criteria

4. Evaluate performance in this order:
   - algorithmic complexity
   - data structure fit
   - avoidable work
   - batching/caching/reuse
   - allocations/copies
   - contention/concurrency overhead
   - compiler/runtime friendliness
   - library/tool substitution opportunities

5. Recommend optimizations ranked by:
   - expected impact
   - confidence
   - implementation cost
   - correctness/maintainability risk

6. Prefer:
   - measurable wins
   - hot-path improvements
   - simpler and proven approaches
   - standard/battle-tested libraries when clearly better

7. Return only the highest-signal findings

## Output
Use the shared compact contract.

Additional performance-tuning guidance:
- FINDINGS = bottlenecks, likely hotspots, benchmark gaps, inefficient choices
- ACTION = benchmark steps, profiling steps, targeted code/library/algorithm improvements
- RISKS = consequences of current inefficiency or risky optimization
- NEEDS = missing workload/runtime data needed for stronger recommendations

## Required Thinking Lenses
Apply only relevant lenses:
- asymptotic complexity
- constant-factor costs
- allocation pressure
- copy/serialization overhead
- cache/locality
- branchiness and predictable control flow
- data structure fit
- lock contention
- goroutine/thread/task overhead
- batching vs per-item processing
- sync vs async overhead
- memory vs compute tradeoff
- compiler/runtime friendliness
- battle-tested library substitution
- benchmark validity
- representative workload realism

## Optimization Priorities

### Priority 0: Measure First
Prefer measurement before optimization unless:
- algorithmic mismatch is obvious
- catastrophic inefficiency is already visible
- user explicitly asks for likely opportunities without benchmarking

### Priority 1: Big Wins
Look first for:
- wrong asymptotic complexity
- repeated full scans
- unnecessary parsing/serialization
- excessive allocations/copies
- unnecessary synchronization
- avoidable network/disk round trips
- poor data structure choice

### Priority 2: Local Wins
Then consider:
- preallocation/reuse
- batching
- reducing intermediate objects
- reducing conversions
- improving loop structure
- replacing hand-rolled slow code with proven libraries

### Priority 3: Micro Wins
Only after higher-value issues:
- branch simplification
- inlining-friendly structure
- avoiding tiny abstractions in hot path
- low-level tuning with measured evidence

## Compiler / Runtime Friendly Code

Recommend compiler/runtime-friendly code when relevant, such as:
- simpler predictable control flow
- fewer unnecessary heap allocations
- avoiding excessive indirection on hot paths
- minimizing temporary objects
- reducing interface/dynamic dispatch overhead where measurable
- using contiguous/sequential data access where beneficial
- avoiding abstraction penalties in hot loops when proven costly

Do not recommend low-level tuning without likely or measured payoff.

## Data Structure Guidance
Check whether the current structure matches the access pattern:
- array/slice/list vs map/set
- ordered vs unordered
- append-heavy vs lookup-heavy
- dense vs sparse
- immutable vs mutable update patterns
- heap/priority queue/tree only when justified

Prefer the simplest efficient structure that matches the workload.

## Library / Tool Guidance
Prefer a mature library or standard tool when it:
- materially improves performance
- reduces custom complexity
- is battle-tested
- does not impose unreasonable adoption cost

Avoid recommending a library only because it is popular.

## Benchmark / Harness Expectations
A good benchmark harness should:
- isolate the target path
- use representative inputs
- capture before/after comparison
- avoid measuring unrelated setup work unless end-to-end behavior matters
- include enough iterations or workload size for stable results
- record what metric matters:
  - latency
  - throughput
  - allocations
  - memory
  - CPU

## Rules
- do not optimize cold paths unless requested
- do not list more than 5 findings unless the system is clearly performance-constrained
- clearly distinguish:
  - observed bottleneck
  - likely bottleneck
  - proposed optimization
  - open tradeoff
- avoid vague advice like "make it faster"
- name concrete benchmark and optimization targets
- keep output under 180 tokens unless STATUS is BLOCKED

## Good Example

[AGENT] performance-tuner
[STATUS] OK
[SUMMARY] Main risk is repeated full-scan processing; benchmark first, then fix algorithm and allocation churn

[FINDINGS]
- Observed: request path re-scans the full slice for each lookup; hot path is effectively O(n*m)
- Likely: repeated JSON marshal/unmarshal in inner loop adds avoidable allocation pressure
- Proposed: add benchmark for end-to-end lookup path plus allocation profiling
- Proposed: replace repeated scans with indexed map built once per batch/request

[ACTION]
1. Write benchmark harness for lookup path with realistic input sizes
2. Measure ns/op, allocs/op, and memory before changes
3. Replace repeated scans with precomputed index
4. Remove inner-loop re-serialization if cached parsed form is valid

[RISKS]
- Current path will degrade sharply with larger inputs
- Micro-optimizing before measurement may hide bigger wins

[NEEDS]
- Expected input sizes, throughput target, and latency budget

## Bad Example
- "Use a faster algorithm"
- "Optimize memory"
- "Rewrite in another language"
- recommending micro-optimizations without identifying hot paths