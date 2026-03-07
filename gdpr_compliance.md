# GDPR Compliance

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Executive Summary

| Area | Score | Livello |
|------|-------|---------|
| **Lawful Basis** | 7.5/10 | ✅ Buono |
| **Consent Management** | 8.0/10 | ✅ Forte |
| **Data Subject Rights** | 7.5/10 | ✅ Buono |
| **Data Minimization** | 6.5/10 | ⚠️ Sufficiente |
| **Security Measures** | 7.5/10 | ✅ Buono |
| **Documentation** | 6.0/10 | ⚠️ Da migliorare |
| **Third-Party Compliance** | 6.0/10 | ⚠️ Da migliorare |
| **OVERALL** | **7.0/10** | **⚠️ Buono** |

---

## 2. Data Processing Inventory

### 2.1 Personal Data Categories

| Categoria | Dati Raccolti | Base Legale | Retention |
|-----------|--------------|-------------|-----------|
| **Identificativi** | Nome, email, telefono, avatar | Contratto (Art. 6.1.b) | Durata account |
| **Fiscali** | P.IVA, codice fiscale, ragione sociale | Obbligo legale (Art. 6.1.c) | 10 anni (obbligo fiscale IT) |
| **Finanziari** | Fatture, pagamenti, conti bancari | Contratto + Obbligo legale | 10 anni |
| **Comunicazioni** | Chat messages, email campaigns | Contratto (Art. 6.1.b) | Durata account |
| **Comportamentali** | Activity log, login history, AI interactions | Legittimo interesse (Art. 6.1.f) | Definire retention |
| **Tecnici** | IP address, User-Agent, session data | Legittimo interesse (Art. 6.1.f) | 30-90 giorni |
| **Marketing** | Email preferences, campaign opens | Consenso (Art. 6.1.a) | Fino a revoca |

### 2.2 Data Flow Map

```
[Utente] → [cloud.kacinka.app] → [api.kacinka.app]
                                       │
                ┌──────────────────────┼──────────────────────┐
                ▼                      ▼                      ▼
         [MariaDB]              [Google Gemini]         [Stripe]
         (dati primari)         (AI messaggi)           (pagamenti)
                                       │                      │
                                 [Elaborato]            [PCI DSS Level 1]
                                 [Non stored]           [Nessun dato carta]
                                                              │
                                                        [Tink/Open Banking]
                                                        (dati bancari)
```

---

## 3. Consent Management (IMPLEMENTATO)

### 3.1 Consent Infrastructure

| Componente | File | Status |
|-----------|------|--------|
| **RecordConsentAction** | `auth/src/Action/Legal/RecordConsentAction.php` | ✅ |
| **GetConsentsAction** | `auth/src/Action/Legal/GetConsentsAction.php` | ✅ |
| **user_consents table** | DB table con consent_type, version, accepted, ip, ua | ✅ |
| **legal_consents view** | Aggregated consent view | ✅ |

### 3.2 Consent Types Tracked

| Tipo | Tracciato | Granulare | Revocabile |
|------|----------|-----------|-----------|
| `privacy_policy` | ✅ | Version-tracked | ✅ |
| `terms_of_service` | ✅ | Version-tracked | ✅ |
| `marketing` | ✅ | Separate consent | ✅ |
| `cookie` | ✅ | Separate consent | ✅ |
| `informativa` | ✅ | IT-specific | ✅ |

### 3.3 Consent Metadata

Ogni record di consenso include:
- `user_id` — Identificazione utente
- `consent_type` — Tipo di consenso
- `version` — Versione del documento
- `accepted` — Boolean
- `ip_address` — IP al momento del consenso
- `user_agent` — Browser/device
- `accepted_at` — Timestamp esatto

### 3.4 Quick Flags
```sql
auth_users.accepted_terms_at — Sync con last terms acceptance
auth_users.accepted_privacy_at — Sync con last privacy acceptance
```

---

## 4. Data Subject Rights (Art. 15-22)

### 4.1 Rights Implementation

| Diritto | Articolo | Implementato | Endpoint | Note |
|---------|----------|-------------|----------|------|
| **Accesso** (Access) | Art. 15 | ✅ | `GET /me/data-export` | JSON export completo |
| **Rettifica** (Rectification) | Art. 16 | ✅ | `PUT /auth/profile` | User self-edit |
| **Cancellazione** (Erasure) | Art. 17 | ✅ | `DELETE /me` | Anonymization + soft delete |
| **Portabilità** (Portability) | Art. 20 | ✅ | `GET /me/data-export` | JSON machine-readable |
| **Limitazione** (Restriction) | Art. 18 | ⚠️ Parziale | N/A | Non esplicitamente implementato |
| **Opposizione** (Objection) | Art. 21 | ⚠️ Parziale | Email unsubscribe | Solo marketing |
| **Non-profilazione** (No Profiling) | Art. 22 | ✅ | N/A | Nessuna decisione automatizzata |

