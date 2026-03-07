# Migration History

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Appendice

---

## 1. Migration Strategy Overview

Kacinka uses a **sequential SQL migration** system rather than a framework-based ORM migrator:

- **Full schema** (`000_full_schema.sql`, 187 KB): Single idempotent file with `CREATE TABLE IF NOT EXISTS` for all 166 tables + 2 views
- **Seed data** (`001_seed_data.sql`, 72 KB): RBAC, plans, addons, bundles, dev users — all idempotent (`ON DUPLICATE KEY UPDATE`)
- **Incremental migrations** (numbered `001`-`055`+): Domain-specific schema changes
- **PHP runners** (`run_008.php` through `run_012.php`): Programmatic migration executors for complex multi-step changes
- **Rollback files**: `.rollback.sql` companions for reversibility

### Execution Protocol
```bash
# Fresh install (2 files)
SOURCE 000_full_schema.sql;  # Creates all 166 tables
SOURCE 001_seed_data.sql;    # Seeds RBAC + plans + addons

# Updates: Run only new incremental files
```

---

## 2. Migration File Inventory

### 2.1 Foundation Files

| File | Size | Purpose |
|------|------|---------|
| `000_full_schema.sql` | 187.2 KB | Full schema: 166 tables, 2 views, all indexes + FK |
| `001_seed_data.sql` | 71.6 KB | RBAC (6 roles, ~45 permissions), 4 plans, 20 addons, 3 bundles |
| `01_drop_all.sql` | 7.2 KB | Drop all tables (destructive reset) |
| `02_create_all.sql` | 1.4 KB | Legacy create wrapper |
| `03_seed_all.sql` | 60.4 KB | Legacy seed (superseded by 001) |
| `04_truncate_all.sql` | 6.1 KB | Truncate all tables (data reset, keep schema) |

### 2.2 Phase Migrations (Chronological)

| # | File | Size | Domain | Tables/Changes |
|---|------|------|--------|----------------|
| 001 | `001_subscriptions_hosting_addons.sql` | 10.6 KB | Commerce | Subscriptions, hosting plans, addon system |
| 002 | `002_contacts_chat_onboarding.sql` | 11.4 KB | CRM/Chat | Contact management, chat infrastructure, onboarding |
| 003 | `003_roles_permissions.sql` | 13.7 KB | RBAC | Full role-permission system, 6 roles |
| 004 | `004_phase2_plans_commerce_stripe.sql` | 24.8 KB | Commerce | Stripe integration, plan tiers, checkout sessions |
| 005 | `005_phase5_demo_site_templates.sql` | 4.8 KB | Sites | Demo project and site builder templates |
| 005b | `005_session_grace_period.sql` | 0.4 KB | Auth | Session rotation grace period column |
| 006a | `006_ai_bot_role.sql` | 0.4 KB | AI | Bot role for AI assistant |
| 006b | `006_company_extra_fields.sql` | 0.3 KB | CRM | Additional company profile fields |
| 006c | `006_email_verification_codes.sql` | 1.2 KB | Auth | Email OTP verification table |
| 006d | `006_persona_password_resets.sql` | 0.6 KB | Auth | Password reset tokens table |
| 006e | `006_plan_feature_flags.sql` | 0.7 KB | Commerce | Per-plan feature flag columns |
| 006f | `006_project_invite_codes.sql` | 0.5 KB | Projects | Invite code generation |
| 006g | `006_site_builder_templates.sql` | 32.6 KB | Sites | Full site builder template library |
| 007a | `007_ai_secretary.sql` | 2.3 KB | AI | AI secretary (proactive suggestions) tables |
| 007b | `007_crm_phase7.sql` | 10.3 KB | CRM | Phase 7 CRM enhancements |
| 008 | `008_feedback_page_status.sql` | 0.6 KB | Feedback | Page feedback and status tracking |
| 008b | `008_ops_phase8.sql` | 7.9 KB | Operations | Phase 8 operational tables |
| 009a | `009_performance_indices.sql` | 1.6 KB | Performance | Additional indexes for query optimization |
| 009b | `009_project_settings_member_visibility.sql` | 0.3 KB | Projects | Member visibility settings |
| 009c | `009_purchase_invoice_number.sql` | 0.3 KB | Invoices | Purchase invoice numbering |
| 010a | `010_email_change_requests.sql` | 0.5 KB | Auth | Email change verification flow |
| 010b | `010_login_attempts_add_path.sql` | 0.3 KB | Security | Login attempt path tracking |
| 010c | `010_site_analytics.sql` | 2.2 KB | Sites | Site analytics tables |
| 011a | `011_ai_support_infrastructure.sql` | 9.5 KB | AI | AI support ticket infrastructure, RAG |
| 011b | `011_sync_missing_schema.sql` | 7.9 KB | Schema | Sync any missing tables/columns |
| 012a | `012_sms_gateway.sql` | 0.9 KB | Communications | SMS gateway configuration |
| 012b | `012_task_comments_checklists_attachments.sql` | 3.2 KB | Tasks | Comments, checklists, file attachments on tasks |
| 013 | `013_invoices_add_contact_id.sql` | 0.7 KB | Invoices | Link invoices to contacts directly |
| 014 | `014_fix_collation.sql` | 2.3 KB | Schema | utf8mb4_unicode_ci collation standardization |

