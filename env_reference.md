# Environment Variables Reference

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Appendice

---

## 1. Overview

Kacinka uses `.env` files for configuration in three locations:

| File | Variables | Scope |
|------|----------|-------|
| `auth/.env` | ~75+ | Backend API — PHP (Slim 4) |
| `app/.env` | ~7 | Cloud frontend — Vite (React) |
| `admin/.env` | ~4 | Admin frontend — Vite (React) |

Admin endpoint `GET /admin/env-status` validates 26 critical variables via regex without exposing values.

---

## 2. Backend Environment Variables (`auth/.env`)

### 2.1 Application Core

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `APP_ENV` | critical | `production` | — | `development` \| `production` |
| `APP_DEBUG` | critical | `false` | — | Enable debug mode (never in production) |
| `APP_NAME` | info | `kacinka-auth` | — | Application identifier |
| `APP_URL` | critical | — | — | Backend API base URL |
| `APP_FRONTEND_URL` | critical | — | — | Cloud app frontend URL |

### 2.2 Database

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `DB_HOST` | critical | `localhost` | `/^.+$/` | MariaDB host |
| `DB_PORT` | critical | `3306` | — | MariaDB port |
| `DB_NAME` | critical | — | `/^.+$/` | Database name |
| `DB_USER` | critical | — | — | Database username |
| `DB_PASS` | critical | — | — | Database password |
| `DB_CHARSET` | info | `utf8mb4` | — | Character set |

### 2.3 Authentication (JWT)

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `JWT_SECRET` | critical | — | `/^.{32,}$/` | ≥32 chars. Generate: `php -r "echo base64_encode(random_bytes(64));"` |
| `JWT_ALGO` | info | `HS256` | — | JWT signing algorithm |
| `JWT_ACCESS_TTL` | info | `1800` | — | Access token TTL (seconds, 30 min) |
| `JWT_REFRESH_TTL` | info | `604800` | — | Refresh token TTL (7 days) |
| `JWT_REMEMBER_TTL` | info | `2592000` | — | Remember-me TTL (30 days) |

### 2.4 Encryption Keys

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `SMTP_ENCRYPTION_KEY` | critical | — | — | SMTP credential encryption. `openssl rand -base64 32` |
| `TOTP_ENCRYPTION_KEY` | critical | — | — | TOTP secret encryption. `openssl rand -base64 32` |
| `APP_KEY` | critical | — | — | Application-level encryption. `openssl rand -base64 32` |

### 2.5 CORS & Cookies

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `CORS_ORIGINS` | important | — | — | Comma-separated allowed origins |
| `CORS_ALLOW_CREDENTIALS` | important | `true` | — | Allow credentials in CORS |
| `CORS_ORIGIN` | important | — | — | Primary CORS origin |
| `CORS_CREDENTIALS` | info | `true` | — | Alias for CORS_ALLOW_CREDENTIALS |
| `COOKIE_DOMAIN` | important | — | validated | Cookie domain (e.g., `.kacinka.app`) |
| `COOKIE_SECURE` | important | `true` | — | HTTPS-only cookies |
| `COOKIE_SAMESITE` | important | `None` | — | SameSite policy |

### 2.6 Mail (SMTP)

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `MAIL_HOST` | critical | — | `/^.+$/` | SMTP server hostname |
| `MAIL_PORT` | critical | — | `/^(465\|587\|25)$/` | SMTP port |
| `MAIL_USERNAME` | critical | — | `/^.+@.+$/` | SMTP username (email format) |
| `MAIL_PASSWORD` | critical | — | `/^.{4,}$/` | SMTP password |
| `MAIL_FROM_ADDRESS` | important | — | — | Default sender email |
| `MAIL_FROM_NAME` | info | `Kacinka` | — | Default sender name |
| `SUPPORT_EMAIL` | info | — | — | Support contact email |
| `INFO_EMAIL` | info | — | — | Info contact email |
| `PLATFORM_EMAIL_FROM` | important | — | — | Self-billing email sender |

### 2.7 Stripe

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `STRIPE_SECRET_KEY` | critical | — | `/^sk_(test\|live)_.+$/` | Stripe secret key (sk_test_ or sk_live_) |
| `STRIPE_WEBHOOK_SECRET` | critical | — | `/^whsec_.+$/` | Stripe webhook signing secret |

