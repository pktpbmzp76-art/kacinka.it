# Architecture Overview

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. System Architecture

### 1.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                              │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────┐    │
│  │ Marketing     │  │ Cloud App    │  │ Admin Panel        │    │
│  │ (kacinka.app) │  │ (cloud.      │  │ (master.           │    │
│  │ Engine PHP    │  │  kacinka.app)│  │  kacinka.app)      │    │
│  │ 204K LOC      │  │ React 19.2  │  │ React 19.2         │    │
│  └──────┬───────┘  └──────┬───────┘  └──────┬─────────────┘    │
│         │                  │                  │                   │
└─────────┼──────────────────┼──────────────────┼─────────────────┘
          │                  │                  │
          ▼                  ▼                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                        API LAYER                                 │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Slim 4 Router (api.kacinka.app)                          │    │
│  │ 1,059 routes │ 1,460 line routes.php                     │    │
│  └──────────────────────┬───────────────────────────────────┘    │
│                         │                                        │
│  ┌──────────────────────┼──────────────────────────────────┐    │
│  │              MIDDLEWARE PIPELINE                          │    │
│  │  CORS → Security → RateLimit → Auth → Permission →      │    │
│  │  PlanLimits → Entitlement → RequestLogger                │    │
│  │  (21 middleware, 3,370 LOC)                              │    │
│  └──────────────────────┼──────────────────────────────────┘    │
│                         │                                        │
│  ┌──────────────────────┼──────────────────────────────────┐    │
│  │              ACTION LAYER (Controllers)                   │    │
│  │  677 Action Classes │ 82 Directories │ 51,141 LOC        │    │
│  └──────────────────────┼──────────────────────────────────┘    │
│                         │                                        │
│  ┌──────────────────────┼──────────────────────────────────┐    │
│  │              SERVICE LAYER (Business Logic)               │    │
│  │  120 Services │ 67,536 LOC                                │    │
│  │  Top: EInvoiceService(4,051) AiAssistant(1,692)          │    │
│  └──────────────────────┼──────────────────────────────────┘    │
│                         │                                        │
└─────────────────────────┼───────────────────────────────────────┘
                          │
┌─────────────────────────┼───────────────────────────────────────┐
│                   DATA & EXTERNAL LAYER                          │
│  ┌────────────┐  ┌─────────────┐  ┌───────────────────────┐    │
│  │ MariaDB     │  │ File System │  │ External Services      │    │
│  │ 176 tables  │  │ Flysystem   │  │ ├── Gemini AI          │    │
│  │             │  │ B2 Backup   │  │ ├── Tink Open Banking  │    │
│  │             │  │             │  │ ├── Stripe Payments     │    │
│  │             │  │             │  │ ├── Pusher WebSocket    │    │
│  │             │  │             │  │ ├── Sentry Monitoring   │    │
│  │             │  │             │  │ ├── OpenAPI SDI/eSign   │    │
│  │             │  │             │  │ └── Google APIs         │    │
│  └────────────┘  └─────────────┘  └───────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

### 1.2 Architecture Pattern: Action-Service-Repository

```
Request → Router → Middleware → Action → Service → Database
                                  │         │
                                  │         ├── External APIs
                                  │         └── File System
                                  │
                                  └── Response (JSON)
```

**Action**: Thin controller. Estrae input dalla request, chiama service, formatta response.
**Service**: Business logic completa. Validazione, orchestrazione, side effects.
**Repository**: Implicita — i service accedono direttamente a PDO/query builder.

---

## 2. Backend Architecture

### 2.1 PHP Stack

