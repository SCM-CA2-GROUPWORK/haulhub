# HaulHub Repository and CI Design Notes
## Purpose
This document explains why the HaulHub repository is organized into the
directories shown below and why configuration items (CIs) are grouped into the
categories recorded in `BL-HH-001`. It is a navigation and rationale aid, not a
replacement for the Software Configuration Management Plan
(`docs/scm/SCMP-HH-001.md`) or the CI Register.
## Repository layout
```text
.
|-- apps/                    Customer and operator applications
|   |-- android/
|   |-- ios/
|   |-- marketplace/
|   `-- web-admin/
|-- services/                Independently owned backend microservices
|   |-- identity_service/
|   |-- listings-service/
|   |-- matching-service/
|   |-- messaging-service/
|   |-- notifications-service/
|   |-- payments-service/
|   `-- tracking-service/
|-- api/
|   |-- openapi/gateway-v1.yaml
|   `-- protobuf/events-v1.proto
|-- db/migrations/postgres/   Versioned relational database changes
|-- infra/
|   |-- argocd/
|   |-- helm/haulhub-umbrella/
|   |-- kafka/topics.yaml
|   `-- terraform/
|-- docs/
|   |-- arch/
|   |-- ops/
|   |-- qa/
|   |-- scm/
|   |-- sec/
|   `-- specs/
|-- .github/
|   `-- CODEOWNERS
|-- README.md
|-- CONTRIBUTING.md
|-- haulhub_software_configuration_management_plan.md
`-- haulhub_ci_register_baseline_log.md
```
The layout uses a monorepository to keep shared contracts, infrastructure
definitions, governance records, and application ownership visible in one
baseline. It separates executable surfaces and technical concerns so that:
- each application or service has a clear owner and release boundary;
- API and event contracts are reviewed separately from their implementations;
- database changes are applied through ordered migrations instead of ad hoc
  schema edits;
- infrastructure is reproducible and auditable through source-controlled
  Terraform, Helm, Kafka, and Argo CD definitions;
- governance documents and implementation artifacts remain traceable.
Most service and application directories are currently scaffolds containing
`.gitkeep` files. A scaffolded path is still a managed repository location:
it gives teams and reviewers a stable address for future implementation
without creating unowned or misplaced source trees.
### Directory rationale
| Area | Responsibility | Why it is separate |
| --- | --- | --- |
| `apps/ios`, `apps/android` | Consumer and driver mobile experiences | Platform-specific toolchains and app-store build/release processes require separate ownership. |
| `apps/marketplace`, `apps/web-admin` | Public marketplace and back-office web experiences | They have different audiences and deployment risk, while sharing API contracts and design systems. |
| `services/identity_service` | Authentication, authorization, and KYC | Identity is security-sensitive and must have explicit ownership and CCB review. |
| `services/listings-service` | Listing lifecycle and marketplace content | It owns listing behavior and changes independently from matching or messaging. |
| `services/matching-service` | Freight and trip matching | Algorithm changes can be developed and tested without coupling to user-facing clients. |
| `services/messaging-service` | In-app communication | Messaging lifecycle and moderation concerns can evolve independently. |
| `services/payments-service` | Settlement and payment orchestration | Payment handling has heightened security, compliance, and audit requirements. |
| `services/tracking-service` | GPS state and proof of delivery | It owns location and delivery confirmation data with different availability requirements. |
| `services/notifications-service` | Push and email delivery | Channel integrations are isolated from the core shipment lifecycle. |
| `api/openapi/` | Mobile and gateway REST contracts | Versioned HTTP contracts let client and service teams coordinate changes. |
| `api/protobuf/` | Internal event and RPC contracts | The shared schema prevents services from evolving incompatible messages. |
| `db/migrations/postgres/` | Forward-only database changes | Sequential migrations make schema state reproducible and prevent baseline drift. |
| `infra/` | Cloud, runtime, and integration configuration | Declarative infrastructure provides reviewable environment state and promotion history. |
| `docs/` | Requirements, architecture, QA, operations, security, and SCM records | Controlled documentation is grouped by lifecycle concern and document ID. |
| `.github/CODEOWNERS` | Review ownership | Path-based ownership sends changes to the appropriate engineers and CCB groups. |
There are two baseline/path naming differences to resolve when implementation
begins:
- the actual marketplace directory is `apps/marketplace/`, while
  `BL-HH-001` names `CI-APP-003` as `/apps/web-marketplace`;
- the actual identity directory is `services/identity_service/`, while
  `BL-HH-001` uses `services/identity-service`.
These are planning inconsistencies, not evidence that both names are valid.
The repository layout should be changed consistently by implementation or the
CI register should be updated through an approved change.
## CI register rationale
The baseline log assigns IDs and owners to artifacts that can materially affect
the build, behavior, deployment, or legal/security status of HaulHub. The
categories in `haulhub_ci_register_baseline_log.md` are used for that purpose:
| CI category | Examples | Rationale |
| --- | --- | --- |
| `CI-DOC-*` | SCMP, SRS, architecture, test, release, and security documents | Requirements and procedures must be versioned with the implementation they govern. |
| `CI-APP-*` | iOS, Android, marketplace, and admin applications | Each shipped application has independent versioning and release approval. |
| `CI-SVC-*` | Identity, listings, matching, messaging, payments, tracking, notifications | Independent services need explicit build, container, owner, and security boundaries. |
| `CI-DAT-*` | PostgreSQL migrations | Schema state is deployable configuration and must not be rewritten after baseline. |
| `CI-API-*` | OpenAPI and protobuf contracts | Shared contracts are integration APIs and therefore require compatibility and CCB control. |
| `CI-INF-*` | Terraform, Helm, Argo CD, and CI/CD definitions | Infrastructure and pipeline definitions determine the reproducible deployed state. |
| `CI-DEP-*` | Pinned dependencies and SBOM | Third-party components affect security and supply-chain provenance. |
Each CI needs more than a filename: an identifier, repository path, identification
scheme or version, and owner create a minimum audit chain. The unique IDs in
`BL-HH-001` allow Jira changes, pull requests, builds, releases, and audit
reports to refer to the same artifact even if a team later renames a package or
source directory.
The groups also determine change impact. Changes to released baselines, shared
APIs, payments, identity, personal data, or data schemas require CCB or Security
Lead review according to `CONTRIBUTING.md`. That higher scrutiny is intended to
protect changes with wider or more sensitive blast radius; it does not need to
be applied to every small documentation or feature change.
## Automated CI list rationale
The required automated gates described by the contribution documents form the
practical CI list:
1. **Build**: proves that affected code and configuration can produce deployable
   artifacts.
2. **Unit and integration tests**: checks local behavior and service interactions
   before review or promotion.
3. **Linting and static analysis**: SonarQube, ESLint, and SwiftLint catch
   maintainability and platform-specific defects consistently across
   languages.
4. **Dependency and license scanning**: Snyk and Dependabot identify vulnerable
   or incompatible third-party components before they enter a baseline.
5. **Container and image scanning**: Trivy checks packaged runtime surfaces
   in addition to source dependencies.
These checks are mandatory because no manual review reliably covers every build
combination, dependency advisory, or platform lint rule. The list is proportionate:
builds and tests validate behavior; static analysis reduces review noise;
dependency and container scans reduce supply-chain risk. The gates must be
scoped to affected components when implementation grows, but they must not be
optional or silently weakened.
No `.github/workflows/` directory exists in the present scaffold. Therefore,
pipeline definitions are a planned controlled CI area, not an implemented one.
When workflows are added, they should live at `/.github/workflows/` and be
registered as `CI-INF-003` in the baseline log. They should run the gates above
and publish immutable, versioned artifacts; promotion should reuse those artifacts
instead of rebuilding them for QA, staging, and production.
