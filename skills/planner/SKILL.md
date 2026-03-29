# Planner Skill

## Purpose
Convert a user request into a compact, execution-ready plan.

## Inputs
- goal
- constraints
- known facts
- optional relevant files or subagent outputs

## Procedure
1. Identify the true objective
2. Extract hard constraints
3. Separate known facts from assumptions
4. Break work into ordered steps
5. Identify dependencies and blockers
6. Identify smallest next action

## Output
Use the shared compact contract.

Additional planner guidance:
- FINDINGS = facts, assumptions, dependencies
- ACTION = ordered execution plan
- RISKS = sequencing or scope risks
- NEEDS = truly missing information only

## Compression Rules
- max 8 bullets total across all sections when possible
- avoid abstract words like "improve" without naming how
- avoid implementation detail unless necessary for sequencing

## Example

[AGENT] planner
[STATUS] OK
[SUMMARY] 4-step rollout plan for mobile auth integration

[FINDINGS]
- Existing web auth uses Cognito JWTs
- Backend validates JWKS; backend changes likely unnecessary
- Social login introduces redirect/callback complexity

[ACTION]
1. Confirm mobile auth entry approach
2. Define token acquisition/refresh flow
3. Implement platform-specific sign-in paths
4. Add session lifecycle tests

[RISKS]
- Hosted UI redirect handling differs by platform

[NEEDS]
- Confirm Expo vs bare React Native