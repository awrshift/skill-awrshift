---
name: awrshift
description: "Adaptive decision framework — one dynamic flow with user checkpoints at every phase. Guides from problem to solution through structured research, metrics, factcheck, and sandbox testing. Use when you face a non-trivial decision, need to research before building, plan a feature or experiment, evaluate trade-offs, or the user says 'awrshift', 'let's think this through', 'research first', 'experiment', 'investigate', 'what's the best approach', 'compare options'. Also trigger on: 'исследуй', 'разберись', 'проанализируй', 'эксперимент', or when starting any new project phase, migration, launch, or architecture decision. Do NOT use for simple tasks with clear instructions — just do those directly."
---

# AWRSHIFT — Adaptive Decision Framework v2

Think before you build. Research before you code. Decide with evidence.

## Core Principle: User-in-the-Loop

Every phase transition requires explicit user confirmation via AskUserQuestion. The system NEVER proceeds silently. The user always knows: what just happened, what happens next, and chooses how to proceed.

## The Flow

One adaptive flow. Depth adjusts per phase based on user's scope choice.

```
IDENTIFY → RESEARCH → EVALUATE-DESIGN → HYPOTHESIZE → PLAN → FACTCHECK → TEST (sandbox) → DECIDE → [IMPLEMENT]
```

**Mandatory (NEVER skip):** IDENTIFY, RESEARCH, EVALUATE-DESIGN, PLAN, FACTCHECK, DECIDE
**Adaptive (user chooses):** HYPOTHESIZE (skip if single clear path), TEST (skip if plan-only)
**Gated (explicit GO required):** IMPLEMENT in main project

## The Checkpoint Pattern

At EVERY phase boundary, execute this exact sequence:

1. **Summary** — what was done, key findings, artifacts created
2. **Preview** — what the NEXT phase does and why it matters
3. **Choice** — AskUserQuestion with options A/B/C/D + free-text

**CRITICAL:** Always use AskUserQuestion tool for choices. Never present options as plain text. The user must get structured, clickable choices.

Example checkpoint:
```
AskUserQuestion:
  question: "IDENTIFY complete. Problem defined, 3 unknowns found. Next: RESEARCH — I'll investigate unknowns using parallel agents. How to proceed?"
  options:
    - "Proceed with research on all 3 unknowns"
    - "I have context on some unknowns — let me share first"
    - "Add more unknowns to investigate"
    - "Skip research — I already know the answer"
```

---

## Phase Execution

### IDENTIFY (mandatory)

Define the problem before solving it. Use AskUserQuestion for structured questionnaire.

**Step 1 — Questionnaire (one question at a time):**

Q1: "What problem are we solving? Describe the current situation."
→ Free text

Q2: "What does 'done' look like? Desired end state."
→ Free text

Q3: "What do we already know? Prior research, decisions, constraints?"
→ Options: A) "I have context to share" B) "No prior context" C) "Check project history"

Q4: "What scope fits this task?"
→ Options:
  A) "Quick investigation — just need an answer" (lightweight)
  B) "Need a plan with metrics" (standard)
  C) "Multiple approaches to compare" (deep)
  D) "Not sure — help me figure it out"

**Step 2 — Produce IDENTIFY summary:**
```
Problem: [what's wrong or needed]
Current: [state now]
Target: [desired state]
Gap: [delta]
Unknowns: [numbered list]
Scope: [lightweight | standard | deep]
```

**Step 3 — Checkpoint:** Show summary, confirm with user, ask to proceed to RESEARCH.

---

### RESEARCH (mandatory)

Gather evidence before forming opinions.

**Step 1 — Generate research questions** from unknowns. Group by topic.

**Step 2 — Checkpoint:** Show questions, ask user to validate via AskUserQuestion before dispatching agents.

**Step 3 — Execute research:**
- Lightweight: 1-2 inline queries
- Standard: 2-3 parallel agents (Agent tool)
- Deep: 3-5 agents, save outputs to `experiments/{NNN}/research/`

