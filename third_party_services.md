# Third-Party Services Inventory

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Appendice

---

## 1. Services Overview

Kacinka integrates with **18 external services** across 8 functional categories. All integrate via environment variables, with graceful degradation when optional services are unconfigured.

| Category | Services | Critical |
|----------|----------|----------|
| Payments | Stripe | Yes |
| Authentication | Google OAuth, Apple OAuth (reserved) | Yes (Google) |
| AI / ML | Google Gemini, OpenAI GPT-4o-mini | Yes |
| OCR | Mindee, Veryfi, Gemini Vision | Important |
| E-Invoicing | OpenAPI.it (SDI/Company/eSign/SMS), Chorus Pro, PEPPOL | Important |
| Banking | TrueLayer/Tink (Open Banking) | Optional |
| Infrastructure | Sentry, Backblaze B2, Pusher, Cloudflare | Important |
| Communications | Telegram, SMTP (Hostinger), Vonage SMS | Important |

---

## 2. Service Profiles

### 2.1 Stripe — Payments & Subscriptions

| Attribute | Value |
|-----------|-------|
| **Purpose** | Subscription billing, checkout, webhooks, invoicing |
| **Integration** | `stripe/stripe-php ^19.3` |
| **Env Keys** | `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET` |
| **Validation** | `sk_(test\|live)_.+`, `whsec_.+` |
| **Level** | Critical |
| **Cost** | 1.5% + €0.25/transaction (EU cards) |
| **Backend LOC** | `StripeService.php` (1,420 LOC) |
| **Webhook Events** | `customer.*`, `invoice.*`, `payment_intent.*`, `checkout.session.*`, `subscription.*` |
| **Webhook URL** | `https://api.kacinka.app/webhook/stripe` |
| **Dashboard** | https://dashboard.stripe.com |
| **Migration Note** | API keys are account-level (no change). Webhook signing secret requires new endpoint creation. |

### 2.2 Google OAuth — Social Login

| Attribute | Value |
|-----------|-------|
| **Purpose** | "Sign in with Google" authentication |
| **Integration** | Direct HTTP (OAuth 2.0 authorization code flow) |
| **Env Keys** | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `GOOGLE_REDIRECT_URI` |
| **Validation** | `.+\.apps\.googleusercontent\.com`, `https://...` |
| **Level** | Important |
| **Cost** | Free |
| **Callback URL** | `https://api.kacinka.app/auth/google/callback` |
| **Console** | https://console.cloud.google.com/apis/credentials |
| **Migration Note** | Add redirect URI + JS origins for `kacinka.app` domains. |

### 2.3 Apple OAuth (Reserved)

| Attribute | Value |
|-----------|-------|
| **Purpose** | "Sign in with Apple" (future) |
| **Env Keys** | `APPLE_CLIENT_ID`, `APPLE_CLIENT_SECRET`, `APPLE_REDIRECT_URI` |
| **Level** | Optional |
| **Status** | Not configured, fields reserved for future |

### 2.4 Google Gemini — AI Assistant (Primary)

| Attribute | Value |
|-----------|-------|
| **Purpose** | AI assistant, proactive suggestions, support triage |
| **Integration** | REST API (Gemini API) |
| **Env Keys** | `GEMINI_API_KEY`, `GEMINI_MODEL` |
| **Validation** | `^AIza.+` |
| **Level** | Important |
| **Models** | `gemini-2.0-flash` (10/40 €¢/1M), `flash-lite` (7/28), `2.5-flash` (15/60) |
| **Default Model** | `gemini-2.0-flash-lite` |
| **Backend LOC** | `AiAssistantService.php` (1,523 LOC), `AiContextBuilder.php` (646 LOC), `AiPolicy.php` (227 LOC) |
| **Rate Limits** | Google: 1,500 RPM free tier. Platform: per-plan spending cap (50% plan price) |
| **Cost Estimate** | ~€100/mo at 5,000 users |
| **Console** | https://aistudio.google.com |

### 2.5 OpenAI — AI Fallback

