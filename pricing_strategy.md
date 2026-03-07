# Pricing Strategy

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Current Pricing Architecture

### Piano Attuale (da `fase3_plan_pricing.php`)

| Piano | Prezzo/mese | Prezzo/anno | Sconto annuale | Target |
|-------|-------------|-------------|----------------|--------|
| **Starter** | €0 | €0 | — | Trial / discovery |
| **Pro** | €19 | €182 | 20% | Freelancer e professionisti |
| **Pro+** | €39 | €374 | 20% | PMI |
| **Elite** | €79 | €758 | 20% | Agenzie e studi professionali |
| **Enterprise** | €149 | €1,430 | 20% | White-label, SSO, multi-format |

### Struttura Tier — Moduli per Piano

| Modulo | Starter | Pro | Pro+ | Elite | Enterprise |
|--------|---------|-----|------|-------|-----------|
| CRM, Tasks, Calendar, Contacts, Files, Chat, Time Tracking | ✅ | ✅ | ✅ | ✅ | ✅ |
| Invoices, Expenses, Reports | ❌ | ✅ | ✅ | ✅ | ✅ |
| Email Marketing, Forms | ❌ | ✅ | ✅ | ✅ | ✅ |
| Webhooks, API Keys | ❌ | ✅ | ✅ | ✅ | ✅ |
| AI, OCR, Accounting, Budget | ❌ | ❌ | ✅ | ✅ | ✅ |
| Banking, Workflows, Site Builder | ❌ | ❌ | ✅ | ✅ | ✅ |
| Fiscal, FatturaPA (e-invoicing) | ❌ | ❌ | ✅ | ✅ | ✅ |
| Client Portal, Analytics | ❌ | ❌ | ❌ | ✅ | ✅ |
| White Label, SSO, Open Banking | ❌ | ❌ | ❌ | ❌ | ✅ |

### Limiti per Piano

| Limite | Starter | Pro | Pro+ | Elite | Enterprise |
|--------|---------|-----|------|-------|-----------|
| Progetti | 1 | 5 | 15 | 50 | ∞ |
| Storage | 0.5 GB | 10 GB | 50 GB | 200 GB | ∞ |
| Team Members | 1 | 5 | 25 | ∞ | ∞ |
| Contatti | 10 | 2,000 | 10,000 | ∞ | ∞ |
| Fatture/mese | 3 | 100 | 500 | ∞ | ∞ |
| AI Calls/mese | 5 | 100 | 500 | 2,000 | ∞ |
| Campagne email/mese | 1 | 10 | 50 | ∞ | ∞ |
| Destinatari/campagna | 50 | 1,000 | 5,000 | ∞ | ∞ |
| Siti | 0 | 3 | 10 | ∞ | ∞ |
| Forms | 1 | 15 | 50 | ∞ | ∞ |
| Workflows | 1 | 15 | 50 | ∞ | ∞ |
| API Access | ❌ | ✅ | ✅ | ✅ | ✅ |
| Custom Domain | ❌ | ❌ | ✅ | ✅ | ✅ |
| Priority Support | ❌ | ❌ | ❌ | ✅ | ✅ |
| White Label | ❌ | ❌ | ❌ | ❌ | ✅ |
| SSO | ❌ | ❌ | ❌ | ❌ | ✅ |

### Add-on Packs (da `fase3_addons.php`)

| Add-on | Prezzo/mese | Prezzo/anno | Stato |
|--------|-------------|-------------|-------|
| Pack AI 500 chiamate | €9.90 | €99 | ✅ Attivo |
| Pack AI 1000 chiamate | €14.90 | €149 | ✅ Attivo |
| Progetti Extra (Pack 10) | €9.90 | €99 | ✅ Attivo |
| Storage Extra 5 GB | €2.90 | €29 | ○ Non attivo |
| Storage Extra 20 GB | €7.90 | €79 | ○ Non attivo |

---

## 2. Competitor Pricing Comparison

### Prezzo per Scenario d'Uso

| Scenario | Kacinka | Holded | Odoo | Zoho | FreshBooks | QuickBooks | Invoice Ninja |
|----------|---------|--------|------|------|------------|------------|---------------|
| **Freelancer IT** (1 user, fatture, e-invoice) | €19 Pro | €14.50 Basic | €11.90 Standard (1 app) | €10 Standard | $23 Lite | $38 Simple Start | $0 Free |
| **PMI 5 dipendenti** (AI, contabilità, e-invoice) | €39 Pro+ | €29.50+SII (€59.50) | €59.50 (5×€11.90) | €30 Premium | $70+$55 ($125) | $115 Plus | $15 Enterprise |
| **Agenzia 15 dipendenti** (tutto + analytics) | €79 Elite | €99 Advanced+addons | €178.50 (15×€11.90) | €100 Elite | $70+$165 ($235) | $275 Advanced | $15+extras |

### Posizionamento Prezzo