### 2.3 Sprint/Feature Migrations

| # | File | Size | Domain | Tables/Changes |
|---|------|------|--------|----------------|
| 018 | `018_billing_monetization.sql` | 6.9 KB | Billing | Monetization tables, usage tracking |
| 019 | `019_site_page_views.sql` | 2.5 KB | Analytics | Page view tracking |
| 020 | `020_site_quotes.sql` | 2.3 KB | Sites | Quote/estimate functionality |
| 021 | `021_site_media.sql` | 1.3 KB | Sites | Media library |
| 022 | `022_user_company_fields.sql` | 0.8 KB | Users | Additional company profile fields |
| 023 | `023_proposals.sql` | 1.7 KB | Sales | Proposal/quote management |
| 024 | `024_crm_emails.sql` | 1.2 KB | CRM | CRM email integration |
| 025 | `025_plan_limits_update.sql` | 3.4 KB | Commerce | Updated plan limits and quotas |

### 2.4 Commerce Series

| # | File | Size | Domain | Tables/Changes |
|---|------|------|--------|----------------|
| 030 | `030_plan_restructure_commerce.sql` | 6.2 KB | Commerce | Major plan tier restructure |
| 031a | `031_ai_nullable_project_id.sql` | 0.6 KB | AI | Allow AI without project context |
| 031b | `031_report_schedules_kpi_alerts.sql` | 2.2 KB | Reports | Scheduled reports, KPI alerts |
| 032a | `032_announcements.sql` | 0.9 KB | UI | Announcement/notification banners |
| 032b | `032_update_plan_limits.sql` | 3.6 KB | Commerce | Revised plan limits |
| 033 | `033_remove_demo_mode.sql` | 0.2 KB | Cleanup | Remove demo mode flag |
| 034 | `034_consumption_addons.sql` | 2.3 KB | Commerce | Usage-based addon consumption tracking |
| 035 | `035_revise_referral_tiers.sql` | 0.8 KB | Referral | Updated referral tier structure |
| 036 | `036_update_plan_pricing.sql` | 0.6 KB | Commerce | Final plan pricing adjustments |
| 037 | `037_whitelabel_architecture.sql` | 1.6 KB | Enterprise | White-label infrastructure |
| 038 | `038_truelayer_migration.sql` | 0.5 KB | Banking | TrueLayer → Tink migration prep |
| 039 | `039_deal_ownership_followers.sql` | 1.7 KB | CRM | Deal ownership + follower system |

### 2.5 Platform Scale Migrations

| # | File | Size | Domain | Tables/Changes |
|---|------|------|--------|----------------|
| 040 | `040_platform_invoices.sql` | 4.1 KB | Billing | Platform self-billing (FatturaPA) |
| 041 | `041_email_configs_domains.sql` | 3.8 KB | Email | Custom email domains, SMTP per-project |
| 042 | `042_trial_flow_frozen.sql` | 0.8 KB | Commerce | Trial → frozen subscription flow |
| 043 | `043_country_tax_profiles.sql` | 4.4 KB | Fiscal | Multi-country tax profiles |
| 044 | `044_fiscal_onboarding.sql` | 0.2 KB | Fiscal | Fiscal onboarding wizard support |
| 045 | `045_journal_account_mappings.sql` | 1.4 KB | Accounting | Journal → chart of accounts mappings |
| 046 | `046_einvoice_multi_format.sql` | 0.6 KB | E-Invoice | Multi-format e-invoice support flag |
| 047 | `047_platform_multi_country.sql` | 4.5 KB | Platform | Multi-country platform expansion tables |
| 048 | `048_open_banking_multi_country.sql` | 0.8 KB | Banking | Open banking multi-country support |
| 049 | `049_ai_memory.sql` | 1.1 KB | AI | AI conversation memory/context |
| 050a | `050_ai_feedback.sql` | 1.1 KB | AI | AI response feedback (thumbs up/down) |
| 050b | `050_fix_schema_post_restore.sql` | 2.0 KB | Schema | Post-restore schema corrections |
| 051 | `051_ai_spending_limits.sql` | 1.5 KB | AI | Per-user AI spending cap enforcement |
| 052 | `052_user_integrations.sql` | 2.7 KB | Integrations | User-level third-party integrations table |
| 053 | `053_google_contacts.sql` | 0.5 KB | Integrations | Google Contacts sync support |
| 054 | `054_google_drive.sql` | 0.7 KB | Integrations | Google Drive file linking |
| 055 | `055_custom_fields_extension.sql` | 0.6 KB | Customization | Custom field definitions extension |

