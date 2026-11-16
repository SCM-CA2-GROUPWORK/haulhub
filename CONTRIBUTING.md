# Contributing to HaulHub

| Field | Detail |
| :--- | :--- |
| **Parent Plan** | `SCMP-HH-001` (v1.0) — Software Configuration Management Plan |
| **Related Records** | `BL-HH-001` — CI Register & Baseline Log |
| **Version** | 1.0 |
| **Task Reference** | `H2-6` |
| **Owner** | SCM Manager / Engineering Tech Leads |

This document is the rule book for working in the HaulHub repository. It sets out the conventions for **branches**, **commits** and **pull requests (PRs)** that every collaborator must follow.

It builds on the [How to Contribute](README.md#how-to-contribute) section of the README and on `SCMP-HH-001`. If anything here appears to conflict with `SCMP-HH-001`, **the SCMP wins**. Raise the conflict with the SCM Manager so this guide can be corrected.

> **Key words:** **MUST** / **MUST NOT** are mandatory rules. **SHOULD** / **SHOULD NOT** are strong recommendations; deviate only with a reason stated in the PR.

---

## Table of Contents

1. [Golden Rules](#1-golden-rules)
2. [Before You Start](#2-before-you-start)
3. [Branch Conventions](#3-branch-conventions)
4. [Commit Conventions](#4-commit-conventions)
5. [Pull Request Conventions](#5-pull-request-conventions)
6. [Review & Approval Rules](#6-review--approval-rules)
7. [Merging Rules](#7-merging-rules)
8. [Hotfix Workflow](#8-hotfix-workflow)
9. [Release & Tagging](#9-release--tagging)
10. [Changes to Controlled Configuration Items](#10-changes-to-controlled-configuration-items)
11. [Quick Reference](#11-quick-reference)

---

## 1. Golden Rules

1. Every change **MUST** trace back to a Jira Change Request (CR) or Story ID (e.g. `H2-12`). No ticket, no branch.
2. Nobody commits directly to `main`. All changes reach `main` through a reviewed PR.
3. `main` **MUST** always be deployable. Every merge to `main` deploys automatically to the QA/Integration environment.
4. **MUST NOT** commit secrets, API keys, credentials, tokens or `.env` files. Secrets live in HashiCorp Vault / cloud KMS only.
5. **MUST NOT** add unpinned dependencies. `latest`, floating tags and open version ranges are prohibited.
6. Baselined Configuration Items (see `BL-HH-001`) can only change through an approved CR and a reviewed PR.

---

## 2. Before You Start

1. Make sure a Jira ticket is assigned to you and describes the change, the reason for it, and the affected CIs.
2. Sync with the latest `main`:
   ```bash
   git checkout main
   git pull origin main
   ```
3. Use the containerized local development setup so your environment matches the higher environments.

---

## 3. Branch Conventions

HaulHub uses **trunk-based development** with short-lived branches (`SCMP-HH-001` §3.1).

### 3.1 Permitted branch types

| Branch Type | Pattern | Branched From | Merges Into | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **Main (trunk)** | `main` | — | — | Always deployable. Protected. |
| **Feature / Story** | `feature/<jira-id>-<short-description>` | `main` | `main` | New features, enhancements, tasks, docs and non-emergency fixes |
| **Release** | `release/x.y` | `main` | — (long-lived for that release) | Release stabilization, QA/UAT, hotfix backports |
| **Hotfix** | `hotfix/<jira-id>-<short-description>` | A release tag (e.g. `v1.2.0`) | `main` **and** the matching `release/x.y` | Emergency production fixes |

No other branch prefixes are allowed. Bug fixes, documentation, refactors and chores that are not production emergencies use the `feature/` prefix. The commit/PR type (see §4) records what kind of change it is.

### 3.2 Naming rules

- The Jira ID **MUST** come straight after the prefix, in its original uppercase form: `H2-6`, not `h2-6`.
- `<short-description>` **MUST** be lowercase kebab-case (`a-z`, `0-9`, `-`). Keep it to about five words or fewer.
- **MUST NOT** contain spaces, underscores, uppercase letters (other than the Jira ID) or personal names.
- `release/x.y` uses only `MAJOR.MINOR` (e.g. `release/1.2`). Patch versions are tags, not branches.

| ✅ Valid | ❌ Invalid | Why invalid |
| :--- | :--- | :--- |
| `feature/H2-11-readme-contributing` | `H2-11-readme-contributing` | Missing type prefix |
| `feature/H2-24-listing-search-filter` | `feature/listing-search-filter` | Missing Jira ID |
| `hotfix/H2-40-payment-timeout` | `hotfix/H2-40_Payment_Timeout` | Underscores / uppercase |
| `release/1.2` | `release/1.2.0` | Release branches are `x.y` only |
| — | `johns-branch`, `test`, `wip` | No prefix, no ticket |

### 3.3 Branch lifecycle

- `feature/*` and `hotfix/*` branches **SHOULD** last no longer than a few days. Split larger work into multiple tickets and PRs.
- Keep your branch current by rebasing on, or merging in, the latest `main` before opening a PR and before merging.
- Delete the branch after it is merged.

```bash
git checkout -b feature/H2-XX-your-feature-name
```

---

## 4. Commit Conventions

Commits follow a [Conventional Commits](https://www.conventionalcommits.org/)–style format **with the Jira ID as the scope**. This gives every commit automatic traceability to its CR (`SCMP-HH-001` §6.4) and drives changelog generation (§3.3).

### 4.1 Format

```text
<type>(<JIRA-ID>): <subject>

[optional body]

[optional footer(s)]
```

Example:

```bash
git commit -m "feat(H2-XX): add listing search filter"
```

### 4.2 Allowed types

| Type | Use for |
| :--- | :--- |
| `feat` | A new feature or user-visible enhancement |
| `fix` | A bug fix |
| `docs` | Documentation only (README, SCMP, specs, this guide) |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf` | Performance improvement |
| `test` | Adding or correcting tests only |
| `build` | Build system, dependency manifests, lockfiles, SBOM |
| `ci` | CI/CD pipeline definitions (`/.github/workflows/`) |
| `chore` | Repo maintenance that doesn't change shipped behavior (e.g. `.gitkeep`, CODEOWNERS) |
| `revert` | Reverts a previous commit |

### 4.3 Subject line rules

- **MUST** include the Jira ID as the scope, e.g. `fix(H2-31): ...`.
- **MUST** use the imperative mood: "add", "fix", "remove" — not "added" or "fixes".
- **MUST** start lowercase after the colon and have no trailing period.
- **SHOULD** be 72 characters or fewer for the whole first line.

### 4.4 Body and footer rules

- Use the body to explain **what** changed and **why**, not how. Wrap at about 72 characters.
- Breaking changes (e.g. to an API contract, DB schema or event schema) **MUST** add `!` after the scope **and** include a `BREAKING CHANGE:` footer:
  ```text
  feat(H2-52)!: rename pickup_window to pickup_slot in listings API

  BREAKING CHANGE: clients must send pickup_slot; pickup_window is removed.
  ```
- Reverts **MUST** name the reverted commit SHA in the body.

### 4.5 Commit hygiene

- Keep each commit focused on one logical change.
- **MUST NOT** commit generated build output, local IDE settings or secrets.
- When you change an API contract, DB schema or dependency, commit the matching update in the same PR:
  - OpenAPI spec (`/api/openapi/`)
  - protobuf/event schema (`/api/protobuf/`)
  - migration script (`/db/migrations/postgres/`)
  - lockfile / SBOM

---

## 5. Pull Request Conventions

### 5.1 Target branch

| Source branch | PR target |
| :--- | :--- |
| `feature/*` | `main` |
| `hotfix/*` | `main` **and** the affected `release/x.y` (two PRs) |
| Backport to a release | `release/x.y` |

### 5.2 PR title

The PR title **MUST** use the same format as a commit subject, because it becomes the squash-merge commit message:

```text
feat(H2-XX): add listing search filter
```

### 5.3 PR description

Every PR description **MUST** include:

```markdown
## Jira
H2-XX — <link to ticket>

## Summary
What changed and why.

## Affected Configuration Items
e.g. CI-SVC-002 (listings-service), CI-API-001 (gateway OpenAPI spec)

## Testing
How this was tested (unit / integration / manual steps).

## Checklist
- [ ] Branch name follows `CONTRIBUTING.md` §3
- [ ] Commits follow `CONTRIBUTING.md` §4
- [ ] Unit and integration tests added/updated
- [ ] OpenAPI / protobuf / migrations / lockfiles updated where relevant
- [ ] No secrets or unpinned dependencies introduced
- [ ] CCB approval obtained (if required — see §6.2)
```

### 5.4 PR size and state

- PRs **SHOULD** be small and single-purpose; one Jira ticket per PR.
- Open a **Draft PR** for early feedback. Mark it *Ready for review* only when all CI gates pass.

### 5.5 Required CI gates

All of the following automated checks **MUST** pass before you request review and before merge:

- Build, plus unit and integration test suites
- Static analysis and linting (`SonarQube`, `ESLint`, `SwiftLint`)
- Dependency and container vulnerability/license scans (`Snyk`, `Dependabot`, `Trivy`)

Disabling, skipping or weakening a CI gate to get a PR merged is prohibited.

---

## 6. Review & Approval Rules

### 6.1 Standard approval

- Every PR **MUST** have **at least one approval** from a Tech Lead or a designated code owner.
- Code owners are defined in [`.github/CODEOWNERS`](.github/CODEOWNERS) and are requested automatically:

| Path | Required Code Owner(s) |
| :--- | :--- |
| `/apps/`, `/services/`, `/api/`, `/db/`, `/infra/` | `@SCM-CA2-GROUPWORK/devs` |
| `/docs/` | `@SCM-CA2-GROUPWORK/ccb`, `@SCM-CA2-GROUPWORK/devs` |
| `/haulhub_software_configuration_management_plan.md` | `@SCM-CA2-GROUPWORK/config-managers` |
| `/haulhub_ci_register_baseline_log.md` | `@SCM-CA2-GROUPWORK/config-managers` |

- Authors **MUST NOT** approve their own PRs.
- Respond to every review comment, either by changing the code or by replying with a reason, before you merge.

### 6.2 Configuration Control Board (CCB) approval

A PR **MUST** have CCB approval, recorded against the Jira CR, **before merge** if it touches any of the following (`SCMP-HH-001` §3.2):

- A **released baseline** (anything already tagged, e.g. `v0.1.0`)
- **Shared APIs**: `/api/openapi/`, `/api/protobuf/`, Kafka topic definitions
- **Payments**: `payments-service`, payment SDKs
- **Identity**: `identity-service`, auth flows, KYC integration
- **Data-handling** components: personal data, schemas, retention, privacy

### 6.3 Security Lead approval

The Security Lead **MUST** also approve:

- Adding any new third-party or open-source dependency
- **Major-version** upgrades of an existing dependency (a CR is also required, `SCMP-HH-001` §5.3)
- Changes to identity, payment or data-handling components
- Onboarding any supplier that touches payment, identity or personal data (this also needs CCB sign-off)

---

## 7. Merging Rules

1. Merge only when **all** of these are true:
   - CI is green.
   - The required approvals are in (§6).
   - The branch is up to date with its target.
2. **Squash merge** is the default for `feature/*` → `main`. The squashed commit message **MUST** match the PR title format from §5.2.
3. A **merge commit** may be used for `hotfix/*` and release backport merges, so their history stays traceable across `main` and `release/x.y`.
4. Delete the source branch after merging.
5. Direct pushes, force-pushes and history rewrites on `main` and `release/*` are prohibited. The only exception is a documented incident/break-glass procedure, which is logged and reviewed after the incident (`SCMP-HH-001` §3.2).

---

## 8. Hotfix Workflow

Hotfixes are for **production emergencies only**.

```bash
# 1. Branch from the affected release tag
git fetch --tags
git checkout -b hotfix/H2-XX-short-description v1.2.0

# 2. Fix, commit using §4 conventions, push
git commit -m "fix(H2-XX): handle null pickup address"
git push -u origin hotfix/H2-XX-short-description
```

3. Open PRs into **both** `release/x.y` and `main`.
4. A hotfix may skip full CCB review for expedited deployment. It **MUST** be ratified retroactively by the CCB **within one business day** (`SCMP-HH-001` §3.2).
5. The CI gates in §5.5 and at least one peer approval are still required.

---

## 9. Release & Tagging

- Releases use **semantic versioning** `MAJOR.MINOR.PATCH`, tagged as `vX.Y.Z` (e.g. `v0.1.0`).
- Tags **MUST** be **annotated** and reference the Jira ticket in the tag message:
  ```bash
  git tag -a vX.Y.Z -m "Release vX.Y.Z: <summary> (H2-XX)"
  git push origin vX.Y.Z
  ```
- Only the **Release Manager** or other designated roles (`SCMP-HH-001` §2.2, §6.3) may create or push release and baseline tags. Contributors **MUST NOT** create, move or delete version tags.
- Tags are immutable. A tag that has been pushed is never moved or reused.
- Production promotion is manual and approved by the CCB / Release Manager.

---

## 10. Changes to Controlled Configuration Items

All items listed in `BL-HH-001` are under formal configuration control. In addition to the rules above:

| CI Category | Extra Rule |
| :--- | :--- |
| **Governance docs** (`CI-DOC-*`) | Keep the Document ID and bump its version number (e.g. `SCMP-HH-001` v1.0 → v1.1). SCMP changes need CCB approval. |
| **DB migrations** (`CI-DAT-001`) | Add new migrations with the next sequential ID (e.g. `0006_<description>`). **MUST NOT** edit a migration that is already in a baseline. Fix forward with a new migration instead. |
| **API contracts** (`CI-API-*`) | Breaking changes need a new spec version (`v1` → `v2`), a `BREAKING CHANGE:` footer and CCB approval. |
| **Infrastructure** (`CI-INF-*`) | All infrastructure changes go through IaC (Terraform/Helm/Argo CD) in a PR. No manual changes to environments. |
| **Dependencies** (`CI-DEP-001`) | Pin exact versions, commit the updated lockfile, and update the SBOM. |
| **CI Register** | If your change adds, removes or renames a CI, tell the SCM Manager so `BL-HH-001` can be updated. |

---

## 11. Quick Reference

```text
Branch:     feature/H2-XX-short-description
            hotfix/H2-XX-short-description   (from release tag)
            release/x.y                      (Release Manager only)

Commit:     <type>(H2-XX): <imperative subject, ≤72 chars, no period>
            types: feat fix docs refactor perf test build ci chore revert

PR title:   same as commit format
PR target:  main  (hotfix → main + release/x.y)
Approvals:  ≥1 Tech Lead / code owner  (+ CCB / Security Lead where §6.2–6.3 apply)
Merge:      squash (feature) · merge commit (hotfix/backport) · delete branch after
Never:      push to main · commit secrets · unpinned deps · move tags · edit baselined migrations
```

Questions about these conventions go to the SCM Manager. Proposed changes to this guide follow the same PR process as everything else.
