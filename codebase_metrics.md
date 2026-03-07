# Codebase Metrics

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Executive Summary

| Metrica | Valore |
|---------|--------|
| **Total LOC (excl. vendor/node_modules)** | ~519,535 |
| **PHP LOC** | ~376,235 |
| **JS/JSX/TS/TSX LOC** | ~141,584 |
| **CSS LOC** | ~1,716 |
| **Total Files (source code)** | ~3,841 |
| **Test Files** | 577 (198 PHP + 379 JS) |
| **i18n Data** | 9.8 MB (20 lingue × 62 namespace) |
| **Database Tables** | 176 (166 base + 10 incremental) |
| **API Routes** | 1,059 |
| **Middleware Components** | 21 (3,370 LOC total) |

---

## 2. Code Distribution by Language

```
PHP      ████████████████████████████████████████████████  72.4%  376,235 LOC
JS/JSX   ██████████████████                               27.2%  141,584 LOC
CSS      ░                                                 0.3%    1,716 LOC
```

---

## 3. PHP Backend — Detailed Breakdown

### 3.1 Layer Distribution

| Layer | Files | LOC | Avg LOC/File |
|-------|-------|-----|-------------|
| **Action** | 677 | 44,983 | 66 |
| **Service** | 137 | 59,747 | 436 |
| **Middleware** | 21 | 2,954 | 141 |
| **App Config** | 4 | 2,626 | 657 |
| **_engine (landing/blog)** | 2,320 | 199,249 | 86 |
| **_build (scripts)** | 9 | 3,169 | 352 |
| **Deploy Scripts** | 18 | 3,205 | 178 |
| **Migrations** | 90+ | ~15,000 (est.) | ~165 |
| **Tests** | 198 | ~25,000 (est.) | ~126 |

### 3.2 Action Directories — Top 15

| Domain | Files | LOC | Note |
|--------|-------|-----|------|
| Admin | 46 | 5,802 | Admin panel, system management |
| SiteBuilder | 14 | 1,898 | Website builder |
| Ai | 9 | 1,613 | Chat, suggestions, NLP |
| EInvoice | 30 | 1,512 | 13 builders, XML generation |
| Task | 24 | 1,527 | Task management, boards |
| Chat | 16 | 1,495 | Real-time messaging |
| Tax | 23 | 1,218 | VAT, withholding, fiscal |
| Contact | 15 | 1,212 | CRM contacts |
| Subscription | 17 | 1,212 | Billing, plans |
| InvoiceV2 | 33 | 1,188 | Invoice CRUD |
| Dashboard | 14 | 1,028 | KPI widgets |
| Auth | 11 | 988 | Login, register, OAuth |
| Deal | 11 | 838 | CRM pipeline deals |
| Webhook | 12 | 820 | Stripe, Telegram, etc. |
| Report | 13 | 799 | Financial reports |

### 3.3 Largest Services

| Service | LOC | Dominio |
|---------|-----|---------|
| EInvoiceService | 3,646 | E-invoicing (13 formats) |
| AiAssistantService | 1,523 | AI chat, prompts, context |
| StripeService | 1,420 | Payments, subscriptions |
| CampaignService | 1,048 | Email marketing |
| PlatformInvoiceService | 1,044 | Platform-level invoicing |
| TinkOpenBankingService | 937 | Open banking (Tink) |
| WorkflowService | 890 | Automation engine |
| OpenBankingService | 885 | Banking abstraction |
| InvoiceServiceV2 | 864 | Invoice business logic |
| ExpenseService | 837 | Expense management |
| SmsCustomService | 833 | SMS campaigns |
| OcrService | 814 | Receipt OCR (multi-provider) |
| BudgetService | 802 | Budget tracking |
| PaymentService | 798 | Payment processing |
| EmailConfigService | 787 | Email SMTP config |

### 3.4 Complexity Analysis

| Fascia LOC | Count | % | Risk |
|-----------|-------|---|------|
| < 100 LOC | 540 | 65% | ✅ Low |
| 100-300 LOC | 200 | 24% | ✅ Low |
| 300-500 LOC | 50 | 6% | ⚠️ Medium |
| 500-1000 LOC | 30 | 4% | 🟡 Monitor |
| > 1000 LOC | 7 | 1% | 🔴 Consider refactor |

> **God class candidates (>1000 LOC)**: EInvoiceService (3,646), AiAssistantService (1,523), StripeService (1,420), CampaignService (1,048), PlatformInvoiceService (1,044), TinkOpenBankingService (937), WorkflowService (890). Tuttavia, la complessità è spesso giustificata dalla domain logic (e.g. EInvoiceService gestisce 13 format nazionali diversi).

---

## 4. Frontend — Detailed Breakdown

### 4.1 App Distribution

