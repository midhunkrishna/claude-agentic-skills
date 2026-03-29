# Claude Agentic Skills

A multi-agent orchestration system for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). It decomposes complex software engineering tasks into specialized sub-tasks handled by narrowly-focused agents, then merges their outputs into a single high-signal result.

## How It Works

The system uses a **hub-and-spoke model**:

1. A main **orchestrator** (defined in `CLAUDE.md`) receives user requests
2. It classifies the task and selects an **execution strategy**: direct, sequential, or parallel
3. Specialized **subagents** handle narrow, well-defined analysis or implementation roles
4. All agents return structured output using a **shared compact contract**
5. The orchestrator merges, deduplicates, and filters findings using a **confidence model** and **priority weights**

### Execution Strategies

| Strategy | When to use | Example flow |
|---|---|---|
| **Direct** | Small, localized task; one skill needed | Single agent handles it |
| **Sequential** | Steps depend on each other | planner → code-reader → implementer → code-reviewer |
| **Parallel** | Independent analysis lenses | code-reviewer + adversarial-analyst + security-analyst |

## Agents

Includes 12 specialized agents (defined in `agents/`):

| Agent | Role |
|---|---|
| **planner** | Breaks ambiguous work into execution-ready plans with dependencies and sequencing |
| **code-reader** | Finds relevant files, symbols, dependencies, and current behavior |
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

Each agent has a corresponding skill (invocable as a slash command) in `skills/`, plus a shared output contract:

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

The orchestrator selects from 13 pre-defined bundles based on the task. Conditional agents (marked with "if ...") are included only when relevant.

| Bundle | Core agents | Conditional |
|---|---|---|
| **critique-plan** | code-reviewer, adversarial-analyst, solution-fit-analyst | security, observability, ux |
| **critique-implementation** | code-reviewer, adversarial-analyst, solution-fit-analyst | security, observability |
| **architecture-sanity-check** | architect, solution-fit-analyst, code-reviewer, adversarial-analyst | security, observability |
| **system-design** | architect, solution-fit-analyst, code-reviewer, observability-analyst | security |
| **api-design-check** | code-reviewer, solution-fit-analyst, architect | security, observability |
| **infra-risk-check** | code-reviewer, adversarial-analyst, security-analyst, solution-fit-analyst, observability-analyst | — |
| **design-user-flow** | ux-designer, code-reviewer, solution-fit-analyst | adversarial, security |
| **operability-check** | observability-analyst, adversarial-analyst, code-reviewer | security |
| **async-system-check** | adversarial-analyst, observability-analyst, code-reviewer, solution-fit-analyst | security |
| **performance-review** | performance-tuner, code-reviewer, solution-fit-analyst | observability |
| **hot-path-check** | performance-tuner, code-reviewer, solution-fit-analyst | — |
| **async-system-performance-check** | performance-tuner, adversarial-analyst, observability-analyst, code-reviewer | — |
| **api-performance-check** | performance-tuner, architect, code-reviewer | observability |

## Project Structure

```
claude-agentic-skills/
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

The system has three components that all need to be installed:

| Component | Purpose |
|---|---|
| **CLAUDE.md** | Orchestrator brain — execution strategy, agent routing, merging rules |
| **agents/** | Subagent role definitions — injected as system prompts when the orchestrator delegates |
| **skills/** | Slash commands — procedural guides registered in settings.json |

### Quick Start (Global)

```bash
# 1. Clone the repo
git clone <repo-url> ~/claude-agentic-skills

# 2. Symlink orchestrator and agents into ~/.claude/
ln -sf ~/claude-agentic-skills/CLAUDE.md ~/.claude/CLAUDE.md
ln -sf ~/claude-agentic-skills/agents ~/.claude/agents

# 3. Register skills in ~/.claude/settings.json
```

Add the following to `~/.claude/settings.json`:

```json
{
  "skills": {
    "planner": "~/claude-agentic-skills/skills/planner/SKILL.md",
    "code-reader": "~/claude-agentic-skills/skills/code-reader/SKILL.md",
    "implementer": "~/claude-agentic-skills/skills/implementer/SKILL.md",
    "code-reviewer": "~/claude-agentic-skills/skills/code-reviewer/SKILL.md",
    "adversarial-analyst": "~/claude-agentic-skills/skills/adversarial-analyst/SKILL.md",
    "security-analyst": "~/claude-agentic-skills/skills/security-analyst/SKILL.md",
    "solution-fit-analyst": "~/claude-agentic-skills/skills/solution-fit-analyst/SKILL.md",
    "obervability-analyst": "~/claude-agentic-skills/skills/obervability-analyst/SKILL.md",
    "ux-designer": "~/claude-agentic-skills/skills/ux-designer/SKILL.md",
    "code-explainer": "~/claude-agentic-skills/skills/code-explainer/SKILL.md",
    "architecture-design": "~/claude-agentic-skills/skills/architecture-design/SKILL.md",
    "performance-tuner": "~/claude-agentic-skills/skills/performance-tuner/SKILL.md",
    "shared-contract": "~/claude-agentic-skills/skills/shared-contract/SKILL.md"
  }
}
```

### Project-Level Installation

To install for a single project instead of globally, symlink into the project's `.claude/` directory:

```bash
ln -sf ~/claude-agentic-skills/CLAUDE.md .claude/CLAUDE.md
ln -sf ~/claude-agentic-skills/agents .claude/agents
```

And add the skills block to `.claude/settings.json` instead.

### Verify Installation

Start a Claude Code session and invoke a skill:

```
/planner Add authentication to the API
/code-reviewer Review the changes in the last commit
/adversarial-analyst Stress-test the caching layer
```

### Using Agents Directly

The `agents/` directory contains role definitions that describe each agent's responsibilities, focus areas, and when to use them. These are used by the orchestrator to route tasks and pack context for subagent calls, but can also be referenced directly when spawning subagents.

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

## Orchestrator Behavior

The orchestrator (defined in `CLAUDE.md`) handles:

- **Cost-aware delegation** — scales agent count with task complexity (low: no fan-out, medium: max 2, high: max 3-4)
- **Context packing** — each subagent receives only the goal, constraints, known facts, and input artifacts it needs
- **Confidence model** — findings classified as HIGH (evidence-backed), MEDIUM (plausible), or LOW (speculative)
- **Priority weights** — when agents conflict: security-analyst > adversarial-analyst > code-reviewer > ux-designer
- **Convergence** — max 1 parallel pass + 1 escalation pass; no loops

## Design Principles

- **Narrow specialization** — each agent does one job well
- **Context isolation** — subagents receive only relevant data
- **Token efficiency** — dense, evidence-based, actionable outputs
- **Orchestrator owns synthesis** — subagents report, the orchestrator merges
- **Evidence-based** — distinguish observed facts from inferences
- **Convergence** — stop when findings are clear; no re-running without new input
