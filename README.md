# FoxProj

Controlled GitHub Actions task dispatch for Gemini CLI.

Security: manual workflow dispatch only; no automatic execution from public issues. Changes require pull request review and are never auto-merged.

## Setup
1. Add repository Actions secret `GEMINI_API_KEY` (never commit it).
2. Review `.github/workflows/gemini-task.yml` and enable Actions.
3. Open an Issue describing a task, then manually dispatch the workflow with its Issue number.
4. Review the generated draft PR and Actions logs before merging.
