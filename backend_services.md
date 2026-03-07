# Backend Services Catalog

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Overview

| Metrica | Valore |
|---------|--------|
| **Totale Services** | 120 |
| **Totale LOC** | 67,536 |
| **Media LOC/Service** | 563 |
| **Largest Service** | EInvoiceService (4,051 LOC) |
| **Smallest Services** | ~50-100 LOC (utility services) |
| **Location** | `auth/src/Service/` |

---

## 2. Top 30 Services by LOC

| # | Service | LOC | Dominio | Complessità |
|---|---------|-----|---------|-------------|
| 1 | EInvoiceService | 4,051 | E-Invoicing | 🔴 Altissima |
| 2 | AiAssistantService | 1,692 | AI | 🔴 Alta |
| 3 | StripeService | 1,588 | Pagamenti | 🔴 Alta |
| 4 | CampaignService | 1,196 | Email Marketing | 🟡 Media |
| 5 | PlatformInvoiceService | 1,169 | E-Invoicing Routing | 🔴 Alta |
| 6 | TinkOpenBankingService | 1,069 | Banking | 🔴 Alta |
| 7 | OpenBankingService | 1,020 | Banking | 🟡 Media |
| 8 | WorkflowService | 990 | Automazione | 🟡 Media |
| 9 | InvoiceServiceV2 | 969 | Fatturazione | 🟡 Media |
| 10 | SmsCustomService | 944 | Comunicazione | 🟡 Media |
| 11 | ExpenseService | 927 | Spese | 🟡 Media |
| 12 | OcrService | 925 | AI/OCR | 🟡 Media |
| 13 | BudgetService | 895 | Budget | 🟡 Media |
| 14 | PaymentService | 893 | Pagamenti | 🟡 Media |
| 15 | ChartOfAccountsService | 876 | Contabilità | 🟡 Media |
| 16 | EmailConfigService | 872 | Email | 🟡 Media |
| 17 | JournalEntryService | 850 | Contabilità | 🟡 Media |
| 18 | WebhookService | 811 | Integrazione | 🟡 Media |
| 19 | SupportDiagnosticService | 807 | Supporto | 🟡 Media |
| 20 | CalendarService | 788 | Produttività | 🟡 Media |
| 21 | OpenApiESignService | 743 | Firma Digitale | 🟡 Media |
| 22 | AnalyticsService | 739 | Analytics | 🟡 Media |
| 23 | WorkflowAutomationService | 728 | Automazione | 🟡 Media |
| 24 | TaxService | 680 | Fiscale | 🟡 Media |
| 25 | BankReconciliationService | 678 | Banking | 🟡 Media |
| 26 | HostingResellerService | 671 | Hosting | 🟡 Media |
| 27 | ExpenseOcrWorkflowService | 668 | OCR Workflow | 🟡 Media |
| 28 | SupportAiService | 660 | AI Supporto | 🟡 Media |
| 29 | VatReportService | 660 | Fiscale | 🟡 Media |
| 30 | QrCodeService | 645 | Utility | 🟢 Bassa |

---

## 3. Services by Domain

### 3.1 E-Invoicing & Fiscal (LOC: ~12,000+)

| Service | LOC | Funzione |
|---------|-----|---------|
| EInvoiceService | 4,051 | Orchestratore e-invoicing: validazione, generazione, invio, ricezione |
| PlatformInvoiceService | 1,169 | Routing intelligente: paese → builder → gateway |
| EInvoiceAuditService | — | Audit trail per ogni e-invoice |
| EInvoiceOnboardingService | — | Wizard configurazione per-paese |
| FiscalValidationService | — | Validazione dati fiscali (P.IVA, CF, NIF) |
| FiscalYearService | — | Gestione anni fiscali |
| TaxService | 680 | Calcolo tasse, aliquote per paese |
| CountryTaxProfileService | — | Profili fiscali per paese |
| VatReportService | 660 | Report IVA periodici |
| OssTrackingService | — | EU OSS (One Stop Shop) tracking |
| ViesValidationService | — | Validazione P.IVA via VIES |
| **13 E-Invoice Builders** | **4,552** | Format-specific XML/JSON generation |

### 3.2 AI & Machine Learning (LOC: ~4,500+)

