# Application logs

Open WebUI sends logs through the Collector to Loki. Configuration is in
`otel-collector.yaml` and `loki.yaml`.

## Run

From the repository directory, with the app's `.env` configured:

```bash
docker compose config --quiet
docker compose up -d --wait --wait-timeout 600
docker compose logs --tail=50 otel-collector loki
```

## What is stored

Only warnings and errors from `open_webui.*` standard Python loggers are kept:
time, severity, logger name, code location, and exception type when available.
Message text, stack traces, and other attributes are removed before storage
to exclude credentials and student conversations. Sanitization failures stop
export. Docker console logs are separate and are not sanitized here.

Direct Loguru calls, prints, browser errors, Ollama, and other containers are
not collected. An HTTP error does not always produce a log event.

Loki is internal to Docker. Grafana (#17) will use `http://loki:3100` and the
query `{service_name="open-webui"}`. Metadata fields include `exception_type`,
`code_file_path`, and `code_line_number`.

## Storage

The `loki-data` volume preserves logs when containers are recreated or Compose
is stopped. `docker compose down --volumes` deletes that history. The existing
backup of the host `data/` directory does not back up this named volume.

Logs are retained for seven days. Intake is limited to 0.01 MiB/second with a
1 MiB burst; excess events may be rejected. Budget at least 10 GiB for logs
and storage overhead. This is not a disk quota. Check usage with
`docker system df -v`.

Acceptance checks and results are tracked in #16.