### 2.6 Commerce Commerce Series (named)

| File | Size | Domain | Tables/Changes |
|------|------|--------|----------------|
| `checkout001_payment_dunning.sql` | 0.3 KB | Billing | Payment dunning/retry logic |
| `commerce005_threshold_alerts.sql` | 0.8 KB | Commerce | Usage threshold alert triggers |
| `commerce007_limit_grace_periods.sql` | 1.7 KB | Commerce | Plan limit grace period handling |
| `commerce008_realign_plan_modules.sql` | 2.2 KB | Commerce | Realign module access per plan |
| `commerce009_team_tier_positioning.sql` | 1.4 KB | Commerce | Team plan positioning |
| `commerce013_referral_program.sql` | 4.3 KB | Referral | Full referral program tables |

### 2.7 Sprint/Feature Migrations (named)

| File | Size | Domain | Tables/Changes |
|------|------|--------|----------------|
| `sprint6_webhooks.sql` | 3.4 KB | Webhooks | Webhook registration + dispatch tables |
| `sprint13_background_jobs.sql` | 2.9 KB | Jobs | Background job queue table |
| `sprint13_background_jobs.rollback.sql` | 0.1 KB | Jobs | Rollback for background jobs |
| `phase14_email_workflows_forms.sql` | 8.9 KB | Workflows | Email workflows, form builder, automations |

### 2.8 Utility Files

| File | Size | Purpose |
|------|------|---------|
| `seed_commerce.sql` | 6.8 KB | Commerce seed data (plans, addons, bundles) |
| `seed_roles_permissions.sql` | 6.3 KB | RBAC seed (superseded by 001) |
| `fix_members.sql` | 2.4 KB | Dev member/subscription fixes |
| `add_missing_tables.sql` | 19.0 KB | Catch-all for missing tables (merged into 000) |
| `run_008.php` | 1.0 KB | PHP migration runner for phase 8 |
| `run_009.php` | 2.3 KB | PHP migration runner for phase 9 |
| `run_010.php` | 0.9 KB | PHP migration runner for phase 10 |
| `run_011.php` | 1.4 KB | PHP migration runner for phase 11 |
| `run_012.php` | 0.6 KB | PHP migration runner for phase 12 |
| `2026_02_07_000001_initial_schema.sql` | 0.4 KB | Timestamped schema marker |
| `2026_02_07_000001_initial_schema.rollback.sql` | 6.5 KB | Full rollback script |
| `README.md` | 2.1 KB | Migration documentation |

---

## 3. PHP Migration Runners

Five PHP runner scripts exist for complex multi-step migrations requiring logic:

| Runner | Phase | Purpose |
|--------|-------|---------|
| `run_008.php` | 8 | Operations tables + data migration |
| `run_009.php` | 9 | Performance indices + settings + purchase inv |
| `run_010.php` | 10 | Auth tables (email change, login paths, analytics) |
| `run_011.php` | 11 | AI support + schema sync |
| `run_012.php` | 12 | SMS gateway + task attachments |

---

## 4. Domain Distribution

| Domain | Migration Count | Total KB |
|--------|----------------|----------|
| Commerce/Billing | 16 | ~58 KB |
| CRM/Sales | 5 | ~25 KB |
| AI | 7 | ~17 KB |
| Auth/Security | 6 | ~4 KB |
| E-Invoice/Fiscal | 5 | ~10 KB |
| Sites/Templates | 4 | ~42 KB |
| Schema/Fixes | 5 | ~20 KB |
| Integrations | 4 | ~5 KB |
| Operations | 3 | ~11 KB |
| Communications | 2 | ~1 KB |
| Other | 6 | ~12 KB |

