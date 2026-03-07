# Code Quality

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Executive Summary

| Area | Score | Livello |
|------|-------|---------|
| **Architecture Patterns** | 8.0/10 | ✅ Forte |
| **Code Organization** | 7.5/10 | ✅ Buono |
| **Type Safety** | 7.0/10 | ⚠️ Buono (migliorabile) |
| **Test Coverage** | 6.5/10 | ⚠️ Sufficiente |
| **Documentation** | 5.5/10 | ⚠️ Da migliorare |
| **DRY / Reuse** | 6.5/10 | ⚠️ Sufficiente |
| **Error Handling** | 7.0/10 | ⚠️ Buono |
| **Tech Debt** | 6.0/10 | ⚠️ Moderato |
| **OVERALL** | **6.8/10** | **⚠️ Buono** |

---

## 2. Codebase Scale

| Metrica | Valore |
|---------|--------|
| **Total LOC** | 519,535 |
| **PHP** | 376,235 LOC (72.4%) |
| **JS/JSX/TS/TSX** | 141,584 LOC (27.2%) |
| **CSS** | 1,716 LOC (0.3%) |
| **Development Time** | ~30 giorni |
| **LOC/Day** | ~17,318 |
| **Developer(s)** | 1 + AI assistants |

---

## 3. Architecture Patterns

### 3.1 Backend (PHP) — Action-Service-Repository

```
Request → Middleware Pipeline → Action → Service → Repository → Database
              (21 middleware)    (677)    (120+)     (PDO)       (176 tables)
```

| Layer | Files | LOC | Pattern | Valutazione |
|-------|-------|-----|---------|-------------|
| **Actions** | 677 | 51,141 | Single-responsibility action classes | ✅ Clean |
| **Services** | 137 | 67,536 | Business logic encapsulation | ✅ Well-structured |
| **Middleware** | 21 | 3,370 | Pipeline pattern (Slim 4) | ✅ Composable |
| **Repositories** | ~50 | ~8,000 | Data access layer | ⚠️ Some services do direct PDO |

### Architecture Quality

| Pattern | Adoption | Note |
|---------|----------|------|
| **Single Action per File** | ✅ 100% | Every endpoint = 1 PHP class |
| **Dependency Injection** | ✅ 100% | php-di container |
| **Interface Segregation** | ⚠️ Parziale | Some interfaces (EInvoiceBuilder), most services concrete |
| **Service Layer** | ✅ Forte | 137 services with clear boundaries |
| **Repository Pattern** | ⚠️ 60-70% | Some services query DB directly |
| **DTO Pattern** | ⚠️ Limitato | JSON arrays più che typed DTOs |
| **Event System** | ❌ Assente | Nessun event dispatcher pattern |

### 3.2 Frontend (React) — Component + Store Pattern

```
Route → Page Component → Feature Components → Zustand Store → API Layer
                                                  (33 stores)
```

| Layer | Details | Valutazione |
|-------|---------|-------------|
| **Pages** | 26 module directories, ~234 files total | ✅ |
| **Stores** | 33 Zustand stores, 7,173 LOC | ✅ Lightweight |
| **API Layer** | Fetch-based, per-module | ⚠️ No centralized API client |
| **Components** | Mix di page-level e shared | ⚠️ Limited reusable component library |
| **Validation** | Zod 4.3 schemas | ✅ Type-safe validation |
| **State Management** | Zustand 5.0 with selectors | ✅ Minimal re-renders |
| **Routing** | React Router 7.13 with lazy loading | ✅ Code splitting |

---

## 4. Type Safety

### 4.1 Backend (PHP)

| Pattern | Coverage | Note |
|---------|---------|------|
| **Strict types** | ⚠️ Non-universal | `declare(strict_types=1)` non in tutti i file |
| **Return types** | ⚠️ ~70% | Molte action return `ResponseInterface` |
| **Parameter types** | ⚠️ ~80% | PHP 8.1 union types utilizzati |
| **Property types** | ⚠️ ~60% | Mix di typed e untyped properties |
| **PHPStan/Psalm** | ❌ Non configurato | Nessun static analysis tool |