```
Prezzo/mese (scenario PMI 5 dipendenti, funzionalità comparabili)

Invoice Ninja  █ €15
Holded          ██████ €59.50
Odoo            ██████ €59.50   (per-user pricing scales up!)
FreshBooks      ████████████ €125
QuickBooks      ███████████ €115
Zoho Books      ███ €30         (ma manca e-invoicing EU)
────────────────────────────────
Kacinka Pro+    ████ €39  ← sweet spot
```

---

## 3. Pricing Philosophy

### Principi Guida

1. **All-Inclusive per Tier** — A differenza di Holded (add-on SII €30+), Odoo (per-user), FreshBooks (per-member), Kacinka include **tutto nel tier**, incluso e-invoicing. Nessun costo nascosto.

2. **Prezzo Flat, Non Per-User** — Il modello per-user di Odoo (€11.90/user) diventa rapidamente costoso per team. Kacinka usa un **prezzo fisso per piano** con limiti generosi di team members.

3. **Freemium Limitato** — Starter a €0 con limiti stretti (1 progetto, 3 fatture/mese, 5 AI calls) per discovery. Non compete con i "free forever" di Zoho/Invoice Ninja ma incentiva l'upgrade.

4. **20% Sconto Annuale** — Standard di mercato per SaaS. Aumenta retention e cash flow upfront.

5. **E-Invoicing Come Differentiatore, Non Upsell** — Holded vende SII a €30-60/mese come add-on. Kacinka include e-invoicing (tutti i 13 formati) da Pro+ in su. Questo è un vantaggio competitivo decisivo in Europa.

### Perché Questi Prezzi Funzionano

| Vantaggio | Dettaglio |
|-----------|---------|
| **Undercut** Holded/Odoo | €39 vs €59.50 per PMI con più funzionalità |
| **Massive undercut** FreshBooks/QuickBooks | €39 vs €115-125 per funzionalità comparabili |
| **Premium vs** Invoice Ninja | €39 vs €15 — giustificato da 26 moduli vs 3-4 moduli |
| **Parità Zoho** da feature perspective | €39 vs €30 ma con 13 formati e-invoice vs 0 (EU) |

---

## 4. Revenue Streams

### Revenue primarie

| Stream | Contributo atteso | Piano |
|--------|-------------------|-------|
| Subscription mensili / annuali | 70-80% | Core business |
| Add-on (AI packs, storage, progetti) | 10-15% | Expansion revenue |
| Extra team members (futuro) | 5-10% | Da implementare |

### Revenue secondarie (roadmap)

| Stream | Potenziale | Timeline |
|--------|-----------|----------|
| Marketplace temi/template | Basso (5%) | 6-12 mesi |
| API usage-based (oltre limiti) | Medio (5-10%) | 3-6 mesi |
| Consulenza setup / migrazione | Basso (3-5%) | 9-12 mesi |
| White-label licensing | Alto (10-20%) | 12-18 mesi |
| Referral program | Medio (5-10%) | 6-9 mesi |
| Transaction fees (payments) | Alto (15-25%) | 12-18 mesi (richiede PSP) |

---

## 5. Unit Economics

### COGS (Cost of Goods Sold) per User

| Componente | Costo/utente/mese | Note |
|-----------|-------------------|------|
| Hosting (Hostinger shared, attuale) | ~€0.02 | €10/mese / ~500 utenti target |
| Gemini AI (Flash-Lite default) | ~€0.10-0.50 | Varia con consumo, Flash-Lite molto economico |
| Storage (incluso hosting) | ~€0.01 | Shared hosting storage |
| Email (PHPMailer + SMTP) | ~€0.05 | Costo SMTP relay |
| Tink Open Banking | ~€0.20 | Solo per utenti attivi OB |
| Stripe fees | ~2.9% + €0.30 | Su payment, non per user |
| Sentry monitoring | ~€0.01 | Incluso nel piano dev |
| **COGS totale stimato** | **~€0.40-0.90/utente/mese** | |

### Gross Margin per Piano

| Piano | ARPU | COGS stima | Gross Margin | GM % |
|-------|------|-----------|-------------|------|
| Starter | €0 | €0.40 | -€0.40 | — |
| Pro | €19 | €0.50 | €18.50 | **97.4%** |
| Pro+ | €39 | €0.70 | €38.30 | **98.2%** |
| Elite | €79 | €0.80 | €78.20 | **99.0%** |
| Enterprise | €149 | €0.90 | €148.10 | **99.4%** |

> **Nota**: Gross margin altissimo è tipico di SaaS a basso stadio con infrastruttura shared. Quando si scala, COGS crescono con infra dedicata (VPS, CDN, scaling AI) ma rimangono >80%.

### Target Blended ARPU

| Phase | ARPU Target | Mix atteso |
|-------|-------------|-----------|
| Y1 (5-100 utenti) | €30-40 | 60% Pro, 30% Pro+, 10% Elite |
| Y2 (100-1.000 utenti) | €45-55 | 40% Pro, 35% Pro+, 20% Elite, 5% Enterprise |
| Y3 (1.000-5.000 utenti) | €55-70 | 30% Pro, 30% Pro+, 25% Elite, 15% Enterprise |

---

## 6. Pricing Optimization Roadmap