### 4.2 Data Export (Art. 15 + 20) — GdprAction.php

**Dati esportati**:
- Profilo utente (nome, email, telefono, P.IVA, codice fiscale, company)
- Progetti (nome, stato, data creazione)
- Contatti (nome, email, telefono, tipo, stato)
- Fatture (numero, stato, totale, data)
- Note (titolo, contenuto, data)
- Activity log (ultime 1,000 entries)
- Audit log (ultime 500 entries)
- Sessioni attive
- Consensi legali
- Timestamp export + label "GDPR Article 20 — Data Portability"

### 4.3 Account Deletion (Art. 17) — GdprAction.php

**Processo**:
1. Password verification richiesta
2. Conferma esplicita: user must type "ELIMINA"
3. Anonymization dei dati personali
4. Soft delete dell'account
5. Audit log dell'operazione

---

## 5. Data Security (Art. 32)

### 5.1 Technical Measures

| Misura | Status | Dettaglio |
|--------|--------|----------|
| **Encryption in transit** | ✅ | HTTPS (TLS 1.2+), HSTS preload |
| **Password hashing** | ✅ | Argon2id (OWASP recommended) |
| **Session security** | ✅ | SHA-256 token hashing, rotation, grace period |
| **CSRF protection** | ✅ | Double-submit cookie, constant-time comparison |
| **Rate limiting** | ✅ | Per-endpoint + plan-based multipliers |
| **Access control** | ✅ | RBAC (5 roles), middleware enforcement |
| **Input validation** | ✅ | Prepared statements, htmlspecialchars, Zod |
| **Encryption at rest** | ⚠️ | SMTP passwords AES-encrypted, ma DB non encrypted |
| **Backup encryption** | ⚠️ | Hostinger backup (not confirmed encrypted) |
| **Audit trail** | ✅ | audit_log + accounting_audit_log + login_history |

### 5.2 Organizational Measures

| Misura | Status | Note |
|--------|--------|------|
| Data Protection Officer (DPO) | ❌ | Non nominato (obbligatorio se data processing su larga scala) |
| Data Processing Register (Art. 30) | ❌ | Non formalizzato |
| Data Protection Impact Assessment (DPIA) | ❌ | Non eseguita |
| Privacy by Design | ⚠️ | Parziale (consent tracking sì, minimization da migliorare) |
| Staff training | N/A | 1 developer |
| Incident response procedure | ❌ | Non documentata |
| Data breach notification (72h) | ❌ | Nessuna procedura formale |

---

## 6. Third-Party Data Processors

### 6.1 Sub-Processor Inventory

| Processor | Tipo Dati | DPA Necessario | DPA Status |
|-----------|----------|----------------|------------|
| **Hostinger** | Tutti i dati (hosting) | ✅ Sì | ⚠️ Da verificare |
| **Stripe** | Dati pagamento | ✅ Sì | ✅ Stripe DPA standard |
| **Google (Gemini)** | AI conversation text | ✅ Sì | ⚠️ Da verificare ToS |
| **OpenAI** | AI fallback text | ✅ Sì | ⚠️ Da verificare DPA |
| **Tink (Visa)** | Dati bancari | ✅ Sì | ⚠️ Da verificare |
| **Pusher** | Chat messages (transit) | ✅ Sì | ⚠️ Da verificare |
| **Sentry** | Error data (may contain PII) | ✅ Sì | ⚠️ Da verificare |
| **Telegram** | Admin alert content | 🟡 Limitato | N/A (solo admin) |

### 6.2 Data Transfer (International)

| Processor | Location | Adeguatezza | Meccanismo |
|-----------|----------|-------------|------------|
| Stripe | USA | ❌ | SCCs (Standard Contractual Clauses) |
| Google | USA/EU | ⚠️ | EU Data Processing Amendment |
| OpenAI | USA | ❌ | SCCs |
| Tink | EU (Svezia) | ✅ | N/A (EEA) |
| Pusher | USA/EU | ⚠️ | SCCs |
| Sentry | USA | ❌ | SCCs |
| Hostinger | EU (Lituania) | ✅ | N/A (EEA) |

