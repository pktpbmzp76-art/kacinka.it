# Monitoring & Observability Audit

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Executive Summary

| Pillar | Score | Livello |
|--------|-------|---------|
| **Error Tracking** | 8.5/10 | Advanced |
| **Alerting** | 7.5/10 | Managed |
| **Request Logging** | 7.0/10 | Managed |
| **Performance Tracking** | 5.0/10 | Initial |
| **Infrastructure Monitoring** | 4.5/10 | Initial |
| **Log Aggregation** | 3.5/10 | Initial |
| **Distributed Tracing** | 2.0/10 | None |
| **Dashboards & Visualization** | 3.0/10 | Initial |
| **OVERALL** | **5.1/10** | **Initial → Managed** |

> Kacinka ha un'ottima base di error tracking (Sentry) e alerting (Telegram), ma manca di APM, log aggregation centralizzata, distributed tracing e dashboard operative.

---

## 2. Observability Architecture

```
                    ┌─────────────────────────────────┐
                    │         Sentry (SaaS)            │
                    │  PHP SDK + React @sentry/react   │
                    │  Errors, Traces, Replays, Logs   │
                    └────────┬───────────┬─────────────┘
                             │           │
              captureException      JS ErrorBoundary
                             │           │
   ┌─────────────────────────┴───────────┴──────────────────┐
   │                   Application Layer                     │
   │                                                         │
   │  QueryTimingMiddleware ──→ slow-requests.log            │
   │       │ (Server-Timing header su TUTTE le risposte)     │
   │       ▼                                                 │
   │  RequestLoggerMiddleware ──→ requests.log (JSON-lines)  │
   │       │ (14 campi sensitive redatti, body > 2KB troncato)│
   │       ▼                                                 │
   │  RateLimitMiddleware ──→ Telegram alert (breach)        │
   │       │                                                 │
   │  ErrorMiddleware ──→ Sentry + Telegram (5xx)            │
   └─────────────────────────────────────────────────────────┘
                             │
              ┌──────────────┴──────────────┐
              │       Monolog (app.log)      │
              │  JSON format, 14-day rotate  │
              │  UidProcessor per-request    │
              └─────────────────────────────┘
                             │
              ┌──────────────┴──────────────┐
              │   TelegramService (alerts)   │
              │  default chat + admin chat   │
              │  + interactive bot commands  │
              └──────────────┬──────────────┘
                             │
              ┌──────────────┴──────────────┐
              │  system_health_snapshots     │
              │  cron → AdminSystemService   │
              │  CPU/mem/disk/DB/sessions    │
              └─────────────────────────────┘

              ┌─────────────────────────────┐
              │  GET /health                 │
              │  8 checks: DB, storage,      │
              │  Stripe, AI, Sentry, OCR,    │
              │  Telegram, Backup B2         │
              └─────────────────────────────┘
```

---

## 3. Error Tracking — Sentry (8.5/10)

### 3.1 PHP Backend (auth/public/index.php)

```php
\Sentry\init([
  'dsn'                  => $_ENV['SENTRY_DSN'],
  'environment'          => $_ENV['SENTRY_ENVIRONMENT'] ?? 'production',
  'release'              => file_get_contents('VERSION'),
  'traces_sample_rate'   => 0.2,      // 20% transactions
  'profiles_sample_rate' => 1.0,      // 100% profiling
  'send_default_pii'     => true,
  'enable_logs'          => true,
  'server_name'          => 'kacinka-auth',
]);
```

| Feature | Status | Note |
|---------|--------|------|
| Error capture | ✅ | All unhandled exceptions |
| Custom tags | ✅ | `service`, `php_version`, `server`, `project_id` |
| User context | ✅ | `uid`, IP (set in error handler) |
| Request context | ✅ | method, URI, query, user-agent |
| Transaction tracing | ✅ | 20% sample rate |
| Profiling | ✅ | 100% of sampled transactions |
| Health path filtering | ✅ | `before_send` drops `/health` events |
| Sentry Logs | ✅ | `enable_logs: true` |

### 3.2 React Frontend (cloud.kacinka.app)

