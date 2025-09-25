# Trunk-Based Development Sandbox
This repository is a sandbox to practice and document trunk-based development (TBD) workflows. It focuses on keeping a single shared trunk, `main`, and using short-lived branches, feature flags, and continuous integration to safely ship small, frequent changes.

## Purpose

The goal of this repo is to:

- Explain the core ideas behind trunk-based development.
- Provide practical guidance for branching, CI, releasing, and collaboration.
- Offer example commands and patterns for learning.

## Key Principles

- Single shared trunk: prefer a single production branch (usually named `main`) that is always releasable.
- Short-lived branches: create small feature branches for short durations (hours to a few days) and merge back quickly. Some smaller teams may even commit directly to the trunk.
- Small, frequent commits: keep changes incremental so they are easy to review and revert if necessary.
- Continuous integration: every change to trunk should trigger automated build, test, and quality checks.
- Feature flags/toggles: use feature flags to integrate incomplete features safely behind runtime switches.

## Branching Strategy

Trunk-based development minimizes long-lived branches. Typical branch types you'll see:

| Branch     | Purpose |
|------------|---------|
| `main`     | Single shared trunk; always kept in a releasable state |
| `[topic]/*`  | Short-lived branches for a focused change (feature/fix), merged quickly |
| `release-*` (optional) | Very short-lived release stabilization branches created from `main` when needed |

### Recommended naming for short-lived branches:

```text
topic/1234-short-description
topic/fix-login-timeout
```

### Branch lifetime expectations:

- Hours to a few days for topic branches.
- Merge to `main` as soon as the change is review-ready and CI is green.

## Typical Workflow

1. Update your local trunk and create a short-lived branch:

```bash
git checkout main
git pull origin main
git checkout -b feature/update-primary-color
```

2. Make small commits and push frequently:

```bash
git add .
git commit -m "feat: update primary color across app"
git push -u origin feature/update-primary-color
```

3. Open a pull request targeting `main`. Keep PRs small and focused.

4. CI runs automatically. Address feedback and failing checks quickly.

5. Merge to `main` when CI is green and review complete. Prefer fast-forward or squash merges to keep history tidy.

6. Delete the topic branch after merge.

## CI and Quality Gates

- Protect `main` with branch protection rules: require passing CI, code review approvals, and signed commits if needed.
- Run a fast test suite on commits and a fuller integration test suite on PRs or trunk.
- Use automated checks for linting, security scanning, dependency updates, and static analysis.

## Releases and Deployments
There are different strategies for releasing, and different approaches to flagging (including dark launches). The best workflow for a team depends on their product, processes, and priorities.

## Best Practices

- Keep changes small and focused. Large refactors should be split into multiple incremental PRs.
- Keep CI fast and reliable; flaky tests slow the flow and reduce trust in automation.
- Prefer code reviews that focus on correctness, readability, and risks rather than large stylistic changes.
- If a change is risky, use a feature flag or a canary rollout rather than blocking trunk integration.
- Automate rollbacks and implement observability to detect issues quickly.

## Example Commands

```bash
# Start a short-lived topic branch
git checkout main
git pull origin main
git checkout -b feat/1234-add-tertiary-button

# Work, commit, and push
git add . && git commit -m "feat(ui): add tertiary button variant"
git push -u origin feat/1234-add-tertiary-button

# Re-base onto trunk if needed to keep history clean
git fetch origin
git rebase origin/main

# Merge flow: open PR, get CI green, then merge into main

# Clean up after merge
git branch -d feat/1234-add-tertiary-button
git push origin --delete feat/1234-add-tertiary-button
```
