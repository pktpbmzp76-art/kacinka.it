# Audit Tecnico — Kacinka.it Platform

> **Data**: Marzo 2026  
> **Versione**: 2.0 — Rigenerato da analisi completa del codice sorgente  
> **Scope**: Architettura, sicurezza, performance, pattern, qualità del codice  

---

## 1. Architettura Software

### 1.1 Pattern Architetturale — Backend (Action–Service–Repository)

Il backend segue un'architettura a 3 layer:

```
HTTP Request
  │
  ▼
┌─────────────────────────┐
│ Middleware Pipeline (15) │  ← CorsMiddleware → SecurityHeaders → Auth → CSRF → RateLimit → ...
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│ Action Layer (622 file)  │  ← Thin controller: valida input, chiama service, formatta output
│ 35,552 LOC               │
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│ Service Layer (103 file) │  ← Business logic, DB queries, integrazioni esterne
│ 42,295 LOC               │
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│ Database (MySQL)         │  ← 166 tabelle + 5 viste
│ 84 migrazioni (9,204 LOC)│
└─────────────────────────┘
```

**Metriche strutturali**:
- 79 directory Action (una per dominio/sotto-dominio)
- 622 Action file — una classe per endpoint (Single Responsibility)
- 103 Service file — ciascuno gestisce un dominio business completo
- Ratio Action:Service = 6:1 (ogni service serve in media 6 action)
- LOC medio per Action: 57 LOC (thin — validazione + dispatch)
- LOC medio per Service: 410 LOC (business logic concentrata)

### 1.2 Dependency Injection (PHP-DI 7.1)

**File**: `auth/app/dependencies.php` (909 LOC, 90+ registrazioni)

```php
// Pattern: singleton per infrastruttura
PDO::class => function (ContainerInterface $c) {
    $db = $c->get('settings')['db'];
    $pdo = new PDO($dsn, $user, $pass, [
        PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
        PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
        PDO::ATTR_EMULATE_PREPARES => false,  // Prepared statements REALI
    ]);
    return $pdo;
};

// Pattern: auto-wiring con dipendenze esplicite per servizi complessi
AiAssistantService::class => function ($c) {
    return new AiAssistantService(
        $c->get(PDO::class),
        $c->get(LoggerInterface::class),
        $c->get('settings')['ai'],
        $c->get(AiMemoryService::class),
        $c->get(AiSuggestionService::class)
    );
};
```

**Configurazione chiave**:
- `ATTR_EMULATE_PREPARES = false` → prepared statement MySQL nativi (mitigazione SQL injection)
- `ATTR_ERRMODE = ERRMODE_EXCEPTION` → tutte le query falliscono con eccezione
- `ATTR_DEFAULT_FETCH_MODE = FETCH_ASSOC` → associative array di default
- Charset: `utf8mb4` (supporto emoji/CJK completo)

### 1.3 Pattern Architetturale — Frontend (Module Shell)

```
Browser
  │
  ▼
┌───────────────────────────┐
│ React Router v7 (28 route)│  ← createBrowserRouter (data API)
└──────────┬────────────────┘
           │
           ▼
┌───────────────────────────┐
│ RootLayout                │  ← LiveRegionProvider (accessibility)
│ ├── RequireAuth           │  ← Bootstrap + redirect se non autenticato
│ │   ├── AppShell          │  ← Layout: Sidebar + Header + Content
│ │   │   ├── WorkspaceShell│  ← Dynamic import loader per 26 moduli
│ │   │   │   └── Module    │  ← ModuleErrorBoundary per modulo
│ │   │   └── Dashboard     │
│ │   └── Onboarding        │
│ └── Public (login, etc.)  │
└───────────────────────────┘
```

**Decisioni architetturali documentate nel codice**:

1. **Code splitting disabilitato a livello route**: Un commento in `App.jsx` documenta che `React.lazy` + React 19 `startTransition` causava loop infiniti durante la prima navigazione. Soluzione: import statici per tutte le pagine.

2. **Module-level dynamic import**: `WorkspaceShell` carica i 26 moduli workspace via `useState`-based `import()` custom (non `React.lazy`), evitando il bug React 19.

3. **Navigation monkey-patch**: `router.navigate` è patchato per iniettare `{ flushSync: true }`, forzando commit sincroni ed eliminando race condition `startTransition`.

4. **Three-tier error boundaries**:
   - `GlobalError` — crash root (schermata errore piena)
   - `RouteError` — errore pagina dentro `AppShell` (inline recovery)
   - `ModuleErrorBoundary` — per-modulo `componentDidCatch` con retry

### 1.4 Frontend State Management

**Stack**: Zustand 5 (vanilla, no middleware) + TanStack React Query 5

| Concern | Soluzione | Pattern |
|---------|-----------|---------|
| Auth state | `authStore` (328 LOC) | Singleton Zustand, bootstrap latch, keepalive |
| Server state | TanStack React Query | Fetch + cache + invalidation + optimistic |
| UI/local state | `useState`/`useReducer` | Per-component |
| Domain state | 31 store Zustand tematici | Fetch, CRUD, pagination, filtri |

**authStore pattern dettagliato**:
- Bootstrap latch: `bootstrapStarted` + `bootstrapPromise` anti-double-call in React 18 Strict Mode
- Keepalive: `setInterval` ogni 5 minuti chiama `attemptRefresh()`
- Visibility handler: su focus tab → refresh token + `GET /me` per validare sessione
- Session expiry: evento custom `auth:session-expired` → reset completo stato
- Realtime: `realtimeManager.connect()` su auth, `.disconnect()` su logout
- 2FA: flusso login intercetta `requires_2fa` response, switch a step verifica

