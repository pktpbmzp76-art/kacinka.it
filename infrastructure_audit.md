# Infrastructure Audit

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Current Infrastructure

### 1.1 Hosting

| Componente | Valore |
|-----------|--------|
| **Provider** | Hostinger (Business Web Hosting) |
| **Server IP** | 82.198.229.237 |
| **SSH Port** | 65002 |
| **Web Server** | LiteSpeed (managed) |
| **PHP** | 8.1+ (shared pool) |
| **Database** | MariaDB 10.x (shared instance) |
| **OS** | CloudLinux (Hostinger standard) |
| **SSL** | Let's Encrypt (auto-renewal) |
| **Backup** | Weekly automated (Hostinger included) |
| **Uptime SLA** | 99.9% (hosting-level, non application-level) |

### 1.2 Domains & Subdomains

| Dominio | Ruolo | Hosting |
|---------|-------|---------|
| **kacinka.app** | Landing page / marketing site | Hostinger |
| **cloud.kacinka.app** | SaaS application (React SPA) | Hostinger |
| **api.kacinka.app** | Backend API (PHP Slim 4) | Hostinger |
| **master.kacinka.app** | Admin panel | Hostinger |
| **kacinka.it** | Marketing site (Italian) | Hostinger |
| **status.kacinka.app** | Status page (planned) | TBD |

### 1.3 DNS Configuration

| Record | Tipo | Valore |
|--------|------|--------|
| kacinka.app | A | 82.198.229.237 |
| cloud.kacinka.app | CNAME | kacinka.app |
| api.kacinka.app | CNAME | kacinka.app |
| master.kacinka.app | CNAME | kacinka.app |
| Mail | MX | Hostinger mail servers |

---

## 2. Deploy Architecture

### 2.1 Current Deploy Process

```
Developer Machine (localhost)
    │
    ├── Frontend Build
    │   └── npm run build → dist/
    │
    ├── Backend Push
    │   └── SSH/SFTP → Hostinger docroot
    │
    └── Database Migrations
        └── SSH → php run_XXX.php
```

### 2.2 Deploy Scripts

| Script | Location | Function |
|--------|----------|----------|
| `deploy_scripts/` | Root directory | Deploy utilities |
| Migration runners | `auth/migrations/run_*.php` | Database schema updates |
| Build scripts | `_build/` | ~70+ build/audit/inject scripts |

### 2.3 Deploy Maturity Assessment

| Aspetto | Status | Valutazione |
|---------|--------|-------------|
| **Version Control** | Git (presumed, .gitignore present) | ✅ |
| **CI/CD Pipeline** | ❌ Assente | 🔴 Critico |
| **Automated Testing on Push** | ❌ Assente | 🔴 Critico |
| **Staging Environment** | ❌ Assente | 🔴 Critico |
| **Blue-Green Deploy** | ❌ N/A (shared hosting) | 🔴 |
| **Rollback Procedure** | ⚠️ Manuale (SQL rollback files exist) | ⚠️ |
| **Database Migrations** | ✅ PHP scripts (90+ files) | ✅ Idempotent |
| **Environment Variables** | `.env` file on server | ✅ |
| **Secret Management** | `.env` file (no vault) | ⚠️ |

---

## 3. Build System

### 3.1 Frontend Build (Vite)

| Aspetto | Dettaglio |
|---------|----------|
| **Tool** | Vite 7.2.4 |
| **Input** | `app/src/` (React + TypeScript) |
| **Output** | `app/dist/` (static files) |
| **Dev Server** | `npm run dev` (HMR) |
| **Prod Build** | `npm run build` |
| **Task Configs** | VS Code tasks for Vite Dev Server + Admin Dev Server |
| **Tailwind** | JIT compilation via PostCSS |

### 3.2 Build Scripts (`_build/` directory)

| Category | Scripts | Purpose |
|----------|---------|---------|
| **Audit/Quality** | audit_categories.php, audit_hreflang.js, audit_seo.js, audit_spokes.php | Code/content quality |
| **Search/Index** | build_search_index.js | Search index generation |
| **Documentation** | generate_api_docs.js, generate_changelog.js, generate_jsdoc.js, generate_phpdoc.js | Auto-docs |
| **i18n** | check_missing_i18n_keys.js, inject_*_i18n.js (40+ files) | Translation management |
| **Coverage** | check_jsdoc_coverage.js, check_phpdoc_coverage.php | Doc coverage |
| **Utility** | cleanup_orphans.php, fix_country_names.js, fix_encoding_i18n.js | Maintenance |
| **SEO** | generate_batch_spokes.php, generate_spoke_translations.php | Hub & spoke SEO |
| **Security** | compute_csp_hashes.ps1 | CSP hash generation |

