# Kacinka — Piattaforma All-in-One per Agenzie, PMI e Freelancer

> CRM, contabilità in partita doppia, fatturazione elettronica (FatturaPA / PEPPOL), gestione progetti, sito web builder, email marketing, automazioni e molto altro in un'unica piattaforma SaaS pensata per il mercato italiano.

---

## Indice

- [Panoramica](#panoramica)
- [Architettura](#architettura)
- [Struttura del Progetto](#struttura-del-progetto)
- [Requisiti](#requisiti)
- [Setup Locale](#setup-locale)
- [Moduli Funzionali](#moduli-funzionali)
- [API Reference](#api-reference)
- [Testing](#testing)
- [SEO & i18n](#seo--i18n)
- [Build & Deploy](#build--deploy)
- [Variabili d'Ambiente](#variabili-dambiente)
- [Convenzioni](#convenzioni)
- [Documentazione](#documentazione)
- [License](#license)

---

## Panoramica

**Kacinka** unifica in un'unica piattaforma gli strumenti che tipicamente richiedono 5-8 abbonamenti separati. Pensata al 100% per il business italiano con IVA, PEC, SDI e Codice Fiscale nativi.

| Caratteristica | Dettaglio |
|---|---|
| Target | Agenzie digitali, PMI, Freelancer (Italia) |
| Pricing | Da €7,90/mese — team illimitati, nessun costo per utente |
| Lingue UI | Italiano (default), English |
| Lingue Landing | 20 (it, en, de, fr, es, pt, nl, pl, ro, sv, da, fi, cs, el, tr, ja, zh, ko, ar, hi) |
| White-label | Branding personalizzabile per agenzie |
| Hosting | Hostinger Business |
| API Endpoints | 567 REST routes |
| Database | 147 tabelle MySQL |

---

## Architettura

La piattaforma è composta da **4 sotto-progetti** che comunicano via REST API:

```
┌──────────────────┐     ┌───────────────────┐     ┌──────────────────┐
│  kacinka.app      │     │  cloud.kacinka.app    │     │ master.kacinka.app │
│  Landing Page    │     │  React SPA (User)  │     │ React SPA (Admin)│
│  HTML statico    │     │  Vite + TailwindCSS│     │ Vite + TailwindCSS│
│  + 10 pagine     │     │  + 28 moduli       │     │ + 19 pagine      │
└──────────────────┘     └────────┬───────────┘     └────────┬─────────┘
                                  │ REST API                  │ REST API
                                  ▼                           ▼
                         ┌────────────────────────────────────────────┐
                         │          api.kacinka.app                   │
                         │       PHP 8.2 / Slim 4 Backend            │
                         │    JWT Auth · Stripe · MySQL · 147 tables │
                         │     468 Actions · 59 Services · 14 MW     │
                         └────────────────────────────────────────────┘
```

| Servizio | Tecnologia | Directory | Porta Dev |
|---|---|---|---|
| **kacinka.app** | HTML/CSS/JS statico (20 lingue) | `./` | Apache (XAMPP) |
| **cloud.kacinka.app** | React 19.2 + Vite 7.2 + TailwindCSS 4.1 | `app/` | `localhost:5173` |
| **master.kacinka.app** | React 19.2 + Vite 7.2 + TailwindCSS 4.1 | `admin/` | `localhost:5174` |
| **api.kacinka.app** | PHP 8.2 + Slim 4 + PHP-DI | `auth/` | `localhost:8080` |
| **Database** | MySQL 8.0 (147 tabelle) | — | `localhost:3306` |

---

## Struttura del Progetto

```
public_html/
├── index.html                # Landing page kacinka.app (1200+ righe, SEO completo)
├── privacy.html              # Informativa Privacy (GDPR)
├── terms.html                # Termini di Servizio
├── cookies.html              # Cookie Policy
├── 404.html                  # Pagina errore 404
├── robots.txt                # Configurazione crawlers
├── sitemap.xml               # Sitemap XML (20 lingue hreflang)
├── site.webmanifest          # PWA manifest
│
├── chi-siamo/index.html      # Chi Siamo
├── contatti/index.html       # Contatti
├── servizi/                  # 5 pagine servizio
│   ├── contabilita/          # Contabilità
│   ├── crm/                  # CRM
│   ├── fatturazione-elettronica/  # Fatturazione Elettronica
│   ├── intelligenza-artificiale/  # AI & Automazioni
│   └── sviluppo-web/        # Sviluppo Web
│
├── assets/                   # Asset condivisi landing
│   ├── css/common.css        # CSS condiviso (dark + light mode)
│   ├── js/i18n.js            # i18n 20 lingue (1500+ righe)
│   ├── js/i18n-legal.js      # i18n pagine legali
│   ├── favicons/             # Favicon SVG + PNG + Apple Touch
│   └── og/                   # OG cover images
│
├── app/                      # 🟢 Frontend utente (React SPA)
│   ├── src/
│   │   ├── components/       # 28 moduli UI riutilizzabili
│   │   ├── pages/            # 25 route pages
│   │   ├── providers/        # React Context (Auth, Theme, Query)
│   │   ├── stores/           # 20 Zustand stores
│   │   ├── lib/              # API client (2041 righe), hooks, schemas (Zod)
│   │   ├── i18n/             # i18next — locales/it.json, locales/en.json
│   │   └── test/             # 35 Vitest test files
│   ├── e2e/                  # 13 Playwright E2E specs
│   ├── public/               # manifest.json
│   ├── package.json
│   ├── vite.config.js
│   └── playwright.config.js
│
├── admin/                    # 🟠 Pannello admin (React SPA)
│   ├── src/
│   │   ├── components/       # Componenti admin
│   │   ├── pages/            # 19 pagine admin
│   │   ├── providers/        # Context providers
│   │   ├── stores/           # Zustand stores
│   │   └── lib/              # API client, utilities
│   ├── package.json
│   └── vite.config.js
│
├── auth/                     # 🔵 Backend API (PHP 8.2 / Slim 4)
│   ├── app/
│   │   ├── routes.php        # 567 API routes (775 righe)
│   │   ├── dependencies.php  # PHP-DI container
│   │   ├── middleware.php     # Pipeline middleware globale
│   │   └── settings.php      # Config da .env (25+ variabili)
│   ├── src/
│   │   ├── Action/           # 468 controller actions (65 domini)
│   │   ├── Middleware/        # 14 middleware (Auth, CSRF, CORS, Rate Limit, ...)
│   │   └── Service/          # 59 business logic services
│   ├── migrations/
│   │   └── 000_full_schema.sql  # Schema completo (147 tabelle, 2567 righe)
│   ├── tests/
│   │   ├── Unit/             # 28 unit test files
│   │   ├── Integration/      # 11 integration test files
│   │   └── Factory/          # 2 factory files (User, Project)
│   ├── storage/
│   │   ├── logs/             # Application logs
│   │   ├── einvoices/        # Generated FatturaPA XML
│   │   └── projects/         # File uploads
│   ├── vendor/               # Composer dependencies
│   ├── public/index.php      # Entry point
│   ├── composer.json
│   ├── phpunit.xml
│   └── VERSION
│
├── deploy_scripts/           # Deploy & Operations
│   ├── deploy.ps1            # Windows PowerShell deploy (SCP/SSH)
│   ├── deploy.sh             # Bash deploy
│   ├── build_and_deploy.sh   # Build + deploy completo
│   ├── health_check.sh       # Health check (API, SSL, disk, cron)
│   ├── cron_email_queue.php  # Email queue processor
│   ├── cron_webhook_retry.php # Webhook retry (exp. backoff)
│   ├── test_seo.ps1          # SEO test script
│   └── test_smoke_app.ps1    # Smoke test script
│
└── docs/                     # Documentazione
    ├── index.html            # Interactive API Reference
    ├── PLATFORM_AUDIT.md     # Audit completa piattaforma
    ├── DESIGN_SYSTEM.md      # Design system
    ├── CROSS_BROWSER_UX_TESTING.md
    ├── Kacinka_API.postman_collection.json (210+ endpoints)
    ├── Kacinka_Local.postman_environment.json
    └── Kacinka_Production.postman_environment.json
```

---

## Requisiti

### Sviluppo locale

| Software | Versione |
|---|---|
| Node.js | >= 18 LTS |
| npm | >= 9 |
| PHP | >= 8.2 |
| Composer | >= 2.x |
| MySQL | >= 8.0 |
| XAMPP | Consigliato (Windows) |

### Estensioni VS Code consigliate

- ES7+ React/Redux/React-Native snippets
- Tailwind CSS IntelliSense
- PHP Intelephense
- SQLTools + MySQL driver
- Prettier - Code formatter
- ESLint

---

## Setup Locale

### 1. Clona il repository

```bash
git clone https://github.com/<org>/kacinka.git
cd kacinka/public_html
```

### 2. Backend (auth/)

```bash
cd auth

# Installa dipendenze PHP
composer install

# Configura l'ambiente
cp .env.example .env
# Modifica .env con le tue credenziali locali (vedi sezione Variabili d'Ambiente)

# Crea il database
mysql -u root -e "CREATE DATABASE kacinka_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"

# Importa lo schema completo (147 tabelle)
mysql -u root kacinka_db < migrations/000_full_schema.sql

# Avvia il server PHP
composer start
# → http://localhost:8080
```

### 3. Frontend Utente (app/)

```bash
cd app

# Installa dipendenze
npm install

# Configura l'ambiente
cp .env.example .env
# Imposta VITE_API_URL=http://localhost:8080

# Avvia il dev server
npm run dev
# → http://localhost:5173
```

### 4. Pannello Admin (admin/)

```bash
cd admin

# Installa dipendenze
npm install

# Avvia il dev server
npm run dev
# → http://localhost:5174
```

### VS Code Tasks

Il progetto include task pre-configurati in `.vscode/tasks.json`:

| Task | Azione |
|---|---|
| **Vite Dev Server** | Avvia app dev server (porta 5173) |
| **Admin Dev Server** | Avvia admin dev server (porta 5174) |

---

## Moduli Funzionali

### 📊 Contabilità (Modulo Completo — 8 fasi)

| Feature | Descrizione |
|---|---|
| Piano dei Conti | Template per paese, CRUD, gerarchia |
| Partita Doppia | Scritture contabili con validazione dare/avere |
| Anni e Periodi Fiscali | Gestione apertura/chiusura |
| Fatturazione V2 | Righe, multi-aliquota, note credito, PDF, numerazione sequenziale |
| Pagamenti | Allocazione multi-fattura, parziali, rimborsi, schedulati |
| Gestione Spese | Workflow approvazione, categorie, ricevute OCR |
| Riconciliazione Bancaria | Auto-matching, import CSV/OFX, regole |
| Report Finanziari | P&L, Bilancio, Cash Flow, Bilancio di Verifica, Aging |
| Gestione Fiscale | Liquidazione IVA, LIPE, ritenute d'acconto, regimi |
| Fatturazione Elettronica | FatturaPA 1.2.2, PEPPOL BIS 3.0, invio SDI |
| Multi-valuta | Tassi ECB, conversione automatica |
| Budget & Centri di Costo | Budget con analisi scostamenti |
| OCR Ricevute | Integrazione Mindee / Veryfi |
| Open Banking | Integrazione PSD2 |
| Client Portal V2 | Accesso clienti con magic link |

### 📋 CRM

| Feature | Descrizione |
|---|---|
| Contatti | CRUD, import CSV, export, merge, timeline, lead scoring |
| Aziende | CRUD con relazione a contatti |
| Pipeline Deals | Kanban drag-and-drop, stadi personalizzabili |
| Deal Activities | Chiamate, email, note, meeting |
| Automazioni CRM | Trigger su eventi contatti/deals |
| Promemoria | CRUD + completamento |
| Campi Personalizzati | Definizioni custom per contatti/deals |
| CRM ↔ Contabilità | Bridge bidirezionale |

### 📁 Project Management

| Feature | Descrizione |
|---|---|
| Progetti | CRUD con limiti piano, template |
| Task | CRUD, drag-reorder, subtask, dipendenze |
| Time Tracking | Timer, entry manuali, report |
| Calendario | Eventi progetto + personale (FullCalendar) |
| File Manager | Upload, download, preview, organize |
| Document Pipeline | Workflow approvazione documenti |
| Deliverable Approvals | Approvazioni con accesso client portal |

### 👥 Team & Collaborazione

| Feature | Descrizione |
|---|---|
| Team Management | Inviti, ruoli, permessi granulari (RBAC) |
| Chat | Messaggi, pin, reazioni, menzioni, thread |
| Commenti | Su task, progetti, deals |
| Notifiche | In-app, email digest, preferenze personalizzate |

### 📧 Comunicazione & Marketing

| Feature | Descrizione |
|---|---|
| Email Templates | Editor template con preview |
| Email Campaigns | Creazione, invio, statistiche |
| Email Lists | Gestione liste + iscritti |
| Unsubscribe | Pagina pubblica di disiscrizione |
| Workflow Builder | Automazioni multi-step con trigger/azioni |

### 🌐 Site Builder & Forms

| Feature | Descrizione |
|---|---|
| Site Builder | Creazione siti web da template |
| Site Templates | Catalogo template personalizzabili |
| Form Builder | Form standalone con embed code |
| Contact Forms | Invio form → CRM automatico |
| Portfolio / Showcase | Pagine portfolio |

### 💰 Billing & Commerce

| Feature | Descrizione |
|---|---|
| Piani & Abbonamenti | Starter / Professional / Agency / Enterprise |
| Checkout Stripe | Pagamento sicuro via Stripe Checkout |
| Billing Portal | Gestione carta, fatture, disdetta |
| Coupon | Validazione codici sconto |
| Commerce | Prodotti, carrello, entitlements |
| Grace Period | Periodo di grazia post-scadenza |

### 🔒 Sicurezza & Compliance

| Feature | Descrizione |
|---|---|
| JWT Auth | Access token + refresh token con rotazione |
| OAuth2 | Google Sign-In (Apple — disabled, Phase 9) |
| 2FA | Two-factor authentication (TOTP) |
| CSRF | Double-submit cookie pattern |
| Rate Limiting | Per-IP, per-endpoint |
| Security Headers | CSP, HSTS (preload), X-Frame-Options, Referrer-Policy |
| Session Management | Lista sessioni, revoca singola/totale |
| Audit Log | Registro completo azioni utente |
| GDPR | Export dati personali, cancellazione account |
| Verification Gate | Blocco scritture per utenti non verificati |
| API Key Auth | HMAC signatures, scopes, rate limits, rotazione |
| Entitlement MW | Feature gating per piano |
| Permission MW | Permessi granulari per ruolo |

### ⚙️ Admin Panel (19 pagine)

| Feature | Descrizione |
|---|---|
| Dashboard | Statistiche utenti, progetti, revenue |
| User Management | CRUD utenti, dettaglio, ban |
| Feature Flags | Toggle funzionalità in tempo reale |
| System Settings | Configurazione globale |
| System Health | Monitoraggio salute sistema |
| Subscriptions | Panoramica abbonamenti attivi |
| Revenue | Report entrate |
| Chat Monitoring | Lettura chat per moderazione |
| AI Escalation | Inbox escalation da AI Assistente |
| Broadcast | Notifiche/comunicazioni a tutti gli utenti |
| Audit Log | Log azioni admin |

### 🤖 AI & Automazione

| Feature | Descrizione |
|---|---|
| AI Assistant | Assistente intelligente (OpenAI) |
| AI Mention | Menzione @AI nei commenti/chat |
| AI Escalation | Escalation intelligente a operatore |
| Workflow Builder | Automazioni multi-step |

### 🔧 DevOps & Monitoring

| Feature | Descrizione |
|---|---|
| Health Check | `/health` + `/health/detailed` |
| DevOps Dashboard | Status servizi in tempo reale |
| Webhooks | CRUD + test + delivery log + retry automatico |
| Cron Jobs | Email queue, webhook retry |
| Legal Consents | Tracking consensi legali |

---

## API Reference

Il backend espone **567 endpoint REST** su `api.kacinka.app`, organizzati in:

- **468 Action classes** across 65 domini
- **59 Service classes** per business logic
- **14 Middleware** layer

### Autenticazione

Tutte le route protette richiedono:
```
Authorization: Bearer <access_token>
```
I cookie `refresh_token` (HttpOnly, Secure, SameSite=None) gestiscono il token refresh automatico.

### Endpoint principali

| Gruppo | Endpoints | Auth |
|---|---|---|
| Health | `GET /health`, `/health/detailed` | No |
| Auth | `POST /login, /register, /refresh, /logout` | No |
| Password Reset | `POST /forgot-password, /reset-password` | No |
| OAuth2 | `GET /auth/google` | No |
| Me | `GET /me`, `PUT /me/profile, /me/password` | Sì |
| Projects | `CRUD /projects` | Sì |
| Tasks | `CRUD /projects/{id}/tasks` | Sì |
| Deals | `CRUD /projects/{id}/deals` | Sì |
| Contacts | `CRUD /contacts` | Sì |
| Companies | `CRUD /companies` | Sì |
| Calendar | `CRUD /projects/{id}/events` | Sì |
| Files | `CRUD /projects/{id}/files` | Sì |
| Team | `CRUD /projects/{id}/team` | Sì |
| Chat | `CRUD /chats, /chats/{id}/messages` | Sì |
| Accounting | `CRUD /accounting/*` (chart, journal, fiscal-years) | Sì |
| Invoices V2 | `CRUD /invoices, /invoices/{id}/payments` | Sì |
| Expenses | `CRUD /expenses, /expenses/{id}/approve` | Sì |
| Bank | `CRUD /bank-accounts, /bank-import, /bank-reconciliation` | Sì |
| Tax | `GET /tax/*, CRUD /tax/vat-liquidation` | Sì |
| E-Invoice | `POST /einvoice/generate, /einvoice/send` | Sì |
| Reports | `GET /reports/*` (P&L, balance-sheet, cash-flow, aging) | Sì |
| Budgets | `CRUD /budgets, /cost-centers` | Sì |
| Multi-currency | `GET /currencies, /exchange-rates` | Sì |
| Client Portal | `GET /portal/*` (magic link auth) | Token |
| Notifications | `GET /notifications` | Sì |
| Email | `CRUD /email/templates, campaigns, lists` | Sì |
| Workflows | `CRUD /workflows` | Sì |
| Forms | `CRUD /standalone-forms, /forms` | Sì |
| Sites | `CRUD /sites` | Sì |
| Webhooks | `CRUD /webhooks` | Sì |
| Analytics | `GET /analytics/overview, trends, export` | Sì |
| API Keys | `CRUD /api-keys` | Sì |
| Legal | `GET /legal/consents, POST /legal/consent` | Sì |
| Admin | `GET /admin/stats, users, ...` (39 routes) | Admin |

📦 **Postman Collection** completa: `docs/Kacinka_API.postman_collection.json` (210+ endpoints documentati)

---

## Testing

### Backend (auth/)

```bash
cd auth

# Esegui tutti i test PHPUnit
vendor/bin/phpunit

# Risultato atteso: 262 tests, 412 assertions — OK
```

| Suite | Files | Coverage |
|---|---|---|
| Unit Tests | 28 files (ChartOfAccounts, JournalEntry, Tax, Expense, Payment, InvoiceV2, Bank, EInvoice, Budget, Currency, Reports, JWT, Mail, Notification, Security, Sessions, 2FA, Audit, OCR, OpenBanking, etc.) | Core business logic |
| Integration Tests | 11 files (AuthFlow, InvoicePayment, ExpenseApproval, BudgetVariance, MultiCurrency, EInvoice, Coupon, Entitlement, InvoiceOwnership, Health) | Cross-service flows |
| Factories | UserFactory, ProjectFactory | Test data generation |

### Frontend (app/)

```bash
cd app

# Unit / Component tests (Vitest + Testing Library)
npm test                # 35 test files

# Con coverage
npm run test:coverage

# E2E tests (Playwright)
npx playwright test     # 13 spec files

# E2E con UI
npx playwright test --ui
```

| Suite | Files | Coverage |
|---|---|---|
| Vitest | 35 files (stores, components, utilities) | UI + state logic |
| Playwright E2E | 13 specs (auth, accounting, billing, invoice, expense, navigation, responsive, i18n, a11y) | User flows |

---

## SEO & i18n

### SEO (tutte le pagine pubbliche)

- ✅ Schema.org JSON-LD (WebSite, Organization, SoftwareApplication, FAQPage, AboutPage, ContactPage, Service, BreadcrumbList)
- ✅ Open Graph (og:title, og:description, og:image, og:url, og:locale, og:locale:alternate, og:site_name)
- ✅ Twitter Card (summary_large_image + title + description + image)
- ✅ Hreflang tags (it, en, x-default) su tutte le pagine
- ✅ Canonical URLs
- ✅ Sitemap XML con hreflang per 20 lingue
- ✅ robots.txt ottimizzato
- ✅ Web Manifest (PWA-ready)
- ✅ SVG + PNG favicon
- ✅ Light mode + Dark mode (`prefers-color-scheme`)
- ✅ Font preloading, deferred CSS, `content-visibility: auto`
- ✅ Accessibility: skip-to-content, aria-labels, heading hierarchy, sr-only

### i18n

| Ambito | Lingue | File |
|---|---|---|
| Landing page + servizi | 20 lingue | `assets/js/i18n.js` (1501 righe) |
| Pagine legali | 6 complete (it, en, de, fr, es, pt) + headings per 14 | `assets/js/i18n-legal.js` |
| App SPA | 2 lingue (it, en) | `app/src/i18n/locales/` |

---

## Build & Deploy

### Build per produzione

```bash
# Frontend utente
cd app && npm run build      # → dist/

# Admin panel
cd admin && npm run build    # → dist/
```

### Deploy su Hostinger

```bash
# Build completa + upload via SSH
./deploy_scripts/build_and_deploy.sh

# Solo deploy (senza rebuild)
./deploy_scripts/deploy.sh

# Windows PowerShell
.\deploy_scripts\deploy.ps1

# Health check post-deploy
./deploy_scripts/health_check.sh

# Controlla i log
./deploy_scripts/logs.sh
```

### Cron Jobs (produzione)

| Job | Script | Frequenza |
|---|---|---|
| Email Queue | `cron_email_queue.php` | Ogni 5 min |
| Webhook Retry | `cron_webhook_retry.php` | Ogni 15 min |

---

## Variabili d'Ambiente

### Backend (`auth/.env`)

| Variabile | Descrizione | Esempio |
|---|---|---|
| `APP_ENV` | Ambiente | `development` / `production` |
| `DB_HOST` | Host MySQL | `localhost` |
| `DB_NAME` | Nome database | `kacinka_db` |
| `DB_USER` | Utente database | `root` |
| `DB_PASS` | Password database | — |
| `JWT_SECRET` | Chiave segreta JWT (base64) | `php -r "echo base64_encode(random_bytes(64));"` |
| `JWT_EXPIRY` | Scadenza access token | `900` (15 min) |
| `REFRESH_EXPIRY` | Scadenza refresh token | `2592000` (30 giorni) |
| `CORS_ORIGIN` | Origini consentite | `http://localhost:5173,http://localhost:5174` |
| `STRIPE_SECRET_KEY` | Stripe secret key | `sk_test_...` |
| `STRIPE_WEBHOOK_SECRET` | Stripe webhook secret | `whsec_...` |
| `MAIL_HOST` | SMTP host | `smtp.hostinger.com` |
| `MAIL_PORT` | SMTP port | `465` |
| `MAIL_USER` | SMTP username | `noreply@kacinka.app` |
| `MAIL_PASSWORD` | SMTP password | — |
| `GOOGLE_CLIENT_ID` | Google OAuth client ID | — |
| `GOOGLE_CLIENT_SECRET` | Google OAuth secret | — |
| `OPENAI_API_KEY` | OpenAI API key | `sk-...` |
| `SENTRY_DSN` | Sentry DSN | `https://...@sentry.io/...` |

### Frontend (`app/.env`)

| Variabile | Descrizione | Esempio |
|---|---|---|
| `VITE_API_URL` | URL del backend API | `http://localhost:8080` |
| `VITE_APP_NAME` | Nome app | `Kacinka` |
| `VITE_APP_ENV` | Ambiente | `development` |
| `VITE_SENTRY_DSN` | Sentry DSN (prod only) | — |

---

## Convenzioni

### Codice

- **Frontend**: React 19 functional components, hooks, Zustand per state management
- **Stile**: TailwindCSS 4 (utility-first), dark + light mode, Inter font
- **Backend**: PSR-4 autoload, Action pattern (1 classe per endpoint), Services per business logic
- **API**: JSON REST, HTTP status codes semantici, risposta `{ data, meta, error }`
- **Auth**: JWT access + refresh token (HttpOnly cookie), CSRF double-submit
- **Validazione**: Zod (frontend), tipo constraint route + Action validation (backend)
- **i18n**: `data-i18n` attributi per HTML statico, i18next per React SPA
- **Test**: PHPUnit (backend), Vitest (frontend unit), Playwright (E2E)

### Branching

| Branch | Uso |
|---|---|
| `main` | Produzione — deploy automatico |
| `develop` | Integrazione — merge delle feature |
| `feature/*` | Nuove funzionalità |
| `fix/*` | Bug fix |

### Database

Lo schema completo è in `auth/migrations/000_full_schema.sql` (147 tabelle, 2567 righe).  
Importabile direttamente in phpMyAdmin o via CLI:

```bash
mysql -u root kacinka_db < auth/migrations/000_full_schema.sql
```

---

## Documentazione

| Documento | Percorso | Descrizione |
|---|---|---|
| API Reference | `docs/index.html` | Interactive API reference (dark theme, ricerca) |
| Postman Collection | `docs/Kacinka_API.postman_collection.json` | 210+ endpoints con esempi |
| Platform Audit | `docs/PLATFORM_AUDIT.md` | Audit completa: cosa funziona, issues, mancanze |
| Design System | `docs/DESIGN_SYSTEM.md` | Specifiche componenti UI |
| Cross-Browser Testing | `docs/CROSS_BROWSER_UX_TESTING.md` | Test matrix browser/device |
| Accounting Roadmap | `docs/ROADMAP_ACCOUNTING_MODULE.md` | Roadmap 16 fasi modulo contabilità |
| Deploy Guide | `deploy_scripts/README.md` | Guida deployment |

---

## License

Proprietary — © 2026 Kacinka. Tutti i diritti riservati.
