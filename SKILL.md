---
name: cairn
description: >-
  A complete software-development discipline in a single file, organized around a durable
  paper trail. Routes every non-trivial build through six phases (clarify, design, plan,
  test-driven implementation, review, ship) and writes a committed markdown document for
  every design and every task, so work survives context loss, restarts, and hand-offs.
  Use this aggressively: trigger it the moment the user wants to build a feature, start or
  scaffold a project, refactor, or fix a bug, and especially when they say "just build it"
  or "vibe code with me" without settled requirements. Activate at the start of any coding
  task, write the design and plan documents as you go, and enforce the quality gates.
  Always defer to explicit overrides such as "skip planning", "no tests", or "don't
  document this".
---

# Cairn

A *cairn* is a stack of stones that marks a trail so the path can be retraced. This skill
does the same for software work: it leaves durable markers — design docs, plan files, a
live task list — at every step, so neither you nor the user loses the thread.

It is a condensed, single-file distillation of the *Superpowers* methodology popularized
by Jesse Vincent's open-source plugin — an original rewrite of the ideas, not a copy of
that project's files. It keeps that methodology's strongest feature: a written, committed
record of every design and every task.

## The one idea that matters

Most failures in agentic coding share one root cause: the agent starts writing code before
it understands the problem. Every expensive retry and every "that's not what I asked for"
traces back to skipping the thinking. So the prime directive is:

**Step back before you step in.** Understand the goal, write it down, agree on a plan,
prove the need with a test, write the minimum to pass, review, then ship. Never jump
straight to implementation on anything non-trivial. Being slower at the start makes you
dramatically faster overall.

## The paper trail

Your context window is finite and will be compressed or reset. The files on disk are your
real memory. Treat documentation as a deliverable, not an afterthought: every design
becomes a spec file, every build becomes a plan file with per-task checkboxes, and every
consequential decision gets recorded. Commit them to git as you go so the trail is durable
and reviewable.

