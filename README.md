# ActionsQuest — Hands-On Exercises

Build and ship real GitHub Actions workflows from scratch.  
Each exercise maps to a mission in the ActionsQuest game, but here you write the code yourself.

---
.
## Setup

### 0. prerequisites

- git installed

Windows, Mac, Ubuntu

### 1. Fork this repository

Go to the top of the page and click **Fork**.  
Clone your fork locally:

```bash
git clone https://github.com/El-leandr0/github-meetup-exercises.git
cd github-meetup-exercises
```

## Exercise 1 — Hello, Pipeline

**File to create:** `.github/workflows/01-hello-pipeline.yml`

### How to trigger and verify

1. create an empty file on `.github/workflows/` and give it a name (i.e `01-hello-pipeline.yml`)
2. copy yaml from Actionsquest an paste it on the file
3. Push the file to your repo:
  - git add .
  - git commit -m "<your message>"
  - git push
4. On GitHub, go to **Actions → hello-pipeline → Run workflow**.
5. Open the run and expand the **Print message** step.
6. You should see `Hello, Actions!` in the log.

### Key concept

`workflow_dispatch` lets anyone run a workflow on demand from the GitHub UI or via the API. It's the simplest way to test a new workflow before wiring up automatic triggers.

---

## Exercise 2 — Guard the Gate

**File to create:** `.github/workflows/02-guard-the-gate.yml`

### How to trigger and verify

1. create an empty file on `.github/workflows/` and give it a name (i.e `02-guard-the-gate.yml`)
2. copy yaml from Actionsquest an paste it on the file
3. Push the file to your repo:
  - git add .
  - git commit -m "<your message>"
  - git push


4. The workflow fires immediately on that push.
5. On GitHub, open **Actions → ci** and confirm all steps are green.
6. Try:
  - create a new branch, add a file and push. verify if the trigger works.
  - breaking a test locally, push, and watch the job fail. verify if the workflow fails

7. return to main branch:
  - git checkout main

### Key concept

CI (Continuous Integration) means tests run automatically on every push. No more "it worked on my machine." This is the foundation of healthy engineering culture.

---

## Exercise 3 — The Night Shift

**File to create:** `.github/workflows/03-night-shift.yml`

1. create an empty file on `.github/workflows/` and give it a name (i.e `03-night-shift.yml`)
2. copy yaml from Actionsquest an paste it on the file
3. Push the file to your repo:
  - git add .
  - git commit -m "<your message>"
  - git push

### Key concept

GitHub Actions wakes up at the scheduled time and runs your pipeline on GitHub-hosted infrastructure. No servers to manage, no cron jobs to configure on a VM.

---

## Exercise 4 — No Secrets Shared

**File to create:** `.github/workflows/04-no-secrets-shared.yml`


### How to trigger and verify

0. On the github repo Go to **Settings →, under security and quality, Secrets and variables → Actions** and create:
   - `API_TOKEN` — any string (e.g. `test-token-123`)
1. create an empty file on `.github/workflows/` and give it a name (i.e `04-no-secrets-shared.yml`)
2. copy yaml from Actionsquest an paste it on the file
3. Push the file to your repo:
  - git add .
  - git commit -m "<your message>"
  - git push

4. Go to the actions tab o github repo and check the the workflow. In the **Authenticate** step you should see `Token loaded (length: ...)` but the actual token value will appear as `***` anywhere it is logged.

### Key concept

GitHub encrypts secrets at rest and automatically masks their values in all log output. They are never exposed in repository code or PR diffs.

---

## Exercise 5 — Branch Guard

**File to create:** `.github/workflows/05-branch-guard.yml`

### How to trigger and verify

1. create an empty file on `.github/workflows/` and give it a name (i.e `05-branch-guard.yml`)
2. copy yaml from Actionsquest an paste it on the file
3. Push the file to your repo:
  - git add .
  - git commit -m "<your message>"
  - git push → the workflow run is trigered.

4. Now lets test a push in a diferent branch:
  - git checkout -b feature/test-branch-filter
  - change any line on the README.md.
  - git add .
  - git commit -m "<your message>"
  - git push --set-upstream origin feature/test-branch-filter -> the workflow 05 will not apear on the console
  - git checkout main
  
### Key concept

Branch filters make CD safe. Feature branches trigger CI, only `main` triggers deployment. This "push to deploy" pattern is how most teams ship software.

---

## Exercise 7 — Ship the Box

**File to create:** `.github/workflows/07-ship-the-box.yml`

### How to trigger and verify


1. create an empty file on `.github/workflows/` and give it a name (i.e `07-ship-the-box.yml`)
2. copy yaml from Actionsquest an paste it on the file
3. Push the file to your repo:
  - git add .
  - git commit -m "<your message>"
  - git push
