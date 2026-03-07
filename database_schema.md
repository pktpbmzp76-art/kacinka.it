# Database Schema

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Overview

| Metrica | Valore |
|---------|--------|
| **DBMS** | MariaDB 10.x (Hostinger) |
| **Charset** | utf8mb4 / utf8mb4_unicode_ci |
| **Engine** | InnoDB (tutte le tabelle) |
| **Schema File** | `auth/migrations/000_full_schema.sql` |
| **Tabelle nel Schema** | 166 |
| **Tabelle Live** | ~176 (10 aggiunte post-schema via migrazioni incrementali) |
| **Migration Files** | 90+ files in `auth/migrations/` + `_migration/` |
| **Naming Convention** | snake_case, singolare/plurale misto |
| **FK Strategy** | ON DELETE CASCADE (predominante) |
| **JSON Support** | JSON columns con CHECK constraint `json_valid()` |

---

## 2. Schema by Domain

### 2.1 Auth & Identity (14 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **auth_users** | id, email, password_hash, role, plan_slug | → plans, → roles | Argon2id hashing |
| **user_sessions** | token_hash, user_id, device, ip | → auth_users | SHA-256 token hash |
| **login_attempts** | email, ip_address, success, path | — | Brute-force protection |
| **login_history** | user_id, ip, user_agent, city, country | → auth_users | Geolocation tracking |
| **account_lockouts** | email, locked_at, expires_at, reason | — | Auto-unlock via expires_at |
| **two_factor_auth** | user_id, secret, recovery_codes | → auth_users | TOTP |
| **password_resets** | email, token, expires_at | — | Time-limited tokens |
| **email_verification_tokens** | user_id, token, expires_at | → auth_users | Signup verification |
| **email_verification_codes** | — | — | 6-digit code flow |
| **phone_verification_codes** | — | — | SMS verification |
| **user_consents** | user_id, consent_type, version | → auth_users | GDPR consent tracking |
| **impersonation_sessions** | admin_id, target_user_id | → auth_users | Admin impersonation |
| **csrf_tokens** | token, user_id, expires_at | — | Server-side CSRF |
| **sse_tokens** | user_id, token | → auth_users | Server-Sent Events auth |

### 2.2 Projects & Workspace (10 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **projects** | id, user_id, name, settings | → auth_users | Workspace container |
| **project_members** | project_id, user_id, role_id | → projects, → auth_users, → roles | Team members |
| **project_files** | project_id, filename, path, size | → projects | Flysystem managed |
| **project_tags** | project_id, name, color | → projects | Organizzazione |
| **project_templates** | name, config, category | — | Starter templates |
| **project_onboarding** | project_id, step, completed | → projects | Setup wizard state |
| **project_addons** | project_id, addon_id, quantity | → projects, → addons | Addon subscriptions |
| **project_hosting** | project_id, hosting_plan_id | → projects | Site hosting |
| **workspace_state** | user_id, project_id, state | → auth_users, → projects | UI state persistence |
| **dashboard_widget_prefs** | user_id, project_id, layout | → auth_users, → projects | Widget config |

### 2.3 CRM (10 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **contacts** | id, project_id, type, email, vat_number | → projects | Lead/Customer/Vendor |
| **deals** | id, project_id, pipeline_id, stage_id, value | → projects, → pipelines | Kanban deals |
| **deal_pipelines** | id, project_id, name | → projects | Custom pipelines |
| **pipeline_stages** | id, pipeline_id, name, position | → deal_pipelines | Stage ordering |
| **deal_activities** | deal_id, type, description | → deals | Activity feed |
| **contact_deals** | contact_id, deal_id | → contacts, → deals | M:N link |
| **contact_submissions** | contact_id, form_id, data | → contacts, → forms | Form captures |
| **crm_accounting_links** | deal_id, invoice_id | → deals, → invoices | CRM→Finance |
| **crm_automations** | project_id, trigger, action, conditions | → projects | Rule-based automation |
| **crm_reminders** | deal_id, user_id, remind_at | → deals | Follow-up reminders |

