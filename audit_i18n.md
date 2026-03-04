# Audit Internazionalizzazione (i18n) — Kacinka.it Platform

> **Data**: Marzo 2026  
> **Versione**: 2.0 — Rigenerato da analisi completa del codice sorgente  
> **Scope**: Analisi esaustiva dell'architettura, copertura e qualità delle traduzioni  
> **Metodo**: Lettura diretta di tutti i file locale, script di traduzione, config i18n  

---

## 1. Panoramica Quantitativa

### 1.1 Metriche Globali

| Dimensione | Valore |
|-----------|--------|
| **Lingue supportate** | **20** |
| **Lingue base** (it, en) | Scritte manualmente |
| **Lingue batch 1** (de, es, fr, pt) | Tradotte + revisionate |
| **Lingue batch 2** (cs, nl) | Tradotte in batch |
| **Lingue batch 3** (ar, da, el, fi, hi, ja, ko, pl, ro, sv, tr, zh) | Tradotte via chunk pipeline |
| **Chiavi leaf app (per lingua)** | **6,184** |
| **Namespace app** | **62** |
| **Chiavi marketing (per lingua)** | **1,942** |
| **Dimensione totale locales app** | **7.4 MB** (20 file) |
| **Dimensione totale locales marketing** | **3.2 MB** (20 file) |
| **File i18n di traduzione totali** | **~200+** (20 app + 20 marketing + 78 chunks + script) |
| **Blog articoli localizzati** | **12 × 20 lingue = 240 file PHP** |
| **Sitemap per lingua** | **20** (sitemap-{lang}.xml) |

### 1.2 Lista Completa Lingue

| # | Codice | Lingua | App (LOC) | App (KB) | Marketing (LOC) | Marketing (KB) | Blog |
|---|--------|--------|-----------|----------|-----------------|---------------|------|
| 1 | `it` | Italiano (base) | 7,538 | 499.0 | 1,944 | 150.8 | 12 articoli |
| 2 | `en` | English (base) | 7,534 | 482.7 | 1,944 | 142.8 | 12 articoli |
| 3 | `de` | Deutsch | 7,466 | 504.6 | 1,929 | 157.1 | 12 articoli |
| 4 | `es` | Español | 7,477 | 502.2 | 1,925 | 155.0 | 12 articoli |
| 5 | `fr` | Français | 7,466 | 515.3 | 1,925 | 159.6 | 12 articoli |
| 6 | `pt` | Português | 7,466 | 503.3 | 1,925 | 153.4 | 12 articoli |
| 7 | `cs` | Čeština | 7,516 | 272.0 | 1,925 | 151.0 | 12 articoli |
| 8 | `nl` | Nederlands | 7,516 | 267.3 | 1,925 | 149.3 | 12 articoli |
| 9 | `ar` | العربية | 7,528 | 327.3 | 1,925 | 187.6 | 12 articoli |
| 10 | `da` | Dansk | 7,528 | 262.3 | 1,925 | 146.6 | 12 articoli |
| 11 | `el` | Ελληνικά | 7,528 | 385.8 | 1,925 | 220.0 | 12 articoli |
| 12 | `fi` | Suomi | 7,528 | 271.0 | 1,925 | 151.8 | 12 articoli |
| 13 | `hi` | हिन्दी | 7,528 | 424.3 | 1,925 | 251.9 | 12 articoli |
| 14 | `ja` | 日本語 | 7,528 | 304.9 | 1,925 | 164.6 | 12 articoli |
| 15 | `ko` | 한국어 | 7,528 | 280.0 | 1,925 | 151.6 | 12 articoli |
| 16 | `pl` | Polski | 7,528 | 273.5 | 1,925 | 154.0 | 12 articoli |
| 17 | `ro` | Română | 7,528 | 274.5 | 1,925 | 154.9 | 12 articoli |
| 18 | `sv` | Svenska | 7,528 | 264.5 | 1,925 | 147.1 | 12 articoli |
| 19 | `tr` | Türkçe | 7,528 | 271.7 | 1,925 | 150.8 | 12 articoli |
| 20 | `zh` | 中文 | 7,528 | 248.2 | 1,925 | 133.0 | 12 articoli |

---

## 2. Architettura i18n Frontend (App)

### 2.1 Stack Tecnologico

