# AgentforceVersionControl

Lean GitHub-based workflow for a 3-4 person team building Agentforce agents
against a single dev org. No one edits directly in Agent Builder — all
changes flow through this repo.

## The Loop (what every dev does)

1. **Pull `main`** — get the latest active version script.
2. **Create a feature branch** — `git checkout -b feature/<short-description>`.
3. **Edit locally** — make the change to the Agent Script file in the authoring bundle (VS Code with the Agentforce DX extension, or any editor).
4. **Test locally** — `sf agent preview start --authoring-bundle <name> --use-live-actions`, then `sf agent preview send --utterance "..."`, to test against the dev org before committing.
5. **Commit and push** — open a PR into `main`.
6. **Automated validation runs** — GitHub Actions runs `afdx validate` on the PR automatically.
7. **Update branch if needed** — if `main` moved since you branched, GitHub will
   ask you to update. Click "Update branch" (or `git merge origin/main`).
8. **Merge** — self-merge once validation passes. Review is optional, not required.
9. **Post "merging now" in team chat** — one line, right before you merge. Optional
   once GitHub → Slack/Teams integration is on, since merges post automatically.

## Promoting to the Dev Org (team-coordinated step)

Once the team agrees the current state of `main` is the next active version:

1. Anyone with access goes to **Actions → Create Version and Deploy to Dev**.
2. Runs it manually (`workflow_dispatch`), typing `deploy` to confirm.
3. The workflow runs `afdx create-version` then `afdx deploy --target dev`.

This step stays manual and deliberate — it's the one moment the whole team
should be aware of, since it changes the shared dev org.

## Underlying CLI Commands (Salesforce CLI `sf agent`)

| Step | Command |
|---|---|
| Local live testing | `sf agent preview start --authoring-bundle <name> --use-live-actions` / `sf agent preview send --utterance "..."` |
| Validate (runs in `validate.yml` on every PR) | `sf agent validate authoring-bundle --api-name <name> --target-org devOrg --json` |
| Publish / create version (runs in `deploy.yml`, manual) | `sf agent publish authoring-bundle --api-name <name> --target-org devOrg` |

`sf agent publish authoring-bundle` is the CLI equivalent of clicking "Commit
Version" in Agentforce Builder — it compiles the Agent Script file, then
creates or updates the `Bot`/`BotVersion`/`GenAiX` metadata in the org.

## What Enforces the "Sequential Merge" Rule

Branch protection on `main` has **"Require branches to be up to date before
merging"** turned on. This means:

- Dev A merges → `main` advances.
- Dev B's open PR is automatically flagged "out of date."
- Dev B must update their branch before they're allowed to merge.
- GitHub does this automatically — no manual chat coordination required for it.

## What's Required vs Optional on PRs

| Check | Status |
|---|---|
| Branch up to date with `main` | **Required** (blocks merge) |
| `validate.yml` status check passes | **Required** (blocks merge) |
| Human PR review | **Optional** (CODEOWNERS auto-tags the team for visibility, doesn't block) |

## Repo Structure

```
.github/
  pull_request_template.md   # shows up automatically on every new PR
  CODEOWNERS                 # auto-tags the team on PRs, non-blocking
  workflows/
    validate.yml              # runs on every PR — compiles/validates the bundle
    deploy.yml                # manual trigger — creates version, deploys to dev org
README.md                    # this file
SETUP.md                     # one-time GitHub settings a repo admin needs to click through
```

## One-Time Setup

See [SETUP.md](./SETUP.md) — branch protection rules and integrations that
have to be configured in the GitHub UI (can't be done via committed files).
