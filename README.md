# TinyDOOR System Architecture (Sanitized Case Study)

## Overview
TinyDOOR is an AI-enabled storytelling product designed to transform user photos and personalization inputs into a generated illustrated children's storybook experience.

This repository is a **sanitized engineering case study** based on my work on TinyDOOR at DOOR Global. Since the production code and internal assets are private, this repo focuses on the **system architecture, backend workflows, cloud patterns, and engineering decisions** I contributed to, without exposing proprietary source code.

The goal of this repository is to present the **engineering thinking behind a real AI-enabled product system** — especially across backend architecture, cloud integration, async workflows, and full-stack product coordination.

---

## My Role
I contributed to TinyDOOR as a **Software Engineering Intern**, working across **backend systems, cloud infrastructure, and full-stack product workflows**.

My work included:

- backend schema and data-model thinking
- Django backend service design
- AWS integration for authentication, storage, and service workflows
- API design and request lifecycle handling
- frontend / backend integration support
- AI generation workflow coordination
- storage organization and output retrieval patterns

I contributed **~400 hours** and was the **#1 contributor across TinyDOOR frontend and backend repositories**.

> Note: This repository intentionally presents a sanitized architectural view rather than proprietary implementation details.

---

## Product Context
TinyDOOR was designed as an AI-enabled storybook generation experience.

A simplified user flow looked like this:

1. User signs in
2. User uploads one or more photos
3. User enters personalization details (e.g. character / story inputs)
4. Frontend submits a generation request to the backend
5. Backend records and tracks the request
6. Async generation workflow processes the request
7. Generated assets are stored and associated with the request
8. Frontend polls request status
9. User receives final storybook outputs for viewing or download

This product pattern required more than just “calling a model” — it required a reliable **product-grade workflow** across authentication, storage, request state, orchestration, and output delivery.

---

## Why This Case Study Matters
This project was important to my growth because it moved me beyond isolated coding tasks and into **production-oriented system thinking**.

TinyDOOR exposed me to how real AI-enabled products require:

- clear API boundaries
- durable request tracking
- cloud-native storage workflows
- async orchestration for long-running jobs
- safe integration between frontend, backend, and downstream services
- design decisions that balance user experience with implementation constraints

This experience strongly shaped my interest in **AI pipelines, MLOps-oriented systems, and production AI infrastructure**.

---

## High-Level Architecture
Below is a simplified high-level architecture diagram that reflects the core engineering patterns involved in the product.

![TinyDOOR High-Level Architecture](assets/tinydoor-high-level-architecture.png)

At a high level, the system involved:

- **Frontend Client**  
  User-facing interface for authentication, uploads, personalization input, request submission, status polling, and result viewing.

- **Authentication Layer (AWS Cognito)**  
  Managed user authentication and protected access flows.

- **Backend API Layer (Django)**  
  Handled request creation, validation, lifecycle tracking, metadata persistence, and frontend-facing API logic.

- **Storage Layer (AWS S3)**  
  Used for uploaded user media, intermediate assets, and generated outputs.

- **Database Layer**  
  Tracked request metadata, asset references, user-linked content, and request status.

- **Controlled Service / Integration Layer**  
  Provided safer separation between frontend-facing APIs and downstream internal workflows.

- **Async Processing Workflow**  
  Supported long-running generation operations without blocking user-facing interactions.

- **AI Generation Components**  
  Responsible for transforming user inputs and assets into final storybook outputs.

---

## Request Lifecycle Pattern
A core engineering pattern in TinyDOOR was the use of an **async request lifecycle** for long-running generation tasks.

![TinyDOOR Request Lifecycle](assets/tinydoor-request-lifecycle.png)

A simplified request state model:

- `PENDING`
- `PROCESSING`
- `COMPLETED`
- `FAILED`

This pattern allowed the product to:

- accept user input quickly
- avoid blocking the frontend while generation ran
- expose stable status polling to the client
- support clearer error handling and retry thinking
- cleanly separate request submission from heavy downstream processing

This kind of architecture is one of the reasons I’m now especially interested in **AI pipelines and MLOps**.

---

## Storage & Asset Flow
Media and generated outputs required a clean storage strategy.

![TinyDOOR Storage Flow](assets/tinydoor-storage-flow.png)

A simplified storage design involved:

- user-uploaded images
- character or story-related assets
- generated output assets
- request-linked storage references
- retrieval patterns for frontend display or download

Key goals of this storage design included:

- keeping user content logically organized
- supporting predictable output retrieval
- separating input assets from generated artifacts
- reducing coupling between storage structure and frontend assumptions

---

## Core Engineering Contributions

### 1. Backend Schema & Data-Model Thinking
I contributed to the evolution of backend data structures and entity relationships across multiple iterations of the product.

This included helping shape how the system represented and connected concepts such as:

