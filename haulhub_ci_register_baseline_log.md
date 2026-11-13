# HaulHub — Configuration Item (CI) Register & Baseline Log

| Field | Detail |
| :--- | :--- |
| **Document ID** | `BL-HH-001` |
| **Parent Plan** | `SCMP-HH-001` (v1.0) |
| **Baseline Tag** | `v0.1.0` |
| **Baseline Type** | Initial Functional & Allocated Baseline (Pre-Alpha / Foundation) |
| **Status** | Approved / Locked |
| **Effective Date** | November 13, 2026 |
| **Task Reference** | `H2-5` (`Haulhub_2`) |
| **Owner** | SCM Manager / Release Manager |

---

## 1. Purpose

This document establishes the formal **Configuration Item (CI) Register** and records the **Initial Baseline Log (`v0.1.0`)** for the HaulHub platform, in accordance with Section 3.1 (*Configuration Identification*) and Section 6.1 (*SCM Records*) of `SCMP-HH-001`. 

All artifacts listed below are officially placed under formal configuration control. Any subsequent modifications to these items require an approved Change Request (CR) and pull request verification prior to merge.

---

## 2. Master Configuration Item (CI) Register

### 2.1 Documentation & Governance CIs (`CI-DOC-*`)

| CI ID | Artifact Name | Repository / Storage Path | Identification Scheme | Owner |
| :--- | :--- | :--- | :--- | :--- |
| `CI-DOC-001` | Software Configuration Management Plan | `/docs/scm/SCMP-HH-001.md` | `SCMP-HH-001` (v1.0) | SCM Manager |
| `CI-DOC-002` | Software Requirements Specification | `/docs/specs/SRS-HH-001.md` | `SRS-HH-001` (v1.0) | Product Owner |
| `CI-DOC-003` | System Architecture & Design Document | `/docs/arch/ARC-HH-002.md` | `ARC-HH-002` (v1.0) | Engineering / Tech Lead |
| `CI-DOC-004` | Master Test Plan | `/docs/qa/TP-HH-003.md` | `TP-HH-003` (v0.1) | QA Lead |
| `CI-DOC-005` | Release Management Procedure | `/docs/ops/REL-HH-004.md` | `REL-HH-004` (v0.1) | Release Manager |
| `CI-DOC-006` | Data Privacy & Security Policy | `/docs/sec/SEC-HH-005.md` | `SEC-HH-005` (v1.0) | Security Lead |
| `CI-DOC-007` | Repository README & Contribution Guide | `/README.md` | Git tag `v0.1.0` | Engineering / Tech Lead |

---

### 2.2 Client Applications (`CI-APP-*`)

| CI ID | Component Name | Repository / Path | Identification Scheme | Owner |
| :--- | :--- | :--- | :--- | :--- |
| `CI-APP-001` | Consumer Mobile App (iOS) | `haulhub-mobile-ios` (`/apps/ios`) | SemVer `0.1.0` + Build `1` | Mobile Tech Lead |
| `CI-APP-002` | Consumer & Driver Mobile App (Android) | `haulhub-mobile-android` (`/apps/android`) | SemVer `0.1.0` + VersionCode `1` | Mobile Tech Lead |
| `CI-APP-003` | Web Marketplace Front End | `haulhub-web` (`/apps/web-marketplace`) | SemVer `0.1.0` | Frontend Tech Lead |
| `CI-APP-004` | Admin & Back-Office Console | `haulhub-admin` (`/apps/web-admin`) | SemVer `0.1.0` | Frontend Tech Lead |

---

### 2.3 Backend Microservices (`CI-SVC-*`)

| CI ID | Microservice | Repository / Path | Container Image Tag | Owner |
| :--- | :--- | :--- | :--- | :--- |
| `CI-SVC-001` | Identity & Auth Service | `/services/identity-service` | `ghcr.io/haulhub/identity:0.1.0` | Security / Backend Lead |
| `CI-SVC-002` | Marketplace Listings Service | `/services/listings-service` | `ghcr.io/haulhub/listings:0.1.0` | Backend Tech Lead |
| `CI-SVC-003` | Freight Matching Engine | `/services/matching-service` | `ghcr.io/haulhub/matching:0.1.0` | Backend Tech Lead |
| `CI-SVC-004` | In-App Messaging Service | `/services/messaging-service` | `ghcr.io/haulhub/messaging:0.1.0` | Backend Tech Lead |
| `CI-SVC-005` | Payments & Settlement Service | `/services/payments-service` | `ghcr.io/haulhub/payments:0.1.0` | Backend / Security Lead |
| `CI-SVC-006` | Live GPS Tracking & POD Service | `/services/tracking-service` | `ghcr.io/haulhub/tracking:0.1.0` | Backend Tech Lead |
| `CI-SVC-007` | Push & Email Notifications Service | `/services/notifications-service` | `ghcr.io/haulhub/notifications:0.1.0` | Backend Tech Lead |