**Step 4 — Compile findings:** Synthesize into one document. Highlight confirmed facts, contradictions, remaining unknowns, surprises.

**Step 5 — Checkpoint:** Summarize findings, ask user to proceed to EVALUATE-DESIGN or do more research.

---

### EVALUATE-DESIGN (mandatory)

Define HOW to measure success BEFORE planning. Without metrics, evaluation is subjective.

**Step 1 — Propose 3-7 measurable metrics:**

Each metric needs: name, how to measure, target value, why it matters.

**Step 2 — Checkpoint:**
```
AskUserQuestion:
  question: "Here are success metrics for this experiment: [table]. These will be our criteria at DECIDE phase."
  options:
    - "Metrics look good, proceed"
    - "Modify these metrics"
    - "Research what metrics exist in the market for this domain"
    - "Skip formal metrics — I'll evaluate qualitatively"
```

If user wants market research on metrics → run focused research round, return with updated proposal.

---

### HYPOTHESIZE (adaptive)

When multiple viable approaches exist, name them explicitly.

**Skip condition:** If RESEARCH revealed a single clear winner, ask:
```
AskUserQuestion:
  question: "Research points to one clear approach: [X]. Explore alternatives?"
  options:
    - "One approach is enough, go to PLAN"
    - "I have an alternative to suggest"
    - "Brainstorm alternatives with Gemini (brainstorm skill)"
    - "Compare 2-3 approaches side by side"
```

**If comparing:** Name each hypothesis (H1, H2, H3) with one-sentence description, expected outcome, risk, effort. Present comparison table. Ask user which to plan for.

---

### PLAN (mandatory)

Concrete implementation plan with sequenced tasks.

**Plan must include:**
- Sequenced tasks with dependencies
- File ownership (what gets created/modified)
- Acceptance criteria linked to EVALUATE-DESIGN metrics
- Risk mitigations
- **Sandbox boundary** — ALL work in `experiments/` folder

**Checkpoint:** Show plan, ask user to proceed to FACTCHECK.

---

### FACTCHECK (mandatory)

Verify plan against original context and research BEFORE execution.

**Self-check (always):**
- Does plan address the problem from IDENTIFY?
- Does plan use evidence from RESEARCH?
- Does plan address all metrics from EVALUATE-DESIGN?
- Does plan respect project constraints?

**Cross-model check (optional, recommended for standard/deep):**
If `gemini` skill available:
```bash
python3 ~/.claude/skills/gemini/gemini.py second-opinion @factcheck-prompt.txt \
  --save experiments/{NNN}/factcheck-gemini.md
```

**Checkpoint:**
```
AskUserQuestion:
  question: "Factcheck complete. [N issues found]. [summary]"
  options:
    - "Issues are minor, proceed to TEST"
    - "Fix issues and re-check"
    - "Critical issue — revise PLAN"
    - "Get Gemini second opinion"
```

---

### TEST — Sandbox Execution (adaptive)

Execute plan ONLY in experiment sandbox. See Sandbox Safety Rules below.

**After testing — Checkpoint:**
```
AskUserQuestion:
  question: "Test complete in sandbox. Results: [metrics vs targets]. Artifacts in experiments/{folder}/."
  options:
    - "Results look good, proceed to DECIDE"
    - "Iterate — fix issues and re-test"
    - "Abandon — results don't meet criteria"
    - "Show me the artifacts before deciding"
```

---

### DECIDE (mandatory)

Explicit GO / NO-GO / PIVOT with evidence.

**Decision includes:** metrics comparison (target vs actual), evidence summary, recommendation.

**Checkpoint:**
```
AskUserQuestion:
  question: "Decision time. [metrics table]. Recommendation: [GO/NO-GO/PIVOT] because [reason]."
  options:
    - "GO — implement in main project"
    - "NO-GO — archive experiment, document learnings"
    - "PIVOT — modify approach and re-test"
    - "Need more data before deciding"
```

---

### IMPLEMENT — Main Project (gated)

Move validated work from sandbox to main project. ONLY after DECIDE(GO).

