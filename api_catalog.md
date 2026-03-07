# API Catalog

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Overview

| Metrica | Valore |
|---------|--------|
| **Totale Routes** | 1,059 |
| **Routes File** | `auth/app/routes.php` (1,460 LOC) |
| **Base URL** | `https://api.kacinka.app` |
| **Authentication** | JWT Bearer Token + API Key |
| **Format** | JSON (request/response) |
| **Versioning** | `/api/v1/` prefix |

---

## 2. Route Distribution

### By HTTP Method

| Method | Count | % | Uso |
|--------|-------|---|-----|
| **GET** | 502 | 47.4% | Read operations, list, search |
| **POST** | 348 | 32.9% | Create, actions, uploads |
| **PUT** | 99 | 9.4% | Full update |
| **DELETE** | 107 | 10.1% | Soft/hard delete |
| **PATCH** | 3 | 0.3% | Partial update |
| **TOTAL** | **1,059** | **100%** | |

### By Domain (82 Action Directories)

| Domain | Actions | Estimated Routes | % |
|--------|---------|-----------------|---|
| Admin | 46 | ~80 | 7.6% |
| InvoiceV2 | 33 | ~50 | 4.7% |
| EInvoice | 30 | ~45 | 4.3% |
| Task | 24 | ~35 | 3.3% |
| Bank | 23 | ~35 | 3.3% |
| Tax | 23 | ~35 | 3.3% |
| Accounting | 22 | ~35 | 3.3% |
| Expense | 22 | ~35 | 3.3% |
| Payment | 22 | ~35 | 3.3% |
| Subscription | 17 | ~25 | 2.4% |
| Chat | 16 | ~25 | 2.4% |
| Contact | 15 | ~25 | 2.4% |
| Dashboard | 14 | ~25 | 2.4% |
| SiteBuilder | 14 | ~25 | 2.4% |
| Report | 13 | ~20 | 1.9% |
| Webhook | 12 | ~20 | 1.9% |
| Auth | 11 | ~20 | 1.9% |
| Deal | 11 | ~18 | 1.7% |
| ApiKey | 11 | ~18 | 1.7% |
| File | 11 | ~18 | 1.7% |
| Ocr | 11 | ~18 | 1.7% |
| OpenBanking | 10 | ~18 | 1.7% |
| Calendar | 10 | ~15 | 1.4% |
| Currency | 10 | ~15 | 1.4% |
| Pipeline | 9 | ~15 | 1.4% |
| Ai | 9 | ~15 | 1.4% |
| Other (57 dirs) | ~200 | ~320 | 30.2% |

---

## 3. API Endpoint Categories

### 3.1 Authentication & User

```
POST   /api/v1/auth/login
POST   /api/v1/auth/register
POST   /api/v1/auth/refresh
POST   /api/v1/auth/forgot-password
POST   /api/v1/auth/reset-password
POST   /api/v1/auth/verify-email
POST   /api/v1/auth/verify-2fa
GET    /api/v1/auth/me
PUT    /api/v1/auth/profile
POST   /api/v1/auth/logout
GET    /api/v1/auth/oauth/{provider}
```

### 3.2 Projects & Workspace

```
GET    /api/v1/projects
POST   /api/v1/projects
GET    /api/v1/projects/{id}
PUT    /api/v1/projects/{id}
DELETE /api/v1/projects/{id}
POST   /api/v1/projects/{id}/settings
GET    /api/v1/projects/{id}/dashboard
```

### 3.3 CRM (Deals & Pipeline)

```
GET    /api/v1/projects/{pid}/deals
POST   /api/v1/projects/{pid}/deals
GET    /api/v1/projects/{pid}/deals/{id}
PUT    /api/v1/projects/{pid}/deals/{id}
DELETE /api/v1/projects/{pid}/deals/{id}
PUT    /api/v1/projects/{pid}/deals/{id}/stage
GET    /api/v1/projects/{pid}/pipelines
POST   /api/v1/projects/{pid}/pipelines
PUT    /api/v1/projects/{pid}/pipelines/{id}
DELETE /api/v1/projects/{pid}/pipelines/{id}
```

### 3.4 Invoicing

```
GET    /api/v1/projects/{pid}/invoices
POST   /api/v1/projects/{pid}/invoices
GET    /api/v1/projects/{pid}/invoices/{id}
PUT    /api/v1/projects/{pid}/invoices/{id}
DELETE /api/v1/projects/{pid}/invoices/{id}
POST   /api/v1/projects/{pid}/invoices/{id}/send
POST   /api/v1/projects/{pid}/invoices/{id}/duplicate
POST   /api/v1/projects/{pid}/invoices/{id}/pdf
POST   /api/v1/projects/{pid}/invoices/{id}/mark-paid
GET    /api/v1/projects/{pid}/invoices/{id}/payments
POST   /api/v1/projects/{pid}/invoices/{id}/payments
...    (~50 total invoice routes)
```

