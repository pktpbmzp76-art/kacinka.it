# Security Audit

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Executive Summary

| Categoria | Score | Livello |
|-----------|-------|---------|
| **Authentication** | 8.5/10 | ✅ Forte |
| **Authorization** | 7.5/10 | ✅ Buono |
| **Input Validation** | 7.0/10 | ⚠️ Sufficiente |
| **Cryptography** | 9.0/10 | ✅ Eccellente |
| **Session Management** | 8.5/10 | ✅ Forte |
| **API Security** | 8.0/10 | ✅ Buono |
| **Infrastructure** | 6.5/10 | ⚠️ Migliorabile |
| **Compliance** | 7.0/10 | ⚠️ Sufficiente |
| **OVERALL** | **7.8/10** | **✅ Buono** |

---

## 2. Authentication Architecture

### 2.1 JWT Implementation

| Aspetto | Implementazione | Valutazione |
|---------|----------------|-------------|
| **Library** | firebase/php-jwt 7.0 | ✅ Mature, audited |
| **Token Storage** | HttpOnly cookie (primary) + Bearer header | ✅ XSS-resistant |
| **Token Type Check** | `$decoded->type === 'access'` | ✅ Prevents token confusion |
| **Refresh Flow** | Separate refresh token + rotation | ✅ Best practice |
| **Grace Period** | 30s grace on token rotation | ✅ Multi-tab safe |

### 2.2 Password Security

| Aspetto | Implementazione | Valutazione |
|---------|----------------|-------------|
| **Algorithm** | Argon2id | ✅ OWASP recommended, memory-hard |
| **Verification** | `password_verify()` (constant-time) | ✅ No timing attacks |
| **Reset Flow** | Time-limited token via email | ✅ Standard pattern |
| **Lockout** | After N failed attempts → account_lockouts table | ✅ Brute-force protection |

### 2.3 Multi-Factor Authentication

| Aspetto | Implementazione | Valutazione |
|---------|----------------|-------------|
| **Type** | TOTP (Time-based One-Time Password) | ✅ Industry standard |
| **Recovery Codes** | Stored hashed in two_factor_auth | ✅ Backup access |
| **Enforcement** | Optional per-user | ⚠️ Non enforced per piani alti |

### 2.4 OAuth

| Aspetto | Implementazione | Valutazione |
|---------|----------------|-------------|
| **Providers** | Google (calendar, drive, contacts) | ✅ Scoped access |
| **Token Storage** | Encrypted in user_integrations | ✅ At-rest encryption |
| **Status Tracking** | active/expired/revoked/disconnected | ✅ Full lifecycle |

---

## 3. Authorization Framework

### 3.1 RBAC (Role-Based Access Control)

```
Roles: owner > admin > member > viewer > ai_bot
              ↓
     role_permissions (M:N)
              ↓
         permissions (module-scoped)
```

| Aspetto | Valutazione |
|---------|-------------|
| 5 system roles + custom roles | ✅ Flexible |
| Module-level gating (PlanLimitsMiddleware 708 LOC) | ✅ Plan enforcement |
| Per-permission checks (PermissionMiddleware 94 LOC) | ✅ Fine-grained |
| Entitlement system (EntitlementMiddleware 168 LOC) | ✅ Feature flags |

### 3.2 Middleware Pipeline (21 Middleware, 3,370 LOC)

```
Request → CORS → SecurityHeaders → RateLimit → CSRF
        → Auth → Permission → PlanLimits → Entitlement
        → TrialGate → FrozenProject → GracePeriod
        → SmtpGate → RequestLogger → QueryTiming
        → ApiVersion → Maintenance → Admin
        → ApiKeyAuth → HttpCache → [Action]
```

### 3.3 API Key Security