Default locations (use the user's own paths or naming if they have a preference):

```
docs/cairn/
├── specs/      one design file per feature        (written in Phase 1)
├── plans/      one plan file per build, w/ tasks   (written in Phase 2)
└── decisions/  decision records and debug findings (written as needed)
```

Name files `YYYY-MM-DD-topic.md` so the trail is chronological at a glance.

### Spec / design document template

```markdown
# [Feature] — Design
Status: draft | approved        Date: YYYY-MM-DD

## Problem & goal
One paragraph: what we're solving and what "done" looks like.

## Out of scope
What we are explicitly NOT doing.

## Approach
The chosen approach, plus 1–2 alternatives considered and why they lost.

## Key decisions & open questions
Decisions and their rationale; anything still unresolved.
```

### Implementation plan template

```markdown
# [Feature] — Implementation Plan
Goal: one sentence.   Architecture: 2–3 sentences.   Stack: key libraries.

## Global constraints
Project-wide rules every task inherits (version floors, naming, platform limits).

## Tasks
- [ ] Task 1 — [name]
      Files: create / modify / test paths
      Behavior: what it does. Test that proves it. Done = ...
- [ ] Task 2 — [name]
      ...
```

Keep each task small enough to finish and verify in one short sitting. The checkboxes are
a live status index — tick them as tasks complete. If the harness offers a persistent
task-list tool, mirror the tasks there too; it survives context compression better than
re-reading a long plan file.

**Gate:** a phase is not finished until its document is written and committed. No design is
"approved" without a spec file; no build starts without a plan file.

## Dispatcher: route before you act

At the start of a task, classify the request and jump to the matching phase. Do this
silently — don't narrate the routing, just start the right phase.

| User is...                                            | Start at      |
| ----------------------------------------------------- | ------------- |
| Describing something to build ("build X", "add Y")    | 1. Brainstorm |
| Handing you an already-approved spec                  | 2. Plan       |
| Reporting a bug or broken behavior                    | 5. Debug      |
| Asking a question, a one-liner, or a throwaway script | none — answer |

**Honor overrides immediately.** "Skip clarifying", "don't write tests", "don't document
this", "just give me the code", "this is throwaway" — drop the matching gate without
argument. State the tradeoff in one line if it's risky, then comply. The discipline serves
the user, not the reverse.

## Phase 1 — Brainstorm, then write the spec

Goal: turn a rough idea into an approved, written design. Do not write code in this phase.

1. Ask sharp, Socratic questions one cluster at a time — not a wall of twenty. Target what
   changes the design: who uses this, the core use case, what's out of scope, what already
   exists, failure modes, what "done" means.
2. If the request spans multiple independent subsystems, say so first and help decompose it
   into sub-projects before refining details. Each sub-project gets its own spec → plan →
   build cycle.
3. Surface unstated assumptions and propose 2–3 approaches with tradeoffs. Challenge the
   request when it's flawed — that's the job.
4. Present the design in digestible sections; let the user validate each before moving on.
   Every project gets a design, even a todo list — it can be short, but it must exist and be
   approved. "Too simple to need a design" is the exact rationalization this skill exists to
   stop.
5. Write the spec to `docs/cairn/specs/`, self-review it for placeholders, contradictions,
   and ambiguity, ask the user to review the file, and commit.

## Phase 2 — Plan, then write the plan file

Goal: a plan the user can actually read and approve.

1. Map which files will be created or modified and what each is responsible for. Prefer
   small, focused files with clear interfaces — you reason best about code you can hold in
   context at once.
2. Break work into atomic tasks ordered by dependency. For each, note files, the proving
   test, and the definition of done. Write it to `docs/cairn/plans/` using the template.
3. Present it in chunks short enough to digest. The single most valuable review in the whole
   workflow happens here — a plan reviewed before any code prevents more bugs than any
   after-the-fact review can. Get explicit approval; don't approve it on the user's behalf.
   Commit the plan.

## Phase 3 — Implement with TDD (red-green-refactor)

For each task, run the cycle. This is the default discipline unless tests were waived.

1. **Red** — write one failing test for the next slice of behavior. Run it and watch it
   fail. A test that passes before the code exists is broken, not passing.
2. **Green** — write the minimum code to pass. No speculative abstraction. Run it; watch it
   pass.
3. **Refactor** — clean names, structure, and duplication with the test green. Commit, and
   tick the task's checkbox in the plan file.

If you wrote implementation before its test, delete that code and restart the cycle. A test
written after the code only proves the code does what it already does, not what it should
do — which is why the order matters.

## Phase 4 — Review (two stages, in order)

Before any task is "done", review it as a skeptical senior engineer who didn't write it.
Two passes catch different bug classes:

1. **Functional** — does it match the spec and plan? Are edge cases handled? Do the tests
   exercise real behavior, not just the happy path?
2. **Quality** — readable, consistent with codebase conventions, free of dead code and
   accidental complexity?

Report issues by severity. Critical issues block progress — don't advance or call work
finished until they're resolved. Log lower-severity issues in the plan or a decision note.

## Phase 5 — Debug (systematic, not flailing)

When something breaks, resist the urge to immediately try a fix — random "might work" fixes
turn one-line bugs into multi-hour rabbit holes.

1. **Reproduce & investigate** — get a reliable repro, then find the root cause from the
   actual error, stack, and data. Don't guess.
2. **Analyze patterns** — is this a one-off, or an instance of a broader problem with
   sibling bugs?
3. **Hypothesize & test** — form a specific hypothesis and a minimal experiment to confirm
   it before changing anything.
4. **Fix & verify** — apply the smallest correct fix plus a regression test that would have
   caught it. Record the root cause and fix in `docs/cairn/decisions/`.

After three failed fix attempts, stop patching — your model of the system is probably
wrong. Step back to an architectural review before trying again.

## Phase 6 — Ship (finish cleanly)

1. Run the full test suite. Green or it isn't done.
2. Make sure the spec, the plan (all boxes ticked), and any decision notes are committed and
   current.
3. Offer the user real options — merge, open a PR, keep working, or discard — rather than
   deciding for them.
4. Clean up scratch branches, worktrees, and temp files; leave the repo tidy.

## Quality gates

- No code before understanding — brainstorm non-trivial work first.
- No design "approved" without a committed spec file.
- No build started without a committed plan file.
- No production code before a failing test, unless tests were waived.
- No "done" before a two-stage review with critical issues cleared.
- No more than three blind fix attempts before an architectural review.
- Every phase ends with its document written and committed.

## Working environment (use when available)

- **Isolated worktrees** — for multi-task features, work on a separate branch or worktree so
  the main branch stays clean and a wrong approach can be discarded wholesale.
- **Subagents** — when supported, delegate independent tasks or reviews for parallelism and
  fresh-eyes review.
- **Persistent task list** — if the harness has one, mirror plan tasks there; it resists
  context compression better than a long plan file.

All optional; degrade gracefully if the environment lacks them.

## Self-improvement

When you notice a workflow repeating, or the user corrects you into a better pattern, offer
to capture it as its own skill so the lesson persists. Hold your own skill-writing to the
same discipline: small, tested, reviewed, documented.

## Routing examples

**"Build me a landing page for my SaaS."** Brainstorm: audience, the one action you want
visitors to take, voice, must-have sections, out-of-scope. Present a section-by-section
design, write the spec, get sign-off, write the plan, TDD the interactive pieces, review,
ship. Spec and plan both committed.

**"Fix this — the login button does nothing on mobile."** Debug: reproduce on a mobile
viewport, find the root cause (handler? CSS overlay? hydration?), hypothesis, test, minimal
fix plus regression test, verify. Record the root cause in `docs/cairn/decisions/`. Skip
Brainstorm and Plan unless the fix needs a redesign.

**"Just write me a regex to match emails, quick."** Bypass everything — it's a one-liner;
answer directly. Forcing ceremony onto trivial requests is exactly the over-application
that makes discipline annoying instead of useful.

**"Vibe code a todo app with me."** Brainstorm anyway, but keep it fast: two or three
questions, a short spec, then move. Vibe coding done right still means knowing what you're
building before you build it — it doesn't mean skipping the thinking or the paper trail.