| Area | Files | LOC |
|------|-------|-----|
| **app/src** (Cloud App) | 435 | 88,518 |
| **admin/src** (Admin Panel) | 57 | 11,269 |
| **Total Frontend** | 492 | 99,787 |

### 4.2 Largest React Components

| Component | LOC | Modulo |
|-----------|-----|--------|
| SettingsPage.jsx | 2,032 | Account settings |
| ConfigTab.jsx | 1,832 | E-invoice configuration |
| ProjectSettingsPage.jsx | 1,293 | Project settings |
| WorkspaceShell.jsx | 1,097 | Main layout shell |
| PortfolioPage.jsx | 922 | Portfolio/clients |
| ComplianceTab.jsx | 912 | Compliance dashboard |
| ContactsShell.jsx | 874 | CRM contacts view |
| SplitEditor.jsx | 852 | Side-by-side editor |
| TaskDetail.jsx | 836 | Task detail panel |
| ProjectDashboardPage.jsx | 811 | Project main dashboard |

### 4.3 State Management

- **33 Zustand stores** (~7,173 LOC combined)
- No Redux, no Context API abuse
- Separate stores per domain module

### 4.4 Frontend Architecture

| Layer | Conteggio | Note |
|-------|----------|------|
| Pages | ~50 | Route-level components |
| Components | ~250 | Reusable UI |
| Hooks | ~40 | Custom React hooks |
| Stores | 33 | Zustand state stores |
| Utils | ~30 | Helper functions |
| i18n namespaces | 62 | Per-module translation files |

---

## 5. Infrastructure Code

### 5.1 Build System

| Categoria | Files | LOC |
|-----------|-------|-----|
| **_build/ scripts** | 70+ | ~3,169+ PHP, ~5,000+ JS |
| **Deploy scripts** | 18 | 3,205 |
| **CI/CD (GitHub Actions)** | 1 | 366 |
| **Vite config** | 2 | ~200 |

### 5.2 Migration & Schema

| Categoria | Files | LOC (est.) |
|-----------|-------|-----------|
| Base schema | 1 | ~8,000 |
| Incremental migrations | 90+ | ~7,000 |
| SQL scripts | 92 total | — |

---

## 6. Internationalization

| Metrica | Valore |
|---------|--------|
| Lingue | 20 |
| Namespace per lingua | 62 |
| File di traduzione | ~1,240 |
| Dimensione totale | 9.8 MB |
| Chiavi stimate per lingua | ~5,000-6,000 |
| Chiavi totali stimate | ~100,000-120,000 |

### Lingue supportate
`ar`, `cs`, `da`, `de`, `el`, `en`, `es`, `fi`, `fr`, `hi`, `it`, `ja`, `ko`, `nl`, `pl`, `pt`, `ro`, `sv`, `tr`, `zh`

---

## 7. API Surface

| Metrica | Valore |
|---------|--------|
| Total routes | 1,059 |
| GET | 502 (47%) |
| POST | 348 (33%) |
| PUT | 99 (9%) |
| DELETE | 107 (10%) |
| PATCH | 3 (0.3%) |
| Action directories | 82 |
| Routes.php LOC | 1,460 |

---

## 8. Database

| Metrica | Valore |
|---------|--------|
| Tables (base schema) | 166 |
| Tables (incremental) | ~10 |
| Tables (total live) | ~176 |
| Domains | 16 |
| Views | 2 |
| Migrations | 90+ |

---

## 9. Code Quality Indicators

| Indicatore | Valore | Benchmark |
|-----------|--------|-----------|
| Avg LOC per Action | 66 | ✅ Excellent (< 100 ideal) |
| Avg LOC per Service | 436 | ⚠️ Medium (< 300 ideal) |
| Avg LOC per Middleware | 141 | ✅ Good |
| Services > 1000 LOC | 7 | 🟡 Monitor |
| Components > 1000 LOC | 4 | 🟡 Monitor |
| Test files / Source files ratio | 577 / 3,841 = 0.15 | ⚠️ Below 0.3 target |
| Test LOC / Source LOC ratio | ~25K / ~519K = 0.048 | ⚠️ Below 0.1 target |

---

## 10. Trend & Growth Projection

### LOC Growth (if linear)

```
Current (M0):   519,535 LOC
M6 projection:  ~600,000 LOC  (+15%)
M12 projection: ~700,000 LOC  (+35%)
```

### Recommendations

1. **Extract sub-services** from god classes (EInvoiceService → per-country handlers already partially done as Builders)
2. **Split large React components** (SettingsPage 2,032 LOC → tab-level components)
3. **Increase test ratio** from 0.15 to 0.3 (target: ~1,150 test files)
4. **Code coverage floor**: enforce 70% in CI/CD pipeline
5. **Dead code analysis**: identify unused Action classes and remove

---

*Documento generato il 2026-03-07. Metriche calcolate tramite analisi statica del codebase (escludendo vendor/, node_modules/, .git/).*
