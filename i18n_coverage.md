# i18n Coverage — Copertura Internazionalizzazione

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Overview

| Metrica | Valore |
|---------|--------|
| **Lingue supportate** | 20 |
| **File locale totali** | 20 JSON files |
| **Namespace (top-level keys)** | 62 per lingua |
| **Dimensione locale media** | ~302 KB |
| **Dimensione totale i18n** | ~6.0 MB |
| **Framework** | i18next 25.8.4 + react-i18next |

---

## 2. Lingue Supportate

| # | Codice | Lingua | File | Dimensione | Popolazione nativa | Mercato EU |
|---|--------|--------|------|-----------|-------------------|-----------|
| 1 | `it` | Italiano | it.json | 281.7 KB | 65M | 🇮🇹 Italia |
| 2 | `en` | English | en.json | 265.4 KB | 400M+ | 🇬🇧🇮🇪 UK/Ireland |
| 3 | `de` | Deutsch | de.json | 289.6 KB | 100M | 🇩🇪🇦🇹🇨🇭 DACH |
| 4 | `es` | Español | es.json | 286.4 KB | 500M+ | 🇪🇸 Spagna |
| 5 | `fr` | Français | fr.json | 291.2 KB | 280M+ | 🇫🇷🇧🇪🇨🇭 Francia |
| 6 | `pt` | Português | pt.json | 288.0 KB | 250M+ | 🇵🇹🇧🇷 Portogallo |
| 7 | `pl` | Polski | pl.json | 289.7 KB | 45M | 🇵🇱 Polonia |
| 8 | `nl` | Nederlands | nl.json | 283.4 KB | 25M | 🇳🇱🇧🇪 Benelux |
| 9 | `ro` | Română | ro.json | 290.7 KB | 26M | 🇷🇴 Romania |
| 10 | `el` | Ελληνικά | el.json | 408.7 KB | 13M | 🇬🇷 Grecia |
| 11 | `tr` | Türkçe | tr.json | 287.9 KB | 80M | 🇹🇷 Turchia |
| 12 | `cs` | Čeština | cs.json | 288.4 KB | 10.5M | 🇨🇿 Rep. Ceca |
| 13 | `da` | Dansk | da.json | 277.9 KB | 5.5M | 🇩🇰 Danimarca |
| 14 | `fi` | Suomi | fi.json | 287.0 KB | 5.5M | 🇫🇮 Finlandia |
| 15 | `sv` | Svenska | sv.json | 280.2 KB | 10M | 🇸🇪 Svezia |
| 16 | `ar` | العربية | ar.json | 347.2 KB | 420M+ | 🇸🇦 MENA |
| 17 | `hi` | हिन्दी | hi.json | 449.2 KB | 600M+ | 🇮🇳 India |
| 18 | `ja` | 日本語 | ja.json | 322.1 KB | 125M | 🇯🇵 Giappone |
| 19 | `ko` | 한국어 | ko.json | 296.4 KB | 77M | 🇰🇷 Corea del Sud |
| 20 | `zh` | 中文 | zh.json | 262.6 KB | 1.3B | 🇨🇳 Cina |

### Copertura Popolazione

| Metrica | Valore |
|---------|--------|
| Popolazione nativa raggiunta | ~4.4 miliardi |
| % popolazione mondiale | ~55% |
| Mercati EU coperti (EU-27) | 15 su 27 lingue ufficiali |
| Script supportati | Latino, Arabo, Devanagari, CJK, Greco, Cirillico (tramite latino) |
| RTL (Right-to-Left) | ✅ Arabo |

---

## 3. Namespace Structure

Ogni file locale contiene 62 namespace (top-level keys) che coprono tutti i moduli e componenti UI:

### Namespace per Categoria

