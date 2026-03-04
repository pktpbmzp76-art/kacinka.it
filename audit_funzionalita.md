# Audit Funzionalità — Kacinka.it Platform

> **Data**: Marzo 2026  
> **Versione**: 2.0 — Rigenerato da analisi completa del codice sorgente  
> **Scope**: Analisi esaustiva di ogni modulo e funzionalità della piattaforma  
> **Metodo**: Lettura diretta di routes.php (1,310 LOC), 622 Action classes, 103 Service classes, 26 moduli frontend  

---

## 1. Mappa Completa degli Endpoint API (936 Route)

### 1.1 Distribuzione per Modulo

| # | Modulo Backend | Route | Action Files | Service LOC | Entitlement |
|---|---------------|-------|-------------|------------|-------------|
| 1 | Health & Sistema | 6 | 3 | — | — |
| 2 | Auth & Verifica | 16 | 8 | 331 (OAuth) + 298 (Security) | — |
| 3 | Profilo & Sicurezza | 17 | 6 | 250 (Session) + 225 (TwoFactor) | — |
| 4 | Email Config (SMTP) | 14 | 2 | 794 | — |
| 5 | Progetti | 8 | 5 | — | — |
| 6 | Task | 26 | 24 | — | — |
| 7 | CRM (Deal+Pipeline+Proposte+Aziende+Auto+Remind+Email) | 40+ | 20+ | 335 (Pipeline) | `crm` |
| 8 | Contatti | 16 | 12 | — | — |
| 9 | Chat | 22 | 15 | — | — |
| 10 | AI Assistant | 21 | 9 | 1,577 + 295 + 175 | — |
| 11 | Notifiche | 8 | 5 | 377 (Notif) | — |
| 12 | Calendario | 15 | 7 | 690 (Calendar) | — |
| 13 | Google Integrations | 22 | 8 | 420+590+579+444 | — |
| 14 | Invoices V2 | 35 | 20+ | 785 + 1,124 | `invoices` |
| 15 | Payments | 22 | 12 | 884 + 243 | `invoices` |
| 16 | Expenses | 18 | 12 | 920 + 235 | `expenses` |
| 17 | Banking | 23 | 15 | 670 + 477 + 324 | `banking` |
| 18 | Accounting | 22 | 22 | 819 + 795 + 457 + 530 | `accounting` |
| 19 | Reports | 14 | 10 | 530 | `reports` |
| 20 | Tax/IVA | 19 | 10 | 508 + 590 + 506 | `fiscal` |
| 21 | E-Invoice | 14 | 10 | 1,132 | `einvoice` |
| 22 | Email Marketing | 30+ | 8 | 965 | `email_marketing` |
| 23 | Site Builder | 29 | 13 | 374 + 345 | `site_builder` |
| 24 | Forms | 22 | 3 | 326 | `forms` |
| 25 | Workflows | 12 | 3 | 874 | `workflows` |
| 26 | Webhooks | 14 | 8 | 602 | `webhooks` |
| 27 | Files & Cartelle | 11 | 8 | 443 | — |
| 28 | Time Tracking | 9 | 6 | 240 | — |
| 29 | Budget & Costi | 18 | 14 | 771 | `budget` |
| 30 | API Keys | 12 | 8 | 491 | `api_access` |
| 31 | Open Banking | 10 | 8 | 971 | `open_banking` |
| 32 | OCR Receipts | 8 | 7 | 185 | — |
| 33 | Client Portal (v1+v2) | 19 | 6 | 346 | `client_portal` |
| 34 | Branding & White Label | 10 | 4 | 302 | opcionale `white_label` |
| 35 | Domain & Hosting | 28 | 4 | 389 + 651 | — |
| 36 | Subscription & Plans | 15 | 8 | 503 + 602 | — |
| 37 | Checkout & Stripe | 4 | 4 | 1,440 | — |
| 38 | Admin Panel | 90+ | 44 | 428 + 370 + 320 + 305 | `admin` role |
| 39 | Misc (tags, notes, comments, search, etc.) | 20+ | 10+ | — | — |

---

## 2. Analisi Dettagliata per Modulo

### 2.1 Task Management

**Backend**: 26 route, 24 Action file, `TaskService` (non in top service — distribuito tra Actions)  
**Frontend**: 9 file, 3,168 LOC (modulo `task-manager`)  
**Store**: `taskStore.js` (552 LOC — il più grande)  
**Database**: `tasks`, `task_comments`, `task_checklists`, `task_checklist_items`, `task_attachments`, `task_tags`, `task_followers`, `task_templates`

**Funzionalità reali implementate**:

| Feature | Endpoint Backend | Frontend |
|---------|-----------------|----------|
| CRUD Task | POST/PUT/DELETE `/projects/{pid}/tasks` | TaskBoard, TaskList |
| Viste multiple | GET con view param | Board (Kanban), List, Calendar, Gantt |
| Subtask | GET `/{id}/subtasks` | Nested display |
| Dipendenze | CRUD `/tasks/{id}/dependencies` | Dependency graph |
| Commenti | CRUD `/tasks/{id}/comments` | Thread commenti |
| Checklist | CRUD `/tasks/{id}/checklists` + toggle items | Checklist interattiva |
| Allegati | POST `/tasks/{id}/attachments` (planLimit:storage+files) | Upload drag&drop |
| Ricorrenza | GET/PUT/DELETE `/tasks/{id}/recurrence` | Config ricorrenza |
| Template | GET `/task-templates`, POST `apply-template` | Template library |
| Riordinamento | PUT `tasks/reorder` | Drag&drop Kanban |
| Import/Export | POST `tasks/import`, GET `tasks/export` | CSV/JSON |
| Follower | Via task_followers table | Watch/Unwatch |

**Plan Limits**: `max_tasks_per_project` (Free: illimitato)

---

### 2.2 CRM (Customer Relationship Management)

**Concetto architetturale**: Il CRM non è un singolo modulo ma un ecosistema distribuito su 9 namespace Action:

```
CRM Ecosystem
├── Deal/ (11 route) — Deal Kanban con pipeline stages
├── Pipeline/ (4 route) — Configurazione pipeline personalizzate
├── Proposal/ (4 route) — Preventivi e proposte commerciali
├── Company/ (5 route) — Anagrafica aziende
├── Automation/ (4 route) — Automazioni CRM
├── Reminder/ (5 route) — Promemoria e follow-up
├── CrmEmail/ (2 route) — Email CRM integrate (SmtpGate)
├── Contact/ (16 route) — Contatti con lead scoring
└── CrmAccounting/ (7 route) — Bridge CRM ↔ Contabilità
```