### 2.8 Platform Billing (FatturaPA)

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `KACINKA_COMPANY_NAME` | important | — | — | Legal company name |
| `KACINKA_VAT_NUMBER` | critical | — | `/^\d{11}$/` | Italian VAT (P.IVA), exactly 11 digits |
| `KACINKA_FISCAL_CODE` | important | — | — | Italian fiscal code |
| `KACINKA_ADDRESS` | important | — | — | Registered address |
| `KACINKA_CITY` | important | — | — | City |
| `KACINKA_ZIP` | important | — | — | ZIP/CAP code |
| `KACINKA_PROVINCE` | important | — | — | Province code (2-letter) |
| `KACINKA_COUNTRY` | important | `IT` | — | ISO country code |
| `KACINKA_SDI_CODE` | important | — | `/^[A-Z0-9]{7}$/` | SDI recipient code (7 alphanumeric) |
| `KACINKA_PEC` | important | — | — | PEC certified email |
| `KACINKA_TAX_REGIME` | important | `RF01` | — | Italian tax regime code |
| `KACINKA_IBAN` | important | — | — | Company IBAN for payments |

### 2.9 Google OAuth

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `GOOGLE_CLIENT_ID` | important | — | `/^.+\.apps\.googleusercontent\.com$/` | Google OAuth client ID |
| `GOOGLE_CLIENT_SECRET` | important | — | — | Google OAuth client secret |
| `GOOGLE_REDIRECT_URI` | important | — | `/^https:\/\/.+$/` | Must be HTTPS (warning if localhost in production) |

### 2.10 Apple OAuth (Reserved)

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `APPLE_CLIENT_ID` | optional | — | — | Apple Sign-In (future) |
| `APPLE_CLIENT_SECRET` | optional | — | — | Apple Sign-In (future) |
| `APPLE_REDIRECT_URI` | optional | — | — | Apple Sign-In (future) |

### 2.11 Sentry (Error Tracking)

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `SENTRY_DSN` | important | — | `/^https:\/\/.+sentry.+$/` | Sentry ingest DSN |
| `SENTRY_ENVIRONMENT` | info | `development` | — | Environment tag |
| `SENTRY_TRACES_SAMPLE_RATE` | info | `0.2` | — | Performance sample rate (0.0-1.0) |

### 2.12 Maintenance

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `MAINTENANCE_MODE` | important | `false` | — | Enable maintenance page |
| `MAINTENANCE_SECRET` | important | — | — | Bypass key for admin access during maintenance |

### 2.13 Logging

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `LOG_LEVEL` | info | `warning` | — | Monolog level: `debug\|info\|notice\|warning\|error` |

### 2.14 AI — Google Gemini

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `GEMINI_API_KEY` | important | — | `/^AIza.+$/` | Google AI Studio API key |
| `GEMINI_MODEL` | info | `gemini-2.0-flash-lite` | — | Default Gemini model |

### 2.15 AI — OpenAI Fallback

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `OPENAI_API_KEY` | optional | — | — | GPT-4o-mini fallback when Gemini fails (429/error) |

### 2.16 Open Banking

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `OPEN_BANKING_PROVIDER` | optional | — | `/^(tink\|truelayer)$/` | Active provider |
| `OPEN_BANKING_API_URL` | optional | — | — | Provider API base URL |
| `OPEN_BANKING_CLIENT_ID` | optional | — | — | OAuth client ID |
| `OPEN_BANKING_CLIENT_SECRET` | optional | — | — | OAuth client secret |
| `TINK_CLIENT_ID` | optional | — | `/^[a-f0-9]{32}$/` | Tink-specific client ID |

### 2.17 OCR

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `OCR_PROVIDER` | important | — | `/^(gemini\|mindee\|veryfi)$/` | Active OCR provider |
| `OCR_API_KEY` | important | — | — | OCR API key |
| `OCR_API_URL` | optional | — | — | Custom OCR endpoint |

### 2.18 Backup

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `BACKUP_RETENTION_DAYS` | info | `30` | — | Daily backup retention |
| `B2_KEY_ID` | important | — | `/^.+$/` | Backblaze B2 application key ID |
| `B2_APPLICATION_KEY` | important | — | — | Backblaze B2 application key |
| `B2_BUCKET_NAME` | important | — | — | B2 bucket for general backups |
| `B2_BUCKET_ID` | important | — | — | B2 bucket ID |
| `B2_INVOICE_BUCKET_NAME` | important | — | — | B2 bucket for invoice archive (10y retention) |
| `B2_INVOICE_BUCKET_ID` | important | — | — | B2 invoice bucket ID |
| `INVOICE_PURGE_DRY_RUN` | info | `false` | — | Preview deletions without acting |

