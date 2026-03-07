# Performance Audit

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Executive Summary

| Area | Score | Livello |
|------|-------|---------|
| **Backend Response Time** | 7.0/10 | ⚠️ Buono (margine miglioramento) |
| **Frontend Bundle Size** | 7.5/10 | ✅ Buono |
| **Database Query Efficiency** | 6.5/10 | ⚠️ Sufficiente |
| **Caching Strategy** | 5.0/10 | ⚠️ Da migliorare |
| **CDN & Static Assets** | 6.0/10 | ⚠️ Da migliorare |
| **Infrastructure** | 4.5/10 | 🔴 Critico |
| **OVERALL** | **6.1/10** | **⚠️ Sufficiente** |

---

## 2. Frontend Performance

### 2.1 Build Pipeline

| Metrica | Valore | Valutazione |
|---------|--------|-------------|
| **Bundler** | Vite 7.2.4 | ✅ Fastest modern bundler |
| **Framework** | React 19.2 | ✅ Latest stable |
| **CSS** | Tailwind CSS 4.1 (JIT) | ✅ Minimal CSS output |
| **TypeScript** | 5.9.3 | ✅ Type-safe |
| **Tree Shaking** | Vite default (Rollup) | ✅ Dead code elimination |

### 2.2 Code Splitting Strategy

| Tecnica | Status | Note |
|---------|--------|------|
| Route-based splitting | ✅ Implementato | React Router lazy imports |
| Module-level splitting | ✅ Implementato | 26 modules caricati on-demand |
| i18n namespace loading | ✅ Implementato | i18next lazy loading per namespace |
| Vendor chunk splitting | ✅ Vite default | Dependencies in chunk separati |
| Component-level splitting | ⚠️ Parziale | Grandi componenti non sempre lazy |

### 2.3 Bundle Size Analysis (Estimated)

| Chunk | Size (gzip) | Note |
|-------|-------------|------|
| **Vendor (React core)** | ~50 KB | React 19 + ReactDOM |
| **Vendor (libraries)** | ~120 KB | Zustand, i18next, date-fns, zod, framer-motion |
| **App shell** | ~30 KB | Router, layout, auth |
| **Per-module avg** | ~15-25 KB | 26 modules × ~20 KB avg |
| **Recharts** | ~40 KB | Solo per analytics/reports |
| **TipTap editor** | ~60 KB | Solo per email/notes editor |
| **FullCalendar** | ~35 KB | Solo per calendar module |
| **Initial Load** | ~200 KB | Shell + frequenti vendor |
| **Full App** | ~600-800 KB | Tutto caricato |

### 2.4 State Management Performance

| Aspetto | Dettaglio | Valutazione |
|---------|----------|-------------|
| **Library** | Zustand 5.0 (33 stores) | ✅ Lightweight (~2 KB) |
| **Re-render Pattern** | Selector-based subscriptions | ✅ Minimal re-renders |
| **Store Size** | 7,173 LOC totali | ⚠️ Alcuni store molto grandi |
| **Persistence** | Nessuna (fresh fetch on mount) | ✅ Simple, no stale data |

### 2.5 Animation Performance

| Library | Use Case | Valutazione |
|---------|---------|-------------|
| **Framer Motion** | Page transitions, modals | ⚠️ Aggiunge ~20 KB, usare CSS per animazioni semplici |
| **CSS Transitions** | Hover effects, toggles | ✅ GPU-accelerated |
| **will-change** | Non utilizzato esplicitamente | ⚠️ Potrebbe migliorare alcune transizioni |

---

## 3. Backend Performance

### 3.1 PHP Runtime

| Metrica | Valore | Valutazione |
|---------|--------|-------------|
| **PHP Version** | 8.1+ | ⚠️ Aggiornare a 8.3 (JIT + fibers) |
| **Framework** | Slim 4 | ✅ Minimal overhead |
| **Dependency Injection** | php-di 7.1 | ✅ Compiled container |
| **Error Tracking** | Sentry 4.20 | ✅ Real-time monitoring |

### 3.2 Middleware Pipeline Impact

| Middleware | LOC | Estimated Overhead | Note |
|-----------|-----|-------------------|------|
| CorsMiddleware | 110 | ~0.1 ms | Header-only |
| SecurityHeadersMiddleware | 92 | ~0.1 ms | Header-only |
| RateLimitMiddleware | 245 | ~1-5 ms | DB/APCu lookup |
| CsrfMiddleware | 158 | ~0.2 ms | Cookie comparison |
| AuthMiddleware | 100 | ~1-2 ms | JWT decode |
| PermissionMiddleware | 94 | ~0.5-1 ms | RBAC check |
| PlanLimitsMiddleware | 708 | ~2-5 ms | Multi-query check |
| QueryTimingMiddleware | 70 | ~0.1 ms | Timer only |
| **Total Pipeline** | **3,370** | **~5-15 ms** | Per request |