---

## 2. Sicurezza — Analisi Approfondita

### 2.1 Autenticazione

#### JWT (JSON Web Tokens)
| Parametro | Valore | File Sorgente |
|-----------|--------|---------------|
| Algoritmo | HS256 (HMAC-SHA256) | `JwtService.php`, `settings.php` |
| Secret | Env `JWT_SECRET` | `settings.php` |
| Issuer | `kacinka-auth` | `JwtService.php` |
| Access Token TTL | 7,200s (2 ore) | `settings.php` |
| Refresh Token TTL | 604,800s (7 giorni) | `settings.php` |
| Remember-me TTL | 7,776,000s (90 giorni) | `settings.php` |
| Storage | HttpOnly, Secure, SameSite cookie | `JwtService.php` |
| Payload access | `iss`, `sub` (userId), `type: 'access'`, `iat`, `exp` | `JwtService.php` |
| Payload refresh | `iss`, `sub`, `type: 'refresh'`, `iat`, `exp` | `JwtService.php` |

**Flusso di autenticazione completo**:
```
1. POST /login → email + password
2. SecurityService.checkLockout(email) → se locked → 423 Locked
3. password_verify(input, hash)
4. Se 2FA abilitato → 200 { requires_2fa: true, temp_token }
5. POST /login/2fa → temp_token + totp_code → TOTP verification (±1 window)
6. JwtService.createAccessToken() → cookie access_token (HttpOnly, Secure, SameSite=None)
7. JwtService.createRefreshToken() → cookie refresh_token
8. SessionService.create() → hash(refresh_token) salvato in user_sessions
```

**Refresh token rotation**:
```
1. POST /refresh → cookie refresh_token
2. SessionService.validateByToken() → hash_equals(stored_hash, sha256(token))
3. Se valido O entro grace period (30s) → 
4. SessionService.rotateSessionToken() → nuovo hash
5. Nuovi cookie access_token + refresh_token
6. Se le vecchie sessioni sono revocate ma entro grace → mantiene flag persistent
   (gestisce race condition multi-tab: tab A refresha → tab B usa vecchio token → 30s grace)
```

#### OAuth2 Social Login
| Provider | Tipo | Callback |
|----------|------|----------|
| Google | OAuth2 Authorization Code | `GET /auth/google/callback` |
| GitHub | OAuth2 Authorization Code | `GET /auth/github/callback` |
| Apple | OAuth2 + JWT | `POST /auth/apple/callback` |

OAuth gestito da `OAuthService` (331 LOC) — crea utente se non esiste, link account OAuth a utente esistente se email corrisponde.

#### Two-Factor Authentication (2FA)
| Parametro | Valore |
|-----------|--------|
| Algoritmo | TOTP RFC 6238 (HMAC-SHA1) |
| Cifre | 6 |
| Periodo | 30 secondi |
| Tolleranza clock drift | ±1 time step (3 finestre) |
| Lunghezza secret | 20 caratteri Base32 |
| Encryption at rest | AES-256-CBC con `TOTP_ENCRYPTION_KEY` dedicata |
| Backup codes | 8 codici, 8-char hex uppercase, bcrypt-hashed |
| Consumo backup | One-time use — rimosso dall'array dopo uso |
| QR code | `api.qrserver.com` esterno |
| OTP URI | `otpauth://totp/Kacinka:{email}?secret=...&issuer=Kacinka&digits=6&period=30` |

#### Protezione Brute-Force
| Parametro | Valore |
|-----------|--------|
| Tentativi max | 5 falliti |
| Finestra conteggio | 60 secondi |
| Durata lockout | 900 secondi (15 minuti) |
| Tabelle | `login_attempts`, `account_lockouts` |
| Sblocco | Admin può sbloccare manualmente |
| Cleanup | Login attempts >24h eliminati automaticamente |
| User-agent | Troncato a 512 caratteri |

### 2.2 CSRF Protection

**Pattern**: Double-Submit Cookie

```
1. Server genera token: bin2hex(random_bytes(32)) → 64 hex chars
2. Cookie `csrf_token` (NOT HttpOnly, Secure, SameSite=None, Domain=.kacinka.it, Max-Age=86400)
3. Frontend legge cookie via document.cookie
4. Frontend invia header X-CSRF-Token su POST/PUT/PATCH/DELETE
5. Server confronta: hash_equals(cookie_value, header_value)
6. Token ruotato dopo ogni richiesta state-changing
```

**Path esenti**:
- Auth: `/login`, `/register`, `/register/email/*`, `/forgot-password`, `/reset-password`, `/refresh`, `/logout`
- OAuth: `/auth/google/callback`, `/auth/apple/callback`
- Webhook: `/stripe/webhook`
- Pubbliche: `/health`, `/api/contact`, `/api/beacon`, `/referral/validate`
- Prefissi: `/portal/`, `/api/forms/`, `/s/`, `/f/`, `/portal-v2/*`
- API key: richieste con header `X-API-Key` o prefix `Bearer kk_` bypassano CSRF

### 2.3 Rate Limiting

**Pattern**: Token-bucket style, DB-backed, per-IP

**22+ regole specifiche**:

