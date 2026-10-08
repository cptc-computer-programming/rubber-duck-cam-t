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

[Who has the problem? What is difficult for them today, and what evidence do we have?]

LLMs: 

- give too much information
- gives non-personalized information
- gives non-contextualized information about how to solve the assignment
- LLMs don't have access to course materials

Assignee: Zac

## Desired outcome

> [!NOTE]  
> An AI tutor that helps students with beginner programming classes without giving away the answer to assignments. 

An AI chatbot that helps beginner computer programming students with programming assignments and concepts. It will do so by having access to program materials, including assignments, lectures, modules, and other course materials. 

The chatbot responds in plain, beginner friendly language. It focuses on student understanding of the underlying concept before addressing the specific assignment. It responds with short, step-by-step responses. It will not overwhelm the student with information, and it waits to move on until the student expresses understanding.

The tool can differentiate between what is core skill the student needs to know, and what is incidental knowledge. For example, the tool won't hestite to generate terminal commands if the student needs help. 

The tool is non-judgemental. 

- the tool works in multiple languages 
- easily accesible
- accessible on campus only to promote attendence

Assignee: Cody (cleanup)

## Scope

[Which users, use cases, and features will this version cover?]

**Use case** = a use case is a description of a system's behavior as users use it

Beginnering computer programming students at Clover Park Technical College

Assignee: Cody (fill this out more)


## Goals

- [A specific result we want to achieve and how we will measure success.]

- get students quicker help
- get students help independent of instructor availability
- get students deeper, more contextualized help

Assignnee: Francis

## Non-goals

- Replace personalized instruction
- Build an AI that can help with anything

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
- hint based assistance (provide progressively more specific hints rather than immediately giving the answer)
- explaining code errors easily

The product should:

- have a friendly, conversational persona
- individual student login
- saved conversation history tied to each student (up to 3 past conversations)
- instructor control board for course material updates and information

The product could:

- change level of hint assistance (depending on chosen level, depends on how in-depth the assistance is)
- disability friendly
- personalized study recommendations based on history
- practice examples similar to provided problem to help further understanding


Asignees: Zane and Anthony



## Design

[Briefly describe the proposed experience or solution. Link sketches, wireframes, or diagrams and explain key decisions.]

Assignee: Ivanna