---

### 2.4 Data Schemas & API Contracts (`CI-DAT-*` / `CI-API-*`)

| CI ID | Artifact Name | Repository / Path | Identification Scheme | Owner |
| :--- | :--- | :--- | :--- | :--- |
| `CI-DAT-001` | Core Relational DB Migrations | `/db/migrations/postgres/` | Sequential ID `0001_init` – `0005_core` | Backend Tech Lead |
| `CI-DAT-002` | Message Broker Topic Definitions | `/infra/kafka/topics.yaml` | Git tag `v0.1.0` | DevOps Lead |
| `CI-API-001` | Public & Mobile Gateway OpenAPI Spec | `/api/openapi/gateway-v1.yaml` | OpenAPI `v1.0.0` (tag `v0.1.0`) | Engineering / Tech Lead |
| `CI-API-002` | Internal Service gRPC / Event Schemas | `/api/protobuf/events-v1.proto` | Proto `v1` (tag `v0.1.0`) | Backend Tech Lead |

---

### 2.5 Infrastructure, CI/CD & Dependencies (`CI-INF-*` / `CI-DEP-*`)

| CI ID | Artifact Name | Repository / Path | Identification Scheme | Owner |
| :--- | :--- | :--- | :--- | :--- |
| `CI-INF-001` | Cloud Provisioning Modules (Terraform) | `/infra/terraform/` | Env-scoped tag `v0.1.0-dev` | DevOps / Platform Lead |
| `CI-INF-002` | Kubernetes Helm Charts & Manifests | `/infra/helm/haulhub-umbrella/` | Chart `0.1.0` (appVersion `0.1.0`) | DevOps / Platform Lead |
| `CI-INF-003` | CI/CD Pipeline Definitions | `/.github/workflows/` | Git tag `v0.1.0` | DevOps / Platform Lead |
| `CI-INF-004` | Argo CD Application Manifests | `/infra/argocd/` | Git tag `v0.1.0` | DevOps / Platform Lead |
| `CI-DEP-001` | Dependency Lockfiles & Initial SBOM | `package-lock.json`, `Podfile.lock`, `sbom-v0.1.0.json` | CycloneDX SBOM `v0.1.0` | Security Lead |

---

## 3. Baseline Log

### 3.1 Baseline History Summary

| Baseline Tag | Baseline Name | Milestone Type | Date Locked | Approved By | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`v0.1.0`** | **Project Initialization & Foundation Baseline** | Functional & Allocated Baseline | 2026-11-13 | SCM Manager, CCB | **Active / Locked** |

---

### 3.2 Detailed Baseline Entry: `v0.1.0`

* **Baseline Tag:** `v0.1.0`
* **Git Reference:** `refs/tags/v0.1.0` on branch `main`
* **Jira Reference:** `H2-5` (*Identify CIs, baseline log, tag v0.1.0*)
* **Baseline Scope & State:**
  * Locks initial repository structure, trunk-based branching rules (`main` protection enabled), and CI/CD workflow skeletons.
  * Establishes initial governance documentation (`SCMP-HH-001`, `SRS-HH-001`, `ARC-HH-002`).
  * Scaffolds the 7 core backend microservices, mobile/web app workspaces, initial database schemas (`0001_init`), and OpenAPI `v1` contracts.
  * Generates the baseline Software Bill of Materials (`sbom-v0.1.0.json`) with all third-party dependencies version-pinned.
* **Verification & Sign-Off:**
  * [x] All CIs assigned unique identifiers and mapped to owners.
  * [x] Automated CI checks (linting, static analysis, dependency scan) passing on `main`.
  * [x] Annotated Git tag `v0.1.0` created and pushed to remote repository.

---

## 4. Tagging Procedure Reference (`v0.1.0`)

To reproduce or verify the `v0.1.0` baseline tag in Git:

```bash
# Verify clean working tree on main
git checkout main
git pull origin main

# Create annotated baseline tag
git tag -a v0.1.0 -m "Baseline v0.1.0: Initial CI identification, governance docs, and service scaffolds (H2-5)"

# Push tag to remote origin
git push origin v0.1.0
```