| Endpoint Pattern | Limite | Finestra | Storage |
|-----------------|--------|----------|---------|
| `/login` | 5 req | 60s | `login_attempts` |
| `/register` | 3 req | 60s | `login_attempts` |
| `/register/email/send-code` | 3 req | 300s | `rate_limits` |
| `/register/email/verify-code` | 5 req | 300s | `rate_limits` |
| `/forgot-password` | 3 req | 900s | `login_attempts` |
| `/reset-password` | 5 req | 900s | `rate_limits` |
| `/verify-email/send` | 3 req | 300s | `rate_limits` |
| `/verify-phone/send` | 3 req | 300s | `rate_limits` |
| `/portal/*/pin` | 5 req | 300s | `rate_limits` |
| `/me/2fa/setup` | 3 req | 300s | `rate_limits` |
| `/me/2fa/verify` | 5 req | 300s | `rate_limits` |
| `/me/2fa/disable` | 3 req | 300s | `rate_limits` |
| `/ai/chat` | 15 req | 60s | `rate_limits` |
| `/ai/ask` | 15 req | 60s | `rate_limits` |
| `/api/v1/secretary/*` | 10 req | 60s | `rate_limits` |
| `/api/contact` | 5 req | 60s | `rate_limits` |
| `/api/forms/*/submit` | 10 req | 60s | `rate_limits` |
| `/projects/*/files` | 20 req | 60s | `rate_limits` |
| `/projects/*/tasks/*/attachments` | 20 req | 60s | `rate_limits` |
| `/projects/*/ocr/*` | 10 req | 60s | `rate_limits` |
| `/chats/*/upload` | 20 req | 60s | `rate_limits` |
| `/me/avatar` | 5 req | 60s | `rate_limits` |
| **Default (tutto il resto)** | **120 req** | **60s** | `rate_limits` |

**Dettagli implementativi**:
- IP detection chain: `X-Forwarded-For` → `X-Real-IP` → `CF-Connecting-IP` → `REMOTE_ADDR`
- DB: `INSERT ON DUPLICATE KEY UPDATE` per sliding window atomico
- Risposte: headers `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`, `Retry-After` su 429
- OPTIONS escluse da rate limiting

### 2.4 Security Headers

**Headers applicati a TUTTE le risposte** (via `SecurityHeadersMiddleware`, 95 LOC):

| Header | Valore (API routes) | Valore (Preview routes) |
|--------|---------------------|-------------------------|
| `X-Robots-Tag` | `noindex, nofollow` | `noindex, nofollow` |
| `X-Content-Type-Options` | `nosniff` | `nosniff` |
| `X-Frame-Options` | `DENY` | `ALLOWALL` |
| `Referrer-Policy` | `strict-origin-when-cross-origin` | `strict-origin-when-cross-origin` |
| `Permissions-Policy` | `camera=(), microphone=(), geolocation=(), payment=(self)` | uguale |
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains; preload` (solo prod) | uguale |

**Content Security Policy (CSP)**:

**API routes** (restrittivo):
```
default-src 'none';
script-src 'none';
style-src 'none';
img-src 'none';
font-src 'none';
connect-src 'self';
frame-ancestors 'none';
base-uri 'none';
form-action 'none'
```

**Preview routes** (`/template-preview/`, `/s/`, `/sites/preview`, `/portal/`, `/f/`):
```
default-src 'self';
script-src 'self' 'unsafe-inline';
style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
img-src 'self' data: https:;
font-src 'self' https://fonts.gstatic.com;
connect-src 'self';
frame-ancestors 'self' https://app.kacinka.it https://*.kacinka.it;
base-uri 'self';
form-action 'self'
```

### 2.5 CORS

**Configurazione** (da `CorsMiddleware.php`, 104 LOC):

| Parametro | Valore |
|-----------|--------|
| Origine (prod) | `https://app.kacinka.it` + CSV opzionale da `CORS_ORIGINS` env |
| Origine (dev) | Anche `http://localhost:*` |
| Credenziali | `true` (cookie) |
| Metodi | `GET, POST, PUT, PATCH, DELETE, OPTIONS` |
| Headers ammessi | `Content-Type, Authorization, X-Requested-With, X-CSRF-Token, sentry-trace, baggage` |
| Headers esposti | `X-Total-Count` |
| Preflight cache | 600s (10 min) |
| Error wrapping | Try/catch aggiunge CORS headers anche su errori (previene masking browser) |

### 2.6 RBAC (Role-Based Access Control)

**Gerarchia ruoli** (`PermissionService.php`, 265 LOC):

| Ruolo | Valore Gerarchia | Scope |
|-------|-----------------|-------|
| `owner` | 100 | Proprietario progetto — accesso completo hardcoded |
| `admin` | 80 | Tutti i permessi via `role_permissions` |
| `manager` | 60 | Gestione team + moduli business |
| `editor` | 40 | Modifica contenuti |
| `viewer` | 20 | Solo lettura |
| `guest` | 10 | Accesso minimo |

**Struttura database RBAC**:
```sql
roles (id, name, hierarchy)
permissions (id, key, module, description)
role_permissions (role_id, permission_id)  -- pivot M:N
project_members (user_id, project_id, role_id, permissions JSON)
```

**Formato permessi**: `module.action` (es. `tasks.create`, `files.delete`, `chats.send`)

**Risoluzione permessi per membro**:
```
Permessi Finali = (Permessi Ruolo Default) + (Grant Override) - (Revoke Override)

// project_members.permissions JSON:
{
  "grant": ["files.delete", "invoices.export"],
  "revoke": ["chats.send"]
}
```

**Bypass**:
- Project owner → TUTTI i permessi (hardcoded, non dipende da membership)
- Platform admin/superadmin → bypassa tutti i controlli progetto (`auth_users.role IN ('admin', 'superadmin')`)
- Hierarchy enforcement: un utente può modificare solo membri con gerarchia inferiore

**Payload frontend** (da `buildPermissionsPayload()`):
```json
{
  "role": "editor",
  "permissions": ["tasks.create", "tasks.edit", "files.upload"],
  "hierarchy": 40,
  "can": {
    "tasks": { "create": true, "edit": true, "delete": false },
    "files": { "upload": true, "delete": false }
  }
}
```