### Fase 1 — Launch (Month 1-6)
- ✅ 5 piani implementati (Starter → Enterprise)
- ✅ Add-on system attivo (AI packs, progetti, storage)
- ✅ Pricing page con comparison table
- ⬜ A/B test pricing page (CTA copy, layout)
- ⬜ Lifetime Deal (LTD) su AppSumo per cash injection + early adopter

### Fase 2 — Optimization (Month 6-12)
- ⬜ Introduce usage-based pricing per AI sopra i limiti del piano
- ⬜ Pack per vertical (es. "Studio Commercialista", "Agenzia Web")
- ⬜ Partner/reseller pricing (sconto 30-40% per volume)
- ⬜ Student/nonprofit discount (50%)
- ⬜ Referral program (1 mese gratis per referral pagante)

### Fase 3 — Expansion (Month 12-24)
- ⬜ Transaction-based revenue (% su payment processing)
- ⬜ White-label licensing (fee mensile + % ricavi)
- ⬜ Marketplace commissioni (20-30% su template/plugin venduti)
- ⬜ Premium support SLA tiering

### Possibili Adjustments

| Scenario | Azione |
|----------|--------|
| Conversion trial→paid < 3% | Migliorare onboarding, estendere trial a 30 giorni |
| ARPU troppo basso (< €30) | Aumentare prezzo Pro a €29, Pro+ a €49 |
| Churn > 8% mensile | Introdurre sconti lock-in (6 mesi, 2 anni) |
| Concorrente taglia prezzi | Mantenere valore, aggiungere feature |
| Domanda Enterprise alta | Creare piano custom-priced Enterprise+ |

---

## 7. LTD (Lifetime Deal) Strategy

### AppSumo / ProductHunt Launch

| Dettaglio | Valore |
|-----------|--------|
| Piattaforma target | AppSumo, ProductHunt |
| Piano LTD offerto | Pro+ equivalente |
| Prezzo LTD | €99-149 one-time |
| Limite LTD | 500-1.000 licenze |
| Revenue atteso LTD | €50K-150K |
| Costi LTD | AppSumo prende ~60% |
| Revenue netto LTD | €20K-60K |
| CAC per LTD user | €0 (no marketing spend) |
| Valore strategico | 500-1.000 beta tester, review, feedback, community |

### Rischi LTD

| Rischio | Mitigazione |
|---------|-------------|
| Utenti LTD non pagano mai upgrade | Limiti: 5 progetti, 500 AI calls/mese |
| Review negative su AppSumo | Launch solo quando product polished |
| Supporto overload | Ridurre cap a 500 licenze, community forum |
| Cannibalizza subscription | LTD solo per 30 giorni, poi chiuso |

---

## 8. Pricing Benchmarks

### SaaS Benchmark Comparison

| Metrica | Benchmark Industry | Kacinka Target | Status |
|---------|-------------------|----------------|--------|
| Gross Margin | 70-85% | 97%+ | ✅ Superiore (shared hosting) |
| Net Revenue Retention | 100-130% | Target 110% | ❌ Non misurabile (no clienti) |
| Trial-to-Paid Conversion | 3-8% | Target 5% | ❌ Non misurabile |
| Monthly Churn | 3-8% | Target < 5% | ❌ Non misurabile |
| ARPU / CAC Ratio | > 3x | Target > 5x | ❌ Non misurabile |
| Payback Period | 6-18 mesi | Target < 6 mesi | ❌ Non misurabile |
| LTV / CAC | > 3x | Target > 10x | ❌ Non misurabile |
| Annual Billing Adoption | 30-50% | Target 40% | ⬜ Da misurare post-launch |

---

## 9. Rischi e Raccomandazioni

### Top 5 Rischi Pricing

| # | Rischio | Probabilità | Impatto | Mitigazione |
|---|---------|------------|---------|-------------|
| 1 | Prezzi troppo bassi per ARPU sostenibile | 🟡 Media | 🔴 Alto | Monitor ARPU, ready to increase P1 |
| 2 | Free tier cannibalizza conversioni | 🟡 Media | 🟡 Medio | Starter molto limitato (3 fatture/mo) |
| 3 | Enterprise €149 troppo basso per corporate | 🟢 Bassa | 🟡 Medio | Aggiungere Enterprise+ custom |
| 4 | AI COGS scala con volume | 🟡 Media | 🟡 Medio | Gemini Flash-Lite economico, pack add-on |
| 5 | Competitor war su pricing e-invoicing | 🟢 Bassa | 🔴 Alto | Mantenere all-inclusive value prop |

### Raccomandazioni Immediate

1. **Validare con 10-20 target customer** — Pre-launch pricing survey/interview
2. **Preparare AppSumo launch plan** — Per cash injection e community building
3. **Implementare metering dashboard** — Tracking consumo AI/storage/fatture per piano
4. **Setup Stripe Billing portal** — Gestione upgrade/downgrade/cancel self-service
5. **Definire churn playbook** — Azioni automatiche per utenti a rischio churn

---

*Documento generato il 2026-03-07. Prezzi basati su codice sorgente (`fase3_plan_pricing.php`, `fase3_addons.php`) e ricerca competitor diretta.*
