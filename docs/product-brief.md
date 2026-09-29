---
layout: default
title: "Rubber Duck: Product Brief"
---

[Documentation home]({{ '/' | relative_url }})

<div style="background: #122638; color: #ffffff; padding: 2rem; border-radius: 8px; margin: 1.5rem 0;">
  <p style="color: #ffd24a; font-size: 0.85rem; font-weight: bold; margin: 0;">PRODUCT BRIEF</p>
  <h1 style="color: #ffffff; border: 0; margin: 0.5rem 0;">Rubber Duck</h1>
  <p style="font-size: 1.2rem; margin: 0;">A chatbot for working through homework</p>
</div>

**On this page:** [Problem](#problem) · [Purpose](#purpose) · [Student guidance](#guidance-for-each-student) · [Instructor controls](#assignment-knowledge-and-instructor-control) · [Classroom use](#classroom-use) · [Campus access](#campus-access) · [Status](#current-status)

## Problem

 Beginning programmers need practice breaking problems into steps, tracing code, testing ideas, and finding their own mistakes. A complete AI-generated solution can skip that practice.

### Generic LLMs give too much help

- **Missing course context:** They do not automatically know the assignment's learning goals, what has been taught, or how much help is allowed.
- **Too much solution detail:** They may rewrite an entire program or introduce unfamiliar techniques when a small hint would be enough.
- **Less practice reasoning:** Following a correct explanation is different from developing and testing an approach independently.
- **Answers beginners cannot judge:** Students may struggle to spot errors in generated code or recognize when it does not fit the assignment.

### Students are going to use AI anyways

Banning AI in introductory classes does not remove students' access to it. This project assumes students will continue to use AI and aims to give instructors an option they can recommend while preserving the reasoning and practice students need.

## Purpose

Rubber Duck is a proposed homework chatbot that helps students **explain a problem, check their reasoning, and decide what to try next**. The amount of help should match their understanding while keeping them responsible for their own work.

> **Customer expectation**
>
> As the customer and instructor, I want a tool I can recommend to my classes with confidence that it supports learning and follows the expectations I set for each assignment.

## Guidance for each student

The chatbot should use a student's explanations, questions, and attempts to check what they understand. A correct or incorrect answer alone is not enough to judge that understanding.

| What the student needs | How the chatbot should help |
| :--- | :--- |
| A small nudge | Ask a question or offer a short hint. |
| Help with a basic concept | Explain it or use a separate example, then return to the assignment. |
| More support after an attempt | Ask what happened and adjust the guidance. |
| Space to continue independently | Step back once the student can explain what to do next. |

### Example: a loop that never stops

**Student:** “My loop never stops.”

1. **Ask:** What should end the loop, and what have you tried?
2. **Adjust:** If the student understands loop conditions, ask about the changing variable. If not, explain the concept with a separate example.
3. **Return to their work:** Ask the student to apply the idea to their code and explain the change.

## Assignment knowledge and instructor control

### Rubber Duck context

The chatbot should use instructor-provided **assignment instructions, learning goals, requirements, and course material**. Guidance should reflect what students have been taught. When information is missing or unclear, it should ask for clarification rather than invent requirements.

## Classroom use

For me to recommend it, the chatbot needs to:

- Give **accurate, useful guidance** in clear language.
- **Protect student information** and follow assignment rules.
- **Acknowledge uncertainty** and refer students to the instructor when it cannot help reliably.

> **Desired result:** Students leave with a better understanding of the problem and a next step they can explain.

## Campus access

**We are hosting the chatbot on a campus server to support our goal of bringing students to campus.** It should be a useful resource alongside instructors, classmates, and campus study spaces.

The chatbot should support those interactions and encourage students to seek help from people when the chatbot is not enough.


[Back to documentation]({{ '/' | relative_url }})