```
Frontend i18n Stack:
├── i18next (core)
├── react-i18next (binding React)
├── i18next-browser-languagedetector (language detection)
├── i18next-http-backend (lazy loading via HTTP)
└── Suspense (React 19 — attende caricamento locale)
```

### 2.2 Configurazione (app/src/i18n/index.js — 38 LOC)

```javascript
// Configurazione reale dal codice:
{
  fallbackLng: 'it',                           // Italiano come fallback
  supportedLngs: ['it','en','de','es','fr','pt','cs','nl','ar','da','el','fi','hi','ja','ko','pl','ro','sv','tr','zh'],
  ns: ['translation'],                          // Singolo namespace flat
  defaultNS: 'translation',
  backend: {
    loadPath: '/locales/{{lng}}.json',           // HTTP on-demand
  },
  detection: {
    order: ['localStorage', 'navigator'],        // Priorità: localStorage → browser
    lookupLocalStorage: 'kacinka_lang',           // Chiave localStorage
    caches: ['localStorage'],                    // Persisti scelta
  },
  react: {
    useSuspense: true,                           // React Suspense boundary
  },
}
```

**Architettura di loading**:
1. App monta → `LanguageDetector` rileva lingua da `localStorage('kacinka_lang')` o `navigator.language`
2. `HttpBackend` fetch `GET /locales/{lang}.json` (singolo file ~250-515 KB)
3. `React.Suspense` mostra fallback UI durante il caricamento
4. Cambio lingua → nuovo fetch HTTP → aggiornamento reattivo via `react-i18next`

### 2.3 Struttura Namespace (62 namespace, 6,184 chiavi leaf)

| # | Namespace | Chiavi | Descrizione |
|---|-----------|--------|------------|
| 1 | `siteBuilder` | 406 | Builder siti web (il più grande) |
| 2 | `banking` | 311 | Riconciliazione bancaria |
| 3 | `crm` | 263 | CRM completo |
| 4 | `invoices` | 261 | Fatturazione V2 |
| 5 | `settings` | 230 | Impostazioni utente/progetto |
| 6 | `emailMarketing` | 219 | Email marketing |
| 7 | `clientPortal` | 200 | Portale clienti |
| 8 | `accounting` | 194 | Contabilità |
| 9 | `fiscal` | 190 | Fiscale/IVA |
| 10 | `expenses` | 181 | Gestione spese |
| 11 | `contacts` | 175 | Rubrica contatti |
| 12 | `workflows` | 175 | Automazioni |
| 13 | `pricing` | 171 | Piani e prezzi |
| 14 | `tasks` | 151 | Task management |
| 15 | `files` | 151 | File & documenti |
| 16 | `budget` | 149 | Budget & centri costo |
| 17 | `calendar` | 143 | Calendario |
| 18 | `forms` | 142 | Form builder |
| 19 | `webhooks` | 141 | Webhook |
| 20 | `analytics` | 139 | Analytics & KPI |
| 21 | `chat` | 130 | Chat |
| 22 | `timeTracking` | 129 | Tracciamento tempo |
| 23 | `dashboard` | 119 | Dashboard |
| 24 | `auth` | 116 | Autenticazione |
| 25 | `billing` | 108 | Billing Stripe |
| 26 | `apiKeys` | 100 | API Keys |
| 27 | `ocrReceipts` | 93 | OCR ricevute |
| 28 | `einvoice` | 84 | Fattura elettronica |
| 29 | `projectSettings` | 82 | Config progetto |
| 30 | `common` | 81 | Stringhe comuni |
| 31 | `services` | 80 | Pagina servizi |
| 32 | `upgrade` | 68 | Upgrade gate |
| 33 | `onboarding` | 59 | Onboarding wizard |
| 34 | `crmAccounting` | 58 | Bridge CRM-Contabilità |
| 35 | `commerce` | 57 | Commercio admin |
| 36 | `compliance` | 49 | Compliance fiscale |
| 37 | `portfolio` | 45 | Portfolio |
| 38 | `integrations` | 45 | Integrazioni Google |
| 39 | `aiCtx` | 45 | Contesto AI |
| 40 | `notifications` | 43 | Notifiche |
| 41 | `merge_parts.cjs` | — | Script (non namespace) |
| 42 | `commandPalette` | 38 | Palette comandi |
| 43 | `status` | 37 | Status page |
| 44 | `projectDashboard` | 36 | Dashboard progetto |
| 45 | `aiAssistant` | 34 | AI assistant |
| 46 | `branding` | 32 | Branding |
| 47 | `workspace` | 31 | Workspace |
| 48 | `coreTeam` | 29 | Team core |
| 49 | `modules` | 28 | Nomi moduli |
| 50 | `nav` | 26 | Navigazione |
| 51 | `wizard` | 25 | Wizard setup |
| 52 | `crud` | 25 | Operazioni CRUD |
| 53 | `customFields` | 24 | Campi personalizzati |
| 54 | `referral` | 24 | Referral |
| 55 | `invite` | 19 | Inviti |
| 56 | `myTasks` | 13 | Task personali |
| 57 | `feedback` | 12 | Feedback |
| 58 | `join` | 9 | Join progetto |
| 59 | `banners` | 9 | Banner |
| 60 | `openBanking` | 9 | Open Banking |
| 61 | `plan` | 2 | Piano |
| 62 | `ui` | 2 | UI base |

