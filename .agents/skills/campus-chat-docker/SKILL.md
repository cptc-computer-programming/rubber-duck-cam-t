---
name: campus-chat-docker
description: Set up or change the campus chatbot Docker Compose deployment, or explain its services and configuration.
---

# Campus chatbot Docker

Read `README.md`, `DEVELOPMENT.md`, and `compose.yaml` at the repository root first.

- Keep M1 small: Open WebUI plus Ollama using the NVIDIA GPU. Add tutoring features or other services only when requested.
- Run Compose commands from the repository root. Confirm the target server before starting or changing a deployment; this checkout may be on a different machine.
- Preserve pinned images, private Ollama networking, authentication, and persistent data unless the requested change requires otherwise.
- Keep `.env` secrets out of output and Git. Use `docker compose config --quiet` to validate configuration without printing secrets.
- Apply configuration changes with `docker compose up -d --wait --wait-timeout 600`; `restart` alone does not apply them.
- Follow the README for first administrator setup and campus access. Use DEVELOPMENT.md for backup and restoration before upgrades.
- Explain changes briefly and distinguish configuration validation from live chat and GPU verification. If Docker or the target server is unavailable, report what remains untested.