| Attribute | Value |
|-----------|-------|
| **Purpose** | Fallback when Gemini fails (429/error) |
| **Integration** | REST API |
| **Env Keys** | `OPENAI_API_KEY` |
| **Level** | Optional |
| **Models** | `gpt-4o-mini` (14/56 €¢/1M tokens) |
| **Backend LOC** | Integrated in `AiAssistantService.php` |
| **Console** | https://platform.openai.com |

### 2.6 Mindee — OCR (Primary)

| Attribute | Value |
|-----------|-------|
| **Purpose** | Receipt/invoice scanning, data extraction |
| **Integration** | REST API |
| **Env Keys** | `OCR_PROVIDER=mindee`, `OCR_API_KEY`, `OCR_API_URL` |
| **Validation** | `^(gemini\|mindee\|veryfi)$` |
| **Level** | Important |
| **Backend LOC** | `OcrService.php` (814 LOC) — multi-provider with 11 country VAT regex |
| **Cost** | Free tier: 250 pages/mo. Pro: €0.10/page |
| **Console** | https://platform.mindee.com |

### 2.7 Veryfi — OCR (Alternative)

| Attribute | Value |
|-----------|-------|
| **Purpose** | Alternative OCR provider |
| **Integration** | REST API via `OcrService.php` |
| **Env Keys** | `OCR_PROVIDER=veryfi` (reuses `OCR_API_KEY`) |
| **Level** | Optional |
| **Cost** | Usage-based (~$0.08/page) |
| **Console** | https://hub.veryfi.com |

### 2.8 OpenAPI.it — SDI E-Invoicing (Italy)

| Attribute | Value |
|-----------|-------|
| **Purpose** | FatturaPA submission to Agenzia delle Entrate SDI |
| **Integration** | REST API (`invoice.openapi.com`) |
| **Env Keys** | `OPENAPI_BEARER_TOKEN`, `OPENAPI_SDI_ENABLED`, `OPENAPI_SDI_SANDBOX`, `OPENAPI_CALLBACK_SECRET` |
| **Validation** | `^[a-f0-9]+$` (bearer token) |
| **Level** | Important (for Italian market) |
| **Cost** | €0.009/invoice |
| **Rate Limit** | 10,000 req/min |
| **Webhook URL** | `https://api.kacinka.app/webhook/openapi/sdi` |
| **Console** | https://console.openapi.com |
| **Cron** | `cron_platform_sdi.php` (every 5 min) |

### 2.9 OpenAPI.it — Company API

| Attribute | Value |
|-----------|-------|
| **Purpose** | EU VAT validation, company search, PA/SDI lookup |
| **Integration** | REST API (`company.openapi.com`) |
| **Env Keys** | `OPENAPI_COMPANY_ENABLED`, `OPENAPI_COMPANY_SANDBOX` (reuses `OPENAPI_BEARER_TOKEN`) |
| **Level** | Important |
| **Cost** | EU VAT: €0.014/call, IT Start: €0.019, IT Advanced: €0.049 |

### 2.10 OpenAPI.it — eSignature

| Attribute | Value |
|-----------|-------|
| **Purpose** | Qualified Electronic Signatures (QES) via Namirial |
| **Integration** | REST API (`signature.openapi.com`) |
| **Env Keys** | `OPENAPI_ESIGN_ENABLED`, `OPENAPI_ESIGN_SANDBOX`, `OPENAPI_ESIGN_CERT_USERNAME`, `OPENAPI_ESIGN_CERT_PASSWORD` |
| **Level** | Optional |
| **Cost** | QES Auto: €0.013, QES OTP: €0.0065, eSeal: €0.013, SES: €0.09, Timestamp: €0.02 |

### 2.11 OpenAPI.it — SMS

| Attribute | Value |
|-----------|-------|
| **Purpose** | Transactional SMS via OpenAPI.it |
| **Integration** | REST API (`sms.openapi.com`) |
| **Env Keys** | `OPENAPI_SMS_ENABLED`, `OPENAPI_SMS_SANDBOX`, `OPENAPI_SMS_SENDER` |
| **Level** | Optional |
| **Cost** | Italy: €0.036, Spain: €0.027, Worldwide: €0.039 |

### 2.12 Chorus Pro — French E-Invoicing