**Frontend**: 14 file, 3,346 LOC (modulo `crm`)  
**Store**: `crmStore.js` (393 LOC)

**Deal Management**:
| Feature | Dettaglio |
|---------|-----------|
| Kanban Board | Pipeline stages con drag&drop (`deals/reorder`) |
| Pipeline personalizzate | CRUD pipeline con stages custom |
| Deal Activities | Timeline attività (`/{id}/activities`) |
| Note Deal | CRUD note per deal |
| Follower | Assegnazione follower per notifiche |
| Import/Export | Bulk import/export deal CSV |

**Contact Management**:
| Feature | Dettaglio |
|---------|-----------|
| Contatti globali/progetto | GET `/contacts` (globali) + `/projects/{pid}/contacts` |
| Lead Scoring | `POST /contacts/recalculate-scores` — scoring automatico |
| Timeline contatto | GET `/{id}/timeline` — cronologia completa |
| Fatture contatto | GET `/{id}/invoices` — fatture associate |
| Deal contatto | GET `/{id}/deals` — deal pipeline |
| Spese contatto | GET `/{id}/expenses` |
| Comunicazioni | GET `/{id}/communications` |
| Import/Export | CSV import con mapping colonne + merge contatti duplicati |
| Campi personalizzati | Via `custom-field-defs` |
| Tag | Tagging multivalore |
| Merge | POST `/contacts/merge` — unificazione duplicati |

**CRM–Contabilità Bridge** (modulo `crm-accounting`, 738 LOC FE):
- Link contatto ↔ account contabile
- Suggerimento link automatico
- Overdue contacts report
- Conversione deal → fattura diretta

**Plan Limits**: `max_contacts` (Free: 10), `max_deals_per_project` (Free: illimitato)  
**Entitlement**: `crm` (richiesto per deal, pipeline, proposte, aziende, automazioni)

---

### 2.3 Fatturazione (Invoicing V2)

**Backend**: 35 route, 20+ Action file  
**Servizi**: `InvoiceServiceV2` (785 LOC), `PlatformInvoiceService` (1,124 LOC), `InvoicePdfService` (496 LOC), `InvoiceNumberingService` (341 LOC)  
**Frontend**: 8 file, 2,108 LOC (modulo `invoices`)

**Ciclo di vita fattura**:
```
Bozza (draft) → Finalizzata (finalized) → Inviata (sent) → Pagata (paid)
                                         ↗ Nota di credito (credit_note)
                                         ↗ Annullata (void)
                                         ↗ Scaduta (overdue — marcatura automatica)
```

**Funzionalità reali**:

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| CRUD Fattura | CRUD `/invoices-v2` | Creazione, modifica, eliminazione |
| Finalizzazione | POST `/{id}/finalize` | Blocco modifiche, assegnazione numero |
| Invio email | POST `/{id}/send` | Via SMTP utente (SmtpGate) |
| Nota di credito | POST `/{id}/credit-note` | Genera nota di credito collegata |
| Duplicazione | POST `/{id}/duplicate` | Clone fattura |
| Conversione | POST `/{id}/convert` | Quote → Invoice |
| Marcatura pagata | POST `/{id}/paid` | Registrazione manualmente |
| Annullamento | POST `/{id}/void` | Storno fattura |
| PDF generazione | GET `/{id}/pdf` | PDF brandizzato |
| Audit log fattura | GET `/{id}/audit-log` | Storico modifiche |
| Marcatura scadute | POST `/mark-overdue` | Batch automatico |
| Aging report | GET `/aging-report` | Report crediti scaduti |
| Fatture ricorrenti | CRUD `/recurring-invoices` | Schedule automatico |
| Codici IVA | CRUD `/tax-codes` + import defaults | Multi-aliquota |
| Ritenute d'acconto | CRUD `/withholding-taxes` | Config per tipo |
| Regime fiscale | GET/PUT `/tax-regime` | Ordinario, forfettario, etc. |
| Template fattura | CRUD `/invoice-templates` | Design personalizzabile |
| Numerazione | Config via compliance endpoint | Per serie, per anno |

**Sistema Pagamenti** (22 route):
| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Lista pagamenti | GET `/payments` | Tutti i pagamenti progetto |
| Registrazione | POST `/payments` | Pagamento manuale |
| Allocazione | POST `/{id}/allocate` | Associa pagamento → fattura |
| Deallocazione | DELETE `/{id}/allocations/{aid}` | Rimuovi associazione |
| Rimborso | POST `/{id}/refund` | Rimborso parziale/totale |
| Non allocati | GET `/payments/unallocated` | Pagamenti senza fattura |
| Piano rate | POST `/invoices/{id}/schedule` | Rateizzazione |
| Rata pagata | POST `/schedule/{installmentId}/paid` | Marcatura singola rata |
| Rate scadute | GET/POST `/schedule/overdue` | Automatico + manuale |
| Rate prossime | GET `/schedule/upcoming` | Prossime scadenze |
| Metodi pagamento | CRUD `/payment-methods` | Bonifico, carta, etc. |

**Plan Limits**: `max_invoices_month` (Free: illimitato)

---

### 2.4 Contabilità (Accounting)

**Backend**: 22 route, 22 Action file  
**Servizi**: `ChartOfAccountsService` (819 LOC), `JournalEntryService` (795 LOC), `FiscalYearService` (457 LOC), `FinancialReportService` (530 LOC)  
**Frontend**: 8 file, 1,715 LOC (modulo `accounting`)  
**Store**: `accountingStore.js` (365 LOC)

**Piano dei Conti**:
| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Lista conti | GET `/accounting/accounts` | Lista tabulare |
| Albero conti | GET `/accounting/accounts/tree` | Struttura gerarchica |
| Saldi | GET `/accounting/accounts/balances` | Saldi correnti |
| CRUD conto | POST/PUT/DELETE `/accounting/accounts` | Tipo, codice, padre |
| Import template | POST `/accounting/accounts/import` | Piano dei conti standard per paese |
| Mastro conto | GET `/accounting/accounts/{id}/ledger` | Movimenti dettagliati |

**Prima Nota (Journal Entries)**:
| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| CRUD registrazioni | CRUD `/accounting/journal-entries` | Dare/Avere multipli |
| Pubblicazione | POST `/{id}/post` | Da bozza a pubblicato |
| Storno | POST `/{id}/void` | Registrazione inversa |
| Bilancio verifica | GET `/accounting/trial-balance` | Totali per conto |

