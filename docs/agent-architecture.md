# Agent Architecture

## Goal
Build a multi-agent setup where specialized subagents solve narrow tasks with isolated context and compact outputs.

## Design Principles

### 1. Narrow roles
Each agent does one job well:
- planner
- codebase-reader
- implementer
- reviewer

### 2. Context isolation
Each subagent receives only:
- goal
- constraints
- known facts relevant to that agent
- minimal artifacts needed

### 3. Shared output contract
All subagents return the same compact schema to reduce merge complexity.

### 4. Token efficiency
Outputs should be:
- short
- dense
- concrete
- evidence-based

### 5. Main orchestrator owns synthesis
Subagents do not own the final user response.
The main agent merges results and decides next actions.

## Standard Task Packet

GOAL:
- one-line objective

CONSTRAINTS:
- hard requirements only

KNOWN FACTS:
- facts relevant to the target subagent

INPUT ARTIFACTS:
- file paths, diffs, snippets, prior outputs

TASK:
- exact requested analysis or action

RETURN CONTRACT:
- shared compact contract only

## Suggested Delegation Patterns

### Sequential
planner -> codebase-reader -> implementer -> reviewer

Use when each step depends on the previous one.

### Parallel
main -> {codebase-reader, reviewer}

Use when tasks are independent.

## Anti-Patterns

- sending full conversation history to every agent
- vague agent roles with heavy overlap
- prose-heavy agent outputs
- asking reviewer to implement
- asking implementer to plan architecture from scratch
- using JSON when a line contract is sufficient