# HaulHub Test Plan

| Field | Detail |
| :--- | :--- |
| Document ID | TP-HH-003 |
| Version | 0.1 |
| Status | Draft |
| Date | 23-11-26 |
| Prepared By | IY |
| Related Ticket | H2-9 |
| Governing Plan | SCMP-HH-001 |

## 1. Purpose
This test plan defines the acceptance criteria a change must satisfy before it
is merged into `main` or promoted to a release baseline.

## 2. Scope
Applies to all Configuration Items under SCMP-HH-001 §3.1: source code,
infrastructure-as-code, container images, database schema, API contracts,
third-party components and documentation.

## 3. Acceptance Criteria Checklist

### 3.1 Change control (SCMP §3.2)
- [ ] A Jira Change Request or Story ID exists for the change
- [ ] The change was made on a short-lived branch from `main`
      (`feature/<jira-id>-<short-description>` or `hotfix/<jira-id>-<short-description>`)
- [ ] Commit messages reference the Jira ID (e.g. `docs(H2-9): ...`)
- [ ] No direct commits to `main`
- [ ] Changes affecting a released baseline, shared APIs, payments, identity or
      data-handling were escalated to and approved by the CCB
- [ ] Emergency/hotfix changes were ratified by the CCB within one business day

### 3.2 Pull request
- [ ] PR targets `main` and links the Jira ticket
- [ ] PR description summarises what changed and why
- [ ] At least one peer review approval from a Tech Lead or code owner
- [ ] All review comments resolved
- [ ] Merged by squash/merge and the feature branch deleted

### 3.3 Automated CI gates (SCMP §3.2, §4.4)
- [ ] Build passes
- [ ] Unit and integration tests pass
- [ ] Static analysis and linting pass (SonarQube, ESLint, SwiftLint)
- [ ] Dependency and container vulnerability scans pass (Snyk, Dependabot, Trivy)

### 3.4 Code and dependency quality (SCMP §5.3)
- [ ] Unit and integration tests accompany code changes
- [ ] API contracts, migration scripts or lockfiles updated where affected
- [ ] Dependencies are pinned (no `latest` or floating tags)
- [ ] No secrets or API keys committed
- [ ] New open-source dependencies or major upgrades have a change request

### 3.5 Release readiness (SCMP §3.4, §3.5)
- [ ] FCA: release candidate meets approved requirements and all test cases pass
- [ ] PCA: deployed artifacts match the approved baseline, with no undocumented drift
- [ ] Release is tagged with a semantic version and a changelog is generated
- [ ] SBOM generated for the release

### 3.6 Records and traceability (SCMP §6.4)
- [ ] Change is traceable from commit to Jira CR to approver (and CCB decision if applicable)
- [ ] Jira status reflects the real state of the work

## 4. Pass / Fail Rule
A change is accepted only when every applicable item above is checked.
Items that do not apply must be marked "N/A" with a reason.

## 5. Revision History
| Version | Date | Author | Change |
| :--- | :--- | :--- | :--- |
| 0.1 | 23-11-26 | IY | Initial draft |