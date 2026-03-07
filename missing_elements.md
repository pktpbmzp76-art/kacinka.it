# Missing Elements — Gap Analysis

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Executive Summary

Analisi sistematica degli elementi mancanti per portare Kacinka da "prodotto funzionante" a "piattaforma SaaS enterprise-ready". Ogni gap è classificato per impatto, urgenza e effort stimato.

| Categoria | Gaps Critici | Gaps Medi | Gaps Bassi | Totale |
|-----------|-------------|-----------|------------|--------|
| **Infrastructure** | 3 | 2 | 1 | 6 |
| **DevOps & CI/CD** | 1 | 3 | 2 | 6 |
| **Security** | 3 | 2 | 1 | 6 |
| **Legal & Compliance** | 4 | 3 | 1 | 8 |
| **Product** | 2 | 4 | 3 | 9 |
| **Operations** | 2 | 3 | 2 | 7 |
| **Customer Facing** | 1 | 3 | 2 | 6 |
| **TOTALE** | **16** | **20** | **12** | **48** |

---

## 2. Infrastructure Gaps

| # | Gap | Impatto | Urgenza | Effort | Timeline |
|---|-----|---------|---------|--------|----------|
| I-1 | **VPS/Cloud migration** — Shared hosting non scala oltre ~100 utenti | 🔴 Critico | Pre-scale | 16-24h | M1-M3 |
| I-2 | **CDN** — Nessun CDN per asset statici (TTFB alto per utenti non-EU) | 🔴 Critico | Pre-launch | 2h | W1 |
| I-3 | **Redis/Memcached** — Nessun caching layer; tutto DB-direct | 🔴 Critico | Pre-scale | 8h | M2-M3 |
| I-4 | **Background job queue** — `job_worker.php` esiste ma usa polling DB | 🟡 Media | M3 | 12h | M3 |
| I-5 | **Database read replica** — Single DB instance = bottleneck reads | 🟡 Media | M6+ | 8h | M6 |
| I-6 | **File storage abstraction** — Flysystem presente ma solo local | 🟢 Bassa | M6+ | 4h | M6 |

### Impatto Stimato Migration Hosting
```
Scenario attuale (Hostinger shared):
  └─ Max concurrent requests: ~15-20
  └─ Max users: ~100
  └─ DB connections: ~10 simultaneous
  └─ No SSH cron reliability guaranteed

Scenario target (VPS €20-35/mo):
  └─ Max concurrent: ~200-500
  └─ Max users: ~1,000-5,000
  └─ DB connections: ~100+
  └─ Full cron control
  └─ Redis available
```

---

## 3. DevOps & CI/CD Gaps

| # | Gap | Impatto | Urgenza | Effort | Status |
|---|-----|---------|---------|--------|--------|
| D-1 | **CI/CD secrets non configurati** — `deploy.yml` esiste (366 LOC) ma secrets SSH mancanti su GitHub | 🔴 Critico | Pre-launch | 2h | Pipeline ready, secrets missing |
| D-2 | **Staging environment non attivo** — deploy-staging job definito, subdomain non configurato | 🟡 Media | Pre-launch | 4h | Job ready, infra missing |
| D-3 | **PHPStan non bloccante** — Config `--level=5` con `|| echo` (non-blocking) | 🟡 Media | M1 | 2h | Semi-active |
| D-4 | **ESLint admin** — Admin linting non incluso nel CI | 🟡 Media | M1 | 1h | Missing |
| D-5 | **Playwright E2E non in CI** — Tests esistono ma no CI job | 🟢 Bassa | M3 | 4h | Tests exist, no CI |
| D-6 | **Docker development env** — Nessun docker-compose.yml per dev locale | 🟢 Bassa | M3 | 8h | Missing |

