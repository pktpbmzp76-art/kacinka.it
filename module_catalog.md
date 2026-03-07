# Module Catalog — Deep Dive per Modulo

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. CRM (Customer Relationship Management)

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 14 |
| Backend actions | Deal(11) + Pipeline(9) + CrmEmail(2) = 22 |
| Services | PipelineService, CrmInvoiceWorkflowService |
| Piano minimo | Starter (€0) |

### Funzionalità
- Gestione deal con drag-and-drop Kanban
- Pipeline personalizzabili (multi-pipeline)
- Conversione deal → fattura (CrmInvoiceWorkflowService)
- Email tracking integrato (CrmEmail)
- Note e attività per deal
- Custom fields per deal
- Collegamento a contatti e aziende
- Revenue forecasting per pipeline stage

### File Structure
```
app/src/pages/project/modules/crm/ (14 files)
auth/src/Action/Deal/ (11 actions)
auth/src/Action/Pipeline/ (9 actions)
auth/src/Action/CrmEmail/ (2 actions)
auth/src/Service/PipelineService.php
auth/src/Service/CrmInvoiceWorkflowService.php
```

---

## 2. Invoicing (v2)

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 8 |
| Backend actions | Invoice(9) + InvoiceV2(33) = 42 |
| Services | InvoiceService, InvoiceServiceV2, InvoicePdfService, InvoiceArchiveService, InvoiceNumberingService, InvoiceWorkflowService |
| Piano minimo | Pro (€19/mo) |

### Funzionalità
- Creazione e gestione fatture (v2 completo)
- Generazione PDF con DomPDF
- Numerazione automatica configurabile
- Workflow fatturazione (bozza → inviata → pagata → archiviata)
- Archivio fatture con ricerca
- Template personalizzabili
- Invio email con fattura allegata
- Pagamento parziale e scadenze
- Report fatturato
- Collegamento CRM → Fattura
- Multi-valuta
- Riepilogo IVA
- Solleciti automatici (DunningService)

### File Structure
```
app/src/pages/project/modules/invoices/ (8 files)
auth/src/Action/Invoice/ (9 actions)
auth/src/Action/InvoiceV2/ (33 actions)
auth/src/Service/InvoiceService.php
auth/src/Service/InvoiceServiceV2.php (969 LOC)
auth/src/Service/InvoicePdfService.php
auth/src/Service/InvoiceArchiveService.php
auth/src/Service/InvoiceNumberingService.php
auth/src/Service/InvoiceWorkflowService.php
```

---

## 3. E-Invoice (Fatturazione Elettronica Multi-Paese)

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 8 |
| Backend actions | EInvoice(30) |
| Services | EInvoiceService (4,051 LOC), EInvoiceAuditService, EInvoiceOnboardingService + 13 Builders |
| Piano minimo | Pro+ (€39/mo) |

### Builder Details

| Builder | LOC | Formato | Paese | Gateway |
|---------|-----|---------|-------|---------|
| FatturaPABuilder | 517 | FatturaPA XML | 🇮🇹 Italia | SDI |
| XRechnungBuilder | 153 | XRechnung UBL | 🇩🇪 Germania | PEPPOL |
| FacturXBuilder | 519 | Factur-X (CII) | 🇫🇷 Francia | Factur-X |
| ChorusProBuilder | 217 | Chorus Pro UBL | 🇫🇷 Francia B2G | Chorus Pro |
| SIIBuilder | 302 | SII XML | 🇪🇸 Spagna | AEAT SII |
| KSeFBuilder | 279 | KSeF FA(2) | 🇵🇱 Polonia | KSeF |
| MyDATABuilder | 327 | myDATA XML | 🇬🇷 Grecia | AADE myDATA |
| EFaturaBuilder | 488 | eFatura UBL-TR | 🇹🇷 Turchia | GİB |
| GSTBuilder | 374 | GST JSON | 🇮🇳 India | GST Portal |
| ZATCABuilder | 589 | ZATCA FATOORAH | 🇸🇦 Arabia Saudita | ZATCA |
| ROCIUSBuilder | 111 | RO CIUS | 🇷🇴 Romania | ANAF |
| PeppolBIS3Builder | 621 | PEPPOL BIS 3.0 | 🌍 EU | PEPPOL SMP |
| EInvoiceBuilderInterface | 55 | Interface | — | — |
| **Totale** | **4,552** | **12 formati + interface** | **11 paesi** | **11 gateway** |

