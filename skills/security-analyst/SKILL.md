# Security Analysis Skill

## Purpose
Identify security vulnerabilities, misconfigurations, and exploit paths across application code, infrastructure, and dependencies.

## Use When
- reviewing auth, APIs, or data handling
- working with AWS/GCP/Azure infrastructure
- introducing new endpoints, flows, or services
- handling user input, tokens, or sensitive data
- validating production readiness
- evaluating dependency/security posture

## Inputs
- goal
- constraints
- relevant files, configs, infra definitions (Terraform, IAM, etc.)
- optional outputs from planner, implementer, reviewer, or adversarial-analyst

## Procedure
1. Identify trust boundaries (user → API → service → DB → external systems)
2. Identify entry points (APIs, events, inputs)
3. Analyze:
   - authentication
   - authorization
   - input validation
   - data flow
4. Check infra:
   - IAM roles/policies
   - network exposure
   - storage permissions
5. Check dependencies:
   - outdated libraries
   - known CVE classes
6. Identify realistic attack paths
7. Prioritize by severity and exploitability
8. Return only highest-signal issues

## Output
Use the shared compact contract.

Additional security-analysis guidance:
- FINDINGS = vulnerabilities, misconfigs, attack paths
- ACTION = mitigations, fixes, validation steps
- RISKS = highest-severity exploitable issues
- NEEDS = missing evidence required to confirm vulnerability

## Required Thinking Lenses
Apply only relevant ones:
- external attacker (unauthenticated)
- authenticated low-privilege user
- compromised internal service
- malicious tenant/user
- replay / token theft
- injection (SQL, command, template)
- broken auth / IDOR
- secrets leakage (logs, env, config)
- IAM over-permission
- public exposure (S3, endpoints)
- dependency exploit (known CVE class)
- downgrade / version mismatch
- missing rate limiting / abuse

## Rules
- prioritize exploitable issues over theoretical ones
- do not list more than 5 findings unless system is critically exposed
- clearly distinguish:
  - observed vulnerability
  - plausible risk
  - open question
- avoid generic advice like "improve security"
- reference concrete:
  - endpoints
  - files
  - IAM policies
  - configs
- keep output under 180 tokens unless STATUS is BLOCKED

## Good Example

[AGENT] security-analyst
[STATUS] OK
[SUMMARY] Auth flow vulnerable to token replay and over-permissive IAM role

[FINDINGS]
- Observed: JWT not bound to device/session → replay possible if stolen
- Observed: IAM role allows s3:* on bucket instead of scoped actions
- Plausible: missing rate limiting on login endpoint enables brute force

[ACTION]
1. Bind JWT to device/session or add short TTL + rotation
2. Restrict IAM policy to least privilege (GetObject/PutObject)
3. Add rate limiting and lockout for auth endpoints

[RISKS]
- Token theft enables account takeover
- Compromised service can exfiltrate full S3 bucket

[NEEDS]
- Confirm whether tokens are stored in secure storage on client

## Bad Example
- "Ensure proper authentication"
- "Check for vulnerabilities"
- listing OWASP categories without mapping to system
- long generic explanations