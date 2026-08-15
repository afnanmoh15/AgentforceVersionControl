# One-Time Bootstrap Setup

Confirmed working end-to-end against this exact repo. Should take about
20-30 minutes total, including the CLI install.

## 1. Salesforce DX Project Files

Confirm these exist at the repo root (already added if you're reading this
from the repo):
- `sfdx-project.json`
- `.forceignore`

Without `sfdx-project.json`, none of the `sf agent` CLI commands below will
work — they need a valid DX project to find the authoring bundle.

## 2. Install the Salesforce CLI + Agent Plugin (each dev, locally)

```powershell
npm install --global @salesforce/cli
sf plugins install @salesforce/plugin-agent
sf --version
sf plugins   # confirm @salesforce/plugin-agent shows up
```

## 3. Authenticate to the Dev Org (each dev, locally)

```powershell
sf org login web --alias devOrg --set-default
sf org display --alias devOrg   # confirm org ID / instance URL look right
```

## 4. Branch Protection on `main`

Settings → Branches → Add classic branch protection rule → branch name
pattern `main`

Turn on:
- [ ] **Require a pull request before merging** — leave **"Require
      approvals" unchecked** (review stays optional; validation is the gate)
- [ ] **Require status checks to pass before merging**
  - You can't select `validate` yet — it needs to run at least once first.
    Come back after step 7 and add it.
- [ ] **Require branches to be up to date before merging**
  - This is what enforces sequential merges automatically

## 5. Add Teammates

Settings → Collaborators → add the other 2-3 devs with **Write** access.

## 6. Add the Repo Secret (for validate.yml / deploy.yml to authenticate)

```powershell
sf org auth show-sfdx-auth-url --target-org devOrg
```

Copy the `force://...` output (**don't paste this anywhere insecure — it's
a live credential**).

Settings → Secrets and variables → Actions → New repository secret
- Name: `SF_DEV_AUTH_URL`
- Value: the `force://...` string

> The bundle API name (`Version_Control` in this repo) is already set in
> `validate.yml` and `deploy.yml`. If you fork this for a different agent,
> update `--api-name` in both files to match your bundle's folder name
> under `force-app/main/default/aiAuthoringBundles/`.

## 7. First PR to Confirm validate.yml Works

- [ ] Create a throwaway branch, make a trivial change to the `.agent` file,
      open a PR
- [ ] Confirm `validate` runs automatically in the Actions tab and passes
- [ ] Merge it, delete the branch

## 8. Lock In the Required Check

Now that `validate` has run once, go back to Settings → Branches → edit the
`main` rule → search for `validate` in the status checks box → select it →
Save.

## 9. GitHub ↔ Chat Integration (optional)

```
/github subscribe afnanmoh15/AgentforceVersionControl
```

Posts merges to `main` automatically in Slack/Teams.

## 10. Confirm the Deploy Workflow

Actions → "Create Version and Deploy to Dev" → Run workflow → type `deploy`.
Should complete in well under a minute with 4 green steps: checkout, install
CLI, authenticate, publish. Nothing else should happen — no files change
locally afterward (`--skip-retrieve` is intentional; see README for why).

Once this checklist is done, the loop in [README.md](./README.md) is live —
send that to the team, and use [DEMO_SCRIPT.md](./DEMO_SCRIPT.md) to walk
them through it.