### 3.5 E-Invoicing

```
GET    /api/v1/projects/{pid}/einvoice/config
PUT    /api/v1/projects/{pid}/einvoice/config
POST   /api/v1/projects/{pid}/einvoice/generate
POST   /api/v1/projects/{pid}/einvoice/submit
GET    /api/v1/projects/{pid}/einvoice/status/{id}
GET    /api/v1/projects/{pid}/einvoice/history
POST   /api/v1/projects/{pid}/einvoice/validate
GET    /api/v1/projects/{pid}/einvoice/formats
POST   /api/v1/projects/{pid}/einvoice/onboarding
...    (~45 total einvoice routes)
```

### 3.6 Expenses & OCR

```
GET    /api/v1/projects/{pid}/expenses
POST   /api/v1/projects/{pid}/expenses
GET    /api/v1/projects/{pid}/expenses/{id}
PUT    /api/v1/projects/{pid}/expenses/{id}
DELETE /api/v1/projects/{pid}/expenses/{id}
POST   /api/v1/projects/{pid}/expenses/ocr
POST   /api/v1/projects/{pid}/expenses/import
GET    /api/v1/projects/{pid}/expenses/categories
...    (~35 total expense + ocr routes)
```

### 3.7 Banking & Payments

```
GET    /api/v1/projects/{pid}/bank-accounts
POST   /api/v1/projects/{pid}/bank-accounts
GET    /api/v1/projects/{pid}/bank-accounts/{id}/transactions
POST   /api/v1/projects/{pid}/bank-accounts/{id}/import
POST   /api/v1/projects/{pid}/bank-accounts/{id}/reconcile
GET    /api/v1/projects/{pid}/open-banking/connect
POST   /api/v1/projects/{pid}/open-banking/sync
GET    /api/v1/projects/{pid}/payments
POST   /api/v1/projects/{pid}/payments
...    (~70 total banking/payment routes)
```

### 3.8 AI & Chat

```
POST   /api/v1/projects/{pid}/ai/chat
POST   /api/v1/projects/{pid}/ai/suggest
GET    /api/v1/projects/{pid}/ai/history
POST   /api/v1/projects/{pid}/ai/feedback
POST   /api/v1/projects/{pid}/ai/escalate
GET    /api/v1/projects/{pid}/chat/channels
POST   /api/v1/projects/{pid}/chat/messages
GET    /api/v1/projects/{pid}/chat/messages
...    (~40 total ai + chat routes)
```

---

## 4. Authentication Methods

### 4.1 JWT Authentication (Primary)

```
Authorization: Bearer <jwt_token>
```
- Token issued on `/auth/login`
- Contains: user_id, project_id, role, plan, permissions
- Expires: configurable (default 24h)
- Refresh: via `/auth/refresh`

### 4.2 API Key Authentication

```
X-API-Key: <api_key>
```
- Created via `/api-keys` endpoints
- Scoped permissions per key
- Rate limited independently
- ApiKeyAuthMiddleware validates (372 LOC)

---

## 5. Rate Limiting

| Endpoint Type | Limit | Window |
|--------------|-------|--------|
| Auth endpoints | 10 req | 1 min |
| General API | 100 req | 1 min |
| File upload | 20 req | 1 min |
| AI calls | Plan-based (5-∞) | 1 month |
| Email sending | Plan-based | 1 month |
| API Key endpoints | 500 req | 1 min |

---

## 6. API Documentation Status

| Aspetto | Status | Note |
|---------|--------|------|
| OpenAPI/Swagger spec | ❌ Mancante | Route file leggibile ma non standardizzato |
| Auto-generated docs | ⚠️ Parziale | `generate_api_docs.js` esiste in _build/ |
| Postman collection | ❌ Mancante | Da generare |
| Example responses | ❌ Mancante | Solo nel codice |
| Error code catalog | ⚠️ Parziale | HTTP status standard |
| Changelog API | ❌ Mancante | Nessun versioning changelog |

### Raccomandazione
Generare OpenAPI 3.0 spec da routes.php + action PHPDoc per documentazione automatica e Swagger UI.

---

*Documento generato il 2026-03-07. Route count da `auth/app/routes.php` (1,460 LOC, 1,059 routes). Endpoint examples dedotti dalla struttura action directories.*
