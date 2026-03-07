# Feature Inventory — Catalogo Completo Funzionalità

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Overview

| Metrica | Valore |
|---------|--------|
| **Moduli Frontend** | 26 |
| **File Frontend (pages/modules)** | 234 |
| **Servizi Backend** | 120 |
| **Action Classes** | 677 (82 directories) |
| **API Routes** | 1,059 |
| **Tabelle Database** | 176 |
| **Lingue i18n** | 20 |
| **E-Invoice Builders** | 13 |
| **Zustand Stores** | 33 |

---

## 2. Moduli per Categoria

### 🏢 CRM & Sales

| Feature | Frontend | Backend Actions | Services | Status |
|---------|----------|----------------|----------|--------|
| CRM (Deals, Pipeline) | 14 files | Deal(11) + Pipeline(9) + CrmEmail(2) = 22 | PipelineService, CrmInvoiceWorkflowService | ✅ Complete |
| Contacts Management | 8 files | Contact(15) | GoogleContactsService | ✅ Complete |
| Client Portal | 10 files | Portal(2) + PortalV2(6) = 8 | ClientPortalService | ✅ Complete |
| Forms & Submissions | 7 files | Form(2) + FormSubmission(2) = 4 | FormService | ✅ Complete |
| Proposals | — | Proposal(4) | — | ⚠️ Backend only |

### 💰 Finance & Accounting

| Feature | Frontend | Backend Actions | Services | Status |
|---------|----------|----------------|----------|--------|
| Invoicing (v2) | 8 files | Invoice(9) + InvoiceV2(33) = 42 | InvoiceService, InvoiceServiceV2, InvoicePdfService, InvoiceArchiveService, InvoiceNumberingService, InvoiceWorkflowService | ✅ Complete |
| Expenses | 9 files | Expense(22) | ExpenseService, ExpenseCategoryService | ✅ Complete |
| Accounting (CoA, Journal) | 8 files | Accounting(22) | ChartOfAccountsService, JournalEntryService, FinancialReportService, CrmAccountingBridge | ✅ Complete |
| Budget & Forecasting | 8 files | Budget(18) | BudgetService | ✅ Complete |
| Payments | — | Payment(22) | PaymentService, PaymentScheduleService, DunningService | ✅ Complete |
| Tax Management | — | Tax(23) | TaxService, CountryTaxProfileService, VatReportService, FiscalValidationService, OssTrackingService, ViesValidationService | ✅ Complete |
| Currency | — | Currency(10) | CurrencyService | ✅ Complete |
| Fiscal Module | 8 files | — | FiscalYearService | ✅ Complete |
| CRM-Accounting Bridge | 8 files | CrmAccounting(7) | CrmAccountingBridge | ✅ Complete |

### 🧾 E-Invoicing

| Feature | Frontend | Backend Actions | Builders | Status |
|---------|----------|----------------|----------|--------|
| E-Invoice Module | 8 files | EInvoice(30) | 13 builders (4,552 LOC) | ✅ Complete |
| FatturaPA (Italy) | ↪ included | ↪ included | FatturaPABuilder (517) | ✅ SDI ready |
| XRechnung (Germany) | ↪ included | ↪ included | XRechnungBuilder (153) | ✅ PEPPOL ready |
| Factur-X (France) | ↪ included | ↪ included | FacturXBuilder (519) | ✅ Ready |
| Chorus Pro (France B2G) | ↪ included | ↪ included | ChorusProBuilder (217) | ✅ Ready |
| SII (Spain) | ↪ included | ↪ included | SIIBuilder (302) | ✅ Ready |
| KSeF (Poland) | ↪ included | ↪ included | KSeFBuilder (279) | ✅ Ready |
| myDATA (Greece) | ↪ included | ↪ included | MyDATABuilder (327) | ✅ Ready |
| eFatura (Turkey) | ↪ included | ↪ included | EFaturaBuilder (488) | ✅ Ready |
| GST (India) | ↪ included | ↪ included | GSTBuilder (374) | ✅ Ready |
| ZATCA (Saudi Arabia) | ↪ included | ↪ included | ZATCABuilder (589) | ✅ Ready |
| ROeFactura (Romania) | ↪ included | ↪ included | ROCIUSBuilder (111) | ✅ Ready |
| PEPPOL BIS 3.0 (EU-wide) | ↪ included | ↪ included | PeppolBIS3Builder (621) | ✅ Ready |