**Esercizi Fiscali**:
| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| CRUD esercizi | CRUD `/accounting/fiscal-years` | Date inizio/fine |
| Azioni esercizio | POST `/{id}/actions` | Apertura, chiusura, riapertura |
| Azioni periodo | POST `/periods/{id}/actions` | Chiusura periodo |

**Report Finanziari** (14 route):
| Report | Endpoint | Dettaglio |
|--------|---------|-----------|
| Bilancio verifica | GET `/reports/trial-balance` | Totali dare/avere per conto |
| Conto economico | GET `/reports/profit-and-loss` | Ricavi vs. costi per periodo |
| Stato patrimoniale | GET `/reports/balance-sheet` | Attività/Passività/Patrimonio netto |
| Cash flow | GET `/reports/cash-flow` | Flussi di cassa |
| Mastro generale | GET `/reports/general-ledger/{accountId}` | Movimenti per conto |
| Estratto conto | GET `/reports/account-statement/{contactId}` | Per cliente/fornitore |
| Breakdown periodo | GET `/reports/period-breakdown` | Mensile/trimestrale |
| Crediti anzianità | GET `/reports/aged` | Aging receivables/payables |
| Snapshot | GET `/reports/snapshot` | Riepilogo rapido |
| Dashboard | GET `/reports/dashboard` | Widget contabili |
| Top accounts | GET `/reports/top-accounts` | Conti più movimentati |
| Schedule report | POST `/reports/schedule` | Report periodico automatico |
| PDF | GET `/reports/{type}/{id}/pdf` | Export PDF |

**Entitlement**: `accounting` per tutti gli endpoint

---

### 2.5 Banking & Riconciliazione

**Backend**: 23 route, 15 Action file  
**Servizi**: `BankReconciliationService` (670 LOC), `BankImportService` (477 LOC), `BankAccountService` (324 LOC)  
**Frontend**: 8 file, 1,506 LOC (modulo `banking`)  
**Store**: `bankingStore.js` (432 LOC)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| CRUD conti bancari | CRUD `/bank-accounts` | Nome, IBAN, valuta, saldo iniziale |
| Riepilogo conto | GET `/{id}/summary` | Saldo attuale, ingressi/uscite |
| CRUD transazioni | GET/POST `/bank-accounts/{id}/transactions` | Manuale |
| Esclusione/Ripristino | POST `/bank-transactions/{txId}/exclude` + `/restore` | Escludi da riconciliazione |
| Import multi-formato | POST `/{id}/import` | CSV, OFX, QIF, MT940 |
| Storico import | GET `/{id}/import-history` | Log batch |
| Elimina batch | POST `/{id}/delete-batch` | Rimuovi import |
| Auto-match | POST `/{id}/auto-match` | Matching automatico transazione↔fattura/spesa |
| Riconciliazione | POST `/{id}/reconciliations` | Avvio sessione |
| Completa riconciliazione | POST `/bank-reconciliations/{recId}/complete` | Chiusura |
| Match manuale | POST `/bank-transactions/{txId}/match` | Associazione |
| Unmatch | POST `/bank-transactions/{txId}/unmatch` | Rimozione associazione |
| Accetta suggerimento | POST `/bank-transactions/{txId}/accept-suggestion` | AI/rule-based |
| Dettagli match | GET `/bank-transactions/{txId}/match-details` | Info associazione |

---

### 2.6 Open Banking (PSD2)

**Backend**: 10 route, 8 Action file  
**Servizio**: `OpenBankingService` (971 LOC)  
**Frontend**: 8 file, 819 LOC (modulo `open-banking`)  
**Store**: `openBankingStore.js` (235 LOC)  
**Provider**: TrueLayer API (sandbox)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Lista istituti | GET `/open-banking/institutions` | Banche disponibili |
| Crea requisizione | POST `/open-banking/requisitions` | Avvia flusso consent |
| Callback | GET `/open-banking/callback` (public) | Ritorno da banca |
| Completa connessione | POST `/{connectionId}/complete` | Finalizza |
| Link conto | POST `/{connectionId}/link` | Associa a bank_account locale |
| Sync transazioni | POST `/{connectionId}/sync` | Importa movimenti |
| Categorizzazione | POST `/{connectionId}/categorize` | Categorizzazione automatica |
| Lista connessioni | GET `/open-banking/connections` | Connessioni attive |
| Storico sync | GET `/{connectionId}/sync-history` | Log sincronizzazioni |
| Revoca | DELETE `/{connectionId}` | Rimuovi consent |

**Entitlement**: `open_banking` (Elite+)

---

### 2.7 Fiscale & Compliance Multi-Paese

**Backend**: 19 route Tax + 15 route Compliance, 10 Action file  
**Servizi**: `TaxService` (508 LOC), `VatReportService` (590 LOC), `CountryTaxProfileService` (506 LOC), `OssTrackingService` (510 LOC)  
**Frontend**: 8 file, 1,193 LOC (modulo `fiscal`)  
**Store**: `fiscalStore.js` (424 LOC)

**IVA / VAT**:
| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Liquidazione IVA | GET `/tax/vat-liquidation` | Calcolo trimestrale |
| Registro IVA | GET `/tax/vat-register` | Registro acquisti/vendite |
| Riepilogo IVA | GET `/tax/vat-summary` | Panoramica periodi |
| LIPE | GET `/tax/lipe` | Comunicazione liquidazioni periodiche (IT) |
| Report IVA CRUD | CRUD `/tax/vat-reports` | Multi-paese |
| File dichiarazione | POST `/{id}/file` | Trasmissione |
| Marcatura pagata | POST `/{id}/paid` | Registra pagamento |

**5 Report IVA Nazionali** (dal `VatReportService`):
| Report | LOC | Paese | Standard |
|--------|-----|-------|----------|
| `ItalianLipeReport` | ~312 | Italia | LIPE trimestrale |
| `SpanishModelo303Report` | ~310 | Spagna | Modelo 303 AEAT |
| `FrenchCA3Report` | ~295 | Francia | Déclaration CA3 |
| `GermanUstvaReport` | ~284 | Germania | UStVA Elster |
| `UkMtdReport` | ~276 | UK | Making Tax Digital |

**Profili Fiscali Paese**:
| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Lista profili | GET `/country-tax-profiles` | Tutti i paesi supportati |
| Profilo paese | GET `/{code}` | Dettaglio specifico |
| Aliquote IVA | GET `/{code}/vat-rates` | Rate per paese |
| Campi obbligatori | GET `/{code}/required-fields` | Requisiti paese |
| Validazione P.IVA | POST `/validate-vat` | Verifica VIES |
| Validazione CF | POST `/validate-fiscal-code` | Verifica codice fiscale |
| Compliance status | GET `/compliance/status` | Stato conformità |
| Numerazione | GET/POST `/compliance/numbering` | Config numerazione fatture |
| Integrità numerazione | GET `/compliance/numbering/integrity` | Verifica sequenzialità |
| Cambio paese | POST `/compliance/change-country` | Migrazione contesto fiscale |

