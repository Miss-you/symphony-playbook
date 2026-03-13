# Bilingual Note Template

Use this template when one topic needs both English and Chinese notes.

## File Naming Convention

- English note: `notes/YYYY-MM-DD-topic-name.md`
- Chinese note: `notes/YYYY-MM-DD-topic-name-cn.md`
- Do not mix long-form English and Chinese in the same note file.
- When one version changes materially, review whether the companion file also needs an update.

## Suggested Workflow

1. Create the English note first with the base filename.
2. Create the Chinese companion with the `-cn.md` suffix.
3. Keep the structure aligned across both files.
4. Update `notes/README.md` when adding a new note pair.

## English Note Template

```md
# YYYY-MM-DD Topic

## Question

- What exactly happened?
- What decision am I trying to make?

## Context

- Tooling:
- Repo:
- Workflow state:

## What I Looked At

- Docs:
- Code:
- Logs:

## Key Findings

- Finding 1
- Finding 2

## Options

### Option A

- When to use:
- Why:

### Option B

- When to use:
- Why:

## Conclusion

- My default recommendation:
- Open questions:
```

## Chinese Note Template

```md
# YYYY-MM-DD 主题

## 问题

- 具体发生了什么？
- 我到底在判断什么？

## 背景

- 工具环境：
- 仓库：
- 工作流状态：

## 我查了哪些东西

- 文档：
- 代码：
- 日志：

## 关键发现

- 发现 1
- 发现 2

## 备选方案

### 方案 A

- 适用场景：
- 原因：

### 方案 B

- 适用场景：
- 原因：

## 结论

- 我的默认建议：
- 仍未确定的问题：
```
