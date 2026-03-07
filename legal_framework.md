# Legal Framework

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Overview

Kacinka opera come piattaforma SaaS B2B rivolta a PMI europee. Questo documento analizza il framework legale, i documenti pubblicati, e le lacune nella compliance legale.

---

## 2. Published Legal Documents

### 2.1 Inventory

| Documento | File | URL | Lingua |
|-----------|------|-----|--------|
| **Privacy Policy** | `privacy.html` | kacinka.app/privacy | ⚠️ Da verificare (IT/EN) |
| **Terms of Service** | `terms.html` | kacinka.app/terms | ⚠️ Da verificare (IT/EN) |
| **Cookie Policy** | `cookies.html` | kacinka.app/cookies | ⚠️ Da verificare (IT/EN) |
| **404 Page** | `404.html` / `404.php` | — | IT |
| **sitemap.xml** | `sitemap.xml` + 20 language variants | — | Multi-lang |
| **robots.txt** | `robots.txt` | — | Standard |
| **site.webmanifest** | `site.webmanifest` | — | PWA manifest |

### 2.2 Missing Legal Documents

| Documento | Necessità | Priorità |
|-----------|----------|----------|
| **Acceptable Use Policy (AUP)** | Definire uso accettabile della piattaforma | 🔴 Alta |
| **Data Processing Agreement (DPA)** | Per clienti enterprise che sono data controller | 🔴 Alta |
| **Service Level Agreement (SLA)** | Uptime e supporto garantiti | 🟡 Media |
| **Refund Policy** | Condizioni di rimborso | 🔴 Alta |
| **Disclaimers** | Limitazione responsabilità fiscale/contabile | 🔴 Alta |
| **Sub-processor List** | Lista pubblica processor terzi (GDPR requirement) | 🔴 Alta |
| **Security Whitepaper** | Per enterprise due diligence | 🟢 Bassa |
| **Compliance Certifications** | SOC 2, ISO 27001, etc. | 🟢 Bassa |

---

## 3. Terms of Service Analysis

### 3.1 Key Clauses (Recommended)

| Clausola | Presente | Note |
|----------|---------|------|
| Definizioni (piattaforma, utente, servizio) | ⚠️ Da verificare | — |
| Registrazione e account | ⚠️ Da verificare | — |
| Piani e pricing | ⚠️ Da verificare | Deve riflettere 5 piani |
| Pagamenti e fatturazione | ⚠️ Da verificare | Stripe come processore |
| Cancellazione e rimborsi | ⚠️ Da verificare | Trial 14gg, grace period |
| Proprietà intellettuale | ⚠️ Da verificare | IP utente vs IP piattaforma |
| Dati utente (ownership) | ⚠️ Da verificare | Utente proprietario dei suoi dati |
| Limitazione responsabilità | ⚠️ Da verificare | Disclaimer su calcoli fiscali |
| Indennizzo | ⚠️ Da verificare | — |
| Forza maggiore | ⚠️ Da verificare | — |
| Legge applicabile e foro competente | ⚠️ Da verificare | IT law / EU consumer protection |
| Modifiche ai termini | ⚠️ Da verificare | Notifica preventiva |
| API Terms | ❌ Mancante | Condizioni uso API |

### 3.2 SaaS-Specific Considerations

| Aspetto | Raccomandazione |
|---------|----------------|
| **Data Portability** | Già implementato (GDPR export), menzionare nei termini |
| **Service Availability** | Definire SLA target (99.5%) o "best effort" |
| **Data Retention** | Post-cancellazione: definire periodo conservazione (30 giorni) |
| **Fiscal Disclaimer** | "Kacinka non fornisce consulenza fiscale" — CRITICO |
| **AI Disclaimer** | "Le risposte AI non sostituiscono consulenza professionale" |
| **E-Invoice Disclaimer** | "L'utente è responsabile della correttezza dei dati fiscali" |

---

## 4. Privacy Policy Analysis

### 4.1 GDPR Requirements Checklist