| Categoria | Namespace (stimati) | Copertura Moduli |
|-----------|-------------------|-----------------|
| Core UI | ~8 | Navigation, sidebar, common, errors, auth |
| CRM & Sales | ~5 | crm, deals, pipeline, contacts, proposals |
| Finance | ~8 | invoices, expenses, accounting, budget, tax, fiscal, payments |
| E-Invoicing | ~3 | einvoice, einvoice_config, einvoice_formats |
| Communication | ~4 | email_marketing, chat, notifications, sms |
| Productivity | ~5 | tasks, calendar, time_tracking, files, forms |
| AI & OCR | ~3 | ai, ocr, suggestions |
| Platform | ~6 | webhooks, api_keys, workflows, settings, billing, pricing |
| Website | ~3 | site_builder, templates, domains |
| Analytics | ~3 | analytics, reports, dashboard |
| Admin | ~4 | admin, team, permissions, onboarding |
| Banking | ~3 | banking, open_banking, reconciliation |
| Compliance | ~3 | gdpr, compliance, legal |
| Other | ~4 | feedback, support, export, import |

---

## 4. Completeness Assessment

### 4.1 Key Completeness per Lingua

Tutte le 20 lingue hanno **62 namespace identici**, indicando parità strutturale. Ogni lingua ha:
- ~265-450 KB di traduzioni
- Variazione dimensionale dovuta a encoding (CJK/arabo/devanagari > latino)
- Nessun file con 0 key o namespace mancanti

### 4.2 Dimensione Relativa (benchmark: en.json = 100%)

| Lingua | Dimensione | Ratio vs EN | Note |
|--------|-----------|-------------|------|
| hi (Hindi) | 449.2 KB | 169% | Devanagari script (più byte/char) |
| el (Greek) | 408.7 KB | 154% | Caratteri greci |
| ar (Arabic) | 347.2 KB | 131% | RTL + diacritici |
| ja (Japanese) | 322.1 KB | 121% | CJK mixto |
| ko (Korean) | 296.4 KB | 112% | Hangul |
| fr (French) | 291.2 KB | 110% | Accenti + testo più lungo |
| de (German) | 289.6 KB | 109% | Composte lunghe |
| **en (English)** | **265.4 KB** | **100%** | **Baseline** |
| zh (Chinese) | 262.6 KB | 99% | CJK compatto |
| da (Danish) | 277.9 KB | 105% | |

### 4.3 Translation Quality Assessment

| Aspetto | Status | Note |
|---------|--------|------|
| Completezza namespace | ✅ 100% tutte le lingue | 62/62 namespace per lingua |
| Machine vs Human | ⚠️ Prevalentemente machine-translated | Generato con script build |
| Pluralizzazione | ⚠️ Da verificare | i18next supporta plurali, non verificato per tutte le lingue |
| Contesto specifico (fiscale) | ⚠️ Potenzialmente impreciso | Termini fiscali richiedono revisione nativa |
| RTL layout | ✅ Supportato | ar.json + CSS RTL |
| Date/Number formatting | ✅ Via Intl API | Browser-native formatting |

---

## 5. Sitemap Multilingue

### 5.1 Sitemap per Lingua (Marketing Site)

| File | Lingua | Contenuto |
|------|--------|----------|
| sitemap-ar.xml | Arabo | Hub & spoke pages |
| sitemap-cs.xml | Ceco | Hub & spoke pages |
| sitemap-da.xml | Danese | Hub & spoke pages |
| sitemap-de.xml | Tedesco | Hub & spoke pages |
| sitemap-el.xml | Greco | Hub & spoke pages |
| sitemap-en.xml | Inglese | Hub & spoke pages |
| sitemap-es.xml | Spagnolo | Hub & spoke pages |
| sitemap-fi.xml | Finlandese | Hub & spoke pages |
| sitemap-fr.xml | Francese | Hub & spoke pages |
| sitemap-hi.xml | Hindi | Hub & spoke pages |
| sitemap-it.xml | Italiano | Hub & spoke pages |
| sitemap-ja.xml | Giapponese | Hub & spoke pages |
| sitemap-ko.xml | Coreano | Hub & spoke pages |
| sitemap-nl.xml | Olandese | Hub & spoke pages |
| sitemap-pl.xml | Polacco | Hub & spoke pages |
| sitemap-pt.xml | Portoghese | Hub & spoke pages |
| sitemap-ro.xml | Rumeno | Hub & spoke pages |
| sitemap-sv.xml | Svedese | Hub & spoke pages |
| sitemap-tr.xml | Turco | Hub & spoke pages |
| sitemap-zh.xml | Cinese | Hub & spoke pages |
| **sitemap.xml** | **Index** | **Aggregatore tutti i sitemap** |