4. Open **Actions → docker-build** and confirm all steps complete.
5. Go to your GitHub profile → **Packages** to see the pushed image.

> **Note:** If your repository is private, the package will be private by default. You can change visibility in the package settings.

### Key concept

`docker/setup-buildx-action` enables BuildKit, the modern Docker build engine. It unlocks parallel build stages, better caching, and cross-platform builds. Always initialise it before the build step.

---

## Exercise 8 — Cache Me If You Can

**File to create:** `.github/workflows/08-cache-me-if-you-can.yml`

1. create an empty file on `.github/workflows/` and give it a name (i.e `08-cache-me-if-you-can.yml`)
2. copy yaml from Actionsquest an paste it on the file
3. Push the file to your repo:
  - git add .
  - git commit -m "<your message>"
  - git push
4. Open **Actions → docker-build** and confirm all steps complete.
5. Push again
6. Both 07 and 08 workflows are trigered. compare the diference between 08 and 07 build after the second push on Actions UI.

### Why `mode=max`?

| Setting | What it caches | Benefit |
|---|---|---|
| `mode=min` | Final image stage only | Small speedup |
| `mode=max` | **All intermediate layers** | Large speedup — the expensive `pip install` layer is cached |

### Key concept

Two lines cut build time by up to 90%. `type=gha` stores Docker layer hashes in GitHub's built-in cache service. `mode=max` caches every intermediate layer so even partial rebuilds benefit.

---

## Exercise 9 — Lock the Gate

**File to create:** `.github/workflows/9-lock-the-gate-ci.yml`  

### Part A — Create the required status check workflow

1. create an empty file on `.github/workflows/` and give it a name (i.e `09-lock-the-gate-ci.yml`)
2. copy yaml from Actionsquest an paste it on the file
3. Push the file to your repo:
  - git add .
  - git commit -m "<your message>"
  - git push

Push this file. The job name (`test`) is what you reference as a required status check.

### Part B — Configure branch protection rules

Go to **Settings → Branches → Add branch ruleset** (or Add rule), branch name pattern: `main`.

- on "Enforcement status" select Active.
- on "Target branches" select "Add target">"include by pattern">write "main" (without "")
- Enable the following:

| Setting | Why |
|---|---|
| ✅ Require a pull request before merging | Blocks direct pushes to `main` |
| ✅ Require status checks to pass (add `test`) | CI must be green before merge |
| ✅ Block force pushes | History rewriting forbidden |
| ❌ Do not enforce for administrators | Admins must follow the same rules |




### How to verify

1. Try to push directly to `main` again:
  - make some change to the README.md
  - git add .
  - git commit -m "<your message>"
  - git push -> will be rejected

2. Create a PR instead. Open it and confirm the `test` status check appears and must pass before merge is enabled.

### Key concept

Branch protection rules are enforced by GitHub itself. Your CI workflow becomes a "required check" — a PR cannot be merged unless the `test` job passes. This is the contract between your team and your codebase.

---

## Exercise 10 — The Production Gate

### Part A — Configure the environments

Go to **Settings → Environments** and create three environments:

**`dev`**
- No required reviewers
- No wait timer
- No deployment branch restrictions

**`staging`**
- No required reviewers
- Deployment branches: `main` only

**`production`**
- Required reviewers: add yourself
- Wait timer: 5 minutes
- Deployment branches and tags > selected branches and tags: `main` only
- Check **Prevent self-review** if you want strict separation

### Part B — Create the workflow

**File to create:** `.github/workflows/10-production-gate.yml`  

1. after exercise 9 direct push to main are disabled. the push must be triggered by a PR (pull request) :
  - git checkout -b feature-ex11
2. create an empty file on `.github/workflows/` and give it a name (i.e `10-production-gate.yml`)
3. copy yaml from Actionsquest an paste it on the file
4. Push the file to your repo:
  - git add .
  - git commit -m "<your message>"
  - git push --set-upstream origin feature-ex11

5. open a new pull request
6. aprove the pull request -> will create a new commit on main
7. Watch **Actions → deploy**:
   - `deploy-dev` runs immediately.
   - `deploy-staging` runs after dev succeeds.
   - `deploy-production` **pauses** and shows a yellow "Waiting for review" badge.
8. Open the paused run, click **Review deployments**, select `production`, and approve.
9. After the 5-minute wait timer, the production job runs.

### Key concept

The `environment:` key in a job connects it to your Environment settings. No code changes needed — GitHub intercepts the job, notifies required reviewers, and waits for approval before the runner even starts. This is how you put a human in the loop without writing any custom logic.

---