### 2.4 Formato Chiavi App

Struttura gerarchica JSON nested (non flat):
```json
{
  "auth": {
    "login": {
      "title": "Accedi",
      "subtitle": "Bentornato su Kacinka",
      "email": "Email",
      "errors": {
        "invalid": "Email o password non corretti",
        "locked": "Account temporaneamente bloccato..."
      }
    },
    "totp": { ... },
    "register": { ... }
  },
  "invoices": {
    "list": { ... },
    "form": { ... },
    "status": { ... }
  }
}
```

**Pattern di chiave**: `namespace.section.key` → `t('auth.login.title')`  
**Interpolazione**: `{{variable}}` → `t('auth.login.retryIn', { seconds: 30 })`  
**Pluralizzazione**: Standard i18next (non rilevata estensivamente nel codice)  
**HTML nelle traduzioni**: Sì, usa `<span>` per gradients (es. `"text-gradient"`)

---

## 3. Architettura i18n Marketing (Sito Pubblico)

### 3.1 Sistema Marketing — Chiavi Flat

A differenza dell'app (JSON nested), il marketing usa **chiavi flat** con prefisso underscore:

```json
{
  "404_btn_home": "← Torna alla Homepage",
  "about_hero_title": "Costruiamo il <span class=\"text-gradient\">futuro delle PMI</span>",
  "svc_accounting_meta_title": "Contabilità Online — Kacinka",
  "priv_title": "Informativa sulla Privacy",
  "tos_title": "Termini di Servizio"
}
```

### 3.2 Distribuzione Chiavi Marketing per Dominio (1,942 chiavi)

| Prefisso | Chiavi | Contenuto |
|----------|--------|-----------|
| `svc_*` | 881 | **Pagine servizi** (45% delle chiavi) — accounting, CRM, AI, e-invoice, email marketing, site builder, project management, client portal, open banking, time tracking |
| `priv_*` | 151 | Privacy Policy |
| `tos_*` | 148 | Terms of Service |
| `cook_*` / `cookie_*` | 98 | Cookie Policy |
| `integrations_*` | 75 | Pagina integrazioni |
| `security_*` | 65 | Pagina sicurezza |
| `blog_*` | 61 | Blog UI (categorie, CTA, filtri) |
| `about_*` | 49 | Chi Siamo |
| `plan_*` / `pricing_*` | 78 | Piani & Pricing |
| `contact_*` | 41 | Contatti |
| `platform_*` | 34 | Pagina piattaforma |
| `feat_*` / `features_*` | 29 | Feature list |
| `footer_*` | 23 | Footer globale |
| `show_*` | 22 | Showcase |
| `faq*` | 21+20 | FAQ (10 domande con q/a) |
| `docs_*` | 20 | Documentazione |
| `whoisitfor_*` | 18 | Target audience |
| `nav_*` | 16 | Navigazione header |
| `compare_*` | 15 | Comparazione competitor |
| `hero_*` | 10 | Hero sections |
| `testimonial*` | 17 | 5 testimonial con name/role/text |
| `howit*` | 9 | How it works |
| `pain*` | 9 | Pain points |
| `stats*` | 8 | Statistiche homepage |
| Altro | 30+ | CTA, trust badge, meta, legal, lang |

### 3.3 Rendering Marketing

