# 2026-03-13 Linear + Symphony: What should I do when AI is "done" but the PR still needs review-driven fixes?

## Question

Original question:

> I want to understand the Linear + Symphony automation workflow.
> A task was assigned to AI in Linear, the AI finished the implementation and opened a PR, but later I noticed:
>
> - the PR still has AI code review comments that need to be fixed
> - or I am not satisfied with the result and want the AI to do another pass
>
> What should I do then?
>
> - Should I keep using the original task?
> - Or should I create a new task?
> - Does Symphony itself support this kind of loop?

## Short Answer

Default recommendation:

- If this is still the same goal, the same PR, and the same acceptance scope, do not create a new task.
- Reuse the same Linear issue and move it back into an executable state so Symphony can pick it up again.
- Create a follow-up issue only when the new work is genuinely outside the original task scope or deserves separate long-term tracking.

## What I Looked At

I inspected these files in the upstream Symphony repository:

- `SPEC.md`
- `elixir/README.md`
- `elixir/WORKFLOW.md`
- `elixir/lib/symphony_elixir/orchestrator.ex`
- `elixir/lib/symphony_elixir/agent_runner.ex`
- `elixir/lib/symphony_elixir/linear/adapter.ex`
- `elixir/lib/symphony_elixir/codex/dynamic_tool.ex`
- `.codex/skills/land/SKILL.md`
- `.codex/skills/push/SKILL.md`
- `.codex/skills/linear/SKILL.md`

Why these files:

- `SPEC.md` defines the product boundary.
- `WORKFLOW.md` defines the actual ticket-state behavior.
- `orchestrator.ex` and `agent_runner.ex` show when Symphony will continue working.
- `linear/adapter.ex` and `dynamic_tool.ex` show what Linear-side capabilities exist.
- The repository skills show how PR feedback is expected to be handled in practice.

## Key Findings

### 1. Symphony is a scheduler and runner, not a built-in product manager

`SPEC.md` makes the boundary explicit:

- Symphony is primarily a scheduler/runner and tracker reader.
- Ticket writes, PR comments, and workflow-specific business logic are expected to live in the workflow prompt and in the available tools.

That means Symphony does not automatically decide:

- whether a review comment should be accepted
- whether this is a small fix or a full retry
- whether a new task should be created

Those decisions mainly come from `WORKFLOW.md`.

### 2. Symphony only keeps working while the issue stays in an active state

From the default `elixir/WORKFLOW.md`, the active states are:

- `Todo`
- `In Progress`
- `Merging`
- `Rework`

The default workflow treats `Human Review` as a waiting state, not an active execution state.

Combined with `agent_runner.ex` and `orchestrator.ex`, the behavior is:

- when an agent turn ends normally and the issue is still active, Symphony can continue
- when the issue is not active, Symphony stops coding

So once an issue reaches `Human Review`, Symphony will not resume coding just because a new GitHub review comment appears. Someone or some other automation must move the issue back into an active state.

### 3. The default workflow already defines two useful paths

The default `WORKFLOW.md` already splits this problem into two paths.

#### Path A: Reuse the same issue and return to execution

When a ticket already has an attached PR, the workflow requires a PR feedback sweep:

- read top-level PR comments
- read inline review comments
- read review summaries and states
- treat actionable feedback as blocking until it is fixed or explicitly pushed back

The same workflow also uses:

- `Todo` as a state that can become executable again
- `Rework` for review-requested changes and another implementation pass

#### Path B: Create a new backlog issue for out-of-scope improvements

The same `WORKFLOW.md` also says:

- if meaningful out-of-scope improvements are discovered,
- create a separate issue in `Backlog`,
- instead of silently expanding the current task

So "rework" and "new scope" are already treated as different things in the default design.

### 4. There is no built-in GitHub-review-event to auto-reopen-work bridge

I did not find a built-in mechanism that says:

- when GitHub gets a new review comment,
- automatically move the related Linear issue back to `Todo` or `Rework`

The built-in dynamic tool I found was `linear_graphql`, which helps the agent talk to Linear. GitHub interactions are still largely handled through repository skills and `gh` CLI.

## Two Practical Solutions

### Solution 1: Reuse the same Linear issue

Use this when:

- it is still the same goal
- the same PR is still relevant
- the work is review follow-up, validation cleanup, or another polish pass

Recommended handling:

- small or moderate follow-up: move the issue back to `Todo`
- deeper reset or direction change: move the issue to `Rework`

Why this is usually better:

- it preserves one task context
- it preserves the same PR, workpad, and acceptance chain
- it avoids splitting one normal review loop into multiple tasks

This should be the default choice in most cases.

### Solution 2: Create a follow-up issue

Use this when:

- the new work is outside the original acceptance scope
- it is a later improvement rather than a merge blocker
- you want independent prioritization and tracking

Typical examples:

- "While we are here, we should also optimize this architecture"
- "This is not a blocker, but it is worth improving later"
- "The current PR can merge first, and this should be handled separately"

In that case:

- create a new Linear issue
- keep the current issue focused
- link the two if needed

## My Recommended Default

Default priority:

1. For review fixes or another pass on the same task, keep the same issue.
2. For a smaller follow-up pass, move it back to `Todo`.
3. For a bigger reset, move it to `Rework`.
4. For clearly out-of-scope work, create a follow-up issue.

One-sentence summary:

> Treat it as the next execution round of the same issue before reaching for a brand-new task.