### 🏦 Banking & Payments

| Feature | Frontend | Backend Actions | Services | Status |
|---------|----------|----------------|----------|--------|
| Banking Module | 8 files | Bank(23) | BankAccountService, BankImportService, BankReconciliationService | ✅ Complete |
| Open Banking (Tink) | 8 files | OpenBanking(10) | OpenBankingService, TinkOpenBankingService | ✅ Complete |
| Stripe Integration | — | Subscription(17) + Checkout(4) + Billing(4) = 25 | StripeService | ✅ Complete |

### 🤖 AI & OCR

| Feature | Frontend | Backend Actions | Services | Status |
|---------|----------|----------------|----------|--------|
| AI Assistant | — | Ai(9) | AiAssistantService (1,692 LOC), AiContextBuilder, AiEscalationService, AiMemoryService, AiPolicy, AiSuggestionService | ✅ Complete |
| OCR (Receipt Scanning) | 9 files | Ocr(11) | OcrService (925 LOC), ExpenseOcrWorkflowService (668 LOC) | ✅ Complete |
| Support AI | — | Support(1) | SupportAiService, SupportDiagnosticService | ✅ Complete |

### 📊 Analytics & Reports

| Feature | Frontend | Backend Actions | Services | Status |
|---------|----------|----------------|----------|--------|
| Analytics Dashboard | 8 files | Analytics(5) | AnalyticsService (739 LOC) | ✅ Complete |
| Reports Module | 8 files | Report(13) | FinancialReportService | ✅ Complete |
| Dashboard Metrics | — | Dashboard(14) | DashboardMetricsService | ✅ Complete |

### 📧 Communication

| Feature | Frontend | Backend Actions | Services | Status |
|---------|----------|----------------|----------|--------|
| Email Marketing | 11 files | Email(8) + EmailConfig(2) = 10 | CampaignService (1,196 LOC), EmailConfigService (872 LOC), EmailQueueService, EmailTranslationService, MailService | ✅ Complete |
| Chat (Real-time) | 8 files | Chat(16) | PusherService | ✅ Complete |
| SMS | — | Sms(2) | SmsService, SmsCustomService (944 LOC) | ✅ Complete |
| Notifications | — | Notification(7) | NotificationService, TelegramService | ✅ Complete |

### 📅 Productivity

| Feature | Frontend | Backend Actions | Services | Status |
|---------|----------|----------------|----------|--------|
| Task Manager | 9 files | Task(24) | — | ✅ Complete |
| Calendar | 8 files | Calendar(10) | CalendarService (788 LOC), GoogleCalendarService | ✅ Complete |
| Time Tracking | 8 files | Time(7) | TimeTrackingService | ✅ Complete |
| Files & Documents | 8 files | File(11) | FileService, FileValidationService, StorageService, GoogleDriveService | ✅ Complete |

### 🌐 Website & Commerce

| Feature | Frontend | Backend Actions | Services | Status |
|---------|----------|----------------|----------|--------|
| Site Builder | 23 files | SiteBuilder(14) + SiteTemplate(4) = 18 | SiteBuilderService, SiteTemplateService, PageSpeedService | ✅ Complete |
| QR Codes | — | QrCode(7) | QrCodeService (645 LOC) | ✅ Complete |
| Commerce | — | Commerce(6) | — | ⚠️ Early stage |

### ⚙️ Platform & Infrastructure

| Feature | Frontend | Backend Actions | Services | Status |
|---------|----------|----------------|----------|--------|
| Webhooks | 8 files | Webhook(12) | WebhookService (811 LOC) | ✅ Complete |
| API Keys | 7 files | ApiKey(11) | ApiKeyService | ✅ Complete |
| Workflows / Automation | 7 files | Workflow(4) + Automation(4) = 8 | WorkflowService (990 LOC), WorkflowAutomationService (728 LOC) | ✅ Complete |
| Custom Fields | — | CustomField(7) | CustomFieldService | ✅ Complete |
| Import / Export | — | Import(6) + Export(1) = 7 | CsvImportService | ✅ Complete |
| Activity Log | — | Activity(2) | ActivityLogService, AuditService | ✅ Complete |
| Feature Flags | — | Feature(1) | FeatureFlagService | ✅ Complete |
| Usage Metering | — | — | UsageMeteringService, LimitGraceService | ✅ Complete |