### Routing Logic (PlatformInvoiceService)
```
IT → FatturaPA/SDI
DE → XRechnung/PEPPOL
FR → FacturX/ChorusPro
EU+VAT → Reverse charge
EU B2C → OSS
Non-EU → PDF+email
```

---

## 4. Accounting

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 8 |
| Backend actions | Accounting(22) |
| Services | ChartOfAccountsService (876 LOC), JournalEntryService (850 LOC), FinancialReportService |
| Piano minimo | Pro+ (€39/mo) |

### Funzionalità
- Piano dei conti (Chart of Accounts) configurabile
- Registrazioni contabili (Journal Entries) con partita doppia
- Bilancio, Conto Economico
- Report finanziari personalizzabili
- Saldo clienti/fornitori
- Riconciliazione bancaria (con BankReconciliationService)
- CRM-Accounting bridge per coerenza dati

---

## 5. Expenses

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 9 |
| Backend actions | Expense(22) |
| Services | ExpenseService (927 LOC), ExpenseCategoryService, ExpenseOcrWorkflowService (668 LOC) |
| Piano minimo | Pro (€19/mo) |

### Funzionalità
- Gestione spese con categorie
- Upload ricevute con OCR automatico (Gemini Vision)
- Workflow spesa: creazione → categorizzazione AI → approvazione → registrazione contabile
- Report spese per periodo/categoria
- Esportazione CSV/PDF

---

## 6. Budget & Forecasting

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 8 |
| Backend actions | Budget(18) |
| Services | BudgetService (895 LOC) |
| Piano minimo | Pro+ (€39/mo) |

### Funzionalità
- Budget per progetto/periodo
- Tracking spesa vs budget
- Alert superamento soglie
- Forecast revenue basato su pipeline CRM
- Confronto budget multi-periodo

---

## 7. Banking

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 8 |
| Backend actions | Bank(23) |
| Services | BankAccountService, BankImportService, BankReconciliationService (678 LOC) |
| Piano minimo | Pro+ (€39/mo) |

### Funzionalità
- Import movimenti bancari (CSV, OFX, MT940)
- Riconciliazione automatica con fatture/spese
- Matching regole personalizzabili
- Multi-conto supportato

---

## 8. Open Banking (Tink)

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 8 |
| Backend actions | OpenBanking(10) |
| Services | OpenBankingService (1,020 LOC), TinkOpenBankingService (1,069 LOC) |
| Piano minimo | Enterprise (€149/mo) |

### Funzionalità
- Connessione diretta a conti bancari via Tink (ex-TrueLayer)
- PSD2 compliant
- Sincronizzazione automatica transazioni
- Categorizzazione AI dei movimenti
- Supporto multi-banca EU

---

## 9. AI Assistant

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | — (integrato in UI globale) |
| Backend actions | Ai(9) |
| Services | AiAssistantService (1,692 LOC), AiContextBuilder, AiEscalationService, AiMemoryService, AiPolicy, AiSuggestionService |
| Piano minimo | Pro+ (€39/mo) |

### Funzionalità
- Chat AI contestuale (comprende dati progetto)
- Suggerimenti automatici (categorizzazione spese, deal scoring)
- Memoria conversazioni (AiMemoryService)
- Escalation automatica a supporto umano
- Policy system per limitare scope risposte
- Multi-model: Gemini 2.0 Flash (OCR) + Flash-Lite (default)

---

## 10. OCR (Receipt Scanning)

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 9 |
| Backend actions | Ocr(11) |
| Services | OcrService (925 LOC), ExpenseOcrWorkflowService (668 LOC) |
| Piano minimo | Pro+ (€39/mo) |

### Funzionalità
- Upload foto ricevuta/scontrino
- Estrazione automatica (data, importo, fornitore, IVA) via Gemini Vision
- Creazione automatica spesa da OCR
- Supporto multi-lingua (riconoscimento ricevute in qualsiasi lingua)
- Batch processing

---

## 11. Email Marketing

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 11 |
| Backend actions | Email(8) + EmailConfig(2) = 10 |
| Services | CampaignService (1,196 LOC), EmailConfigService (872 LOC), EmailQueueService, EmailTranslationService, MailService |
| Piano minimo | Pro (€19/mo) |

