# TinyDOOR System Architecture

A **public-safe architecture case study** of a hybrid AI-enabled story generation platform designed around **asynchronous workflows, cloud-native storage, and production-minded request lifecycle management**.

> **Important:** This repository is a **sanitized portfolio artifact**, not a mirror of the production system.  
> It intentionally abstracts and generalizes implementation details to avoid exposing proprietary logic, internal naming, private infrastructure, or confidential product internals.

---

## Table of Contents

- [Overview](#overview)
- [Why This Repository Exists](#why-this-repository-exists)
- [My Role](#my-role)
- [Architecture at a Glance](#architecture-at-a-glance)
- [Core System Design Principles](#core-system-design-principles)
- [System Layers](#system-layers)
- [Request Lifecycle Model](#request-lifecycle-model)
- [End-to-End Story Generation Flow](#end-to-end-story-generation-flow)
- [Artifact Storage Pattern](#artifact-storage-pattern)
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
- generation metadata
- structured story context

into a **multi-page illustrated storybook experience**.

This repository focuses on the **architecture patterns** behind that kind of product rather than proprietary implementation.

TinyDOOR is best understood not as a simple "prompt → model → response" app, but as a **workflow-oriented AI product system** that coordinates:

- authentication
- uploads
- request creation
- async state transitions
- structured content generation
- image generation
- result aggregation
- final asset delivery

The emphasis of this repository is on:

- **system design**
- **backend workflow thinking**
- **cloud-native architecture**
- **async orchestration**
- **production-oriented AI product patterns**

---

## Why This Repository Exists

This repository exists as a **portfolio-safe architecture showcase** based on real engineering work.

It is intentionally designed to demonstrate:

- how real AI products often behave like **workflow systems**, not one-shot model wrappers
- how asynchronous generation affects product and backend design
- how cloud infrastructure decisions shape reliability, scalability, and UX
- how durable state and artifact management matter in production AI systems

All examples in this repository are intentionally:

- **generalized and simplified**
---

## My Role

I contributed to TinyDOOR as a **Software Engineering Intern**, working across:

- backend systems
- cloud-connected product flows
- async AI workflow integration
- frontend/backend coordination
- request lifecycle and result delivery patterns

My work included:

- contributing to backend architecture and workflow discussions
- supporting state-oriented backend service behavior
- working with cloud-connected flows for authentication, storage, and async processing
- helping shape request lifecycle handling and result delivery patterns
- contributing across both frontend and backend repositories in a startup environment

### Contribution Highlights

- **388 total hours contributed**
- **#1 contributor across frontend + backend repositories**

> This repository intentionally presents **architecture patterns and engineering lessons**, not confidential implementation details.

---

## Architecture at a Glance

TinyDOOR combines:

- a **frontend** (e.g. React/Next.js) for the user-facing product journey
- an **application backend** (e.g. Python/Django) for authentication, persistence, and state-oriented APIs
- **serverless async workers** for event-driven generation workflows
- **cloud services** (e.g. object storage, identity, notifications) for supporting infrastructure

### High-Level Flow

```text
Frontend
        ↓
Async Processing Layer
(event-driven orchestration / generation triggers)
        ↓
Application Backend
(auth, persistence, state queries, callbacks)
        ↓
Cloud Services
(object storage, identity, supporting infrastructure)
```

This is best described as a hybrid layered + event-driven architecture.

It is designed to support:

- long-running generation workflows
- partial recoverability
- clean separation between business state and async execution
- scalable handling of intermediate and final artifacts
- safer frontend/backend boundaries
- user-friendly multi-stage generation UX

### Core Development Agenda

TinyDOOR is not just a synchronous inference app.

It is a **workflow-first AI product system** built around:

- durable state transitions
- asynchronous orchestration
- artifact-backed processing
- polling-based user experience
- recoverability under long-running workloads

That shift — from "model call" to "workflow system" — was one of the most important engineering lessons from the project.

---

## Core System Design Principles

The architecture is built around several production-oriented principles:

1. **Workflow-first, not model-first** — Stable business state separated from async generation execution
2. **Artifact-backed storage** — For recoverability
3. **Explicit request-state transitions** — For frontend UX
4. **Serverless-friendly execution** — With continuation-aware design
5. **Frontend, backend, and generation pipeline** — Designed together

These principles made the system better suited for real-world AI product behavior, where generation is often:

- multi-stage
- latency-heavy
- dependent on external services
- failure-prone
- tightly coupled to user-facing product flow

---

## System Layers

### 1) Presentation Layer — Frontend

The frontend (e.g. React/Next.js) drives the multi-step user journey and product interaction flow.

**Primary responsibilities:**

- onboarding and navigation
- authentication UI
- personalization input collection
- image upload interactions
- generation progress UI
- result polling and display
- final delivery flows
- client-side media preparation utilities

**Representative responsibilities:**

- page routing and onboarding flow
- auth views
- upload UI
- generation progress screens
- result display surfaces
- media preparation / transformation helpers

### 2) Frontend Business Logic Layer

TinyDOOR's frontend was not just a thin UI shell.

It also contained meaningful product-side orchestration and state management, including API coordination and client-assisted asset preparation.

**Primary responsibilities:**

- unified API client behavior
- auth-aware request handling
- generation payload construction
- request-state awareness on the client
- client-side media preparation
- final asset composition where appropriate

This layer mattered because parts of the user-facing workflow depended on coordinated client-side state, not just backend responses.

### 3) Service Layer — Application Backend

The application backend (e.g. Django framework) acted as the stateful application core.

It served as the durable business layer where product entities became persistent, queryable, and business-facing.

**Primary responsibilities:**

- token-aware authentication integration
- protected route validation
- user-linked request persistence
- request and story status APIs
- upload-related endpoints
- async callback / webhook handling
- cloud service wrappers and integration logic

Its role was intentionally stable and business-oriented, rather than serving as the primary long-running execution engine.

### 4) Async Processing Layer — Serverless Workers

A serverless async layer handled long-running or event-triggered work that would be poorly suited to the synchronous backend request/response cycle.

**Representative responsibilities:**

- generation request initialization
- upload event processing
- structured content generation triggers
- image generation triggers
- output aggregation
- offline / model-related workflow initiation
- result finalization callbacks

This layer kept generation-heavy and event-driven work outside the synchronous application path.

> **Note:** Worker responsibilities and names in this repository are intentionally generalized and do not reflect production-identical internal service naming.

### 5) Data Layer

TinyDOOR used a multi-store architecture, with each store serving a distinct purpose.

| Store | Purpose |
|-------|---------|
| **Relational Database** | Application state, users, request metadata, durable backend entities, status-oriented product records |
| **Object Storage** | Uploaded inputs, intermediate artifacts, generated outputs, final assets, workflow artifacts, resumable processing context |
| **Managed Identity Service** | Identity, token issuance, protected access state, separation of auth concerns from core application logic |

This separation allowed the system to keep:

- business state in a queryable database
- workflow artifacts in object storage
- identity concerns in a managed auth boundary

---

## Request Lifecycle Model

One of the central engineering patterns in TinyDOOR was the explicit request-state lifecycle.

### Conceptual Lifecycle Pattern

The system used an explicit **multi-stage, pollable, recoverable** request lifecycle.

A simplified conceptual progression might look like:

```text
Collected
  → Processing
  → Generating
  → Complete
  (or Failed)
```

This illustrates the architecture pattern: durable state transitions that support polling UX, observability, and recovery — not a production-identical state machine.

### Why This Matters

This lifecycle was a key architectural primitive because it:

- supported frontend polling UX
- improved observability of long-running jobs
- enabled retry and recovery strategies
- made async workflows understandable to the user
- provided a stable abstraction across multiple generation stages

This is one of the clearest examples of TinyDOOR behaving like a workflow system, not a simple CRUD app or one-shot inference service.

---

## End-to-End Story Generation Flow

Conceptually, the flow followed an **asynchronous multi-stage pipeline**:

1. User inputs and media are collected
2. A generation request is created and persisted
3. Storage events or callbacks trigger async processing
4. Content and visual generation run in stages
5. Results are aggregated and state is updated
6. The frontend polls for updates and renders outputs

### Conceptual Flow (High-Level)

```text
Input Collection
        ↓
Request Creation + Persistence
        ↓
Async Processing (content + assets)
        ↓
Result Aggregation + State Update
        ↓
Frontend Polling + Delivery
```

This is fundamentally an **asynchronous multi-stage AI pipeline** — multi-phase, recoverable, and polling-friendly — not a synchronous request/response system.

---

## Artifact Storage Pattern

TinyDOOR used object storage as more than simple file storage.

In practice, storage acted as part of the system's workflow memory and artifact backbone.

### Design Principles (Pattern-Level)

The storage design followed these principles rather than any specific folder layout:

- **User-scoped isolation** — Artifacts organized by user to support multi-tenancy and access control
- **Request-level isolation** — Each generation request had its own logical namespace
- **Lifecycle-tiered organization** — Inputs, intermediate outputs, and final outputs separated by processing stage
- **Recoverability** — Enough context persisted to resume or retry long-running work

This pattern supported:

- request-level isolation
- user-level organization
- intermediate artifact recovery
- final-output retrieval
- partial progress continuation
- resumable processing for long-running jobs

**Key idea:** Object storage was not just a passive bucket — it was part of the operational workflow model.

---

## Key Engineering Decisions

### 1) Stable Backend + Async Worker Split

TinyDOOR intentionally separated responsibilities:

- **Application backend** = stable business APIs, auth, persistence, status queries
- **Async workers** = request initialization, orchestration, generation triggers, output processing

This helped:

- keep business state stable
- isolate long-running work from synchronous APIs
- make async workflows easier to scale
- reduce coupling between product-facing state and generation execution

### 2) Managed Authentication Boundary

Authentication was handled through a managed identity service, with token-based access and protected backend routes.

This allowed the product to:

- separate identity from core business logic
- leverage managed auth flows
- protect backend APIs with token-based access
- keep application concerns focused on product state and workflow logic

### 3) Continuation-Aware Serverless Design

A notable production-oriented design feature was continuation-aware execution for serverless workloads.

The general pattern:

1. detect remaining execution budget
2. persist enough state to resume
3. continue work in follow-up invocations when necessary
4. skip already completed work where possible

This matters because AI generation workflows often exceed the comfort zone of naive synchronous or short-lived execution.

It improves:

- reliability
- partial recoverability
- cost-aware execution
- resilience under real-world workloads

### 4) Flexible Input and Client Participation

The architecture supported **multiple input sources** (e.g. web upload, alternate clients) rather than assuming a single upload path.

Where appropriate, the **client participated in asset preparation** — some composition or finalization steps ran on the client rather than purely on the backend. This can improve responsiveness, perceived speed, and flexibility in output handling.

---

## Architecture Strengths

From a systems perspective, the strongest aspects of TinyDOOR's architecture were:

- clear separation of stable vs async responsibilities
- strong fit for long-running AI generation workflows
- object storage functioning as workflow memory, not just storage
- explicit request-state modeling for reliable polling UX
- continuation-aware serverless design for reliability
- frontend and pipeline designed together rather than stitched together later

These patterns make the architecture feel much closer to a **production AI product system** than a demo application.

---

## Tradeoffs and Risks

Like any real system, this architecture also involved tradeoffs.

### Key Tradeoffs

- Hybrid backend + async worker ownership increases coordination complexity
- Distributed state across DB / storage / workers can complicate consistency guarantees
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
- production-oriented AI infrastructure

---

## Future Improvements

If evolving this architecture further, some portfolio-safe directions would include:

- stronger workflow orchestration (e.g. state machines or queue-backed job systems)
- better request tracing and centralized observability
- more formal generation job objects / output manifests
- clearer typed contracts between frontend, backend, workers, and output stages
- a more explicit separation between application services and model-processing infrastructure
- gradual migration from polling-heavy flows toward push-based updates where appropriate

These would improve maintainability, observability, and scalability as the system matures.

---

## Repository Scope

**To respect confidentiality**, this repository does not include:

- proprietary source code
- internal production endpoints
- private schemas or credentials
- internal prompts or datasets
- private UI assets
- internal infrastructure identifiers
- confidential business logic
- production-identical storage layouts
- production-identical worker names
- production-identical state machines

**This repository is intentionally focused on:**

- architecture thinking
- engineering tradeoffs
- system design maturity
- production-oriented AI workflow patterns

---
