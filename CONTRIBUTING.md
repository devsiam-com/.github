# Contributing to Devsiam repositories

Full rules: [docs/github-standard.md](https://github.com/devsiam-com/.github/blob/main/docs/github-standard.md)

1. Branch from the default branch: `<type>/<monday-item-id>-<short-kebab>` (types: feat, fix, infra, docs, chore, hotfix).
2. Commit with Conventional Commits: `type(scope): summary`.
3. Open a PR using the template. Link the monday task.
4. CI must be green (lint, tests, gitleaks). No direct pushes to `main` or `develop`, including AI agents.
5. Squash merge. The branch is deleted automatically.