### 4.2 Frontend (TypeScript/JSX)

| Pattern | Coverage | Note |
|---------|---------|------|
| **TypeScript** | ✅ TS 5.9.3 configured | tsconfig.json presente |
| **Strict mode** | ⚠️ Da verificare | TypeScript strict options |
| **JSX/TSX mix** | ⚠️ Prima JSX, migrating to TSX | Legacy files still .jsx |
| **Zod schemas** | ✅ Per form validation | Runtime + compile-time safety |
| **Props typing** | ⚠️ ~60% | Mix di typed e untyped props |

---

## 5. Testing

### 5.1 Test Infrastructure

| Aspetto | Backend (PHP) | Frontend (React) |
|---------|--------------|-----------------|
| **Framework** | PHPUnit 11.5 | Vitest 4.0.18 |
| **E2E** | Playwright 1.58 | Playwright 1.58 |
| **Config** | phpunit.xml | vitest.config.* |
| **Test Files** | 159 files | 37 files |
| **Tests Count** | ~1,533+ passing | TBD |
| **CI Integration** | ❌ No CI | ❌ No CI |

### 5.2 Test Coverage Estimate

| Area | Test Files | Estimated Coverage | Note |
|------|-----------|-------------------|------|
| **Auth & Session** | ~15 | ~70% | Login, register, 2FA well-tested |
| **Invoicing** | ~20 | ~60% | Core CRUD + e-invoice tested |
| **CRM** | ~12 | ~50% | Deals, contacts tested |
| **Banking** | ~10 | ~50% | Reconciliation tested |
| **AI** | ~8 | ~40% | Chat + OCR basic tests |
| **E-Invoice Builders** | ~13 | ~60% | Per-builder tests |
| **Middleware** | ~10 | ~70% | Security middleware tested |
| **Admin** | ~15 | ~40% | Admin actions partially tested |
| **Frontend Components** | 37 | ~20% | Low frontend coverage |
| **OVERALL** | 196 files | **~45-50%** | ⚠️ Below 60% target |

### 5.3 Test Quality

| Aspetto | Valutazione |
|---------|-------------|
| Unit tests | ✅ Present for core services |
| Integration tests | ⚠️ Some DB-hitting tests |
| E2E tests | ⚠️ Playwright present but coverage unknown |
| Snapshot tests | ❌ None |
| Performance tests | ❌ None |
| Load tests | ❌ None |
| Mutation tests | ❌ None |

---

## 6. Linting & Formatting

### 6.1 ESLint Configuration

| Aspetto | Dettaglio |
|---------|----------|
| **Version** | ESLint 9.39.2 |
| **Config** | `eslint.config.js` (flat config) |
| **Plugins** | react-hooks, react-refresh |
| **Auto-fix** | ⚠️ Non configurato in CI |

### 6.2 PHP Linting

| Aspetto | Dettaglio |
|---------|----------|
| **PHP CS Fixer** | ❌ Non configurato |
| **PHPStan** | ❌ Non configurato |
| **Psalm** | ❌ Non configurato |
| **PHP_CodeSniffer** | ❌ Non configurato |

### 6.3 Formatting

| Tool | Status | Note |
|------|--------|------|
| Prettier | ❌ Non configurato | Nessun formatter JS standard |
| EditorConfig | ⚠️ Da verificare | Para consistent indentation |
| PHP-CS-Fixer | ❌ | Nessun auto-formatter PHP |

---

## 7. Documentation

### 7.1 Code Documentation

| Tipo | Coverage | Note |
|------|---------|------|
| **PHPDoc** | ⚠️ ~40-50% | `check_phpdoc_coverage.php` tool exists |
| **JSDoc** | ⚠️ ~30-40% | `check_jsdoc_coverage.js` tool exists |
| **README** | ✅ Present | Root README.md |
| **API Docs** | ⚠️ Partial | `generate_api_docs.js` exists |
| **Architecture Decision Records (ADRs)** | ❌ None | No decision documentation |
| **CHANGELOG** | ✅ Present | CHANGELOG.md maintained |