### 2.4 Finance & Invoicing (16 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **invoices** | id, project_id, contact_id, number, status, total | → projects, → contacts | Core invoice |
| **invoice_items** | invoice_id, description, qty, price, tax_rate | → invoices | Line items (legacy) |
| **invoice_line_items** | invoice_id, product_id, qty, unit_price, tax | → invoices, → products | Line items (v2) |
| **invoice_relations** | parent_id, child_id, type | → invoices ×2 | Credit notes, duplicates |
| **invoice_sequences** | project_id, prefix, next_number | → projects | Auto-numbering |
| **invoice_templates** | project_id, name, html, css | → projects | Custom templates |
| **recurring_invoices** | invoice_id, frequency, next_date, end_date | → invoices | Auto-generation |
| **payments** | id, invoice_id, amount, method, date | → invoices | Payment tracking |
| **payment_allocations** | payment_id, invoice_id, amount | → payments, → invoices | Split payments |
| **payment_schedules** | invoice_id, installments, frequency | → invoices | Payment plans |
| **payment_methods_config** | project_id, stripe_enabled, bank_enabled | → projects | Payment config |
| **products** | id, project_id, name, sku, price | → projects | Product catalog |
| **exchange_rates** | base, target, rate, date | — | FX rates cache |
| **currencies** | code, name, symbol, decimals | — | ISO 4217 |
| **refunds** | payment_id, amount, reason | → payments | Refund tracking |
| **deliverable_approvals** | invoice_id, approved_by, approved_at | → invoices | Approval workflow |

### 2.5 Accounting (8 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **chart_of_accounts** | id, project_id, code, name, type | → projects | CoA tree |
| **journal_entries** | id, project_id, date, description, status | → projects | Double-entry |
| **journal_entry_lines** | journal_id, account_id, debit, credit | → journal_entries, → chart_of_accounts | Debit/Credit lines |
| **fiscal_years** | project_id, start_date, end_date, status | → projects | Fiscal period management |
| **accounting_periods** | fiscal_year_id, project_id, type, status | → fiscal_years, → projects | Monthly/quarterly |
| **accounting_audit_log** | entity_type, entity_id, action, field_changes | → projects | Financial audit trail |
| **cost_centers** | project_id, name, code | → projects | Cost allocation |
| **vat_reports** | project_id, period, country, totals | → projects | VAT filing data |

### 2.6 Tax & E-Invoicing (5 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **tax_codes** | id, project_id, name, rate, country | → projects | Tax rate catalog |
| **tax_regime_configs** | project_id, country, regime_type, settings | → projects | Fiscal regime (IT forfettario, etc.) |
| **withholding_tax_configs** | project_id, country, rate, threshold | → projects | Ritenuta d'acconto |
| **einvoice_configs** | project_id, country, gateway, credentials | → projects | Per-country gateway config |
| **einvoice_submissions** | invoice_id, format, status, xml_hash, response | → invoices | Submission tracking |

### 2.7 Expenses & Budget (6 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **expenses** | id, project_id, amount, category_id, date, receipt_path | → projects | Expense records |
| **expense_categories** | id, project_id, name, parent_id | → projects | Hierarchical categories |
| **expense_line_items** | expense_id, description, amount, tax_rate | → expenses | Multi-line expenses |
| **ocr_receipts** | id, project_id, file_path, extracted_data, confidence | → projects | OCR scan results |
| **budgets** | id, project_id, name, period, total_amount | → projects | Budget headers |
| **budget_lines** | budget_id, category_id, planned, actual | → budgets, → expense_categories | Line items |

### 2.8 Banking (6 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **bank_accounts** | id, project_id, name, iban, currency | → projects | Bank account register |
| **bank_transactions** | id, bank_account_id, external_id, amount, date | → bank_accounts | Imported transactions |
| **bank_reconciliations** | transaction_id, invoice_id, status | → bank_transactions, → invoices | Auto-matching |
| **open_banking_connections** | project_id, provider, institution_id, status | → projects | Tink connections |
| **open_banking_sync_log** | connection_id, items_synced, errors | → open_banking_connections | Sync history |
| **stripe_webhook_events** | event_id, type, payload, processed_at | — | Stripe event log |