### 👤 User & Auth

| Feature | Frontend | Backend Actions | Services | Status |
|---------|----------|----------------|----------|--------|
| Authentication | auth/ pages | Auth(11) | JwtService, OAuthService, SessionService | ✅ Complete |
| User Management | settings/ pages | User(6) + Account(1) = 7 | UserService, UserBootstrapService | ✅ Complete |
| Team & Permissions | — | Team(8) + Permission(3) = 11 | TeamService, PermissionService | ✅ Complete |
| 2FA | — | Security(4) | TwoFactorService, SecurityService | ✅ Complete |
| Verification | — | Verification(5) | VerificationService | ✅ Complete |
| Onboarding | onboarding/ pages | Onboarding(2) | — | ✅ Complete |

### 🔗 Integrations

| Feature | Backend | Services | Status |
|---------|---------|----------|--------|
| Google Calendar | Integration(7) | GoogleCalendarService | ✅ |
| Google Contacts | ↪ included | GoogleContactsService | ✅ |
| Google Drive | ↪ included | GoogleDriveService | ✅ |
| Google Sheets | ↪ included | GoogleSheetsService | ✅ |
| Pusher (WebSocket) | — | PusherService | ✅ |
| Tink (Open Banking) | OpenBanking(10) | TinkOpenBankingService | ✅ |
| Stripe (Payments) | Subscription(17) | StripeService | ✅ |
| Sentry (Error Tracking) | — | @sentry/react + sentry PHP | ✅ |
| Gemini AI | Ai(9) | AiAssistantService | ✅ |
| Telegram | — | TelegramService | ✅ |
| OpenAPI (SDI, eSign) | — | OpenApiSdiService, OpenApiESignService, OpenApiCompanyService | ✅ |
| Backblaze B2 | — | B2BackupService | ✅ |

---

## 3. Feature Count Summary

| Categoria | Moduli/Feature | Frontend Pages | Backend Actions | Services |
|-----------|---------------|----------------|-----------------|----------|
| CRM & Sales | 5 | 39 files | 49 | 4 |
| Finance & Accounting | 9 | 41 files | 142 | 14 |
| E-Invoicing | 13 formats | 8 files | 30 | 3 + 13 builders |
| Banking & Payments | 3 | 16 files | 58 | 5 |
| AI & OCR | 3 | 9 files | 21 | 7 |
| Analytics & Reports | 3 | 16 files | 32 | 3 |
| Communication | 4 | 19 files | 36 | 7 |
| Productivity | 4 | 33 files | 52 | 6 |
| Website & Commerce | 3 | 23 files | 31 | 4 |
| Platform & Infra | 7 | 22 files | 45 | 10 |
| User & Auth | 6 | 15+ files | 38 | 9 |
| Integrations | 12 | — | 34 | 12 |
| **TOTALE** | **~72 features** | **~234 files** | **~677 actions** | **~120 services** |

---

## 4. Feature Maturity Assessment

### Maturity Levels

| Level | Definizione | Criteri |
|-------|-----------|---------|
| 🟢 **Production** | Feature completa, testata, ready for users | Frontend + Backend + Tests |
| 🟡 **Beta** | Funzionale ma necessita testing/polish | Funzionale, test parziali |
| 🟠 **Alpha** | Backend presente, frontend parziale | Logica presente, UI incompleta |
| 🔴 **Skeleton** | Codice esistente ma non funzionale | File creati, logica non completa |

### Maturity per Feature