- users
- story requests
- character or personalization data
- uploaded media assets
- generated outputs
- request state and lifecycle metadata

This work helped align product needs with an implementable backend architecture.

---

### 2. Django Backend Service Design
I worked on backend workflows within a Django-based service layer, including patterns related to:

- request creation
- input validation
- request status tracking
- API response shaping
- integration between product flows and downstream processing

This improved my understanding of how backend systems must be designed not just for correctness, but for **product reliability and maintainability**.

---

### 3. Cloud Integration Across AWS Services
I contributed to cloud-connected workflows involving services such as:

- **AWS Cognito** for authentication
- **AWS S3** for storage
- **backend service deployment patterns**
- **service-to-service integration boundaries**

This gave me more practical exposure to how cloud services support real product architecture beyond isolated demos.

---

### 4. Full-Stack Workflow Coordination
Although much of my work was backend-leaning, TinyDOOR also strengthened my **full-stack capability** because the product required tight coordination between:

- frontend UX flow
- backend APIs
- auth constraints
- async request handling
- generated result retrieval

This meant thinking about the **entire user journey**, not just backend internals.

---

### 5. Async Product Thinking for AI Workflows
One of the most valuable lessons from TinyDOOR was learning how AI-enabled products often need to behave like **workflow systems**, not just inference endpoints.

Instead of a simple request/response pattern, the product needed:

- request creation
- state persistence
- deferred processing
- polling / retrieval
- output association
- graceful failure handling

That experience is directly relevant to the kind of **AI pipeline / orchestration / MLOps** work I want to continue building toward.

---

### 6. Engineering in a Real Startup Environment
TinyDOOR also gave me exposure to the realities of engineering inside a startup environment:

- evolving requirements
- greenfield architecture decisions
- balancing speed with structure
- shipping under ambiguity
- working across both technical and product-facing constraints

This was a major step up from coursework because it required both execution and judgment.

---

## Sanitized Data Model (Illustrative)
Below is a simplified conceptual data model used only to illustrate the kinds of entities involved.

- **User**
- **StoryRequest**
- **CharacterProfile**
- **MediaAsset**
- **StoryOutput**
- **RequestStatusHistory**

A typical relationship flow might look like:

- A **User** creates one or more **StoryRequests**
- A **StoryRequest** references one or more **MediaAssets**
- A **StoryRequest** may use one or more **CharacterProfiles**
- A completed **StoryRequest** produces one or more **StoryOutputs**
- Status changes may be recorded in **RequestStatusHistory**

> This is intentionally illustrative and not a direct reproduction of internal schemas.

---

## Example API Patterns (Sanitized)
Below are examples of the kinds of API patterns involved in the system.

### Create Generation Request
`POST /api/requests`

**Purpose:**  
Create a new story generation request from user inputs and uploaded assets.

### Get Request Status
`GET /api/requests/{request_id}`

**Purpose:**  
Return request metadata and lifecycle state so the frontend can poll for progress.

### Get Output Assets
`GET /api/requests/{request_id}/outputs`

**Purpose:**  
Return references to generated outputs after successful completion.

> These are representative patterns only and do not reflect exact production endpoints.

---

## What This Repository Does *Not* Include
To respect confidentiality and platform integrity, this repository does **not** include:

- proprietary source code
- internal service code
- production infrastructure details
- credentials or environment configuration
- internal schemas or sensitive ERDs
- private datasets
- internal model code or prompts
- customer / user data
- company-owned UI assets

This repository is intended to showcase **engineering patterns and system thinking**, not internal implementation.

---

## Key Takeaways
Working on TinyDOOR strengthened my experience in:

- backend architecture
- Django-based service workflows
- cloud-connected application design
- request lifecycle modeling
- async AI product workflows
- storage and output delivery patterns
- full-stack coordination across user-facing systems
- translating product ideas into implementable engineering structures

Most importantly, it shifted how I think about AI systems:

> from **“model demos”**  
> to **“production-oriented workflows and pipelines”**

That shift is a big reason why I’m now especially interested in **backend infrastructure, AI pipelines, and MLOps-oriented systems**.

---

## Related Public Demos
To complement this sanitized case study, I’m building public demo repositories that reflect similar engineering patterns:

- `async-ai-request-pipeline-demo`
- `s3-presigned-upload-demo`
- `django-job-status-api-demo`

These public demos are intended to show simplified implementations of the architectural patterns discussed here.

---

## About This Repository
This repository is part of my engineering portfolio and is intended to demonstrate how I think about:

- backend system design
- cloud-native product workflows
- AI-enabled product architecture
- full-stack coordination in startup environments
- production-minded engineering patterns

If you’re viewing this as part of my profile, feel free to explore the rest of my GitHub repositories and connect with me on LinkedIn.