### 2.7 Plan Enforcement — Triple Layer

#### Layer 1: EntitlementMiddleware (Feature Gating)

Tre livelli verificati in ordine:

1. **Piano features (boolean)**: `white_label`, `sso`, `api_access`, `custom_domain` — explicit true/false
2. **Piano moduli** (array): `modules: ['all']` per piani Agency/Enterprise, lista specifica per altri
3. **Entitlements table**: Prodotti acquistati separatamente con status `active` + scadenza opzionale
4. **Feature flags**: `feature_flags` + `feature_flag_overrides` per-utente

**Project-owner-first logic**: Per operazioni project-scoped, si usa il piano del PROPRIETARIO del progetto (i membri team ereditano).

#### Layer 2: PlanLimitsMiddleware (Quantitative Limits)

740 LOC — middleware più complesso. Enforces limiti quantitativi:

| limitType | Piano Feature Key | Scope | Free Default |
|-----------|-----------------|-------|-------------|
| `projects` | `max_projects` | utente | 1 |
| `team_members` | `max_team_members` | progetto | 0 |
| `contacts` | `max_contacts` | utente | 10 |
| `tasks` | `max_tasks_per_project` | progetto | -1 (illimitato) |
| `deals` | `max_deals_per_project` | progetto | -1 |
| `files` | `max_files_per_project` | progetto | -1 |
| `invoices` | `max_invoices_month` | utente/mese | -1 |
| `ai_calls` | `max_ai_calls_month` | utente/mese | 0 |
| `email_campaigns` | `max_email_campaigns_month` | utente/mese | 0 |
| `storage` | `max_storage_gb` | utente | 0.1 GB |
| `sites` | `max_sites` | utente | -1 |
| `forms` | `max_forms` | utente | -1 |
| `workflows` | `max_workflows` | utente | -1 |

**Meccanismi avanzati**:
- `-1` = illimitato per qualsiasi limite
- **Entitlement stacking**: Add-on (`extra_ai_calls`, `storage_gb`) si sommano al piano base
- **Subscription overrides**: Admin può impostare override per-utente (sostituisce, non somma)
- **Storage aggregato**: `project_files.size_bytes` + `task_attachments.file_size` + `chat_messages.file_size`
- **Warning headers**: `X-Usage-Warning`, `X-Usage-Percentage`, `X-Usage-Status` a 80% e 95%
- **Errore**: HTTP 403 con `upgrade_required: true`, `limit_code`, `limit_max`, `grace_period` opzionale

#### Layer 3: UpgradeGate (Frontend)

Gating client-side per 25 moduli su 5 tier:
- **Starter** (7 moduli): task-manager, contacts, calendar, files, chat, forms, api-keys
- **Pro** (14): + invoices, expenses, crm, time-tracking, email-marketing, banking, webhooks
- **Pro+** (23): + accounting, reports, budget, analytics, workflows, fiscal, einvoice, client-portal, site-builder
- **Elite** (25): + open-banking, ocr-receipts
- **Enterprise** (all): tutti + SSO + white_label

### 2.8 File Upload Security

**FileValidationService**: validazione a 6 livelli (227 LOC)

```
Step 1: Verifica esistenza file
Step 2: Verifica dimensione <= limite
Step 3: Verifica estensione non in 49 blocklisted
Step 4: Double-extension detection (.php.jpg)
Step 5: Magic bytes via finfo (verifica MIME reale)
Step 6: Cross-validazione MIME dichiarato vs. rilevato
Step 7 (solo SVG): Scan contenuto per script/event handler (primi 8KB)
```

**49 estensioni bloccate**:
```
php, phtml, php3, php4, php5, phps, phar,
exe, bat, cmd, com, vbs, vbe, js, jse, ws, wsf, wsc, wsh, msi,
sh, bash, csh, ksh, zsh,
py, pyw, rb, pl, cgi,
asp, aspx, jsp, jspx,
htaccess, htpasswd,
sql, sqlite, db,
reg, inf, scr, ps1, psm1, psd1,
svg (spostato, validato separatamente),
dll, so, dylib
```

**SVG content scan** (primi 8KB):
- Blocca: `<script`, `javascript:`, `on` event handlers, `data:text/html`, `base64`

**sanitizeFilename()**:
- Rimuove null bytes, path traversal (`../`), caratteri non-ASCII
- Blocca double dots, percorsi assoluti
- Genera nome fallback se risultato troppo corto

---

## 3. Performance

### 3.1 Backend — HTTP Caching

**HttpCacheMiddleware** (123 LOC) — 3-tier caching:

| Route | Strategy | TTL | Tipo |
|-------|----------|-----|------|
| `/plans`, `/plans/compare`, `/hosting-plans` | `public, max-age=300, stale-while-revalidate=60` | 5 min | CDN-cacheable |
| `/me` | `private, max-age=30, stale-while-revalidate=10` | 30s | Solo browser |
| `/subscription/usage` | `private, max-age=60, stale-while-revalidate=10` | 1 min | Solo browser |
| `/features` | `private, max-age=300, stale-while-revalidate=10` | 5 min | Solo browser |
| **Tutti altri GET** | `no-store` | — | No cache |
| **Non-GET** | Nessun header cache | — | — |

**ETag**: Weak ETag `W/"<md5_16chars>"` calcolato su hash body risposta. Supporto `If-None-Match` → 304 Not Modified.

### 3.2 Frontend — Build Optimization

**Vite Config** (da `app/vite.config.js`):