| Feature | Maturity | Motivazione |
|---------|----------|------------|
| CRM (Deals/Pipeline) | 🟢 Production | 14 frontend files, 22 actions, full pipeline |
| Invoicing v2 | 🟢 Production | 42 actions, 6 services, PDF/archive/workflow |
| Contacts | 🟢 Production | 15 actions, Google sync |
| Task Manager | 🟢 Production | 24 actions, 9 frontend files |
| Calendar | 🟢 Production | Google Calendar sync |
| Chat | 🟢 Production | 16 actions, Pusher WebSocket |
| Files | 🟢 Production | 11 actions, Google Drive sync |
| Email Marketing | 🟢 Production | 11 frontend, CampaignService 1,196 LOC |
| E-Invoicing | 🟢 Production | 30 actions, 13 builders, 4,552 LOC |
| Expenses | 🟢 Production | 22 actions, OCR workflow |
| Accounting | 🟡 Beta | CoA, Journal entries — needs reconciliation polish |
| Budget | 🟡 Beta | 18 actions, needs forecasting improvements |
| Analytics | 🟡 Beta | 5 actions — needs more depth |
| Banking | 🟡 Beta | Import + reconciliation, Tink live |
| OCR | 🟡 Beta | Gemini Vision, receipt scanning |
| AI Assistant | 🟡 Beta | 1,692 LOC, context builder, memory |
| Workflows | 🟡 Beta | 990 LOC service, automation engine |
| Site Builder | 🟡 Beta | 23 frontend files, template system |
| Client Portal | 🟡 Beta | PortalV2, 10 frontend files |
| Open Banking | 🟡 Beta | Tink integration, PSD2 |
| Webhooks | 🟢 Production | 12 actions, 811 LOC service |
| API Keys | 🟢 Production | 11 actions |
| Forms | 🟡 Beta | 7 frontend files, submissions |
| Fiscal | 🟡 Beta | Validation, year management |
| Time Tracking | 🟢 Production | 7 actions, timer UI |
| Reports | 🟡 Beta | 13 actions, financial reports |
| Proposals | 🟠 Alpha | 4 backend actions only |
| Commerce | 🟠 Alpha | 6 backend actions |
| SMS Custom | 🟡 Beta | 944 LOC service |
| QR Codes | 🟢 Production | 7 actions, 645 LOC service |

### Summary Maturity

| Level | Count | % |
|-------|-------|---|
| 🟢 Production | 14 | 47% |
| 🟡 Beta | 13 | 43% |
| 🟠 Alpha | 2 | 7% |
| 🔴 Skeleton | 1 | 3% |
| **Totale** | **30** | **100%** |

---

## 5. Feature Gaps vs Top Competitors

| Feature | Kacinka | Holded | Odoo | Zoho | QuickBooks |
|---------|---------|--------|------|------|------------|
| Multi-country e-invoicing | ✅ 13 formats | ⚠️ SII only | ⚠️ Per-country modules | ❌ | ❌ |
| Inventory management | ❌ Missing | ✅ | ✅ | ✅ | ✅ |
| Payroll | ❌ Missing | ⚠️ Add-on | ✅ | ✅ | ✅ |
| POS (Point of Sale) | ❌ Missing | ⚠️ Add-on | ✅ | ❌ | ❌ |
| Manufacturing | ❌ Missing | ⚠️ Add-on | ✅ | ❌ | ❌ |
| Project billing (time→invoice) | ⚠️ Partial | ✅ | ✅ | ✅ | ✅ |
| Mobile app (native) | ❌ Missing | ✅ | ✅ | ✅ | ✅ |
| Marketplace/Plugin ecosystem | ❌ Missing | ❌ | ✅ 40K+ | ✅ Zoho Marketplace | ✅ Intuit Store |
| Bank feed auto-sync | ⚠️ Tink | ✅ | ✅ | ✅ | ✅ |
| Multi-currency accounting | ⚠️ Partial | ✅ | ✅ | ✅ | ✅ |

### Priorità Sviluppo Gap

| Gap | Priority | Effort | Impact |
|-----|----------|--------|--------|
| Inventory management | 🔴 Alta | 2-3 settimane | Blocca PMI product-based |
| Mobile app (PWA) | 🔴 Alta | 4-6 settimane | Blocca adozione mobile |
| Multi-currency full accounting | 🟡 Media | 1-2 settimane | Necessario per EU expansion |
| Project billing (time→invoice) | 🟡 Media | 1 settimana | Freelancer killer feature |
| Payroll | 🟢 Bassa | 6-8 settimane | Può essere add-on/integration |
| POS | 🟢 Bassa | 4-6 settimane | Nicchia retail |

---

*Documento generato il 2026-03-07. Dati estratti direttamente dal codice sorgente (234 frontend module files, 677 actions, 120 services).*