### 2.9 Productivity (10 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **tasks** | id, project_id, title, status, assignee_id, due_date | → projects | Task board |
| **task_dependencies** | task_id, depends_on_id, type | → tasks ×2 | Gantt dependencies |
| **task_templates** | name, config, checklist | — | Task presets |
| **calendar_events** | id, project_id, title, start, end, type | → projects | Calendar |
| **time_entries** | id, task_id, user_id, duration, date | → tasks, → auth_users | Time tracking |
| **notes** | id, project_id, entity_type, entity_id, content | → projects | Universal notes |
| **comments** | id, entity_type, entity_id, user_id, body | → auth_users | Entity comments |
| **forms** | id, project_id, name, schema, settings | → projects | Form builder |
| **form_submissions** | form_id, data, source_ip | → forms | Submissions |
| **standalone_form_submissions** | — | — | Public form captures |

### 2.10 Communication (16 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **chats** | id, project_id, name, type | → projects | Chat channels |
| **chat_messages** | id, chat_id, user_id, body, type | → chats, → auth_users | Messages |
| **chat_participants** | chat_id, user_id | → chats, → auth_users | Membership |
| **chat_sections** | chat_id, name, position | → chats | Channel sections |
| **chat_reactions** | message_id, user_id, emoji | → chat_messages | Reactions |
| **chat_polls** | chat_id, question, options | → chats | Polls |
| **chat_poll_votes** | poll_id, user_id, option_index | → chat_polls | Votes |
| **chat_typing** | chat_id, user_id, typing_at | → chats | Typing indicator |
| **chat_file_requests** | id, chat_id, requested_by | → chats | File request flow |
| **email_campaigns** | id, project_id, subject, body, status | → projects | Marketing campaigns |
| **email_lists** | id, project_id, name | → projects | Subscriber lists |
| **email_list_members** | list_id, email, status | → email_lists | Members |
| **campaign_recipients** | campaign_id, email, status, opened_at | → email_campaigns | Delivery tracking |
| **email_queue** | id, to, subject, body, status, send_at | — | Async email delivery |
| **email_templates** | id, project_id, name, html | → projects | Email templates |
| **email_unsubscribes** | email, project_id, reason | → projects | Unsubscribe registry |

### 2.11 Website & Hosting (7 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **user_sites** | id, user_id, project_id, domain, slug | → auth_users, → projects | Published sites |
| **user_site_customizations** | site_id, theme, pages, settings | → user_sites | Site content (JSON) |
| **site_templates** | id, name, category, preview, config | — | Starter templates |
| **site_forms** | site_id, form_config | → user_sites | Embedded forms |
| **client_portal_config** | project_id, settings | → projects | Portal config |
| **client_portal_documents** | id, portal_config_id, file_path | → client_portal_config | Shared docs |
| **client_portal_messages** | id, portal_config_id, body | → client_portal_config | Messages |

### 2.12 AI & Support (6 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **ai_interactions** | user_id, model, input_tokens, output_tokens, cost_cents | → auth_users | Usage & cost tracking |
| **ai_conversation_summaries** | user_id, project_id, summary | → auth_users | Progressive summarization |
| **ai_escalations** | interaction_id, reason, status, assigned_to | → ai_interactions | Human escalation queue |
| **notifications** | user_id, type, title, data, read_at | → auth_users | In-app notifications |
| **notification_preferences** | user_id, channel, type, enabled | → auth_users | Notification settings |
| **user_feedback** | user_id, rating, comment, page | → auth_users | NPS/feedback |

