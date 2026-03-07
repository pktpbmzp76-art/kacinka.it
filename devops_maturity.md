# DevOps Maturity Assessment

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Executive Summary

| Dimensione | Livello | Score | Target (M6) |
|-----------|---------|-------|-------------|
| **Source Control** | Advanced | 8.0/10 | 9.0 |
| **CI/CD Pipeline** | Defined | 7.0/10 | 8.5 |
| **Testing** | Managed | 6.5/10 | 8.0 |
| **Deployment** | Defined | 6.5/10 | 8.5 |
| **Infrastructure** | Initial | 3.5/10 | 7.0 |
| **Monitoring** | Initial | 4.0/10 | 7.5 |
| **Security Ops** | Managed | 5.5/10 | 7.5 |
| **Documentation** | Managed | 6.0/10 | 7.5 |
| **OVERALL** | **Defined** | **5.9/10** | **7.9** |

> Modello di riferimento: DORA DevOps Capabilities + Accelerate Four Key Metrics framework.

---

## 2. DORA Metrics Assessment

### 2.1 Four Key Metrics

| Metrica | Attuale | Livello DORA | Target |
|---------|---------|-------------|--------|
| **Deployment Frequency** | Ad-hoc (~1-2x/week manual) | Low | On-demand (daily) |
| **Lead Time for Changes** | ~hours (commit → manual deploy) | Medium | < 1 hour (CI/CD) |
| **Mean Time to Recovery** | ~30 min (SSH hot fix) | Medium | < 15 min (rollback) |
| **Change Failure Rate** | Unknown (no tracking) | N/A | < 15% |

### 2.2 Current vs Industry Benchmarks

```
                    Kacinka      Elite SaaS     Median SaaS
Deploy Freq         1-2x/week    Multiple/day   1x/week
Lead Time           Hours        < 1 hour       1-7 days
MTTR                ~30 min      < 1 hour       < 1 day
Change Fail Rate    Unknown      < 5%           16-30%
```

---

## 3. Source Control

### 3.1 Assessment: 8.0/10

| Pratica | Status | Note |
|---------|--------|------|
| Git repository | ✅ | GitHub |
| Branch strategy | ✅ | `main` (prod) + `develop` (staging) |
| `.gitignore` completo | ✅ | Aggiornato con vendor, dist, .env, storage |
| Commit conventions | ⚠️ | Non formalmente enforced (no commitlint) |
| PR reviews | ⚠️ | Solo fondatore; no branch protection rules attive |
| Merge strategy | ⚠️ | Non definito (squash vs merge commit) |

### 3.2 Raccomandazioni

1. **Attivare branch protection** su `main`:
   - Require PR reviews (almeno 1 approvazione)
   - Require status checks to pass (CI/CD)
   - No direct push
2. **Adottare Conventional Commits** (`feat:`, `fix:`, `chore:`)
3. **Configurare commitlint** + husky pre-commit hook

---

## 4. CI/CD Pipeline

### 4.1 Assessment: 7.0/10

Pipeline GitHub Actions presente e ben strutturata (366 LOC), ma **non attiva** (secrets non configurati).

### 4.2 Pipeline Architecture

