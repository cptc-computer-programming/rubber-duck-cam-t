# Application logs

**Log flow:** Open WebUI → OpenTelemetry Collector → Loki

| Configuration | Purpose |
| --- | --- |
| [otel-collector.yaml](otel-collector.yaml) | Filter, sanitize, and forward logs |
| [loki.yaml](loki.yaml) | Store logs and apply retention |
| [grafana/datasources.yaml](grafana/datasources.yaml) | Connect Grafana Explore to Loki |

## Commands

From the repository directory, with the app's `.env` configured. Set
`GRAFANA_ADMIN_PASSWORD` to a separate password before starting.

**Validate and start**

```bash
docker compose config --quiet
docker compose up -d --wait --wait-timeout 600
```

**Check service logs**

```bash
docker compose logs --tail=50 otel-collector loki grafana
```

**Check disk usage**

```bash
docker system df -v
```

## Log coverage

| Item | Behavior |
| --- | --- |
| Collected | Warnings and errors from `open_webui.*` standard Python loggers |
| Stored | Time, severity, logger name, code location, and exception type when available |
| Removed | Message text, stack traces, and other attributes that could contain credentials or student conversations |
| Not collected | INFO/DEBUG events, third-party loggers, direct Loguru calls, prints, browser errors, Ollama, and other containers |
| Sanitization failure | Stops export |

**Limits:** An HTTP error does not always produce a log event. Docker console
logs are separate and are **not sanitized** by this pipeline.

## Query in Grafana

1. Open `http://localhost:3001` on the Docker host.
2. Sign in as `admin` using `GRAFANA_ADMIN_PASSWORD` from `.env`.
3. Open **Explore**, select **Loki**, and switch the query editor to **Code**.
4. Enter the query below, choose a time range, and select **Run query**.
5. Expand a log entry to see its diagnostic metadata.

For a remote server, run this on your computer and then open `http://localhost:3001`:

```bash
ssh -L 3001:127.0.0.1:3001 user@server-address
```

The `.env` password initializes the account only on first startup. Change an
existing password in Grafana; editing `.env` does not reset it.

| Setting | Value |
| --- | --- |
| Loki address | `http://loki:3100` — internal Docker access only |
| Metadata fields | `exception_type`, `code_file_path`, `code_line_number` |

**Explore query**

```logql
{service_name="open-webui"}
```

No results means no matching stored events in that time range; it does not prove
collection is working. Dashboards and Prometheus integration remain in
[#17](https://github.com/cptc-computer-programming/rubber-duck-cam-t/issues/17).

## Storage

| Setting | Value |
| --- | --- |
| Persistent volume | `loki-data` |
| Retention | 7 days |
| Intake limit | 0.01 MiB/second; 1 MiB burst. Excess events may be rejected. |
| Disk budget | At least 10 GiB for logs and overhead; **not a disk quota** |

- **Preserves history:** stopping Compose or recreating containers.
- **Deletes history:** `docker compose down --volumes`.
- **Backup gap:** the host `data/` backup does not include `loki-data`.

Grafana accounts and settings persist in `grafana-data`. That volume is also
excluded from the host `data/` backup and deleted by `down --volumes`.

Acceptance checks and results: [#16](https://github.com/cptc-computer-programming/rubber-duck-cam-t/issues/16).
