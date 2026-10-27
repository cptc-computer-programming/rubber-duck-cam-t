---
layout: default
title: Product requirements template
---

# Product requirements: Rubber Duck CAM-T

**Authors:**
Anthony Brunner
Zac Kimball
Zane Marcoe
Ivanna Otero
Cody Pekarek
Francis Sengele


## Problem

The rapid integration of Large Language Models (LLMs) into educational environments has introduced unprecedented opportunities for automated tutoring; however, current off-the-shelf implementations fail to deliver pedagogically sound, targeted support. The fundamental bottleneck is contextual isolation. When students interact with generic LLMs, they are met with generalized data dumps rather than scaffolded guidance. This paradigm inadvertently bypasses the learning process, yielding outputs that are technically accurate in a vacuum but educationally counterproductive.

Standard, unspecialized LLMs exhibit several critical architectural and operational limitations in academic use cases:

- **Algorithmic Information Overload:** 
Instead of employing pedagogical scaffolding or targeted hints, general models generate overly verbose, fully resolved solutions that subvert critical thinking and knowledge retention.

- **Lack of Adaptive Personalization:**
Standard models operate with static personas, failing to dynamically adapt to a user’s current technical proficiency, underlying skill gaps, or unique cognitive learning style.

- **Misalignment with Structural Constraints:**
Generic LLMs deploy broad problem-solving methodologies that frequently violate the specific architectural constraints, formatting rules, and strict parameters mandated by an assignment's scope.

- **Curriculum Blindness:**
Operating without integration into proprietary academic assets—such as syllabi, grading rubrics, and direct lecture material—LLM outputs remain fundamentally detached from the localized learning objectives.

Ultimately, this systemic friction degrades the utility of AI in academic settings. When developers in training are debugging complex logic, they require targeted directional guidance to build syntactic muscle memory, not an automated proxy to do the work for them. Attempting to engineer a standard LLM into an effective teaching assistant introduces unacceptable latency and frustration into the development workflow. Instead of acting as an integrated, 24/7 tutor that reinforces core competencies, the technology devolves into a disconnected workaround. To realize the true potential of AI-assisted education, the architecture must evolve from providing unconstrained answers to delivering context-integrated, curriculum-aligned guidance.

**Assignee: Zac**

## Desired outcome

> [!NOTE]  
> An AI tutor that helps students with beginner programming classes without giving away the answer to assignments. An AI chatbot that helps beginner computer 
programming students with programming assignments and concepts. It will do so by having access to program materials, including assignments, lectures, modules, 
and other course materials. It will:

- Focus on student comprehension of underlying concepts
- Short, direct responses
- No complex information
- differentiate core skills and incidental knowledge
- the tool works in multiple languages 
- accessible on campus only to promote attendence
- the tool works in multiple languages
- accessible on campus only to promote attendence

## Scope

[Which users, use cases, and features will this version cover?]

**Use case** = a use case is a description of a system's behavior as users use it

Beginnering computer programming students at Clover Park Technical College

### Use Cases Covered in This Version

- A student selects their active assignment via a dropdown, providing key information for ollama, to determine necessary response.
- The chatbot will not give code blocks, only instructions and knowledge.
- The chatbot will use real world analogies to not overwhelm the student.
- If a student asks about concepts outside the intended scope of skills, it will provide a brief summary of the concept without delving too deep into the concept.

### Key Features

- Focused support for classes and their assignments.


## Goals

- Help beginner students learn programming.

- Help students when teachers are not available.

- Help students understand assignments step by step, without giving direct answers.

Assignnee: Francis

## Non-goals

- The AI tutor will not replace teachers.

- The AI tutor will not answer questions about everything. It will focus on programming.

Assignee: francis

## Assumptions

- [Something we believe is true that we still need to check.]

- There is a demand for this product (Students are already using AI in a non-productive way)
- CAM-T has computing resources than can support local AI development


Assumptions: Rachel

## Milestones

| Milestone | Target date | Owner(s) | Done when |
| --- | --- | --- | --- |
| [Checkpoint or deliverable] | [Date] | [Names] | [Observable result] |

## Requirements

Describe what the product must do and how we will check it works. Include constraints such as accessibility, privacy, or performance where relevant.


The product must:

- run an LLM
- be limited to beginner content
- be accessible only on campus
- be able to manage course content and allow admins to add and remove it
- limit its help to just programming problems
- support web and mobile web clients
- communicate in a way that is easy for beginner programmers to understand

The product should:

- have a friendly, conversational persona

The product could:


Asignees: Zane and Anthony



## Design

[Briefly describe the proposed experience or solution. Link sketches, wireframes, or diagrams and explain key decisions.]

Assignee: Ivanna