**OSS (One-Stop Shop)**:
- `OssTrackingService` (510 LOC)
- Tracking vendite intra-UE per sopra/sotto soglia
- Report per dichiarazione OSS

**Ritenute d'Acconto**:
| Feature | Endpoint |
|---------|---------|
| CRUD config | CRUD `/tax/withholding-configs` |
| Config fattura | CRUD `/withholding-taxes` (su InvoiceV2) |

**Regime Fiscale**:
| Feature | Endpoint |
|---------|---------|
| Lista regimi | GET `/tax/regimes` |
| Regime attivo | GET `/tax/regimes/active` |
| CRUD regimi | CRUD `/tax/regimes` |

---

### 2.8 Fatturazione Elettronica (E-Invoice)

**Backend**: 14 route, 10 Action file  
**Servizio**: `EInvoiceService` (1,132 LOC) + 5 builder + 1 interfaccia  
**Frontend**: 8 file, 1,155 LOC (modulo `einvoice`)

**5 Formati Supportati**:
| Formato | Builder | LOC | Paese/Standard |
|---------|---------|-----|----------------|
| FatturaPA | `FatturaPABuilder` | ~270 | Italia (SDI) |
| Peppol BIS 3.0 | `PeppolBIS3Builder` | ~342 | EU/Internazionale (UBL) |
| Factur-X/ZUGFeRD | `FacturXBuilder` | ~282 | Francia/Germania |
| XRechnung | `XRechnungBuilder` | ~128 | Germania |
| Chorus Pro | `ChorusProBuilder` | ~171 | Francia |

**Workflow e-invoice**:
```
Fattura → Genera XML (formato scelto) → Validazione XML → Trasmissione SDI/Peppol → 
  → Notifiche stato (consegnata/scartata/accettata/rifiutata) → Archiviazione
```

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Configurazione | GET/PUT `/einvoice/config` | Provider, credenziali SDI, PEC |
| Genera FatturaPA | POST `/einvoice/generate/fatturapa/{invoiceId}` | XML FatturaPA |
| Genera Peppol | POST `/einvoice/generate/peppol/{invoiceId}` | XML UBL Peppol |
| Lista invii | GET `/einvoice/submissions` | Storico trasmissioni |
| Dettaglio invio | GET `/einvoice/submissions/{id}` | Status dettagliato |
| Download XML | GET `/einvoice/submissions/{id}/xml` | File XML generato |
| Validazione | POST `/einvoice/submissions/{id}/validate` | Pre-validazione |
| Trasmissione | POST `/einvoice/submissions/{id}/submit` | Invio effettivo |
| Eliminazione | DELETE `/einvoice/submissions/{id}` | Rimuovi bozza |
| Notifica entrata | POST `/einvoice/notification` | Webhook ricezione |
| Parse passiva | POST `/einvoice/parse-passive` | Import fattura passiva XML |
| Statistiche | GET `/einvoice/stats` | Invii/errori/pendenti |
| Non trasmesse | GET `/einvoice/untransmitted` | Fatture da trasmettere |

---

### 2.9 Chat

**Backend**: 22 route, 15 Action file  
**Frontend**: 8 file, 1,918 LOC (modulo `chat`)  
**Meccanismo realtime**: HTTP polling (no WebSocket)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Lista chat | GET `/chats` o `/projects/{pid}/chats` | Globali o per progetto |
| Crea chat | POST `/chats` | Privata o canale |
| Messaggi | GET/POST `/chats/{id}/messages` | Lista + invio |
| Modifica messaggio | PUT `/chats/{id}/messages/{mid}` | Edit in-place |
| Elimina messaggio | DELETE `/chats/{id}/messages/{mid}` | Soft delete |
| Pin messaggio | PUT `/chats/{id}/messages/{mid}/pin` | Toggle pin |
| Reazione | POST `/chats/{id}/messages/{mid}/react` | Emoji reactions |
| Typing indicator | POST `/chats/{id}/typing` | Mostra "sta scrivendo" |
| Gestione membri | CRUD `/chats/{id}/members` | Aggiungi/rimuovi/ruolo |
| Ricerca messaggi | GET `/chats/search` | Full-text search |
| Mention suggest | GET `/chats/{id}/mentions` | Autocomplete @user |
| Upload file | POST `/chats/{id}/upload` | File sharing (planLimit:storage) |
| Statistiche | GET `/chats-stats` | Conteggi e attività |
| Sondaggi | CRUD `/chats/{id}/polls` + voto | Polling interattivo |

---

### 2.10 AI Assistant

**Backend**: 21 route, 9 Action file  
**Servizi**: `AiAssistantService` (1,577 LOC — il più grande), `AiSuggestionService` (295 LOC), `AiMemoryService` (175 LOC), `AiEscalationService` (150 LOC)  
**Provider**: Google Gemini (`gemini-2.0-flash-lite`), OpenAI (fallback)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Chat conversazionale | POST `/ai/chat` | Conversazione multi-turno con memoria |
| Storico | GET `/ai/history` | Lista conversazioni |
| Cancella storico | DELETE `/ai/history` | Pulizia |
| Domanda singola | POST `/ai/ask` | Q&A one-shot |
| Conversazioni | GET `/ai/conversations` + `/{id}` | CRUD conversazioni |
| Escalation | POST `/ai/escalate/{conversationId}` | Scala a operatore umano |
| Feedback | POST/GET `/ai/feedback` + stats | Rating risposte AI |
| Memoria | CRUD `/ai/memory` | Preferenze utente persistenti |
| Impostazioni | GET/PUT `/ai/settings` + usage | Config AI personale |
| Suggerimenti | GET `/projects/{pid}/ai/suggest` | Suggerimenti contestuali |
| Secretary API | POST/GET `/api/v1/secretary/{action}` | API pubblica per integrazioni |

**Plan Limits**: `max_ai_calls_month` (Free: 0 — AI solo per piani a pagamento)

---

### 2.11 Email Marketing

**Backend**: 30+ route (Campagne + Liste + A/B + Sequenze + Deliverability)  
**Servizio**: `CampaignService` (965 LOC)  
**Frontend**: 11 file, 2,049 LOC (modulo `email-marketing`)  
**Store**: `emailMarketingStore.js` (286 LOC)

