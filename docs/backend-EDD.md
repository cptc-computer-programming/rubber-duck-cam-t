---
layout: default
title: Engineering Design Document Template
---

[Documentation home]({{ '/' | relative_url }})

# EDD: M1 Backend Engineering Design Document

All sections in this document are optional. Use what you need.

**Document Version: 1.0**

## Executive Summary

| Field | Guidance |
| --- | --- |
| **Status** | DRAFT |
| **Created Date** | October 20, 2026|
| **Author** | Anthony Brunner, Cody Pekarek, Zane Marcoe|
| **EDD Size** | Estimated scope (days, weeks, months). |
| **Consulted** | Frontend Team, Rachel King, Joe Ortiz |
| **Supporting Documentation** | Links to PRDs, RFCs, architecture docs, tickets, and diagrams. Not external references or guides. |

## Overview

### Problem

Clearly define the problem being solved:

- The Model does not know to not give the explicit answer to the student and       rather tutor the student through the problem
- The Model has no context to the current course work the students are working with
- The Model needs a way to determine which learning objectives the student is having difficulty with.
- The Model has no means of identifying when a question goes beyond the scope of the students current skill sets, 
    and summarizing brief responses without overwhelming the student.
    
### Proposed Solution

High-level description of the solution:

- Giving users access to course content, such as assignments, to get their desired help with context
- Bar the model from giving the student the "answers" to their problems (force them to work through it)
- Keep prompting the user if they understand the assignment or the content, if not then help the user towards comprehension
    using plain language that the user can understand

- Use Keywords and concepts, to identify specific skillsets the student is lacking.
- Provide multiple abstract analogies that the student can relate to outside the lingo of programming.
- Identify skill sets contained within the assignment that are beyond the current scope, that the student may indirectly become involved with, 
    and allow for a brief summary of the concept but not overwhelm the student.

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
