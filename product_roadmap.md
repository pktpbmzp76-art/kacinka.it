# Product Roadmap

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Current State (Baseline)

### Completato

| Area | Moduli/Feature | Status |
|------|---------------|--------|
| **Frontend** | 26 moduli, 234 files, React 19.2 | ✅ |
| **Backend** | 120 services, 677 actions, PHP 8.1 | ✅ |
| **Database** | 176 tabelle, MariaDB | ✅ |
| **API** | 1,059 routes | ✅ |
| **E-Invoicing** | 13 builders, 11 paesi | ✅ |
| **i18n** | 20 lingue, 62 namespace | ✅ |
| **AI** | Gemini 2.0 Flash + Flash-Lite | ✅ |
| **Open Banking** | Tink integration | ✅ |
| **Payment** | Stripe subscription + billing | ✅ |
| **5 Piani** | Starter/Pro/Pro+/Elite/Enterprise | ✅ |
| **Tests** | ~1,533+ test passanti (Vitest + Playwright) | ✅ |
| **Marketing Site** | Hub & spoke, 20 lingue, blog | ✅ |

---

## 2. Roadmap Phases

### Fase 0 — Pre-Launch Hardening (Week 1-4)

**Obiettivo**: Production-ready, zero critical bugs.

| # | Task | Priority | Effort | Owner |
|---|------|----------|--------|-------|
| 0.1 | Security audit (penetration test) | 🔴 Critica | 1 settimana | External + founder |
| 0.2 | Performance optimization (Lighthouse ≥ 90) | 🔴 Critica | 3 giorni | Founder |
| 0.3 | Error handling review (Sentry alerts) | 🔴 Critica | 2 giorni | Founder |
| 0.4 | GDPR compliance review | 🔴 Critica | 2 giorni | Legal + founder |
| 0.5 | SDI production gateway test (FatturaPA) | 🔴 Critica | 1 settimana | Founder |
| 0.6 | Stripe production billing test | 🔴 Critica | 2 giorni | Founder |
| 0.7 | Backup & disaster recovery test | 🟡 Alta | 1 giorno | Founder |
| 0.8 | Load testing (1000 concurrent users) | 🟡 Alta | 2 giorni | Founder |
| 0.9 | Documentation update (API docs, user guide) | 🟡 Alta | 3 giorni | Founder |
| 0.10 | Beta test with 20-50 Italian SMEs | 🔴 Critica | 2-4 settimane | Founder |

### Fase 1 — Launch Italy (Month 1-3)

**Obiettivo**: 100+ paying customers in Italia.

| # | Task | Priority | Effort | Impact |
|---|------|----------|--------|--------|
| 1.1 | ProductHunt + AppSumo launch | 🔴 Critica | 1 settimana | Brand awareness |
| 1.2 | Interactive onboarding tour | 🔴 Critica | 1-2 settimane | Conversion +30% |
| 1.3 | Light mode toggle | 🟡 Alta | 3-5 giorni | User retention |
| 1.4 | Global search (Cmd+K) | 🟡 Alta | 3 giorni | UX improvement |
| 1.5 | Mobile UX optimization | 🟡 Alta | 2 settimane | Mobile users |
| 1.6 | SDI corrispettivi (scontrini IT) | 🟡 Alta | 3 giorni | Italian completeness |
| 1.7 | SEO content: 12 blog posts IT | 🟡 Alta | Ongoing | Organic traffic |
| 1.8 | Partner program launch (commercialisti) | 🟡 Alta | 2 settimane | Channel sales |
| 1.9 | Customer feedback loop (Canny/similar) | 🟡 Alta | 2 giorni | Product-market fit |
| 1.10 | Weekly metrics dashboard (internal) | 🟢 Media | 1 giorno | Decision making |

### Fase 2 — Expansion Southern EU (Month 3-6)

**Obiettivo**: 400+ paying, 3 countries active (IT, ES, DE).

| # | Task | Priority | Effort | Impact |
|---|------|----------|--------|--------|
| 2.1 | Spain market entry (SII/Verifactu production) | 🔴 Critica | 1 settimana | New market |
| 2.2 | Germany market entry (XRechnung production) | 🔴 Critica | 1 settimana | New market |
| 2.3 | Native review IT/ES/DE i18n | 🟡 Alta | 1 settimana | Quality |
| 2.4 | Inventory management module | 🟡 Alta | 2-3 settimane | Feature gap fix |
| 2.5 | Multi-currency full accounting | 🟡 Alta | 1-2 settimane | EU cross-border |
| 2.6 | Project billing (time → invoice) | 🟡 Alta | 1 settimana | Freelancer feature |
| 2.7 | API documentation v2 (OpenAPI/Swagger) | 🟡 Alta | 1 settimana | Developer experience |
| 2.8 | Accessibility audit + WCAG AA fixes | 🟡 Alta | 1 settimana | Compliance |
| 2.9 | Paid ads launch (Google, LinkedIn) | 🟡 Alta | Setup + iterazione | Lead gen |
| 2.10 | Referral program live | 🟢 Media | 3 giorni | Organic growth |

### Fase 3 — Feature Expansion (Month 6-12)

**Obiettivo**: 1,500+ paying, 4+ countries, €75K MRR.

