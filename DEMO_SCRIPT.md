# Team Demo Script (~10-15 min)

Goal: show the loop end-to-end, live, so the team sees it's just "branch,
edit, test, PR, merge" — nothing heavier than that.

## 0. Set the frame (1 min)
Say out loud: "No one edits in Agent Builder directly anymore. Everything
goes through this repo. I'll show the whole loop live — branch to merged."

## 1. Show the repo structure (1 min)
Open the repo, point at:
- `README.md` — the loop, in one page
- `.github/workflows/` — the two automations
- Branch protection settings screen — "up to date" + required status check

## 2. Live: make a change (3-4 min)
```bash
git checkout main
git pull
git checkout -b feature/demo-change
# make a small visible edit in the Agent Script file
sf agent preview start --authoring-bundle <name> --use-live-actions
sf agent preview send --utterance "..."   # show local test passing
git add .
git commit -m "demo: small change for walkthrough"
git push -u origin feature/demo-change
```
Open the PR in GitHub. Point out:
- PR template auto-filled
- CODEOWNERS auto-tagged the team
- `validate` check kicks off automatically

## 3. Show validation running (1-2 min)
Switch to the Actions tab, show `validate.yml` running live, then passing.
Say: "This is the only gate. No one has to review it unless they want to."

## 4. Merge (1 min)
Click merge. Point out:
- Merge posts to team chat automatically (if integration is set up)
- Branch auto-deletes

## 5. Show the "sequential merge" protection (2-3 min)
This is the part worth demoing concretely, since it answers "how do we avoid
stepping on each other":
- Open two branches ahead of time (prep before the demo) both based on the
  same old commit of `main`.
- Merge one.
- Show the second PR flip to "out of date, must update branch."
- Click "Update branch" to show how trivial the resolution is.

## 6. Show the deploy step (1-2 min)
Go to Actions → "Create Version and Deploy to Dev" → Run workflow →
type `deploy`. Explain this is the *only* manual, team-coordinated step —
everything before it is self-serve.

## Closing line
"Every dev's day-to-day is: branch, edit, test locally, PR, merge when green.
The only thing we coordinate as a team is this one deploy button."