| Aspetto | Implementazione | Valutazione |
|---------|----------------|-------------|
| **Key Format** | `kk_` prefix + random (ApiKeyAuthMiddleware 372 LOC) | ✅ Identifiable |
| **Storage** | Hash-only in DB (key_hash) | ✅ No plaintext storage |
| **Scopes** | Per-key permission scoping | ✅ Least privilege |
| **Rate Limiting** | Per-key limits (api_key_rate_limits) | ✅ Abuse prevention |
| **Usage Tracking** | api_key_usage table with latency_ms | ✅ Audit trail |

---

## 4. CSRF Protection

### Implementation (CsrfMiddleware.php, 158 LOC)

| Aspetto | Dettaglio | Valutazione |
|---------|----------|-------------|
| **Pattern** | Double-submit cookie | ✅ Stateless CSRF |
| **Token Generation** | `bin2hex(random_bytes(32))` — 64 hex chars | ✅ Cryptographically secure |
| **Validation** | `hash_equals()` — constant-time comparison | ✅ No timing leak |
| **Rotation** | After every state-changing request | ✅ One-time use |
| **Cookie Config** | `Secure=true, SameSite=None, Max-Age=86400` | ⚠️ SameSite=None richiesto per CORS cross-origin |
| **Exemptions** | Auth routes, Stripe webhooks, OAuth, API key requests | ✅ Reasonable exceptions |

---

## 5. Security Headers

### SecurityHeadersMiddleware.php (92 LOC)

| Header | Valore | Note |
|--------|--------|------|
| `X-Content-Type-Options` | `nosniff` | ✅ MIME sniffing prevention |
| `X-Frame-Options` | `DENY` (API) / `ALLOWALL` (preview) | ✅ Clickjacking protection |
| `Referrer-Policy` | `strict-origin-when-cross-origin` | ✅ Privacy |
| `Permissions-Policy` | camera=(), microphone=(), geolocation=(), payment=(self) | ✅ Feature restriction |
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains; preload` | ✅ HSTS (production) |
| `X-Robots-Tag` | `noindex, nofollow` | ✅ API not indexed |

### Content Security Policy (CSP)

**API Routes** (Maximally restrictive):
```
default-src 'none'; script-src 'none'; frame-ancestors 'none'
```

**Preview Routes** (Site builder):
```
default-src 'self'; script-src 'self' 'unsafe-inline';
style-src 'self' 'unsafe-inline' fonts.googleapis.com;
img-src 'self' data:; font-src fonts.gstatic.com;
frame-ancestors *.kacinka.app
```

| Aspetto | Valutazione |
|---------|-------------|
| Dual CSP profile | ✅ Context-appropriate |
| `unsafe-inline` su preview | ⚠️ Necessario per site builder, ma rischio XSS |
| Nonce-based CSP | ❌ Non implementato (miglioramento futuro) |

---

## 6. Rate Limiting

### RateLimitMiddleware.php (245 LOC)

| Endpoint | Limite | Finestra | Storage |
|----------|--------|----------|---------|
| `/login` | 5 req | 1 min | DB (login_attempts) |
| `/register` | 3 req | 1 hour | DB |
| `/forgot-password` | 3 req | 15 min | DB |
| `/ai/chat` | 15 req | 1 min | APCu |
| API (default) | 120 req | 1 min | APCu |

### Plan-Based Multipliers

| Piano | Multiplier | Effective API Limit |
|-------|-----------|-------------------|
| Starter | 0.25× | 30 req/min |
| Pro | 1.0× | 120 req/min |
| Business/Pro+ | 2.5× | 300 req/min |
| Enterprise | 10× | 1,200 req/min |

### Alerting
- Telegram alert se violations > 2× limit
- Throttled: max 1 alert per 10 min per key
- Response headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`, `Retry-After`

---

## 7. Input Validation & Injection Prevention

### 7.1 SQL Injection

| Pattern | Implementazione | Valutazione |
|---------|----------------|-------------|
| **PDO Prepared Statements** | Utilizzati in tutti i Repository | ✅ Parametrizzazione completa |
| **ORM** | Nessun ORM — raw PDO | ⚠️ Più rischio umano |
| **Dynamic queries** | Whitelist-based column names dove necessario | ✅ May need review |