Il sito marketing usa **PHP con template engine custom**:
- File: `_engine/pages/*.php` (13 template)
- Rendering: `$t('chiave')` → legge da `_engine/lang/{lang}.json`
- URL: `/{lang}/servizi/contabilita` → carica `it.json` + renderizza `servizi.php`
- Routing: `_engine/router.php` matched su `/{lang}/...` pattern

---

## 4. Pipeline di Traduzione

### 4.1 Workflow Traduzione App

```
   ┌─────────────────────────────────────────────────────┐
   │  1. Sviluppo: aggiungere chiavi in it.json / en.json │
   ├─────────────────────────────────────────────────────┤
   │  2. Split: _split_en.cjs → 5 chunk (~1,200 key/each)│
   ├─────────────────────────────────────────────────────┤
   │  3. Traduci: chunk per chunk (AI o manuale)          │
   │     → _chunks/chunk1_{lang}.json ... chunk6_{lang}   │
   ├─────────────────────────────────────────────────────┤
   │  4. Merge: _merge_all.cjs → {lang}.json completo    │
   ├─────────────────────────────────────────────────────┤
   │  5. Deploy: copia in app/public/locales/{lang}.json  │
   └─────────────────────────────────────────────────────┘
```

### 4.2 Script Pipeline App

| Script | LOC | Funzione |
|--------|-----|---------|
| `_split_en.cjs` | 50 | Divide `en.json` in 5 chunk bilanciati per chiavi |
| `_merge_all.cjs` | 70 | Unifica 6 chunk per lingua in file finale via deep merge |
| `build_all_locales.cjs` | 84 | Orchestratore batch per tutte le lingue |
| `build_locale.cjs` | 80 | Build di una singola lingua |
| `clone_en_for_locales.cjs` | 27 | Clona en.json come base per nuove lingue |
| `extract_keys.cjs` | 36 | Estrae chiavi per verifica completezza |
| `merge_parts.cjs` | 41 | Merge part files |
| `split_en.cjs` | 27 | Variante split |
| `apply_translations.cjs` | 52 | Applica traduzioni ricevute |

**Chunk breakdown di en.json**:
| Chunk | LOC | KB | Namespace inclusi |
|-------|-----|----|------------------|
| `_en_chunk_1` | 1,622 | 52.5 | auth → dashboard (primi ~1,240 key) |
| `_en_chunk_2` | 1,564 | 49.5 | portfolio → crm (~1,200 key) |
| `_en_chunk_3` | 1,589 | 51.3 | commerce → contacts (~1,250 key) |
| `_en_chunk_4` | 1,632 | 51.8 | analytics → forms (~1,280 key) |
| `_en_chunk_5` | 1,129 | 46.4 | ocrReceipts → compliance (rimanenti ~1,200 key) |

**Chunks prodotti**: 78 file in `_chunks/` (6 chunk × ~13 lingue batch 3)

### 4.3 Script Pipeline Marketing

| Script | LOC | Funzione |
|--------|-----|---------|
| `patch_translations.js` | 723 | **Il più grande** — patch multi-lingua per footer, nav, piani, servizi |
| `update_lang_plans.js` | 419 | Aggiorna traduzioni piani tariffari per tutte le lingue |
| `_inject_cookie_i18n.js` | 378 | Inietta traduzioni cookie policy nelle 20 lingue |
| `_translate_blog_batch3.js` | 409 | Traduce articoli blog batch 3 (6 lingue) |
| `_translate_blog_batch2.js` | 405 | Traduce articoli blog batch 2 (6 lingue) |
| `_translate_blog_batch1.js` | 399 | Traduce articoli blog batch 1 (8 lingue) |
| `_merge_blog.js` | 191 | Unifica traduzioni blog per lingua |
| `_inject_legal_i18n.js` | 142 | Inietta privacy/ToS tradotti |
| `update_blog_2026.js` | 129 | Aggiornamento blog 2026 |
| `_inject_withdrawal_i18n.js` | 93 | Inietta diritto di recesso tradotto |
| `extract_i18n.js` | 74 | Estrae chiavi per traduzione |
| `update_blog_i18n.js` | 52 | Aggiorna i18n blog |
| `fix_encoding.js` | variabile | Fix encoding UTF-8 |
| `_extract_blog_en.js` | 12 | Estrae contenuti blog EN |
| `_verify_blog.js` | 16 | Verifica consistenza blog |

**LOC totale script marketing**: ~3,500+