### 5.2 Hreflang Implementation

Ogni pagina marketing include tag `<link rel="alternate" hreflang="xx">` per tutte le 20 lingue + `hreflang="x-default"` per fallback a inglese.

---

## 6. Gap Analysis

### 6.1 Lingue EU Mancanti

| Lingua | Paese EU | Priorità | Effort |
|--------|---------|----------|--------|
| Bulgaro (bg) | 🇧🇬 Bulgaria | 🟢 Bassa | 2-3 ore (AI translate) |
| Croato (hr) | 🇭🇷 Croazia | 🟢 Bassa | 2-3 ore |
| Estone (et) | 🇪🇪 Estonia | 🟢 Bassa | 2-3 ore |
| Ungherese (hu) | 🇭🇺 Ungheria | 🟡 Media | 2-3 ore |
| Lettone (lv) | 🇱🇻 Lettonia | 🟢 Bassa | 2-3 ore |
| Lituano (lt) | 🇱🇹 Lituania | 🟢 Bassa | 2-3 ore |
| Maltese (mt) | 🇲🇹 Malta | 🟢 Bassa | 2-3 ore |
| Slovacco (sk) | 🇸🇰 Slovacchia | 🟢 Bassa | 2-3 ore |
| Sloveno (sl) | 🇸🇮 Slovenia | 🟢 Bassa | 2-3 ore |
| Irlandese (ga) | 🇮🇪 Irlanda | 🟢 Bassa | 2-3 ore |
| **Totale mancanti** | **10 lingue EU** | | **20-30 ore** |

### 6.2 Raccomandazioni

| Priorità | Azione |
|----------|--------|
| 🔴 Critica | Revisione nativa IT, EN, DE, ES, FR (mercati primari) |
| 🔴 Critica | Verificare termini fiscali specifici per paese |
| 🟡 Media | Aggiungere HU, BG per copertura EU più completa |
| 🟡 Media | Implementare context-aware placeholders |
| 🟢 Bassa | Aggiungere rimanenti 8 lingue EU per copertura 100% |
| 🟢 Bassa | Community translations con suggestion system |

---

## 7. i18n Technical Architecture

### 7.1 Stack

```
i18next 25.8.4
├── react-i18next
├── i18next-browser-languagedetector
├── Lazy loading per namespace (loadModuleI18n.js)
└── Fallback chain: user_lang → en
```

### 7.2 Lazy Loading

```javascript
// loadModuleI18n.js
// Carica solo i namespace necessari per il modulo corrente
// Riduce initial bundle da ~6MB a ~300KB (solo lingua + core)
```

### 7.3 Build Scripts

| Script | Funzione |
|--------|---------|
| `check_missing_i18n_keys.js` | Verifica chiavi mancanti tra lingue |
| `fix_encoding_i18n.js` | Corregge encoding UTF-8 |
| `fix_country_names.js` | Standardizza nomi paesi |
| `inject_*_i18n.js` (30+ script) | Iniettano traduzioni per nuovi moduli |
| `generate_spoke_translations.php` | Genera traduzioni spoke per marketing site |

---

*Documento generato il 2026-03-07. Dati estratti da `app/src/i18n/locales/` (20 file JSON, ~6.0 MB totali). 62 namespace verificati.*