| Componente | Package | Versione | Ruolo |
|-----------|---------|----------|-------|
| Web Framework | Slim Framework | 4.* | Routing, middleware, PSR-7 |
| DI Container | PHP-DI | 7.1 | Dependency injection |
| Logging | Monolog | 3.10 | Structured logging |
| Environment | vlucas/phpdotenv | 5.6 | Configuration management |
| Authentication | firebase/php-jwt | 7.0 | JWT token generation/validation |
| Payments | stripe/stripe-php | 19.3 | Billing, subscriptions |
| Email | phpmailer/phpmailer | 7.0 | SMTP email sending |
| File Storage | league/flysystem | 3.31 | Abstracted file system |
| Error Tracking | sentry/sentry | 4.20 | Error monitoring |
| PDF Generation | dompdf/dompdf | 3.1 | Invoice PDF rendering |
| QR Codes | chillerlan/php-qrcode | 5.0 | QR code generation |
| Testing | phpunit/phpunit | 11.5 | Unit/integration tests |

### 2.2 Middleware Pipeline

| # | Middleware | LOC | Funzione |
|---|-----------|-----|---------|
| 1 | CorsMiddleware | 110 | Cross-origin resource sharing |
| 2 | SecurityHeadersMiddleware | 92 | CSP, X-Frame-Options, HSTS |
| 3 | MaintenanceMiddleware | 69 | Maintenance mode toggle |
| 4 | RateLimitMiddleware | 245 | IP + user-based rate limiting |
| 5 | AuthMiddleware | 100 | JWT validation |
| 6 | AdminMiddleware | 64 | Admin-only routes |
| 7 | ApiKeyAuthMiddleware | 372 | API key authentication |
| 8 | ApiV1PrefixMiddleware | 60 | API versioning prefix |
| 9 | ApiVersionMiddleware | 48 | Version negotiation |
| 10 | PermissionMiddleware | 94 | Role-based access control |
| 11 | PlanLimitsMiddleware | 708 | Plan feature/limit enforcement |
| 12 | EntitlementMiddleware | 168 | Module access control per plan |
| 13 | GracePeriodMiddleware | 105 | Grace period for overages |
| 14 | FrozenProjectMiddleware | 137 | Block writes on frozen projects |
| 15 | TrialGateMiddleware | 123 | Trial period enforcement |
| 16 | VerificationGateMiddleware | 140 | Email verification requirement |
| 17 | SmtpGateMiddleware | 118 | SMTP configuration check |
| 18 | CsrfMiddleware | 158 | CSRF protection |
| 19 | HttpCacheMiddleware | 175 | HTTP caching headers |
| 20 | QueryTimingMiddleware | 70 | SQL query performance logging |
| 21 | RequestLoggerMiddleware | 214 | Request/response audit trail |
| | **TOTALE** | **3,370** | |

### 2.3 Route Analysis

| Method | Count | % |
|--------|-------|---|
| GET | 502 | 47.4% |
| POST | 348 | 32.9% |
| PUT | 99 | 9.4% |
| DELETE | 107 | 10.1% |
| PATCH | 3 | 0.3% |
| **TOTALE** | **1,059** | **100%** |

---

## 3. Frontend Architecture

### 3.1 React Stack

