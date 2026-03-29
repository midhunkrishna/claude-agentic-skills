# Hydra

A multi-agent orchestration framework for Claude Code. Hydra decomposes complex software engineering tasks into specialized sub-tasks handled by narrowly-focused agents, then merges their outputs into a single high-signal result.

## How It Works

Hydra uses a **hub-and-spoke model**:

1. A main **orchestrator** (defined in `CLAUDE.md`) receives user requests
2. It decides on an execution strategy: **direct**, **sequential**, or **parallel**
3. Specialized **subagents** handle narrow, well-defined roles
4. All agents return structured output using a **shared compact contract**
5. The orchestrator merges, deduplicates, and filters findings into a final response

## Agents

Hydra includes 12 specialized agents (defined in `agents/`):

| Agent | Role |
|---|---|
| **planner** | Breaks ambiguous work into execution-ready plans with dependencies and sequencing |
| **codebase-reader** | Finds relevant files, symbols, dependencies, and current behavior |
| **implementer** | Produces concrete code/config changes from a validated plan |
| **code-reviewer** | Reviews code, diffs, APIs, and plans for correctness and maintainability |
| **adversarial-analyst** | Stress-tests plans by finding failure modes, edge cases, and abuse paths |
| **security-analyst** | Identifies vulnerabilities, misconfigurations, and attack paths (CVE-aware) |
| **solution-fit-analyst** | Evaluates whether the chosen approach is the right fit for the problem |
| **observability-analyst** | Evaluates logging, metrics, and tracing quality; identifies blind spots |
| **ux-designer** | Designs user experience flows and interaction patterns |
| **code-explainer** | Explains code in human-friendly terms with runtime flow and ASCII diagrams |
| **architect** | Researches design options and produces technical designs with explicit tradeoffs |
| **performance-tuner** | Finds optimization opportunities and designs measurement harnesses |

## Skills

Each agent has a corresponding skill implementation in `skills/`, plus a shared output contract:

```
skills/
├── adversarial-analyst/SKILL.md
├── architecture-design/SKILL.md
├── code-explainer/SKILL.md
├── code-reader/SKILL.md
├── code-reviewer/SKILL.md
├── implementer/SKILL.md
├── obervability-analyst/SKILL.md
├── performance-tuner/SKILL.md
├── planner/SKILL.md
├── security-analyst/SKILL.md
├── shared-contract/SKILL.md
├── solution-fit-analyst/SKILL.md
└── ux-designer/SKILL.md
```

## Pre-defined Agent Bundles

The orchestrator includes 13 pre-defined bundles for common task patterns:

- **critique-plan** — code-reviewer + adversarial-analyst + solution-fit-analyst + optional security/observability/ux
- **critique-implementation** — code-reviewer + adversarial-analyst + solution-fit-analyst
- **architecture-sanity-check** — architect + solution-fit-analyst + code-reviewer + adversarial-analyst
- **api-design-check** — code-reviewer + solution-fit-analyst + architect
- **performance-review** — performance-tuner + code-reviewer + solution-fit-analyst
- **infra-risk-check** — code-reviewer + adversarial-analyst + security-analyst + solution-fit-analyst
- **design-user-flow** — ux-designer + code-reviewer + adversarial-analyst
- **system-design** — architect + solution-fit-analyst + code-reviewer + observability-analyst
- **operability-check** — observability-analyst + adversarial-analyst + code-reviewer
- **async-system-check** — adversarial-analyst + observability-analyst + code-reviewer
- **hot-path-check** — performance-tuner + code-reviewer + solution-fit-analyst
- **async-system-performance-check** — performance-tuner + adversarial-analyst + observability-analyst
- **api-performance-check** — performance-tuner + architect + code-reviewer

## Project Structure

