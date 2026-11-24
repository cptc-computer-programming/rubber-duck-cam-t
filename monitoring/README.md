# Monitoring

**Log flow:** Open WebUI → OpenTelemetry Collector → Loki

**Metrics flow:** Open WebUI → OpenTelemetry Collector → Prometheus → Grafana

| Configuration | Purpose |
| --- | --- |
| [otel-collector.yaml](otel-collector.yaml) | Filter and forward application warnings and errors |
| [loki.yaml](loki.yaml) | Store logs and apply retention |
| [prometheus.yaml](prometheus.yaml) | Scrape metrics and application health; retain metrics |
| [blackbox.yaml](blackbox.yaml) | Check the app's HTTP health endpoint |
| [grafana/datasources.yaml](grafana/datasources.yaml) | Connect Grafana to Prometheus and Loki |
| [grafana/dashboards.yaml](grafana/dashboards.yaml) | Load dashboards from Git |
| [grafana/dashboards/operations.json](grafana/dashboards/operations.json) | Define the operations dashboard and panel queries |

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
| Stored | Original messages and attributes, including exception type, message, and stack trace when supplied by the app |
| Not collected | INFO/DEBUG events, third-party loggers, direct Loguru calls, prints, browser errors, Ollama, and other containers |

**Limits:** An HTTP error does not always produce a log event. Exception traces
require the application to log with exception information (for example,
`exc_info=True`). Docker console logs are separate from this pipeline.

**Learning environment policy:** Logs are not redacted and may contain credentials
or student content. Loki retains them for seven days. This intentionally relaxes
the original sensitive-data exclusion requirement in #16.

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
| Metadata fields | `exception_type`, `exception_message`, `exception_stacktrace`, `code_file_path`, `code_line_number` |

**Explore query**

```logql
{service_name="open-webui"}
```

Search original message text:

```logql
{service_name="open-webui"} |= "Error processing chat metadata"
```

Filter by exception type and expand the entry to inspect its stack trace:

```logql
{service_name="open-webui"} | exception_type="AttributeError"
```

No results means no matching stored events in that time range; it does not prove
collection is working.

## Operations dashboard

Open **Dashboards → Rubber Duck — Operations**. It loads automatically, shows
the last hour, and refreshes every 30 seconds. Edit its JSON file in Git;
Grafana checks for dashboard file changes every 30 seconds.

| Panel | Meaning |
| --- | --- |
| Application reachable | `/health` responds successfully inside Docker; does not test inference or browser access |
| Metrics collection | Prometheus can scrape the Collector; does not confirm fresh app exports |
| HTTP errors | Percentage of HTTP requests returning 4xx or 5xx |
| HTTP response startup time | Median and p95 response startup time in milliseconds; **not full streamed answer duration** |
| Requests per minute | Average HTTP request rate, scaled to one minute |
| Total requests | Estimated count during the selected time range |
| Chat requests per minute | POST requests to the two chat-completion routes, including rejected requests |
| Failed chat requests | HTTP 4xx/5xx and background-processing/stream failures per minute, shown separately; these may overlap |
| Time to first token | Median/p95 time from request arrival to first text chunk at the backend |
| Full answer duration | Median/p95 time from request arrival to successful answer completion |

The chat timing panels require the updated WebUI image. First-token timing is
for streaming text only (not role, reasoning, or tool-call chunks). Full-answer
timing includes tool continuations for UI chats and excludes cancelled/failed
answers and later title/tag generation. One request can produce several model
answers. Timings exclude browser/network delivery, and contain no user or chat labels.
These panels show **No data** until matching observations have been exported.

Traffic and latency exclude `/health` and include both UI and API requests.
**No data** and chart gaps mean unavailable measurements or, for latency/error
percentage, no traffic. They are not proof of health. The Collector can retain
app measurements for up to five minutes after app exports stop.

After changing service or data-source configuration, apply it with:

```bash
docker compose up -d --no-deps --force-recreate blackbox-exporter prometheus grafana
```

The health probe uses Blackbox Exporter **v0.29.1**, checks every **15 seconds**,
and times out after **5 seconds**. It exposes no host port and stores no data.

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
