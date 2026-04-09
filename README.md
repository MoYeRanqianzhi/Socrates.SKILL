# Socrates.SKILL

A dialectical thinking engine skill for AI coding agents. Applies **Socratic questioning + First principles + Occam's razor** in iterative cycles to deepen understanding before action.

This skill doesn't solve problems. It deepens understanding by relentlessly questioning assumptions, reducing to irreducible truths, and rebuilding with minimal complexity.

## Compatibility

Built on the [open agent skills ecosystem](https://skills.sh/), this skill works with any agent that supports the Skills standard:

| Agent | Status |
|-------|--------|
| [Claude Code](https://docs.anthropic.com/en/docs/claude-code) | Fully supported |
| [Copilot CLI](https://githubnext.com/projects/copilot-cli) | Compatible via skills plugin |
| [Gemini CLI](https://github.com/google-gemini/gemini-cli) | Compatible via skill activation |
| Other Skills-compatible agents | Should work out of the box |

## How It Works

Each iteration runs a complete **Thesis -> Antithesis -> Synthesis** cycle:

1. **Thesis** -- State the current understanding as one clear, falsifiable sentence
2. **Antithesis** -- Challenge it using Socratic questioning (5 question types)
3. **Synthesis** -- Strip to first principles, rebuild with Occam's razor

The synthesis becomes the next thesis. The cycle continues until the user interrupts or the **Mutation Guard** detects an epistemic boundary (2 consecutive rounds with no genuine shift in understanding).

## Quick Start

### Installation

**Via Skills CLI (recommended):**

Search and install from the skills ecosystem:

```bash
# Find the skill
npx skills find socrates

# Install globally
npx skills add MoYeRanqianzhi/Socrates.SKILL@socrates -g -y
```

**Via Git:**

```bash
# Clone and copy into your project
git clone https://github.com/MoYeRanqianzhi/Socrates.SKILL.git
cp -r Socrates.SKILL/skills/socrates/ /path/to/your-project/skills/socrates/
```

**Manual:**

Copy the `skills/socrates/` directory into your project's or user-level skill directory.

### Usage

Trigger the skill by asking your agent to think deeply:

```
/socrates Should we use microservices or a monolith?
```

Or naturally:

```
Think deeply about whether we should take VC funding or bootstrap.
Question the assumptions behind our decision to migrate to Kubernetes.
Analyze from first principles whether democracy is the best governance system.
```

The skill activates automatically when it detects complex decisions, architectural trade-offs, philosophical questions, first-principles reasoning requests, or debates between two approaches.

## Skill Structure

```
skills/socrates/
├── SKILL.md                          # Core SOP (259 lines, ~3-4k tokens)
└── reference/
    ├── socratic-questions.md         # 5 question types in depth (loaded on demand)
    └── engineering.md                # Engineering specialization overlay (loaded on demand)
```

Follows **Progressive Disclosure** -- only loads what's needed:

| Level | Content | Loaded When | Tokens |
|-------|---------|-------------|--------|
| L1 | Name + description | Always | ~100 |
| L2 | SKILL.md | Skill triggered | ~3-4k |
| L3 | Reference files | On demand | Unlimited |

## Five Socratic Question Types

| Type | When to Use |
|------|-------------|
| **Clarification** | Terms are ambiguous, definitions assumed, scope unclear |
| **Assumption Probing** | Thesis rests on unexamined premises |
| **Evidence Probing** | Claims lack justification or evidence is weak |
| **Perspective Shifting** | Only one viewpoint considered |
| **Consequence Tracing** | Implications unexplored, may lead to contradictions |

## Dual Mode

### Independent Mode (User-Facing)

Outputs visible thinking chain with structured rounds:

```
## Round N

### Thesis
> Current understanding

### Antithesis
**Question type**: Assumption Probing
[Questioning process...]
**Shaken assumption**: [What was undermined]

### Synthesis
**Irreducible facts**: [...]
**Simplest reconstruction**: [New understanding]
**Mutation**: Yes -- [what shifted]
```

### Engine Mode (Called by Other Skills)

Runs silently, returns only final synthesis. Other skills can invoke Socrates as a pre-analysis thinking engine:

```
[socrates-config]
max_rounds: 3
focus: "architecture decision"
return: "final_synthesis"
[/socrates-config]
```

## Key Mechanisms

### Mutation Guard

Prevents degeneration into repetition. After each synthesis:
- **Mutation detected** -> Proceed to next round
- **No mutation** -> Switch question type, try deeper assumptions
- **2 consecutive failures** -> Declare epistemic boundary (user decides next step)

Rephrasing is not mutation. Reordering is not mutation. Only genuine shifts count.

### Engineering Overlay

Automatically activates when the problem involves software engineering. Adds:
- Engineering-specific questioning dimensions
- First principles for engineering (physical constraints, theoretical limits, resource constraints)
- Common assumption traps (microservices scaling, caching performance, NoSQL for unstructured data, etc.)

## Hard Rules

1. Never skip the antithesis
2. Never disguise repetition as mutation
3. Never appeal to authority in synthesis
4. Never converge prematurely
5. Every round must expose at least one assumption

## Evaluation Results

Tested with TDD approach (RED -> GREEN):

- **Baseline (no skill)**: 0 self-questioning, conclusion-first, authority-as-proof
- **With skill**: 24/24 assertions pass, all baseline deficiencies resolved
- **Engine mode**: Correctly runs silent, respects max_rounds
- **Mutation guard**: Correctly detects convergence, declares epistemic boundary

## License

[MIT](LICENSE) -- Copyright (c) 2026 墨叶染千枝