**Campagne**:
| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| CRUD campagna | CRUD `/email/campaigns` | Nome, template, lista target |
| Invio | POST `/{id}/send` | SmtpGate + planLimit |
| Test | POST `/{id}/send-test` | Invio test |
| Statistiche | GET `/{id}/stats` | Open rate, click, bounce |

**A/B Testing**:
| Feature | Endpoint |
|---------|---------|
| Configura A/B | GET/PUT `/{id}/ab-test` |
| Invia test | POST `/{id}/ab-test/send` |
| Eleggi vincitore | POST `/{id}/ab-test/winner` |

**Sequenze (Drip)**:
| Feature | Endpoint |
|---------|---------|
| CRUD sequenza | CRUD `/email/sequences` |
| CRUD step | POST/DELETE `/{id}/steps` |
| Enrollment | POST `/{id}/enroll` |

**Liste**:
| Feature | Endpoint |
|---------|---------|
| CRUD lista | CRUD `/email/lists` |
| Membri | GET/POST/DELETE `/{id}/members` |
| Import CSV | POST `/{id}/import` |
| Sync CRM | POST `/{id}/sync-crm` |

**Deliverability**:
| Feature | Endpoint |
|---------|---------|
| Report deliverability | GET `/email/deliverability` |
| Spam complaints | GET `/email/spam-complaints` |
| Unsubscribe | GET/POST `/unsubscribe` (public) |

**Entitlement**: `email_marketing`

---

### 2.12 Site Builder

**Backend**: 29 route, 13 Action file  
**Servizi**: `SiteBuilderService` (374 LOC), `SiteTemplateService` (345 LOC)  
**Frontend**: 21 file, 5,529 LOC (modulo più grande)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| CRUD sito | CRUD `/sites` | Nome, template, impostazioni |
| Duplicazione | POST `/{id}/duplicate` | Clone completo |
| Pubblica/Nascondi | POST `/{id}/toggle-publish` | Toggle visibilità |
| Dominio custom | PUT/POST `/{id}/domain` + verify + SSL | CNAME + SSL check |
| Statistiche siti | GET `/sites-stats` | Aggregato |
| Anteprima | POST `/sites/preview` | Preview live |
| Analytics sito | GET `/{id}/analytics` | Pagine viste, visitatori |
| Performance | GET `/{id}/performance` | Metriche velocità |
| Render pubblico | GET `/s/{slug}` (public) | Sito live |
| Template preview | GET `/template-preview/{slug}` (public) | Preview template |
| Beacon tracking | POST `/api/beacon` (public) | Analytics JS |
| Quotes | CRUD `/site-quotes` | Citazioni/Testimonial |
| Media | CRUD `/site-media` | Asset immagini/video |

**Template System** (4 route pubbliche):
| Feature | Endpoint |
|---------|---------|
| Lista template | GET `/site-templates` (public) |
| Customizzazioni utente | GET `/site-templates/my-customizations` |
| Dettaglio template | GET `/site-templates/{slug}` (public) |
| Personalizza | POST `/site-templates/{slug}/customize` |

**Entitlement**: `site_builder`

---

### 2.13 Workflows (Automazioni)

**Backend**: 12 route, 3 Action file  
**Servizio**: `WorkflowService` (991 LOC — DAG BFS engine)  
**Frontend**: 7 file, 1,424 LOC (modulo `workflows`)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| CRUD workflow | CRUD `/workflows` | Nome, trigger, nodi, edge |
| Duplicazione | POST `/{id}/duplicate` | Clone workflow |
| Esecuzione manuale | POST `/{id}/execute` | Test workflow |
| Toggle attivo | POST `/{id}/toggle-active` | Abilita/disabilita |
| Storico esecuzioni | GET `/{id}/executions` | Log run |
| Preset | GET `/workflows/presets` | 3 template pre-configurati |
| Opzioni nodo | GET `/workflows/node-options` | Dropdown team, template, etc. |
| Statistiche | GET `/workflows-stats` | Conteggi successo/errore |

**16 tipi di nodo**: trigger, condition, if_else, for_each, try_catch, switch, delay, filter, transform, send_email, create_task, update_field, update_deal, notify, webhook, http_request, error_handler

**9 trigger**: contact_created, deal_stage_changed, form_submitted, tag_added, task_completed, timer, manual, webhook_incoming, schedule

---

### 2.14 Webhooks

**Backend**: 14 route, 8 Action file  
**Servizio**: `WebhookService` (602 LOC)  
**Frontend**: 8 file, 1,272 LOC (modulo `webhooks`)

| Feature | Tipo | Endpoint |
|---------|------|---------|
| Outgoing webhook CRUD | Out | CRUD `/projects/{pid}/webhooks` |
| Lista eventi | Out | GET `/webhooks/events` |
| Test | Out | POST `/{id}/test` |
| Deliveries | Out | GET `/{id}/deliveries` + `/{deliveryId}` |
| Incoming webhook config | In | CRUD `/projects/{pid}/webhooks/incoming` |
| Incoming webhook receive | In | POST `/webhooks/incoming/{source}` (public) |

---

### 2.15 Calendar & Booking

**Backend**: 15 route, 7 Action file  
**Servizio**: `CalendarService` (690 LOC), `GoogleCalendarService` (420 LOC)  
**Frontend**: 8 file, 1,513 LOC (modulo `calendar`)  
**UI**: FullCalendar v6 (day/week/month/list view)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Eventi progetto | CRUD `/projects/{pid}/events` | CRUD completo |
| Calendario unificato | GET `/calendar/unified` | Cross-project |
| Eventi utente | GET `/calendar/events` | Tutti gli eventi personali |
| Cross-project | GET `/calendar/cross-project` | Vista multi-progetto |
| Disponibilità | GET/PUT `/calendar/availability` | Fasce orarie disponibili |
| Booking page | GET/PUT `/calendar/booking-page` | Pagina prenotazione pubblica |
| Prenotazione pubblica | GET/POST `/book/{slug}` + slots | Prenotazione esterna |
| Google Sync | POST `/integrations/google-calendar/sync` | Bidirezionale |

---

### 2.16 Time Tracking

**Backend**: 9 route, 6 Action file  
**Servizio**: `TimeTrackingService` (240 LOC)  
**Frontend**: 8 file, 1,731 LOC (modulo `time-tracking`)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| CRUD entry | CRUD `/time-entries` | Start/stop/manual |
| Report | GET `/time-report` | Per membro, periodo, progetto |
| Rate card | GET/PUT `/rate-cards` | Tariffe orarie per ruolo |
| Riepilogo fatturabile | GET `/time-entries/billable-summary` | Ore da fatturare |
| Genera fattura | POST `/time-entries/generate-invoice` | Timer→Fattura |
| Import | POST `/time-entries/import` | CSV bulk |
| Export | GET `/time-entries/export` | CSV/JSON |

