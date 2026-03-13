# Symphony Playbook Bootstrap Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Bootstrap a lightweight note-taking repository for Symphony workflow learnings.

**Architecture:** Keep the repository intentionally small. Use a dated note as the source of truth, then add a root README and minimal supporting directories so future entries remain easy to append.

**Tech Stack:** Markdown, Git

---

### Task 1: Create the repository skeleton

**Files:**
- Create: `.gitignore`
- Create: `notes/README.md`
- Create: `cases/README.md`
- Create: `templates/note-template.md`

**Steps:**

1. Create the repository directory under `~/Documents/GitHub/symphony-playbook`.
2. Initialize a Git repository.
3. Add a minimal `.gitignore` for local clutter.
4. Add empty-but-purposeful index files for `notes/` and `cases/`.

### Task 2: Write the root README

**Files:**
- Create: `README.md`

**Steps:**

1. Explain the repository positioning in both Chinese and English.
2. Document the intended structure.
3. Add an index entry for the first note.
4. Keep the tone practical rather than polished.

### Task 3: Add the first real note

**Files:**
- Create: `notes/2026-03-13-linear-symphony-review-rework.md`

**Steps:**

1. Preserve the original user question in plain language.
2. Record which Symphony files were inspected.
3. Summarize the key findings from `SPEC.md`, `WORKFLOW.md`, `orchestrator.ex`, and `agent_runner.ex`.
4. Write down the two practical solutions:
   - reuse the same issue
   - create a follow-up issue
5. End with a default recommendation.

### Task 4: Add a reusable template

**Files:**
- Create: `templates/note-template.md`

**Steps:**

1. Keep the template lightweight.
2. Include sections for the original question, inspected code, options, and final conclusion.
3. Make it easy to reuse for future Symphony workflow notes.