```
┌──────────────────────────────────────────────────────────┐
│                 GitHub Actions — deploy.yml               │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  push/PR to main/develop                                 │
│       │                                                  │
│       ▼                                                  │
│  ┌─────────┐                                             │
│  │  LINT   │  ESLint (app), PHP syntax, PHPStan L5       │
│  └────┬────┘                                             │
│       │                                                  │
│  ┌────┴─────────────────┐                                │
│  │                      │                                │
│  ▼                      ▼                                │
│  ┌──────────────┐  ┌───────────────┐                     │
│  │ TEST-FRONTEND│  │ TEST-BACKEND  │  Parallel           │
│  │ Vitest       │  │ PHPUnit +     │                     │
│  │ (app+admin)  │  │ MariaDB svc   │                     │
│  └──────┬───────┘  └──────┬────────┘                     │
│         │                 │                               │
│         └────────┬────────┘                               │
│                  ▼                                        │
│          ┌──────────────┐                                 │
│          │    BUILD     │  Vite production builds         │
│          │  + artifacts │  Upload app/admin dist           │
│          └──────┬───────┘                                 │
│                 │                                        │
│       ┌─────────┴─────────┐                              │
│       │                   │                              │
│       ▼                   ▼                              │
│  ┌──────────┐    ┌──────────────┐                         │
│  │ DEPLOY   │    │   DEPLOY     │                         │
│  │ PROD     │    │   STAGING    │                         │
│  │ (main)   │    │   (develop)  │                         │
│  │ + Health  │    │   + Health   │                         │
│  │ + Sentry  │    └──────────────┘                         │
│  └──────────┘                                             │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### 4.3 Pipeline Jobs Detail

| Job | Steps | Caching | Duration (est.) |
|-----|-------|---------|----------------|
| **lint** | Checkout → Node setup → npm ci (app, admin) → ESLint → PHP setup → Composer install → PHP syntax → PHPStan | npm cache | ~3 min |
| **test-frontend** | Checkout → Node → npm ci → Vitest (app) → Vitest (admin) | npm cache | ~4 min |
| **test-backend** | Checkout → PHP setup → Composer → PHPUnit (MariaDB 10.11 service) | Composer | ~5 min |
| **build** | Checkout → Node → npm ci → Vite build (app + admin) → Upload artifacts | npm cache | ~3 min |
| **deploy** | Checkout → Download artifacts → SSH setup → SCP backend → SCP frontend → SCP landing → Post-deploy (composer, perms) → Health check → Sentry release | — | ~5 min |
| **deploy-staging** | Same as deploy, target: staging subdirectory | — | ~3 min |

**Estimated total pipeline**: ~12-15 min (lint sequential; test parallel; build; deploy)

### 4.4 What's Missing in Pipeline

| Gap | Impatto | Effort |
|-----|---------|--------|
| Secrets configuration (SSH_PRIVATE_KEY, SSH_HOST, etc.) | 🔴 Pipeline non funzionale | 30 min |
| Database migration step | 🟡 Migrations manuali | 1h |
| Playwright E2E job | 🟡 No end-to-end validation | 4h |
| OWASP ZAP / security scan | 🟡 No automated security testing | 4h |
| Dependency audit (`npm audit`, `composer audit`) | 🟡 No vuln detection | 1h |
| Slack/Telegram notification | 🟢 No deploy alerts | 1h |
| Rollback automation | 🟢 Rollback è manuale (script esiste) | 2h |

---

## 5. Deployment

### 5.1 Assessment: 6.5/10

### 5.2 Deploy Scripts Inventory

| Script | LOC | Funzione |
|--------|-----|---------|
| `deploy.sh` | ~200 | Full production deploy via SSH/rsync (8 steps) |
| `build_and_deploy.sh` | ~80 | Build frontend + invoke deploy.sh |
| `rollback.sh` | ~100 | Symlink-based rollback (last 3 releases, 30s max) |
| `post_deploy.sh` | ~120 | Server-side setup (dirs, perms, composer, .htaccess) |
| `health_check.sh` | ~98 | Check 5 URLs + SSL expiry + disk + log errors |
| `logs.sh` | ~80 | Log viewer (app/php/cron, follow mode) |
| `deploy.ps1` | ~100 | Windows PowerShell deploy alternative |
| `deploy_hostinger.ps1` | ~80 | Hostinger-specific deploy |
| `http_smoke_test.js` | 105 | Node.js smoke test (21 endpoints) |
| `k6_load_test.js` | 148 | k6 load test (up to 1,000 VUs) |

### 5.3 Deploy Flow

```
Developer Machine
    │
    ├── npm run build (app + admin)
    │
    ├── deploy.sh
    │   ├── Step 0: Backup current production
    │   ├── Step 1: Upload backend (rsync auth/)
    │   ├── Step 2: Upload frontend (rsync app/dist, admin/dist)
    │   ├── Step 3: Upload landing page
    │   ├── Step 4: Upload deploy scripts
    │   ├── Step 5: Composer install (production)
    │   ├── Step 6: Run migrations (php migrate.php)
    │   ├── Step 7: Fix permissions
    │   └── Step 8: Health check
    │
    └── OR: push to main → GitHub Actions deploy job
