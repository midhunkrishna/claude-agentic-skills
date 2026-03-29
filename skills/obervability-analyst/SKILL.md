# Observability Analysis Skill

## Purpose
Evaluate a system's observability posture and recommend practical improvements in logging, metrics, tracing, health signals, and telemetry design.

## Use When
- reviewing a codebase, architecture, API, or implementation plan for observability
- identifying logging and instrumentation gaps
- improving production debugging and operational visibility
- assessing whether a service, daemon, desktop app, or async workflow is observable enough
- balancing over-logging vs under-logging
- designing telemetry aligned with industry-standard practices

## Inputs
- goal
- constraints
- relevant code, architecture notes, APIs, implementation plan, or runtime flow
- optional outputs from codebase-reader, reviewer, adversarial-analyst, or solution-fit-analyst

## Procedure
1. Identify the system shape:
   - cloud service
   - API/backend
   - event-driven/distributed workflow
   - batch job
   - daemon/background process
   - desktop/client application
2. Identify critical flows:
   - request path
   - job/task lifecycle
   - retry path
   - external dependency boundaries
   - startup/shutdown/reconnect loops
3. Evaluate current observability across:
   - logs
   - metrics
   - traces
   - health probes/signals
   - business/domain events
4. Ask:
   - can failures be detected?
   - can failures be diagnosed?
   - can latency/reliability regressions be measured?
   - can events/requests/jobs be correlated?
   - is signal quality high enough without excessive noise?
5. Identify blind spots and tradeoffs:
   - under-instrumented critical paths
   - over-logged noisy paths
   - high-cardinality metric misuse
   - missing structured fields
   - missing lifecycle instrumentation
6. Recommend practical, industry-standard improvements
7. Return only the highest-signal findings

## Output
Use the shared compact contract.

Additional observability-analysis guidance:
- FINDINGS = missing signals, poor signal design, over/under instrumentation, blind spots
- ACTION = specific instrumentation/logging/metrics/tracing improvements
- RISKS = operational consequences of poor observability
- NEEDS = missing runtime/system information needed for stronger recommendations

## Required Thinking Lenses
Apply only relevant lenses:
- request lifecycle
- job/task lifecycle
- event/message lifecycle
- dependency boundary visibility
- startup/shutdown/restart visibility
- retry/backoff visibility
- structured logging quality
- metrics usefulness
- trace propagation/correlation
- alertability
- debuggability
- cardinality control
- telemetry cost/noise
- privacy/sensitive-data logging risk
- desktop diagnostics
- daemon heartbeat and degraded-state visibility

## Industry-Standard Expectations

### Logs
Use logs for:
- discrete events
- errors/failures
- state transitions
- contextual debugging
- audit-like operational breadcrumbs where appropriate

Good logging qualities:
- structured, machine-readable
- stable field names
- correlation IDs / request IDs / job IDs when relevant
- meaningful severity levels
- enough context to diagnose, not everything

Avoid:
- logging every internal step by default
- duplicate logs at multiple layers without added value
- high-volume noisy info logs on hot paths
- sensitive secrets/PII unless explicitly safe and required

### Metrics
Use metrics for:
- rates
- errors
- durations/latency
- queue depth/backlog
- retry counts
- resource/saturation indicators
- health trend analysis

Good metric qualities:
- low-cardinality labels
- tied to SLO/SLA style questions where possible
- useful for dashboards and alerts

Avoid:
- unbounded-cardinality labels
- metrics that mirror logs without aggregation value

### Traces
Use traces for:
- multi-step request flows
- cross-service communication
- async job/event propagation when trace linkage is possible
- latency attribution and dependency diagnosis

Avoid:
- forcing distributed tracing where system complexity does not justify it
- adding trace spans without meaningful boundaries

### Health Signals
Use health/liveness/readiness/heartbeat style signals for:
- services
- daemons
- background workers
- reconnecting agents
- long-running local processes

### Desktop / Daemon Guidance
For local/non-cloud apps, prioritize:
- startup/shutdown state
- config load failures
- dependency/connectivity failures
- retry/reconnect attempts
- crash diagnostics
- local queue/task backlog visibility
- degraded-mode visibility
- user-action or system-trigger correlation where helpful

## Tradeoff Rules

### Under-logging
Risks:
- failures cannot be diagnosed
- incidents take too long to resolve
- behavior appears random
- no useful audit trail of critical state transitions

### Over-logging
Risks:
- noise hides signal
- cost/storage blow-up
- performance overhead
- duplicate reporting
- operators ignore logs because too much is emitted

### Balanced Approach
Aim for:
- rich logs at important boundaries
- sparse logs in hot loops
- metrics for trends and alerting
- traces for flow attribution
- domain events only when they help operations or product understanding

## Rules
- recommend the minimum high-signal instrumentation needed for safe operation
- do not recommend "log everything"
- distinguish logs vs metrics vs traces clearly
- do not list more than 5 findings unless the system is severely under-observed
- clearly distinguish:
  - observed blind spot
  - proposed instrumentation improvement
  - open tradeoff
- name concrete telemetry additions where possible
- keep output under 180 tokens unless STATUS is BLOCKED

## Good Example

[AGENT] observability-analyst
[STATUS] OK
[SUMMARY] Core flow is functional but incident diagnosis would be weak under retries and dependency failures

[FINDINGS]
- Observed: request logs lack stable correlation/request IDs across service boundaries
- Observed: retry path emits errors but no metric for retry rate or final exhaustion
- Proposed: add queue/job lifecycle metrics and structured state-transition logs
- Proposed: add dependency latency/error metrics around external API calls

[ACTION]
1. Add correlation IDs to structured logs and propagate across async boundaries
2. Add counters for retries, exhaustion, and failed jobs
3. Add duration/error metrics for external dependencies
4. Keep debug-level per-attempt details off by default on hot paths

[RISKS]
- Incidents will be detectable late and hard to diagnose
- Retry storms may be invisible until downstream systems degrade

[NEEDS]
- Confirm current metrics/tracing stack (e.g. OpenTelemetry, Prometheus, vendor APM)

## Bad Example
- "Add more logs"
- "Use OpenTelemetry everywhere"
- "Track everything with metrics"
- generic advice without signal design or tradeoff reasoning