---

## 4. External Services

### 4.1 Service Map

| Servizio | Use Case | Endpoint | Criticità |
|----------|---------|----------|-----------|
| **Stripe** | Payments, subscriptions | api.stripe.com | 🔴 Critico |
| **Google Gemini** | AI chat, OCR, support | generativelanguage.googleapis.com | 🟡 Media |
| **OpenAI** | AI fallback | api.openai.com | 🟢 Bassa (fallback) |
| **Tink (Visa)** | Open banking | api.tink.com | 🟡 Media |
| **Pusher** | WebSocket (realtime chat) | *.pusher.com | 🟡 Media |
| **Sentry** | Error tracking | sentry.io | 🟡 Media |
| **Telegram Bot** | Admin alerts | api.telegram.org | 🟢 Bassa |
| **Google APIs** | Calendar, Drive, Contacts | googleapis.com | 🟡 Media |
| **SDI (Agenzia Entrate)** | FatturaPA Italia | sdi*.fatturapa.gov.it | 🔴 Critico (per IT) |
| **Let's Encrypt** | SSL certificates | acme-v02.api.letsencrypt.org | 🟢 Automatico |

### 4.2 Dependency Chain

```
CRITICAL PATH:
  Stripe → subscriptions, payments, plan gating
  SDI → Italian e-invoice submission
  Tink → bank account sync, open banking

DEGRADED SERVICE (if unavailable):
  Gemini → fallback to GPT-4o-mini → graceful error
  Pusher → chat becomes polling-based
  Sentry → silent failure, no error tracking
```

---

## 5. File System Architecture

### 5.1 Server Docroot Structure

```
public_html/
├── app/           # React SPA (frontend)
│   ├── src/       # Source code
│   └── dist/      # Built assets (served by web server)
├── auth/          # PHP Backend API
│   ├── app/       # Slim 4 application
│   ├── src/       # Source code (Actions, Services, Middleware)
│   ├── tests/     # PHPUnit + Playwright tests
│   ├── migrations/# DB migrations (90+ files)
│   └── vendor/    # Composer dependencies
├── admin/         # Admin panel
├── _engine/       # Public website engine (204K LOC)
├── _build/        # Build & audit scripts (70+ files)
├── _migration/    # Additional DB migrations
├── assets/        # Static assets (images, fonts)
├── blog/          # Blog content
├── docs/          # Documentation
├── openclaw/      # Audit documentation (this project)
└── status/        # Status page
```

### 5.2 Storage

| Tipo | Location | Strategy |
|------|----------|----------|
| **File uploads** | Hostinger filesystem | Flysystem (local adapter) |
| **Database backups** | Hostinger automated | Weekly |
| **Log files** | PHP error_log + Sentry | Sentry cloud + local |
| **Static assets** | Hostinger docroot | Direct serve via LiteSpeed |
| **User sites** | Hostinger subfolders | Per-site directory |

---

## 6. Infrastructure Limitations

### 6.1 Shared Hosting Constraints

| Limitazione | Impatto | Workaround |
|------------|---------|-----------|
| No root access | Cannot install Redis, custom software | APCu for caching |
| No background workers | Async processing impossible | Sync processing, email_queue table |
| Shared CPU/RAM | Performance unpredictable under load | None (must migrate) |
| No custom web server config | LiteSpeed only, no nginx/Apache tuning | .htaccess limited |
| No container support | No Docker, no K8s | N/A |
| No log aggregation | Cannot install ELK/Loki | Sentry only |
| IP shared with other sites | Reputation risk, no dedicated IP | Cloudflare proxy |
| PHP version locked | Cannot upgrade independently | Wait for Hostinger update |

### 6.2 Scale Ceiling

| Metrica | Current Ceiling | Adequate Until |
|---------|----------------|----------------|
| Concurrent users | ~50-100 | ~100 paying users |
| Database connections | ~50 (shared pool) | ~200 paying users |
| Storage | Hostinger plan limit | ~500 paying users |
| Bandwidth | ~100 GB/mo | ~300 paying users |
| PHP workers | ~5-10 (shared) | ~100 paying users |

---

## 7. Target Architecture

### 7.1 Phase 1 — VPS (100-500 users)

