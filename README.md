# OpenAI Symphony Playbook

Setup notes and field logs for building, understanding, and using OpenAI Symphony.

用于记录我在搭建、理解和使用 OpenAI Symphony 过程中的真实问题、踩坑、工作流判断和后续沉淀。

## Positioning / 仓库定位

- `Logbook first, playbook later`
- Focus on real setup notes before abstract best practices.
- Start from dated notes, then distill repeated lessons into reusable cases.
- 先记录真实搭建过程，再慢慢提炼方法。
- 先按时间记录，再把反复出现的问题提炼成稳定方法。

## Structure / 目录结构

- `notes/`
  - Dated research notes and workflow logs.
  - 按日期记录的问题、分析和结论。
- `cases/`
  - Distilled reusable cases and patterns.
  - 后续沉淀出的可复用案例和方法。
- `templates/`
  - Lightweight templates for new notes.
  - 新日志的轻量模板。
- `docs/plans/`
  - Small design and bootstrap planning docs for this repo itself.
  - 这个仓库自身的设计和初始化计划文档。

## Current Index / 当前索引

### Notes / 日志

- [2026-03-13 Linear + Symphony: What should I do when AI is "done" but the PR still needs review-driven fixes?](notes/2026-03-13-linear-symphony-review-rework.md) `EN`
- [2026-03-13 Linear + Symphony：当 AI 已完成但 PR 还需要继续修时，该怎么处理？](notes/2026-03-13-linear-symphony-review-rework-cn.md) `CN`
- Keywords: `Linear`, `Symphony`, `Human Review`, `Todo`, `Rework`, `PR feedback`

## Language Convention / 语言约定

- Long-form notes should not mix English and Chinese in the same file.
- Use the base filename for English.
- Use the same filename plus `-cn.md` for the Chinese companion.
- Root-level summaries may stay briefly bilingual when useful.

- 长篇笔记不要在同一个文件里中英混写。
- 英文使用基础文件名。
- 中文使用同名加 `-cn.md`。
- 仓库根目录的简短说明在必要时可以保留双语。

## Working Method / 记录方式

1. Capture the exact question first.
2. Record which code and docs were inspected.
3. Write the conclusion in plain language.
4. Only distill into `cases/` when the same pattern shows up repeatedly.

1. 先写清楚原始问题。
2. 记下查过哪些代码和文档。
3. 用简单语言写出结论。
4. 只有当问题重复出现，才把它沉淀到 `cases/`。

## Near-Term Direction / 近期方向

- Keep notes short but concrete.
- Prefer real workflow decisions over abstract theory.
- Add English summaries so the notes remain shareable.

- 先保持日志简短但具体。
- 重点记录真实工作流决策，不追求一开始就抽象成体系。
- 每篇保留英文摘要，方便后续分享或整理。
