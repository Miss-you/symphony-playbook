# 2026-03-13 Linear + Symphony：当 AI 已完成但 PR 还需要继续修时，该怎么处理？

## 问题

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

## 简短结论

默认建议：

- 如果还是同一个目标、同一个 PR、同一轮验收范围里的返工，不要新建 task。
- 继续使用同一个 Linear issue，把它移回可执行状态，让 Symphony 再跑一轮。
- 只有当问题已经超出原任务范围，或者你希望它作为独立事项长期追踪时，才新建 follow-up issue。

## 我查了哪些代码

我查看了上游 Symphony 仓库里的这些文件：

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

为什么查这些：

- `SPEC.md` 解释产品边界。
- `WORKFLOW.md` 解释真实的任务状态流转。
- `orchestrator.ex` 和 `agent_runner.ex` 解释 Symphony 什么时候会继续工作。
- `linear/adapter.ex` 和 `dynamic_tool.ex` 解释 Linear 侧有哪些能力。
- 仓库技能文件解释 PR feedback 在实践中应该怎么处理。

## 关键发现

### 1. Symphony 是调度器和执行器，不是内建的产品经理

`SPEC.md` 把边界写得很清楚：

- Symphony 主要是调度器/执行器和 issue tracker 的读取方。
- 对 ticket 的写入、PR 评论、以及工作流特有的业务逻辑，预期放在 workflow prompt 和可用工具中。

这意味着 Symphony 不会自动帮你决定：

- 这条 review 要不要接受
- 这次是小修还是重新来一轮
- 什么时候应该拆成一个新任务

这些判断主要依赖 `WORKFLOW.md`。

### 2. 只有 issue 仍处于 active state 时，Symphony 才会继续干活

根据默认的 `elixir/WORKFLOW.md`，active states 是：

- `Todo`
- `In Progress`
- `Merging`
- `Rework`

默认 workflow 把 `Human Review` 当作等待态，而不是执行态。

再结合 `agent_runner.ex` 和 `orchestrator.ex` 可以看出：

- agent 一轮正常结束后，如果 issue 还在 active state，Symphony 会继续下一轮
- 如果 issue 不在 active state，Symphony 就会停止编码

所以一旦 issue 进入 `Human Review`，即使 GitHub 上后来出现新的 review comment，Symphony 也不会因为“看见评论了”就自动恢复编码。必须有人或其他自动化把 issue 调回 active state。

### 3. 默认 workflow 已经定义了两条有用的处理路径

默认 `WORKFLOW.md` 已经把这个问题拆成了两种路径。

#### 路径 A：继续使用同一个 issue，并回到执行态

当 ticket 已经挂着 PR 时，workflow 要求先做完整的 PR feedback sweep：

- 读取 PR 顶层评论
- 读取 inline review comments
- 读取 review summary 和状态
- 把所有有操作性的反馈当成阻塞项，直到修复或明确 push back

同时默认状态流转里也已经包含：

- `Todo`，可以作为重新进入可执行状态的入口
- `Rework`，用于处理 review 驱动的修改和新一轮实现

#### 路径 B：为超出范围的改进创建新的 backlog issue

同一个 `WORKFLOW.md` 还明确说：

- 如果执行过程中发现有意义但超出当前范围的改进，
- 应该创建一个新的 `Backlog` issue，
- 而不是悄悄把当前任务继续膨胀下去。

也就是说，“返工”和“新增范围”在默认设计里本来就是不同的事情。

### 4. 没有内建的 “GitHub review 事件 -> 自动重开执行” 桥接

我没有找到一个内建机制能够做到：

- GitHub 一出现新的 review comment，
- 就自动把对应的 Linear issue 调回 `Todo` 或 `Rework`

我找到的内建动态工具是 `linear_graphql`，它解决的是 agent 如何和 Linear 交互。GitHub 侧的交互依然主要依赖仓库技能和 `gh` CLI。

## 两种可执行方案

### 方案 1：继续使用同一个 Linear issue

适用场景：

- 还是同一个目标
- 还是同一个 PR
- 本质上是 review 后续修复、验证补齐，或者再做一轮打磨

推荐做法：

- 小到中等规模的继续修改：把 issue 调回 `Todo`
- 方向变了、需要更大重置：把 issue 调到 `Rework`

为什么通常这样更合理：

- 保留一个完整任务上下文
- 保留原 PR、原 workpad、原验收链路
- 不会把一次正常的 review loop 拆成多个 task

这应该是大多数情况下的默认选择。

### 方案 2：新建一个 follow-up issue

适用场景：

- 新工作已经超出原本的验收范围
- 它更像后续优化，而不是当前 PR 的合并阻塞项
- 你希望它独立排期和追踪

典型例子：

- “既然改到这里了，顺手把架构也优化一下”
- “这不是 blocker，但后续值得改进”
- “当前 PR 可以先合，这部分拆出去更清楚”

这时应该：

- 新建一个 Linear issue
- 保持当前 issue 聚焦
- 必要时把两个 issue 关联起来

## 我的默认建议

默认优先级：

1. 同一任务内的 review 修复或再做一轮，继续用原 issue。
2. 小修：回 `Todo`。
3. 大修：进 `Rework`。
4. 明显超出当前任务范围：新建 follow-up issue。

一句话总结：

> 优先把它看成同一个 issue 的下一轮执行，而不是立刻新开一个 task。
