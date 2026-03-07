# Fiscal Compliance

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Overview

Kacinka supporta la fatturazione elettronica multi-paese con 13 builder per 11 paesi. Questo documento analizza la conformità fiscale per ogni giurisdizione coperta.

---

## 2. Country Coverage Matrix

### 2.1 E-Invoice Builders

| Paese | Builder | Format | Standard | LOC | Status |
|-------|---------|--------|----------|-----|--------|
| 🇮🇹 Italia | FatturaPA | XML PA 1.2.2 | SDI | 517 | ✅ Production |
| 🇩🇪 Germania | XRechnung | UBL 2.1 | PEPPOL | 153 | ✅ Beta |
| 🇫🇷 Francia | FacturX | CII (ZUGFeRD) | ChorusPro | 519 | ✅ Beta |
| 🇫🇷 Francia | ChorusPro | UBL 2.1 | ChorusPro B2G | 217 | ✅ Beta |
| 🇪🇸 Spagna | SII | XML AEAT | Suministro Inmediato | 302 | ✅ Beta |
| 🇵🇱 Polonia | KSeF | XML FA(2) | KSeF | 279 | ✅ Beta |
| 🇷🇴 Romania | ROCIUS | UBL 2.1 | ANAF e-Factura | 111 | ✅ Beta |
| 🇬🇷 Grecia | MyDATA | XML AADE | myDATA | 327 | ✅ Beta |
| 🇹🇷 Turchia | EFatura | UBL-TR | GİB | 488 | ✅ Beta |
| 🇸🇦 Arabia S. | ZATCA | UBL 2.1 | FATOORAH | 589 | ✅ Beta |
| 🇮🇳 India | GST | JSON | GST/e-Way | 374 | ✅ Beta |
| EU | PeppolBIS3 | UBL 2.1 | PEPPOL BIS 3.0 | 621 | ✅ Production |

### 2.2 Routing Logic (PlatformInvoiceService)

```
Country    → Builder          → Gateway
─────────────────────────────────────────
IT         → FatturaPA        → SDI (Agenzia Entrate)
DE         → XRechnung        → PEPPOL Access Point
FR         → FacturX          → ChorusPro (B2G) / PPF (B2B post-2026)
EU + VAT   → PeppolBIS3       → PEPPOL (+ reverse charge)
EU B2C     → PeppolBIS3       → OSS regime
non-EU     → PDF              → Email delivery
ES         → SII              → AEAT portal
PL         → KSeF             → KSeF platform
RO         → ROCIUS           → ANAF e-Factura
GR         → MyDATA           → AADE myDATA
TR         → EFatura          → GİB portal
SA         → ZATCA            → FATOORAH
IN         → GST              → GST portal
```

---

## 3. Italian Fiscal Compliance (Market Priority)

### 3.1 FatturaPA Compliance

| Requisito | Status | Note |
|-----------|--------|------|
| XML Schema PA 1.2.2 | ✅ | Validato |
| Codice Destinatario (7 chars) | ✅ | Supportato |
| PEC (per chi non ha SDI code) | ✅ | Fallback |
| Regime fiscale | ✅ | RF01-RF19 (forfettari, ordinari, etc.) |
| Ritenuta d'acconto | ✅ | withholding_tax_configs table |
| Split payment (scissione pagamenti) | ✅ | Per fatture PA |
| Bollo virtuale (€2 su > €77.47) | ⚠️ | Da verificare automazione |
| Numerazione progressiva | ✅ | invoice_sequences per progetto |
| Conservazione sostitutiva | ❌ | Non implementata (10 anni obbligo) |

### 3.2 Regimi Fiscali Italiani

| Regime | Supportato | Note |
|--------|-----------|------|
| Ordinario | ✅ | IVA standard (22%, 10%, 5%, 4%) |
| Forfettario | ✅ | tax_regime_configs per progetto |
| Regime dei minimi | ⚠️ | In sunset, supporto parziale |
| Split payment | ✅ | Per PA |
| Reverse charge | ✅ | Per EU B2B |

### 3.3 Tax Configuration

| Table | Ruolo |
|-------|-------|
| `tax_codes` | Catalogo aliquote per progetto/paese |
| `tax_regime_configs` | Regime fiscale per progetto/paese |
| `withholding_tax_configs` | Ritenuta d'acconto |
| `einvoice_configs` | Credenziali gateway per paese |
| `fiscal_years` | Anni fiscali per contabilità |

---

## 4. EU ViDA (VAT in the Digital Age) Readiness

### 4.1 ViDA Timeline

| Data | Obbligo | Impatto Kacinka |
|------|---------|----------------|
| **2028 Q1** | EU-wide B2B e-invoicing mandate | ✅ Coperto via PEPPOL + country builders |
| **2028 Q1** | Digital Reporting Requirements (DRR) | ⚠️ Da implementare |
| **2030** | Real-time reporting per transazioni cross-border | ⚠️ Da implementare |

### 4.2 Readiness Assessment