---

### 2.17 Spese (Expenses)

**Backend**: 18 route, 12 Action file  
**Servizio**: `ExpenseService` (920 LOC)  
**Frontend**: 9 file, 1,517 LOC (modulo `expenses`)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Categorie | CRUD + seed `/expense-categories` | Categorie personalizzabili + seed |
| CRUD spese | CRUD `/expenses` | Data, importo, categoria, fornitore |
| Fatturabili | GET `/expenses/billable` | Spese da ribaltare al cliente |
| Per categoria | GET `/expenses/by-category` | Breakdown |
| Riepilogo | GET `/expenses/summary` | Totali periodo |
| Submit approvazione | POST `/{id}/submit` | Flusso approvazione |
| Approva | POST `/{id}/approve` | Approvazione manager |
| Rifiuta | POST `/{id}/reject` | Rifiuto con motivazione |
| Annulla | POST `/{id}/void` | Storno |
| Allega ricevuta | POST `/{id}/receipt` | Upload ricevuta |
| Segna fatturate | POST `/expenses/mark-billed` | Batch |
| Import/Export | POST import, GET export | CSV |

---

### 2.18 Budget & Centri di Costo

**Backend**: 18 route, 14 Action file  
**Servizio**: `BudgetService` (771 LOC)  
**Frontend**: 8 file, 1,273 LOC (modulo `budget`)  
**Store**: `budgetStore.js` (374 LOC)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Centri costo | CRUD `/cost-centers` | Dipartimenti, progetti |
| CRUD budget | CRUD `/budgets` | Periodi, importi, per conto contabile |
| Approvazione | POST `/{id}/approve` | Workflow approvativo |
| Attivazione | POST `/{id}/activate` | Budget operativo |
| Chiusura | POST `/{id}/close` | Fine periodo |
| Varianza | GET `/{id}/variance` | Scostamento budget vs. consuntivo |
| Varianza per CC | GET `/{id}/variance/cost-centers` | Per centro di costo |
| Time non fatturato | GET `/uninvoiced-time` | Ore da fatturare |
| Time→Invoice | POST `/time-to-invoice` | Conversione ore→righe fattura |
| Statistiche | GET `/budgets/stats` | Panoramica |
| Alert | GET `/budgets/alerts` | Superamento soglie |

---

### 2.19 Files & Documents

**Backend**: 11 route, 8 Action file  
**Servizio**: `FileService` (443 LOC), `FileValidationService` (227 LOC)  
**Frontend**: 8 file, 1,210 LOC (modulo `files`)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Lista file | GET `/projects/{pid}/files` | Con filtri e paginazione |
| Upload | POST `/files` | Validazione 6-layer, planLimit:storage+files |
| Download | GET `/{id}/download` | Streaming file |
| Preview | GET `/{id}/preview` | Preview in-browser |
| Preview info | GET `/{id}/preview-info` | Metadati preview |
| CRUD file | GET/PUT/DELETE `/{id}` | Rinomina, sposta, elimina |
| Crea cartella | POST `/files/folders` | Struttura gerarchica |
| Rinomina cartella | PUT `/files/folders/{id}` | Modifica nome |
| Elimina cartella | DELETE `/files/folders/{id}` | Con contenuto |

---

### 2.20 Forms

**Backend**: 22 route (+ alias), 3 Action file  
**Servizio**: `FormService` (326 LOC)  
**Frontend**: 7 file, 913 LOC (modulo `forms`)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| CRUD form | CRUD `/standalone-forms` (e `/forms`) | Builder drag&drop |
| Duplicazione | POST `/{id}/duplicate` | Clone form |
| Submissions | GET `/{id}/submissions` + DELETE | Risposte ricevute |
| Statistiche | GET `/{id}/stats` | Tasso completamento |
| Embed code | GET `/{id}/embed` | Snippet HTML/iframe |
| Form pubblico | GET/POST `/f/{slug}` | Render + submit pubblico |
| Submit sito | POST `/api/forms/{siteId}/submit` | Submit da site builder |

---

### 2.21 Client Portal (v1 + v2)

**Backend**: 19 route, 6 Action file  
**Servizio**: `ClientPortalService` (346 LOC)  
**Frontend**: 10 file, 1,575 LOC (modulo `client-portal`)

**Portal v1** (token-based, PIN auth):
| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| CRUD portal | CRUD `/projects/{pid}/portal` | Config per contatto |
| Visualizza | GET `/portal/{token}` (public) | Portale condiviso |
| Accesso PIN | POST `/portal/{token}/pin` (public) | Autenticazione PIN |
| Approvazione | POST `/portal/{token}/approvals/{id}/decide` | Approva/rifiuta deliverable |

**Portal v2** (session-based, auth completa):
| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Config v2 | GET/PUT `/portal-v2/config` | Impostazioni avanzate |
| Messaggi | GET/POST `/{contactId}/messages` | Chat con cliente |
| Documenti | CRUD `/portal-v2/documents` | Condivisione file |
| Approvazioni v2 | CRUD `/portal-v2/approvals` + resend | Flusso approvativo |
| Auth client | POST `/portal-v2/auth/{action}` (public) | Login/register/verify client |
| Fatture client | GET `/portal-v2/client/invoices` | Vista cliente |
| Messaggi client | GET/POST `/portal-v2/client/messages` | Chat lato cliente |
| Documenti client | GET `/portal-v2/client/documents` | Download documenti |

---

### 2.22 OCR Receipts

**Backend**: 8 route, 7 Action file  
**Servizio**: `OcrService` (185 LOC)  
**Provider**: Mindee OCR API  
**Frontend**: 9 file, 654 LOC (modulo `ocr-receipts`)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Upload ricevuta | POST `/ocr/receipts` | Foto/scan (planLimit:storage) |
| Lista ricevute | GET `/ocr/receipts` | Storico OCR |
| Dettaglio | GET `/ocr/receipts/{id}` | Dati estratti |
| Elabora | POST `/ocr/receipts/{id}/process` | Invio a Mindee |
| Review | PUT `/ocr/receipts/{id}/review` | Correzione manuale |
| Link a spesa | POST `/ocr/receipts/{id}/link-expense` | Associa a `expenses` |
| Batch processing | POST `/ocr/receipts/batch-process` | Elaborazione multipla |
| Elimina | DELETE `/ocr/receipts/{id}` | Rimuovi |

---

### 2.23 API Keys

