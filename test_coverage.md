# Test Coverage Report

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Executive Summary

| Metrica | Valore | Target |
|---------|--------|--------|
| **PHP Unit Test Files** | 141 | 300+ |
| **PHP Integration Test Files** | 17 | 50+ |
| **JS Unit/Component Test Files** | 41 (37 app + 4 admin) | 100+ |
| **E2E Test Files (Playwright)** | 75 | — |
| **Total Test Files** | 274 | 500+ |
| **Estimated Line Coverage PHP** | ~45-50% | 70% |
| **Estimated Line Coverage JS** | ~30-40% | 60% |
| **E2E Flow Coverage** | ~80% of critical paths | 90% |
| **Overall Score** | **6.0/10** | **8.0/10** |

---

## 2. Test Pyramid

```
         ▲
        /E2E\         75 Playwright specs (broad, slow)
       /──────\
      / Component \    41 JS unit/component tests (mid)
     /──────────────\
    /   Unit Tests   \  158 PHP unit+integration tests (fast)
   /──────────────────\
  ────────────────────────
        IDEAL PYRAMID

  Current shape: ⚠️ INVERTED — more E2E than unit tests in frontend
```

### Shape Analysis

| Layer | Ideal % | Actual % | Gap |
|-------|---------|----------|-----|
| Unit tests | 70% | 58% (158 PHP) | ⚠️ PHP OK, JS deficit |
| Component/Integration | 20% | 15% (41 JS) | ⚠️ Too few |
| E2E | 10% | 27% (75 Playwright) | ✅ Extensive |

---

## 3. PHP Backend Tests

### 3.1 Overview

| Tipo | Files | Framework | Runner |
|------|-------|-----------|--------|
| Unit | 141 | PHPUnit 11.5 | `vendor/bin/phpunit` |
| Integration | 17 | PHPUnit 11.5 | MariaDB service in CI |
| **Total** | **158** | | |

### 3.2 Coverage by Domain (Estimated)

| Domain | Action Files | Service Files | Test Files | Coverage Est. |
|--------|-------------|---------------|------------|--------------|
| Auth | 11 | 4 | ~15 | ✅ 70%+ |
| InvoiceV2 | 33 | 2 | ~12 | ⚠️ 50% |
| E-Invoice | 30 | 1 (3,646 LOC) | ~10 | ⚠️ 40% |
| CRM/Contact | 15 | 3 | ~8 | ⚠️ 45% |
| Subscription | 17 | 2 | ~10 | ✅ 60% |
| Task | 24 | 2 | ~8 | ⚠️ 40% |
| Banking | 23 | 3 | ~8 | ⚠️ 40% |
| Admin | 46 | 5 | ~10 | ❌ 25% |
| Expense | 22 | 1 | ~8 | ⚠️ 45% |
| AI | 9 | 3 | ~8 | ⚠️ 50% |
| Middleware | 21 | — | ~6 | ⚠️ 35% |
| Other (~400 actions) | 400+ | 110+ | ~55 | ❌ 20-30% |

### 3.3 CI Integration

```yaml
test-backend:
  services:
    mysql:
      image: mariadb:10.11
  steps:
    - PHPUnit --no-coverage
```

⚠️ **Coverage reporting not enabled** in CI (`--no-coverage` flag). No coverage threshold enforcement.

---

## 4. Frontend Tests

### 4.1 Vitest Unit/Component Tests

| Area | Files | Framework |
|------|-------|-----------|
| **Store tests** | ~30 | Vitest 4.0 |
| **Component tests** | 3 | Vitest + @testing-library/react |
| **Library/util tests** | 3 | Vitest |
| **Page tests** | 1 | Vitest |
| **Admin tests** | 4 | Vitest |
| **Total** | **41** | |

### 4.2 Store Test Coverage

Stores with dedicated test files:
- `accountingStore`, `analyticsStore`, `apiKeysStore`, `appStore`, `authStore`
- `bankingStore`, `brandingStore`, `budgetStore`, `calendarStore`, `chatStore`
- `clientPortalStore`, `contactsStore`, `crmAccountingStore`, `crmStore`
- `einvoiceStore`, `emailMarketingStore`, `expensesStore`, `filesStore`
- `fiscalStore`, `formsStore`, `invoicesStore`, `ocrReceiptsStore`
- `openBankingStore`, + others

**~30/33 stores tested** (91% coverage of store layer) ✅

### 4.3 Frontend Test Gaps

| Gap | Impatto | Effort |
|-----|---------|--------|
| Component tests (3 only) | 🔴 | 40h to reach 50+ |
| Page integration tests (1 only) | 🔴 | 24h to reach 20+ |
| Hook tests (0) | 🟡 | 16h |
| Accessibility tests (0 in Vitest) | 🟡 | 8h |
| Snapshot tests | 🟢 | 4h |

---

## 5. E2E Tests (Playwright)

### 5.1 Overview

| Metrica | Valore |
|---------|--------|
| Framework | Playwright 1.58 |
| Test files | 75 |
| Total size | ~550KB |
| Avg LOC per file | ~200 |
| CI Integration | ❌ Not in pipeline |

### 5.2 E2E Coverage by Domain

