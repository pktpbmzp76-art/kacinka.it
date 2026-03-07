# Data Security

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Overview

Questo documento analizza le misure di sicurezza dei dati implementate nella piattaforma Kacinka, coprendo crittografia, controllo accessi, protezione dei dati in transito e a riposo, e gestione delle credenziali.

---

## 2. Encryption

### 2.1 Data In Transit

| Protocollo | Status | Dettaglio |
|-----------|--------|----------|
| **TLS 1.2+** | ✅ | Tutte le comunicazioni HTTPS |
| **HSTS** | ✅ | `max-age=31536000; includeSubDomains; preload` |
| **Certificate** | ✅ | Let's Encrypt (auto-renewal) |
| **API ↔ External Services** | ✅ | HTTPS per Stripe, Gemini, Tink, Pusher, Sentry |
| **Database Connection** | ⚠️ | Localhost (no TLS needed), ma in cloud servirà TLS |

### 2.2 Data At Rest

| Tipo Dato | Crittografia | Metodo |
|-----------|-------------|--------|
| **Passwords** | ✅ Hash irreversibile | Argon2id (PHP password_hash) |
| **API Keys** | ✅ Hash | SHA-256 (key_hash in DB) |
| **Session Tokens** | ✅ Hash | SHA-256 (token_hash in DB) |
| **SMTP Credentials** | ✅ Encrypted | AES (smtp_pass_encrypted) |
| **OAuth Tokens** | ✅ Encrypted | Column-level encryption |
| **JWT Tokens** | ✅ Signed | HS256/RS256 (firebase/php-jwt) |
| **Database** | ❌ Non encrypted | Hostinger default (no InnoDB encryption) |
| **File Uploads** | ❌ Non encrypted | Filesystem plaintext |
| **Backups** | ⚠️ | Hostinger managed (encryption status unknown) |
| **AI Conversations** | ❌ Plaintext | ai_interactions table |

### 2.3 Key Management

| Chiave | Storage | Rotazione |
|--------|---------|-----------|
| JWT Secret | `.env` file | ❌ Nessuna rotazione automatica |
| Stripe Secret Key | `.env` file | Via Stripe dashboard |
| Gemini API Key | `.env` file | Via Google Cloud |
| OpenAI API Key | `.env` file | Via OpenAI dashboard |
| Tink Credentials | `.env` file | Via Tink dashboard |
| SMTP Password Encryption Key | `.env` or derived | ❌ Nessuna rotazione |

---

## 3. Access Control

### 3.1 Authentication Layers

```
Layer 1: Network (HTTPS, CORS, Rate Limiting)
Layer 2: Session (JWT token + HttpOnly cookie)
Layer 3: CSRF (Double-submit cookie)
Layer 4: RBAC (Role-based permissions)
Layer 5: Plan Gating (Module + limit enforcement)
Layer 6: Resource Ownership (project_id filtering)
```

### 3.2 Role Hierarchy

| Role | Access Level | Capabilities |
|------|-------------|-------------|
| **owner** | Full | All actions + account deletion + billing |
| **admin** | Near-full | All project actions, member management |
| **member** | Write | CRUD on assigned resources |
| **viewer** | Read-only | View only, no modifications |
| **ai_bot** | Restricted | AI-specific actions only |

### 3.3 Multi-Tenancy Isolation

| Mechanism | Implementation |
|-----------|---------------|
| Project scoping | `WHERE project_id = ?` su tutte le query |
| Middleware enforcement | PlanLimitsMiddleware verifica ownership |
| Row-level security | Nessun accesso cross-project possibile |
| Admin impersonation | Tracked via `impersonation_sessions` table |

---

## 4. Credential Security

### 4.1 Password Policy

| Aspetto | Implementazione |
|---------|----------------|
| **Hash Algorithm** | Argon2id (memory-hard, GPU-resistant) |
| **Minimum Length** | Validazione lato frontend |
| **Complexity Rules** | ⚠️ Da verificare enforcement backend |
| **Breach Check** | ❌ Non integrato (es. HaveIBeenPwned API) |
| **History** | ❌ Nessun password history check |

### 4.2 API Key Security

| Aspetto | Implementazione |
|---------|----------------|
| **Format** | `kk_` prefix + random string |
| **Storage** | Hash-only (SHA-256, key never stored) |
| **Scopes** | Per-key permission scoping |
| **Expiration** | ⚠️ No expiration (permanent until revoked) |
| **Last Used** | ✅ Tracked in api_key_usage |

### 4.3 2FA

| Aspetto | Implementazione |
|---------|----------------|
| **Method** | TOTP (Time-based One-Time Password) |
| **Recovery Codes** | ✅ Stored hashed |
| **Enforcement** | ⚠️ Optional (non obbligatorio per admin) |
| **SMS Fallback** | ❌ Non implementato |