### 3.3 Query Timing (QueryTimingMiddleware)

| Aspetto | Dettaglio |
|---------|----------|
| **Measurement** | `X-Query-Time` header on every response |
| **Slow Query Detection** | ⚠️ Non implementato a livello middleware |
| **Query Logging** | Via RequestLoggerMiddleware (214 LOC) |
| **Profiling** | ❌ Nessun APM tool integrato |

### 3.4 Caching Architecture

| Layer | Implementazione | Valutazione |
|-------|----------------|-------------|
| **APCu** | Rate limiting, plan multipliers (60s TTL) | ✅ In-memory, fast |
| **HTTP Cache** | HttpCacheMiddleware (175 LOC) — ETag/Last-Modified | ✅ Conditional GET |
| **Query Cache** | ❌ Nessun query result caching | 🔴 Critico per performance |
| **Redis/Memcached** | ❌ Non disponibile (shared hosting) | 🔴 Necessario per scale |
| **CDN** | ❌ No CDN per static assets | 🔴 Latenza globale |
| **OPcache** | PHP OPcache (Hostinger default) | ✅ Bytecode caching |

---

## 4. Database Performance

### 4.1 Schema Metrics

| Metrica | Valore | Valutazione |
|---------|--------|-------------|
| **Total Tables** | 176 | Adeguato al dominio |
| **Indexed Queries** | Composite indices su main lists (Migration 009) | ✅ Key queries covered |
| **Foreign Keys** | Presenti su ~70% relazioni | ⚠️ Alcune mancanti |
| **JSON Columns** | ~15 tabelle con JSON | ⚠️ Non indicizzabili nativamente |
| **FULLTEXT** | Solo `support_knowledge_base` | ⚠️ Limitato |

### 4.2 Query Patterns

| Pattern | Status | Note |
|---------|--------|------|
| **Prepared Statements** | ✅ 100% | PDO parameterized |
| **N+1 Query Problem** | ⚠️ Possibile | No ORM, eager loading manuale |
| **Pagination** | ✅ LIMIT/OFFSET standard | Ma OFFSET lento su tabelle grandi |
| **Soft Delete Filtering** | ⚠️ | `WHERE deleted_at IS NULL` su ogni query |
| **Aggregation Queries** | ⚠️ | Dashboard queries potenzialmente lente |

### 4.3 High-Volume Tables (Growth Projections)

| Tabella | Growth Rate | Y1 (1,500 users) | Y3 (5,000 users) | Action |
|---------|-------------|-------------------|-------------------|--------|
| ai_interactions | ~50 rows/user/mo | ~900K rows | ~9M rows | ⚠️ Partition/archive |
| audit_log | ~100 rows/user/mo | ~1.8M rows | ~18M rows | ⚠️ Partition |
| bank_transactions | ~200 rows/user/mo | ~3.6M rows | ~36M rows | 🔴 Partition required |
| chat_messages | ~30 rows/user/mo | ~540K rows | ~5.4M rows | Monitor |
| invoices | ~10 rows/user/mo | ~180K rows | ~1.8M rows | OK |
| webhook_deliveries | ~50 rows/webhook/mo | Variable | Variable | ⚠️ Retention policy |

### 4.4 Index Health

| Aspetto | Status |
|---------|--------|
| Performance composite indices (Migration 009) | ✅ 9 key indices |
| Primary keys on all tables | ✅ |
| Foreign key indices (InnoDB auto) | ✅ |
| Missing indices on JSON queries | ⚠️ |
| EXPLAIN/ANALYZE usage | ❌ Non automatizzato |

---

## 5. Infrastructure Performance

### 5.1 Current Setup (Hostinger Shared)

| Componente | Spec | Limitazione |
|-----------|------|------------|
| **Web Server** | LiteSpeed (shared) | No config control |
| **PHP** | 8.1 (shared pool) | No dedicated workers |
| **Database** | MariaDB (shared) | No connection pooling |
| **Storage** | SSD (shared) | I/O competition |
| **Memory** | ~512 MB PHP limit | Insufficient per AI calls pesanti |
| **CPU** | Shared cores | Throttling under load |
| **SSL** | Let's Encrypt (auto) | ✅ |
| **Bandwidth** | ~100 GB/mo | Sufficiente per early stage |

### 5.2 Performance Bottlenecks

| Bottleneck | Impatto | Soluzione |
|-----------|---------|----------|
| **No Redis** | Cache in-memory impossibile oltre APCu | Migrare a VPS con Redis |
| **No connection pooling** | Ogni request = nuova connessione DB | PgBouncer equivalent per MariaDB |
| **No worker processes** | Email, webhooks, OCR sincroni | Background job queue (già tabella `email_queue`) |
| **Shared CPU** | Spike di latenza sotto carico | VPS/Cloud con CPU dedicata |
| **No CDN** | Latency per utenti non-IT | Cloudflare CDN |
| **No APM** | Nessuna visibilità su bottleneck reali | Datadog / New Relic |