| Categoria | Spec Files | Coverage |
|-----------|-----------|----------|
| **E-Invoice** | 14 (IT, DE, ES, FR, GR, IN, PL, RO, SA, TR, Peppol, Onboarding, Placeholder, Prod) | ✅ Extensive |
| **Audit/Smoke** | 12 (admin, ai, api, app, auth, crosscutting, i18n, modules, pages, public, full, admin-full) | ✅ Broad |
| **Core Flows** | 10 (login, register, invoice, dashboard, settings, navigation, billing, checkout, forgot-password, responsive) | ✅ Critical paths |
| **Phase Testing** | 10 (phase0-phase12) | ✅ Phased validation |
| **OCR/Expense** | 4 (expense-ocr-flow, expense, ocr-multinational, import-csv-pdf) | ✅ Good |
| **Banking** | 3 (banking-reconciliation, openbanking, i18n-banking-all) | ⚠️ Adequate |
| **AI** | 4 (ai-mention, ai-suggestions, audit-ai-full, audit-ai-translations) | ✅ Good |
| **Workflow** | 2 (workflow-automation, workspace-modules) | ⚠️ Basic |
| **i18n** | 5 (i18n-a11y, i18n-banking, i18n-einvoice, i18n-ocr, phase8-encoding) | ✅ Good |
| **Production** | 3 (prod-smoke, prod-einvoice, prod-onboarding) | ✅ Critical |
| **Other** | 8 (remaining specs) | Mixed |

### 5.3 Notable Large E2E Specs

| File | Size | Focus |
|------|------|-------|
| audit-pages.spec.js | 26 KB | Full page navigation audit |
| audit-ai-full.spec.js | 24 KB | Complete AI feature testing |
| audit-public.spec.js | 22 KB | Public-facing page validation |
| prod-onboarding.spec.js | 15 KB | Production onboarding flow |
| audit-auth.spec.js | 13 KB | Authentication flow audit |
| audit-admin-full.spec.js | 13 KB | Admin panel full audit |

---

## 6. Test Quality Assessment

### 6.1 Strengths

| # | Strength | Detail |
|---|----------|--------|
| 1 | **Store tests comprehensive** | ~30/33 Zustand stores tested |
| 2 | **E2E breadth** | 75 specs cover all major user flows |
| 3 | **E-invoice E2E** | 14 country-specific E2E tests |
| 4 | **Production smoke tests** | Dedicated prod-smoke, prod-einvoice, prod-onboarding |
| 5 | **CI backend tests** | PHPUnit runs with real MariaDB in CI |
| 6 | **Phase-based validation** | 10 phase specs document feature completeness |

### 6.2 Weaknesses

| # | Weakness | Impact | Fix Effort |
|---|----------|--------|-----------|
| 1 | **No coverage metrics** | Cannot track regression | 2h (enable --coverage) |
| 2 | **Frontend component tests minimal** | UI regressions undetected | 40h |
| 3 | **E2E not in CI** | Manual validation only | 4h |
| 4 | **No mutation testing** | Test quality unvalidated | 8h |
| 5 | **No contract tests** | API changes can break frontend silently | 12h |
| 6 | **No visual regression** | CSS changes undetected | 8h |
| 7 | **Integration tests few** (17 PHP) | Service interactions not validated | 24h |

---

## 7. Coverage Improvement Roadmap

### Phase 1: Quick Wins (Settimane 1-2) — Score: 6.0 → 7.0

| # | Azione | Effort | Impact |
|---|--------|--------|--------|
| 1 | Enable `--coverage` in PHPUnit CI job | 2h | Visibility |
| 2 | Add Vitest `--coverage` with threshold 30% | 2h | Baseline |
| 3 | Add Playwright to CI (headless) | 4h | E2E in pipeline |
| 4 | Coverage badge in README | 30min | Visibility |
| 5 | Fix any flaky E2E tests for CI reliability | 4h | CI stability |

### Phase 2: Foundation (Mesi 1-3) — Score: 7.0 → 7.5

| # | Azione | Effort | Impact |
|---|--------|--------|--------|
| 6 | Write 20+ component tests (critical UI) | 20h | Component coverage |
| 7 | Write 10+ PHP integration tests (happy paths) | 16h | Service interaction |
| 8 | Enforce coverage floor: PHP 50%, JS 40% | 2h | Quality gate |
| 9 | Page snapshot tests for all major views | 8h | Visual regression |

### Phase 3: Maturity (Mesi 3-6) — Score: 7.5 → 8.0

| # | Azione | Effort | Impact |
|---|--------|--------|--------|
| 10 | Mutation testing (Infection PHP, Stryker JS) | 16h | Test quality |
| 11 | API contract tests (Pact or similar) | 12h | API stability |
| 12 | Raise coverage floor: PHP 70%, JS 60% | — | Continuous |
| 13 | Visual regression (Playwright screenshots) | 8h | UI stability |
| 14 | Accessibility tests (axe-core in Playwright) | 8h | A11y compliance |

---

## 8. Testing Tools & Recommendations

### Current Stack

| Tool | Version | Purpose | Assessment |
|------|---------|---------|-----------|
| PHPUnit | 11.5 | PHP unit + integration | ✅ Latest |
| Vitest | 4.0 | JS unit + component | ✅ Latest |
| Playwright | 1.58 | E2E browser testing | ✅ Latest |
| @testing-library/react | — | React component testing | ✅ Present |

### Recommended Additions

| Tool | Purpose | Priority | Effort |
|------|---------|----------|--------|
| **Infection** | PHP mutation testing | 🟡 | 4h setup |
| **Stryker** | JS mutation testing | 🟡 | 4h setup |
| **Pact** | Contract testing | 🟡 | 12h |
| **axe-core** | Accessibility testing in E2E | 🟡 | 4h |
| **Percy / Chromatic** | Visual regression | 🟢 | 8h |
| **Codecov / Coveralls** | Coverage tracking SaaS | 🔴 | 1h |

---

*Documento generato il 2026-03-07. Analisi basata su: 158 PHP test files, 41 JS test files, 75 Playwright E2E specs, struttura auth/tests/, app/src/, app/e2e/. Coverage percentuali stimate in assenza di coverage report formali.*