### 7.2 Documentation Tools (in `_build/`)

| Tool | Purpose |
|------|---------|
| `check_jsdoc_coverage.js` | JS documentation coverage analysis |
| `check_phpdoc_coverage.php` | PHP documentation coverage analysis |
| `generate_api_docs.js` | API documentation generator |
| `generate_jsdoc.js` | JSDoc documentation generator |
| `generate_phpdoc.js` | PHPDoc documentation generator |
| `generate_changelog.js` | Changelog generator |

---

## 8. Tech Debt Inventory

### 8.1 High Priority

| Debt Item | Impact | Effort | Priority |
|-----------|--------|--------|----------|
| No CI/CD pipeline | Risk of shipping broken code | 8h | 🔴 Critico |
| No static analysis (PHPStan) | Type errors undetected | 4h | 🔴 Critico |
| No PHP formatter | Inconsistent code style | 2h | 🟡 Media |
| Mixed JSX/TSX files | TS benefits not universal | 16h | 🟡 Media |
| Low frontend test coverage (~20%) | UI regressions undetected | 40h | 🟡 Media |

### 8.2 Medium Priority

| Debt Item | Impact | Effort |
|-----------|--------|--------|
| Repository pattern incomplete | Services with direct DB access | 24h |
| No event dispatcher | Tight coupling between services | 16h |
| No centralized API client (frontend) | Code duplication in API calls | 8h |
| No DTO classes (backend) | Array-based data passing | 32h |
| Missing interfaces for services | Hard to mock for testing | 24h |

### 8.3 Low Priority

| Debt Item | Impact | Effort |
|-----------|--------|--------|
| No ADRs | Architecture decisions undocumented | 8h |
| No Prettier config | Minor formatting inconsistencies | 1h |
| No snapshot tests | UI regressions | 16h |
| No mutation testing | Test quality unknown | 8h |

---

## 9. Positive Patterns

| Pattern | Evidence | Note |
|---------|---------|------|
| **Single-action classes** | 677 action files, 1 class per file | Excellent SRP |
| **Service encapsulation** | 137 services with clear domains | Well-organized |
| **Middleware composition** | 21 middleware in clean pipeline | Composable security |
| **Zustand stores** | 33 lightweight stores | Modern state management |
| **Zod validation** | Frontend form validation | Type-safe |
| **i18next lazy loading** | 20 languages × 62 namespaces | Performance-aware |
| **Monolog logging** | Structured logging via monolog 3.10 | Good observability |
| **Sentry integration** | Both PHP + React | Error tracking |
| **Idempotent migrations** | Re-runnable migration scripts | Safe deployment |
| **Build tooling** | 70+ build/audit scripts | Automation mindset |

---

## 10. Raccomandazioni

### Quick Wins (< 4 ore)
1. Configurare PHPStan level 5 → catturare type errors
2. Aggiungere Prettier per auto-formatting JS/TS
3. Configurare `pre-commit` hook per lint
4. Aggiungere `declare(strict_types=1)` globalmente

### Short-term (1-2 settimane)
5. Setup GitHub Actions CI: lint + test + PHPStan
6. Migrare tutti i .jsx a .tsx con tipi
7. Creare API client centralizzato (frontend)
8. Scrivere test per i top 20 services per LOC

### Medium-term (1-3 mesi)
9. Implementare event dispatcher (PSR-14)
10. Estrarre repository pattern completo
11. Raggiungere 60% code coverage
12. ADR framework per decisioni architetturali

---

*Documento generato il 2026-03-07. Metriche da analisi statica: 519,535 LOC totali, 677 actions (51,141 LOC), 137 services (67,536 LOC), 21 middleware (3,370 LOC), 159 PHP test files, 37 JS test files.*