```

### 5.4 Deployment Strategies

| Strategia | Supportata | Note |
|-----------|-----------|------|
| Direct deploy (SSH/SCP) | ✅ | Primary method |
| CI/CD automated | ⚠️ | Defined, not active |
| Symlink rollback | ✅ | rollback.sh (30s) |
| Blue-green | ❌ | Requires load balancer |
| Canary | ❌ | Requires traffic splitting |
| Feature flags | ⚠️ | DB table exists, basic usage |
| Zero-downtime | ⚠️ | Near zero (SCP in-place) |

---

## 6. Testing Automation

### 6.1 Assessment: 6.5/10

| Layer | Framework | Tests | CI | Coverage |
|-------|-----------|-------|----|---------| 
| **PHP Unit** | PHPUnit 11.5 | ~1,200+ | ✅ (in pipeline) | ~45-50% |
| **JS Unit** | Vitest 4.0 | ~333+ | ✅ (in pipeline) | ~40-50% |
| **E2E** | Playwright 1.58 | Exists | ❌ (not in CI) | Key flows |
| **Load** | k6 | 1 script (148 LOC) | ❌ | 1,000 VUs |
| **Smoke** | Node.js HTTP | 1 script (105 LOC, 21 endpoints) | ❌ | All domains |
| **Security** | — | ❌ | ❌ | — |
| **Total** | | ~1,533+ | Partial | ~45% |

### 6.2 Test Gaps

| Gap | Impatto | Effort |
|-----|---------|--------|
| E2E non in CI | 🟡 | 4h (add Playwright job) |
| No mutation testing | 🟢 | 8h (infection/stryker) |
| No snapshot testing (React) | 🟢 | 4h |
| Coverage threshold non enforced | 🟡 | 2h |
| No contract testing (API) | 🟡 | 12h |

---

## 7. Scheduled Operations (Cron)

### 7.1 Cron Job Inventory

| Script | Frequenza | Funzione | Lock | Alerts |
|--------|----------|----------|------|--------|
| `cron_backup.php` | `0 3 * * *` (daily 3 AM) | DB backup → Backblaze B2 (30 daily + 12 monthly) | ✅ flock | Telegram |
| `cron_email_queue.php` | `*/5 * * * *` (5 min) | Process email queue | ✅ flock | — |
| `cron_webhook_retry.php` | `*/5 * * * *` (5 min) | Retry failed webhooks (exponential backoff) | ✅ flock | — |
| `cron_platform_sdi.php` | `*/5 * * * *` (5 min) | Multi-gateway e-invoice submission (IT SDI, FR Chorus, DE PEPPOL) | ✅ flock | — |
| `cron_uptime_monitor.php` | `* * * * *` (1 min) | Monitor 4 domains + alert on downtime | ✅ flock | Telegram |
| `cron_kpi_alerts.php` | `*/15 * * * *` (15 min) | Evaluate KPI thresholds → notifications | ✅ flock | In-app |
| `cron_support_monitor.php` | `*/15 * * * *` (15 min) | Stuck tickets, SLA metrics, auto-close | ✅ flock | Telegram |
| `cron_cross_module_alerts.php` | `0 * * * *` (hourly) | Overdue invoices, stale deals, disconnected banks | ✅ flock | Email |
| `cron_scheduled_reports.php` | `0 8 * * *` (daily 8 AM) | Scheduled reports → email queue | ✅ flock | — |
| `cron_ob_consent.php` | `0 9 * * *` (daily 9 AM) | PSD2 consent expiry notifications | ✅ flock | Email |
| `cron_trial_expiry.php` | `0 2 * * *` (daily 2 AM) | Trial → Starter downgrade | ✅ flock | — |
| `cron_freeze_expired.php` | `0 3 * * *` (daily 3 AM) | Freeze expired subscriptions / data delete 12mo+ | ✅ flock | — |
| `cron_invoice_purge.php` | `0 0:30 * * *` (daily) | B2 archive purge (10y+1d retention) | ✅ flock | — |
| `cron_proactive_ai.php` | Daily | AI suggestions (trial CTA, weekly digest, cash-flow, overload) | ✅ flock | In-app |
| `cron_site_performance.php` | `0 3 * * *` (daily 3 AM) | PageSpeed scan published sites | ✅ flock | — |
| `job_worker.php` | `* * * * *` (1 min) | Background job queue processor | ✅ flock | — |

**Buone pratiche osservate**:
- ✅ Tutti usano `cron_lock.php` (flock) per prevenire esecuzione parallela
- ✅ Logging su `storage/logs/cron.log`
- ✅ Telegram alerts per operazioni critiche
- ⚠️ Manca: monitoring centralizzato esecuzione cron
- ⚠️ Manca: alerting su cron failure (se un cron non esegue)

---

## 8. Infrastructure as Code

### 8.1 Assessment: 3.5/10

| Pratica | Status |
|---------|--------|
| Server provisioning (Terraform/Ansible) | ❌ |
| Docker development | ❌ |
| Docker production | ❌ |
| Kubernetes | ❌ |
| Infrastructure docs | ⚠️ |
| Environment config (.env.example) | ✅ |
| `.env.example` completezza | ✅ (40+ variabili documentate) |
| `.env.production` (deploy_scripts) | ✅ |
| EnvStatusAction | ✅ (admin endpoint verifica env var) |

### 8.2 Environment Files

| File | Variabili | Scopo |
|------|----------|-------|
| `auth/.env.example` | ~50+ | Backend reference (DB, JWT, Stripe, AI, OCR, Backblaze, Telegram, etc.) |
| `app/.env.example` | ~5 | Frontend Vite config (API URL, Sentry, Pusher, Turnstile) |
| `deploy_scripts/.env.production` | ~10 | Deploy targets |
| `auth/.env.testing` | ~20 | PHPUnit test environment |

---

## 9. DevOps Maturity Roadmap

### Phase 1: Quick Wins (Settimane 1-2) — Target: 7.0/10

| # | Azione | Effort | Impact |
|---|--------|--------|--------|
| 1 | Configure GitHub Actions secrets | 30 min | CI/CD attivato |
| 2 | Attivare branch protection su `main` | 15 min | Quality gate |
| 3 | Add `npm audit` + `composer audit` step | 1h | Vuln detection |
| 4 | Cloudflare setup (DNS + CDN + WAF) | 2h | Security + perf |
| 5 | Configure Telegram deploy notification | 1h | Visibility |

### Phase 2: Strengthen (Mesi 1-3) — Target: 7.9/10

| # | Azione | Effort | Impact |
|---|--------|--------|--------|
| 6 | Add Playwright E2E to CI | 4h | E2E validation |
| 7 | PHPStan blocking mode (remove `|| echo`) | 2h | Type safety |
| 8 | Coverage threshold enforcement (70%) | 2h | Quality gate |
| 9 | Docker compose for dev | 8h | Dev consistency |
| 10 | Database migration in deploy job | 1h | Automated migrations |

### Phase 3: Optimize (Mesi 3-6) — Target: 8.5/10

| # | Azione | Effort | Impact |
|---|--------|--------|--------|
| 11 | VPS + Ansible provisioning | 24h | IaC |
| 12 | Blue-green deployment | 16h | Zero-downtime |
| 13 | OWASP ZAP in CI | 4h | Security automation |
| 14 | Mutation testing | 8h | Test quality |
| 15 | Cost monitoring (deploy costs, CI minutes) | 2h | FinOps |

---

*Documento generato il 2026-03-07. Assessment basato sull'analisi della pipeline CI/CD (deploy.yml 366 LOC), 10 deploy scripts, 16 cron jobs, e confronto con DORA metrics framework.*