```javascript
Sentry.init({
  integrations: [
    Sentry.browserTracingIntegration(),
    Sentry.replayIntegration(),
    Sentry.reactRouterV7BrowserTracingIntegration(),
  ],
  tracesSampleRate: 0.2,
  replaysSessionSampleRate: 0.1,   // 10% normal sessions
  replaysOnErrorSampleRate: 1.0,   // 100% error sessions
  ignoreErrors: ['ResizeObserver loop', /Loading chunk \d+ failed/],
});
```

| Feature | Status | Note |
|---------|--------|------|
| Error capture | ✅ | `<Sentry.ErrorBoundary>` wraps app |
| Session replay | ✅ | 10% normal, 100% on error |
| Performance tracing | ✅ | Browser tracing + React Router v7 |
| Trace propagation | ✅ | `localhost`, `auth.kacinka.it`, `api.kacinka.it` |
| Error dialog | ✅ | Italian error page with retry button |
| Noise filtering | ✅ | 4 regex patterns for common benign errors |

### 3.3 Admin Panel (master.kacinka.app)

Same Sentry project, release tag `master.kacinka.app@${BUILD_TS}`. Simpler integrations (no router tracing).

---

## 4. Alerting — TelegramService (7.5/10)

### 4.1 Service Overview

**508 LOC** — Full-featured Telegram bot with alerts and interactive commands.

| Canale | Env Var | Scopo |
|--------|---------|-------|
| Default | `TELEGRAM_CHAT_ID` | Business notifications |
| Admin | `TELEGRAM_ADMIN_CHAT_ID` | Critical system alerts |

### 4.2 Automated Alerts

| Alert Method | Trigger | Severity | Dual-Send |
|-------------|---------|----------|-----------|
| `sendErrorAlert($msg, $file, $line)` | 5xx exception | 🔴 Critical | ✅ |
| `sendHealthAlert($status, $checks)` | Health degraded/unhealthy | 🔴/🟡 | ✅ (critical) |
| `sendBackupAlert($ok, $details)` | Cron backup result | ✅/🔴 | ✅ (failure) |
| `notifyRateLimitExceeded($ip, $path)` | Rate limit breach | 🟡 Warning | ❌ |
| `sendKpiAlert(...)` | KPI threshold exceeded | 🟡 Warning | ❌ |
| `notifyNewUser($email, $name)` | Registration | ℹ️ Info | ❌ |
| `notifyPurchase($email, $plan, $amount)` | Stripe purchase | ✅ Success | ❌ |
| `notifyPaymentFailed($email, $plan)` | Stripe failure | 🟡 Warning | ❌ |

### 4.3 Interactive Bot Commands

| Command | Accesso | Funzione |
|---------|---------|---------|
| `/status` | All | Uptime, online users (5min), last error |
| `/stats` | All | New users today, total users, invoices today, MRR |
| `/errors` | All | Last 5 error_log entries |
| `/backup` | All | Last backup status from backup_log |
| `/users` | All | User count breakdown by plan |
| `/maintenance on\|off` | Admin | Toggle maintenance mode |
| `/ban <email>` | Admin | Ban user |
| `/plan <email> <slug>` | Admin | Change user plan |
| `/help` | All | List commands |

---

## 5. Request Logging — RequestLoggerMiddleware (7.0/10)

**207 LOC** — JSON-lines structured logging.

### 5.1 Logged Fields Per Request

```json
{
  "ts": "2026-03-07T12:00:00",
  "rid": "a1b2c3d4e5f6",
  "uid": 42,
  "input": {
    "method": "POST", "path": "/invoices",
    "query": {"project_id": "5"},
    "body": {"amount": 100, "password": "***REDACTED***"},
    "ip": "203.0.113.1", "user_agent": "...", "content_type": "application/json"
  },
  "action": "CreateInvoiceAction",
  "output": {
    "status": 201,
    "body": {"id": 123},
    "ms": 45.2
  }
}
```

### 5.2 Security Features

| Feature | Dettaglio |
|---------|----------|
| **Sensitive field redaction** | 14 field names: `password`, `token`, `secret`, `api_key`, `credit_card`, `cvv`, etc. |
| **Body truncation** | Response > 2KB → `"[5432 bytes]"` (unless error status) |
| **String truncation** | Individual values capped at 500 chars |
| **IP resolution** | `X-Forwarded-For`, `X-Real-Ip`, `CF-Connecting-IP` |
| **Request correlation** | `X-Request-Id` response header added |
| **Path exclusion** | `/health` and `OPTIONS` requests skipped |