**Pre-implementation gate (mandatory):**
```
AskUserQuestion:
  question: "Here's exactly what will change in main project:
    CREATE: [file list]
    MODIFY: [file list with what changes]
    Proceed?"
  options:
    - "Proceed with all changes"
    - "Implement only specific items"
    - "Show diff preview first"
    - "Wait — let me review sandbox artifacts again"
```

NEVER start without this gate passing.

---

## Sandbox Safety Rules

These protect the main project from unvalidated changes. NEVER violate.

### During experiment (IDENTIFY through DECIDE):
1. Create files ONLY in `experiments/{NNN}-{slug}/`
2. Read main project files — allowed (for context)
3. Modify main project files — **FORBIDDEN**
4. Create rules, skills, or configs in main project — **FORBIDDEN**
5. Install packages or change dependencies — **FORBIDDEN**
6. Need to test against main project code — create copies in experiment folder

### After DECIDE(GO) + IMPLEMENT gate:
7. Show exact file list before any changes
8. One-by-one confirmation for modifications
9. Document rollback plan
10. If anything goes wrong — stop, tell user, offer git revert

### If you accidentally modify main project during experiment:
1. Stop immediately
2. Tell user what was modified
3. Offer to revert
4. Document in experiment PLAN.md

---

## Experiment Documentation

### File structure

Simple (single file):
```
experiments/{NNN}-{short-name}.md
```

Complex (folder):
```
experiments/{NNN}-{short-name}/
├── PLAN.md              ← Master: status, phases, metrics, decisions
├── research/
│   └── 01-{topic}.md
├── factcheck.md
└── [artifacts]
```

### PLAN.md Template

```markdown
# Experiment {NNN}: {Title}

**Status:** {IDENTIFY | RESEARCH | EVALUATE-DESIGN | HYPOTHESIZE | PLAN | FACTCHECK | TEST | DECIDE | DONE (GO/NO-GO/PIVOT)}
**Started:** {date}
**Scope:** {lightweight | standard | deep}

## Problem
{From IDENTIFY}

## Success Metrics
| Metric | Target | Actual | Status |
|--------|--------|--------|--------|

## Phases
| Phase | Status | Key Output |
|-------|--------|------------|

## Key Decisions
{Inline as they happen}

## Research Findings
{Summary or pointer to research/}

## Factcheck Results
{Summary or pointer to factcheck.md}
```

### Numbering
Continue from highest existing number in `experiments/`. Check before creating.

---

## Integration with Other Skills

### brainstorm (optional)
**When:** HYPOTHESIZE phase, multiple viable paths.
**How:** Offer as AskUserQuestion choice: "Brainstorm alternatives with Gemini"
Brainstorm produces converged recommendation → feed back as hypothesis.

### gemini (optional)
**When:** FACTCHECK for cross-validation. HYPOTHESIZE for second opinion.
**How:**
```bash
python3 ~/.claude/skills/gemini/gemini.py second-opinion @prompt.txt --save result.md
```

Neither skill is mandatory. The framework works standalone.

---

## Adaptive Behavior

Scope (set at IDENTIFY) influences depth, not structure:

| Scope | RESEARCH | HYPOTHESIZE | FACTCHECK | TEST |
|-------|----------|-------------|-----------|------|
| Lightweight | 1-2 queries | Skip (single path) | Self-check only | Optional |
| Standard | 2-3 agents | If multiple paths | Self + optional Gemini | Yes |
| Deep | 3-5 agents | Always compare | Self + Gemini mandatory | Yes + iterate |

If research reveals unexpected complexity, propose upgrading scope via AskUserQuestion.

**Minimum that NEVER gets skipped (any scope):**
1. IDENTIFY — define the problem
2. EVALUATE-DESIGN — define success criteria
3. PLAN — have a plan before executing
4. FACTCHECK — verify before testing
5. DECIDE — end with explicit decision
6. Documentation — create experiment file
7. Sandbox — work in experiments/ during experiment
8. AskUserQuestion — at every phase transition