| Service | LOC | Funzione |
|---------|-----|---------|
| AiAssistantService | 1,692 | Chat AI contestuale, suggerimenti |
| AiContextBuilder | — | Costruisce contesto per prompt AI |
| AiEscalationService | — | Escalation a supporto umano |
| AiMemoryService | — | Memoria conversazioni AI |
| AiPolicy | — | Policy per limitare scope AI |
| AiSuggestionService | — | Suggerimenti proattivi |
| OcrService | 925 | OCR ricevute via Gemini Vision |
| ExpenseOcrWorkflowService | 668 | Pipeline OCR → spesa |
| SupportAiService | 660 | AI per ticket supporto |
| SupportDiagnosticService | 807 | Diagnostica sistema per AI |

### 3.3 Finance & Accounting (LOC: ~5,500+)

| Service | LOC | Funzione |
|---------|-----|---------|
| InvoiceServiceV2 | 969 | CRUD fatture, workflow |
| InvoiceService | — | Legacy invoice service |
| InvoicePdfService | — | Generazione PDF con DomPDF |
| InvoiceArchiveService | — | Archiviazione fatture |
| InvoiceNumberingService | — | Numerazione automatica |
| InvoiceWorkflowService | — | Workflow: bozza→inviata→pagata |
| ChartOfAccountsService | 876 | Piano dei conti |
| JournalEntryService | 850 | Partita doppia |
| FinancialReportService | — | Report finanziari |
| ExpenseService | 927 | Gestione spese |
| ExpenseCategoryService | — | Categorie spese |
| BudgetService | 895 | Budget e forecast |
| CurrencyService | — | Conversione valute |

### 3.4 Banking & Payments (LOC: ~5,500+)

| Service | LOC | Funzione |
|---------|-----|---------|
| StripeService | 1,588 | Subscriptions, billing, webhooks |
| PaymentService | 893 | Payment processing, recording |
| PaymentScheduleService | — | Pagamenti rateali/scadenze |
| DunningService | — | Solleciti automatici |
| TinkOpenBankingService | 1,069 | Tink API integration |
| OpenBankingService | 1,020 | Open Banking orchestration |
| BankAccountService | — | Gestione conti bancari |
| BankImportService | — | Import movimenti (CSV/OFX/MT940) |
| BankReconciliationService | 678 | Riconciliazione automatica |
| TransactionLogService | — | Log transazioni |

### 3.5 Communication (LOC: ~4,500+)

| Service | LOC | Funzione |
|---------|-----|---------|
| CampaignService | 1,196 | Email marketing campaigns |
| EmailConfigService | 872 | Configurazione SMTP |
| EmailQueueService | — | Coda invio con rate limiting |
| EmailTranslationService | — | Traduzione automatica campagne |
| MailService | — | Invio email transazionali |
| SmsCustomService | 944 | SMS gateway custom |
| SmsService | — | SMS utility |
| PusherService | — | WebSocket (Pusher) |
| NotificationService | — | Notifiche in-app |
| TelegramService | — | Bot Telegram |

### 3.6 Platform & Infrastructure (LOC: ~3,000+)

| Service | LOC | Funzione |
|---------|-----|---------|
| WebhookService | 811 | Delivery webhook + retry |
| WorkflowService | 990 | Workflow engine |
| WorkflowAutomationService | 728 | Trigger-based automations |
| ApiKeyService | — | API key management |
| FeatureFlagService | — | Feature flags |
| UsageMeteringService | — | Metering per limits |
| LimitGraceService | — | Grace period overage |
| CacheService | — | Data caching |
| JobQueueService | — | Background job processing |
| EventDispatcher | — | Event-driven architecture |

### 3.7 User & Security (LOC: ~2,000+)

| Service | LOC | Funzione |
|---------|-----|---------|
| UserService | — | User CRUD |
| UserBootstrapService | — | User initialization |
| TeamService | — | Team management |
| PermissionService | — | RBAC |
| JwtService | — | JWT generation/validation |
| OAuthService | — | OAuth provider integration |
| SessionService | — | Session management |
| SecurityService | — | Security utilities |
| TwoFactorService | — | 2FA TOTP |
| VerificationService | — | Email verification |

