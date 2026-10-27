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
| **Consulted** | Frontend Team (Francis, Ivanna, Zac), Rachel King, Joe Ortiz |
| **Supporting Documentation** | [M1](m1.md), [Current architecture](current-architecture.md)|

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

Before the context of the users question reaches the LLM, Ollama, the API will see if the class and assignment have been selected. If so, the product will pull the required information from both ID's to respond with a contextualized answer for the user. Then push the users question and the pulled information towards the LLM

The system formats this metadata into a structured system prompt wrapper, merges it with the student's message history, and pipelines the final 
compiled payload to the Ollama core server.

### Technical Design Elements

```text
               ┌────────────────────────────────────────────────────────┐
               │              M1 Intermediary Backend Layer             │
               │                                                        │
[Student Request] ───► [API Controller] ───► [Contextual Compiler]      │
 (Class/Task IDs)            │                      ▲                   │
                             ▼                      │                   │
                     [Database Lookup] ─────────────┘                   │
                 (Rubrics / Objectives)                                 │
                                                                        │
                                                                        │
[Socratic Response] ◄── [Ollama Engine] ◄─── [Structured Payload]       │
                       (Llama 3.2 3B)       (Prompt + Course Rules)     │
                                                                        │
               └────────────────────────────────────────────────────────┘
```

### Technical Details

#### System Diagrams

- Architecture diagrams (high-level and/or detailed).
- Sequence diagrams for critical flows.
- Data flow diagrams if applicable.
- Clearly label services, dependencies, and boundaries.

##### Interceptor Prompt Pipeline Sequence
1. Client frontend sends a `POST` request containing `class_id`, `assignment_id`, and `student_message`.
2. API queries persistent storage for the assignment parameters (e.g., restricted methods, target concepts, allowed libraries).
3. Interceptor builds a system prompt injecting the context alongside strict formatting instructions (*"Do not write full functions; use Socratic questioning; provide abstract programming analogies"*).
4. Compiled text payload routes over the isolated virtual network adapter directly to the Ollama container interface.
5. Streaming or blocked output routes clean textual components back through to the student's browser window.


#### APIs

- New or modified endpoints.
- Request/response formats (schemas).
- Authentication/authorization requirements.

* **Request Schema (`application/json`):**
```json
{
  "class_id": "CS-101",
  "assignment_id": "lab-03-loops",
  "session_id": "550e8400-e29b-41d4-a716-446655440000",
  "message": "How do I print numbers from 1 to 10 without copying print 10 times?"
}
```

* **Response Schema (`application/json`):**
```json
{
  "session_id": "550e8400-e29b-41d4-a716-446655440000",
  "detected_gaps": ["loops", "iteration"],
  "out_of_scope_detected": false,
  "response": "To repeat an action cleanly, programmers use a structural pattern called a loop. Think of it like a repeating track playlist that counts each song play. If you wanted a counter to start at 1 and stop after 10 plays, what condition would tell the track playlist to finally stop?"
}
```

#### Database

- Schema changes (tables, fields, indexes).
- Migrations and rollout plan.

##### Schema Updates
To support dynamic contextual rendering, two relational structures will be implemented inside the system persistence layer:

* **`assignments` Table:**
  * `id` (VARCHAR, PK): Unique identification key (e.g., `lab-03-loops`).
  * `class_id` (VARCHAR, FK): Maps back to the active tracking class registry.
  * `title` (VARCHAR): Display name of the student coursework.
  * `objectives` (JSON/TEXT): Array of mandatory key terms or target skill competencies.
  * `rubric_constraints` (TEXT): Explicit instructions regarding prohibited tools or design patterns.
  * `max_scope_level` (INT): Numeric curriculum tier flag used to flag out-of-scope concepts.

* **`student_skill_analytics` Table:**
  * `id` (BIGINT, PK): Unique tracking entry indicator.
  * `student_id` (VARCHAR): Tracking tag bound to the active user account.
  * `class_id` (VARCHAR, FK): Maps tracking explicitly back to specific courses.
  * `concept_deficit` (VARCHAR): Tagged skill gap keyword (e.g., `nested-loops`).
  * `occurrence_count` (INT): Running tally of times this conceptual deficit was identified.
  * `last_observed` (TIMESTAMP): Time signature tracking for real-time instructor dashboards.



