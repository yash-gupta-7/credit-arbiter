<div align="center">

# 🏦 Halcyon Credit

### Agentic Underwriting Copilot

*An evidence-backed, human-in-the-loop lending decision engine — ML risk scoring + RAG policy retrieval + LLM explanations, with fairness, audit, and cost guardrails built in.*

<br/>

![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.111-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![LightGBM](https://img.shields.io/badge/LightGBM-ML-9ACD32?style=for-the-badge&logo=leaflet&logoColor=white)
![Qdrant](https://img.shields.io/badge/Qdrant-RAG-DC244C?style=for-the-badge&logo=qdrant&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)

![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)
![Version](https://img.shields.io/badge/version-1.0.0-blue?style=flat-square)
![Tests](https://img.shields.io/badge/tests-117%20passing-brightgreen?style=flat-square)
![Sprints](https://img.shields.io/badge/sprints%201--4-complete-success?style=flat-square)
![PRs](https://img.shields.io/badge/PRs-welcome-ff69b4?style=flat-square)
![RAG](https://img.shields.io/badge/RAG-Qdrant%20%2B%20fastembed-blueviolet?style=flat-square)
![LLM](https://img.shields.io/badge/LLM-OpenRouter-black?style=flat-square)
![Postgres](https://img.shields.io/badge/DB-PostgreSQL-336791?style=flat-square&logo=postgresql&logoColor=white)

</div>

> [!NOTE]
> **Halcyon Credit** is an **Agentic Underwriting Copilot** for a digital consumer lender. It combines a trained **ML risk model**, a **RAG policy engine**, and an **LLM explanation layer** to produce **evidence-backed lending recommendations** — but a **human underwriter always makes the final call**. Every automated recommendation ships with a complete, citable **evidence chain**, and hard guardrails (fairness hard-block, kill-switch, cost cutoff, immutable audit log) prevent the system from ever auto-approving on incomplete or biased evidence.

> [!IMPORTANT]
> **Honesty first.** This README documents *only what exists in the codebase*. Where a capability is a deliberate simplification, a mock, or a deferred item, it is marked **`Not implemented`**, **`Simulated`**, or **`Deferred`** — never dressed up. This mirrors the project's own engineering docs (`claude.md`, `docs/ACCEPTANCE_VERIFICATION.md`).

---

## 📑 Table of Contents

1. [Live Demo & Screenshots](#-live-demo--screenshots)
2. [Features](#-features)
3. [Why This Project Exists](#-why-this-project-exists)
4. [Product Requirements (PRD)](#-product-requirements-prd)
5. [System Overview](#-system-overview)
6. [High-Level Architecture (HLD)](#-high-level-architecture-hld)
7. [Low-Level Design (LLD)](#-low-level-design-lld)
8. [Data Flow](#-data-flow--request-lifecycle)
9. [Database Design](#-database-design)
10. [API Documentation](#-api-documentation)
11. [The AI / RAG Pipeline](#-the-ai--rag-pipeline)
12. [The ML Pipeline](#-the-ml-pipeline)
13. [Folder Structure](#-folder-structure)
14. [Tech Stack](#-tech-stack)
15. [Architecture Decisions](#-architecture-decisions)
16. [Installation & Setup](#-installation--setup)
17. [Environment Variables](#-environment-variables)
18. [Running the Project](#-running-the-project)
19. [Testing](#-testing)
20. [Deployment](#-deployment)
21. [Security](#-security)
22. [Observability & Ops](#-observability--ops)
23. [Design Patterns](#-design-patterns)
24. [Roadmap](#-roadmap)
25. [Known Limitations](#-known-limitations)
26. [Troubleshooting](#-troubleshooting)
27. [FAQ](#-faq)
28. [Interview Guide](#-interview-guide)
29. [Contributing](#-contributing)
30. [License & Credits](#-license--credits)

---

## 🎬 Live Demo & Screenshots

> [!WARNING]
> **Live Demo:** `Not implemented` — no public hosted demo URL is committed to the repository. The app runs locally with **one command** (`docker compose up -d --build`) and is then reachable at `http://localhost`. A demo walkthrough script exists at [`docs/demo_script.md`](docs/demo_script.md).

| View | Placeholder |
|------|-------------|
| 🖥️ Desktop — Underwriter Queue | `docs/screenshots/queue.png` *(add your screenshot)* |
| 🔎 Application Detail + Evidence Panel | `docs/screenshots/evidence-panel.png` |
| 📊 Ops Dashboard | `docs/screenshots/ops-dashboard.png` |
| 🌙 Dark Mode (glassmorphism, default) | `docs/screenshots/dark.png` |
| 📱 Mobile | `docs/screenshots/mobile.png` |
| 🎥 GIF Demo | `docs/screenshots/demo.gif` |

*The UI ships a single dark glassmorphism theme (`ui/style.css`); a light-mode toggle is `Not implemented`.*

---

## ✨ Features

Every row below maps to a real user story (US-1xx…US-4xx) delivered across Sprints 1–4. Status reflects the project's own [`ACCEPTANCE_VERIFICATION.md`](docs/ACCEPTANCE_VERIFICATION.md).

| # | Feature | Description | Status | Technology |
|---|---------|-------------|--------|------------|
| 1 | **JWT Auth + RBAC** | Two roles — `applicant` (own apps only) and `underwriter`/ops (all apps + tools). Ops endpoints gated by `require_ops` (403 otherwise). | ✅ Done | PyJWT, passlib[bcrypt] |
| 2 | **Application Ingestion** | Normalises a raw Home-Credit-shaped row into an `Application`, flags `INCOMPLETE` on missing required fields without crashing. | ✅ Done | FastAPI, SQLAlchemy |
| 3 | **ML Risk Scoring** | Trained **LightGBM** (hardened, aux-table aggregates) scores new applications; falls back to a deterministic rule-based scorer. | ✅ Done | LightGBM, scikit-learn |
| 4 | **RAG Policy Retrieval** | Scheme-aware clause retrieval over a versioned policy corpus (6 schemes, 21 clauses). TF-IDF default; **Qdrant + embeddings** in production. | ✅ Done | Qdrant, fastembed, scikit-learn |
| 5 | **Deterministic Policy Engine** | Machine-readable rules per clause; **a failed rule can never yield Approve.** | ✅ Done | Pure Python |
| 6 | **Recommendation + Evidence Chain** | Composes 6 evidence components into Approve/Refer/Decline; any missing component → Refer (kill-switch). | ✅ Done | Pure Python |
| 7 | **SHAP Explanations** | Top-5 risk contributors, each with a human-readable label + direction (increases/decreases risk). | ✅ Done | SHAP |
| 8 | **LLM Narrative (FR-9)** | Grounded, PII-redacted narrative via **OpenRouter**. LLM explains only — **never decides.** Falls back to deterministic text. | ✅ Done | OpenRouter (OpenAI-compatible) |
| 9 | **PII Redaction Gate** | Redacts SSN/DOB/account/card/email/phone and blocks any residual-PII prompt before an LLM call. | ✅ Done | Regex gate |
| 10 | **Fairness Monitor + Hard-Block** | Segment approval-rate deltas; a **>5pp gap pauses the scheme** and blocks auto-decisions. | ✅ Done | Pure Python |
| 11 | **Proxy-Leakage Audit** | Cramér's V / correlation ratio of features vs gender/age. Finding: `OCCUPATION_TYPE` ↔ gender (V≈0.40). | ✅ Done | pandas, scipy |
| 12 | **Document Upload + Verification** | Type-validated uploads; completeness (missing docs per scheme) + consistency (name/income agreement). | ✅ Done | FastAPI |
| 13 | **Regulatory Validation** | Identity/employment/tax/sanctions checks with exponential-backoff retry; escalates on exhaustion. **Deterministic mock** (no live KYC). | ✅ Done (Simulated) | Pure Python |
| 14 | **Escalation Workflow** | ML confidence <0.60, fairness pause, cost breach, or missing evidence → human-review queue with a reason code. | ✅ Done | Pure Python |
| 15 | **Override Capture** | An override requires a structured `reason_code` + note; decisions are single & final (409 on re-decide). | ✅ Done | FastAPI |
| 16 | **Immutable Audit Log** | Append-only **SHA-256 hash chain**; tamper-evident; `verify` + `reconstruct` endpoints. | ✅ Done | hashlib, SQLAlchemy |
| 17 | **Cost Metering + Hard Cutoff** | Per-app cost estimate persisted; projected >$0.08 → human fallback. | ✅ Done | Pure Python |
| 18 | **Kill-Switch + Degraded Mode** | Global flag routes everything to human review on any guardrail breach. | ✅ Done | SystemFlag table |
| 19 | **Ops Dashboard** | Throughput, P95, cost/app, acceptance, override, fairness gap with thresholds/alerts. | ✅ Done | FastAPI + UI panel |
| 20 | **Least-Privilege Secrets** | Secrets from env only; per-tool scopes; secret-literal scan test. | ✅ Done | os.environ, scopes |

---

## 🧭 Why This Project Exists

| Dimension | Detail |
|-----------|--------|
| **❗ Problem** | Consumer-lending underwriting is slow, inconsistent, and hard to audit. Loan officers manually cross-reference credit data against dense, versioned policy manuals under time pressure — producing decisions that are difficult to explain to a regulator and prone to hidden bias. |
| **🔍 Existing solutions** | Pure rules engines (rigid, no risk nuance); black-box ML scorecards (accurate but unexplainable, unsuitable for adverse-action notices); and raw LLM "auto-deciders" (fast but hallucinate, cannot be trusted with a credit decision). |
| **🚧 Limitations** | None of the above deliver **all four** of: quantitative risk, grounded policy citations, fairness protection, and a tamper-evident audit trail — while keeping a human legally accountable for the outcome. |
| **✅ How Halcyon solves it** | A **copilot, not an autopilot.** ML produces the risk band, RAG cites the exact governing clause, a deterministic engine enforces policy, guardrails block unfair/incomplete decisions, an LLM writes a human-readable explanation, and a **human underwriter accepts or overrides** — every step recorded in an immutable evidence chain. |

**Value delivered:**

- 💼 **Business value:** faster reviews (simulated 19-min median), consistent policy application, regulator-ready audit trail, fairness risk contained before it becomes a lawsuit.
- 👤 **User value:** underwriters get pre-assembled evidence + a draft rationale; applicants get a status view of their own applications.
- 🛠️ **Technical value:** a clean, testable, env-switchable architecture (rule↔ML, TF-IDF↔vector, none↔LLM, SQLite↔Postgres) that degrades gracefully when any dependency is absent.

---

## 📋 Product Requirements (PRD)

> Source of truth: [`docs/Halcyon_Credit_PRD.txt`](docs/Halcyon_Credit_PRD.txt) and the agile backlog. Summarised here.

| Section | Content |
|---------|---------|
| **Vision** | Every consumer-lending decision is fast, fair, policy-compliant, and fully explainable — without removing human accountability. |
| **Mission** | Give underwriters an AI copilot that assembles risk, policy, document, regulatory, and fairness evidence into one auditable recommendation. |
| **Goals** | (1) Evidence-backed recommendations; (2) never auto-approve on incomplete/biased evidence; (3) tamper-evident audit; (4) cost & latency within budget. |
| **Non-Goals** | LLM auto-decisions; live KYC/bureau integration (v1 mock); document OCR; multi-tenant SaaS; a heavyweight SPA framework. |
| **Target Users** | Credit underwriters / ops (primary); loan applicants (self-service status); compliance & risk officers (audit consumers). |
| **Personas** | *Priya, Underwriter* — needs evidence fast, hates re-keying. *Ravi, Compliance* — needs to reconstruct any past decision. *Alex, Applicant* — wants to submit and track a loan. |
| **Pain Points** | Slow manual policy lookup; unexplainable scores; bias exposure; no audit reconstruction. |
| **Functional Reqs** | FR-1 ingestion · FR-2 risk score · FR-4 policy retrieval · FR-3 policy engine · FR-5 documents · FR-7 fairness · FR-8 recommendation · FR-9 explanation · FR-10 audit. |
| **Non-Functional Reqs** | P95 latency ≤ 20s · cost/app under budget · tamper-evident audit · secrets from env only · graceful degradation. |
| **Success Metrics** | Acceptance rate ≥ 75% (target) · P95 latency · cost/app · fairness gap ≤ 5pp · 0% hallucination. |
| **Acceptance Criteria** | AC-1…AC-11 tracked in [`ACCEPTANCE_VERIFICATION.md`](docs/ACCEPTANCE_VERIFICATION.md): **8 PASS, 1 PARTIAL, 2 NOT MET** (each with documented remediation). |
| **Risks** | Model AUC below target (0.7755 vs 0.80); regulatory mock ≠ live; simulated pilot ≠ real underwriters. |
| **Future Scope** | Real KYC/bureau APIs, OCR, richer feature engineering to reach AUC 0.80, real human pilot, insert-only audit for all writes. |

<details>
<summary><b>🎯 Acceptance Criteria snapshot (click to expand)</b></summary>

| AC | Requirement | Status |
|----|-------------|--------|
| AC-2 | AUC ≥ 0.80, F1 ≥ 0.72 | ❌ **NOT MET** — AUC **0.7755**; F1 infeasible at ~8% prevalence (flagged as likely spec error). |
| AC-7 | 0% hallucination | ✅ **PASS** (0% by construction; grounded generator + eval harness). |
| AC-8 | Cost per app under budget | ✅ **PASS** — ~$0.02 (under both the $0.05 PRD and $0.08 story thresholds). |
| AC-11 | Pilot acceptance ≥ 75% | ⚠️ **PARTIAL / NOT MET (simulated)** — 58% in a 3×20 simulation; needs a real human pilot. |
| AC-1,3,4,5,6,9,10 | Ingestion, retrieval, audit, fairness, escalation, evidence, runbook | ✅ **PASS** |

</details>

---

## 🌐 System Overview

In plain English:

1. An **applicant** (or an **underwriter** on their behalf) submits a loan application. Ingestion normalises it and flags missing fields.
2. An **underwriter** opens the queue and clicks **Assess** on an application.
3. The backend runs a **six-component evidence pipeline**:
   - 🎯 **Risk** — LightGBM (or rule-based) probability → Low/Medium/High band.
   - 📚 **Policy** — RAG retrieves the governing clause(s) for the loan scheme; a deterministic engine evaluates machine-readable rules.
   - 📄 **Documents** — completeness + consistency checks.
   - 🏛️ **Regulatory** — identity/employment/tax/sanctions verdict (mock, with retry).
   - ⚖️ **Fairness** — is this scheme currently paused for a bias gap?
   - 💬 **Explanation** — a grounded, PII-safe narrative (LLM or deterministic).
4. A **recommendation** (Approve / Refer / Decline) is produced. **Guardrails** can force *Refer*: kill-switch on, cost breach, retrieval failure, incomplete evidence, scheme paused, low confidence, or unresolved regulatory.
5. The full **evidence chain + cost + policy version** is persisted as a `decision_record`, and an entry is appended to the **hash-chained audit log**.
6. The underwriter reviews the evidence panel and **Accepts** or **Overrides** (override requires a structured reason). The decision is **single and final**.

---

## 🏛️ High-Level Architecture (HLD)

```mermaid
flowchart TB
    subgraph Client["🖥️ Client"]
        UI["Vite + Vanilla JS UI<br/>(glassmorphism dark theme)"]
    end

    subgraph Edge["🌐 Edge (Docker)"]
        NGINX["nginx<br/>serves SPA + proxies /api"]
    end

    subgraph API["⚙️ FastAPI Backend (src/api)"]
        R["Routers<br/>(HTTP layer)"]
        S["Services<br/>(pure business logic)"]
        AUTH["JWT Auth + RBAC"]
    end

    subgraph AI["🧠 AI / ML Layer"]
        ML["LightGBM risk model<br/>src/risk_model"]
        RAG["RAG retrieval<br/>TF-IDF | Qdrant"]
        LLM["LLM explanation<br/>OpenRouter"]
    end

    subgraph Data["💾 Data Layer"]
        PG[("PostgreSQL / SQLite<br/>SQLAlchemy")]
        QD[("Qdrant<br/>vector store")]
        CORPUS["Policy corpus JSON<br/>(versioned)"]
    end

    UI --> NGINX --> R --> AUTH
    R --> S
    S --> ML
    S --> RAG --> QD
    S --> LLM
    S --> PG
    RAG --> CORPUS
```

### Request lifecycle (Assess)

```mermaid
flowchart LR
    A["POST /api/assess"] --> B{Kill-switch?}
    B -- on --> H["Refer to human review"]
    B -- off --> C["Score risk<br/>ML then rule fallback"]
    C --> D["Retrieve policy clauses<br/>vector then TF-IDF fallback"]
    D --> E["Evaluate policy rules"]
    E --> F["Verify docs + regulatory + fairness"]
    F --> G{"All 6 evidence<br/>components present<br/>and guardrails clear?"}
    G -- no --> H
    G -- yes --> I["Apply recommendation matrix"]
    I --> J["Persist decision_record<br/>+ append audit event"]
    J --> K["Underwriter Accept / Override"]
```

### Deployment flow

```mermaid
flowchart LR
    Dev["docker compose up --build"] --> WEB["web<br/>nginx + Vite build"]
    Dev --> APIC["api<br/>FastAPI + ML + RAG + LLM"]
    Dev --> PGC["postgres:16"]
    Dev --> QDC["qdrant:latest"]
    WEB -->|/api proxy| APIC
    APIC --> PGC
    APIC --> QDC
```

---

## 🔬 Low-Level Design (LLD)

### Module map

| Layer | Location | Responsibility |
|-------|----------|----------------|
| **Routers** | `src/api/routers/` | HTTP endpoints, auth dependencies, request/response validation. Thin — delegate to services. |
| **Services** | `src/api/services/` | Pure, testable business logic. No HTTP. This is where the intelligence lives. |
| **Models** | `src/api/models.py` | SQLAlchemy ORM tables. |
| **Schemas** | `src/api/schemas.py` | Pydantic request/response contracts. |
| **Auth** | `src/api/auth.py` | JWT create/verify, `get_current_user`, `require_ops` / `require_roles`. |
| **Settings** | `src/api/settings.py` | Secret access + per-tool least-privilege scopes. |
| **Risk model** | `src/risk_model/` | Training, tuning, preprocessing, SHAP, fairness, inference bridge. |
| **Tools** | `src/tools/` | Agent-style tool wrappers (`risk_scoring_tool.py`). |
| **Ingestion** | `scripts/ingestion/` | Standalone bulk-ingest CLI (loader, hasher, schema manager, db). |

### Key services (`src/api/services/`)

| Service | Role | Notable design |
|---------|------|----------------|
| `assessment.py` | **Orchestrator.** Composes all evidence → recommendation. | Recommendation matrix `(policy_status, risk_band) → action`; escalation cascade; evidence-completeness gate. |
| `scoring.py` | Rule-based risk score. | Debt-to-income, loan-to-income, employment tenure. Excludes `CODE_GENDER`/`DAYS_BIRTH`. |
| `retrieval.py` / `vector_retrieval.py` | RAG. | Identical return shape; vector path falls back to TF-IDF on error. |
| `policy_engine.py` | Deterministic rule evaluator. | A failed rule can never yield Approve. |
| `pii_redaction.py` | Trust-boundary gate. | Redacts + **blocks** residual-PII prompts. |
| `llm_explanation.py` | FR-9 narrative. | Grounded claims only; LLM never decides; deterministic fallback. |
| `explanation.py` | Deterministic grounded generator. | LLM stand-in; 0% hallucination by construction. |
| `hallucination_eval.py` | Release gate. | Faithfulness/hallucination harness. |
| `audit_log.py` | Tamper-evident log. | Append-only SHA-256 hash chain (`prev_hash → entry_hash`). |
| `cost_meter.py` | Budget guardrail. | Per-app cost estimate; hard cutoff. |
| `kill_switch.py` | Circuit breaker. | Global `SystemFlag`; degraded-mode routing. |
| `fairness_monitor.py` | Bias hard-block. | >5pp segment gap → `SchemePause`. |
| `regulatory.py` | KYC mock. | Exponential-backoff retry; escalate on exhaustion; **never fabricates**. |
| `document_service.py` | Docs. | Type validation, completeness, consistency. |
| `ops_metrics.py` | Dashboard aggregates. | Throughput, P95, cost, acceptance, override, fairness gap. |

**Control & error flow:** each guardrail is an ordered branch in `run_assessment` — the first breach short-circuits to *Refer* with a machine `escalation_reason_code`. Integration failures (ML libs absent, Qdrant down, no LLM key) **degrade to a lighter path** rather than raising. **Retry** lives in the regulatory client (exponential backoff). **Async:** FastAPI is async-capable; the assessment pipeline itself is synchronous and CPU-bound (no Celery/queue — `Not implemented`).

---

## 🔄 Data Flow — Request Lifecycle

```mermaid
sequenceDiagram
    participant U as Underwriter (UI)
    participant N as nginx
    participant API as FastAPI Router
    participant AS as assessment.py
    participant ML as risk_model
    participant RAG as retrieval
    participant LLM as llm_explanation
    participant DB as Database
    participant AUD as audit_log

    U->>N: POST /api/assess {application_id}
    N->>API: proxy /api
    API->>AS: run_assessment(app)
    AS->>ML: score (ML then rule fallback)
    ML-->>AS: probability + band
    AS->>RAG: retrieve clauses (vector then TF-IDF)
    RAG-->>AS: clauses + source_ids + version
    AS->>AS: policy engine + docs + regulatory + fairness
    AS->>LLM: grounded narrative (PII-redacted)
    LLM-->>AS: narrative (or deterministic fallback)
    AS->>DB: persist decision_record + evidence chain
    AS->>AUD: append hash-chained audit event
    AS-->>API: DecisionRecordOut
    API-->>U: recommendation + full evidence panel
    U->>API: POST /assessments/{id}/decision {accept|override}
    API->>DB: stamp final decision (single & final)
```

---

## 🗄️ Database Design

**ORM:** SQLAlchemy 2.0. **Engine:** SQLite (default, local) → PostgreSQL 16 (production) via `DATABASE_URL`.

```mermaid
erDiagram
    users ||--o{ applications : "owns (owner_id)"
    users ||--o{ decision_record : "decides"
    applications ||--o{ decision_record : "assessed_by"
    applications ||--o{ documents : "has"
    decision_record ||--o{ audit_event : "logged_in"

    users {
        int id PK
        string email UK
        string hashed_password
        string role "applicant | underwriter"
    }
    applications {
        int id PK
        string external_id UK
        int owner_id FK
        string loan_scheme
        float amt_income_total
        float amt_credit
        string status "COMPLETE | INCOMPLETE"
        text raw_row_json
    }
    decision_record {
        int id PK
        int application_id FK
        float risk_score
        string risk_band
        string recommendation
        text evidence_chain_json
        string policy_version
        bool evidence_complete
        float estimated_cost_usd
        string underwriter_action "accept | override"
        string underwriter_reason_code
    }
    audit_event {
        int id PK
        string event_type "decision | external_call"
        text payload_json
        string prev_hash
        string entry_hash "SHA-256 chain"
    }
    documents {
        int id PK
        int application_id FK
        string doc_type
        float declared_income
    }
    scheme_pause {
        int id PK
        string scheme
        float gap_pp
        datetime released_at "NULL = active pause"
    }
    system_flag {
        int id PK
        string key UK
        string value
    }
    policy_corpus_version {
        int id PK
        string version
        int clause_count
    }
```

**Constraints & indexes:** unique indexes on `users.email`, `applications.external_id`, `system_flag.key`; foreign keys from `applications.owner_id`, `decision_record.application_id` / `underwriter_user_id`, `documents.application_id`. **Audit integrity is application-enforced** via the hash chain (any edit to a historical row breaks every subsequent `entry_hash`). Tables are auto-created at startup (`Base.metadata.create_all`); a formal migration tool (Alembic) is `Not implemented`.

---

## 🔌 API Documentation

Base URL: `/api` · Auth: `Authorization: Bearer <JWT>` (except register/login/health). Ops-only routes return **403** without the `underwriter` role. Interactive docs at **`/docs`** (Swagger) and **`/redoc`** — auto-generated by FastAPI.

### 🔑 Auth & Users

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| `POST` | `/api/auth/register` | Public | Register; body `{email, password, role?}` → returns JWT. |
| `POST` | `/api/auth/login` | Public | OAuth2 password form → returns JWT. |
| `GET`  | `/api/users/me` | User | Current user profile. |

### 📥 Applications

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| `POST` | `/api/applications/ingest` | User | Ingest a raw application row (Home-Credit-shaped fields). |
| `GET`  | `/api/applications` | **Ops** | List all applications. |
| `GET`  | `/api/applications/my` | Applicant | Own applications + decision status (Pending/Approved/Denied). |
| `GET`  | `/api/applications/{id}` | User | Application detail (owner or ops). |

### 🎯 Scoring, Policy & Regulatory

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| `POST` | `/api/score` | User | Risk probability + band for an application. |
| `POST` | `/api/policy/retrieve` | User | RAG clause retrieval (scheme-filtered). |
| `POST` | `/api/policy/evaluate` | User | Deterministic policy-rule evaluation. |
| `GET`  | `/api/policy/versions` | User | Loaded corpus versions. |
| `GET`  | `/api/policy/corpus` | User | Full active corpus. |
| `GET`  | `/api/policy/clause/{clause_id}` | User | Single clause source text + version (citation drill-down). |
| `POST` | `/api/policy/reindex` | User | Reindex the policy corpus. |
| `POST` | `/api/regulatory/verify` | User | Identity/employment/tax/sanctions verdict (mock). |

### 🧾 Assessments & Decisions

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| `POST` | `/api/assess` | **Ops** | Run the full evidence pipeline → recommendation + evidence chain. |
| `GET`  | `/api/assessments/{id}` | User | Retrieve a decision record. |
| `GET`  | `/api/assessments/{id}/explanation` | User | Grounded / LLM narrative for a decision. |
| `POST` | `/api/assessments/{id}/decision` | **Ops** | Accept or override (override needs `reason_code` + note). **Single & final** (409 if re-decided). |
| `GET`  | `/api/assessments/queue/human-review` | **Ops** | Escalation queue with reason codes. |
| `GET`  | `/api/assessments/metrics/override-rate` | **Ops** | Override-rate metric. |

### 📄 Documents · ⚖️ Fairness · 🛠️ Ops

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| `POST` | `/api/applications/{id}/documents` | User | Upload a supporting document (type-validated). |
| `GET`  | `/api/applications/{id}/documents` | User | List documents. |
| `GET`  | `/api/applications/{id}/documents/verify` | User | Completeness + consistency check. |
| `POST` | `/api/fairness/monitor` | **Ops** | Recompute segment approval-rate deltas. |
| `GET`  | `/api/fairness/paused-schemes` | **Ops** | Schemes paused by the hard-block. |
| `POST` | `/api/fairness/release/{scheme}` | **Ops** | Release a paused scheme. |
| `GET`  | `/api/fairness/proxy-leakage` | **Ops** | Proxy-leakage audit report. |
| `GET`  | `/api/ops/dashboard` | **Ops** | Throughput, P95, cost, acceptance, override, fairness gap. |
| `GET` / `POST` | `/api/ops/kill-switch` | **Ops** | Read / toggle the global kill-switch. |
| `GET`  | `/api/ops/audit/verify` | **Ops** | Verify the audit hash chain integrity. |
| `GET`  | `/api/ops/audit/reconstruct/{id}` | **Ops** | Reconstruct a past decision from the audit log. |
| `GET`  | `/api/health` | Public | Liveness probe. |

<details>
<summary><b>Example — run an assessment (click to expand)</b></summary>

```bash
# 1. Login
TOKEN=$(curl -s -X POST http://localhost/api/auth/login \
  -d "username=ops@halcyon.com&password=secret" | jq -r .access_token)

# 2. Assess an application
curl -s -X POST http://localhost/api/assess \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"application_id": 1}' | jq
```

```jsonc
// Response (shape) — DecisionRecordOut
{
  "id": 12,
  "recommendation": "Refer",
  "risk_score": 0.41,
  "risk_band": "Medium",
  "policy_version": "1.0",
  "escalation_flag": true,
  "escalation_reason_code": "low_model_confidence",
  "evidence_complete": true,
  "estimated_cost_usd": 0.02,
  "evidence_chain_json": { "risk_score": {}, "policy_clauses": [], "...": {} }
}
```
</details>

---

## 🧠 The AI / RAG Pipeline

```mermaid
flowchart LR
    Q["Application + scheme"] --> EMB["Embed query<br/>fastembed BGE-small"]
    EMB --> SR["Vector search<br/>Qdrant, cosine"]
    SR --> FL{"score >= floor<br/>(0.35)?"}
    FL -- no --> FAIL["retrieval_failed to Refer"]
    FL -- yes --> CTX["Top clauses<br/>+ source_id + version"]
    CTX --> PE["Policy engine<br/>machine rules"]
    PE --> CLAIMS["Grounded claims"]
    CLAIMS --> PII["PII redaction gate"]
    PII --> GEN["LLM narrative<br/>OpenRouter"]
    GEN --> EVAL["Hallucination eval"]
```

| Stage | Default | Production | Notes |
|-------|---------|-----------|-------|
| **Chunking** | Per-clause (corpus is pre-chunked JSON) | Same | 6 schemes, 21 clauses, each with a machine-readable `rule`. |
| **Embedding** | — (TF-IDF) | `BAAI/bge-small-en-v1.5` via **fastembed** (local ONNX, no API key) | — |
| **Storage** | In-memory TF-IDF | **Qdrant** (embedded `:memory:` or server) | Lazy idempotent indexing (uuid5 ids). |
| **Retrieval** | Cosine over TF-IDF | Cosine over embeddings | Scheme-filtered; `VECTOR_SCORE_FLOOR` gates failure. |
| **Prompt assembly** | Grounded claims only | Same | LLM sees only source-tagged claims. |
| **Generation** | Deterministic grounded text | **OpenRouter** `openai/gpt-4o-mini` | LLM explains only, never decides. |
| **Evaluation** | Hallucination/faithfulness harness | Same | Blocks release below threshold; **0% by construction**. |

> The two RAG backends return an **identical shape**, so the policy engine, assessment, and UI citations are backend-agnostic. Vector automatically falls back to TF-IDF on any error.

---

## 🤖 The ML Pipeline

**Dataset:** Kaggle **Home Credit Default Risk** (~307K rows). **Champion model:** `models/production/risk_model_v1.pkl`.

```mermaid
flowchart LR
    RAW["application_train.csv<br/>+ 5 aux tables"] --> AUX["Aux aggregates<br/>bureau, prev-app,<br/>installments, POS, cc"]
    AUX --> FE["Feature engineering<br/>54 features"]
    FE --> EXCL["Exclude protected:<br/>CODE_GENDER, DAYS_BIRTH,<br/>AGE_YEARS"]
    EXCL --> TRAIN["Train LightGBM<br/>hardened"]
    TRAIN --> TUNE["Threshold tuning<br/>+ SHAP"]
    TUNE --> SEL["select_model.py<br/>to production/"]
    SEL --> INFER["predict_from_features()<br/>inference bridge"]
```

| Aspect | Value |
|--------|-------|
| **Champion** | LightGBM (Hardened, Aux Features) — `v1_hardened` |
| **Held-out ROC-AUC** | **0.7755** (up from 0.7654 baseline; below the 0.80 stretch AC) |
| **Best F1** | 0.3285 @ threshold 0.66 (F1 capped by ~8% default prevalence) |
| **Feature count** | 54 (aux aggregates + core) |
| **Protected features** | `CODE_GENDER`, `DAYS_BIRTH`, `AGE_YEARS` **excluded** (fairness A-8b; test-enforced) |
| **Threshold policy** | conservative 0.36 · balanced 0.66 · revenue-friendly 0.75 |
| **Explainability** | SHAP top-5 contributors with human labels + direction |
| **Experiment tracking** | MLflow (`mlflow.db`, `mlruns/`) |
| **Candidates tracked** | Logistic Regression (baseline, AUC 0.744), XGBoost v1/v2, LightGBM tuned — in `models/` |

**Fairness & leakage:** `proxy_leakage.py` computes Cramér's V / correlation ratio; key finding — `OCCUPATION_TYPE` materially correlates with gender (V≈0.40), documented in `reports/ml/PROXY_LEAKAGE.md`.

**Inference bridge:** `predict_from_features` scores a brand-new application from its raw fields (`Application.raw_row_json`) through the exact trained pipeline (aux aggregates 0-filled for applicants with no history). Enabled with `RISK_SCORER=ml`; falls back to rule-based otherwise. The chosen scorer is recorded in the evidence chain.

---

## 📁 Folder Structure

```text
credit-arbiter/
├── src/
│   ├── api/                      # FastAPI backend
│   │   ├── main.py               # App factory, CORS, router wiring, /health
│   │   ├── auth.py               # JWT + RBAC (require_ops, require_roles)
│   │   ├── database.py           # SQLAlchemy engine/session (SQLite|Postgres)
│   │   ├── models.py             # ORM tables (User, Application, DecisionRecord, AuditEvent...)
│   │   ├── schemas.py            # Pydantic request/response contracts
│   │   ├── settings.py           # Secret access + per-tool least-privilege scopes
│   │   ├── routers/              # HTTP layer (thin)
│   │   │   ├── auth · applications · scoring · policy · regulatory
│   │   │   └── assessments · documents · fairness · ops
│   │   └── services/             # Business logic (pure, testable)
│   │       ├── assessment.py        # Orchestrator: evidence -> recommendation
│   │       ├── scoring.py           # Rule-based risk scorer
│   │       ├── retrieval.py         # TF-IDF RAG
│   │       ├── vector_retrieval.py  # Qdrant + embeddings RAG
│   │       ├── policy_engine.py     # Deterministic rule evaluator
│   │       ├── llm_explanation.py   # OpenRouter narrative (FR-9)
│   │       ├── explanation.py       # Deterministic grounded generator
│   │       ├── pii_redaction.py     # Trust-boundary PII gate
│   │       ├── audit_log.py         # SHA-256 hash-chain audit
│   │       ├── cost_meter.py        # Per-app cost + hard cutoff
│   │       ├── kill_switch.py       # Circuit breaker / degraded mode
│   │       ├── fairness_monitor.py  # >5pp gap -> scheme pause
│   │       ├── regulatory.py        # KYC mock + retry
│   │       ├── document_service.py  # Upload + verify
│   │       ├── ops_metrics.py       # Dashboard aggregates
│   │       ├── hallucination_eval.py# Release gate
│   │       └── retrieval_monitor.py # Context precision/recall alerts
│   ├── risk_model/               # ML: train / tune / preprocess / SHAP / fairness / predict
│   └── tools/                    # Agent-style tool wrappers (risk_scoring_tool)
├── ui/                           # Vite + Vanilla JS + CSS (glassmorphism)
│   ├── main.js · style.css · index.html · package.json
├── scripts/                      # index_policies, seed_db, load_test, run_pilot, ingestion CLI
├── tests/                        # 117 pytest tests across 23 files
├── data/                         # Policy corpus (v0.1, v1.0), eval sets, uploads, Home Credit data
├── models/                       # baseline · xgboost · lightgbm · production (champion + metadata)
├── config/                       # sources.yaml (ingestion config)
├── deploy/                       # Dockerfile.web (nginx + Vite build) + nginx.conf
├── docs/                         # PRD, RUNBOOK, DEPLOY_AWS, PILOT_RESULTS, ACCEPTANCE, HLD.png
├── notebooks/                    # Jupyter ML experimentation
├── reports/                      # ML reports (SHAP, proxy leakage, plots), ops load test
├── Dockerfile                    # Backend image (API + ML + RAG + LLM)
├── docker-compose.yml            # Full stack: web -> api -> postgres + qdrant
├── requirements*.txt             # base / -ml / -rag / -llm / -ingestion (layered installs)
├── claude.md                     # Engineering context & sprint log (deep-dive reference)
└── LICENSE                       # MIT
```

---

## 🧰 Tech Stack

| Category | Technology |
|----------|-----------|
| **Backend** | Python 3.12, FastAPI 0.111, Uvicorn |
| **Frontend** | Vite 8, Vanilla JavaScript, Vanilla CSS (glassmorphism dark theme), Inter font |
| **Database** | PostgreSQL 16 (prod) · SQLite (dev), SQLAlchemy 2.0, `psycopg` 3 |
| **Vector DB** | Qdrant (embedded `:memory:` or server) |
| **Embeddings** | fastembed (local ONNX) · `BAAI/bge-small-en-v1.5` |
| **LLM** | OpenRouter (OpenAI-compatible) · `openai` SDK · default `openai/gpt-4o-mini` |
| **ML** | LightGBM 4.6, scikit-learn 1.5, XGBoost, SHAP, pandas, numpy, pyarrow, joblib |
| **Experiment tracking** | MLflow |
| **Auth** | PyJWT, passlib[bcrypt], OAuth2 password flow |
| **Infra / DevOps** | Docker, Docker Compose, nginx (SPA + `/api` proxy) |
| **Testing** | pytest (117 tests), DeepEval (`.deepeval`) |
| **Cloud** | AWS (documented in `docs/DEPLOY_AWS.md`) |
| **Config** | python-dotenv, PyYAML, env-switched integrations |

---

## 🏗️ Architecture Decisions

<details open>
<summary><b>Interview-grade rationale for every major choice</b></summary>

| Decision | Why chosen | Why not the alternative |
|----------|-----------|------------------------|
| **FastAPI** over Flask | Native async, Pydantic validation, auto OpenAPI docs, dependency-injection for auth/DB. | Flask needs extensions for all of the above; no first-class typing/validation. |
| **LightGBM** over XGBoost / LogReg | Best held-out AUC (0.7755) on tabular Home Credit; handles missingness & categoricals well; fast. | LogReg baseline AUC 0.744; XGBoost tracked but LightGBM won on this data. |
| **Qdrant** for vectors | Runs **embedded in-memory** for dev *and* as a server for prod — same client, no infra tax to start. | pgvector couples the vector store to Postgres; Pinecone is a paid managed dependency. |
| **fastembed** for embeddings | Local ONNX, **no API key, no torch** — keeps the image light and offline-capable. | OpenAI embeddings add cost + a network hop + a key requirement. |
| **OpenRouter** for the LLM | One OpenAI-compatible endpoint, many models, easy to swap; LLM used **only for narrative**. | Locking to one vendor SDK; the LLM never touches the decision, so vendor lock-in risk is low anyway. |
| **TF-IDF default, vector opt-in** | A 21-clause corpus doesn't *need* embeddings; TF-IDF is zero-dependency and deterministic for tests. | Forcing Qdrant everywhere would bloat the default install and slow CI. |
| **SQLAlchemy + Postgres/SQLite** | One ORM, two engines: SQLite for frictionless dev, Postgres for prod — switch via `DATABASE_URL`. | MongoDB: the data is relational (apps → decisions → audit), integrity matters more than schema flexibility. |
| **JWT (PyJWT)** | Stateless, standard, works cleanly with FastAPI's OAuth2 dependency. | Server-side sessions add state; overkill for this scope. |
| **Deterministic policy engine** (not LLM) | Credit decisions must be reproducible & auditable; an LLM can't be a decision-maker of record. | LLM auto-decisions are explicitly a **Non-Goal** (hallucination risk, legal exposure). |
| **SHA-256 hash-chain audit** | Tamper-evident with zero external infra; any historical edit breaks the chain. | A blockchain/ledger DB is disproportionate for the requirement. |
| **Vanilla JS + Vite** (no React) | The UI is a focused internal tool; raw JS keeps the bundle tiny and dependency-free. | React/Next add build weight and a framework the POC doesn't need. |
| **Env-switched integrations w/ graceful fallback** | The app runs on a laptop *and* scales to the full stack; every integration degrades safely. | Hard dependencies would make the project un-runnable without Postgres/Qdrant/an LLM key. |
| **Celery / message queue** | **`Not implemented`** — the assessment path is synchronous and fast (P95 ≈ 0.34s in-process); a queue was unnecessary for the current scope. | — |

</details>

---

## ⚙️ Installation & Setup

### Prerequisites

| Tool | Version | Needed for |
|------|---------|-----------|
| Python | 3.12 | Backend |
| Node.js | 22+ | Frontend (Vite) |
| Docker + Compose | latest | Full-stack one-command run |
| An OpenRouter API key | — | LLM explanations (optional) |

### Option A — 🐳 Full stack with Docker (recommended)

```bash
git clone <your-repo-url> && cd Credit-arbiter/credit-arbiter

# Provide secrets via env (never bake them into an image)
export OPENROUTER_API_KEY=sk-or-...        # optional; omit -> deterministic explanations
export JWT_SECRET_KEY=$(openssl rand -hex 32)

docker compose up -d --build
# -> UI at http://localhost   |   API proxied at http://localhost/api
```

This starts four services: **web** (nginx + built Vite app), **api** (FastAPI + ML + RAG + LLM), **postgres:16**, and **qdrant:latest**.

### Option B — 🧪 Lightweight local (defaults, no Docker)

```bash
cd credit-arbiter
python -m venv .venv && source .venv/bin/activate     # Windows: .venv\Scripts\activate
pip install -r requirements.txt                       # base only
cp .env.example .env                                  # then set RISK_SCORER=rule, RETRIEVER=tfidf, LLM_PROVIDER=none
uvicorn src.api.main:app --reload --port 8000         # SQLite + TF-IDF + rule-based + deterministic text
```

Frontend:

```bash
cd ui && npm install && npm run dev
```

### Option C — 🚀 Production-like without Docker

```bash
pip install -r requirements-ml.txt -r requirements-rag.txt -r requirements-llm.txt
docker compose up -d postgres qdrant                  # just the datastores
python -m scripts.index_policies                      # index the corpus into Qdrant
uvicorn src.api.main:app --port 8000
```

> **Windows / Linux / Mac:** commands are identical except venv activation (`.venv\Scripts\activate` on Windows). Docker Compose behaves the same across all three.

---

## 🔑 Environment Variables

Copy `.env.example` → `.env`. All values have safe defaults **except secrets**.

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `JWT_SECRET_KEY` | ✅ | `change-me...` | HMAC signing key for JWTs. **Set a long random value in prod.** |
| `JWT_ALGORITHM` | — | `HS256` | JWT signing algorithm. |
| `JWT_ACCESS_TOKEN_EXPIRE_MINUTES` | — | `1440` | Token lifetime (minutes). |
| `DATABASE_URL` | — | `sqlite:///./local_dev.db` | SQLAlchemy URL. Postgres: `postgresql+psycopg://halcyon:halcyon@localhost:5432/halcyon`. |
| `RISK_SCORER` | — | `rule` | `rule` or `ml` (needs `requirements-ml.txt` + the model file). |
| `RETRIEVER` | — | `tfidf` | `tfidf` or `vector` (needs `requirements-rag.txt`). |
| `QDRANT_URL` | — | `:memory:` | Qdrant server URL, or embedded in-memory. |
| `QDRANT_COLLECTION` | — | `halcyon_policies` | Collection name. |
| `EMBEDDING_MODEL` | — | `BAAI/bge-small-en-v1.5` | fastembed model. |
| `VECTOR_SCORE_FLOOR` | — | `0.35` | Cosine floor below which retrieval = failed. |
| `LLM_PROVIDER` | — | `none` | `none` or `openrouter`. |
| `OPENROUTER_API_KEY` | ⚠️ if LLM on | — | OpenRouter secret. |
| `OPENROUTER_MODEL` | — | `openai/gpt-4o-mini` | Explanation model. |
| `OPENROUTER_BASE_URL` | — | `https://openrouter.ai/api/v1` | API base. |

> The shipped `.env.example` enables the **production profile** (`RISK_SCORER=ml`, `RETRIEVER=vector`, `LLM_PROVIDER=openrouter`). For the lightweight path, switch these to `rule` / `tfidf` / `none`.

---

## ▶️ Running the Project

| Target | Command |
|--------|---------|
| **Backend (dev)** | `uvicorn src.api.main:app --reload --port 8000` |
| **Frontend (dev)** | `cd ui && npm run dev` |
| **Full stack** | `docker compose up -d --build` |
| **Index policies (vector)** | `python -m scripts.index_policies` |
| **Seed database** | `python -m scripts.seed_db` |
| **Load test** | `python -m scripts.load_test` → `reports/ops/load_test.json` |
| **Simulated pilot** | `python -m scripts.run_pilot` → `docs/PILOT_RESULTS.md` |
| **Train the champion** | `python -m src.risk_model.train_hardened` |
| **Workers / scheduler** | **`Not implemented`** (no Celery/cron) |

---

## 🧪 Testing

**117 tests** across **23 files** (`tests/`), run with pytest (`pytest.ini`: `pythonpath = .`).

```bash
pytest                                   # full suite
pytest tests/test_assessment.py -v       # one module
pytest -k "fairness or audit"            # by keyword
```

| Area | Test file(s) |
|------|-------------|
| Auth & RBAC | `test_rbac.py`, `test_api_endpoints.py` |
| Assessment & escalation | `test_assessment.py`, `test_escalation_evidence.py` |
| ML & inference | `test_risk_model.py`, `test_ml_inference.py`, `test_proxy_leakage.py` |
| RAG & retrieval | `test_retrieval.py`, `test_retrieval_accuracy.py`, `test_retrieval_monitor.py` |
| Policy | `test_policy_engine.py`, `test_policy_versioning.py` |
| Guardrails | `test_audit_and_guardrails.py`, `test_pii_redaction.py`, `test_secrets_and_ops.py` |
| Fairness | `test_fairness_monitor.py` |
| Documents & regulatory | `test_documents.py`, `test_regulatory.py` |
| Explanations | `test_explanation_eval.py` |
| Integrations | `test_integrations.py`, `test_scoring.py`, `test_ingestion.py`, `test_sprint3_api.py` |

> Coverage % is not formally measured in-repo; **`Coverage report: Not implemented`**. Notable enforced invariants include *protected features excluded from the model*, *audit chain is tamper-evident*, and *no secret literals in code*.

---

## 🚢 Deployment

**Primary target:** AWS (single EC2 host running `docker compose`) — full guide in [`docs/DEPLOY_AWS.md`](docs/DEPLOY_AWS.md). Operational procedures (deploy/rollback/kill-switch/on-call) in [`docs/RUNBOOK.md`](docs/RUNBOOK.md); restart flows in [`docs/RESTART_GUIDE.md`](docs/RESTART_GUIDE.md).

```mermaid
flowchart LR
    User(("User")) -->|:80| C1
    subgraph EC2["AWS EC2 host"]
        direction TB
        C1["web (nginx :80)"] --> C2["api (uvicorn :8000)"]
        C2 --> C3[("postgres :5432")]
        C2 --> C4[("qdrant :6333")]
    end
```

| Platform | Status |
|----------|--------|
| **Docker Compose** | ✅ First-class (`docker-compose.yml`) |
| **AWS (EC2 + Compose)** | ✅ Documented (`docs/DEPLOY_AWS.md`) |
| **nginx** | ✅ SPA serving + `/api` reverse proxy (`deploy/nginx.conf`) |
| Azure / GCP / Railway / Render | ⚠️ Not documented — the Docker image is portable to any of them, but no platform-specific config is committed. |
| Kubernetes | ❌ `Not implemented` (no manifests/Helm charts) |
| CI/CD | ❌ `Not implemented` (no committed GitHub Actions / pipeline) |

---

## 🔒 Security

| Control | Implementation |
|---------|----------------|
| **Authentication** | JWT (HS256) via PyJWT; bcrypt password hashing (passlib). |
| **Authorization** | RBAC — `applicant` sees only own applications (`owner_id`); ops-only routes gated by `require_ops` (403). |
| **Secrets** | Read **from env only** (`settings.get_secret`); a test scans for secret literals in code. |
| **Least privilege** | Per-tool scopes (`TOOL_SCOPES`); a tool cannot exceed its declared actions (`ScopeError`). |
| **PII protection** | Redaction gate strips SSN/DOB/account/card/email/phone and **blocks** residual-PII prompts before any LLM call. |
| **Audit** | Append-only SHA-256 hash chain; tamper-evident; external calls logged. |
| **Input validation** | Pydantic schemas on every endpoint; document type validation; override reason-code enum. |
| **Rate limiting** | ⚠️ `Not implemented` (no limiter middleware). |
| **HTTPS / TLS** | ⚠️ Terminate at the edge (ALB/nginx) in prod — not configured in-repo. |
| **CORS** | Currently `allow_origins=["*"]` — **restrict to the frontend origin in production** (noted in `main.py`). |

> [!CAUTION]
> The dev defaults (`allow_origins=["*"]`, fallback JWT secret, `halcyon:halcyon` Postgres creds) are for **local development only**. Harden all three before any real deployment.

---

## 📈 Observability & Ops

| Concern | Implementation |
|---------|----------------|
| **Health check** | `GET /api/health` (liveness); Postgres has a compose healthcheck. |
| **Ops dashboard** | `GET /api/ops/dashboard` + UI panel — throughput, P95, cost/app, acceptance, override, fairness gap with thresholds/alerts. |
| **Load test / P95** | `scripts/load_test.py` → `reports/ops/load_test.json` (50-concurrent, P95 ≈ 0.34s, 0 errors, per-stage attribution). |
| **Retrieval monitoring** | `retrieval_monitor.py` — context precision/recall + failure-rate alert (currently 100/100/0). |
| **Cost metering** | Per-app cost persisted on each decision; hard cutoff → human fallback. |
| **Audit reconstruction** | `GET /api/ops/audit/verify` + `/audit/reconstruct/{id}`. |
| **Experiment tracking** | MLflow (`mlflow.db`, `mlruns/`). |
| **Logging** | Python `logging` (`halcyon.*` loggers). Structured tracing / Prometheus / Grafana: `Not implemented`. |

---

## 🎨 Design Patterns

| Pattern | Where |
|---------|-------|
| **Layered architecture** | Routers (HTTP) → Services (domain) → Models (persistence). |
| **Strategy** | Risk scorer (`rule` \| `ml`), retriever (`tfidf` \| `vector`), explainer (`none` \| `openrouter`) — selected by env. |
| **Adapter** | `vector_retrieval` mirrors `retrieval`'s return shape; `llm_explanation` adapts OpenRouter to the grounded-claims contract. |
| **Dependency Injection** | FastAPI `Depends` for DB sessions, current user, `require_ops`. |
| **Repository (light)** | Services encapsulate all DB access; routers never query the ORM directly for logic. |
| **Circuit Breaker** | Kill-switch + degraded-mode routing. |
| **Chain of Responsibility** | Ordered guardrail cascade in `run_assessment`. |
| **Factory** | `require_roles(*roles)` returns a dependency; app factory in `main.py`. |
| **Graceful degradation / Null Object** | Every integration falls back to a lighter path when its deps/keys are absent. |

**Coding standards:** services are pure and unit-testable; protected features are centralised in `config.py` (single source of truth); secrets never hard-coded; docstrings map code to user stories (US-xxx) and requirements (FR-x).

---

## 🗺️ Roadmap

```mermaid
timeline
    title Halcyon Credit — Delivery
    Sprint 1 : Auth + RBAC : Ingestion : Rule scorer : TF-IDF RAG : Recommendation + audit
    Sprint 2 : Hardened LightGBM : SHAP : Multi-scheme corpus : Policy engine : PII gate
    Sprint 3 : Documents + verify : Regulatory mock : Fairness hard-block : Escalation : Evidence panel
    Sprint 4 : Immutable audit chain : Cost cutoff : Load test : Kill-switch : Ops dashboard : Pilot
    Future   : Reach AUC 0.80 : Live KYC/bureau : OCR : Real human pilot : CI/CD + K8s
```

**Future features (not yet built):** raise AUC to 0.80 via Kaggle-scale FE; live KYC/bureau integration; document OCR; a real human-underwriter pilot; insert-only audit for all writes; CI/CD + Kubernetes; rate limiting.

---

## ⚠️ Known Limitations

> An honest list, mirrored from `claude.md` and `ACCEPTANCE_VERIFICATION.md`.

- 📉 **ML AUC 0.7755 < 0.80 target**; F1 target infeasible at ~8% default prevalence (likely a spec error, flagged to PO).
- 🏛️ **Regulatory services are deterministic mocks** (hash-based verdicts + transient-outage simulation) — no live KYC/bureau in v1.
- 📄 **Document OCR out of scope** — `declared_name` / `declared_income` supplied as structured metadata.
- 👥 **Pilot is simulated** (58% acceptance vs 75% target); needs real underwriters.
- 🧾 **`decision_record` underwriter fields updated in place** (the separate insert-only chain is `audit_event`).
- ⚡ **Load test & cost are in-process** (no network/LLM) — re-certify on the deployed stack.
- 🚫 **No CI/CD, no rate limiting, no K8s, no Alembic migrations, no worker queue** — all `Not implemented`.
- 🌗 **UI is dark-mode only**, Vanilla JS (no committed `node_modules`; `npm install` required).

---

## 🛠️ Troubleshooting

<details>
<summary><b>Common issues & fixes</b></summary>

| Symptom | Cause | Fix |
|---------|-------|-----|
| ML scorer silently falls back to rule-based | `requirements-ml.txt` not installed or `models/production/risk_model_v1.pkl` missing | Install ML deps + ensure the model file exists; set `RISK_SCORER=ml`. |
| Vector retrieval falls back to TF-IDF | Qdrant unreachable or `requirements-rag.txt` missing | Start Qdrant / set `QDRANT_URL`; install RAG deps; run `python -m scripts.index_policies`. |
| LLM narrative is deterministic, not fluent | `LLM_PROVIDER != openrouter` or `OPENROUTER_API_KEY` unset | Set both; install `requirements-llm.txt`. |
| `libgomp` error with LightGBM/fastembed | System OpenMP lib missing | Docker image installs `libgomp1`; on bare metal `apt-get install libgomp1`. |
| 403 on ops endpoints | Logged in as `applicant` | Register/login with `role=underwriter`. |
| UI can't reach API | Wrong API base | In Docker the UI auto-selects same-origin `/api`; in dev point it at `http://localhost:8000`. |
| `npm run build` fails | `node_modules` not installed | `cd ui && npm install`. |
</details>

---

## ❓ FAQ

<details>
<summary><b>20 questions — click to expand</b></summary>

1. **Does the LLM make the lending decision?** No. The LLM only writes the *explanation*; Approve/Refer/Decline is deterministic (policy + model + evidence).
2. **What happens if the LLM/Qdrant/ML libs are missing?** Each degrades gracefully — deterministic text, TF-IDF, and the rule-based scorer respectively.
3. **Why is the AUC only 0.7755?** Reaching 0.80 on Home Credit needs Kaggle-scale feature engineering across all tables; documented as a stretch goal.
4. **Is the regulatory check real?** No — it's a deterministic mock with retry/backoff. Live KYC/bureau is future scope.
5. **How is bias prevented?** Protected features (gender, DOB, age proxy) are excluded from the model; a >5pp segment gap pauses the scheme.
6. **What is the evidence chain?** The six components (risk, risk factors, policy clauses, documents, regulatory, fairness) persisted per decision.
7. **What makes the audit tamper-evident?** An append-only SHA-256 hash chain — editing any historical row breaks every later hash.
8. **Can a decision be changed after the fact?** No — decisions are single & final (409 on re-decide).
9. **What DB does it use?** SQLite for dev, PostgreSQL for prod, via `DATABASE_URL`.
10. **Which vector DB and embeddings?** Qdrant + fastembed (`BAAI/bge-small-en-v1.5`), local ONNX, no API key.
11. **Why not React?** The internal underwriter tool is small; Vanilla JS keeps it dependency-free and fast.
12. **How are secrets handled?** From env only; a test scans for hard-coded secret literals.
13. **What's the P95 latency?** ≈0.34s in an in-process 50-concurrent load test (re-certify on the deployed stack).
14. **What's the cost per app?** ~$0.02, under both the $0.05 PRD and $0.08 story thresholds.
15. **How does escalation work?** Low confidence (<0.60), fairness pause, cost breach, retrieval failure, missing evidence, or unresolved regulatory → human-review queue with a reason code.
16. **What roles exist?** `applicant` (own apps) and `underwriter` (ops: all apps + tools).
17. **How do I switch to the production stack?** Env vars: `RISK_SCORER=ml`, `RETRIEVER=vector`, `LLM_PROVIDER=openrouter`, `DATABASE_URL=postgres...`.
18. **Is there CI/CD?** Not implemented in-repo.
19. **How many tests?** 117 across 23 files.
20. **Is there a hosted demo?** Not implemented — run locally with `docker compose up -d --build`.
</details>

---


## 🤝 Contributing

Contributions welcome! This repo uses a feature-branch workflow (`feature/*` → PR → `main`).

```bash
git checkout -b feature/your-feature
# make changes
pytest                                 # keep all 117 tests green
git commit -m "feat: your change"      # Conventional Commits
git push origin feature/your-feature
```

**Guidelines:** keep services pure & tested; never hard-code secrets; add/adjust a test for any behaviour change; mark anything unfinished honestly (`Not implemented`).

---

## 📦 Versioning

Follows **[Semantic Versioning](https://semver.org)** — `MAJOR.MINOR.PATCH`. Current: **v1.0.0** (Sprints 1–4 complete). Commits follow **Conventional Commits** (`feat:`, `fix:`, `docs:`, `chore:`).

<details>
<summary><b>Changelog template</b></summary>

```markdown
## [1.0.0] - 2026-07
### Added
- Full evidence pipeline (ML + RAG + LLM), fairness hard-block, immutable audit, ops dashboard.
### Changed
- Champion model upgraded to hardened LightGBM (AUC 0.7654 -> 0.7755).
### Known Issues
- AUC below 0.80 target; regulatory + pilot are mocks/simulated.
```
</details>

---

## 📜 License & Credits

**License:** [MIT](LICENSE) © 2026 AbeKuriachan

**Credits & Acknowledgements:**
- 📊 **Home Credit Default Risk** dataset (Kaggle) — the ML training corpus.
- ⚡ **FastAPI**, **LightGBM**, **Qdrant**, **fastembed**, **OpenRouter**, **SHAP**, **SQLAlchemy** — the shoulders this stands on.
- 📚 Requirements & backlog authored in `docs/` (PRD, agile backlog).

**Contact & Support:** open a GitHub Issue for bugs/questions, or a Discussion for design proposals. PRs welcome.

---

## 🌟 Star History · 👥 Contributors

```text
[ Star History chart placeholder — e.g. https://star-history.com/#<owner>/<repo>&Date ]
[ Contributors grid placeholder — e.g. https://contrib.rocks/image?repo=<owner>/<repo> ]
```

---

<div align="center">

### 🏦 Halcyon Credit — *Agentic Underwriting, with a human always in the loop.*

**Built with** FastAPI · LightGBM · Qdrant · OpenRouter · Docker

*Evidence-backed. Fair by design. Fully auditable.*

<sub>⭐ If this project helped you, consider giving it a star.</sub>

</div>