### 7.2 XSS Prevention

| Pattern | Implementazione | Valutazione |
|---------|----------------|-------------|
| **Output encoding** | `htmlspecialchars()` in email templates, XML builders | ✅ Context-aware |
| **Frontend** | React auto-escapes JSX | ✅ Built-in protection |
| **DOMPurify** | Client-side HTML sanitization (TipTap editor) | ✅ Rich text safe |
| **AI Output** | AiPolicy::sanitizeResponse() strips HTML/code/URLs | ✅ AI-specific XSS prevention |

### 7.3 AI Security (AiPolicy.php, 227 LOC)

| Aspetto | Implementazione | Valutazione |
|---------|----------------|-------------|
| **Content Policy** | Mandatory system prompt with allowed/forbidden topics | ✅ Topic guardrails |
| **Prompt Injection** | 18 regex patterns detecting jailbreak attempts | ✅ Comprehensive |
| **Output Sanitization** | Strips internal URLs, DB tables, SQL, server paths, credentials | ✅ Data leakage prevention |
| **Information Hiding** | Never reveals architecture, endpoints, DB schema, credentials | ✅ Attack surface reduction |

---

## 8. Data Protection

### 8.1 Encryption

| Data | At Rest | In Transit |
|------|---------|-----------|
| **Passwords** | Argon2id hash | N/A |
| **JWT tokens** | N/A | HTTPS + HttpOnly cookie |
| **API keys** | SHA-256 hash | HTTPS |
| **Session tokens** | SHA-256 hash | HTTPS |
| **SMTP passwords** | AES encryption (smtp_pass_encrypted) | N/A |
| **OAuth tokens** | Encrypted column | HTTPS |
| **AI interactions** | Plaintext in DB | HTTPS |
| **File uploads** | Plaintext on disk (Flysystem) | HTTPS |

### 8.2 Sensitive Data Handling

| Pattern | Valutazione |
|---------|-------------|
| No plaintext passwords anywhere | ✅ |
| API keys hash-only storage | ✅ |
| SMTP credentials AES-encrypted | ✅ |
| Credit card data → Stripe (never stored) | ✅ |
| PII in logs → minimal (email only for auth logs) | ⚠️ |
| Database encryption at rest | ❌ (dipende da Hostinger) |

---

## 9. Session Management

### SessionService.php

| Aspetto | Implementazione | Valutazione |
|---------|----------------|-------------|
| **Token hashing** | `hash('sha256', $refreshToken)` in DB | ✅ No plaintext tokens |
| **Rotation** | New token on refresh | ✅ Token replay prevention |
| **Grace period** | 30s for old token after rotation | ✅ Multi-tab UX |
| **Revoke all** | "Disconnect everywhere" (except current) | ✅ Compromised account recovery |
| **Device tracking** | UA parsing for session list | ✅ User visibility |
| **Persistent sessions** | remember_me flag → extended TTL | ✅ UX friendly |

---

## 10. CORS Configuration

### CorsMiddleware.php (110 LOC)

| Aspetto | Implementazione | Valutazione |
|---------|----------------|-------------|
| **Allowed Origins** | Env-configured + primary domain | ✅ Whitelist-based |
| **Dev Mode** | localhost:* via regex | ⚠️ Assicurarsi che non sia in produzione |
| **Credentials** | `Access-Control-Allow-Credentials: true` | ✅ Required for cookies |
| **Preflight Cache** | `Max-Age: 600` (10 min) | ✅ Performance |
| **Error Wrapping** | CORS headers anche su errori | ✅ No CORS-masked errors |

---

## 11. Vulnerability Assessment

### 11.1 OWASP Top 10 Mapping