| Ottimizzazione | Implementazione |
|----------------|-----------------|
| **Vendor splitting** | 6 chunk manuali: `vendor-react` (react, react-dom, react-router), `vendor-data` (zustand, react-query, react-hook-form, zod), `vendor-i18n` (i18next), `vendor-icons` (lucide), `vendor-motion` (framer-motion), `vendor-charts` (recharts) |
| **Target** | `es2020` |
| **Hash filenames** | `assets/js/[name]-[hash].js` → cache immutabile |
| **Source maps** | Abilitati per Sentry error tracking |
| **Chunk warning** | 600 KB |
| **Compressione** | Nessuna (Hostinger `mod_deflate` server-side — brotli pre-compressi causavano `SyntaxError`) |
| **Bundle analysis** | `rollup-plugin-visualizer` via `ANALYZE` env var |

### 3.3 Frontend — Runtime Performance

| Tecnica | Implementazione | File |
|---------|-----------------|------|
| **SWR cache** | `Map` in-memory con TTL 1 min + stale-while-revalidate | `apiFetch.js` |
| **Virtual scrolling** | `@tanstack/react-virtual` con `overscan=5`, `estimateSize=48px` | `VirtualList.jsx` |
| **Dynamic module loading** | 26 moduli caricati via custom `useState`-based `import()` | `WorkspaceShell` |
| **Lazy i18n** | `i18next-http-backend` — solo locale attivo caricato (≈5 MB risparmiati) | `i18n/index.js` |
| **AbortController** | Timeout 30s su tutte le fetch | `apiFetch.js` |
| **Pull-to-refresh** | Touch gesture su mobile con 0.4x damping, threshold 50px | `VirtualList.jsx` |
| **Skeleton loaders** | Preset-based: `kpi-bar`, `table-row` durante Suspense | `SkeletonLoader.jsx` |
| **Hash-based caching** | Asset immutabili con content hash | `vite.config.js` |

### 3.4 Database — Pattern di Query

**Pattern prevalente**: PDO Prepared Statements esclusivamente

```php
// Pattern tipo (100% dei 103 servizi)
$stmt = $this->db->prepare('
    SELECT c.*, COUNT(d.id) as deal_count
    FROM contacts c
    LEFT JOIN deals d ON d.contact_id = c.id
    WHERE c.project_id = :projectId
    AND c.deleted_at IS NULL
    ORDER BY c.created_at DESC
    LIMIT :limit OFFSET :offset
');
$stmt->execute([':projectId' => $projectId, ':limit' => $limit, ':offset' => $offset]);
```

**Pattern avanzati**:

| Pattern | Dove | Dettaglio |
|---------|------|-----------|
| `SELECT … FOR UPDATE SKIP LOCKED` | `JobQueueService.pop()` | Lock ottimistico per worker concorrenti |
| `INSERT ON DUPLICATE KEY UPDATE` | `RateLimitMiddleware`, `NotificationService` | Upsert atomico |
| Transazioni esplicite | `JobQueueService.pop()`, `.fail()` | BEGIN/COMMIT/ROLLBACK con try/catch |
| JSON column decode | `ActivityLogService`, `PlanLimitsMiddleware` | `json_decode()` su colonne JSON |
| Dynamic SQL (controllato) | `WorkflowService.executeUpdateField()` | Whitelist tabelle/colonne, MAI input utente |
| LIMIT/OFFSET pagination | Tutti i servizi con list | Universale |
| LEFT JOIN denormalizzato | `AuditService.listAll()`, `ActivityLogService` | Per read performanti |
| `DATE_SUB` server-side | `AuditService.cleanup()` | Retention-based cleanup |

**Nessun ORM**: Tutte le query sono SQL nativo con PDO prepared statements. Nessun query builder, nessun Doctrine/Eloquent.

### 3.5 Job Queue System

**`JobQueueService`** (516 LOC, 14 metodi):

| Caratteristica | Dettaglio |
|----------------|---------|
| **Backend** | Database (tabelle `jobs` + `failed_jobs`) |
| **Code (queue)** | 8 canali: `email`, `webhook`, `pdf`, `ocr`, `ai`, `report`, `einvoice`, `workflow` |
| **Priorità** | 1 (massima) → 9 (minima), default 5 |
| **Lifecycle** | `pending → reserved → running → completed/buried` |
| **Concurrency** | `SELECT … FOR UPDATE SKIP LOCKED` per pop multi-worker |
| **Retry** | Backoff esponenziale: 30s, 120s, 480s (×4) |
| **Max tentativi** | 3 (default) |
| **Dead letter** | Jobs falliti → `failed_jobs` (bury) |
| **Stale recovery** | `releaseStale()` reclama job stuck dopo timeout configurabile |
| **Statistiche** | Conteggi per-stato, per-coda, throughput/ora, falliti/24h, tempo medio (ultimi 100), grafico 7 giorni |
| **Auto DDL** | `ensureTable()` crea `jobs` + `failed_jobs` se assenti |
| **Troncamento output** | `mb_substr` a 10,000 caratteri per errori/output |

### 3.6 Assenza di Cache Layer

**Limitazione critica**: Non c'è Redis, Memcached, o altro cache layer in-memory.

**Motivo**: Hosting condiviso Hostinger non supporta servizi persistenti aggiuntivi.

**Impatti**:
- Rate limiting: DB-backed (query per ogni richiesta ad endpoint limitato)
- Sessioni: DB-backed (query per ogni richiesta autenticata)
- Caching applicativo: Solo HTTP cache headers + frontend SWR
- Feature flags: Query DB per ogni check (con cache in-memory per-request)
- RBAC: Cache in-memory per-request (`rolePermCache`, `memberPermCache`) ma nessuna cache cross-request

---

## 4. Observability & Logging

### 4.1 Logging Stack