### 5.3 Limitazioni

- ❌ Output su file locale (non aggregato centralmente)
- ❌ Nessun dashboarding (niente Kibana, Grafana, etc.)
- ❌ Nessuna ricerca full-text sui log
- ❌ Nessun log retention policy configurato (file grows)

---

## 6. Performance Tracking — QueryTimingMiddleware (5.0/10)

**82 LOC** — Total request latency measurement.

| Caratteristica | Dettaglio |
|--------------|----------|
| **Posizione** | Outermost middleware (misura tutto) |
| **Precision** | `hrtime(true)` (nanosecond resolution) |
| **Header** | `Server-Timing: total;dur=45.2;desc="Total"` su ogni risposta |
| **Slow threshold** | 200ms |
| **Slow log** | `storage/logs/slow-requests.log` (plain text) |
| **Format** | `[2026-03-07 12:00:00] POST /ai/suggestions → 200 \| 342ms \| uid=42` |
| **Non-blocking** | `@file_put_contents` con `LOCK_EX` |

### 6.1 Limitazioni

- ❌ Nessun breakdown per middleware/service/DB
- ❌ Nessun percentile aggregation (p50, p95, p99)
- ❌ Nessun alerting su latency spikes
- ❌ Non traccia query individuali (solo total request time)

---

## 7. Infrastructure Monitoring (4.5/10)

### 7.1 system_health_snapshots Table

| Metrica | Campo |
|---------|-------|
| CPU | `cpu` |
| Memory | `memory` |
| DB connections | `db_connections` |
| DB size | `db_size_bytes` |
| Disk total | `disk_total_bytes` |
| Disk free | `disk_free_bytes` |
| PHP memory | `php_memory_usage`, `php_memory_peak` |
| Active sessions | `active_sessions` |
| API latency | `avg_api_latency_ms` |
| Errors (1h) | `error_count_1h` |
| Requests (1h) | `request_count_1h` |

**Recording**: `AdminSystemService::takeHealthSnapshot()` (cron-triggered)
**Reading**: `getPerformanceMetrics()` → latest + 24h trend → admin dashboard charts

### 7.2 Health Check Endpoint

**`GET /health`** — 8 service checks:

| Check | Metodo | OK | Degraded |
|-------|--------|-----|---------|
| `db` | `SELECT 1` + latency | < 200ms | slow / fail |
| `storage` | Write temp file | writable | fail |
| `stripe` | Secret key present | configured | not_configured |
| `ai` | Gemini key (+ OpenAI fallback) | ok | fallback_only |
| `sentry` | DSN env var | ok | not_configured |
| `ocr` | OCR API key | ok | not_configured |
| `telegram` | Bot token + chat ID | ok | not_configured |
| `backup_b2` | B2 key ID + app key | ok | not_configured |

**Response**: `healthy | degraded | unhealthy` — Returns HTTP 503 if unhealthy.

### 7.3 Uptime Monitor Cron (cron_uptime_monitor.php)

- Frequency: every 1 minute
- Monitors: `api.kacinka.app/health`, `cloud.kacinka.app`, `kacinka.app`, `docs.kacinka.app`
- Alerts: Telegram on downtime
- Storage: Results in DB for trend tracking

### 7.4 Health Check Script (health_check.sh)

- 5 URL checks + SSL expiry + disk usage + log error count (24h)
- Cron mode: `*/15 * * * *` — silent unless failures
- Used for additional monitoring outside PHP

---

## 8. Logging Architecture (3.5/10)

### 8.1 Monolog Configuration

| Parametro | Valore |
|-----------|--------|
| **Logger name** | `kacinka` |
| **Handler** | `RotatingFileHandler` |
| **Path** | `storage/logs/app.log` → `app-YYYY-MM-DD.log` |
| **Rotation** | 14 giorni |
| **Format** | JSON (`JsonFormatter`) |
| **Level** | from `LOG_LEVEL` env (default: `warning`) |
| **Processors** | `UidProcessor` (unique ID per request) |