### 2.19 Telegram

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `TELEGRAM_BOT_TOKEN` | important | — | `/^\d+:[A-Za-z0-9_-]+$/` | Bot API token (from @BotFather) |
| `TELEGRAM_CHAT_ID` | important | — | `/^-?\d+$/` | Default alert chat (can be negative for groups) |
| `TELEGRAM_ADMIN_CHAT_ID` | optional | — | — | Admin-only alerts (falls back to CHAT_ID) |

### 2.20 SMS

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `SMS_PROVIDER` | optional | `vonage` | — | SMS gateway provider |
| `SMS_API_KEY` | optional | — | — | SMS provider API key |
| `SMS_API_SECRET` | optional | — | — | SMS provider API secret |
| `SMS_FROM_NUMBER` | optional | `Kacinka` | — | Sender alias |

### 2.21 OpenAPI.it Platform

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `OPENAPI_BEARER_TOKEN` | important | — | `/^[a-f0-9]+$/` | Shared bearer token for all OpenAPI.it services |
| `OPENAPI_SDI_ENABLED` | optional | `false` | `/^(true\|false)$/` | Enable SDI e-invoicing |
| `OPENAPI_SDI_SANDBOX` | optional | `true` | — | Use sandbox endpoint |
| `OPENAPI_CALLBACK_SECRET` | important | — | — | Webhook verification secret |
| `OPENAPI_COMPANY_ENABLED` | optional | `false` | — | Enable company search API |
| `OPENAPI_COMPANY_SANDBOX` | optional | `true` | — | Use sandbox endpoint |
| `OPENAPI_ESIGN_ENABLED` | optional | `false` | — | Enable e-signature API |
| `OPENAPI_ESIGN_SANDBOX` | optional | `true` | — | Use sandbox endpoint |
| `OPENAPI_ESIGN_CERT_USERNAME` | optional | — | — | Namirial device name |
| `OPENAPI_ESIGN_CERT_PASSWORD` | optional | — | — | Namirial device PIN |
| `OPENAPI_SMS_ENABLED` | optional | `false` | — | Enable OpenAPI.it SMS |
| `OPENAPI_SMS_SANDBOX` | optional | `true` | — | Use sandbox endpoint |
| `OPENAPI_SMS_SENDER` | optional | `Kacinka` | — | SMS sender alias |

### 2.22 E-Invoice Gateways (International)

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `SDI_GATEWAY` | optional | `openapi` | — | `openapi\|aruba\|infocert\|sdi_direct\|custom` |
| `SDI_ENDPOINT` | optional | — | — | Custom SDI endpoint |
| `SDI_API_KEY` | optional | — | — | Custom SDI API key |
| `CHORUS_PRO_ENDPOINT` | optional | — | — | French B2G Chorus Pro URL |
| `CHORUS_PRO_CLIENT_ID` | optional | — | — | Chorus Pro OAuth client ID |
| `CHORUS_PRO_CLIENT_SECRET` | optional | — | — | Chorus Pro OAuth secret |
| `PEPPOL_AP_ENDPOINT` | optional | — | — | PEPPOL Access Point URL |
| `PEPPOL_AP_CERT` | optional | — | — | Path to PEPPOL client certificate |
| `PEPPOL_AP_KEY` | optional | — | — | Path to PEPPOL private key |

### 2.23 Infrastructure

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `CLOUDFLARE_ZONE_ID` | optional | — | — | Cloudflare zone for cache purge |
| `CLOUDFLARE_API_TOKEN` | optional | — | — | Cloudflare API token |
| `WKHTMLTOPDF_PATH` | optional | — | — | wkhtmltopdf binary path (fallback: DomPDF) |

### 2.24 Pusher (Real-Time)

| Variable | Level | Default | Regex | Description |
|----------|-------|---------|-------|-------------|
| `PUSHER_APP_ID` | optional | — | `/^\d+$/` | Pusher app ID |
| `PUSHER_KEY` | optional | — | `/^[a-f0-9]+$/` | Pusher API key |
| `PUSHER_SECRET` | optional | — | — | Pusher API secret |
| `PUSHER_CLUSTER` | optional | `eu` | — | Pusher cluster region |

---

## 3. Frontend Environment Variables

### 3.1 Cloud App (`app/.env`)

| Variable | Prefix | Description |
|----------|--------|-------------|
| `VITE_API_URL` | VITE_ | Backend API base URL |
| `VITE_APP_NAME` | VITE_ | Application display name |
| `VITE_APP_ENV` | VITE_ | Environment identifier |
| `VITE_SENTRY_DSN` | VITE_ | Sentry ingest DSN for React |
| `VITE_PUSHER_KEY` | VITE_ | Pusher public key (empty = HTTP polling fallback) |
| `VITE_PUSHER_CLUSTER` | VITE_ | Pusher cluster (default: `eu`) |
| `VITE_TURNSTILE_SITE_KEY` | VITE_ | Cloudflare Turnstile site key (empty = CAPTCHA disabled) |

