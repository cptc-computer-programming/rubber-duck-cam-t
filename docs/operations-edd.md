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

#### Investigating problems

Metrics help us spot a problem, but they may not explain what happened. We need a shared place to search application logs and inspect errors from the same time period.

#### Experimentation
As we try different prompts, models, and approaches to homework help, we need evidence of what works. Health metrics are a starting point, but they do not tell us whether the AI is helping students understand the material.

#### Enterprise-level decisions

 We need a shared view to help the program decide where to invest time and resources:

- Which classes and assignments do students need help with?
- Can the application meet demand?
- Do changes improve the help students receive?

### Proposed Solution

**M1 — establish monitoring.** We will use Open WebUI's built-in metrics and log export with **OpenTelemetry Collector, Prometheus, Loki, and Grafana**. The dashboard will show:

- Request volume
- HTTP errors
- Request duration
- Application logs for investigating problems

This gives us a shared place to check performance, investigate failures, and see the effects of testing and changes. Prometheus will store metrics and Loki will store logs. Grafana will let us query both.

We will self-host Grafana OSS and Loki on the lab server. Neither requires a paid subscription or software license fee; storage, computing resources, and maintenance still use lab resources. See [Grafana licensing](https://grafana.com/licensing/) and [Loki OSS](https://grafana.com/oss/loki/).

**Later milestones — support experimentation and decisions.** Grafana will give us a place to build dashboards for enterprise-level decision making as we identify and collect the right data. We want to:

- Compare prompt and model versions.
- Understand which assignments and classes students use most.
- Use evals to assess how well the AI helps students.


## Technical Design

### Overview

The monitoring services will run alongside the app. Open WebUI will send metrics and logs to the Collector. Prometheus will collect metrics from the Collector, while the Collector will forward logs to Loki. Grafana will query both stores. Dashboards and saved queries will be defined in repository files.

### Technical Details

#### System Diagrams

```text
Lab server
  Open WebUI <--> Ollama
      |
      | HTTP metrics and application logs
      v
  OpenTelemetry Collector
      ^                         |
      | collects metrics        | forwards logs
  Prometheus                    v
      ^                        Loki
      |                         ^
      +----- Grafana queries ---+
```

#### APIs

For M1, we will use Open WebUI's existing health endpoint and built-in metrics and log export.

| Open WebUI capability | How we will use it |
| --- | --- |
| `GET /health` | Continue checking application health through the existing container health check. |
| `http.server.requests` counter | Measure request volume and identify HTTP errors using status codes. |
| `http.server.duration` histogram | Track HTTP request duration over time. |
| OpenTelemetry log export | Send available application logs to the Collector using `ENABLE_OTEL_LOGS` and `OTEL_LOGS_EXPORTER_OTLP_ENDPOINT`. |

The metrics include HTTP method, route, and status code labels. Open WebUI exports them through OpenTelemetry; they are not REST endpoints we call. We will verify the metrics against our deployed version. See the [Open WebUI monitoring guide](https://docs.openwebui.com/reference/monitoring/otel/).

For later milestones, we still need to identify which Open WebUI APIs can support assignment usage tracking and repeatable evals across prompt and model versions.

#### Log collection and storage

We will start with logs exported by Open WebUI. We still need to verify which events and fields our deployed version exports; this does not automatically collect Ollama or other containers' logs.

| Component | Configuration needed |
| --- | --- |
| Collector | Add a logs pipeline using the OTLP receiver and an `otlphttp` exporter pointing to Loki's `/otlp` endpoint. Keep the metrics pipeline separate. |
| Loki | Configure a single instance with persistent storage under `/loki`, a compatible TSDB schema, and structured metadata enabled for OTLP logs. Set retention and storage limits before deployment. |
| Grafana | Provision Loki as a second data source. Define log panels and LogQL queries in dashboard files. |

Keep Loki internal to the Compose network and control user access through Grafana. Inspect sample logs before enabling collection; exclude credentials and student conversation content. Log levels, retention, and filtering rules remain to be decided.

See [Loki OpenTelemetry ingestion](https://grafana.com/docs/loki/latest/send-data/otel/) and [Loki storage](https://grafana.com/docs/loki/latest/configure/storage/).


#### Grafana configuration

We will define the Prometheus and Loki data sources and dashboard provisioning in YAML. Dashboards will contain PromQL queries for metrics and LogQL queries for logs in JSON. These files will live in the repository and be mounted through Compose. Changes will be reviewed in Git, with UI saving disabled for provisioned dashboards. Grafana will be used to view and explore the data.

This keeps the setup reproducible and lets us track and roll back changes. See [Grafana provisioning](https://grafana.com/docs/grafana/latest/administration/provisioning/).

#### Docker Compose and lab configuration

Key additions to `compose.yaml` are outlined below. This is a draft snippet to merge into the existing configuration. Image versions and lab settings remain open; the mounted configuration files still need to be created.

```yaml
services:
  open-webui:
    environment: # Add to the existing environment settings.
      ENABLE_OTEL: "true"
      ENABLE_OTEL_METRICS: "true"
      ENABLE_OTEL_LOGS: "true"
      OTEL_METRICS_EXPORTER_OTLP_ENDPOINT: "${OTEL_METRICS_ENDPOINT:?Set Collector endpoint}"
      OTEL_LOGS_EXPORTER_OTLP_ENDPOINT: "${OTEL_LOGS_ENDPOINT:?Set Collector endpoint}"
      OTEL_EXPORTER_OTLP_INSECURE: "${OTEL_INSECURE:?Choose TLS setting}"

  otel-collector:
    image: "otel/opentelemetry-collector-contrib:${OTEL_VERSION:?Choose version}"
    restart: unless-stopped
    command: ["--config=/etc/otelcol/config.yaml"]
    volumes:
      # Receive OTLP, expose metrics, and forward logs to Loki.
      - ./monitoring/otel-collector.yaml:/etc/otelcol/config.yaml:ro

  prometheus:
    image: "prom/prometheus:${PROMETHEUS_VERSION:?Choose version}"
    restart: unless-stopped
    volumes:
      # Configure the Collector as a scrape target.
      - ./monitoring/prometheus.yaml:/etc/prometheus/prometheus.yml:ro
      - prometheus-data:/prometheus

  loki:
    image: "grafana/loki:${LOKI_VERSION:?Choose version}"
    restart: unless-stopped
    command: ["-config.file=/etc/loki/config.yaml"]
    volumes:
      # Configure storage under /loki, OTLP support, and retention.
      - ./monitoring/loki.yaml:/etc/loki/config.yaml:ro
      - loki-data:/loki

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
      # Provision Prometheus, Loki, and the dashboard provider.
      - ./monitoring/grafana/provisioning:/etc/grafana/provisioning:ro
      - ./monitoring/grafana/dashboards:/var/lib/grafana/dashboards:ro

volumes:
  prometheus-data:
  loki-data:
  grafana-data:
```

Configuration references: [Collector](https://opentelemetry.io/docs/collector/configuration/), [Prometheus](https://prometheus.io/docs/prometheus/latest/installation/), [Grafana](https://grafana.com/docs/grafana/latest/setup-grafana/installation/docker/).

#### Deployment

Deployment is manual. We will pull the latest changes from `rubber-duck-cam-t` on the lab server, then run Docker Compose to apply the updated configuration and start the services. The monitoring configuration and dashboard will be kept in the repository.

## Out of Scope

- Frontend assignment selection and backend coaching behavior belong to Teams 1 and 2.
- Assignment and class usage metrics, prompt and model comparisons, and AI evals are future work beyond M1.
- Collecting Ollama, host, and other containers' logs is outside the initial logging setup.


## Observability

### Proactive

The Grafana dashboard will show request volume, HTTP errors, and request duration over time. We will also check monitoring storage usage and distinguish missing data from healthy activity.

### Reactive

We will generate requests and a controlled failure in the development environment, then verify the metrics and available error logs in Grafana. We will compare logs and metrics over the same time range to investigate failures.

We will check that stored logs survive container recreation. If data is missing, we will trace the metrics path through Prometheus or the logs path through Loki, starting at Open WebUI and the Collector.


## Risks

| Risk | Description | Mitigation |
| --- | --- | --- |
| Health metrics do not measure learning | A successful HTTP request does not tell us whether the response helped the student. | Explain the dashboard's limits. Use separate evals to assess effectiveness. |
| Measuring the wrong thing | Heavy usage could mean the tool is useful, or that students keep asking because they are stuck. | Define each metric and its limits before using it to make decisions. |
| Experiments that cannot be compared | Changing the prompt, model, and assignment together makes it hard to identify what helped. | Track versions, use consistent evaluation cases, and change one factor at a time where practical. |
| Logs contain sensitive data | Logs could include credentials or student conversation content. | Inspect exported fields, filter sensitive content, and restrict access. |
| Logs consume lab resources | Growing log storage could affect the application on the same server. | Set retention and storage limits and monitor disk usage. |
| Incomplete log coverage | Open WebUI's export may omit events needed to explain a failure. | Verify coverage with controlled failures and document what is missing. |

## Timeline & Scope

This effort should take on the order of days to complete. 