---

## 5. Schema Evolution Timeline

### Phase 1 — Foundation (Est. Jan 2025)
- Core auth, projects, tasks, invoices, contacts
- `000_full_schema.sql` base: ~80 tables

### Phase 2 — Commerce (Est. Feb 2025)
- Plans, subscriptions, Stripe checkout
- Addons, bundles, hosting plans
- `004_phase2_plans_commerce_stripe.sql` (24.8 KB)

### Phase 3 — CRM + Chat (Est. Mar 2025)  
- Contact management, deal pipelines
- Real-time chat with participants
- `002_contacts_chat_onboarding.sql` (11.4 KB)

### Phase 4 — RBAC (Est. Mar 2025)
- 6 roles (owner, admin, manager, member, accountant, viewer)
- ~45 granular permissions
- `003_roles_permissions.sql` (13.7 KB)

### Phase 5 — Sites + Templates (Est. Apr 2025)
- Site builder templates (32.6 KB of template data)
- Demo project creation
- Analytics, page views

### Phase 6 — Micro-Features (Est. May 2025)
- AI bot role, email verification, invite codes, plan flags
- 7 small migrations in batch

### Phase 7 — CRM Enhancement + AI (Est. Jun 2025)
- AI secretary, proactive suggestions
- CRM phase 7 enhancements (10.3 KB)

### Phase 8 — Operations (Est. Jul 2025)
- Feedback system, operational tables
- Performance indices

### Phase 9-12 — Refinement (Est. Aug-Oct 2025)
- Email changes, site analytics
- AI support infrastructure (RAG, tickets)
- SMS gateway, task attachments
- Schema sync, collation fixes

### Phase 13 — Background Jobs (Est. Nov 2025)
- Job queue table (`sprint13_background_jobs.sql`)
- Worker architecture

### Phase 14 — Workflows (Est. Dec 2025)
- Email workflows, form builder
- Automation rules engine
- `phase14_email_workflows_forms.sql` (8.9 KB)

### Phase 18-25 — Monetization & Polish (Est. Jan 2026)
- Billing monetization, page views
- Quotes, proposals, media library
- Plan limits updates

### Phase 30-39 — Scale (Est. Feb 2026)
- Plan restructure, consumption addons
- Platform invoices, email domains
- Referral program, white-label
- TrueLayer → Tink migration

### Phase 40-55 — Pan-EU Expansion (Est. Feb-Mar 2026)
- Country tax profiles, fiscal onboarding
- Multi-country e-invoicing + open banking
- AI memory, feedback, spending limits
- Google integrations (Contacts, Drive)
- Custom fields extension

---

## 6. Statistics

| Metric | Value |
|--------|-------|
| **Total migration files** | 93 |
| **Total size** | ~690 KB |
| **Foundation files** | 6 (326.6 KB) |
| **Incremental migrations** | 77 |
| **PHP runners** | 5 |
| **Rollback files** | 2 |
| **Superseded/merged** | 3 |
| **Tables in full schema** | 166 |
| **Views in full schema** | 2 |
| **Seed data files** | 3 (138.8 KB) |

---

## 7. Known Issues & Debt

| Issue | Impact | Resolution |
|-------|--------|------------|
| No migration version table | Cannot track which migrations are applied | Add `migrations` table with checksum tracking |
| Numbering gaps (015-017, 026-029) | Cosmetic only | No action needed |
| Duplicate numbers (006a-g, 050a-b, etc.) | Ambiguous ordering | Consolidated in 000_full_schema |
| Some migrations merged into 000 | Running incrementals on fresh DB is redundant | README documents correct order |
| No down-migration system | Rollback is manual SQL | Add rollback registry |

### Recommendations

1. **Add migration tracking table**: `CREATE TABLE migrations (id INT AUTO_INCREMENT, filename VARCHAR(255) UNIQUE, checksum CHAR(64), applied_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)`
2. **PHP migration runner**: Simple script that checks `migrations` table and runs unapplied files in order
3. **Generate rollback files**: For all migrations missing `.rollback.sql` companions
4. **Consolidation**: Periodically merge incrementals into `000_full_schema.sql` (already done for most)

---

*Documento generato il 2026-03-07. Fonte: auth/migrations/ (93 file, ~690 KB totali), migrations/README.md.*