| Attribute | Value |
|-----------|-------|
| **Purpose** | French B2G e-invoice submission |
| **Env Keys** | `CHORUS_PRO_ENDPOINT`, `CHORUS_PRO_CLIENT_ID`, `CHORUS_PRO_CLIENT_SECRET` |
| **Level** | Optional (when entering French market) |
| **Status** | Not configured — fields reserved |
| **Backend LOC** | `ChorusProBuilder.php` (217 LOC) |

### 2.13 PEPPOL — EU B2B/B2G

| Attribute | Value |
|-----------|-------|
| **Purpose** | Pan-EU e-invoice exchange via Access Point |
| **Env Keys** | `PEPPOL_AP_ENDPOINT`, `PEPPOL_AP_CERT`, `PEPPOL_AP_KEY` |
| **Level** | Optional (EU expansion) |
| **Status** | Not configured — fields reserved |
| **Backend LOC** | `PeppolBIS3Builder.php` (621 LOC) |

### 2.14 TrueLayer / Tink — Open Banking (PSD2)

| Attribute | Value |
|-----------|-------|
| **Purpose** | Bank account linking, transaction import, balance display |
| **Integration** | REST API + OAuth redirect flow |
| **Env Keys** | `OPEN_BANKING_PROVIDER`, `OPEN_BANKING_API_URL`, `OPEN_BANKING_CLIENT_ID`, `OPEN_BANKING_CLIENT_SECRET`, `TINK_CLIENT_ID` |
| **Validation** | `^(tink\|truelayer)$`, `^[a-f0-9]{32}$` (Tink) |
| **Level** | Optional |
| **Backend LOC** | `TinkOpenBankingService.php` (937 LOC), `OpenBankingService.php` (885 LOC) |
| **Cron** | `cron_ob_consent.php` (daily PSD2 consent expiry check) |
| **Console** | https://console.truelayer.com / https://console.tink.com |
| **Migration** | `038_truelayer_migration.sql` — provider swap infrastructure |

### 2.15 Sentry — Error Tracking & Performance

| Attribute | Value |
|-----------|-------|
| **Purpose** | Error tracking, performance monitoring, session replay |
| **Integration** | `sentry/sentry ^4.20` (PHP), `@sentry/react ^10.39` (React) |
| **Env Keys** | `SENTRY_DSN`, `SENTRY_ENVIRONMENT`, `SENTRY_TRACES_SAMPLE_RATE` |
| **Validation** | `^https://.+sentry.+$` |
| **Level** | Important |
| **PHP Config** | 20% traces, 100% profiling, before_send filters /health, user/request/tag context |
| **React Config** | browserTracing, replay (10% normal / 100% error), reactRouterV7, ErrorBoundary |
| **Cost** | Free tier: 5K errors, 10K perf events. Team: $26/mo |
| **Console** | https://sentry.io |
| **Migration Note** | DSN unchanged. Add `*.kacinka.app` to allowed domains. |

### 2.16 Backblaze B2 — Cloud Storage

| Attribute | Value |
|-----------|-------|
| **Purpose** | Database backups (30 daily + 12 monthly retention), e-invoice archive (10y+1d) |
| **Integration** | REST API (B2 native) |
| **Env Keys** | `B2_KEY_ID`, `B2_APPLICATION_KEY`, `B2_BUCKET_NAME`, `B2_BUCKET_ID`, `B2_INVOICE_BUCKET_NAME`, `B2_INVOICE_BUCKET_ID` |
| **Level** | Important |
| **Cost** | $0.005/GB/mo storage, $0.01/GB egress |
| **Buckets** | `kacinka` (backups), `kacinkainvoice` (invoices, 10y+1d lifecycle) |
| **Cron** | `cron_backup.php` (daily 3AM), `cron_invoice_purge.php` (daily, dry run configurable) |
| **Console** | https://secure.backblaze.com |
| **Migration Note** | Keys are account-level. Update CORS rules for new domain. |

### 2.17 Pusher — Real-Time Channels