#### Deployment

How will code changes be shipped? Include information on:

- Automated steps.
- Manual steps.
- CI/CD pipelines.
- Containerization technology.

Code changes will be shipped using standard environment isolation patterns:
* Services run entirely within Docker containers via a unified `compose.yaml` system stack.
* Push events to the main source branch trigger basic linting, static code analysis, and integration verification configurations.
* System maintainers pull updated image configurations on the target campus node and trigger `docker compose up -d --build` to safely cycle internal application runtimes.


## Out of Scope

Explicitly list what is not included:

- Features or capabilities intentionally excluded.
- Future work that will be handled separately.
- Avoid ambiguity to prevent scope creep.

The following items are explicitly excluded from the current M1 implementation phase:
* The backend will not execute student scripts or interface with structural testing frameworks (like JUnit or PyTest); code evaluation remains conceptual.
* No traffic will route to external third-party model providers (OpenAI, Anthropic, or external API relays); 100% of evaluation execution happens locally on campus server hardware.
* Real-time data logs only track obfuscated, relational IDs to construct general class-wide concept maps; direct personal student identity profiling is barred.
* Interaction models are restricted exclusively to alphanumeric plain-text character strings; speech-to-text input processing will not be evaluated.


## Observability

### Proactive

Metrics to track system health (latency, throughput, error rates):

- Inference Latency Metrics: Monitor the time-to-first-token and total generation times via Ollama log targets to flag performance dips.
- System Resource Thresholds: Set up alerts when host GPU VRAM or core server system memory saturation hits or exceeds 90% utilization limits.
- API Success Trackers: Monitor HTTP status distribution charts; any systemic spike in error codes (5xx) triggers automated notifications.

### Reactive

- Structured JSON Engine Logging: All backend middleware tracking events log clean JSON records containing standard severity levels (INFO, WARN, ERROR).
- Session Debugging Hooks: Developers can append validation flags (?debug=true) to active development requests to return raw compiled system prompt templates alongside the generated token stream.


## Risks

### Model Ignores the "Coaching" rules

#### Description

With the small model it may be possible it gives away the full answer, especially if a student was to prompt inject or keep pushing. If it gives its answers than we fail the purpose of the program

#### Mitigation Strategy

Keep the rules short and concrete. Run multiple behavior tests to see if or where the model fails and fix as needed. Never give the model the final answer thus making it so that it can not give it to the student. Content files <ins>ONLY</ins> ever contain: Instructions, objectives, misconceptions, and hints. <ins>NO</ins> solutions, answer keys, or student data. Can also include this rule on a pull request checklist. 

### Front-end and Back-end disagree

#### Description

While both teams are working in parallel and talking to each other, a mismatch in field names, ID's, or error handling could cause major problems and bugs. 

#### Mitigation Strategy

Take the front-end's EDD shape (class_id and assignment_id grouped under one object) and adopt it on the back-end as the starting point. Then both teams can review together and record the outcome in this document. Until then, nothing is hard-coded on either side of the field and we will create a contract in a singular place that both teams link to instead of copying.

### Student privacy in logs and backups removed

#### Description

Conversations are stored in the database, and backups include accounts, chats, and the secret(webui key). Verbose logging or loose backup could expose the student information.

#### Mitigation Strategy

Log identifiers and counts only, not the message text. keep the backups protected and off the server.

### Instructor content is late or unclear

#### Description

The assignment text, objectives, and hints have to come from the instructor. If they arrive late or leave gaps, the duck has nothing accurate to coach with and may invent requirements.

#### Mitigation Strategy

Make sure the assignment data is acquired well ahead of time and if it is missing, then do not allow students to select that particular assignment for help. In the coaching rules, tell the model to refer the student to the instructor if the instructions do not answer a question or are unclear. 

## Timeline & Scope

The big question to answer: will this take days, weeks, or months?

- Key milestones and phases.
- Dependencies and sequencing.
- Resource assumptions (team size, roles).