### 3.2 Admin Panel (`admin/.env`)

| Variable | Prefix | Description |
|----------|--------|-------------|
| `VITE_API_URL` | VITE_ | Backend API base URL |
| `VITE_APP_NAME` | VITE_ | Admin app name |
| `VITE_APP_ENV` | VITE_ | Environment identifier |
| `VITE_SENTRY_DSN` | VITE_ | Sentry DSN |

> **Note**: All `VITE_` prefixed variables are exposed to the browser bundle at build time. Never store secrets here.

---

## 4. EnvStatusAction Validation

The admin endpoint `GET /admin/env-status` validates 26 variables at runtime:

### Validation Levels

| Level | Count | Impact |
|-------|-------|--------|
| `critical` | 10 | App won't function without these |
| `important` | 11 | Features degraded without these |
| `optional` | 5 | Nice-to-have, graceful fallback |

### Security Warnings (Production)

| Warning | Trigger |
|---------|---------|
| JWT development placeholder | `JWT_SECRET` contains `dev_secret` in production |
| OAuth still localhost | `GOOGLE_REDIRECT_URI` contains `localhost` in production |

### Response Format

```json
{
  "success": true,
  "environment": "production",
  "total_keys": 26,
  "summary": { "ok": 20, "missing": 4, "invalid": 2 },
  "keys": {
    "DB_HOST": { "status": "ok", "level": "critical" },
    "STRIPE_SECRET_KEY": { "status": "missing", "level": "critical" }
  },
  "warnings": [],
  "timestamp": "2026-03-07T10:00:00+01:00"
}
```

---

## 5. Variable Count Summary

| Category | Count |
|----------|-------|
| Application Core | 5 |
| Database | 6 |
| Authentication (JWT) | 5 |
| Encryption Keys | 3 |
| CORS & Cookies | 7 |
| Mail (SMTP) | 9 |
| Stripe | 2 |
| Platform Billing | 12 |
| Google OAuth | 3 |
| Apple OAuth (reserved) | 3 |
| Sentry | 3 |
| Maintenance | 2 |
| Logging | 1 |
| AI (Gemini + OpenAI) | 3 |
| Open Banking | 5 |
| OCR | 3 |
| Backup (B2) | 8 |
| Telegram | 3 |
| SMS | 4 |
| OpenAPI.it Platform | 13 |
| E-Invoice Gateways | 9 |
| Infrastructure | 3 |
| Pusher | 4 |
| **Backend Total** | **~116** |
| Frontend (app) | 7 |
| Frontend (admin) | 4 |
| **Grand Total** | **~127** |

---

## 6. Setup Checklist

### Minimum Viable Launch (critical + important)

```bash
# 1. Copy template
cp auth/.env.example auth/.env

# 2. Generate secrets
php -r "echo 'JWT_SECRET=' . base64_encode(random_bytes(64)) . PHP_EOL;"
openssl rand -base64 32  # → SMTP_ENCRYPTION_KEY
openssl rand -base64 32  # → TOTP_ENCRYPTION_KEY
openssl rand -base64 32  # → APP_KEY

# 3. Fill critical database vars
# DB_HOST, DB_NAME, DB_USER, DB_PASS

# 4. Configure mail
# MAIL_HOST, MAIL_PORT, MAIL_USERNAME, MAIL_PASSWORD

# 5. Add Stripe keys (test mode initially)
# STRIPE_SECRET_KEY=sk_test_...
# STRIPE_WEBHOOK_SECRET=whsec_...

# 6. Set VAT number
# KACINKA_VAT_NUMBER=03070310994

# 7. Frontend .env
echo "VITE_API_URL=https://api.kacinka.app" > app/.env
echo "VITE_APP_NAME=Kacinka" >> app/.env
```

### Graceful Degradation
Services that work without their env vars (polling fallback, disabled feature, etc.):
- **Pusher** → HTTP polling
- **Telegram** → No alerts (errors logged)
- **OCR** → Feature disabled
- **OpenAI** → Gemini-only (no fallback)
- **OpenAPI.it** → E-invoicing disabled
- **Cloudflare** → No cache purge
- **Apple OAuth** → Not shown in login
- **SMS** → Phone verification disabled
- **Chorus Pro / PEPPOL** → Country gateways disabled

---

*Documento generato il 2026-03-07. Fonte: auth/.env.example (227 righe), app/.env.example (12 righe), EnvStatusAction.php (105 righe).*