### Current CI/CD Pipeline Status
```
deploy.yml (GitHub Actions) — 366 LOC
├── lint (ESLint app + PHP syntax + PHPStan)
├── test-frontend (Vitest app + admin)
├── test-backend (PHPUnit with MariaDB service)
├── build (Vite production builds + artifacts)
├── deploy (main → production via SSH/SCP)
│   ├── SSH key setup
│   ├── Backend deploy (auth/app, auth/src, auth/public)
│   ├── Frontend deploy (app/dist, admin/dist)
│   ├── Landing deploy (index.html, assets, _engine)
│   ├── Post-deploy (composer install, permissions)
│   ├── Health check (4 URLs)
│   └── Sentry release
└── deploy-staging (develop → staging via SSH/SCP)
```

---

## 4. Security Gaps

| # | Gap | Impatto | Urgenza | Effort | OWASP |
|---|-----|---------|---------|--------|-------|
| S-1 | **WAF assente** — Nessun web application firewall | 🔴 Critico | Pre-launch | 2h (Cloudflare) | A06 |
| S-2 | **Database encryption at rest** — Dati finanziari in plaintext su disco | 🔴 Critico | Pre-scale | 8h | A02 |
| S-3 | **Penetration test** — Mai eseguito | 🔴 Critico | Pre-launch | €2-5K budget | A05 |
| S-4 | **SameSite=None sui cookie** — Cross-site possibile | 🟡 Media | M1 | 1h | A07 |
| S-5 | **CSP unsafe-inline** — Preview endpoint permette inline scripts | 🟡 Media | M2 | 4h | A03 |
| S-6 | **Vulnerability scanning automatico** — No Dependabot, no Snyk | 🟢 Bassa | M2 | 1h | A06 |

---

## 5. Legal & Compliance Gaps

| # | Gap | Impatto | Urgenza | Effort |
|---|-----|---------|---------|--------|
| L-1 | **DPO (Data Protection Officer)** — Non nominato (obbligatorio se trattamento large-scale) | 🔴 Critico | Pre-launch | Esterno €1-3K/anno |
| L-2 | **DPA con sub-processor** — Mancano DPA firmati con Stripe, Google, Sentry, Pusher, Mindee, Backblaze | 🔴 Critico | Pre-launch | 16h |
| L-3 | **Fiscal disclaimer** — Fatture generate senza disclaimer "non sostitutivo della consulenza fiscale" | 🔴 Critico | Pre-launch | 2h |
| L-4 | **Art. 30 GDPR register** — Registro dei trattamenti non compilato | 🔴 Critico | Pre-launch | 8h |
| L-5 | **AUP (Acceptable Use Policy)** — Assente | 🟡 Media | M1 | 4h |
| L-6 | **Breach notification procedure** — Nessun playbook formale (GDPR Art. 33: 72h) | 🟡 Media | Pre-launch | 4h |
| L-7 | **SLA (Service Level Agreement)** — Nessun commitment di uptime | 🟡 Media | M2 | 4h |
| L-8 | **Trademark registration** — "Kacinka" non registrato EUIPO | 🟢 Bassa | M6 | €850 + 3-6 mesi |

---

## 6. Product Gaps

| # | Gap | Impatto | Urgenza | Effort |
|---|-----|---------|---------|--------|
| P-1 | **Onboarding wizard** — Nessun setup guidato post-registrazione | 🔴 Critico | Pre-launch | 24h |
| P-2 | **Conservazione sostitutiva** — Obbligo fiscale IT non implementato | 🔴 Critico | Pre-launch IT | 40h |
| P-3 | **Multi-currency** — Solo EUR nei plan; molti mercati target usano altre valute | 🟡 Media | M3 | 16h |
| P-4 | **SSO SAML/OIDC** — Enterprise plan lo promette, non implementato | 🟡 Media | M6 | 40h |
| P-5 | **API documentation pubblica** — OpenAPI spec non generata; docs parziali | 🟡 Media | M2 | 16h |
| P-6 | **Webhook outgoing** — Per integrazioni third-party (users want Zapier compatibility) | 🟡 Media | M3 | 24h |
| P-7 | **Mobile app** — Solo web responsive; competitors hanno mobile native | 🟢 Bassa | M12+ | 200h+ |
| P-8 | **Import/export CSV** — CSV import esiste, export limitato | 🟢 Bassa | M3 | 8h |
| P-9 | **White label** — Enterprise plan lo promette, parzialmente implementato | 🟢 Bassa | M6 | 24h |