| Attribute | Value |
|-----------|-------|
| **Purpose** | Real-time chat, typing indicators, live notifications |
| **Integration** | `pusher-js ^8.4` (client), PHP Pusher SDK (server) |
| **Env Keys** | `PUSHER_APP_ID`, `PUSHER_KEY`, `PUSHER_SECRET`, `PUSHER_CLUSTER` |
| **Validation** | `^\d+$` (app ID), `^[a-f0-9]+$` (key) |
| **Level** | Optional (app falls back to HTTP polling if empty) |
| **Cost** | Free: 200K messages/day, 100 concurrent connections |
| **Console** | https://dashboard.pusher.com |
| **Migration Note** | Update CORS origins in Pusher dashboard. |

### 2.18 Telegram — Monitoring & Alerts

| Attribute | Value |
|-----------|-------|
| **Purpose** | Alert delivery, interactive bot commands, health monitoring |
| **Integration** | Telegram Bot API (REST) |
| **Env Keys** | `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`, `TELEGRAM_ADMIN_CHAT_ID` |
| **Validation** | `^\d+:[A-Za-z0-9_-]+$` (token), `^-?\d+$` (chat ID) |
| **Level** | Important |
| **Cost** | Free |
| **Backend LOC** | `TelegramService.php` (508 LOC) |
| **Channels** | 3 (default, admin, fallback) |
| **Alert Methods** | 8: sendErrorAlert (5xx, dual-send), sendHealthAlert, sendBackupAlert, notifyRateLimitExceeded, sendKpiAlert, notifyNewUser, notifyPurchase, notifyPaymentFailed |
| **Bot Commands** | 9: /status, /stats, /errors, /backup, /users, /maintenance, /ban, /plan, /help |
| **Webhook URL** | `https://api.kacinka.app/webhook/telegram` |
| **Migration Note** | Token is bot-level (no change). Update webhook URL if configured. |

### 2.19 SMTP — Email Delivery

| Attribute | Value |
|-----------|-------|
| **Purpose** | Transactional email (verification, password reset, notifications, invoices) |
| **Integration** | `phpmailer/phpmailer ^7.0` |
| **Env Keys** | `MAIL_HOST`, `MAIL_PORT`, `MAIL_USERNAME`, `MAIL_PASSWORD`, `MAIL_FROM_ADDRESS`, `MAIL_FROM_NAME` |
| **Level** | Critical |
| **Provider** | Hostinger SMTP (`smtp.hostinger.com:465`) |
| **Cost** | Included in hosting |
| **Cron** | `cron_email_queue.php` (every 5 min) |
| **DNS Required** | SPF, DKIM, DMARC records for `kacinka.app` |

### 2.20 Vonage — SMS Verification

| Attribute | Value |
|-----------|-------|
| **Purpose** | Phone number verification via SMS OTP |
| **Env Keys** | `SMS_PROVIDER=vonage`, `SMS_API_KEY`, `SMS_API_SECRET`, `SMS_FROM_NUMBER` |
| **Level** | Optional |
| **Cost** | ~€0.06/SMS (EU) |
| **Console** | https://dashboard.nexmo.com |

### 2.21 Cloudflare — CDN & Security

| Attribute | Value |
|-----------|-------|
| **Purpose** | DNS, CDN, cache purge on deploy, DDoS protection |
| **Env Keys** | `CLOUDFLARE_ZONE_ID`, `CLOUDFLARE_API_TOKEN` |
| **Level** | Optional (DNS active, cache purge deferred) |
| **Cost** | Free plan |
| **Console** | https://dash.cloudflare.com |
| **Migration Note** | Requires NEW API token specific to `kacinka.app` zone. |

### 2.22 Cloudflare Turnstile — CAPTCHA

| Attribute | Value |
|-----------|-------|
| **Purpose** | Anti-bot challenge on registration/login forms |
| **Env Keys** | `VITE_TURNSTILE_SITE_KEY` (frontend) |
| **Level** | Optional (empty = CAPTCHA disabled) |
| **Cost** | Free |
| **Console** | https://dash.cloudflare.com/turnstile |

---

## 3. Service Configuration Status

| Status | Count | Services |
|--------|-------|----------|
| ✅ Configured | 7 | Stripe, Google OAuth, Sentry, Gemini, SMTP, OpenAI, Mindee |
| ❌ Empty (need credentials) | 6 | Telegram, B2, Pusher, OpenAPI.it, Vonage, Cloudflare |
| ⏸ Reserved (future) | 5 | Apple OAuth, Chorus Pro, PEPPOL, SDI legacy, eSign |
| 🔄 Sandbox only | 1 | TrueLayer/Tink |