| Requisito ViDA | Status | Gap |
|---------------|--------|-----|
| Structured e-invoice (CEN 16931) | ✅ | UBL 2.1 + CII compliant |
| PEPPOL network access | ✅ | PeppolBIS3Builder (621 LOC) |
| Cross-border B2B invoicing | ✅ | Routing logic implementato |
| Digital Reporting | ❌ | Da sviluppare |
| Real-time VAT reporting | ❌ | Da sviluppare |
| OSS/IOSS compliance | ⚠️ Parziale | OSS route exists |

---

## 5. Per-Country Compliance Status

### 5.1 Germany (XRechnung)

| Requisito | Status |
|-----------|--------|
| XRechnung 3.0.1 schema | ✅ |
| PEPPOL delivery | ✅ |
| Leitweg-ID (B2G routing) | ⚠️ Da verificare |
| GoBD compliance (accounting) | ⚠️ N/A (non SaaS accounting per DE) |

### 5.2 France (FacturX)

| Requisito | Status |
|-----------|--------|
| Factur-X 1.0 (ZUGFeRD 2.1) | ✅ |
| ChorusPro B2G | ✅ (217 LOC builder) |
| PPF (Portail Public de Facturation) B2B | ⚠️ Post-September 2026 |
| Chorus Pro certification | ❌ Da ottenere |

### 5.3 Spain (SII)

| Requisito | Status |
|-----------|--------|
| SII XML format | ✅ |
| AEAT Suministro Inmediato | ✅ (302 LOC) |
| TicketBAI (Basque Country) | ❌ Non implementato |
| Certificado electrónico | ⚠️ Da configurare per produzione |

### 5.4 Poland (KSeF)

| Requisito | Status |
|-----------|--------|
| KSeF FA(2) format | ✅ |
| KSeF platform API | ✅ (279 LOC) |
| KSeF mandatory (postponed to 2026) | ⚠️ Timeline in evoluzione |
| Digital signature | ⚠️ Da verificare |

### 5.5 Other Countries

| Paese | Builder Status | Production Ready |
|-------|---------------|-----------------|
| Romania (ANAF) | ✅ Builder exists (111 LOC) | ⚠️ Beta |
| Greece (myDATA) | ✅ Builder exists (327 LOC) | ⚠️ Beta |
| Turkey (GİB) | ✅ Builder exists (488 LOC) | ⚠️ Beta |
| Saudi Arabia (ZATCA) | ✅ Builder exists (589 LOC) | ⚠️ Beta |
| India (GST) | ✅ Builder exists (374 LOC) | ⚠️ Beta |

---

## 6. Accounting Compliance

### 6.1 Double-Entry Bookkeeping

| Feature | Status |
|---------|--------|
| Chart of Accounts | ✅ (876 LOC ChartOfAccountsService) |
| Journal Entries | ✅ (850 LOC JournalEntryService) |
| Fiscal Year Management | ✅ |
| Period Locking | ✅ |
| Accounting Audit Log | ✅ (separate table) |
| VAT Reports | ✅ (660 LOC VatReportService) |

### 6.2 Tax Reporting

| Report | Status |
|--------|--------|
| VAT Summary | ✅ |
| VAT Liquidazione (IT) | ⚠️ Template base |
| Annual Tax Report | ⚠️ Da completare |
| Intrastat | ❌ Non implementato |
| Withholding Tax Report (CU) | ⚠️ Data exists, report template needed |

---

## 7. Gap Analysis

| # | Gap | Paese | Rischio | Priorità |
|---|-----|-------|---------|----------|
| 1 | Conservazione sostitutiva (IT) | IT | 🔴 Alto | Pre-launch Italia |
| 2 | ViDA Digital Reporting | EU | 🟡 Medio | Pre-2028 |
| 3 | TicketBAI (Basque Country) | ES | 🟡 Medio | Per espansione Spagna |
| 4 | PPF French B2B platform | FR | 🟡 Medio | Pre-September 2026 |
| 5 | Bollo virtuale automation | IT | 🟡 Medio | Pre-launch Italia |
| 6 | Intrastat declaration | EU | 🟡 Medio | Per commercio intra-EU |
| 7 | Corrispettivi telematici (RT) | IT | 🟢 Basso | Retail feature |
| 8 | CFDI (Mexico) | MX | 🟢 Basso | Future market |

---

## 8. Raccomandazioni

### Pre-Launch Italia
1. Implementare conservazione sostitutiva (partnership con provider certificato)
2. Automatizzare bollo virtuale (€2 su fatture > €77.47 esenti IVA)
3. Validare XML FatturaPA con tool AdE ufficiale
4. Testare flusso SDI end-to-end con ambiente di test AdE

### Espansione EU
5. Certificazione ChorusPro per mercato francese
6. Testing KSeF con ambiente sandbox polacco
7. Preparare Digital Reporting infrastructure per ViDA 2028
8. Aggiungere TicketBAI per Basque Country

---

*Documento generato il 2026-03-07. Analisi basata su 13 e-invoice builders (4,552 LOC), PlatformInvoiceService (1,169 LOC), EInvoiceService (4,051 LOC), e migration files per tax/fiscal configuration.*
