# Repository Instructions

These instructions apply to Codex and other repository-aware coding agents working in this repository.

## Primary Rule for This Environment

- In the current Codex environment, `AGENTS.md` is the repository instruction file that is definitely being read and applied.
- Treat this file as the primary source of repository-specific behavior for Codex.
- `CLAUDE.md` must remain aligned with this file so different agent environments do not drift.

## Language Rules for Notes

- Long-form notes in `notes/` and `cases/` must not mix English and Chinese in the same file.
- Use the base filename for the English note.
- Use the same filename plus `-cn.md` for the Chinese note.
- Example:
  - English: `notes/2026-03-13-linear-symphony-review-rework.md`
  - Chinese: `notes/2026-03-13-linear-symphony-review-rework-cn.md`

## Update Rules

- When creating a new bilingual note, create two files instead of mixing languages.
- When materially changing one language version, review whether the companion file also needs to be updated.
- Keep indexes and cross-links consistent when adding or renaming notes.

## Scope Exception

- Short bilingual summaries are allowed in top-level navigation documents such as `README.md` when they improve discoverability.
- This exception does not apply to long-form note content.