---

## 4. Cost Projection

### Monthly Costs at Scale (5,000 users)

| Service | Free Tier | Estimated Cost |
|---------|-----------|----------------|
| Stripe | — | ~€450/mo (1.5% of ~€30K MRR) |
| Google Gemini | 1,500 RPM | ~€80/mo |
| OpenAI (fallback) | — | ~€20/mo |
| Mindee OCR | 250 pages/mo | ~€30/mo |
| Sentry | 5K errors/mo | €0 (free tier) or €26/mo |
| Backblaze B2 | 10 GB free | ~€5/mo |
| Pusher | 200K msg/day | €0 (free tier) |
| Telegram | Unlimited | €0 |
| SMTP | Included | €0 |
| Cloudflare | Free plan | €0 |
| OpenAPI.it SDI | — | ~€50/mo (at volume) |
| Vonage SMS | — | ~€10/mo |
| **Total** | — | **~€645-671/mo** |

### Gross Margin Impact

At €30K MRR:
- Third-party costs: ~€671/mo → **97.8% gross margin**
- Stripe fees are largest cost (~67% of total third-party)

---

## 5. Redundancy & Fallback Map

| Service | Failure Mode | Fallback |
|---------|-------------|----------|
| Gemini | 429 / API error | → OpenAI GPT-4o-mini automatic failover |
| Mindee OCR | Rate limit / error | → Veryfi or Gemini Vision |
| Pusher | Down / unconfigured | → HTTP polling (zero breaking changes) |
| TrueLayer | API unavailable | → Tink provider swap |
| OpenAPI.it SDI | Down | → Retry queue (`cron_webhook_retry.php`, exponential backoff) |
| Stripe | Webhook failure | → Retry queue with 72h dunning |
| SMTP | Server down | → Email queue (`cron_email_queue.php`, retries every 5 min) |
| Sentry | DSN unreachable | → Local file logging (Monolog) |
| Telegram | Bot unreachable | → Errors logged to file only |
| B2 | API failure | → Local backup retained, retry next cron cycle |

---

## 6. Security Considerations

| Risk | Mitigation |
|------|------------|
| API key exposure | All keys in `.env`, never in git. EnvStatusAction validates without exposing values. |
| Webhook spoofing | Stripe: signature verification (`whsec_`). OpenAPI.it: shared secret. Telegram: chat ID validation. |
| Token rotation | Manual process. Recommend quarterly rotation for high-value keys (Stripe, JWT). |
| Scope minimization | Cloudflare token: Zone > Cache Purge only. B2 key: bucket-restricted. |
| Third-party breach | Password/key hashing where possible. Monitoring via Sentry + Telegram alerts. |

---

## 7. Migration Checklist (Domain Change)

Based on `third_party_migration_guide.md` (447 LOC):

| # | Service | Action | New Keys? |
|---|---------|--------|-----------|
| 1 | Telegram | Copy token + chat IDs, update webhook URL | No |
| 2 | B2 | Copy keys, update bucket CORS rules | No |
| 3 | Pusher | Copy credentials, update CORS origins in dashboard | No |
| 4 | Stripe | Create NEW webhook endpoint → new `whsec_` | Partial |
| 5 | Google OAuth | Add redirect URI + JS origins for `.app` domain | No |
| 6 | Sentry | Add `*.kacinka.app` to allowed domains | No |
| 7 | OpenAPI.it | Copy token, update ALL callback URLs | No |
| 8 | TrueLayer | Update redirect URI + webhook URL | No |
| 9 | Vonage | Copy credentials | No |
| 10 | Cloudflare | Create NEW API token for `kacinka.app` zone | Yes |
| 11 | SMTP DNS | Add SPF + DKIM + DMARC records | N/A |
| 12 | Search Console | Add property + sitemap + address change | N/A |
| 13 | Analytics | Add data stream for `kacinka.app` | N/A |

---

*Documento generato il 2026-03-07. Fonte: auth/.env.example, _migration/third_party_migration_guide.md (447 righe), codebase service files.*
