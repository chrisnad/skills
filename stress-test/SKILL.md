---
name: stress-test
description: Use when user wants to stress-test a plan, get challenged on their design, pressure-test assumptions, or mentions "challenge me". Also use when user presents a design and asks "what am I missing" or "poke holes in this".
---

# Stress-Test

Adversarial plan review through structured interrogation. Surfaces hidden assumptions, missing problems, incoherent design, and failure modes the author can't see due to confirmation bias.

## Two Premises That Shape Every Phase

**No one knows exactly what they want.** The plan is a hypothesis about a problem the author has only partially formulated. Stated requirements are a starting point, not ground truth. A plan often solves a *stated* problem while the *real* problem hides behind it. Your job is to help the author discover what they actually need — not to grade what they wrote down.

**A plan needs a design concept.** A coherent design has one organizing idea that every decision reinforces. A plan without a stateable main idea is an aggregation of decisions, not a design. Decisions that pull against the main idea are fracture lines and predict where the plan will fail. Constraints surface *during* this conversation; the ones the author hadn't yet hit are the dangerous ones.

## Phase 1: Ground in Reality

Before asking a single question, gather context silently. Pick the audit mode that matches the document.

**Mode A — Intent docs** (brainstorming output, design proposals, RFCs). Audit claims **against reality**:

- Existing systems and prior art the doc references — do they exist and behave as claimed?
- Alternatives considered — are dismissed options actually unviable?
- User-need and use-case claims — verifiable, or assumed?
- "We tried this before" / prior-art claims — accurate?

**Mode B — Implementation plans** (post-/writing-plans, concrete proposals). Audit claims **against the codebase**. **Crucial for brownfield work** — most plan failures here are wrong assumptions about existing code.

- Identify all actual consumers, callers, integration points
- Compare against the proposal's claimed consumers/callers/scope
- Verify data flows, types, function signatures match what the proposal describes
- Flag every omission, discrepancy, or contradiction

If the document is mixed or unclear, run both modes at the appropriate depth.

**In both modes, also:**

- **Excavate the real problem.** The doc describes a *solution*. State the *problem* it claims to solve. Then ask whether that's the actual problem — often the stated problem is itself a proposed solution to an unstated one. Hold this open until the underlying need is articulated.
- **Name the design concept.** State the doc's organizing idea in one sentence. If you can't — or the author can't agree with your statement — that is itself a finding: the doc is decisions, not design.

Summarize back: real problem, design concept, factual discrepancies. Treat each discrepancy as its own interrogation branch. Only then begin questioning.

## Phase 2: Interrogate Depth-First

Walk each branch of the decision tree one at a time. For each decision:

1. **Steel-man first.** Articulate the strongest version of why this choice makes sense before challenging it.
2. **Test fit to the design concept.** Does this decision reinforce the main idea, or pull against it? Conflicts here are first-class findings, not stylistic notes.
3. **Test fit to the real problem.** Does this decision serve the underlying need, or only the stated requirement? "Right answer to the wrong question" is a common failure.
4. **Challenge.** One focused question. Wait for the answer. If it doesn't fully resolve the concern, push the same point harder. Do not move on until satisfied.
5. **Pre-mortem the branch.** "If this choice fails in production, what's the most likely cause?" Bias toward constraints that surfaced *during* this conversation — those are the ones the author hadn't thought through.
6. **Resolve, then advance.** Only move to the next branch after the current one is settled.

Before starting, identify all decision branches and their dependencies. Start with the highest-leverage question — often "should we do this at all?" or "is this the real problem?" Resolve branches that constrain downstream choices first.

For each open question, recommend an answer.

## Phase 3: Converge

After all branches are resolved, summarize:

- **The real problem** as now understood (possibly different from the originally stated one)
- **The design concept** as now stated (possibly refined or replaced)
- **Decisions made** and their justifications
- **Risks accepted** and their mitigations
- **Open items** that still need answers

Ask: "Does this match your understanding?" The session ends when the user confirms.

## Rules

- **One question at a time.** Never dump a list of questions.
- **Explore code instead of asking** when a question has a factual answer in the codebase.
- **Never trust the proposal over the code.** If the proposal says there are N callers, verify by searching. If exploration data contradicts a claim, raise it immediately — don't wait for the user to catch it.
- **Treat stated requirements as hypotheses.** The author doesn't fully know what they want yet. Your interrogation is how they find out.
- **Demand a design concept.** If the plan can't be stated as a single organizing idea, that absence is the most important finding in the session.
- **No softballs.** If a question could be answered by any junior engineer, it's too shallow. Push deeper.
- **Stay collaborative, not adversarial.** Steel-manning earns the right to challenge hard.
