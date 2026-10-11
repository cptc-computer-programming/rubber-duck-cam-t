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

The chatbot responds in plain, beginner friendly language. It focuses on student understanding of the underlying concept before addressing the specific assignment. It responds with short, step-by-step responses. It will not overwhelm the student with information, and it waits to move to move on until the student expresses understanding.

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

The product should:

- have a friendly, conversational persona

The product could:


Asignees: Zane and Anthony



## Design

It is a minimalist and modern design approach with both light and dark mode support. The layout is divided into two primary sections: a fixed left navigation sidebar and a spacious content area that prioritizes user interaction and readability. 

**Visual Style**
- The design uses a soft, uncluttered interface with generous whitespace to reduce cognitive load.
- A handwritten-style typography gives the platform a friendly, approachable, and student-focused personality.
- A vibrant purple accent color is consistently used for interactive elements such as buttons, icons, dropdown indicators, and input field borders, matching the school colors.
- A subtle green vertical divider separates the navigation panel from the main workspace, providing clear structure without overwhelming the interface, also matching the school colors!

**Navigation Sidebar** 
- Positioned on the left side for quick access to core features.
- Includes sections for:
  - Notes
  - Class
  - Topic
  - Chats
- Class and Topic menus use expandable dropdown indicators for hierarchical navigation.
- The CAM-T logo is placed at the top-left corner, reinforcing branding and providing a familiar anchor point.

**Header Area** 
- Located in the upper-right corner.
- Contains:
  - User authentication controls (Log In / Log out)
  - Theme switching option (Light and Dark Mode)
- The simple icon-based controls maintain a clean and intuitive appearance.

**Main Content Area** 
The central workspace emphasizes focus and interaction through minimal visual distractions. 

**Welcome Screen** 
- Displays a personalized greeting such as "Hello... (User Name)".
- Includes a centered rounded input field for user queries, notes, or conversation prompts.
- The layout creates a welcoming and conversational experience.

**Chat Interface**
- Features a large, rounded conversation panel positioned toward the uppser-right sections. Simulating a users message.
- A message input field is placed near the bottom center for easy communication.
- Rounded corners and outlined components create a soft, modern appearance while maintaining visual consitency.

**Dark Mode** 
- Uses a deep charcoal background instead of pure black to reduce eye strain.
- Maintains the same purple accent color for interactive elements and focus states.
- Light typography provided strong readability and contrast.
- Preserves the exact layout and component hierarchy of the light mode fora seamless user experience.

Link to the design: https://canva.link/vftgqy8whcpiyis
Assignee: Ivanna


