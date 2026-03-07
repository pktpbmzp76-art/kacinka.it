# Tech Stack Versions

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Appendice

---

## 1. Runtime Environment

| Componente | Versione | EOL | Note |
|-----------|---------|-----|------|
| **PHP** | ^8.1 (require), 8.2 (CI) | 8.1: Dec 2025 ⚠️, 8.2: Dec 2026 | Target: 8.3+ |
| **MariaDB** | 10.11 (CI service) | Jul 2028 | LTS |
| **Node.js** | 20 (CI) | Apr 2026 | LTS |
| **LiteSpeed** | Server-provided (Hostinger) | N/A | Managed |

---

## 2. PHP Backend Stack

### 2.1 Production Dependencies

| Package | Version Constraint | Latest (Mar 2026) | Status |
|---------|-------------------|-------------------|--------|
| `slim/slim` | 4.* | 4.14 | ✅ Current |
| `slim/psr7` | ^1.8 | 1.8 | ✅ Current |
| `php-di/php-di` | ^7.1 | 7.1 | ✅ Current |
| `monolog/monolog` | ^3.10 | 3.10 | ✅ Current |
| `vlucas/phpdotenv` | ^5.6 | 5.6 | ✅ Current |
| `firebase/php-jwt` | ^7.0 | 7.0 | ✅ Current |
| `stripe/stripe-php` | ^19.3 | 19.3 | ✅ Current |
| `phpmailer/phpmailer` | ^7.0 | 7.0 | ✅ Current |
| `league/flysystem` | ^3.31 | 3.31 | ✅ Current |
| `sentry/sentry` | ^4.20 | 4.20 | ✅ Current |
| `dompdf/dompdf` | ^3.1 | 3.1 | ✅ Current |
| `chillerlan/php-qrcode` | ^5.0 | 5.0 | ✅ Current |

### 2.2 Development Dependencies

| Package | Version Constraint | Status |
|---------|-------------------|--------|
| `phpunit/phpunit` | ^11.5 | ✅ Current |

### 2.3 PHP Extensions

| Extension | Required | Status |
|-----------|---------|--------|
| ext-curl | * | ✅ |
| ext-pdo | * | ✅ |
| ext-pdo_mysql | * | ✅ |
| ext-mbstring | * | ✅ |
| ext-openssl | * | ✅ |
| ext-fileinfo | * | ✅ |

---

## 3. Frontend Stack — Cloud App

### 3.1 Core Framework

| Package | Version | Status |
|---------|---------|--------|
| `react` | ^19.2.0 | ✅ Current (React 19) |
| `react-dom` | ^19.2.0 | ✅ Current |
| `react-router-dom` | ^7.13.0 | ✅ Current (v7) |
| `zustand` | ^5.0.11 | ✅ Current (v5) |
| `@tanstack/react-query` | ^5.90.20 | ✅ Current |
| `zod` | ^4.3.6 | ✅ Current (v4) |

### 3.2 UI Libraries

| Package | Version | Status |
|---------|---------|--------|
| `framer-motion` | ^12.33.0 | ✅ Current |
| `lucide-react` | ^0.563.0 | ✅ Current |
| `recharts` | ^3.7.0 | ✅ Current |
| `react-hot-toast` | ^2.6.0 | ✅ Current |
| `react-hook-form` | ^7.71.1 | ✅ Current |
| `@hookform/resolvers` | ^5.2.2 | ✅ Current |
| `html-to-image` | ^1.11.13 | ✅ Current |

### 3.3 Rich Text Editor (TipTap)

| Package | Version | Status |
|---------|---------|--------|
| `@tiptap/react` | ^3.19.0 | ✅ Current (v3) |
| `@tiptap/starter-kit` | ^3.19.0 | ✅ |
| `@tiptap/pm` | ^3.19.0 | ✅ |
| `@tiptap/extension-highlight` | ^3.19.0 | ✅ |
| `@tiptap/extension-link` | ^3.19.0 | ✅ |
| `@tiptap/extension-placeholder` | ^3.19.0 | ✅ |
| `@tiptap/extension-task-item` | ^3.19.0 | ✅ |
| `@tiptap/extension-task-list` | ^3.19.0 | ✅ |
| `@tiptap/extension-text-align` | ^3.19.0 | ✅ |
| `@tiptap/extension-underline` | ^3.19.0 | ✅ |

### 3.4 Calendar (FullCalendar)