---

## 7. Cookie & Tracking

### 7.1 Current Cookies

| Cookie | Tipo | Scopo | Durata | Consenso |
|--------|------|-------|--------|----------|
| `access_token` | HttpOnly, Secure | JWT authentication | 24h | Necessario |
| `csrf_token` | Secure, SameSite=None | CSRF protection | 24h | Necessario |
| `refresh_token` | HttpOnly, Secure | Session refresh | Configurable | Necessario |

### 7.2 Cookie Policy

| Aspetto | Status |
|---------|--------|
| Cookie banner | ⚠️ Present on marketing site (cookies.html) |
| Cookie categories (necessary/analytics/marketing) | ⚠️ Da verificare |
| Cookie opt-out mechanism | ⚠️ Da verificare |
| Third-party cookie blocking | ✅ Nessun cookie di terze parti in app |

---

## 8. Legal Pages

### 8.1 Published Pages

| Pagina | URL | Status |
|--------|-----|--------|
| Privacy Policy | `privacy.html` | ✅ Presente |
| Terms of Service | `terms.html` | ✅ Presente |
| Cookie Policy | `cookies.html` | ✅ Presente |
| 404 | `404.html` + `404.php` | ✅ |

### 8.2 Page Content Compliance

| Requisito | Privacy | Terms | Cookie |
|-----------|---------|-------|--------|
| Data controller info | ⚠️ Da verificare | ⚠️ | — |
| DPO contact | ❌ | — | — |
| Legal basis per processing | ⚠️ | — | — |
| Data subject rights | ⚠️ | — | — |
| Data retention periods | ⚠️ | — | — |
| International transfers | ⚠️ | — | — |
| Sub-processor list | ❌ | — | — |
| Cookie categories | — | — | ⚠️ |

---

## 9. Gap Analysis

### 9.1 Critical Gaps

| # | Gap | GDPR Article | Rischio | Priorità |
|---|-----|-------------|---------|----------|
| 1 | Nessun DPO nominato | Art. 37 | 🔴 Alto | Pre-launch |
| 2 | Nessun DPIA | Art. 35 | 🔴 Alto | Pre-launch |
| 3 | Nessun Data Processing Register (Art. 30) | Art. 30 | 🔴 Alto | Pre-launch |
| 4 | DPA mancanti con sub-processors | Art. 28 | 🔴 Alto | Pre-launch |
| 5 | Nessuna procedura data breach notification | Art. 33-34 | 🔴 Alto | Pre-launch |
| 6 | Right to restriction non implementato | Art. 18 | 🟡 Medio | M1-M3 |
| 7 | Data retention policy non definita | Art. 5.1.e | 🟡 Medio | M1-M3 |
| 8 | AI data minimization | Art. 5.1.c | 🟡 Medio | M1-M3 |

### 9.2 Implementation Strengths

| ✅ Punto di Forza | Note |
|-------------------|------|
| Consent tracking granulare con versioning | 5 consent types, IP/UA recorded |
| Data export completo (Art. 20) | JSON export endpoint funzionante |
| Account deletion con anonymization (Art. 17) | Password verification + confirmation |
| Audit trail esteso | 3 audit tables + login history |
| Nessun cookie di tracking | Solo cookies tecnici necessari |
| Password security state-of-the-art | Argon2id |
| No credit card storage | 100% Stripe-delegated |

---

## 10. Compliance Roadmap

### Pre-Launch
1. Nominare DPO (anche esterno/consulente)
2. Redigere Data Processing Register (Art. 30)
3. Eseguire DPIA (Art. 35) per AI processing
4. Firmare DPA con Hostinger, Google, Tink, Pusher, Sentry, OpenAI
5. Procedura data breach notification (72h workflow)
6. Aggiornare privacy policy con sub-processor list + retention policy

### Post-Launch (M1-M3)
7. Implementare right to restriction (account freeze)
8. Setup automated data retention (purge vecchi log)
9. AI data minimization audit
10. Cookie consent management platform (CMP)

### Scale (M3-M12) 
11. Annual DPIA review
12. Penetration test + GDPR security audit
13. Staff training program (se team cresce)
14. ISO 27701 gap analysis

---

*Documento generato il 2026-03-07. Analisi basata su GdprAction.php, RecordConsentAction.php, GetConsentsAction.php, user_consents table, legal pages (privacy.html, terms.html, cookies.html).*