### 5.3 Target Architecture

```
[User] → [Cloudflare CDN] → [Load Balancer]
              ↓                    ↓
         [Static Assets]    [PHP-FPM Workers ×4]
                                   ↓
                            [Redis Cache]
                                   ↓
                        [MariaDB Primary + Replica]
                                   ↓
                         [Background Worker Queue]
```

---

## 6. API Performance

### 6.1 Estimated Response Times

| Endpoint Type | Target | Current Estimated | Status |
|--------------|--------|-------------------|--------|
| Simple CRUD (GET list) | <100 ms | ~50-150 ms | ⚠️ |
| Complex queries (reports, analytics) | <500 ms | ~200-800 ms | ⚠️ |
| AI Chat | <3s | ~1-5s (Gemini API) | ⚠️ External dependency |
| OCR Processing | <10s | ~3-15s (Gemini Vision) | ⚠️ External dependency |
| File Upload | <2s | ~1-5s (disk I/O) | ⚠️ |
| E-Invoice Generation | <2s | ~0.5-2s | ✅ |
| PDF Generation | <3s | ~1-3s (DomPDF) | ⚠️ |

### 6.2 Concurrency

| Metrica | Current | Target |
|---------|---------|--------|
| **Concurrent Users** | ~10-50 | 500+ |
| **Requests/sec** | ~10-30 | 200+ |
| **DB Connections** | ~10-20 (shared) | 100+ (pooled) |
| **WebSocket Connections** | Pusher (managed) | Pusher scales |

---

## 7. Monitoring & Observability

### 7.1 Current

| Tool | Coverage | Valutazione |
|------|---------|-------------|
| **Sentry** | Error tracking (PHP + React) | ✅ |
| **QueryTimingMiddleware** | X-Query-Time header | ⚠️ No aggregation |
| **RequestLoggerMiddleware** | Request logging (214 LOC) | ⚠️ No dashboards |
| **system_health_snapshots** | Periodic CPU/memory/DB stats | ⚠️ Basic |

### 7.2 Missing

| Tool | Necessità | Timeline |
|------|----------|----------|
| APM (Application Performance Monitoring) | 🔴 Critico | Pre-launch |
| Real User Monitoring (RUM) | 🟡 Media | M1-M3 |
| Synthetic Monitoring (uptime) | 🔴 Critico | Pre-launch |
| Database Query Analyzer | 🟡 Media | M1-M3 |
| Log Aggregation (ELK/Loki) | 🟡 Media | M3-M6 |

---

## 8. Performance Roadmap

### Phase 0 — Pre-Launch (Settimane 1-4)
| # | Azione | Impatto | Effort |
|---|--------|---------|--------|
| 1 | Aggiornare PHP a 8.3 | +10-20% PHP performance | 2h |
| 2 | Implementare uptime monitoring (UptimeRobot free) | Availability visibility | 1h |
| 3 | Aggiungere `EXPLAIN` logging per query > 100ms | Identify slow queries | 4h |
| 4 | Setup Cloudflare (free tier) per CDN + WAF | Latency + security | 4h |

### Phase 1 — Launch (Mesi 1-3)
| # | Azione | Impatto | Effort |
|---|--------|---------|--------|
| 5 | Migrare a VPS (Hetzner/Contabo) | Dedicated resources | 8h |
| 6 | Installare Redis per caching | -50% DB load | 4h |
| 7 | Background job worker per email/webhooks | Non-blocking async | 16h |
| 8 | Database query optimization (N+1 fix) | -30% query count | 16h |

### Phase 2 — Scale (Mesi 3-12)
| # | Azione | Impatto | Effort |
|---|--------|---------|--------|
| 9 | Read replica per reporting queries | Separate read/write | 8h |
| 10 | Table partitioning (ai_interactions, audit_log) | Query performance | 8h |
| 11 | APM integration (Datadog / New Relic) | Full observability | 8h |
| 12 | Cursor-based pagination (replace OFFSET) | Scale pagination | 16h |

---

## 9. Benchmark Targets

| Metrica | Pre-Launch | M3 | M12 |
|---------|-----------|-----|------|
| **P50 Response** | <200 ms | <100 ms | <50 ms |
| **P99 Response** | <2s | <500 ms | <200 ms |
| **Uptime** | 99% | 99.5% | 99.9% |
| **Error Rate** | <5% | <1% | <0.1% |
| **Time to First Byte** | <500 ms | <200 ms | <100 ms |
| **Lighthouse Score** | >70 | >80 | >90 |

---

*Documento generato il 2026-03-07. Stime basate su analisi statica del codice, architettura middleware, e caratteristiche dell'infrastruttura attuale (Hostinger shared hosting).*
