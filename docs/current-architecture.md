---
layout: default
title: "Rubber Duck: Current Architecture"
---

[Documentation home]({{ '/' | relative_url }})

# How the app is currently built

The current app is a campus-hosted chatbot assembled from two published container images: Open WebUI and Ollama. Docker Compose connects and runs them on a Linux server with an NVIDIA GPU. This repository contains the deployment configuration and documentation; it does not contain a custom application frontend or backend.

This page describes the configuration checked into the repository, rather than a verified running deployment.

## Architecture

```text
Student or instructor's browser
              |
              | HTTP: host port 3000 by default
              v
+--------------------------------------------------------+
| Campus Linux server                                    |
|                                                        |
|  Docker Compose project: campus-chat                   |
|  +--------------------------------------------------+  |
|  | Open WebUI (container port 8080)                  |  |
|  | Chat interface, sign-in, accounts, conversations  |  |
|  +--------------------------------------------------+  |
|              |                       |                 |
|              | Internal model API    +--> Host storage |
|              | http://ollama:11434    data/open-webui/  |
|              v                                         |
|  +--------------------------------------------------+  |
|  | Ollama                                           |  |
|  | Loads models and generates responses             |  |
|  +--------------------------------------------------+  |
|              |                       |                 |
|              v                       +--> Host storage |
|         NVIDIA GPU                   data/ollama/      |
+--------------------------------------------------------+
```

### What happens when someone sends a message

1. The user opens Open WebUI in a browser and signs in. The first account becomes the administrator; subsequent users default to pending approval.
2. Open WebUI handles the conversation and sends the model request to Ollama over Docker's internal network.
3. Ollama runs the selected downloaded model using the server's available GPU resources. The setup guide uses `llama3.2:3b`, downloaded separately after startup.
4. The response returns through Open WebUI to the browser. Open WebUI's accounts, conversations, and settings are stored separately from Ollama's downloaded models.

### Services, storage, and access

- **Open WebUI** provides the application interface and account management. Its data directory is mapped to `data/open-webui/` on the host so data survives container recreation. The configuration requires sign-in and disables the OpenAI API integration.
- **Ollama** provides local model inference. Its model directory is mapped to `data/ollama/`. It has no published host port; Open WebUI reaches it by the Docker service name `ollama`.
- **Docker Compose** starts Ollama first and waits for its health check before starting Open WebUI. Both services have health checks and use `restart: unless-stopped` to recover after failure or reboot unless deliberately stopped.
- **Local configuration** comes from `.env`, based on `.env.example`. It supplies the web binding address, web port, and required `WEBUI_SECRET_KEY`. Open WebUI also saves settings in its own data store; saved settings can override environment values.
- **Browser access** initially binds to `127.0.0.1:3000` for administrator setup. A remote computer can connect using an SSH tunnel. Campus access requires changing the binding address and arranging network access with IT. The checked-in deployment serves HTTP; it does not include an HTTPS proxy.

### Current scope

The implemented foundation provides chat, user accounts, conversation storage, and local model access. Assignment knowledge, instructor-controlled homework guidance, and adaptive rubber-duck behavior described in the product brief still need to be developed and evaluated.

## Tech stack

| Layer | Technology | Current role or configuration |
| --- | --- | --- |
| Browser application | Open WebUI `v0.11.3` | Published image `ghcr.io/open-webui/open-webui:v0.11.3`; provides chat, accounts, and administration. |
| Model runtime | Ollama `0.33.3` | Published image `ollama/ollama:0.33.3`; serves locally downloaded models. |
| Starter model | Llama 3.2 3B (`llama3.2:3b`) | Model used in the setup instructions; downloaded after startup, not bundled into Compose. |
| Container deployment | Docker Engine and Docker Compose `2.30+` | Runs the two services, internal networking, storage mounts, health checks, and restart policies. |
| Host operating system | Linux | Target environment for the campus deployment. |
| GPU acceleration | NVIDIA GPU, NVIDIA driver, and NVIDIA Container Toolkit | Makes host GPUs available to Ollama through `gpus: all`. |
| Persistent storage | Host directories mounted into containers | `data/open-webui/` for application data and `data/ollama/` for models; no separate database service is declared. |
| Deployment configuration | YAML and environment variables | `compose.yaml` defines the deployment; `.env` holds local settings and the secret. |
| Source control and documentation | Git, Markdown, and Jekyll/Liquid page templates | Tracks deployment files and provides the repository's Pages documentation. |



