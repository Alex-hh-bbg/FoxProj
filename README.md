# FoxProj

Controlled GitHub Actions task dispatch for Gemini CLI and the official Google Antigravity CLI.

Security: manual workflow dispatch only; no automatic execution from public issues. Changes require pull request review and are never auto-merged.

## Existing Gemini workflow

The existing `.github/workflows/gemini-task.yml` workflow is unchanged.

1. Add repository Actions secret `GEMINI_API_KEY` (never commit it).
2. Review `.github/workflows/gemini-task.yml` and enable Actions.
3. Open an Issue describing a task, then manually dispatch the workflow with its Issue number.
4. Review the generated artifact and Actions logs.

## Antigravity workflow

`.github/workflows/antigravity-task.yml` uses Google's official `agy` binary directly in headless mode. It does not pass Antigravity OAuth tokens to another agent or service.

### One-time Mac runner setup

1. Register a repository-scoped, dedicated macOS self-hosted runner under a dedicated macOS user.
2. Add the custom runner label `foxproj-antigravity`. Do not reuse this label for general or pull-request workflows.
3. Install the official Antigravity CLI from [Google's Antigravity documentation](https://antigravity.google/docs/getting-started), plus GitHub CLI and `jq`.
4. While signed in as the same macOS user that runs the GitHub runner service, launch `agy` once and complete Google's browser OAuth flow with the intended personal Google AI Pro account. Do not store or copy login credentials into GitHub secrets.
5. In `agy /config`, leave **Use AI Credits** off. The workflow also temporarily forces `useG1Credits: false` and restores the prior settings afterward.
6. In repository Actions settings, allow workflows to request read/write `GITHUB_TOKEN` permissions and to create pull requests. Do not enable auto-merge.

The runner must have access to that macOS user's login keychain. If the runner is installed as another user or cannot access the login keychain, the workflow will fail authentication; do not work around this by exporting OAuth tokens.

### Running a task

1. Create an open Issue authored by `Alex-hh-bbg`. Treat the Issue as public and do not include secrets.
2. From the Actions tab, select **Antigravity Issue Task (manual, draft PR)** on `main`.
3. Enter the Issue number and run the workflow as `Alex-hh-bbg`.
4. Review the resulting Draft PR. The workflow never marks it ready, merges it, or enables auto-merge.

### Guardrails

- The job runs only for an owner dispatch from `main` on a runner carrying `self-hosted`, `macOS`, and `foxproj-antigravity`.
- The Antigravity process receives no GitHub token.
- Official headless mode runs with the terminal sandbox and scoped deny rules.
- AI credit overages are disabled for the run.
- Changes to workflows, local actions, `.gitmodules`, and symlinks are rejected.
- Proposed changes are capped at 100 files and a 2 MB staged patch.
- Publishing creates only a Draft PR; repository tests should be reviewed and run separately.

Official references: [headless/CI mode](https://antigravity.google/docs/cli/headless/), [authentication](https://antigravity.google/docs/install), [permissions](https://antigravity.google/docs/permissions?tab=cli), [AI credits](https://antigravity.google/docs/cli/credits), and [terms](https://www.antigravity.google/terms).