### Funzionalità
- Creazione campagne email con editor drag & drop (TipTap)
- Segmentazione contatti per tag/proprietà
- Template personalizzabili
- Traduzione automatica campagne (EmailTranslationService)
- Coda invio con rate limiting (EmailQueueService)
- Tracking aperture e click
- Configurazione SMTP custom
- A/B testing (basic)

---

## 12. Calendar

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 8 |
| Backend actions | Calendar(10) |
| Services | CalendarService (788 LOC), GoogleCalendarService |
| Piano minimo | Starter (€0) |

### Funzionalità
- Calendario interattivo (FullCalendar)
- Creazione eventi, promemoria
- Sync bidirezionale con Google Calendar
- Vista giornaliera/settimanale/mensile
- Collegamento eventi a deal/task

---

## 13. Chat (Real-Time)

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 8 |
| Backend actions | Chat(16) |
| Services | PusherService |
| Piano minimo | Starter (€0) |

### Funzionalità
- Chat real-time team (WebSocket via Pusher)
- Canali per progetto
- Messaggi diretti (DM)
- Upload file in chat
- Menzioni @utente
- Notifiche push

---

## 14. Task Manager

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 9 |
| Backend actions | Task(24) |
| Piano minimo | Starter (€0) |

### Funzionalità
- Lista task con priorità, scadenze, assegnazioni
- Vista Kanban + Lista
- Subtask e checklist
- Commenti e allegati per task
- Reminder automatici
- Time tracking per task
- Custom fields

---

## 15. Site Builder

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | 23 (più grande modulo frontend) |
| Backend actions | SiteBuilder(14) + SiteTemplate(4) = 18 |
| Services | SiteBuilderService, SiteTemplateService, PageSpeedService, CustomDomainService, DomainRegistrarService |
| Piano minimo | Pro+ (€39/mo) |

### Funzionalità
- Website builder visuale (drag & drop)
- Template gallery pre-built
- Custom domain support
- PageSpeed audit integrato
- Hosting incluso nella piattaforma
- SEO settings per pagina
- Multi-page site
- Form integration

---

## 16. Webhooks & API Keys

### Overview
| Metrica | Valore |
|---------|--------|
| Frontend files | Webhooks(8) + API Keys(7) = 15 |
| Backend actions | Webhook(12) + ApiKey(11) = 23 |
| Services | WebhookService (811 LOC), ApiKeyService |
| Piano minimo | Pro (€19/mo) |

### Funzionalità
- Webhooks configurabili per ogni evento (fattura creata, deal chiuso, ecc.)
- API Keys con permessi granulari (scopes)
- Webhook retry con backoff esponenziale
- Log consegna webhook
- Test endpoint

---

## 17-26. Moduli Rimanenti (Summary)

| # | Modulo | Frontend | Actions | Service | Piano |
|---|--------|----------|---------|---------|-------|
| 17 | Contacts | 8 files | 15 | GoogleContactsService | Starter |
| 18 | Time Tracking | 8 files | 7 | TimeTrackingService | Starter |
| 19 | Files | 8 files | 11 | FileService, StorageService | Starter |
| 20 | Reports | 8 files | 13 | FinancialReportService | Pro |
| 21 | Forms | 7 files | 4 | FormService | Pro |
| 22 | Fiscal | 8 files | — | FiscalYearService, FiscalValidationService | Pro+ |
| 23 | Analytics | 8 files | 5 | AnalyticsService | Elite |
| 24 | Client Portal | 10 files | 8 | ClientPortalService | Elite |
| 25 | CRM-Accounting | 8 files | 7 | CrmAccountingBridge | Pro+ |
| 26 | Workflows | 7 files | 8 | WorkflowService, WorkflowAutomationService | Pro+ |

---

## Summary Totals

| Metrica | Valore |
|---------|--------|
| Moduli totali | 26 |
| Frontend files (modules/) | 234 |
| Backend action directories | 82 |
| Backend action classes | 677 |
| Backend services | 120 |
| Top service by LOC | EInvoiceService (4,051) |
| Largest frontend module | site-builder (23 files) |
| Most backend actions | InvoiceV2 (33) + Admin(46) |

---

*Documento generato il 2026-03-07. Dati estratti dal codice sorgente per ogni modulo con conteggi file verificati.*