| Layer | Strumento | Destinazione | Dettagli |
|-------|-----------|-------------|---------|
| **Request/Response** | `RequestLoggerMiddleware` (170 LOC) | `storage/logs/requests.log` (JSON-lines) | Ogni richiesta: method, path, sanitized body (12 campi redatti), IP, UA, status, duration_ms, request_id |
| **Application** | Monolog 3.8 (3 canali) | `storage/logs/app.log` | Canali: `app` (general), `request`, `cron` |
| **Security audit** | `AuditService` (93 LOC) | Tabella `audit_log` DB | Login, password change, 2FA, data export, permission changes |
| **Activity tracking** | `ActivityLogService` (57 LOC) | Tabella `activity_log` DB | Azioni utente: CRUD su entità business con meta JSON |
| **Cron** | `CronLogger` (73 LOC) | Tabella `cron_runs` DB | Start/success/fail con durata in ms, output troncato |
| **Error monitoring** | Sentry 4.12 (BE) + 9.14 (FE) | Sentry cloud | Traces sample rate 20%, user/request/project context |
| **Alert** | `TelegramService` (165 LOC) | Telegram Bot API | Errori 5xx inviati automaticamente |
| **Job queue** | `JobQueueService` | Tabelle `jobs`/`failed_jobs` | Ogni job: tentativo, errore, durata, output |

### 4.2 Request Logger — Dettagli

**Campi redatti (12)**:
```
password, token, secret, api_key, credit_card, cvv, 
ssn, authorization, refresh_token, access_token, otp, pin
```

**Comportamento**:
- Genera `X-Request-Id` (12-char hex) per correlazione
- Esclude: `/health`, `OPTIONS`
- Cattura risposta body solo per errori o <2KB
- Stringhe lunghe troncate a 200 char + marker `[N chars]`
- Scrittura: `FILE_APPEND + LOCK_EX` (thread-safe)
- `finally` block garantisce logging anche su eccezioni

### 4.3 Event System

**`EventDispatcher`** (66 LOC):
- Pattern Observer/Mediator
- `fire()` — fault-tolerant (catch `\Throwable`, return 0 su errore)
- `fireOrFail()` — propagate eccezioni (per testing)
- Dispatch sincrono → futuro: async (Redis/RabbitMQ documentato come TODO)
- Delega a `WebhookService` per delivery outgoing webhook

---

## 5. Workflow Automation Engine

### 5.1 Architettura (`WorkflowService`, 991 LOC)

**Modello**: DAG (Directed Acyclic Graph) con esecuzione BFS

**16 tipi di nodo**:
| Nodo | Tipo | Descrizione |
|------|------|-------------|
| `trigger` | Entry | Evento scatenante |
| `condition` | Logic | Valutazione booleana |
| `if_else` | Logic | Branch true/false (edge-label) |
| `for_each` | Logic | Iterazione su lista (inietta `_loop_index`, `_loop_count`) |
| `try_catch` | Logic | Error handling con retry configurabile |
| `switch` | Logic | Multi-branch |
| `delay` | Control | Pausa esecuzione |
| `filter` | Transform | Filtra dati |
| `transform` | Transform | Trasforma dati |
| `send_email` | Action | Invio email (via `EmailQueueService`) |
| `create_task` | Action | Creazione task |
| `update_field` | Action | Aggiornamento campo entità (whitelist) |
| `update_deal` | Action | Aggiornamento deal CRM |
| `notify` | Action | Notifica utente |
| `webhook` | Action | HTTP webhook outgoing |
| `http_request` | Action | Richiesta HTTP generica (curl, 10-15s timeout) |
| `error_handler` | Handler | Gestione errori custom |

**9 tipi di trigger**:
```
contact_created, deal_stage_changed, form_submitted, tag_added,
task_completed, timer, manual, webhook_incoming, schedule
```

**Safety mechanism**:
- Timeout: 300s MAX per esecuzione
- Concorrenza: max 5 esecuzioni per utente contemporanee
- Cicli: visited-set previene loop infiniti nel grafo
- Field update: whitelist tabelle/colonne (`contacts: [type, notes, lead_score, source]`, etc.)
- Try-catch: `max_retries` configurabile, azioni errore (`notify`/`skip`/`abort`)
- Template: interpolazione `{{var}}` nei dati nodo

**3 preset template**:
1. Nurturing Lead
2. Onboarding Cliente
3. Form → Deal

---

## 6. API Key System

### 6.1 Architettura (`ApiKeyAuthMiddleware`, 377 LOC)

**Formato chiave**: `kk_live_<hex>` o `kk_test_<hex>`

**Metodi di auth**:
1. Header `X-API-Key: kk_live_xxxxx`
2. Header `Authorization: Bearer kk_live_xxxxx`

**Rate limiting per-chiave**:
- Hourly limit + burst limit (dalla record chiave)
- Monthly plan-level: `max_api_calls_month` (usa piano del proprietario progetto)

**Scope (13 categorie)**:

| Scope | Route Coperte | Descrizione |
|-------|--------------|-------------|
| `admin` | API key management | Bypass totale tutti i controlli |
| `read` | Qualsiasi route in scope | Solo GET/HEAD/OPTIONS |
| `write` | Qualsiasi route in scope | Solo POST/PUT/PATCH/DELETE |
| `projects` | `/projects`, `/projects/*` | CRUD projetti |
| `tasks` | Task, dipendenze, template | Task management |
| `contacts` | Contatti utente/progetto | CRM contatti |
| `crm` | Deal, pipeline, proposte, aziende | CRM completo |
| `invoices` | Fatture, pagamenti | Fatturazione |
| `accounting` | Contabilità, report, tasse, OCR | Contabilità completa |
| `expenses` | Spese progetto | Gestione spese |
| `banking` | Conti, transazioni, riconciliazione, OB | Banking |
| `time` | Time entries | Time tracking |
| `calendar` | Calendario, eventi | Calendario |
| `webhooks` | Webhook progetto | Webhook |
| `analytics` | Analytics utente/progetto | Analytics |
| `sites` | Siti | Site builder |
| `forms` | Form, standalone forms | Form builder |
| `workflows` | Workflow | Automazioni |
| `email_marketing` | Campagne, liste email | Email marketing |

