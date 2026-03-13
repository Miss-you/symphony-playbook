# 2026-03-13 Linear + Symphony: AI 完成后，如果要继续修 AI CR 或我自己不满意的内容，应该怎么处理？

## Question / 问题

原始问题：

> 我想弄懂 Linear + Symphony 这样的自动化开发工作流。
> 当一个任务已经在 Linear 上分配给 AI，AI 也已经完成并提了 PR，但后面我发现：
>
> - PR 上还有 AI code review 需要继续修
> - 或者我自己对结果不满意，想让 AI 再改一轮
>
> 这时应该怎么做？
>
> - 是继续用原来的 task？
> - 还是新建一个 task？
> - Symphony 本身有没有支持这种能力？

## Short Answer / 简短结论

默认建议：

- 如果还是同一个目标、同一个 PR、同一轮验收范围里的返工，不要新建 task。
- 继续使用同一个 Linear issue，把它移回可执行状态，让 Symphony 再跑一轮。
- 只有当问题已经超出原任务范围，或者你希望它作为独立事项长期追踪时，才新建 follow-up issue。

Default recommendation:

- Do not create a new task for normal review-driven rework on the same goal.
- Reuse the same Linear issue and move it back into an active state so Symphony can pick it up again.
- Create a follow-up issue only when the new work is genuinely out of scope or deserves separate tracking.

## What I Looked At / 我查了哪些代码

I inspected these files in the upstream Symphony repo:

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

- `SPEC.md` explains the product boundary.
- `WORKFLOW.md` explains the actual ticket-state behavior.
- `orchestrator.ex` and `agent_runner.ex` show when Symphony will continue working.
- `linear/adapter.ex` and `dynamic_tool.ex` show what Linear-side tooling exists.
- The repo skills show how PR feedback is expected to be handled in practice.

## Key Findings / 关键发现

### 1. Symphony is a scheduler and runner, not a built-in product manager

`SPEC.md` makes the boundary explicit:

- Symphony is mainly a scheduler/runner and tracker reader.
- Ticket writes, PR comments, and workflow-specific business logic are expected to live in the workflow prompt and the available tools.

这意味着 Symphony 不会自动替你决定：

- 这条 review 要不要接受
- 这次是“小修”还是“重新来一轮”
- 什么时候应该拆成一个新任务

这些判断主要依赖 `WORKFLOW.md` 的设计。

### 2. Symphony only keeps working while the issue stays in an active state

From the default `elixir/WORKFLOW.md`, the active states are:

- `Todo`
- `In Progress`
- `Merging`
- `Rework`

The default workflow treats `Human Review` as a waiting state, not an active execution state.

再结合 `agent_runner.ex` 和 `orchestrator.ex` 可以看出：

- agent 正常结束后，如果 issue 仍在 active state，Symphony 会继续下一轮
- 如果 issue 不在 active state，Symphony 就不会继续动手

所以：

- 一旦 issue 进入 `Human Review`
- 后面即使 GitHub PR 上又出现新的 review comment
- Symphony 也不会因为“看见评论了”就自动恢复编码

必须有人或其他自动化把 issue 调回 active state。

### 3. Default workflow already defines two useful paths

默认 `WORKFLOW.md` 已经把这个问题拆成了两种处理方式：

#### Path A: Same issue, back to execution

When a ticket already has an attached PR, the workflow requires a full PR feedback sweep:

- read top-level PR comments
- read inline review comments
- read review summaries and states
- treat actionable feedback as blocking until fixed or explicitly pushed back

而且默认状态映射里已经定义了：

- `Todo` can act as a queued state that becomes executable again
- `Rework` is for review-requested changes and another implementation round

#### Path B: New backlog issue for out-of-scope improvements

The same `WORKFLOW.md` also says:

- if meaningful out-of-scope improvements are discovered,
- create a separate issue in `Backlog`,
- instead of silently expanding the current task.

也就是说，“返工”和“新增范围”在默认设计里本来就是两件不同的事。

### 4. There is no built-in GitHub-review-event -> auto-reopen-work bridge

I did not find a built-in mechanism that says:

- when GitHub gets a new review comment,
- automatically move the related Linear issue back to `Todo` or `Rework`.

The available built-in dynamic tool is `linear_graphql`, which helps the agent talk to Linear.
GitHub interactions are mostly handled through repo skills and `gh` CLI.

## Two Practical Solutions / 两种可执行方案

### Solution 1: Reuse the same Linear issue

Use this when:

- it is still the same goal
- the same PR is still relevant
- the work is review follow-up, validation gap, or another polish pass

Recommended handling:

- small or moderate follow-up: move the issue back to `Todo`
- deeper reset or direction change: move the issue to `Rework`

为什么这样更合理：

- 保留一个完整的任务上下文
- 保留原 PR、原 workpad、原验收链路
- 不会把一次正常的 review loop 误拆成多个 task

This should be the default choice in most cases.

### Solution 2: Create a follow-up issue

Use this when:

- the new work is out of the original acceptance scope
- it is a later improvement rather than a merge blocker
- you want independent prioritization and tracking

典型例子：

- “顺手把这个架构再优化一下”
- “这不是 blocker，但值得后续改进”
- “当前 PR 可以先合，这部分单独做更清楚”

In that case:

- create a new Linear issue
- keep the current issue focused
- link the two if needed

## My Recommended Default / 我的默认建议

默认优先级：

1. 同一任务内的 review 修复或不满意重做：继续用原 issue
2. 小修：回 `Todo`
3. 大修：进 `Rework`
4. 明显超出当前任务范围：新建 follow-up issue

一句话总结：

> 先把它看成同一个 issue 的下一轮执行，而不是立刻新开一个 task。

## English Summary

Symphony does support the general rework loop, but not as a fully automatic “GitHub review arrives, Symphony resumes coding” feature.

What it really supports is:

- poll Linear
- run agents for issues that are still in active states
- let the workflow prompt define how review/rework should be handled

That leads to two practical choices:

1. Reuse the same Linear issue and move it back to `Todo` or `Rework` when the PR needs another pass.
2. Create a follow-up issue only when the new work is truly outside the original scope.

For my own workflow, I should treat normal PR review fixes as the same issue by default.