```
[Cloudflare CDN + WAF]
         │
    [VPS (Hetzner CX31)]
    ├── nginx reverse proxy
    ├── PHP-FPM 8.3 (8 workers)
    ├── MariaDB (dedicated)
    ├── Redis 7.x (caching + sessions)
    └── Supervisor (background workers)

Cost: ~€20-35/mo
```

### 7.2 Phase 2 — Cloud (500-5,000 users)

```
[Cloudflare CDN + WAF]
         │
    [Load Balancer]
    ├── Web Server 1 (PHP-FPM)
    ├── Web Server 2 (PHP-FPM)
    │
    [MariaDB Primary]
    ├── Read Replica 1
    │
    [Redis Cluster]
    ├── Sessions
    ├── Cache
    ├── Queue (Bull/Sidekiq equivalent)
    │
    [Object Storage (S3-compatible)]
    └── File uploads, backups

Cost: ~€100-300/mo
```

### 7.3 Phase 3 — Kubernetes (5,000+ users)

```
[Cloudflare Enterprise]
         │
    [K8s Cluster (3+ nodes)]
    ├── PHP-FPM pods (auto-scaling)
    ├── Worker pods (queue processing)
    ├── Cron pods (scheduled tasks)
    │
    [Managed MariaDB (RDS/PlanetScale)]
    ├── Multi-AZ failover
    ├── Read replicas
    │
    [ElastiCache Redis Cluster]
    │
    [S3 + CloudFront]
    │
    [Monitoring Stack]
    ├── Datadog / Grafana Cloud
    ├── PagerDuty alerts
    └── ELK/Loki logs

Cost: ~€500-2,000/mo
```

---

## 8. Migration Plan

### 8.1 Hostinger → VPS

| Step | Azione | Duration | Risk |
|------|--------|----------|------|
| 1 | Provision VPS (Hetzner CX31) | 30 min | Low |
| 2 | Install stack (nginx, PHP-FPM, MariaDB, Redis) | 2h | Low |
| 3 | Clone codebase to VPS | 30 min | Low |
| 4 | Import database dump | 1h | Medium |
| 5 | Configure .env, SSL, domains | 2h | Medium |
| 6 | DNS cutover (Cloudflare proxy) | 5 min | Low |
| 7 | Verify all endpoints | 2h | Medium |
| 8 | Monitor for 48h | 48h | Low |
| **Total** | | **~8h active + 48h monitoring** | |

### 8.2 Pre-Migration Checklist

- [ ] Full database backup + verification
- [ ] Document all .env variables
- [ ] Test deploy on staging VPS first
- [ ] Set up Cloudflare proxy before DNS cut
- [ ] Prepare rollback DNS records
- [ ] Notify early users of maintenance window
- [ ] Verify Stripe webhook endpoints
- [ ] Update CORS origins
- [ ] Test all external service integrations

---

## 9. Infrastructure Cost Comparison

| Setup | Monthly Cost | Capacity | Cost/User @500 |
|-------|-------------|----------|----------------|
| **Hostinger (current)** | ~€10-20 | ~100 users | €0.15 |
| **Hetzner VPS CX31** | ~€20-35 | ~500 users | €0.05 |
| **Hetzner Dedicated** | ~€50-100 | ~2,000 users | €0.03 |
| **AWS/GCP Basic** | ~€100-300 | ~5,000 users | €0.04 |
| **AWS/GCP Production** | ~€500-2,000 | ~50,000 users | €0.02 |

---

## 10. Raccomandazioni

### Immediato (Pre-Launch)
1. **Setup Cloudflare** (free tier) — CDN + WAF + DDoS protection
2. **Automated backups** to external storage (S3 o Backblaze B2)
3. **Uptime monitoring** (UptimeRobot free)

### Mese 1-3 (Post-Launch)
4. **Migrare a Hetzner VPS** — dedicated resources, Redis, background workers
5. **CI/CD pipeline** (GitHub Actions) — automated testing + deploy
6. **Staging environment** — replica dell'ambiente produzione

### Mese 3-12 (Scale)
7. **Read replica** per query analytics/reporting
8. **Object storage** per file uploads (S3-compatible)
9. **Container orchestration** se crescita > 2,000 utenti

---

*Documento generato il 2026-03-07. Infrastruttura attuale basata su Hostinger shared hosting. Proiezioni costi da listini pubblici Hetzner/AWS/GCP (marzo 2026).*