**Usage tracking**: Log asincrono per ogni chiamata API (path, method, latency ms, error flag). Non-blocking.

---

## 7. Gate Middleware (Business Rules)

### 7.1 Pipeline Gate (5 middleware dedicati)

| Gate | LOC | Scopo | Bypass |
|------|-----|-------|--------|
| `FrozenProjectMiddleware` | 107 | Blocca scritture su progetti congelati | `/subscription`, `/checkout`, `/billing`, `/stripe/webhook`, `/logout` |
| `TrialGateMiddleware` | 99 | Richiede abbonamento active/trialing/past_due | 22 path esenti (auth, health, onboarding, checkout, etc.) |
| `VerificationGateMiddleware` | 115 | Blocca scritture per utenti con email non verificata | Admin/superadmin bypass, 20 path esenti |
| `SmtpGateMiddleware` | 105 | Blocca invio email se SMTP non verificato | Solo route email, solo write |
| `GracePeriodMiddleware` | 94 | Permette lettura durante grace, blocca scrittura | `/subscription`, `/checkout`, `/billing`, `/stripe/webhook` |
| `MaintenanceMiddleware` | 54 | Blocco globale con bypass segreto | `?maintenance_secret=X` o `X-Maintenance-Secret` header |

### 7.2 FrozenProjectMiddleware — Dettagli

- Progetto congelato ha `frozen_at` NOT NULL
- Permette lettura per export dati pre-cancellazione
- Mostra `data_deletion_scheduled_at` e URL riattivazione
- Extraction project_id: URL regex `/projects/(\d+)` → body → query param

### 7.3 TrialGateMiddleware — Dettagli

- JOIN `subscriptions` + `plans` per risolvere stato
- Inietta attributi downstream: `subscription_status`, `plan_slug`, `trial_ends_at`
- 403 `subscription_required` con URL checkout

---

## 8. Build & Deploy

### 8.1 Configurazione Build

**Backend**:
- Nessun build step — PHP interpretato
- Composer per dipendenze (`vendor/autoload.php`)
- OPcache reset post-deploy (`opcache-reset` endpoint)

**Frontend App** (`app/vite.config.js`):
| Setting | Valore |
|---------|--------|
| Plugin | `@vitejs/plugin-react`, `@tailwindcss/vite` |
| Target | `es2020` |
| Source maps | ✅ (Sentry) |
| Chunks manuali | 6 (react, data, i18n, icons, motion, charts) |
| Naming | `assets/js/[name]-[hash].js` |
| Alias | `@` → `./src` |
| Dev proxy | `/api`, `/auth` → `https://local.auth.kacinka.it` |
| Chunk warning | 600 KB |

**Frontend Admin** (`admin/vite.config.js`):
| Setting | Valore |
|---------|--------|
| Plugin | `@vitejs/plugin-react` |
| Dev port | 5174 |
| Dev proxy | `/auth`, `/api` → `https://local.auth.kacinka.it` |
| Nessun chunk manuale | Tutti vendor in un unico bundle |

### 8.2 Deploy Pipeline

| Script | File | Scopo |
|--------|------|-------|
| `deploy_app.sh` | `deploy_scripts/` | Build + SCP frontend app |
| `deploy_admin.sh` | `deploy_scripts/` | Build + SCP admin panel |
| `deploy_auth.sh` | `deploy_scripts/` | SCP backend (no build) |
| `deploy_all.sh` | `deploy_scripts/` | Pipeline completa |
| OPcache reset | `GET /opcache-reset` | Reset post-deploy |

**SSH**: Porta 65002 (custom, non standard)

### 8.3 Infrastruttura (from `settings.php`)

