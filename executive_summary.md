# Executive Summary — Kacinka Platform

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale — Per Investitori

---

## Cos'è Kacinka

**Kacinka** è una piattaforma SaaS all-in-one per la gestione aziendale delle PMI europee. Combina fatturazione elettronica multi-paese, CRM, contabilità, gestione progetti, time tracking, open banking, AI assistant e 20+ moduli integrati in un'unica interfaccia moderna — supportando **20 lingue** e **13 sistemi di fatturazione elettronica** nazionali fin dal primo giorno.

---

## Il Problema

Le PMI europee (25+ milioni di imprese, 99,8% del tessuto imprenditoriale UE) utilizzano in media **5-8 tool SaaS separate** per gestire la propria attività: una per fatturazione, una per CRM, una per project management, una per time tracking, una per contabilità. Questo genera:

- **Frammentazione dei dati** e mancanza di una visione unificata
- **Costi crescenti** (€100-500/mese cumulati tra diverse piattaforme)
- **Compliance fiscale complessa**: il mandato UE per la fatturazione elettronica B2B (ViDA 2028) obbligherà milioni di imprese ad adottare sistemi conformi
- **Nessun competitor** offre una piattaforma completa con e-invoicing nativo per 13+ paesi europei in un unico prodotto

---

## La Soluzione

| Metrica | Valore |
|---------|--------|
| **Moduli integrati** | 26 (invoicing, CRM, accounting, expenses, budget, open banking, e-invoicing, OCR, AI assistant, chat, forms, site builder, workflows, calendar, time tracking, reports, analytics, client portal, email marketing, file management, fiscal, API keys, webhooks, task management, custom fields, proposals) |
| **Lingue** | 20 (ar, cs, da, de, el, en, es, fi, fr, hi, it, ja, ko, nl, pl, pt, ro, sv, tr, zh) |
| **E-Invoice paesi** | 13 builder (IT/FatturaPA, DE/XRechnung, FR/ChorusPro+FacturX, ES/SII, PL/KSeF, GR/myDATA, TR/eArşiv, IN/GST, SA/ZATCA, RO/eFactura, PEPPOL BIS 3.0) |
| **AI integrato** | Gemini 2.0 Flash — OCR fatture, AI Assistant, categorizzazione intelligente, cash flow forecasting |
| **Open Banking** | Tink — riconciliazione bancaria automatica |

---

## KPI Tecnici

| Metrica | Valore | Benchmark |
|---------|--------|-----------|
| **Lines of Code** | **519.535** | Equivalente a ~5 anni di sviluppo tradizionale |
| Backend (PHP) | 376.235 LOC — 3.372 file | Slim 4.14, Action-Service-Repository |
| Frontend (React) | 141.584 LOC — 633 file | React 19.2, Vite 7.2, Zustand 5 |
| API Routes | 1.059 endpoint registrati | GET 502 / POST 348 / PUT 99 / DELETE 107 |
| Database | 176 tabelle | MariaDB, schema normalizzato |
| Services | 120 classi — 67.536 LOC | Top: EInvoiceService (4.051 LOC) |
| Actions | 677 classi — 51.141 LOC | 82 namespace API |
| Stores (frontend) | 33 Zustand stores — 7.173 LOC | State management modulare |
| Middleware | 21 classi — 3.370 LOC | Security, rate limiting, RBAC |
| i18n Keys | 7.171 chiavi — 271 KB/locale | 20 file locale completi |
| Test automatizzati | 1.533+ test E2E | 15 blocchi di fix completati |

---

## Tempo e Costo di Sviluppo

| Metrica | Valore |
|---------|--------|
| **Tempo di sviluppo** | **~30 giorni** |
| **Costi infrastruttura totali** | **~€200** |
| **Sviluppatori** | 1 (AI-assisted: GitHub Copilot + Claude) |
| **Produttività** | ~17.300 LOC/giorno vs 50-100 LOC/giorno industry standard |
| **Moltiplicatore** | **173x** la produttività media |

---

## Modello di Business

| Piano | Prezzo/mese | Target |
|-------|-------------|--------|
| **Starter** | €0 (trial 14gg) | Freelancer, test drive |
| **Pro** | €29 | Liberi professionisti, micro-imprese |
| **Pro+** | TBD | PMI in crescita |
| **Elite** | TBD | Team strutturati |
| **Enterprise** | €199 | Aziende multi-sede, agency |

Modello subscription con trial + carta obbligatoria. Margine lordo stimato **>95%** (costi infra ~€200 totali).

---

## Competitor vs Kacinka

| Feature | Kacinka | QuickBooks ($38) | Holded (€14-49) | Odoo (€31+) | FreshBooks ($21) | Invoice Ninja ($0-15) |
|---------|---------|-------------------|-----------------|-------------|-------------------|----------------------|
| Lingue | **20** | 5 | 4 | 30+ | 3 | 10+ |
| E-Invoice paesi | **13** | 0 | 2 (ES) | 5-8 | 0 | 1 (PEPPOL) |
| AI Assistant | **✅** | ✅ (Intuit Assist) | ❌ | ❌ | ✅ (basic) | ❌ |
| Open Banking | **✅** (Tink) | ✅ (Plaid) | ✅ | ❌ | ❌ | ❌ |
| CRM integrato | **✅** | ❌ | ✅ | ✅ | ❌ | ❌ |
| Project Management | **✅** | ❌ | ✅ | ✅ | ❌ | ❌ |
| Time Tracking | **✅** | ✅ | ✅ | ✅ | ✅ | ❌ |
| Site Builder | **✅** | ❌ | ❌ | ✅ | ❌ | ❌ |
| Prezzo entry | **€0** | $38 | €14.50 | €31 | $21 | $0 |
| Open Source | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ |

---

## Why Now

1. **EU ViDA Mandate 2028**: La fatturazione elettronica B2B diventerà obbligatoria in tutta l'UE — milioni di PMI dovranno adottare sistemi conformi
2. **PSD2/PSD3 Open Banking**: Riconciliazione bancaria automatica diventa standard
3. **AI Disruption**: Le PMI si aspettano AI integrato (49% dei buyer secondo Capterra 2025)
4. **Tool Consolidation Trend**: Le PMI cercano di ridurre il numero di tool SaaS separate
5. **Solo 20% delle PMI UE** è sostanzialmente digitalizzata — enorme spazio di crescita

---

## Status Attuale

- ✅ MVP funzionante con 26 moduli, 1.059 API routes, 176 tabelle DB
- ✅ 15/15 blocchi di fix completati, 1.533+ test passati
- ✅ E-invoicing per 13 paesi implementato (builder + validation)
- ✅ 20 lingue con 7.171 chiavi tradotte per locale
- ✅ AI (Gemini) integrato per OCR, assistente, categorizzazione
- ✅ Open Banking (Tink) con riconciliazione automatica
- ⏳ Produzione: necessarie credenziali Stripe/Tink/SDI production
- ⏳ Infrastruttura: migrazione da shared hosting a VPS per scalabilità

---

## Next Steps

1. **Go-to-market Italia** (Q2 2026): mercato nativo, SDI già integrato
2. **Espansione Spagna + Germania** (Q3 2026): SII + XRechnung pronti
3. **Mobile app** (Q4 2026): React Native
4. **Pan-EU** (2027): 20 paesi con localizzazione completa
5. **Fundraising**: seed round per accelerare crescita e team

---

*Documento generato automaticamente dall'analisi diretta del codebase Kacinka. Tutte le metriche sono verificabili nel repository.*