```
hydra/
├── CLAUDE.md              # Orchestrator instructions (execution strategy, routing, merging)
├── README.md              # This file
├── agents/                # Agent role definitions (12 .md files)
├── skills/                # Skill implementations (13 directories, each with SKILL.md)
├── docs/                  # Architecture documentation
│   └── agent-architecture.md
└── examples/              # Example task packets and agent outputs
    ├── task-packet-example.md
    └── agent-output-example.md
```

## Installation

### Install as Claude Code Skills

Hydra's skills are designed to be installed into Claude Code via the project-level `.claude/settings.json` configuration.

#### 1. Clone or copy the plugin

```bash
# If part of claude-agent-kit
git clone <repo-url> ~/claude-agent-kit

# Or copy just the hydra plugin to a location of your choice
cp -r plugins/hydra ~/.claude/plugins/hydra
```

#### 2. Add the CLAUDE.md orchestrator instructions

Copy the contents of `CLAUDE.md` into your project's `.claude/CLAUDE.md` (or your global `~/.claude/CLAUDE.md`) to enable the orchestrator logic:

```bash
# For global use (all projects)
cat CLAUDE.md >> ~/.claude/CLAUDE.md

# For a specific project
mkdir -p .claude
cat /path/to/hydra/CLAUDE.md >> .claude/CLAUDE.md
```

#### 3. Register skills in settings.json

Add skill entries to your `.claude/settings.json` (project-level) or `~/.claude/settings.json` (global). Each skill maps a slash command name to its `SKILL.md` file:

```json
{
  "skills": {
    "planner": "/path/to/hydra/skills/planner/SKILL.md",
    "code-reader": "/path/to/hydra/skills/code-reader/SKILL.md",
    "implementer": "/path/to/hydra/skills/implementer/SKILL.md",
    "code-reviewer": "/path/to/hydra/skills/code-reviewer/SKILL.md",
    "adversarial-analyst": "/path/to/hydra/skills/adversarial-analyst/SKILL.md",
    "security-analyst": "/path/to/hydra/skills/security-analyst/SKILL.md",
    "solution-fit-analyst": "/path/to/hydra/skills/solution-fit-analyst/SKILL.md",
    "obervability-analyst": "/path/to/hydra/skills/obervability-analyst/SKILL.md",
    "ux-designer": "/path/to/hydra/skills/ux-designer/SKILL.md",
    "code-explainer": "/path/to/hydra/skills/code-explainer/SKILL.md",
    "architecture-design": "/path/to/hydra/skills/architecture-design/SKILL.md",
    "performance-tuner": "/path/to/hydra/skills/performance-tuner/SKILL.md",
    "shared-contract": "/path/to/hydra/skills/shared-contract/SKILL.md"
  }
}
```

Replace `/path/to/hydra` with the actual path where you placed the plugin (e.g., `~/.claude/plugins/hydra`).

#### 4. Verify installation

Start a Claude Code session and invoke a skill:

```
/planner Add authentication to the API
/code-reviewer Review the changes in the last commit
/adversarial-analyst Stress-test the caching layer
```

### Using the Agents Directly

You can also reference agent definitions directly when spawning subagents. The `agents/` directory contains role definitions that describe each agent's responsibilities, focus areas, and when to use them. These are used by the orchestrator to route tasks and pack context for subagent calls.

## Shared Output Contract

All agents return structured output in this format:

```
[AGENT] <name>
[STATUS] OK | BLOCKED | NEEDS_INPUT
[SUMMARY] <= 20 words

[FINDINGS]
- <fact>

[ACTION]
1. <next step>

[RISKS]
- <risk>

[NEEDS]
- <missing item>
```

Max ~180 tokens per response unless status is BLOCKED.

## Design Principles

- **Narrow specialization** — each agent does one job well
- **Context isolation** — subagents receive only relevant data
- **Token efficiency** — dense, evidence-based, actionable outputs
- **Orchestrator owns synthesis** — subagents report, the orchestrator merges
- **Evidence-based** — distinguish observed facts from inferences
- **Convergence** — max 1 parallel pass + 1 escalation pass; no loops
