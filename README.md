<div align="center">

![Claude Code](https://img.shields.io/badge/Claude%20Code-D97757?style=for-the-badge&logo=claude&logoColor=white)
![Codex CLI](https://img.shields.io/badge/Codex%20CLI-000000?style=for-the-badge&logo=openai&logoColor=white)
![Gemini CLI](https://img.shields.io/badge/Gemini%20CLI-4285F4?style=for-the-badge&logo=google&logoColor=white)
![License MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)

# AWRSHIFT

**Adaptive decision-making framework for AI agents. Think before you build.**

*Works with any AI coding assistant that supports [Agent Skills](https://agentskills.io)*

</div>

---

## What It Does

- **Selects the right mode** automatically — Quick (trivial tasks), Standard (research needed), Scientific (competing hypotheses)
- **Creates experiment files** with structured phases, research artifacts, and decision records
- **Dispatches parallel research agents** for independent questions — no sequential bottleneck
- **Integrates with brainstorm & gemini** skills for multi-model ideation and fact-checking
- **Adapts dynamically** — skips phases that add no value, expands when findings are insufficient

## Quick Install

**Claude Code (recommended):**
```
/plugin marketplace add awrshift/skill-awrshift
```

**Manual (any agent):**
```bash
mkdir -p .claude/skills/awrshift
curl -sL https://raw.githubusercontent.com/awrshift/skill-awrshift/main/skills/awrshift/SKILL.md \
  -o .claude/skills/awrshift/SKILL.md
```

## How It Works

The framework has three modes. It picks one automatically based on task complexity.

### Quick Mode
```
IDENTIFY → IMPLEMENT → TEST → DONE
```
For clear tasks with no unknowns. No experiment file needed.

### Standard Mode
```
IDENTIFY → FORMULATE → RESEARCH → COMPILE → PLAN → IMPLEMENT → TEST → EVALUATE → DECIDE
```
For tasks with multiple approaches. Creates an experiment file, uses parallel research agents.

### Scientific Mode
```
IDENTIFY → FORMULATE → RESEARCH → HYPOTHESIZE → [H1: test] → [H2: test] → COMPARE → DECIDE
```
For high-stakes decisions with competing hypotheses. Adds Gemini fact-check gates.

## Usage

| You say | AWRSHIFT does |
|---------|--------------|
| "Let's think this through" | Activates Standard mode, creates experiment |
| "Research first" | FORMULATE phase — generates questions, asks you to validate |
| "Compare these two approaches" | Scientific mode with hypothesis testing |
| "What's the best approach for X?" | IDENTIFY + mode selection based on complexity |
| "Experiment" | Creates experiment file, starts IDENTIFY phase |

## Experiment Structure

Every Standard/Scientific task creates persistent documentation:

```
experiments/{NNN}-{slug}/
├── EXPERIMENT.md          ← Status, phases, decisions
├── research/
│   ├── 01-{topic}.md      ← Agent findings
│   └── ...
└── {NN}-compile.md        ← Synthesized results
```

## Key Principles

1. **Always identify before solving** — state the problem before writing code
2. **Research before planning** — unknowns kill plans
3. **Document decisions inline** — no separate ADR files, decisions live in EXPERIMENT.md
4. **One experiment = one topic** — no mixing concerns
5. **Every experiment ends with DECIDE** — GO / NO-GO / PIVOT with evidence

## Works With

| Platform | Install |
|----------|---------|
| Claude Code | `/plugin marketplace add awrshift/skill-awrshift` |
| Codex CLI | Copy `SKILL.md` to `.openai/skills/awrshift/` |
| Gemini CLI | Copy `SKILL.md` to `.gemini/skills/awrshift/` |
| Cursor | Copy `SKILL.md` to `.cursor/skills/awrshift/` |
| Any Agent Skills-compatible tool | Follow [spec](https://agentskills.io) |

## Gotchas

- **Quick mode doesn't create experiment files** — if you want documentation, say "standard mode"
- **FORMULATE phase asks you questions** — this is intentional. Your context prevents wasted research
- **Parallel agents need the Agent tool** — if your setup doesn't support subagents, research runs sequentially
- **Gemini integration requires the `gemini` skill** — needed only for Scientific mode fact-check gates

## Part of the AWRSHIFT Ecosystem

- [**AWRSHIFT Framework**](https://github.com/awrshift/awrshift) — the full methodology + Claude Code integration
- [**ClawClaw Soul**](https://clawclawsoul.com) — persistent identity protocol for AI agents
- [**skill-brainstorm**](https://github.com/awrshift/skill-brainstorm) — multi-model brainstorm (Claude x Gemini) *(coming soon)*

## Contributing

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## License

MIT — see [LICENSE](LICENSE) for details.

---

<div align="center">
<em>Think before you build.</em>
</div>