| # | Vulnerabilità | Status | Note |
|---|--------------|--------|------|
| A01 | Broken Access Control | ✅ Mitigato | RBAC + middleware chain |
| A02 | Cryptographic Failures | ✅ Mitigato | Argon2id, SHA-256, AES |
| A03 | Injection | ✅ Mitigato | PDO prepared statements |
| A04 | Insecure Design | ⚠️ Parziale | Manca threat model formale |
| A05 | Security Misconfiguration | ⚠️ Parziale | CSP `unsafe-inline`, shared hosting |
| A06 | Vulnerable Components | ⚠️ Da verificare | Serve dependency audit regolare |
| A07 | Auth Failures | ✅ Mitigato | Lockout, 2FA, rate limiting |
| A08 | Software/Data Integrity | ⚠️ Parziale | Manca SRI, CSP nonce |
| A09 | Security Logging | ✅ Buono | audit_log, accounting_audit_log, login_history |
| A10 | Server-Side Request Forgery | ⚠️ Da verificare | Webhook URLs, Open Banking callbacks |

### 11.2 Critical Findings

| ID | Severità | Finding | Raccomandazione |
|----|----------|---------|----------------|
| SEC-01 | 🔴 Alta | Shared hosting (Hostinger) — no WAF, no isolation | Migrare a VPS/cloud con WAF |
| SEC-02 | 🟡 Media | `SameSite=None` su CSRF cookie | Valutare `SameSite=Lax` con CORS adeguato |
| SEC-03 | 🟡 Media | `unsafe-inline` in CSP (site preview) | Implementare nonce-based CSP |
| SEC-04 | 🟡 Media | No database encryption at rest | Abilitare InnoDB tablespace encryption |
| SEC-05 | 🟡 Media | AI interactions stored in plaintext | Considerare encryption per dati sensibili in conversazioni AI |
| SEC-06 | 🟢 Bassa | File uploads non scansionati (antivirus) | Integrare ClamAV o servizio cloud |
| SEC-07 | 🟢 Bassa | Nessun dependency security scanning automatico | Integrare `composer audit` + `npm audit` in CI |
| SEC-08 | 🟢 Bassa | PII in auth logs (email) | Pseudonimizzazione email in log storici |

---

## 12. Security Monitoring

### Current

| Strumento | Coverage | Note |
|-----------|---------|------|
| **Sentry** | Error tracking (PHP + React) | ✅ |
| **Telegram Alerts** | Rate limit violations | ✅ |
| **audit_log table** | General actions | ✅ |
| **accounting_audit_log** | Financial operations | ✅ |
| **login_history** | Auth events con geo | ✅ |
| **ai_interactions** | AI usage con costi | ✅ |

### Missing

| Strumento | Importanza | Note |
|-----------|-----------|------|
| WAF (Web Application Firewall) | 🔴 Critico | Nessun WAF attualmente |
| SIEM (Security Information & Event Management) | 🟡 Media | No aggregated security monitoring |
| Intrusion Detection System | 🟡 Media | Solo basic rate limiting |
| Automated vulnerability scanning | 🟡 Media | Nessun tool automatico |
| Penetration testing | 🔴 Critico | Mai eseguito |

---

## 13. Raccomandazioni Prioritizzate

### Fase 1 — Pre-Launch (Settimane 1-4)
1. ✅ Penetration test (almeno automated OWASP ZAP scan)
2. ✅ `composer audit` + `npm audit` → zero critical vulnerabilities
3. ✅ Rimuovere CORS localhost in produzione
4. ✅ Verificare 2FA enforcement per admin accounts

### Fase 2 — Post-Launch (Mesi 1-3)
5. Migrare a hosting con WAF (Cloudflare, AWS WAF)
6. Implementare nonce-based CSP
7. Aggiungere file upload scanning
8. Database encryption at rest

### Fase 3 — Scale (Mesi 3-12)
9. SIEM integration (es. ELK Stack / Datadog)
10. Bug bounty program
11. SOC 2 Type II preparation
12. ISO 27001 gap analysis

---

*Documento generato il 2026-03-07. Analisi basata su code review di 21 middleware (3,370 LOC), security services, e migration files.*
