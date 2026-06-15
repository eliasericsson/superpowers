---
name: red-teaming-assumptions
description: "You MUST use this to stress-test a design or spec before it is locked. Dispatches an INDEPENDENT subagent that sees only the written spec - not the conversation that produced it - and tries to break each load-bearing assumption with concrete counter-examples. Use after a spec is written and self-reviewed, before asking the user to sign off, or any time someone wants to validate that the assumptions in a design actually hold."
---

# Red-Teaming Assumptions

Find the assumptions a design rests on, then try to break them with concrete counter-examples — before they turn into wasted implementation work.

The point of this skill is independence. An agent that just brainstormed a design shares the blind spots that produced it; asking it to critique its own assumptions mostly produces agreement. So the actual red-teaming is done by a fresh subagent that receives only the spec document — never the brainstorming transcript, never the reasoning, never any reassurance about why a choice was made. It reads the artifact cold and assumes it is wrong.

## When this runs

This skill is invoked as a step in the brainstorming flow, after the spec is written and self-reviewed and before the user review gate. The user should review a spec whose assumptions have already been stress-tested. It can also be invoked on its own against any existing spec.

## Checklist

You MUST create a task for each of these items and complete them in order:

1. **Locate the spec** — the written design document (e.g. `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`). If no written spec exists, stop and write one first; you cannot red-team a conversation.
2. **Dispatch the red-team subagent** — give it ONLY the spec path and the red-team prompt. See "Dispatching" below.
3. **Receive the report** — a structured list of assumptions, counter-examples, and severities.
4. **Triage each finding** — for every counter-example, decide: revise the assumption/design, add explicit handling, or accept as a documented risk with rationale. Do not silently ignore findings.
5. **Update the spec inline** — apply revisions and added handling. Record accepted risks in an "Assumptions & accepted risks" section of the spec.
6. **Save the report** — write it to `docs/superpowers/specs/YYYY-MM-DD-<topic>-redteam.md` and commit, so the audit trail survives.
7. **Return to the flow** — hand back to brainstorming's user review gate with an updated spec.

## Dispatching

Spawn a subagent (the Task tool in Claude Code; see using-superpowers tool mappings for Codex / Gemini / Copilot equivalents). The dispatch MUST be clean:

Give the subagent:

- The path to the spec document.
- The full contents of `red-team-prompt.md` (this directory) as its instructions.

Do NOT give the subagent:

- The brainstorming conversation or any summary of it.
- The reasons a particular approach was chosen.
- Any framing like "we already considered X" or "this is fine because Y."

If the subagent is anchored to your reasoning, it stops being independent and the exercise is worthless. The only context it gets is the artifact a future implementer would get.

## Triage stance

When the report comes back, resist the urge to defend the design. A counter-example you can wave away in your head is exactly the kind of unexamined assumption this process exists to catch. For each finding ask: if this scenario actually happened, what would the system do? If the honest answer is "something bad" or "I don't know," it needs a revision or an explicitly accepted risk — not a dismissal.

Accepting a risk is a legitimate outcome, but it must be explicit and written down, with the reasoning, so the user can see it at the review gate and overrule it if they disagree.

## Key Principles

- **Independence over thoroughness** — a fresh subagent reading the spec cold beats the original author re-reading their own work, even if the author would find more on paper.
- **Concrete over abstract** — "this might not scale" is noise; "at 10k concurrent users the in-memory session map exhausts heap" is a finding.
- **Hunt implicit assumptions** — the dangerous ones are never written in the spec. They live in what the spec takes for granted.
- **Find breaks, don't solve** — the subagent's job is to break things; deciding what to do about each break is the main agent's job during triage.
- **Every accepted risk is documented** — silent dismissal defeats the purpose.
