# Application logs

Open WebUI sends logs to the existing Collector. The Collector keeps warnings
and errors from `open_webui.*` Python loggers and forwards them to Loki.
Loki is internal to Docker; Grafana will connect to `http://loki:3100` in #17.

## Run

From the repository directory, with the app's `.env` configured:

```bash
docker compose config --quiet
docker compose up -d --wait --wait-timeout 600
docker compose logs --tail=50 otel-collector loki
```

The first startup downloads Loki `3.7.10`. Configuration is in `loki.yaml` and
the logs pipeline in `otel-collector.yaml`. Both are mounted read-only.

## What is stored

Logs retain their time, severity, logger name, code location, and exception
type when available. The Collector replaces message text and removes stack
traces and all other log attributes before forwarding. Resource attributes
are replaced with the fixed service name `open-webui`; scope attributes are
removed. Sanitization errors stop export rather than forwarding raw records.

This deliberately excludes passwords, keys, student conversations, request
bodies, and exception messages from the stored log content. It also limits
diagnosis: developers can identify where and what kind of error occurred,
but cannot read the original message. Docker's application console logs are
separate and are not sanitized by this pipeline.

The inspected fork's `utils/logger.py` forwards standard Python logging through
its OTel handler. Direct Loguru calls, plain prints, browser errors, Ollama logs,
and other containers' logs are not covered by this pipeline. INFO/DEBUG events
and third-party loggers are filtered out. An HTTP error does not necessarily
produce a log event; the HTTP metrics still record its status.

In Grafana Explore, use `{service_name="open-webui"}`. Loki converts attribute
dots to underscores, so exception type is `exception_type` and code location
uses fields such as `code_file_path` and `code_line_number`.

## Storage

The `loki-data` volume preserves logs when containers are recreated or Compose
is stopped. `docker compose down --volumes` deletes that history. The existing
backup of the host `data/` directory does not back up this named volume.

Logs are retained for seven days and removed by Loki's compactor. Sustained
ingestion is limited to 0.01 MiB/second (about 6 GiB/week before compression),
with a 1 MiB burst allowance. Allow at least 10 GiB of free disk for logs,
indexes, the write-ahead log, and delayed cleanup; this is not a disk quota.
Check usage with `docker system df -v`. During a sustained error flood Loki can
reject events, so log coverage may be incomplete.

Acceptance checks and results belong in #16. Before closing it, generate a
controlled error through the development app and confirm its sanitized event
appears in Loki. Grafana setup remains in #17.