| # | Task | Priority | Effort | Impact |
|---|------|----------|--------|--------|
| 3.1 | France market entry (Factur-X mandato 2026-27) | 🔴 Critica | 1 settimana | New market |
| 3.2 | Poland market entry (KSeF mandato 2026) | 🟡 Alta | 1 settimana | New market |
| 3.3 | PWA (Progressive Web App) con offline | 🟡 Alta | 2 settimane | Mobile experience |
| 3.4 | AI enhancements (deal scoring, cash flow forecast) | 🟡 Alta | 3 settimane | AI differentiation |
| 3.5 | Workflow automation v2 (visual builder) | 🟡 Alta | 4 settimane | Power users |
| 3.6 | Client portal v2 (branded, custom domain) | 🟡 Alta | 2 settimane | Professional tier |
| 3.7 | Marketplace foundations (plugin architecture) | 🟢 Media | 4 settimane | Ecosystem |
| 3.8 | White-label v2 (full customization) | 🟢 Media | 3 settimane | Enterprise |
| 3.9 | E-invoice: Greece myDATA + Romania ANAF production | 🟡 Alta | 1 settimana | Market expansion |
| 3.10 | Hire: 1 frontend dev + 1 backend dev | 🔴 Critica | Ongoing | Team scaling |

### Fase 4 — Scale Pan-EU (Month 12-24)

**Obiettivo**: 5,000+ paying, 8+ countries, €300K MRR.

| # | Task | Priority | Effort | Impact |
|---|------|----------|--------|--------|
| 4.1 | PEPPOL Access Point registration | 🔴 Critica | 2-3 mesi (burocrazia) | Pan-EU e-invoicing |
| 4.2 | Turkey + Saudi Arabia market | 🟡 Alta | 2 settimane | Non-EU expansion |
| 4.3 | Remaining EU languages (10 lingue) | 🟢 Media | 1 settimana | Full EU coverage |
| 4.4 | Native mobile app (React Native) | 🟡 Alta | 2-3 mesi | Mobile-first |
| 4.5 | SOC2 Type II certification | 🔴 Critica | 3-6 mesi | Enterprise deals |
| 4.6 | Cloud migration (AWS/GCP) | 🟡 Alta | 1 mese | Scalability |
| 4.7 | Payroll module (basic) | 🟢 Media | 2 mesi | Feature completeness |
| 4.8 | POS module (basic) | 🟢 Media | 1 mese | Retail segment |
| 4.9 | Advanced analytics (BI dashboard) | 🟡 Alta | 1 mese | Enterprise value |
| 4.10 | Team: 5+ developers, 1 designer, 1 PM | 🔴 Critica | Ongoing | Execution scale |

---

## 3. Feature Prioritization Matrix

### MoSCoW per Fase

| Feature | Must | Should | Could | Won't (now) |
|---------|------|--------|-------|-------------|
| SDI production | ✅ F0 | | | |
| Security audit | ✅ F0 | | | |
| Onboarding tour | ✅ F1 | | | |
| Light mode | | ✅ F1 | | |
| Inventory management | | ✅ F2 | | |
| Multi-currency | | ✅ F2 | | |
| PWA | | | ✅ F3 | |
| Native mobile app | | | | ✅ F4 |
| Payroll | | | | ✅ F4 |
| POS | | | | ✅ F4 |
| Manufacturing | | | | ✅ Never (out of scope) |

---

## 4. Technical Debt Roadmap

| Item | Severity | Estimated Fix | Phase |
|------|----------|--------------|-------|
| ~1,533 tests ma coverage non misurata | 🟡 Media | 1 settimana (setup coverage) | F0 |
| PHP 8.1 (upgrade a 8.3) | 🟢 Bassa | 2 giorni | F1 |
| Shared hosting → VPS/Cloud | 🟡 Media | 1 settimana | F2 |
| Missing OpenAPI/Swagger spec | 🟡 Media | 1 settimana | F2 |
| No CI/CD pipeline | 🔴 Alta | 3 giorni | F0 |
| No staging environment | 🔴 Alta | 1 giorno | F0 |
| Migration system ad-hoc | 🟡 Media | 1 settimana | F1 |
| Logging inconsistent | 🟢 Bassa | 3 giorni | F1 |

---

## 5. Metric Targets per Phase

| Metric | Fase 0 | Fase 1 | Fase 2 | Fase 3 | Fase 4 |
|--------|--------|--------|--------|--------|--------|
| Paying customers | 0 | 100 | 400 | 1,500 | 5,000 |
| MRR | €0 | €3-5K | €18K | €75K | €300K |
| Countries | 0 | 1 (IT) | 3 | 5 | 8+ |
| Team size | 1 | 1-2 | 2-3 | 4-5 | 8-12 |
| Test coverage | ~1,533 | +500 | +1,000 | +2,000 | +5,000 |
| Uptime | — | 99% | 99.5% | 99.9% | 99.95% |
| NPS | — | 25+ | 35+ | 50+ | 60+ |

---

## 6. Risk Tracker

| Rischio | Phase | Probabilità | Mitigazione |
|---------|-------|------------|-------------|
| SDI rejection in production | F0 | 🟡 Media | Test estensivo con Agenzia Entrate sandbox |
| First users find critical bugs | F1 | 🔴 Alta | Beta test, error monitoring, rapid response |
| Slow organic growth | F1 | 🟡 Media | Paid ads backup, AppSumo injection |
| Competitor launches multi-country | F2 | 🟢 Bassa | Speed advantage, 13 builders already built |
| Scaling issues (hosting) | F3 | 🟡 Media | Migration plan ready, cloud-first architecture |
| Team hiring difficulties | F3-F4 | 🟡 Media | Remote hiring, competitive equity package |

---

*Documento generato il 2026-03-07. Roadmap basata su analisi codebase (519K LOC), gap analysis competitor, e market timing (ViDA 2028).*
