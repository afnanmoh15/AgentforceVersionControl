# Team Demo Script (~12-15 min)

Goal: show the loop end-to-end, live, so the team sees it's just "branch,
edit, test, PR, merge" — nothing heavier than that. Every step below has
already been run for real against this repo — this isn't a mockup.

## 0. Set the frame (1 min)
Say out loud: "No one edits in Agent Builder directly anymore. The
`Version_Control.agent` script in this repo is the single source of truth.
I'll show the whole loop live — branch to merged to deployed."

## 1. Show the repo structure (1-2 min)
Open the repo, point at:
- `README.md` — the loop, in one page, plus the exact `sf agent` commands
- `force-app/.../Version_Control.agent` — the one file that actually matters
- `.github/workflows/` — `validate.yml` (every PR) and `deploy.yml` (manual)
- Branch protection settings screen — required PR, required `validate` check,
  "up to date" enforcement, review NOT required

## 2. Live: make a change (3-4 min)
```bash
git checkout main
git pull
git checkout -b feature/demo-change
# make a small visible edit in Version_Control.agent
sf agent validate authoring-bundle --api-name Version_Control --target-org <your-alias>
git add .
git commit -m "demo: small change for walkthrough"
git push -u origin feature/demo-change
```
Open the PR in GitHub. Point out:
- PR template auto-filled
- CODEOWNERS auto-tagged the team
- `validate` check kicks off automatically — this runs the exact same
  command you just ran locally, against the dev org, via a stored secret

## 3. Show validation running (1-2 min)
Switch to the Actions tab, show `validate` running live, then passing
("Required" tag visible next to the check). Say: "This is the only gate.
No one has to review it unless they want to — validation replaces review
as the safety net."

## 4. Merge (1 min)
Click merge. Point out:
- Branch auto-deletes
- (If chat integration is set up) merge posts to the team channel automatically

## 5. Show the "sequential merge" protection (2-3 min)
This is the part worth demoing concretely, since it answers "how do we avoid
stepping on each other":
- Open two branches ahead of time (prep before the demo) both based on the
  same starting commit of `main`.
- Merge one.
- Refresh the second PR — show it flip to "This branch is out-of-date with
  the base branch," merge button greyed out.
- Click "Update branch" — show `validate` automatically re-running on the
  merged result, then the merge button unlocking.
- Say: "GitHub enforces this mechanically. No chat pings, no 'wait, did you
  pull latest?' — the merge button itself won't let you skip this."

## 6. Show the deploy step (2 min)
Go to Actions → "Create Version and Deploy to Dev" → Run workflow →
type `deploy`. While it runs, explain:
- This is the *only* manual, team-coordinated step — everything before it
  is self-serve
- It runs `sf agent publish authoring-bundle`, which creates a real new
  version in the org from whatever is currently on `main`
- It deliberately does **not** pull anything back into the repo afterward
  (`--skip-retrieve`) — the script is the source of truth, the org's
  generated metadata is just a byproduct, fully reproducible any time by
  publishing again

Once it finishes green, switch to Agent Builder and show the new version
live — closing the loop from "edited a text file" to "agent updated in
the org," entirely through git.

## Closing line
"Every dev's day-to-day is: branch, edit, validate locally, PR, merge when
green — no waiting on a reviewer. The only thing we coordinate as a team is
one deploy button, and even that's a single command."