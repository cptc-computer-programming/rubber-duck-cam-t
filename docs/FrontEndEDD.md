---
layout: default
title: Engineering Design Document Template
---

[Documentation home]({{ '/' | relative_url }})

# Engineering Design Document Template

All sections in this document are optional. Use what you need.

**Document Version: 1.0**

## Executive Summary
Field       Information
Status      IN PROGRESS
Created     Date  October 25, 2026
Author      Frontend Team – Francis Sengele, Ivanna Otero, Zac
EDD Size    1–2 weeks (estimate)
Consulted   Backend Team (Cody, Zane, Anthony), Instructor Rachel



## Overview

### Problem

- Open WebUI provides a generic, blank chat interface.
 Students currently have no way to specify which class assignment they are working on before starting a conversation.
 Without knowing the specific assignment, the backend cannot load the correct context, constraints, 
 or "rubber duck" system prompts required to properly tutor the student.

### Technical Details
- We need to map out the view/component hierarchy (navigation, pages, forms). 
  More importantly, we need to define the API contracts with the back-end team. 
  We can't build our HTTP requests or UI models until we know exactly what JSON payload 
  they are sending us and what they expect us to send back.

### Bullet Points
- The frontend must create a user experience that encourages problem-solving through guidance, hints, 
  and coaching rather than answer delivery.
- A scalable component architecture must be designed to support assignment views, 
  chat interactions, navigation, progress tracking, and future educational features
  without requiring major UI redesigns.
- Clear API contracts are needed so frontend engineers can build reliable data models, request
  handlers, and error states that properly communicate with backend services.
- The success of the product depends heavily on usability, accessibility, 
  and user engagement, requiring specialized frontend engineering to transform backend AI 
  functionality into an intuitive learning platform that students will actually use effectively.

  ### Problem Statement — Frontend

Open WebUI already has a chat system. Students can type questions and receive AI responses.
However, the chat does not currently have a way for students to select the homework assignment they need help with.
For Milestone 1, we want students to select the SQL Joins assignment from Principles of Relational Databases.
The frontend must send the selected assignment information to the backend so the AI can give help related to that assignment.
The AI should guide students and help them understand what to try next, not simply give them the homework answers.



### Proposed Solution

We plan to add two dropdown menus to the Open WebUI chat page:
Select Class: Principles of Relational Databases
Select Assignment: SQL Joins
We will use the existing Chat.svelte component.
The selected values will be stored in two variables:
selectedClassId
selectedAssignmentId
The frontend will send these values to the backend using the existing chat API.
We will use the rubber_duck_context structure agreed on with the backend team.
This approach allows us to reuse the existing Open WebUI chat system without building a new chat interface.


## Frontend Technical Problem

- Open WebUI has an existing chat interface, but M1 requires a way for students to select the supported SQL JOIN assignment.
- We need to decide where assignment selection should appear in the chat interface.
- We need to identify the existing frontend components responsible for starting a chat and submitting messages.
- The frontend and backend teams need to agree on how assignment information will be sent and received.

## Proposed Solution

- Add an assignment selection UI using Svelte components.
- Display the supported SQL JOIN assignment clearly to students.
- Connect the selected assignment to the homework help chat.
- Investigate the existing chat submission API and reuse it if appropriate.
- Work with the backend team to agree on the assignment data format.

## Technical Decisions Still Needed

- Which Svelte component should we modify?
- Should assignment information come from the backend or be defined in the frontend for M1?
- Which API should carry the selected assignment?
- How should the assignment be associated with the chat session?

## Technical Design

### Overview

Provide a system-level explanation:

- How the solution fits into the existing architecture.
- Major components and how they interact.
- Data flow at a high level.
- Include a simple diagram if useful.

### Technical Details

#### System Diagrams

- Architecture diagrams (high-level and/or detailed).
- Sequence diagrams for critical flows.
- Data flow diagrams if applicable.
- Clearly label services, dependencies, and boundaries.

#### APIs

- New or modified endpoints.
- Request/response formats (schemas).
- Authentication/authorization requirements.

#### Database

- Schema changes (tables, fields, indexes).
- Migrations and rollout plan.


#### Deployment

How will code changes be shipped? Include information on:

- Automated steps.
- Manual steps.
- CI/CD pipelines.
- Containerization technology.

## Out of Scope

Explicitly list what is not included:

- Features or capabilities intentionally excluded.
- Future work that will be handled separately.
- Avoid ambiguity to prevent scope creep.

## Observability

### Proactive

Metrics to track system health (latency, throughput, error rates):

- Alerts and thresholds.
- Health checks and dashboards.

### Reactive

- Logging strategy (structured logs, log levels).
- Debugging tools and workflows.


## Risks

Identify and assess risks:

- Technical risks (scalability, reliability).
- Product risks (user adoption, UX issues).
- Operational risks (deployment complexity).
- Dependencies on other teams or systems.

For each risk, include the following headers:

### [Risk Name]

#### Description

Describe the risk and its potential impact.

#### Mitigation Strategy

Describe how the risk will be reduced or addressed.

## Timeline & Scope

The big question to answer: will this take days, weeks, or months?

- Key milestones and phases.
- Dependencies and sequencing.
- Resource assumptions (team size, roles).