| Componente | Package | Versione | Ruolo |
|-----------|---------|----------|-------|
| UI Library | React | 19.2 | Component rendering |
| DOM | react-dom | 19.2 | Browser rendering |
| Routing | react-router-dom | 7.13 | Client-side routing |
| State | Zustand | 5.0.11 | Global state (33 stores) |
| Data Fetching | @tanstack/react-query | 5.90.20 | Server state + caching |
| i18n | i18next | 25.8.4 | 20 lingue, 62 namespace |
| Animations | framer-motion | 12.33 | UI animations |
| Charts | recharts | 3.7 | Data visualization |
| Forms | Zod | 4.3.6 | Schema validation |
| Rich Editor | @tiptap/* | 3.19 | Rich text editing |
| Icons | lucide-react | 0.563 | Icon library |
| Calendar | @fullcalendar/* | 6.1.20 | Calendar views |
| Dates | date-fns | 4.1 | Date manipulation |
| WebSocket | pusher-js | 8.4 | Real-time updates |
| Sanitization | dompurify | 3.3.1 | XSS protection |
| Error Tracking | @sentry/react | 10.39 | Frontend error monitoring |

### 3.2 State Management Architecture

```
Zustand Stores (33) ─── Each store: create + devtools
├── Global Stores
│   ├── useAuthStore (user, token, permissions)
│   ├── useProjectStore (current project, settings)
│   ├── useNotificationStore (toasts, notifications)
│   └── useThemeStore (dark mode, sidebar state)
├── Module Stores
│   ├── useCrmStore (deals, pipeline)
│   ├── useInvoiceStore (invoices, templates)
│   ├── useExpenseStore (expenses, categories)
│   ├── useContactStore (contacts, import)
│   ├── useTaskStore (tasks, kanban state)
│   ├── useCalendarStore (events, views)
│   ├── useChatStore (messages, channels)
│   └── ... (per module)
└── Service Stores
    ├── useAiStore (AI chat, suggestions)
    ├── useBillingStore (plans, subscription)
    └── useWebSocketStore (Pusher connection)
```

### 3.3 Build & Dev

| Tool | Package | Versione |
|------|---------|----------|
| Bundler | Vite | 7.2.4 |
| CSS | Tailwind CSS | 4.1.18 |
| Testing | Vitest | 4.0.18 |
| E2E Testing | Playwright | 1.58.1 |
| TypeScript | typescript | 5.9.3 |
| Linting | ESLint | 9.39.2 |

---

## 4. Database Architecture

### 4.1 Overview

| Metrica | Valore |
|---------|--------|
| DBMS | MariaDB (MySQL-compatible) |
| Total tables | 176 |
| Migrations | 11 files (auth/_migrations/) |
| Connection | PDO with prepared statements |

### 4.2 Table Categories (176 tables)

| Categoria | Tabelle stimate | Esempi |
|-----------|----------------|--------|
| Users & Auth | ~15 | users, sessions, tokens, oauth, 2fa |
| Projects & Teams | ~10 | projects, teams, members, permissions |
| CRM | ~12 | deals, pipelines, stages, activities |
| Invoicing | ~15 | invoices, invoice_items, payments, templates |
| E-Invoicing | ~8 | einvoices, einvoice_logs, einvoice_config |
| Accounting | ~12 | accounts, journal_entries, fiscal_years |
| Expenses | ~8 | expenses, expense_categories, receipts |
| Banking | ~10 | bank_accounts, transactions, reconciliations |
| Communication | ~10 | emails, campaigns, chat_messages, sms |
| Tasks & Calendar | ~8 | tasks, events, time_entries |
| Files | ~5 | files, file_versions |
| Settings & Config | ~15 | settings, feature_flags, plans, addons |
| Webhooks & API | ~8 | webhooks, api_keys, webhook_logs |
| Activity & Audit | ~8 | activity_logs, audit_trail |
| Site Builder | ~6 | sites, pages, templates |
| Analytics | ~5 | metrics, reports |
| Platform | ~20+ | billing, subscriptions, notifications, etc. |

---

## 5. Infrastructure Architecture

### 5.1 Current Deployment

```
Hostinger Shared Hosting
├── Domain: kacinka.app (marketing)
├── Subdomain: cloud.kacinka.app (frontend SPA)
├── Subdomain: api.kacinka.app (backend API)
├── Subdomain: master.kacinka.app (admin panel)
├── SSH: 82.198.229.237:65002
├── MariaDB: local instance
├── PHP: 8.1
├── Storage: Shared disk + Backblaze B2 (backup)
└── SSL: Let's Encrypt auto-renewal
```

### 5.2 Target Architecture (Post-Funding)

```
Cloud Provider (AWS / GCP)
├── Load Balancer (ALB)
├── Application Servers (EC2/GKE)
│   ├── PHP-FPM (API)
│   └── Node.js (frontend SSR, optional)
├── Database
│   ├── RDS MariaDB (primary)
│   └── Read replica (reporting)
├── Cache
│   ├── Redis (sessions, rate limiting)
│   └── Memcached (query cache)
├── Storage
│   ├── S3 (files, invoices)
│   └── CloudFront CDN (static assets)
├── Monitoring
│   ├── Sentry (errors)
│   ├── CloudWatch (infra)
│   └── Custom dashboard (business metrics)
└── CI/CD
    ├── GitHub Actions
    └── Staging + Production environments
```

---

## 6. Security Architecture

### 6.1 Authentication Flow

```
Login Request
  ↓
AuthMiddleware → Validate JWT
  ↓
JwtService → Decode token, check expiry
  ↓
SessionService → Validate session exists
  ↓
TwoFactorService → Check 2FA if enabled
  ↓
PermissionMiddleware → Check role/permissions
  ↓
PlanLimitsMiddleware → Enforce plan limits
  ↓
EntitlementMiddleware → Check module access
  ↓
Action → Process request
```

### 6.2 Security Layers

| Layer | Implementation | LOC |
|-------|---------------|-----|
| JWT Authentication | firebase/php-jwt | ~200 (JwtService) |
| 2FA (TOTP) | TwoFactorService | ~300 |
| Rate Limiting | RateLimitMiddleware | 245 |
| CORS | CorsMiddleware | 110 |
| CSRF | CsrfMiddleware | 158 |
| Security Headers | SecurityHeadersMiddleware | 92 |
| XSS Protection | DOMPurify (frontend) | — |
| SQL Injection | PDO prepared statements | Global |
| Input Validation | Zod (frontend) + PHP validation | Global |
| API Key Auth | ApiKeyAuthMiddleware | 372 |
| File Validation | FileValidationService | ~200 |
| Permission RBAC | PermissionService + middleware | ~300 |

---

## 7. Integration Architecture

### 7.1 External Service Map

```
Kacinka API
├── AI / ML
│   └── Google Gemini API (Flash + Flash-Lite)
├── Payments
│   └── Stripe (subscriptions, billing, webhooks)
├── Banking
│   └── Tink (Open Banking, PSD2)
├── Communication
│   ├── PHPMailer (SMTP email)
│   ├── Pusher (WebSocket real-time)
│   ├── Telegram (notifications bot)
│   └── SmsCustomService (SMS gateway)
├── Google Suite
│   ├── Google Calendar (sync)
│   ├── Google Contacts (sync)
│   ├── Google Drive (file storage)
│   └── Google Sheets (export)
├── E-Invoicing Gateways
│   ├── OpenAPI SDI (Italy)
│   ├── OpenAPI eSign (digital signatures)
│   └── OpenAPI Company (business registry)
├── Monitoring
│   └── Sentry (PHP + React)
├── Storage
│   └── Backblaze B2 (backup)
└── Domain
    └── DomainRegistrarService (custom domains)
```

---

## 8. LOC Distribution

### 8.1 By Layer

| Layer | LOC | % |
|-------|-----|---|
| Backend Services | 67,536 | 13.0% |
| Backend Actions | 51,141 | 9.8% |
| Backend Middleware | 3,370 | 0.6% |
| Backend Other (routes, config, migrations) | ~5,000 | 1.0% |
| Engine (Marketing Site) | 204,199 | 39.3% |
| Frontend App | ~141,584 | 27.2% |
| E-Invoice Builders | 4,552 | 0.9% |
| Backend Total (excl. engine) | ~131,599 | 25.3% |
| CSS | 1,716 | 0.3% |
| **TOTAL** | **~519,535** | **100%** |

---

*Documento generato il 2026-03-07. Architettura derivata dall'analisi diretta del codice sorgente (519,535 LOC, 176 tabelle, 1,059 routes, 21 middleware, 120 services).*