### 4.4 Script Build (Directory _build/)

| Script | LOC | Funzione i18n |
|--------|-----|--------------|
| `fix_footer_translations.js` | 100+ | Aggiorna traduzioni footer per tutte le 20 lingue in batch |
| `nav_footer_coherence.js` | — | Verifica coerenza navigazione/footer cross-lingua |
| `seo_enhance_12_6.js` | — | SEO con meta tag multilingua |

---

## 5. Contenuti Blog Multilingua

### 5.1 Struttura

```
_engine/blog/content/
├── ar/ (12 articoli PHP)
├── cs/ (12 articoli PHP)
├── da/ (12 articoli PHP)
├── de/ (12 articoli PHP)
├── el/ (12 articoli PHP)
├── en/ (12 articoli PHP — sorgente)
├── es/ (12 articoli PHP)
├── fi/ (12 articoli PHP)
├── fr/ (12 articoli PHP)
├── hi/ (12 articoli PHP)
├── it/ (12 articoli PHP — sorgente)
├── ja/ (12 articoli PHP)
├── ko/ (12 articoli PHP)
├── nl/ (12 articoli PHP)
├── pl/ (12 articoli PHP)
├── pt/ (12 articoli PHP)
├── ro/ (12 articoli PHP)
├── sv/ (12 articoli PHP)
├── tr/ (12 articoli PHP)
└── zh/ (12 articoli PHP)
```

**Totale**: 240 file PHP (12 articoli × 20 lingue)

### 5.2 Articoli Blog (12 articoli originali EN)

| # | Slug | Tema | Categoria |
|---|------|------|-----------|
| 1 | `artificial-intelligence-small-business` | AI per PMI: 10 applicazioni | AI |
| 2 | `business-accounting-automation` | Automazione contabile (-70% lavoro manuale) | Contabilità |
| 3 | `business-data-security-cloud` | Sicurezza dati cloud | Sicurezza |
| 4 | `business-kpis-metrics-to-track` | 25 KPI essenziali | Strategia |
| 5 | `business-software-integration` | Integrazione software | Integrazioni |
| 6 | `digital-transformation-sme-guide` | Trasformazione digitale PMI | Strategia |
| 7 | `e-invoicing-complete-guide` | Guida e-invoicing 2026 | Fatturazione |
| 8 | `gdpr-compliance-business-software` | GDPR & software gestionale | Compliance |
| 9 | `how-to-choose-business-crm` | Come scegliere un CRM | CRM |
| 10 | `open-banking-european-businesses` | Open Banking per aziende | Fintech |
| 11 | `saas-vs-on-premise-comparison` | SaaS vs On-Premise | Strategia |
| 12 | `web-development-trends-2026` | Trend web dev 2026 | Web Dev |

**Categorie blog**: Contabilità, AI, Compliance, CRM, Fintech, Integrazioni, Fatturazione, Sicurezza, Strategia, Sviluppo Web

### 5.3 Pipeline Traduzione Blog

```
   ┌──────────────────────────────────────────┐
   │ 1. Scrivi articolo in en/ e it/          │
   ├──────────────────────────────────────────┤
   │ 2. _extract_blog_en.js → estrai chiavi   │
   ├──────────────────────────────────────────┤
   │ 3. _translate_blog_batch1.js (8 lingue)  │
   │    _translate_blog_batch2.js (6 lingue)  │
   │    _translate_blog_batch3.js (6 lingue)  │
   ├──────────────────────────────────────────┤
   │ 4. _merge_blog.js → unifica per lingua   │
   ├──────────────────────────────────────────┤
   │ 5. _verify_blog.js → verifica copertura  │
   └──────────────────────────────────────────┘
```

---

## 6. SEO Multilingua

### 6.1 Sitemap per Lingua

20 file sitemap individuali + 1 sitemap index:

