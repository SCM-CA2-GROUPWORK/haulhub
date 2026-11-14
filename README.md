# haulhub

Facebook marketplace logistic needs tracker

## How to Contribute

All contributions to **HaulHub** are governed by our Software Configuration Management Plan (`SCMP-HH-001`). Please follow the workflow below to ensure full traceability, code quality, and baseline integrity.

### 1. Prerequisites & Setup

1. Ensure you have an assigned **Jira Change Request (CR) or Story ID** (e.g., `H2-12`) before starting work.
2. Clone the repository and ensure your local environment matches our containerized dev setup:
   ```bash
   git clone <repository-url>
   cd haulhub
   git checkout main && git pull origin main
   ```
3. Never commit secrets, API keys, or unpinned dependencies (`latest`/floating tags are prohibited).

### 2. Branching Strategy

HaulHub uses **trunk-based development**. Direct commits to `main` are blocked. Create a short-lived branch from `main` using the naming conventions below:

| Branch Type | Naming Convention | Purpose |
| :--- | :--- | :--- |
| **Feature / Story** | `feature/<jira-id>-<short-description>` | New features, enhancements, or tasks (e.g., `feature/H2-11-readme-contributing`) |
| **Release** | `release/x.y` | Cut from `main` for release stabilization and QA/UAT |
| **Hotfix** | `hotfix/<jira-id>-<short-description>` | Emergency production fixes branched from a release tag |

```bash
git checkout -b feature/H2-XX-your-feature-name
```

### 3. Making Changes & Commits

* Write clean, modular code accompanied by **unit and integration tests**.
* If you modify API contracts, database schemas, or add/upgrade third-party packages, update the corresponding OpenAPI spec, migration scripts, or lockfiles.
* Reference your Jira ticket ID in your commit messages for automated traceability:
  ```bash
  git commit -m "feat(H2-XX): add listing search filter"
  ```

### 4. Pull Request & Review Process

1. Push your branch to the remote repository and open a **Pull Request (PR)** targeting `main`:
   ```bash
   git push -u origin feature/H2-XX-your-feature-name
   ```
2. Link the corresponding **Jira ticket** in the PR description and summarize what changed and why.
3. Ensure all **automated CI gates** pass before requesting review:
   * Build & unit/integration test suites
   * Static analysis & linting (`SonarQube`, `ESLint`, `SwiftLint`)
   * Dependency & container vulnerability scans (`Snyk`, `Dependabot`, `Trivy`)
4. Obtain **at least one peer review approval** from a Tech Lead or designated code owner.
   * *Note:* Changes affecting released baselines, shared APIs, payments, identity, or data-handling components require **Configuration Control Board (CCB)** approval prior to merge.
5. Once approved and green, squash/merge your PR into `main` and delete your feature branch.