| Requisito (Art. 13/14) | Presente | Note |
|------------------------|---------|------|
| Identità e contatti del titolare | ⚠️ Da verificare | — |
| Contatti DPO | ❌ | DPO non nominato |
| Finalità del trattamento | ⚠️ Da verificare | — |
| Base giuridica per ogni finalità | ⚠️ Da verificare | — |
| Categorie di dati personali | ⚠️ Da verificare | — |
| Destinatari/categorie destinatari | ⚠️ Da verificare | Sub-processors |
| Trasferimenti extra-UE | ⚠️ Da verificare | USA (Stripe, Google, OpenAI, Sentry) |
| Periodo di conservazione | ⚠️ Da verificare | — |
| Diritti dell'interessato | ⚠️ Da verificare | Art. 15-22 |
| Diritto di reclamo al Garante | ⚠️ Da verificare | — |
| Profilazione e decisioni automatizzate | ⚠️ Da verificare | AI usage |

### 4.2 Multi-Language Compliance

| Lingua | Privacy | Terms | Cookie |
|--------|---------|-------|--------|
| Italiano | ⚠️ | ⚠️ | ⚠️ |
| English | ⚠️ | ⚠️ | ⚠️ |
| Other 18 lingue | ❌ | ❌ | ❌ |

> **Nota**: Per vendere in ciascun paese, le condizioni legali dovrebbero essere nella lingua locale. Almeno IT + EN + DE + FR + ES per i primi mercati.

---

## 5. Cookie Policy Analysis

### 5.1 Current Cookie Usage

| Cookie | Tipo | Durata | Consenso Art. 5.3 ePrivacy |
|--------|------|--------|---------------------------|
| `access_token` | Tecnico/necessario | 24h | ✅ Esente (strettamente necessario) |
| `csrf_token` | Tecnico/necessario | 24h | ✅ Esente |
| `refresh_token` | Tecnico/necessario | Configurabile | ✅ Esente |

### 5.2 Cookie Compliance

| Aspetto | Status |
|---------|--------|
| Solo cookie tecnici | ✅ |
| Nessun cookie analytics | ✅ |
| Nessun cookie marketing | ✅ |
| Cookie banner | ⚠️ Presente su marketing site |
| CMP (Consent Management Platform) | ❌ Non integrato |

> **Nota**: Poiché Kacinka usa SOLO cookie strettamente necessari, un cookie banner informativo (non blocking) è sufficiente secondo ePrivacy Directive Art. 5.3 e GDPR Recital 32.

---

## 6. Consent Management

### 6.1 Implemented

| Tipo Consenso | Endpoint | Tracciamento |
|--------------|----------|-------------|
| `privacy_policy` | POST /legal/consent | ✅ Versioned, IP, UA, timestamp |
| `terms_of_service` | POST /legal/consent | ✅ Versioned |
| `marketing` | POST /legal/consent | ✅ Separate consent |
| `cookie` | POST /legal/consent | ✅ Separate consent |
| `informativa` | POST /legal/consent | ✅ IT-specific |

### 6.2 Consent UI Flow

```
Registration → Accept Terms + Privacy (required)
                → Marketing consent (optional, unchecked)
                → Cookie informational banner

Settings → Manage consents
         → Withdraw marketing consent
         → Email unsubscribe link
```

---

## 7. Intellectual Property

### 7.1 Platform IP

| Asset | Protezione |
|-------|-----------|
| Source code | Copyright © 2025-2026 |
| "Kacinka" brand | ⚠️ Trademark da registrare |
| UI/UX design | Copyright |
| E-invoice builders | Copyright + trade secret |
| AI system prompts | Trade secret |
| Database schema | Copyright |

### 7.2 Open Source Dependencies

| Tipo | Licenze Predominanti |
|------|---------------------|
| PHP packages | MIT, Apache 2.0, BSD |
| npm packages | MIT, ISC, BSD |
| Compatibility | ✅ Tutte compatibili con uso commerciale |

### 7.3 User Data Ownership

