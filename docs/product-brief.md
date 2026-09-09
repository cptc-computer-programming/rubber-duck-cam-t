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

## Purpose

Rubber Duck is a proposed homework chatbot for students who need help getting started, understanding a problem, or checking their approach. It should ask questions, respond to what students have tried, and offer hints that help them continue their own work.

The name comes from rubber duck debugging: explaining a problem aloud can help someone notice an error or figure out what they do not understand.

## Goals

- Help students explain where they are stuck.
- Give questions, hints, and feedback suited to their current understanding.
- Help students understand their work and continue independently.
- Use clear language, protect student information, and respect assignment rules.
- Acknowledge uncertainty when it cannot give reliable help.

## Example

A student says, "My loop never stops." The chatbot asks what should end the loop and helps them trace a small example. The student identifies the condition to check and explains the change they would make.

## Current status

The campus chatbot uses Open WebUI and Ollama for conversations, user accounts, and model access. This provides the starting point for the project. The homework-specific behavior still needs to be developed and evaluated.

The goal is for students to leave a conversation with a better understanding of the problem and a next step they can explain.