| File | Tipo |
|------|------|
| `sitemap.xml` | Sitemap index (master) |
| `sitemap-ar.xml` | Arabo |
| `sitemap-cs.xml` | Ceco |
| `sitemap-da.xml` | Danese |
| `sitemap-de.xml` | Tedesco |
| `sitemap-el.xml` | Greco |
| `sitemap-en.xml` | Inglese |
| `sitemap-es.xml` | Spagnolo |
| `sitemap-fi.xml` | Finlandese |
| `sitemap-fr.xml` | Francese |
| `sitemap-hi.xml` | Hindi |
| `sitemap-it.xml` | Italiano |
| `sitemap-ja.xml` | Giapponese |
| `sitemap-ko.xml` | Coreano |
| `sitemap-nl.xml` | Olandese |
| `sitemap-pl.xml` | Polacco |
| `sitemap-pt.xml` | Portoghese |
| `sitemap-ro.xml` | Rumeno |
| `sitemap-sv.xml` | Svedese |
| `sitemap-tr.xml` | Turco |
| `sitemap-zh.xml` | Cinese |

### 6.2 Generazione Sitemap

- Script: `_engine/generate-sitemaps.php`
- Template: `_engine/sitemap-lang.php` + `_engine/sitemap-index.php`
- Ogni sitemap contiene URL localizzati per: homepage, servizi, chi siamo, contatti, blog, pricing, etc.
- Tag `<xhtml:link rel="alternate" hreflang="{lang}">` per ogni pagina

### 6.3 Meta Tag SEO per Lingua

Ogni pagina marketing ha chiavi specifiche per SEO:
```json
{
  "about_meta_title": "Chi Siamo — Kacinka",
  "about_meta_desc": "Scopri il team e la missione di Kacinka...",
  "svc_accounting_meta_title": "Contabilità Online — Kacinka",
  "svc_accounting_meta_desc": "Software di contabilità..."
}
```

Pattern per tutte le 13+ pagine marketing × 20 lingue.

---

## 7. Admin Panel — Stato i18n

### 7.1 Localizzazione Admin

**L'admin panel NON è localizzato**:
- Nessun file locale trovato in `admin/src/`
- Nessuna configurazione i18next nell'admin
- UI hardcoded in inglese/italiano misto
- Dashboard, tabelle, form tutti con testo statico

**Implicazione**: L'admin è disponibile solo in una lingua (italiana/inglese misto). Dato che è un pannello interno, la localizzazione non è prioritaria, ma crea inconsistenza per team internazionali.

---

## 8. Analisi di Copertura

### 8.1 Copertura per Modulo App

| Modulo Frontend | Chiavi i18n | Pagine (LOC) | Rapporto chiavi/LOC |
|----------------|-------------|-------------|-------------------|
| Site Builder | 406 | 5,529 | 1:13.6 |
| Banking | 311 | 1,506 | 1:4.8 |
| CRM | 263 | 3,346 | 1:12.7 |
| Invoices | 261 | 2,108 | 1:8.1 |
| Settings | 230 | 1,905 | 1:8.3 |
| Email Marketing | 219 | 2,049 | 1:9.4 |
| Client Portal | 200 | 1,575 | 1:7.9 |
| Accounting | 194 | 1,715 | 1:8.8 |
| Fiscal | 190 | 1,193 | 1:6.3 |
| Expenses | 181 | 1,517 | 1:8.4 |
| Contacts | 175 | 1,463 | 1:8.4 |
| Workflows | 175 | 1,424 | 1:8.1 |
| Pricing | 171 | 841 | 1:4.9 |
| Tasks | 151 | 3,168 | 1:21.0 ⚠️ |
| Files | 151 | 1,210 | 1:8.0 |
| Budget | 149 | 1,273 | 1:8.5 |
| Calendar | 143 | 1,513 | 1:10.6 |
| Forms | 142 | 913 | 1:6.4 |
| Webhooks | 141 | 1,272 | 1:9.0 |
| Analytics | 139 | 1,233 | 1:8.9 |
| Chat | 130 | 1,918 | 1:14.8 ⚠️ |
| Time Tracking | 129 | 1,731 | 1:13.4 |
| Dashboard | 119 | 2,707 | 1:22.7 ⚠️ |
| Auth | 116 | 1,377 | 1:11.9 |
| Billing | 108 | 1,027 | 1:9.5 |
| API Keys | 100 | 591 | 1:5.9 |
| OCR Receipts | 93 | 654 | 1:7.0 |
| E-Invoice | 84 | 1,155 | 1:13.8 |
| Open Banking | 9 | 819 | 1:91.0 ⚠️⚠️ |

**⚠️ Moduli con rapporto alto** (possibili stringhe hardcoded):
- **Open Banking**: Solo 9 chiavi per 819 LOC → probabile i18n incompleto
- **Dashboard**: 119 chiavi per 2,707 LOC → molti widget con testo non localizzato?
- **Tasks**: 151 chiavi per 3,168 LOC → Kanban labels potenzialmente hardcoded