| Componente | Dettaglio |
|-----------|---------|
| **Hosting** | Hostinger shared |
| **Database** | MySQL 8.x (senza Redis/Memcached) |
| **Storage file** | Filesystem locale (`storage/`) |
| **Backup** | Backblaze B2 (S3-compatible) |
| **CDN** | Nessuno configurato |
| **SSL** | Hostinger-managed (Let's Encrypt probabile) |
| **HSTS** | `max-age=31536000; includeSubDomains; preload` |
| **Monitoraggio** | Sentry (BE+FE) + Telegram alerting |

---

## 9. Testing Architecture

### 9.1 Backend (PHPUnit 11)

| Metrica | Valore |
|---------|--------|
| File test | 107 |
| LOC totali | 34,515 |
| LOC medio/file | 322 |
| Framework | PHPUnit 11.5 |
| Coverage | Non misurata (no config `phpunit.xml` coverage) |

**Pattern test**:
- Integration test con database reale (no mock DB)
- HTTP test funzionali (simulate request → assert response)
- Service unit test con PDO mock
- Fixture seeding per dati di test

### 9.2 Frontend E2E (Playwright)

| Metrica | Valore |
|---------|--------|
| Spec file | 25 |
| LOC totali | 3,316 |
| LOC medio/spec | 133 |
| Framework | Playwright 1.52 |
| Config | `playwright.config.js` |

### 9.3 Admin (Vitest)

| Metrica | Valore |
|---------|--------|
| File test | 4 |
| Test subjects | adminStore, api, App, components |
| Framework | Vitest |

### 9.4 Gap Testing

| Area | Copertura | Gap |
|------|-----------|-----|
| Backend servizi | **107 file test** | Alta copertura (34K LOC test vs 42K LOC service) |
| Frontend store | **0 test** | Nessun test Vitest per i 32 store app |
| Frontend componenti | **0 test** | Nessun test Vitest per i 55 componenti |
| Frontend pagine | **0 test** | Nessun test Vitest per le 284 pagine |
| E2E flows | **25 spec** | Copertura parziale dei 26 moduli |
| Admin | **4 test** | Copertura minima |

---

## 10. Qualità del Codice

### 10.1 Pattern Positivi

| Pattern | Dove | Benefit |
|---------|------|---------|
| Single Responsibility Action | 622 azioni thin | Manutenibilità |
| Prepared statements 100% | Tutti i 103 servizi | Sicurezza SQL injection |
| Fault-tolerant dispatching | `EventDispatcher.fire()` | Webhook failure non rompe flusso principale |
| Six-layer file validation | `FileValidationService` | Defense in depth upload |
| Token rotation con grace | `SessionService` (30s) | Multi-tab race condition gestita |
| Triple error boundary | App.jsx (3 livelli) | Resilienza UI progressiva |
| Per-request RBAC cache | `PermissionService` | Nessuna query RBAC duplicata per richiesta |
| SWR client-cache | `apiFetch.js` | Riduce richieste ripetute |
| Exponential backoff | `JobQueueService` | Retry intelligente job falliti |

### 10.2 Area di Miglioramento

| Issue | Severità | Dettaglio |
|-------|----------|---------|
| PlanLimitsMiddleware monolitico | Media | 740 LOC in una classe — andrebbe estratto in strategy pattern |
| Nessun cache layer | Alta | Rate limiting, sessioni, feature flags tutti DB-backed |
| Chat polling-based | Media | Nessun WebSocket; polling HTTP per realtime |
| Test FE mancanti | Alta | Zero test Vitest per store/componenti/pagine (75K+ LOC non testati) |
| Stringhe italiane hardcoded | Bassa | ~28 stringhe IT in codice FE (non i18n) |
| N+1 notifiche | Bassa | `notifyProjectMembers()` fa N+1 query |
| No CDN | Media | Asset serviti da shared hosting senza CDN |
| Event dispatch sincrono | Media | `EventDispatcher` sincrono (TODO async documentato) |
| SVG QR external | Bassa | QR code via `api.qrserver.com` — dipendenza esterna |
| Code splitting route disabilitato | Media | Tutto il codice route in bundle iniziale (~83K LOC) |

### 10.3 Metriche Complessità

| File | LOC | Complessità |
|------|-----|------------|
| `PlanLimitsMiddleware.php` | 740 | **Molto alta** — 14+ limit types, 4+ resolution paths, grace period |
| `WorkflowService.php` | 991 | **Molto alta** — BFS engine, 16 node types, interpolation |
| `AiAssistantService.php` | 1,577 | **Alta** — multi-provider, memory, streaming, tools |
| `StripeService.php` | 1,440 | **Alta** — 20+ operazioni Stripe, webhook events |
| `EInvoiceService.php` | 1,132 | **Alta** — 5 formati, validazione multi-standard |
| `ApiKeyAuthMiddleware.php` | 377 | **Media** — scope map, rate limiti, usage tracking |
| `routes.php` | 1,310 | **Alta** — 936 route definite in un file |

---

## 11. Riepilogo Tecnico

### Punti di Forza Architetturali
1. **Sicurezza multi-layer**: 20 middleware, 6 gate, CSRF, rate limiting per-endpoint, RBAC, file validation 6-step
2. **Prepared statements al 100%**: Zero rischio SQL injection su 80K LOC backend
3. **JWT con rotation + grace**: Gestione race condition multi-tab elegante
4. **Plan enforcement triplo**: Server (entitlement + limits) + Client (UpgradeGate)
5. **Job queue robusto**: Backoff esponenziale, dead letter, stale recovery, auto-DDL
6. **Observability**: 7 layer di logging (request, app, audit, activity, cron, Sentry, Telegram)

### Rischi Tecnici Principali
1. ~~**Mancanza cache layer**~~ — CacheService file-based implementato ✅
2. ~~**Test frontend assenti**~~ — 55+ PHPUnit + Playwright E2E multi-fase ✅
3. **Monolito PHP** — 80K LOC in un singolo app, nessun microservice (accettabile per scala)
4. ~~**No WebSocket**~~ — Pusher real-time implementato ✅
5. ~~**Event dispatch sincrono**~~ — EventDispatcher asincrono implementato ✅
6. **Code splitting route** — chunk strategy migliorata con Vite manualChunks

---

## Aggiornamento Post-Roadmap (v3.0)

### Miglioramenti Tecnici Implementati
- **CacheService**: File-based caching per rate limit, feature flags, dati frequenti
- **Pusher**: Real-time messaging per support chat, notifiche, dashboard updates
- **Cloudflare CDN**: Static assets serviti via CDN edge nodes
- **VIES SOAP**: Validazione IVA europea real-time con cache 24h
- **EventDispatcher**: Async event system con listener registrabili
- **JobQueueService**: Background processing con backoff esponenziale
- **N+1 Query Fix**: ListTasks, ListDeals, ListContacts ottimizzati
- **SWR Cache**: React store con stale-while-revalidate per UX fluida
- **i18n Split**: Namespace split per lazy loading, ~30KB first load
- **Service Worker**: Offline caching, background sync, push notifications

