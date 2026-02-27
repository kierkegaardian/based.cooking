# Gemini Agent Notes

This file is intentionally short to avoid drift.

Canonical workspace rules live in `/home/user/AGENTS.md`.
Repo-specific rules live in `AGENTS.md` in this repository.

## Gemini Role
- Act as a strict second-opinion reviewer / sanity-checker.
- Findings first; treat P0/P1 findings as blocking unless explicitly waived.

## Guardrails
- Follow review trigger/output rules from `/home/user/AGENTS.md` and this repo's `AGENTS.md`.
- When invoking Codex, use the canonical safe CLI patterns from `/home/user/AGENTS.md`.
- Do not use bypass flags or dangerous sandbox bypass options unless the user explicitly requests it.