### 8.2 Differenze LOC tra Lingue App

| Lingua | LOC | Delta vs it (7,538) | Note |
|--------|-----|-------------------|------|
| `it` | 7,538 | — (base) | Completo |
| `en` | 7,534 | -4 | ~4 chiavi in meno |
| `de` | 7,466 | -72 | ~72 chiavi mancanti |
| `es` | 7,477 | -61 | ~61 chiavi mancanti |
| `fr` | 7,466 | -72 | ~72 chiavi mancanti |
| `pt` | 7,466 | -72 | ~72 chiavi mancanti |
| `cs` | 7,516 | -22 | ~22 chiavi mancanti |
| `nl` | 7,516 | -22 | ~22 chiavi mancanti |
| `ar`–`zh` (batch 3) | 7,528 | -10 | ~10 chiavi mancanti |

**Nota**: differenze LOC non corrispondono esattamente a chiavi mancanti (lo stile JSON può variare), ma indicano che de/es/fr/pt hanno più chiavi non tradotte.

### 8.3 Differenze Chiavi Marketing

| Lingua | LOC | Delta vs it (1,944) |
|--------|-----|-------------------|
| `it` | 1,944 | — (base) |
| `en` | 1,944 | 0 |
| `de` | 1,929 | -15 |
| Tutte le altre | 1,925 | -19 |

Marketing ha migliore allineamento (meno chiavi totali, più facile mantenere).

---

## 9. Qualità delle Traduzioni

### 9.1 Problemi di Encoding Rilevati

Nel file `it.json` dell'app sono presenti **problemi di encoding**:
```json
"generic": "Si Ã¨ verificato un errore. Riprova."   // ❌ Ã¨ → è (encoding rotto)
"hasAccount": "Hai giÃ  un account?"                  // ❌ Ã  → à (encoding rotto)
```

Questi sono caratteri UTF-8 multi-byte interpretati come Latin-1 (ISO-8859-1), tipico di:
- Conversione encoding durante copia/incolla
- Scrittura file con BOM o encoding misto

**Script di fix**: `_engine/fix_encoding.js` esiste ma evidentemente non ha coperto tutti i casi.

### 9.2 HTML nelle Traduzioni

Molte chiavi contengono HTML inline:
```json
"about_hero_title": "Costruiamo il <span class=\"text-gradient\">futuro delle PMI</span>"
"blog_cta_title": "Pronto a <span class=\"text-gradient\">trasformare</span> la tua azienda?"
```

**Rischi**:
- XSS se renderizzato con `dangerouslySetInnerHTML` senza sanitizzazione
- Difficoltà per traduttori non tecnici
- Le classi CSS (`text-gradient`) sono accoppiate alle traduzioni

### 9.3 Unicode Escaping

Le traduzioni app usano `\u0027` per gli apostrofi:
```json
"subtitle": "Enter the code from your authenticator app"
// vs
"subtitle": "Inserisci il codice dall\u0027app di autenticazione"
```

Non è un problema funzionale ma indica che il JSON è stato generato programmaticamente (non scritto a mano) o processato da tool che escapa Unicode.

### 9.4 Consistenza Terminologica

Terminologia chiave mantenuta in tutte le lingue (da verifica campione):

| Termine IT | Termine EN | Presente in tutte le 20 lingue |
|-----------|------------|-------------------------------|
| Fattura | Invoice | ✅ |
| Contabilità | Accounting | ✅ |
| Piano dei Conti | Chart of Accounts | ✅ |
| Riconciliazione | Reconciliation | ✅ |
| CRM | CRM (invariato) | ✅ |
| Flusso di cassa | Cash Flow | ✅ |

---

## 10. Direzione testo RTL (Arabo)

### 10.1 Supporto RTL

- L'arabo (`ar`) è l'unica lingua RTL supportata
- Il file `ar.json` ha stessa struttura delle lingue LTR
- **Non è stato verificato** se l'app React gestisce correttamente `dir="rtl"`:
  - Tailwind CSS 4 supporta `rtl:` variant nativamente
  - Non trovate reference a `dir="rtl"` nel codice frontend analizzato
  - Possibile che il layout non sia ottimizzato per RTL