### 8.2 Log File Inventory

| File | Source | Format | Rotation |
|------|--------|--------|----------|
| `app-YYYY-MM-DD.log` | Monolog (all app logging) | JSON structured | 14 days |
| `requests.log` | RequestLoggerMiddleware | JSON-lines | ❌ None |
| `slow-requests.log` | QueryTimingMiddleware | Plain text | ❌ None |
| `cron.log` | Cron jobs (stdout redirect) | Plain text | ❌ None |
| `php_errors.log` | PHP native error log | Plain text | ❌ None |

### 8.3 Limitazioni

- ❌ **No centralized aggregation** — Log su disco locale, accessibili solo via SSH
- ❌ **No log search** — grep manuale unico strumento
- ❌ **No log retention per tutti i file** — Solo app.log ha rotation (14d)
- ❌ **No log levels per channel** — Un singolo logger, nessun channel separation
- ❌ **No correlation tracing** — Request ID (`rid`) non propagato a tutti i log
- ✅ **JSON structured** — Pronto per ingestione in ELK/Loki senza parsing custom

---

## 9. Admin Dashboard Monitoring

**AdminSystemService** fornisce:

| Panel | Data Source | Visualizzazione |
|-------|-----------|----------------|
| Performance trends | `system_health_snapshots` (24h) | Line charts (latency, errors, requests) |
| System status | Latest health snapshot | CPU, memory, disk, DB gauge |
| Recent errors | `error_log` table | Error list with stack traces |
| Active sessions | `sessions` table | Count + trend |
| Recent deploys | `.deploy_info` file | Last deploy hash + timestamp |

### EnvStatusAction (`GET /admin/env-status`)

Validates all required environment variables against regex patterns without exposing values. 25+ variables checked with severity levels (critical, important, optional).

---

## 10. Gap Analysis

### 10.1 What's Present vs What's Missing

| Capability | Status | Copertura |
|-----------|--------|-----------|
| Error tracking (crashes) | ✅ Sentry | PHP + React + Admin |
| Error alerting | ✅ Sentry + Telegram | ≤ 30s latency |
| Session replay (frontend) | ✅ Sentry Replay | 10% normal, 100% error |
| Transaction tracing | ✅ Sentry Traces | 20% sample rate |
| PHP profiling | ✅ Sentry Profiling | 100% of traced requests |
| Request audit trail | ✅ RequestLogger | JSON-lines, local file |
| Slow request detection | ⚠️ QueryTiming | > 200ms logged, no alerting |
| Health checks | ✅ /health endpoint | 8 service checks |
| Uptime monitoring | ✅ cron_uptime_monitor | 1-min checks, Telegram alerts |
| System resource tracking | ⚠️ system_health_snapshots | Basic, admin-only |
| **APM (per-endpoint latency, p95/p99)** | ❌ Missing | — |
| **Real User Monitoring (frontend perf)** | ❌ Missing | Sentry traces partial |
| **Distributed tracing** | ❌ Missing | No OpenTelemetry |
| **Log aggregation** | ❌ Missing | Files on local disk |
| **Log search / querying** | ❌ Missing | grep only |
| **Custom metrics & dashboards** | ❌ Missing | No Grafana/DataDog |
| **Anomaly detection** | ❌ Missing | No ML alerting |
| **SLO/SLI tracking** | ❌ Missing | No error budget |
| **Synthetic monitoring** | ⚠️ health_check.sh | Bash script, not SaaS |
| **Cost monitoring** | ❌ Missing | No FinOps |

### 10.2 Severity Assessment

```
✅ Error Tracking      ████████████████████░░░░  85%  → Excellent
✅ Alerting            ███████████████░░░░░░░░░  75%  → Good
⚠️ Request Logging    ██████████████░░░░░░░░░░  70%  → Good (missing aggregation)
⚠️ Perf Tracking      ██████████░░░░░░░░░░░░░░  50%  → Basic
⚠️ Infra Monitoring   █████████░░░░░░░░░░░░░░░  45%  → Basic
❌ Log Aggregation     ███████░░░░░░░░░░░░░░░░░  35%  → Minimal
❌ Dashboards          ██████░░░░░░░░░░░░░░░░░░  30%  → Admin panel only
❌ Distributed Tracing ████░░░░░░░░░░░░░░░░░░░░  20%  → None
```