| Principio | Implementazione |
|-----------|----------------|
| User owns their data | ✅ Data export (GDPR Art. 20) |
| Platform processes, doesn't own | ✅ Tipo "data processor" per dati utente |
| Termini chiari su IP content utente | ⚠️ Da verificare nei Terms |

---

## 8. Regulatory Compliance per Market

### 8.1 Italy (Primary Market)

| Regolamento | Applicazione | Status |
|------------|-------------|--------|
| **GDPR** (Reg. UE 2016/679) | Privacy e dati | ⚠️ Parziale |
| **D.Lgs. 196/2003** (Codice Privacy aggiornato) | Privacy IT | ⚠️ |
| **D.Lgs. 82/2005** (CAD) | Conservazione sostitutiva | ❌ |
| **DPR 633/1972** | IVA e fatturazione | ✅ (FatturaPA) |
| **D.Lgs. 127/2015** | Fatturazione elettronica | ✅ (SDI) |
| **D.Lgs. 70/2003** | E-commerce | ⚠️ Da verificare |

### 8.2 EU-Wide

| Regolamento | Applicazione | Status |
|------------|-------------|--------|
| **GDPR** | Privacy | ⚠️ Parziale (vedi gdpr_compliance.md) |
| **ePrivacy Directive** | Cookie & communications | ✅ (solo cookie tecnici) |
| **PSD2** | Open Banking | ✅ (Tink integration) |
| **ViDA** (2028) | E-invoicing mandate | ⚠️ In preparazione |
| **Digital Services Act** | Platform obligations | ⚠️ Da valutare applicabilità |
| **AI Act** | AI regulation | ⚠️ Da valutare (risk category) |

---

## 9. AI Act Implications

### 9.1 Risk Classification

| AI Feature | Categoria di Rischio | Note |
|-----------|---------------------|------|
| **AI Chat Assistant** | Rischio limitato | Obbligo di trasparenza (Art. 52) — user deve sapere che parla con AI |
| **OCR Receipt Scanning** | Rischio minimo | Automazione semplice |
| **AI Support Agent** | Rischio limitato | Obbligo trasparenza |
| **AI Financial Suggestions** | ⚠️ Da valutare | Se influenza decisioni finanziarie |

### 9.2 Compliance Actions

| Azione | Priorità |
|--------|----------|
| Label chiaro "Powered by AI" su tutte le risposte AI | 🔴 Alta |
| Informativa su come funziona l'AI nell'app | 🟡 Media |
| Assessment completo AI Act risk category | 🟡 Media |
| Human-in-the-loop per decisioni critiche (già presente via escalation) | ✅ Già implementato |

---

## 10. Gap Analysis & Roadmap

### Pre-Launch Critical

| # | Doc/Azione | Priorità | Effort |
|---|-----------|----------|--------|
| 1 | Aggiornare Privacy Policy (GDPR full compliance) | 🔴 | 8h |
| 2 | Aggiornare Terms of Service (SaaS-specific, disclaimers fiscali) | 🔴 | 8h |
| 3 | Creare Acceptable Use Policy | 🔴 | 4h |
| 4 | Creare DPA template per enterprise | 🔴 | 4h |
| 5 | Creare Refund Policy | 🔴 | 2h |
| 6 | Pubblicare Sub-processor List | 🔴 | 2h |
| 7 | AI transparency labels | 🔴 | 2h |
| 8 | Fiscal disclaimer prominente | 🔴 | 1h |
| 9 | Registrare trademark "Kacinka" | 🟡 | External |

### Post-Launch

| # | Azione | Priorità |
|---|--------|----------|
| 10 | Tradurre legal docs in DE, FR, ES | 🟡 |
| 11 | API Terms of Use | 🟡 |
| 12 | SLA formale | 🟡 |
| 13 | AI Act compliance assessment | 🟡 |
| 14 | ISO 27001 o SOC 2 preparation | 🟢 |

---

*Documento generato il 2026-03-07. Analisi basata su file legali pubblicati (privacy.html, terms.html, cookies.html), RecordConsentAction.php, GetConsentsAction.php, e struttura codebase.*