**Backend**: 12 route, 8 Action file  
**Servizio**: `ApiKeyService` (491 LOC)  
**Frontend**: 7 file, 591 LOC (modulo `api-keys`)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Lista chiavi | GET `/api-keys` | Per progetto |
| CRUD chiave | CRUD `/api-keys` | Nome, scope, limiti |
| Revoca | POST `/{id}/revoke` | Disabilitazione |
| Rotazione | POST `/{id}/rotate` | Nuova chiave, stessa config |
| Usage | GET `/{id}/usage` | Statistiche utilizzo |
| Info | GET `/api-keys/info` | Documentazione scope |
| Rate limit info | GET `/api-keys/rate-limit` | Limiti correnti |
| Scopes | GET `/api-keys/scopes` | 19 scope disponibili |
| Stats | GET `/api-keys/stats` | Aggregati |

---

### 2.24 Analytics & KPI

**Backend**: 5 route + 4 KPI alert, 5 Action file  
**Servizio**: `AnalyticsService` (672 LOC)  
**Frontend**: 8 file, 1,233 LOC (modulo `analytics`)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Overview | GET `/analytics/overview` | KPI principali |
| Trend | GET `/analytics/trends` | Grafici Recharts |
| Export | GET `/analytics/export` | CSV/PDF |
| Project analytics | GET `/projects/{pid}/analytics` | Per progetto |
| KPI Alerts | CRUD `/kpi-alerts` | Soglie con notifica automatica |

---

### 2.25 Multi-Valuta

**Backend**: 10 route  
**Servizio**: `CurrencyService` (170 LOC)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| CRUD valute | CRUD `/currencies` | Codice, simbolo, decimali |
| Tassi cambio | GET/POST `/exchange-rates` | Manuali o automatici |
| Conversione | POST `/currencies/convert` | Calcolo istantaneo |
| Fetch ECB | POST `/currencies/fetch-ecb` | Aggiornamento da BCE |
| Rivalutazione | POST `/projects/{pid}/currencies/revalue` | Rivalutazione cambi |
| Statistiche | GET `/currencies/stats` | Volumi per valuta |

---

### 2.26 Dominio & Hosting

**Domain Registrar** (13 route):
| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Ricerca dominio | GET `/domain-registrar/search` | Check disponibilità |
| Registrazione | POST `/domain-registrar/register` | Acquisto dominio |
| Lista registrazioni | GET `/registrations` | Domini posseduti |
| Auto-renew | POST `/{id}/auto-renew` | Toggle rinnovo |
| Trasferimento | POST `/transfer` | Trasferimento dominio |
| DNS CRUD | GET/POST/DELETE `/{id}/dns` | Gestione record DNS |
| Auto-configure DNS | POST `/{id}/dns/auto-configure` | Setup automatico |
| Email dominio | GET/POST `/{id}/email` | Config email su dominio |

**Hosting Reseller** (15 route):
| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Piani hosting | GET `/hosting/plans` | Listino |
| CRUD account | CRUD `/hosting/accounts` | Provisioning |
| Cambio piano | POST `/{id}/change-plan` | Upgrade/downgrade |
| Pannello | GET `/{id}/panel` | Link pannello controllo |
| Versione PHP | POST `/{id}/php-version` | Cambio PHP |
| SSL | GET `/{id}/ssl` | Stato certificato |
| Storage | GET `/{id}/storage` | Utilizzo disco |
| Email hosting | POST `/{id}/email` | Config caselle |
| Billing hosting | GET `/hosting/billing` | Fatturazioni |
| Rinnovo | POST `/{id}/renew` | Rinnovo manuale |
| Checklist | GET `/hosting/checklist` | Setup guidato |

---

### 2.27 Google Integrations (22 route, 4 servizi)

| Integrazione | Servizio | LOC | Funzionalità |
|-------------|---------|-----|-------------|
| **Google Calendar** | `GoogleCalendarService` | 420 | Sync bidirezionale eventi |
| **Google Contacts** | `GoogleContactsService` | 590 | Import/export contatti People API |
| **Google Drive** | `GoogleDriveService` | 579 | Sync file, preview documenti |
| **Google Sheets** | `GoogleSheetsService` | 444 | Export report, template fogli |

Per ogni integrazione: connect, callback, status, sync, disconnect.

---

### 2.28 Sottoscrizioni & Billing

**Backend**: 15 route subscription + 4 checkout + 4 billing  
**Servizi**: `StripeService` (1,440 LOC), `PlanService` (503 LOC), `UsageMeteringService` (602 LOC), `LimitGraceService` (584 LOC), `DunningService` (610 LOC)

| Feature | Endpoint | Dettaglio |
|---------|---------|-----------|
| Piani (public) | GET `/plans`, `/hosting-plans`, `/addons`, `/bundles` | Listino pubblico |
| Comparazione | GET `/plans/compare` | Side-by-side |
| Sottoscrizione | GET/PUT/DELETE `/subscription` | Visualizza/modifica/cancella |
| Usage | GET `/subscription/usage` | Consumo corrente |
| Limiti | GET `/subscription/limits` | Limiti piano |
| Upgrade paths | GET `/subscription/upgrade-paths` | Opzioni upgrade |
| Metering | GET `/subscription/metering` | Contatori uso |
| Grace period | GET `/subscription/limit-grace` | Stato grace |
| Prossima fattura | GET `/subscription/upcoming-invoice` | Preview Stripe |
| Checkout | POST `/checkout/session` | Creazione sessione Stripe |
| Multi checkout | POST `/checkout/multi` | Acquisto multiplo |
| Cart checkout | POST `/checkout/cart` | Carrello |
| Stripe webhook | POST `/stripe/webhook` (public) | Gestione eventi Stripe |
| Billing portal | POST `/billing/portal` | Link Stripe Customer Portal |
| Validate coupon | POST `/billing/validate-coupon` | Verifica codice sconto |
| Billing data | PUT `/billing/data` | Dati fiscali |
| Fatture Stripe | GET `/billing/invoices` | Storico pagamenti |
| Acquisti | GET `/purchases` | Prodotti acquistati |
| Referral | GET `/referral` + POST validate | Programma affiliazione |

---

### 2.29 Admin Panel (90+ route, 44 Action file backend)

**Frontend**: 30 pagine, 56 file, 10,541 LOC  
**Servizi backend**: `AdminDashboardService` (428 LOC), `AdminBillingService` (370 LOC), `AdminAnalyticsService` (320 LOC), `AdminSystemService` (305 LOC)

**Pagine Admin per Area**:

