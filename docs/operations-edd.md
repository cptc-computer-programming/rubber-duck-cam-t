---
layout: default
title: Team 3 Operations Engineering Design Document
---

[Documentation home]({{ '/' | relative_url }})

# Team 3 Operations Engineering Design Document

**Document Version: 1.0**

## Executive Summary

| Field | Value |
| --- | --- |
| **Status** | DRAFT |
| **Created Date** | October 20, 2026 |
| **Author** | Rachel King|
| **EDD Size** | Small |
| **Consulted** | Joe Ortiz|
| **Supporting Documentation** | [M1](m1.html), [Current architecture](current-architecture.html) |

## Overview

### Problem

#### Application performance
Students depend on the app responding when they need help. Our team needs visibility into performance so we can spot problems and understand their impact. The current Compose configuration runs Open WebUI and Ollama without a monitoring stack. We need a shared view of:

- Request volume
- Response times
- Failures over time

#### Experimentation
As we try different prompts, models, and approaches to homework help, we need evidence of what works. Health metrics are a starting point, but they do not tell us whether the AI is helping students understand the material.

#### Enterprise-level decisions

 We need a shared view to help the program decide where to invest time and resources:

- Which classes and assignments do students need help with?
- Can the application meet demand?
- Do changes improve the help students receive?

### Proposed Solution

**M1 — establish monitoring.** We will use Open WebUI's built-in HTTP metrics with **OpenTelemetry Collector, Prometheus, and Grafana**. The dashboard will show:

- Request volume
- HTTP errors
- Request duration

This gives us a shared place to check performance and see the effects of testing and changes.

**Later milestones — support experimentation and decisions.** Grafana will give us a place to build dashboards for enterprise-level decision making as we identify and collect the right data. We want to:

- Compare prompt and model versions.
- Understand which assignments and classes students use most.
- Use evals to assess how well the AI helps students.


## Technical Design

### Overview

The monitoring services will run alongside the app. Open WebUI will send metrics to the Collector. Prometheus will collect and store them, and Grafana will query Prometheus. Grafana provides a UI to create custom views around the collected metrics.

### Technical Details

#### System Diagrams

```text
Lab server
  Open WebUI <--> Ollama
      |
      | HTTP metrics
      v
  OpenTelemetry Collector
      ^
      | collects metrics
  Prometheus
      ^
      | queries metrics
  Grafana
```

#### APIs

For M1, we will use Open WebUI's existing health endpoint and built-in metrics export.

| Open WebUI capability | How we will use it |
| --- | --- |
| `GET /health` | Continue checking application health through the existing container health check. |
| `http.server.requests` counter | Measure request volume and identify HTTP errors using status codes. |
| `http.server.duration` histogram | Track HTTP request duration over time. |

The metrics include HTTP method, route, and status code labels. Open WebUI exports them through OpenTelemetry; they are not REST endpoints we call. We will verify the metrics against our deployed version. See the [Open WebUI monitoring guide](https://docs.openwebui.com/reference/monitoring/otel/).

For later milestones, we still need to identify which Open WebUI APIs can support assignment usage tracking and repeatable evals across prompt and model versions.


#### Docker Compose and lab configuration

Key additions to `compose.yaml` are outlined below. This is a draft snippet to merge into the existing configuration. Image versions and lab settings remain open; the mounted configuration files still need to be created.

```yaml
services:
  open-webui:
    environment: # Add to the existing environment settings.
      ENABLE_OTEL: "true"
      ENABLE_OTEL_METRICS: "true"
      OTEL_METRICS_EXPORTER_OTLP_ENDPOINT: "${OTEL_METRICS_ENDPOINT:?Set Collector endpoint}"
      OTEL_EXPORTER_OTLP_INSECURE: "${OTEL_INSECURE:?Choose TLS setting}"

  otel-collector:
    image: "otel/opentelemetry-collector-contrib:${OTEL_VERSION:?Choose version}"
    restart: unless-stopped
    command: ["--config=/etc/otelcol/config.yaml"]
    volumes:
      # Receive OTLP and expose metrics for Prometheus.
      - ./monitoring/otel-collector.yaml:/etc/otelcol/config.yaml:ro

  prometheus:
    image: "prom/prometheus:${PROMETHEUS_VERSION:?Choose version}"
    restart: unless-stopped
    volumes:
      # Configure the Collector as a scrape target.
      - ./monitoring/prometheus.yaml:/etc/prometheus/prometheus.yml:ro
      - prometheus-data:/prometheus

  grafana:
    image: "grafana/grafana:${GRAFANA_VERSION:?Choose version}"
    restart: unless-stopped
    ports:
      # Choose a host port different from Open WebUI's.
      - "${GRAFANA_BIND_ADDRESS:?Set lab address}:${GRAFANA_PORT:?Choose port}:3000"
    environment:
      GF_SECURITY_ADMIN_PASSWORD: "${GRAFANA_ADMIN_PASSWORD:?Set locally}"
    volumes:
      - grafana-data:/var/lib/grafana
      # Provision the Prometheus data source and dashboard provider.
      - ./monitoring/grafana/provisioning:/etc/grafana/provisioning:ro
      - ./monitoring/grafana/dashboards:/var/lib/grafana/dashboards:ro

volumes:
  prometheus-data:
  grafana-data:
```

Configuration references: [Collector](https://opentelemetry.io/docs/collector/configuration/), [Prometheus](https://prometheus.io/docs/prometheus/latest/installation/), [Grafana](https://grafana.com/docs/grafana/latest/setup-grafana/installation/docker/).

#### Deployment

Deployment is manual. We will pull the latest changes from `rubber-duck-cam-t` on the lab server, then run Docker Compose to apply the updated configuration and start the services. The monitoring configuration and dashboard will be kept in the repository.

## Out of Scope

- Frontend assignment selection and backend coaching behavior belong to Teams 1 and 2.
- Assignment and class usage metrics, prompt and model comparisons, and AI evals are future work beyond M1.


## Observability

### Proactive

The Grafana dashboard will show request volume, HTTP errors, and request duration over time. 

### Reactive

We will generate requests in the development environment and show how the dashboard changes. If measurements are missing, we will trace the connections from Open WebUI through the Collector, Prometheus, and Grafana.


## Risks

| Risk | Description | Mitigation |
| --- | --- | --- |
| Health metrics do not measure learning | A successful HTTP request does not tell us whether the response helped the student. | Explain the dashboard's limits. Use separate evals to assess effectiveness. |
| Measuring the wrong thing | Heavy usage could mean the tool is useful, or that students keep asking because they are stuck. | Define each metric and its limits before using it to make decisions. |
| Experiments that cannot be compared | Changing the prompt, model, and assignment together makes it hard to identify what helped. | Track versions, use consistent evaluation cases, and change one factor at a time where practical. |

## Timeline & Scope

This effort should take on the order of days to complete. 