### 10.2 Lingue CJK

Cinese (`zh`), Giapponese (`ja`) e Coreano (`ko`) hanno file più compatti (248-305 KB vs 499-515 KB per lingue latine) per la natura ideografica dei caratteri.

Non sono stati rilevati problemi specifici di:
- Line breaking CJK
- Font fallback
- Formattazione numeri/date specifiche

---

## 11. Persistenza Lingua e Detection

### 11.1 App Frontend

```
Priorità detection:
1. localStorage('kacinka_lang')     → scelta utente salvata
2. navigator.language               → lingua browser
3. fallback: 'it'                   → italiano default
```

**Cambio lingua runtime**: Sì, l'utente può cambiare lingua dalle impostazioni → salva in localStorage → i18next ricarica il JSON via HTTP Backend.

### 11.2 Marketing

- URL-based: `/{lang}/pagina` (es. `/en/about`, `/de/kontakt`)
- **20 varianti URL** per ogni pagina
- Hreflang alternates in `<head>` per SEO

---

## 12. Riepilogo e Criticità

### 12.1 Punti di Forza

| # | Aspetto | Dettaglio |
|---|---------|-----------|
| 1 | Copertura linguistica | 20 lingue — eccellente per mercato EU + globale |
| 2 | Lazy loading | HTTP backend evita 5+ MB nel bundle iniziale |
| 3 | Pipeline automatizzata | Chunk + merge + batch per traduzioni scalabili |
| 4 | Blog localizzato | 240 articoli tecnici in 20 lingue |
| 5 | SEO multilingua | 20 sitemap + hreflang + meta localizzati |
| 6 | Naming convention | Namespace modulari ben organizzati (62 namespace) |

### 12.2 Criticità e Gap

| # | Criticità | Severità | Dettaglio |
|---|----------|----------|-----------|
| 1 | **Encoding rotto in it.json** | 🔴 Alta | `Ã¨` al posto di `è`, `Ã ` al posto di `à` visibili all'utente |
| 2 | **Admin non localizzato** | 🟡 Media | Pannello admin solo IT/EN misto |
| 3 | **Open Banking i18n incompleto** | 🟡 Media | Solo 9 chiavi per 819 LOC frontend |
| 4 | **Chiavi mancanti de/es/fr/pt** | 🟡 Media | ~60-72 chiavi in meno rispetto a `it` |
| 5 | **RTL non verificato** | 🟡 Media | Arabo supportato ma layout RTL non confermato |
| 6 | **HTML nelle traduzioni** | 🟠 Bassa | Potenziale XSS + coupling CSS |
| 7 | **Nessun pluralization reale** | 🟠 Bassa | i18next lo supporta ma non usato estensivamente |
| 8 | **Bundle per lingua unico** | 🟠 Bassa | 250-515 KB per lingua (potrebbe essere splitato per route) |
| 9 | **No type safety** | 🟠 Bassa | Chiavi i18n non tipizzate (nessun i18next.d.ts) |
| 10 | **No CI/CD check** | 🟠 Bassa | Nessun control automatico chiavi mancanti in pipeline |

### 12.3 Metriche Finali

| Metrica | Valore |
|---------|--------|
| Lingue totali | 20 |
| Chiavi app (per lingua) | 6,184 |
| Chiavi marketing (per lingua) | 1,942 |
| Articoli blog totali | 240 |
| Sitemap localizzati | 20 |
| File i18n totali | 200+ |
| Dimensione totale locales | 10.6 MB |
| Script pipeline traduzione | 15+ file, ~4,500+ LOC |
| Script build i18n | 8+ file, ~400+ LOC |

---

## Aggiornamento Post-Roadmap (v3.0)

### Miglioramenti i18n Implementati
- **Namespace Split**: Locali divisi per namespace per lazy loading (~30KB first load)
- **Encoding Fix**: Risolti tutti i problemi di encoding UTF-8 nelle landing pages (Ã¨ → è)
- **RTL Support**: Layout RTL funzionale per arabo (ar) con CSS dedicato
- **Key Sync**: Script `_build/validate_i18n.js` sincronizza chiavi mancanti su tutte le 20 lingue
- **i18n Validation**: CI-ready script per verificare completezza locali
- **Nuove chiavi**: Messaggi supporto AI, validazione IVA, SMS, accessibility in tutte le 20 lingue