| Area | Pagine | Funzionalità Principali |
|------|--------|------------------------|
| **Dashboard** | 1 | KPI real-time, stats utenti, revenue |
| **Utenti** | 2 (lista + dettaglio) | 10+ filtri, bulk actions, CSV export, impersonation con reason, note, tag, override limiti, storage usage |
| **Progetti** | 2 | Lista + dettaglio con statistiche |
| **Sottoscrizioni** | 1 | Cancel/extend/changePlan/coupon/trial/grace, trial management |
| **Commerce** | 1 (796 LOC) | CRUD 6 entità: plans, hosting_plans, addons, bundles, products, coupons |
| **Features** | 1 | Toggle feature flags + override per-utente |
| **Analytics** | 1 (~350 LOC) | Funnel, Feature Adoption, Cohort analysis, Churn scores |
| **Revenue** | 1 (~350 LOC) | MRR breakdown, churn trend, top customers |
| **Dunning** | 1 (373 LOC) | Grace period, force retry, cancel, timeline |
| **Platform Invoices** | 1 (642 LOC) | Multi-formato, SDI status, OSS dashboard |
| **AI Escalations** | 1 | Conversazioni escalate, statistiche AI |
| **Chat Admin** | 1 (760 LOC) | Chat real-time con polling, typing, reactions, pin, reply, file |
| **Job Queue** | 1 (438 LOC) | Stats, throughput, filtri, purge/release stale |
| **Cron Status** | 1 | Stato cron + storico esecuzioni |
| **Webhook Log** | 1 | Deliveries, stats, retry |
| **Email Templates** | 1 | CRUD template + preview |
| **Announcements** | 1 | Banner globali per tutti gli utenti |
| **Feedback** | 1 | Feedback utenti, statistiche |
| **Audit Log** | 1 | Log sicurezza completo |
| **System Settings** | 1 | Configurazione globale piattaforma |
| **Infrastructure** | 1 | Server, servizi, monitoring |
| **Communications** | 1 | Messaggi diretti a utenti |
| **Contacts Admin** | 1 | Vista contatti globale con patch |

---

## 3. Funzionalità Trasversali

### 3.1 Bulk Import/Export

| Entità | Import | Export | Formati |
|--------|--------|--------|---------|
| Contatti | ✅ CSV con mapping colonne | ✅ CSV | CSV |
| Task | ✅ CSV/JSON | ✅ CSV/JSON | CSV, JSON |
| Fatture | ✅ Bulk import | ✅ Bulk export | CSV |
| Spese | ✅ Bulk import | ✅ Bulk export | CSV |
| Time entries | ✅ Bulk import | ✅ Bulk export | CSV |
| Deal | ✅ Bulk import | ✅ Bulk export | CSV |
| Transazioni bancarie | ✅ Multi-formato | — | CSV, OFX, QIF, MT940 |
| Piano dei conti | ✅ Template per paese | — | JSON template |

### 3.2 Ricerca Globale
- Endpoint: `GET /search`
- Full-text search cross-entità

### 3.3 Dashboard (15 widget)
```
Dashboard Widgets:
1. Stats generali (utenti, progetti, revenue)
2. PnL Summary (conto economico sintetico)
3. Cash Position (posizione di cassa)
4. Growth Metrics (crescita)
5. Pipeline Summary (funnel CRM)
6. Team Utilization (utilizzo team)
7. My Tasks (task personali)
8. Running Timer (timer attivo)
9. Chat Preview (anteprima chat)
10. Calendar Today (agenda giornaliera)
11. Project Health (salute progetto)
12. Approvals (approvazioni pendenti)
13. Widget Preferences (personalizzazione layout)
14. Portfolio (overview multi-progetto)
```

### 3.4 Onboarding
- 4 step: Welcome → Company → Project → Complete
- Avanzamento progressivo via API

### 3.5 Legal & GDPR
- `GET /legal/consents` — consensi registrati
- `POST /legal/consent` — registra consenso
- `GET /me/data-export` — export GDPR completo
- `DELETE /me` — cancellazione account (right to be forgotten)

### 3.6 Branding & White Label
| Feature | Endpoint | Entitlement |
|---------|---------|-------------|
| Branding base | GET/PUT `/branding` | Incluso |
| Branding pubblico | GET `/branding/public` | Incluso |
| Tema login | GET `/branding/login` | Incluso |
| Tema app | GET `/branding/theme` | Incluso |
| Custom domain | CRUD `/white-label/domain` | `white_label` |
| Branding team | PUT `/branding/team/{userId}` | `white_label` |

---

## 4. Riepilogo Quantitativo Finale

| Dimensione | Valore |
|-----------|--------|
| **Route HTTP backend** | **936** |
| **Action file backend** | **622** |
| **Service file backend** | **103** |
| **Middleware backend** | **20** |
| **Moduli frontend** | **26** |
| **Pagine frontend** | **284** |
| **Store Zustand** | **32** |
| **Pagine admin** | **30** |
| **Tabelle database** | **166 + 5 viste** |
| **Lingue supportate** | **20** |
| **Integrazioni esterne** | **17** |
| **Formati e-invoice** | **5** |
| **Report IVA nazionali** | **5** |
| **Tipi nodo workflow** | **16** |
| **Trigger workflow** | **9** |
| **Widget dashboard** | **15** |
| **Scope API key** | **19** |

---

## Aggiornamento Post-Roadmap (v3.0)

### Nuovi Moduli Funzionali
| Modulo | Descrizione | Fase |
|--------|------------|------|
| **Support AI Chat** | Assistente AI L1 (Gemini 2.0 Flash Lite) con escalation automatica a operatore umano | Fase 3 |
| **Support Diagnostics** | Diagnostica automatizzata account per admin (query DB, stato subscription, errori) | Fase 3 |
| **PageSpeed Insights** | Monitoraggio performance sito con Lighthouse scores e Core Web Vitals | Fase 2 |
| **Google Analytics 4** | Integrazione GA4 per Site Builder con configurazione GDPR-compliant | Fase 2 |
| **VIES VAT Validation** | Validazione IVA europea real-time via VIES SOAP (27 paesi UE) | Fase 1 |
| **SMS Gateway** | Invio SMS transazionali via Twilio/Vonage con OTP verification | Fase 4 |
| **Event System** | EventDispatcher asincrono con listener registrabili per inter-module communication | Fase 7 |
| **Background Jobs** | JobQueueService con backoff esponenziale e dead letter management | Fase 7 |
| **Real-time Updates** | Pusher-based live updates per chat, notifiche, dashboard | Fase 6 |
| **Accessibility** | WCAG 2.1 AA compliance con ARIA labels, RTL support, keyboard navigation | Fase 5 |

