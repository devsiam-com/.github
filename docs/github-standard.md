# Devsiam GitHub Standard

Applies to every repository in `devsiam-com` and to every contributor, human or AI agent.

## 1. Branch strategy by repo type

| Repo type | Branches | Deploy |
|---|---|---|
| Infra (e.g. `devsiam-infra`) | `main` + short-lived feature branches | PR runs `terraform plan` in CI. After merge, the owner runs apply / deploy by hand. Never auto-apply. |
| Kit / product | `main` + feature branches | Release only by pushing a SemVer tag `vX.Y.Z`. |
| App with staging (website, client apps) | `main` (prod) + `develop` (staging) + feature branches | feature → PR into `develop` (auto-deploy staging). Release PR `develop` → `main` (auto-deploy prod). |

Park unfinished work in a Draft PR. Infra and kit repos have no `dev` branch.

## 2. Naming

- **Branch:** `<type>/<monday-item-id>-<short-kebab>`, e.g. `infra/2884945662-terraform-base`. Types: `feat`, `fix`, `infra`, `docs`, `chore`, `hotfix`. Omit the id when there is no monday item.
- **Commits:** [Conventional Commits](https://www.conventionalcommits.org/) — `type(scope): summary`, imperative mood. Breaking changes use `!` or a `BREAKING CHANGE:` footer.
- **Repos:** kebab-case; Devsiam-owned repos start with `devsiam-`.
- **Releases:** [SemVer](https://semver.org/) tags `vMAJOR.MINOR.PATCH`.

## 3. Pull requests and merging

- No direct pushes to `main` or `develop`, including AI agents. Everything goes through a PR.
- One PR per monday task; keep PRs small.
- Use the PR template: What / Why / How tested / monday link / checklist.
- CI must pass: lint, tests, gitleaks. Infra repos also run `terraform fmt`, `tflint`, `terraform plan`, `docker compose config`, `shellcheck`, `yamllint`.
- AI opens the PR; the owner reviews and merges. Approval is not enforced (a solo owner cannot self-approve).
- Merge method depends on the PR type. Head branches are deleted automatically.

  | PR type | Merge method |
  |---|---|
  | feature → `main` (infra / kit) or feature → `develop` (app) | **Squash** |
  | promotion / release: `develop` → `uat` → `main` | **Merge commit** (keeps branch histories in sync) |
  | hotfix → `main`, then back-merge `main` → `develop` | **Merge commit** |

  Never squash a long-lived branch into another: the branches diverge and the next release shows old commits and conflicts.
- monday status: PR opened → `In Review`; merged → `Done`.

## 4. Secrets and safety

- Never commit `.env`, keys, tokens, credentials, client data or PII. Commit `.env.example` with placeholders only.
- Secrets live in GCP Secret Manager or in `.env` on the server.
- Pin image and dependency versions; never use `latest`.

## 5. Files every repo must have

- `README.md`, `AGENTS.md` (shared AI context), `CLAUDE.md` containing `@AGENTS.md`
- `.gitignore`, `.env.example` (if env vars are used)
- `.pre-commit-config.yaml`, `.github/workflows/ci.yml` (start from the *Devsiam CI* workflow template)
- `.github/dependabot.yml`
- Private repos: no open-source license; optional `LICENSE` with `Copyright (c) <year> Devsiam. All rights reserved.`

## 6. Org and repo settings

- Org plan: GitHub Team (rulesets on private repos).
- Org ruleset `devsiam-default-branch` (default branch, all repos): require PR (0 approvals, conversation resolution), block force push and deletion, allowed merge methods **squash + merge commit**, no linear-history rule.
- Org ruleset `devsiam-develop` (branches `develop` / `uat`, added when the first app repo exists): same rules.
- Per-repo ruleset `require-ci`: required status checks for that repo's CI jobs (org rulesets cannot require checks that some repos lack).
- Repo settings, infra / kit repos: squash merge only (PR title + description), always suggest updating PR branches, auto-delete head branches, default branch `main`.
- Repo settings, app repos with `develop`: squash merge **and** merge commits enabled; everything else as above.
