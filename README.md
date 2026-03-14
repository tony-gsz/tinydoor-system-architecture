# TinyDOOR System Architecture

A **sanitized architecture case study** of a hybrid AI-enabled story generation platform built around **asynchronous workflows, cloud-native storage, and production-minded request lifecycle management**.

> **Note:** This repository does **not** contain proprietary source code or internal production assets.  
> It is a **portfolio-safe system design showcase** based on real engineering work, focused on architecture, backend workflows, cloud infrastructure patterns, and async orchestration.

---

## Table of Contents

- [Overview](#overview)
- [My Role](#my-role)
- [Architecture at a Glance](#architecture-at-a-glance)
- [Core System Design Principles](#core-system-design-principles)
- [System Layers](#system-layers)
- [Request Lifecycle Model](#request-lifecycle-model)
- [End-to-End Story Generation Flow](#end-to-end-story-generation-flow)
- [S3-Centric Storage Design](#s3-centric-storage-design)
- [Key Engineering Decisions](#key-engineering-decisions)
- [Architecture Strengths](#architecture-strengths)
- [Tradeoffs and Risks](#tradeoffs-and-risks)
- [What This Project Taught Me](#what-this-project-taught-me)
- [Future Improvements](#future-improvements)
- [Repository Scope](#repository-scope)
- [Portfolio Summary](#portfolio-summary)

---

## Overview

**TinyDOOR** is a hybrid AI-enabled story generation platform that transforms:

- user personalization inputs
- uploaded images
- story context
- generation metadata

into a **multi-page illustrated storybook experience**.

Rather than functioning as a simple "prompt → model → response" app, TinyDOOR is best understood as a **workflow-oriented AI product system** that manages:

- authentication
- uploads
- request creation
- async state transitions
- story generation
- image generation
- result aggregation
- final asset delivery

This repository focuses on the **system architecture and engineering patterns** behind that workflow.

It is designed to showcase:

- **backend systems thinking**
- **cloud infrastructure design**
- **event-driven orchestration**
- **async AI workflow design**
- **production-oriented request lifecycle management**

---

## My Role

I contributed to TinyDOOR as a **Software Engineering Intern**, working across:

- **backend systems**
- **cloud-connected product flows**
- **async AI workflows**
- **frontend/backend integration**
- **request lifecycle and result delivery patterns**

My contributions included:

- supporting backend architecture and API workflow design
- contributing to Django service flows and state-oriented endpoints
- working on AWS-connected product flows involving authentication, storage, and Lambda-triggered async processing
- helping shape request lifecycle handling and result delivery behavior
- contributing across both frontend and backend repositories in a startup environment

### Contribution Highlights

- **388 total hours contributed**
- **#1 contributor across TinyDOOR frontend + backend repositories**
- **107 commits** in the frontend repository
- **61 commits** in the backend repository

> This repository intentionally presents a **sanitized architectural view** rather than proprietary implementation details.

---

## Architecture at a Glance

TinyDOOR combines:

- a **Next.js frontend** for the user-facing product journey
- a **Django backend** for authentication, persistence, and state-oriented APIs
- **AWS Lambda functions** for asynchronous generation and orchestration
- **AWS services** for identity, storage, and supporting infrastructure

### High-Level Flow

```text
Frontend (Next.js)
        ↓
Lambda Functions
(request creation / event-driven orchestration / generation triggers)
        ↓
Django Backend
(auth, stories, uploads, webhooks, state queries)
        ↓
AWS Services
(S3, Cognito, Lambda, SES, supporting infrastructure)
```

TinyDOOR uses a hybrid layered + event-driven architecture.

This design exists to support:

- long-running generation workflows
- multi-stage async processing
- recoverability across partial failures
- scalable storage of intermediate and final assets
- safer separation between user-facing logic and generation-facing logic
- a smoother product experience for multi-step AI generation

### Core Takeaway

TinyDOOR is not a synchronous inference app.

It is a **workflow-first AI product system** built around:

- durable state transitions
- asynchronous orchestration
- artifact storage
- polling-based UX
- timeout-aware recovery

That shift — from "model call" to "workflow system" — was one of the most important engineering lessons from the project.

---

## Core System Design Principles

The architecture is built around several production-oriented principles:

1. **Workflow-first, not model-first** — Stable business state separated from async generation execution
2. **Artifact-driven storage** — For recoverability
3. **Explicit request-state transitions** — For frontend UX
4. **Serverless execution** — With timeout-aware continuation
5. **Frontend, backend, and generation pipeline** — Designed together

These principles made the system better suited for real-world AI product behavior, where generation is often:

- multi-stage
- failure-prone
- latency-heavy
- dependent on external services
- tightly coupled to product UX

---

## System Layers

### 1) Presentation Layer — Frontend (Next.js)

The frontend drives the multi-step user journey and product interaction flow.

**Primary responsibilities:**

- onboarding and navigation
- authentication UI
- personalization input collection
- image upload interactions
- generation progress UI
- result polling and display
- ebook / final delivery flows
- client-side image composition and cropping utilities

**Representative areas:**

- `app/page.tsx`
- `app/Components/`
- `app/layout.tsx`
- `app/login/`
- `app/registration/`
- `app/forgot-keys/`
- `app/capture/`
- `app/api/transcribe/`

### 2) Frontend Business Logic Layer

TinyDOOR's frontend was not just a thin UI shell.

It also contained meaningful product-side orchestration and state management, including API coordination and asset preparation.

**Representative files:**

- `lib/api.ts`
- `CharacterContext.tsx`
- `StoryContext.tsx`
- `compilePageImage.ts`
- `cropImage.ts`

**Primary responsibilities:**

- unified API client behavior
- auth-aware request handling
- story payload construction
- client-side request-state awareness
- image composition during finalization
- crop / upload preparation utilities

This layer was important because parts of the user-facing workflow depended on coordinated client-side state, not just backend responses.

### 3) Service Layer — Django Backend

The Django backend acted as the stateful application core.

It served as the durable business layer where product entities became persistent, queryable, and business-facing.

**Primary responsibilities:**

- Cognito-linked authentication
- JWT validation
- user-linked request persistence
- story and request status query APIs
- upload-related endpoints
- webhook receivers from async workflows
- AWS service wrappers / service integration logic

**Representative modules:**

- `backend/apps/authentication/`
- `backend/apps/stories/`
- `backend/apps/webhooks/`
- `backend/apps/uploads/`
- `backend/services/`

Django's role was intentionally stable and business-oriented, rather than serving as the primary long-running execution engine.

### 4) Async Processing Layer — AWS Lambda

AWS Lambda formed the event-driven execution layer.

This layer handled asynchronous work that would be poorly suited to the synchronous request/response cycle of the main backend.

**Representative responsibilities:**

- generation request creation
- presigned upload support
- S3 event listening
- LLM trigger logic
- image generation triggers
- output aggregation
- inference / training workflow initiation

**Representative Lambda units:**

- Gen_Request_Handler
- S3-Event-Listener
- LLM-Trigger
- tinydoor-lambda
- Gen_Result_Handler
- Inference-Trigger
- TinyDoorTrainingTrigger

This layer kept long-running, event-driven, and generation-heavy tasks outside the synchronous backend path.

### 5) Data Layer

TinyDOOR used a multi-store architecture, with each store serving a distinct purpose.

| Store | Purpose |
|-------|---------|
| **PostgreSQL** | Application state, users, request metadata, durable backend entities, status-oriented product records |
| **Amazon S3** | Raw user inputs, uploaded images, intermediate artifacts, generated page outputs, final story assets, request-level artifacts, timeout recovery state |
| **AWS Cognito** | Identity, JWT/token issuance, protected access state, auth boundary separation from application logic |

This separation allowed the system to keep:

- business state in a queryable database
- workflow artifacts in object storage
- identity concerns in a managed auth service

---

## Request Lifecycle Model

One of the central engineering patterns in TinyDOOR was the explicit request-state lifecycle.

### Simplified Lifecycle

```
PENDING
  → UPLOAD_COMPLETE
  → ANALYSING
  → ANALYSE_COMPLETE
  → GENERATING
  → VERIFYING
  → GENERATION_COMPLETE
  (or FAILED)
```

### Why This Matters

This lifecycle was a key architectural primitive because it:

- supported frontend polling UX
- improved observability of long-running jobs
- enabled retry and recovery strategies
- made async workflows understandable to the user
- provided a stable abstraction across multiple generation stages

This was one of the clearest examples of TinyDOOR behaving like a workflow system, not a simple CRUD app or one-shot inference service.

---

## End-to-End Story Generation Flow

A simplified end-to-end flow looked like this:

1. The user submits personalization inputs
2. A generation request is created
3. Request metadata is persisted
4. The user uploads images
5. S3 events trigger downstream processing
6. The LLM stage generates structured story output
7. Image generation creates multi-page visual assets
8. Result handlers aggregate outputs
9. Request state is updated to completion (or failure)
10. The frontend polls for updates and renders final outputs

### Simplified Flow Diagram

```text
User Input + Uploads
        ↓
Create Request
        ↓
Persist Metadata (DB)
        ↓
Store Raw Assets (S3)
        ↓
S3 Event Trigger
        ↓
LLM Processing
        ↓
Image Generation
        ↓
Result Aggregation
        ↓
State Update (DB)
        ↓
Frontend Polling + Final Delivery
```

This is fundamentally an **asynchronous multi-stage AI pipeline**, not a synchronous request/response system.

---

## S3-Centric Storage Design

TinyDOOR used Amazon S3 as more than simple file storage.

In practice, S3 acted as part of the system's workflow memory and artifact backbone.

### Example Request-Level Structure

```text
gen_requests/<user_id>/<req_uuid>/
├── raw_inputs/
├── llm_outputs/
│   └── llm_output.json
├── raw_gen_outputs/
│   ├── page_1/
│   ├── page_2/
│   ├── ...
│   └── page_10/
├── finalised_images/
│   ├── page_1.png
│   ├── page_2.png
│   ├── ...
│   └── page_10.png
└── image_gen_job.json
```

### Why This Structure Worked

It supported:

- request-level isolation
- user-level organization
- intermediate artifact recovery
- final-output retrieval
- partial progress continuation
- timeout-aware resume logic

**This is an important architectural pattern:** S3 was not just a passive bucket — it was part of the operational workflow model.

---

## Key Engineering Decisions

### 1) Dual-Backend Split (Django + Lambda)

TinyDOOR intentionally split responsibilities:

- **Django** = stable business APIs, auth, persistence, status queries
- **Lambda** = request creation, async orchestration, generation triggers, output processing

This separation helped:

- keep business state stable
- isolate long-running work from synchronous APIs
- make async workflows easier to scale
- reduce coupling between product-facing state and generation execution

### 2) Cognito-Based Authentication

Authentication was handled via AWS Cognito, with JWT-based flows and protected backend routes.

This allowed the product to:

- separate identity from core business logic
- leverage managed auth flows
- protect backend APIs with token-based access
- align frontend auth behavior with AWS-native infrastructure

### 3) Timeout-Aware Serverless Recovery

A notable production-oriented design feature was timeout-aware recovery for serverless execution.

The general pattern:

1. detect remaining execution time
2. persist enough state to resume
3. re-invoke or continue when near timeout
4. skip already completed work where possible

This matters because AI generation workflows often exceed the comfort zone of naive serverless execution.

It improves:

- reliability
- partial recoverability
- cost-aware execution
- resilience under real-world workloads

### 4) App Clip / Alternative Upload Path

A specialized App Clip upload path suggested the system supported multiple media ingestion paths, not just standard browser uploads.

This reflects product architecture that considered:

- mobile-adjacent flows
- alternate client entry points
- non-traditional upload experiences

### 5) Frontend-Assisted Finalization

The frontend participated in composing and uploading final page assets, rather than treating final rendering as purely backend-owned.

This is a notable product-engineering decision because it shifts part of the asset-preparation workflow closer to the user-facing layer.

That can improve:

- responsiveness
- user control
- perceived speed
- product flexibility for final asset handling

---

## Architecture Strengths

From a systems perspective, the strongest aspects of TinyDOOR's architecture were:

- clear separation of stable vs async responsibilities
- strong fit for long-running AI generation workflows
- S3 functioning as workflow memory, not just storage
- explicit request-state modeling for reliable polling UX
- timeout-aware recovery for serverless reliability
- frontend and pipeline designed together rather than stitched together later

These patterns make the architecture feel much closer to a **production AI product system** than a demo application.

---

## Tradeoffs and Risks

Like any real system, this architecture also involved tradeoffs.

### Key Tradeoffs

- Hybrid Django + Lambda ownership increases coordination complexity
- Distributed state across DB / S3 / Lambda can complicate consistency guarantees
- Polling-based UX is simple and reliable, but adds repeated request overhead
- Multi-stage workflows increase debugging and failure-handling complexity
- Artifact-heavy pipelines require stronger observability and operational discipline

These are normal tradeoffs for systems optimizing for:

- async processing
- recoverability
- product responsiveness
- rapid iteration in a startup environment

---

## What This Project Taught Me

TinyDOOR significantly changed how I think about AI systems.

**Before working on it**, it was easy to think of AI products as:

> input → model → output

**In practice**, TinyDOOR showed that real AI-enabled products require:

- stateful request handling
- multi-stage orchestration
- artifact management
- recovery-aware workflows
- cloud-native storage patterns
- product-friendly async UX
- explicit boundaries between frontend, backend, and generation systems

That shift is a major reason I'm especially interested in:

- backend systems
- cloud infrastructure
- async AI workflows
- AI pipelines
- MLOps-oriented architecture

---

## Future Improvements

If evolving this architecture further, some portfolio-safe directions would include:

- stronger workflow orchestration (e.g. Step Functions or queue-backed job systems)
- better request tracing and centralized observability
- more formal generation job objects / output manifests
- clearer typed contracts between frontend, Django, Lambda, and output stages
- a more explicit MLOps boundary between application backend and training/inference platform layers
- migration from polling-heavy flows toward event push / websocket / pub-sub where appropriate

These would improve maintainability, observability, and scalability as the system matures.

---

## Repository Scope

**To respect confidentiality**, this repository does not include:

- proprietary source code
- internal production endpoints
- private schemas / credentials
- internal prompts or datasets
- private UI assets
- sensitive infrastructure identifiers
- confidential business logic or customer-facing internals

**This repository is intentionally focused on:**

- architecture thinking
- engineering tradeoffs
- system design maturity
- production-oriented AI workflow patterns

---

## Portfolio Summary

TinyDOOR is best understood as a **hybrid product-and-pipeline system**.

It combines:

- a polished frontend product flow
- Django-backed business state
- Lambda-based async orchestration
- S3-backed workflow memory
- multi-stage AI generation
- polling-based result delivery
- production-minded request lifecycle design

### Most Important Architectural Ideas

1. **Hybrid backend split**
2. **Workflow-first architecture**
3. **S3 as an artifact backbone**
4. **Request lifecycle as a product primitive**
5. **Production-minded async design**

This project was one of the most important steps in my growth as an engineer because it moved me from thinking about **features** to thinking about **systems**.

---

## Final Note

This repository is intended as a **system architecture portfolio artifact**.

It is designed to demonstrate:

- how a real AI product can be structured beyond a simple model call
- how async workflows shape backend and product design
- how cloud infrastructure choices affect reliability and UX
- how production AI systems require orchestration, recoverability, and state modeling

**If you're reviewing this repository as a recruiter, engineer, or hiring manager**, the key takeaway is:

> TinyDOOR was a real-world lesson in building AI products as **durable systems** — not just demos.
