# Bilingual Note Split Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Split the first mixed-language Symphony note into separate English and Chinese files, then codify the repository rule in both `AGENTS.md` and `CLAUDE.md`.

**Architecture:** Keep the current repository structure. Preserve the English file as the canonical base filename, add a `-cn.md` companion file for Chinese, and update repository instructions so future agents do not mix long-form Chinese and English in the same note.

**Tech Stack:** Markdown, Git, GitHub

---

### Task 1: Split the existing mixed note

**Files:**
- Modify: `notes/2026-03-13-linear-symphony-review-rework.md`
- Create: `notes/2026-03-13-linear-symphony-review-rework-cn.md`

**Step 1: Rewrite the existing note as English-only**

- Keep the current base filename as the English note.
- Remove all Chinese sections and produce a clean English log.

**Step 2: Create the Chinese companion note**

- Add `-cn.md` for the same topic.
- Preserve the same structure and conclusions in Chinese only.

**Step 3: Verify naming consistency**

Run: `find notes -maxdepth 1 -type f | sort`
Expected: both the English note and the `-cn.md` note exist.

### Task 2: Add repo instruction files

**Files:**
- Create: `AGENTS.md`
- Create: `CLAUDE.md`

**Step 1: Write the note-language rule**

- Long-form notes in `notes/` and `cases/` must not mix Chinese and English.
- Use the base filename for English and `-cn.md` for Chinese.

**Step 2: Clarify tool behavior**

- Record that Codex in this environment is definitely reading `AGENTS.md`.
- Keep `CLAUDE.md` aligned as a mirrored instruction file for other agent environments.

**Step 3: Add synchronization guidance**

- When one language version changes materially, review whether the companion file also needs an update.

### Task 3: Update repository indexes

**Files:**
- Modify: `README.md`
- Modify: `notes/README.md`

**Step 1: Update links**

- Replace the single mixed-note entry with separate English and Chinese links.

**Step 2: Add a language convention summary**

- Keep it short.
- Make the file naming rule discoverable from the repository root.

### Task 4: Verify, commit, and push

**Files:**
- Modify: repository Git history only

**Step 1: Run verification**

Run: `git diff --check`
Expected: no whitespace or merge marker errors.

Run: `git status --short`
Expected: only intended doc changes are present.

**Step 2: Commit**

- Use a docs-focused commit message that mentions bilingual note handling.

**Step 3: Push**

- Push the updated `main` branch to `origin`.