### 2.13 Platform & Billing (20 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **plans** | id, slug, name, price_monthly, price_yearly, features | — | Plan definitions |
| **subscriptions** | id, user_id, plan_id, status, stripe_sub_id | → auth_users, → plans | Active subscriptions |
| **subscription_history** | subscription_id, event, data | → subscriptions | Lifecycle |
| **plan_usage** | user_id, project_id, metric, current_value | → auth_users, → projects | Limit tracking |
| **usage_events** | user_id, event_type, metadata | → auth_users | Event stream |
| **usage_metering** | user_id, metric, value, period | → auth_users | Monthly metering |
| **usage_metrics** | — | — | Aggregated metrics |
| **entitlements** | user_id, feature, granted_at | → auth_users | Feature flags per user |
| **feature_flags** | name, enabled, rollout_pct | — | Global feature flags |
| **feature_flag_overrides** | flag_id, user_id, enabled | → feature_flags, → auth_users | Per-user overrides |
| **stripe_checkout_sessions** | session_id, user_id, plan_id, status | → auth_users, → plans | Checkout tracking |
| **coupons** | code, discount_type, value, expires_at | — | Promo codes |
| **coupon_usage** | coupon_id, user_id, used_at | → coupons, → auth_users | Redemption log |
| **upgrade_paths** | from_plan, to_plan, discount | → plans ×2 | Upgrade matrix |
| **cancellation_surveys** | user_id, reason, feedback | → auth_users | Churn analysis |
| **revenue_snapshots** | date, mrr, arr, churn_rate | — | Revenue metrics |
| **platform_stats** | date, users, projects, invoices | — | Platform KPIs |
| **system_health_snapshots** | timestamp, cpu, memory, db_connections | — | Infra monitoring |
| **system_settings** | key, value, type | — | Platform config |
| **testimonials** | name, role, company, quote | — | Marketing content |

### 2.14 RBAC & Permissions (4 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **roles** | id, slug, name, is_system | — | owner, admin, member, viewer, ai_bot |
| **permissions** | id, slug, name, module | — | Fine-grained permissions |
| **role_permissions** | role_id, permission_id | → roles, → permissions | M:N mapping |
| **features** | id, slug, plan_id, enabled | → plans | Plan-feature matrix |

### 2.15 Integrations & Webhooks (8 tabelle)

| Tabella | Colonne Chiave | Relazioni | Note |
|---------|---------------|-----------|------|
| **webhooks** | id, project_id, url, events, secret | → projects | Outbound webhooks |
| **webhook_deliveries** | webhook_id, event, payload, status, response_code | → webhooks | Delivery log |
| **api_keys** | id, user_id, project_id, key_hash, scopes | → auth_users, → projects | API authentication |
| **api_key_usage** | api_key_id, endpoint, method, status, latency_ms | → api_keys | Usage analytics |
| **api_key_rate_limits** | api_key_id, limit_per_minute | → api_keys | Per-key limits |
| **user_integrations** | user_id, provider, access_token, scopes, status | → auth_users | Google, etc. |
| **audit_log** | user_id, action, entity, ip_address | → auth_users | General audit |
| **activity_log** | project_id, user_id, action, entity_type | → projects, → auth_users | Activity feed |

### 2.16 Custom Fields (1 tabella + future)

| Tabella | Colonne Chiave | Note |
|---------|---------------|------|
| **custom_field_defs** | project_id, entity_type, field_key, field_type, config | Polymorphic custom fields |

> Custom field values stored in `custom_field_values` table (created via `_migration/expand_custom_fields.php`, not in base schema).

---

## 3. Index Strategy

### Performance Indices (Migration 009)

```sql
-- Composite indices for main list endpoints:
idx_deals_project_active      (project_id, deleted_at, position)
idx_contacts_filter           (user_id, project_id, type, status)
idx_da_deal_created          (deal_id, created_at)
idx_tc_task                  (task_id)
idx_ta_task                  (task_id)
idx_tci_checklist            (checklist_id)
idx_inv_project_status       (project_id, status, created_at)
idx_cm_channel_created       (channel_id, created_at)
idx_tx_company_date          (company_id, date)
```

### Common Index Patterns
- **Tenant isolation**: `idx_{table}_project (project_id)` su quasi tutte le tabelle
- **Status filtering**: `idx_{table}_status (status)` dove applicabile
- **Date ordering**: `idx_{table}_created (created_at)` per cronologia
- **Unique constraints**: `uq_email` su auth_users, `uq_slug` su plans, ecc.
- **Foreign keys**: Indici automatici InnoDB su tutte le FK

