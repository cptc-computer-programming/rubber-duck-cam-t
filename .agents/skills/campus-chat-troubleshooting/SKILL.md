---
name: campus-chat-troubleshooting
description: Diagnose startup, model, GPU, access, or persistence problems in the campus chatbot, including requested restarts and resets.
---

# Campus chatbot troubleshooting

Read `compose.yaml` and `DEVELOPMENT.md` at the repository root. Confirm which server and deployment have the problem.

Start with evidence relevant to the symptom:

- Service status: `docker compose ps`.
- Recent errors: `docker compose logs --tail=100 open-webui ollama`. Summarize relevant errors without exposing credentials or private conversations.
- Missing models: `docker compose exec ollama ollama list`.
- GPU use: `docker compose exec ollama ollama ps` immediately after a reply; use the GPU prerequisite checks in DEVELOPMENT.md if needed.
- Campus access: check the configured bind address, published port, and campus routing. Do not broaden network access simply to make a test pass.
- Ignored settings: Open WebUI database settings can override environment values; inspect the Admin Panel before assuming Compose is wrong.

Choose the smallest fix supported by the evidence. Follow DEVELOPMENT.md for restart, recreation, backup, and reset commands. Ordinary restarts preserve data; moving data aside creates a fresh instance. Do not reset accounts, chats, settings, or models unless the user requested that scope. Preserve a backup before a reset.

Verify the original symptom after the fix. Report the cause if established, the change, and any remaining checks; a healthy container alone does not prove chat works.