---

## 11. Observability Roadmap

### Phase 0 — Quick Wins (Settimana 1-2) — Score: 5.1 → 6.5

| # | Azione | Effort | Impact |
|---|--------|--------|--------|
| 1 | UptimeRobot free plan (external synthetic monitoring) | 1h | External validation |
| 2 | Configure `requests.log` rotation (logrotate/cron) | 1h | Disk space safety |
| 3 | Add latency alerting to QueryTimingMiddleware (Telegram if avg > 500ms) | 2h | Perf alerting |
| 4 | Betteruptime.com status page (free) | 1h | Customer-facing uptime |
| 5 | Sentry Alerts: configure rate-based alert (>10 errors/5min) | 30min | Proactive |

### Phase 1 — Foundation (Mesi 1-2) — Score: 6.5 → 7.5

| # | Azione | Effort | Impact |
|---|--------|--------|--------|
| 6 | Grafana Cloud free tier (10K metrics, 50GB logs) | 4h | Centralized dashboards |
| 7 | Log shipping: requests.log + app.log → Grafana Loki | 4h | Centralized log search |
| 8 | Custom PHP metrics → Prometheus (request count, latency histogram) | 8h | APM foundation |
| 9 | Frontend: Web Vitals reporting → analytics | 2h | RUM |
| 10 | SLI definition: availability (99.5%), latency p95 < 500ms | 2h | SLO tracking |

### Phase 2 — Advanced (Mesi 3-6) — Score: 7.5 → 8.5

| # | Azione | Effort | Impact |
|---|--------|--------|--------|
| 11 | OpenTelemetry PHP + JS integration | 16h | Distributed tracing |
| 12 | Grafana/DataDog dashboards per service | 8h | Per-endpoint monitoring |
| 13 | Anomaly detection alerts (error rate, latency spikes) | 4h | Proactive |
| 14 | PagerDuty/OpsGenie on-call rotation | 4h | Incident management |
| 15 | Error budget tracking + burn rate alerts | 4h | SRE practices |

### Phase 3 — Enterprise (Mesi 6-12) — Score: 8.5 → 9.0

| # | Azione | Effort | Impact |
|---|--------|--------|--------|
| 16 | Full DataDog APM / New Relic (§enterprise budget) | 16h | Complete observability |
| 17 | Custom business metrics (MRR tracking, churn forecasting) | 8h | Business observability |
| 18 | Chaos engineering (failure injection testing) | 16h | Resilience validation |
| 19 | AI-powered alert correlation (reduce noise) | 8h | Alert fatigue reduction |

---

## 12. Recommended Tool Stack

### Cost-Optimized (Budget < €50/mo)

| Layer | Tool | Costo |
|-------|------|-------|
| Error Tracking | Sentry (existing) | Free tier → Team $26/mo |
| Uptime | UptimeRobot | Free (50 monitors) |
| Status Page | Betteruptime | Free |
| Logs | Grafana Cloud free tier | Free (50GB/mo) |
| Metrics | Grafana Cloud free tier | Free (10K metrics) |
| Alerting | Telegram (existing) + Sentry | Free |
| **Total** | | **€0-26/mo** |

### Growth (Budget < €200/mo)

| Layer | Tool | Costo |
|-------|------|-------|
| Error Tracking | Sentry Team | $26/mo |
| APM | Sentry Performance | Included |
| Uptime | UptimeRobot Pro | $7/mo |
| Status Page | Betteruptime paid | $20/mo |
| Logs + Metrics | Grafana Cloud Pro | $50/mo |
| Alerting | PagerDuty Starter | $21/mo |
| **Total** | | **~€130/mo** |

---

*Documento generato il 2026-03-07. Assessment basato su: Sentry init config (PHP + 2x React), TelegramService (508 LOC), RequestLoggerMiddleware (207 LOC), QueryTimingMiddleware (82 LOC), HealthAction.php, AdminSystemService, 16 cron jobs, Monolog configuration.*