---

## 4. Data Architecture Patterns

### 4.1 Multi-Tenancy
- **Project-scoped**: Quasi tutte le tabelle business hanno `project_id` FK → `projects.id`
- **User-scoped**: Tabelle auth/billing usano `user_id` FK → `auth_users.id`
- **Isolation**: Nessun accesso cross-project possibile (enforced a livello middleware + query)

### 4.2 Soft Deletes
- `deleted_at DATETIME DEFAULT NULL` su: deals, tasks, projects
- Query pattern: `WHERE deleted_at IS NULL`

### 4.3 JSON Columns
- `features JSON` su plans (limiti del piano)
- `metadata JSON` su invoices, expenses, tasks
- `settings JSON` su projects, einvoice_configs
- `data JSON` su form_submissions, notifications
- `scopes JSON` su user_integrations, api_keys
- Tutti con CHECK `json_valid()` constraint

### 4.4 Temporal Patterns
- `created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP`
- `updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP`
- Presenti su ~90% delle tabelle

### 4.5 Views
```sql
CREATE VIEW invoices_v2 AS ...    -- Unified invoice view
CREATE VIEW legal_consents AS ... -- GDPR consent aggregation
```

---

## 5. Migration History

### Migration Evolution (90+ files)

| Phase | Migrations | Focus |
|-------|-----------|-------|
| **000-003** | Full schema + seed + contacts + roles | Foundation |
| **004-006** | Plans, commerce, Stripe, sessions, AI bot role | Monetization |
| **007-009** | AI secretary, CRM phase 7, ops, performance indices | Feature expansion |
| **010-012** | Site analytics, AI support, SMS gateway | Communication |
| **013-025** | Invoice contacts, collation fix, billing, site features | Refinement |
| **030-039** | Plan restructure, referrals, white-label, TrueLayer→Tink | Architecture |
| **040-049** | Platform invoices, email configs, trials, fiscal, e-invoice | Multi-country |
| **050-055** | AI memory, feedback, spending limits, Google integration, custom fields | AI + Integration |
| **commerce001-013** | Payment dunning, alerts, grace periods, modules, referral, team tiers | Commerce stack |
| **sprint6-13** | Webhooks, background jobs | Dev sprints |

### Rollback Support
- `2026_02_07_000001_initial_schema.rollback.sql` — Full rollback disponibile
- `sprint13_background_jobs.rollback.sql` — Sprint rollback

---

## 6. Gap Analysis

| Area | Issue | Priorità |
|------|-------|----------|
| Naming inconsistency | Mix di singolare (expenses) e senza FK naming standard | Bassa |
| Missing FK constraints | Alcune tabelle _migration/ non aggiungono FK formali | Media |
| No partitioning | Tabelle ad alto volume (ai_interactions, audit_log, bank_transactions) cresceranno | Media |
| No archival strategy | Nessuna policy di archiviazione per dati storici | Media |
| Collation mix | `utf8mb4_general_ci` vs `utf8mb4_unicode_ci` (migration 014_fix_collation) | Bassa |
| Full-text indices | Solo `support_knowledge_base` ha FULLTEXT, potrebbe servire su notes/chat | Bassa |

---

## 7. Raccomandazioni

1. **Standardizzare naming**: Adottare convenzione completamente plurale o singolare
2. **Partitioning**: Considerare partition by range (monthly) su ai_interactions, audit_log, webhook_deliveries
3. **Read replicas**: Per analytics/reporting queries pesanti (quando in cloud)
4. **Data retention**: Definire policy (es. ai_interactions > 90 giorni → archivio, login_attempts > 30 giorni → purge)
5. **Schema documentation**: Generare ERD automatico da `000_full_schema.sql`

---

*Documento generato il 2026-03-07. Schema da `auth/migrations/000_full_schema.sql` (166 tabelle) + 10 tabelle incrementali. Tutti i nomi tabella verificati dal dump schema.*