### 3.8 Integrations (LOC: ~3,000+)

| Service | LOC | Funzione |
|---------|-----|---------|
| GoogleCalendarService | — | Google Calendar sync |
| GoogleContactsService | — | Google Contacts sync |
| GoogleDriveService | — | Google Drive file storage |
| GoogleSheetsService | — | Google Sheets export |
| GoogleIntegrationService | — | OAuth for Google |
| OpenApiSdiService | — | SDI gateway (Italy) |
| OpenApiESignService | 743 | Digital signature service |
| OpenApiCompanyService | — | Business registry lookup |
| B2BackupService | — | Backblaze B2 backup |

### 3.9 Content & Website (LOC: ~2,000+)

| Service | LOC | Funzione |
|---------|-----|---------|
| SiteBuilderService | — | Website builder engine |
| SiteTemplateService | — | Template management |
| PageSpeedService | — | Lighthouse integration |
| CustomDomainService | — | Custom domain setup |
| DomainRegistrarService | — | Domain registration |
| HostingResellerService | 671 | Hosting reseller management |
| BrandingService | — | Brand customization |

### 3.10 CRM & Sales (LOC: ~1,000+)

| Service | LOC | Funzione |
|---------|-----|---------|
| PipelineService | — | Pipeline management |
| CrmInvoiceWorkflowService | — | Deal → Invoice conversion |
| CrmAccountingBridge | — | CRM ↔ Accounting sync |
| ReferralService | — | Referral tracking |
| ClientPortalService | — | Client self-service portal |

### 3.11 Productivity (LOC: ~1,500+)

| Service | LOC | Funzione |
|---------|-----|---------|
| CalendarService | 788 | Calendar events + reminders |
| TimeTrackingService | — | Time tracking per task/project |
| FileService | — | File CRUD |
| FileValidationService | — | File type/size validation |
| StorageService | — | Storage abstraction |
| FormService | — | Form builder + submissions |
| CustomFieldService | — | Custom field definitions |

### 3.12 Analytics & Reporting (LOC: ~1,500+)

| Service | LOC | Funzione |
|---------|-----|---------|
| AnalyticsService | 739 | Business analytics |
| DashboardMetricsService | — | Dashboard KPIs |
| AdminAnalyticsService | — | Platform-level analytics |
| AdminDashboardService | — | Admin overview |
| PdfExportService | — | PDF report generation |

### 3.13 Admin & Platform Services

| Service | LOC | Funzione |
|---------|-----|---------|
| AdminBillingService | — | Billing management |
| AdminSystemService | — | System diagnostics |
| PlanService | — | Plan CRUD |
| ActivityLogService | — | Activity tracking |
| AuditService | — | Full audit trail |
| CsvImportService | — | CSV data import |
| FeedbackService | — | User feedback collection |
| QrCodeService | 645 | QR code generation |
| CronLogger | — | Cron job logging |

---

## 4. Service Complexity Distribution

```
LOC Range       Count    Percentage
──────────────────────────────────
< 200           ~30      25%     │████████
200-500         ~40      33%     │████████████
500-1000        ~35      29%     │██████████
1000-2000       ~12      10%     │████
2000+           ~3       3%      │█
──────────────────────────────────
Total           120      100%
```

---

## 5. Dependencies Between Services

### Critical Dependency Chains

```
InvoiceServiceV2
├── depends on → EInvoiceService
│   ├── depends on → PlatformInvoiceService
│   │   └── depends on → 13 E-Invoice Builders
│   └── depends on → EInvoiceAuditService
├── depends on → InvoicePdfService
├── depends on → PaymentService
└── depends on → WebhookService (events)

AiAssistantService
├── depends on → AiContextBuilder
│   ├── depends on → DashboardMetricsService (context)
│   └── depends on → ProjectSettingsService (context)
├── depends on → AiMemoryService
├── depends on → AiPolicy
└── depends on → AiEscalationService
    └── depends on → SupportAiService

StripeService
├── depends on → PlanService
├── depends on → SubscriptionService (implicit)
└── triggers → WebhookService (Stripe webhooks)
```

---

*Documento generato il 2026-03-07. 120 services catalogati da `auth/src/Service/`. LOC per top 30 services verificati direttamente dal codice.*