---

## 7. Operations Gaps

| # | Gap | Impatto | Urgenza | Effort |
|---|-----|---------|---------|--------|
| O-1 | **Incident response playbook** — Nessun runbook formale | 🔴 Critico | Pre-launch | 8h |
| O-2 | **On-call rotation** — Solo fondatore (single point of failure) | 🔴 Critico | M3 | Hiring |
| O-3 | **APM (Application Performance Monitoring)** — Solo Sentry errors, no req-level tracing | 🟡 Media | M1 | 4h |
| O-4 | **Log aggregation** — Log locali su disco, no ELK/Loki | 🟡 Media | M3 | 8h |
| O-5 | **Capacity planning** — Nessuna proiezione load vs infrastructure | 🟡 Media | M2 | 4h |
| O-6 | **Disaster recovery test** — Mai eseguito restore da B2 | 🟢 Bassa | M1 | 2h |
| O-7 | **Runbook per cron jobs** — 15 cron definiti, documentazione operativa assente | 🟢 Bassa | M2 | 4h |

---

## 8. Customer-Facing Gaps

| # | Gap | Impatto | Urgenza | Effort |
|---|-----|---------|---------|--------|
| C-1 | **Knowledge base / Help center** — Solo AI assistant, no KB searchable | 🔴 Critico | Pre-launch | 24h |
| C-2 | **Status page** — Nessuna pagina pubblica uptime | 🟡 Media | Pre-launch | 2h (Betteruptime free) |
| C-3 | **Changelog pubblico** — CHANGELOG.md esiste, non esposto agli utenti | 🟡 Media | M1 | 4h |
| C-4 | **Onboarding email sequence** — Nessuna drip email post-signup | 🟡 Media | M1 | 8h |
| C-5 | **Video tutorials** — Nessun contenuto video | 🟢 Bassa | M3 | 20h |
| C-6 | **Community / forum** — Nessuna community platform | 🟢 Bassa | M6 | 8h |

---

## 9. Priority Matrix

### Must Fix Before Launch (Settimane 1-3)

| # | Gap | Effort | Owner |
|---|-----|--------|-------|
| L-3 | Fiscal disclaimer | 2h | Dev |
| I-2 | CDN setup (Cloudflare) | 2h | DevOps |
| S-1 | WAF (Cloudflare) | 2h | DevOps |
| D-1 | CI/CD secrets config | 2h | DevOps |
| C-2 | Status page | 2h | DevOps |
| S-4 | SameSite cookie fix | 1h | Dev |
| L-6 | Breach notification playbook | 4h | Legal |
| O-1 | Incident response playbook | 8h | Ops |
| L-2 | DPA drafting | 16h | Legal |
| L-4 | Art. 30 register | 8h | Legal |
| | **Subtotale** | **~49h** | |

### Must Fix Before Scale (M1-M3)

| # | Gap | Effort |
|---|-----|--------|
| P-1 | Onboarding wizard | 24h |
| I-1 | VPS migration | 16-24h |
| C-1 | Knowledge base | 24h |
| P-2 | Conservazione sostitutiva | 40h |
| L-1 | DPO nomination | External |
| S-3 | Penetration test | External |
| | **Subtotale** | **~120h + external** |

---

## 10. Gap Closure Roadmap

```
Week 1  ─── Cloudflare (CDN + WAF) + CI/CD secrets + Status page
Week 2  ─── Legal documents (DPA, disclaimers, Art. 30)
Week 3  ─── Incident playbook + Breach procedure
Week 4  ─── Soft launch (closed beta)
Month 2 ─── VPS migration + Onboarding wizard + Knowledge base
Month 3 ─── Redis + Conservazione sostitutiva + Pen test
Month 6 ─── SSO + API docs + Multi-currency
```

---

*Documento generato il 2026-03-07. Gap assessment basato su analisi completa del codebase (519,535 LOC), confronto con SaaS maturity model, e requisiti regulatory EU/IT.*
