# TinyDOOR System Architecture

## Overview
TinyDOOR is a hybrid AI-enabled story generation platform that transforms user inputs, uploaded images, and personalization data into a generated multi-page illustrated storybook experience.

This repository is a **sanitized architecture case study** based on my work on TinyDOOR. Since the production code and internal assets are private, this repo focuses on the **system design, backend workflows, cloud infrastructure patterns, and async orchestration model** rather than proprietary implementation details.

TinyDOOR is best understood not as a simple “model-call app,” but as a **workflow-oriented AI product system** that manages:

- authentication
- uploads
- request creation
- async state transitions
- story generation
- image generation
- result aggregation
- final delivery

This repository is intended to demonstrate **engineering patterns, architecture thinking, and production-minded system design** in a portfolio-safe format.

---

## My Role
I contributed to TinyDOOR as a **Software Engineering Intern**, working across **backend systems, cloud infrastructure, async AI workflows, and full-stack product integration**.

My work involved:

- contributing to backend architecture and system design discussions
- supporting Django backend service workflows and API behavior
- working with AWS-connected product flows across authentication, storage, and Lambda-driven async processing
- helping shape request lifecycle handling and result delivery patterns
- contributing across both frontend and backend repositories in a real startup environment

I contributed **388 hours** and was the **#1 contributor across TinyDOOR frontend and backend repositories**, with:

- **107 commits** in the frontend repository
- **61 commits** in the backend repository

> Note: This repository intentionally presents a sanitized architectural view rather than proprietary implementation details.

---

## High-Level Architecture
TinyDOOR combines:

- a **Next.js frontend** for the user-facing product journey
- a **Django backend** for authentication, persistence, and state-oriented APIs
- a set of **AWS Lambda functions** for asynchronous processing and generation workflows
- and **AWS services** for storage, identity, and orchestration

At a high level:

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
