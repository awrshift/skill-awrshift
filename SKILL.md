---
name: awrshift
description: "Dynamic decision-making framework that adapts to task complexity. Automatically selects Quick (trivial), Standard (most work), or Scientific (competing hypotheses) mode. Use this skill whenever you face a non-trivial decision, need to research before building, plan a feature or experiment, evaluate trade-offs, or the user says 'awrshift', 'let's think this through', 'research first', 'experiment', 'investigate', 'what's the best approach', 'compare options', or asks to plan anything that has unknowns. Also trigger when starting any new project phase, technical migration, launch planning, or architecture decision. Do NOT use for simple implementation tasks with clear instructions — just do those directly."
---

# AWRSHIFT — Adaptive Decision Framework

Think before you build. Research before you code. The framework adapts to the task.

## Mode Selection (automatic)

Assess the task and pick one mode. State your choice explicitly before starting.

### Quick Mode
**When:** Clear solution, no unknowns, implementation-only.
```
IDENTIFY → IMPLEMENT → TEST → DONE
```
No experiment file needed. Just do the work.

### Standard Mode
**When:** Multiple approaches possible, some unknowns, needs research.
```
IDENTIFY → FORMULATE → RESEARCH → COMPILE → PLAN → IMPLEMENT → TEST → EVALUATE → DECIDE
```
Creates experiment file. Uses parallel research agents when beneficial.

### Scientific Mode
**When:** Competing hypotheses, high stakes, need rigorous comparison.
```
IDENTIFY → FORMULATE → RESEARCH → COMPILE → HYPOTHESIZE
→ [H1: PLAN → IMPLEMENT → TEST → EVALUATE]
→ [H2: PLAN → IMPLEMENT → TEST → EVALUATE]
→ COMPARE → DECIDE
```
Creates experiment file. Requires Gemini fact-check gate. Use `brainstorm` skill for multi-model ideation when exploring the hypothesis space.

## Phase Execution

### IDENTIFY
Define the problem in structured format:
```
Problem: [what's wrong or what's needed]
Current: [state now]
Target: [desired state]
Gap: [current → target delta]
Unknowns: [what we don't know]
```

### FORMULATE
Generate research questions grouped by topic. **Ask the user** to validate before proceeding — they may have context you don't. This prevents researching the wrong thing.

### RESEARCH
Dispatch parallel research agents (Agent tool) for independent questions. Each agent returns findings — do NOT duplicate their work yourself.

For Standard: 1-3 agents, focused queries.
For Scientific: 2-5 agents + Gemini gate (use `gemini` skill with `second-opinion` for cross-validation).

### COMPILE
Synthesize all research into one document. For Scientific mode, include Gemini fact-check. Output: `{NN}-compile.md` in experiment directory.

### PLAN
Create concrete implementation plan with:
- Sequenced tasks (TaskCreate with blockedBy for Standard/Scientific)
- File ownership (one file = one agent, always)
- Acceptance criteria per task
- Risk mitigations

### IMPLEMENT → TEST → EVALUATE
Execute the plan. Run tests. Measure against the criteria from PLAN.

### DECIDE
GO / NO-GO / PIVOT with concrete evidence. Every experiment ends with a decision.

## Experiment Documentation (mandatory for Standard + Scientific)

### File Structure
```
experiments/{NNN}-{slug}/
├── EXPERIMENT.md          ← Master file: status, phases, decisions
├── research/              ← Research agent outputs
│   ├── 01-{topic}.md
│   └── ...
├── {NN}-compile.md        ← Synthesized findings (if applicable)
└── [other artifacts]
```

### EXPERIMENT.md Template
```markdown
# Experiment {NNN}: {Title}

**Status:** {IN PROGRESS | DONE (GO) | DONE (NO-GO) | DONE (PIVOT)}
**Started:** {date} (Session {N})
**Mode:** {Quick | Standard | Scientific}
**Hypothesis:** {one sentence}

## Phases

| Phase | Status | Notes |
|-------|--------|-------|
| IDENTIFY | {status} | {brief} |
| ... | | |

## Key Decisions

{Inline with context — no separate ADR files}

## Backlog

| ID | Task | Status | Depends on |
|----|------|--------|------------|
```

### Experiment Numbering
Continue from the highest existing number in `experiments/`. Check before creating.

### Rules
1. One experiment = one focused topic. No mixing.
2. Experiment ends with DECIDE (GO/NO-GO + concrete deliverable).
3. Next experiment starts only after previous reaches DECIDE.
4. Decisions are recorded inline in EXPERIMENT.md, not in separate files.
5. Research outputs go in `research/` subdirectory.

## Integration with Other Skills

### brainstorm
When Standard or Scientific mode needs multi-model ideation (exploring options, challenging assumptions), invoke the `brainstorm` skill. Brainstorm produces a converged decision; AWRSHIFT provides the structural container (experiment, phases, documentation).

**When to use brainstorm inside AWRSHIFT:**
- FORMULATE phase: exploring what questions to ask
- Between RESEARCH and PLAN: choosing between approaches
- COMPARE phase (Scientific): stress-testing hypotheses

### gemini
For fact-check gates in Scientific mode, use the `gemini` skill directly:
- `second-opinion` for cross-validation of findings
- `ask --grounded` for quick factual verification

## Adaptive Behavior

The framework adapts, not the other way around:

- **Skip phases that add no value.** If RESEARCH reveals a clear winner, skip COMPARE.
- **Merge phases when obvious.** IDENTIFY + FORMULATE can be one step for small tasks.
- **Expand when needed.** Add research rounds if early findings are insufficient.
- **Always preserve:** experiment documentation, decision records, research artifacts.

The minimum that NEVER gets skipped:
1. **IDENTIFY** — always state the problem before solving it
2. **Documentation** — always create EXPERIMENT.md for Standard/Scientific
3. **DECIDE** — always end with an explicit decision
4. **Artifacts** — always save research outputs in experiments/