---

## 5. Network Security

### 5.1 Current

| Misura | Status | Note |
|--------|--------|------|
| **HTTPS** | ✅ | Let's Encrypt, HSTS preload |
| **CORS** | ✅ | Whitelist-based origins |
| **CSP** | ✅ | Dual profile (API strict, preview relaxed) |
| **Rate Limiting** | ✅ | Per-endpoint + plan multipliers |
| **WAF** | ❌ | Nessun Web Application Firewall |
| **DDoS Protection** | ❌ | Nessuna protezione dedicata |
| **IP Whitelisting** | ❌ | Non supportato |
| **VPN** | ❌ | Nessun accesso VPN per admin |

### 5.2 Headers Sicurezza

| Header | Valore | Status |
|--------|--------|--------|
| `X-Content-Type-Options` | `nosniff` | ✅ |
| `X-Frame-Options` | `DENY` | ✅ |
| `Referrer-Policy` | `strict-origin-when-cross-origin` | ✅ |
| `Permissions-Policy` | camera=(), microphone=(), geolocation=() | ✅ |
| `Strict-Transport-Security` | preload, 1 year | ✅ |
| `X-Robots-Tag` | `noindex, nofollow` | ✅ (API) |

---

## 6. Monitoring & Detection

### 6.1 Security Logging

| Log | Table | Retention |
|-----|-------|-----------|
| Login attempts | `login_attempts` | ⚠️ Indefinita |
| Login history | `login_history` | ⚠️ Indefinita |
| General audit | `audit_log` | ⚠️ Indefinita |
| Accounting audit | `accounting_audit_log` | 10 years (legal) |
| AI interactions | `ai_interactions` | ⚠️ Indefinita |
| API key usage | `api_key_usage` | ⚠️ Indefinita |
| Webhook deliveries | `webhook_deliveries` | ⚠️ Indefinita |

### 6.2 Alerting

| Tipo | Canale | Trigger |
|------|--------|---------|
| Rate limit violation | Telegram | > 2× limit per key |
| Sentry error | Email/Sentry dashboard | Unhandled exceptions |
| AI escalation | Telegram + Email | Human escalation triggered |

### 6.3 Missing Detection

| Capability | Status |
|-----------|--------|
| Intrusion Detection (IDS) | ❌ |
| Anomaly detection (unusual login patterns) | ❌ |
| File integrity monitoring | ❌ |
| Automated vulnerability scanning | ❌ |
| Penetration testing schedule | ❌ |

---

## 7. Data Classification

| Classificazione | Dati | Protezione Richiesta |
|----------------|------|---------------------|
| **Critico** | Passwords, API keys, OAuth tokens | Encrypted hash, never logged |
| **Sensibile** | Email, telefono, P.IVA, codice fiscale, dati bancari | Encrypted at rest (partial), access-controlled |
| **Interno** | Fatture, contatti, deal, task, chat | Project-scoped, audit logged |
| **Pubblico** | Template siti, prodotti catalogo | Nessuna restrizione |

---

## 8. Incident Response

### 8.1 Current Capability

| Aspetto | Status |
|---------|--------|
| Incident response plan | ❌ Non documentato |
| Communication templates | ❌ |
| Escalation contacts | ⚠️ Solo admin Telegram |
| Forensic capability | ❌ |
| Post-incident review process | ❌ |

### 8.2 Recommended IR Plan

1. **Detection** (< 1h): Sentry alert + monitoring anomaly
2. **Triage** (< 2h): Classify severity (P1-P4)
3. **Containment** (< 4h): Isolate affected systems
4. **Notification** (< 72h): GDPR data breach notification if PII involved
5. **Remediation**: Fix root cause, patch, deploy
6. **Post-mortem**: Document lessons learned, update procedures

---

## 9. Raccomandazioni

### Immediate (Pre-Launch)
1. Setup Cloudflare WAF (free tier includes basic WAF)
2. Implement API key expiration (optional configurable)
3. Enforce 2FA for admin/owner roles
4. Define data retention policy per table
5. Create incident response runbook

### Short-term (M1-M3)
6. Database encryption at rest (InnoDB tablespace encryption post-migration to VPS)
7. File upload encryption (server-side AES before storage)
8. Password breach check integration (HaveIBeenPwned API)
9. Automated vulnerability scanning (OWASP ZAP monthly)

### Medium-term (M3-M12)
10. SOC 2 Type I preparation
11. Secret management (HashiCorp Vault or AWS Secrets Manager)
12. Key rotation automation
13. Penetration test by external firm

---

*Documento generato il 2026-03-07. Analisi basata su security middleware (3,370 LOC), SessionService, GdprAction, auth configuration files, e database schema.*