| Package | Version | Status |
|---------|---------|--------|
| `@fullcalendar/core` | ^6.1.20 | ✅ Current |
| `@fullcalendar/daygrid` | ^6.1.20 | ✅ |
| `@fullcalendar/interaction` | ^6.1.20 | ✅ |
| `@fullcalendar/list` | ^6.1.20 | ✅ |
| `@fullcalendar/react` | ^6.1.20 | ✅ |
| `@fullcalendar/timegrid` | ^6.1.20 | ✅ |

### 3.5 Drag & Drop

| Package | Version | Status |
|---------|---------|--------|
| `@dnd-kit/core` | ^6.3.1 | ✅ Current |
| `@dnd-kit/sortable` | ^10.0.0 | ✅ Current |
| `@dnd-kit/utilities` | ^3.2.2 | ✅ Current |

### 3.6 Internationalization

| Package | Version | Status |
|---------|---------|--------|
| `i18next` | ^25.8.4 | ✅ Current |
| `react-i18next` | ^16.5.4 | ✅ Current |
| `i18next-browser-languagedetector` | ^8.2.0 | ✅ Current |
| `i18next-http-backend` | ^3.0.2 | ✅ Current |

### 3.7 Utilities

| Package | Version | Status |
|---------|---------|--------|
| `date-fns` | ^4.1.0 | ✅ Current (v4) |
| `dompurify` | ^3.3.1 | ✅ Current |
| `@sentry/react` | ^10.39.0 | ✅ Current |
| `@tanstack/react-virtual` | ^3.13.18 | ✅ Current |
| `pusher-js` | ^8.4.0 | ✅ Current |

---

## 4. Frontend Stack — Admin Panel

| Package | Version | Status |
|---------|---------|--------|
| `@sentry/react` | ^10.39.0 | ✅ |
| `lucide-react` | ^0.563.0 | ✅ |
| `react` | ^19.2.4 | ✅ |
| `react-dom` | ^19.2.4 | ✅ |
| `react-hot-toast` | ^2.6.0 | ✅ |
| `react-router-dom` | ^7.13.0 | ✅ |
| `zustand` | ^5.0.11 | ✅ |

---

## 5. Build & Dev Tools

| Tool | Version | Scopo |
|------|---------|-------|
| `vite` | ^7.2.4 (app) / ^7.3.1 (admin) | Build tool + HMR |
| `@vitejs/plugin-react` | ^5.1.1 / ^5.1.3 | React plugin |
| `tailwindcss` | ^4.1.18 | CSS framework |
| `@tailwindcss/vite` | ^4.1.18 | Vite integration |
| `typescript` | ^5.9.3 | Type checking |
| `eslint` | ^9.39.2 | Linting |
| `prettier` | ^3.8.1 | Formatting |
| `vitest` | ^4.0.18 | Testing |
| `@playwright/test` | ^1.58.1 | E2E testing |
| `jsdom` | ^28.0.0 | DOM emulation |
| `rollup-plugin-visualizer` | ^6.0.5 | Bundle analysis |
| `vite-plugin-compression` | ^0.5.1 | Gzip/Brotli |

---

## 6. CI/CD Environment

| Tool | Version | Scopo |
|------|---------|-------|
| **GitHub Actions** | Latest | CI/CD pipeline |
| `actions/checkout` | v4 | Repository clone |
| `actions/setup-node` | v4 | Node.js setup |
| `shivammathur/setup-php` | v2 | PHP setup |
| `actions/upload-artifact` | v4 | Build artifacts |
| `actions/download-artifact` | v4 | Download artifacts |
| **MariaDB** (CI service) | 10.11 | Test database |

---

## 7. Version Compatibility Matrix

```
PHP 8.1+  ──→  Slim 4.* + PHP-DI 7 + Monolog 3 + PHPUnit 11
Node 20   ──→  Vite 7 + React 19 + Vitest 4 + Playwright 1.58
MariaDB   ──→  10.11 LTS (compatible with MySQL 8.x syntax)
```

### Upgrade Path Recommendations

| Componente | Current | Target | Timeline | Effort |
|-----------|---------|--------|----------|--------|
| PHP | 8.1 → 8.2 (CI) | 8.3 | M1 | 2h (test + deploy) |
| Node.js | 20 LTS | 22 LTS | M6 | 1h |
| MariaDB | 10.11 | 11.x | M6 (with VPS migration) | 4h |

---

*Documento generato il 2026-03-07. Versioni estratte da composer.json, package.json (app + admin), e deploy.yml.*
