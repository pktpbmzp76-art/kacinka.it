# Audit Comparativo — Kacinka SaaS Service Layer

> **Generato**: Gennaio 2026 (aggiornato post-roadmap)  
> **Percorso**: `auth/src/Service/`  
> **Totale file**: 110+ (91 main + 6 EInvoice/ + 6 VatReport/ + nuovi servizi)  
> **Totale LOC**: ~42.000+  
> **Pattern architetturale**: Service-layer con PDO dependency injection (Slim 4)

---

## Sommario Esecutivo

| Metrica | Valore |
|---------|--------|
| File PHP totali | 103 |
| LOC totali | ~38.500 |
| Classi concrete | 101 |
| Interfacce | 2 (`EInvoiceBuilderInterface`, `VatReportInterface`) |
| API esterne integrate | Stripe, Google Gemini AI, OpenAI GPT-4o-mini, TrueLayer (PSD2), Aruba SDI, Infocert SDI, Google (OAuth/Calendar/Contacts/Drive/Sheets), Mindee OCR, Veryfi OCR, Telegram, Backblaze B2, Apple OAuth, ECB (tassi cambio) |
| DB engine | MySQL/MariaDB via PDO |
| Tabelle DB referenziate | ~180+ distinte |

---

## Indice per LOC (decrescente)

| # | File | LOC | Classe |
|---|------|-----|--------|
| 1 | AiAssistantService.php | 1577 | AiAssistantService |
| 2 | StripeService.php | 1440 | StripeService |
| 3 | EInvoiceService.php | 1132 | EInvoiceService |
| 4 | PlatformInvoiceService.php | 1124 | PlatformInvoiceService |
| 5 | OpenBankingService.php | 971 | OpenBankingService |
| 6 | CampaignService.php | 965 | CampaignService |
| 7 | ExpenseService.php | 920 | ExpenseService |
| 8 | PaymentService.php | 884 | PaymentService |
| 9 | WorkflowService.php | 874 | WorkflowService |
| 10 | ChartOfAccountsService.php | 819 | ChartOfAccountsService |
| 11 | JournalEntryService.php | 795 | JournalEntryService |
| 12 | EmailConfigService.php | 794 | EmailConfigService |
| 13 | InvoiceServiceV2.php | 785 | InvoiceServiceV2 |
| 14 | BudgetService.php | 771 | BudgetService |
| 15 | CalendarService.php | 690 | CalendarService |
| 16 | AnalyticsService.php | 672 | AnalyticsService |
| 17 | BankReconciliationService.php | 670 | BankReconciliationService |
| 18 | HostingResellerService.php | 651 | HostingResellerService |
| 19 | DunningService.php | 610 | DunningService |
| 20 | UsageMeteringService.php | 602 | UsageMeteringService |
| 21 | WebhookService.php | 602 | WebhookService |
| 22 | VatReportService.php | 590 | VatReportService |
| 23 | GoogleContactsService.php | 590 | GoogleContactsService |
| 24 | LimitGraceService.php | 584 | LimitGraceService |
| 25 | GoogleDriveService.php | 579 | GoogleDriveService |
| 26 | FinancialReportService.php | 530 | FinancialReportService |
| 27 | OssTrackingService.php | 510 | OssTrackingService |
| 28 | TaxService.php | 508 | TaxService |
| 29 | CountryTaxProfileService.php | 506 | CountryTaxProfileService |
| 30 | PlanService.php | 503 | PlanService |
| 31 | FormService.php | 493 | FormService |
| 32 | ApiKeyService.php | 491 | ApiKeyService |
| 33 | ReferralService.php | 481 | ReferralService |
| 34 | BankImportService.php | 477 | BankImportService |
| 35 | FiscalYearService.php | 457 | FiscalYearService |
| 36 | CustomDomainService.php | 456 | CustomDomainService |
| 37 | JobQueueService.php | 454 | JobQueueService |
| 38 | GoogleIntegrationService.php | 451 | GoogleIntegrationService |
| 39 | DomainRegistrarService.php | 435 | DomainRegistrarService |
| 40 | CountryTaxProfileSeeder.php | 418 | CountryTaxProfileSeeder |
| 41 | InvoiceNumberingService.php | 417 | InvoiceNumberingService |
| 42 | AdminAnalyticsService.php | 412 | AdminAnalyticsService |
| 43 | InvoicePdfService.php | 404 | InvoicePdfService |
| 44 | CrmAccountingBridge.php | 398 | CrmAccountingBridge |
| 45 | BrandingService.php | 395 | BrandingService |
| 46 | AiSuggestionService.php | 375 | AiSuggestionService |
| 47 | SiteBuilderService.php | 367 | SiteBuilderService |
| 48 | GoogleSheetsService.php | 366 | GoogleSheetsService |
| 49 | OcrService.php | 355 | OcrService |
| 50 | TimeTrackingService.php | 348 | TimeTrackingService |
| 51 | EInvoice/PeppolBIS3Builder.php | 342 | PeppolBIS3Builder |
| 52 | MailService.php | 340 | MailService |
| 53 | GoogleCalendarService.php | 340 | GoogleCalendarService |
| 54 | CurrencyService.php | 327 | CurrencyService |
| 55 | BankAccountService.php | 324 | BankAccountService |
| 56 | ClientPortalService.php | 313 | ClientPortalService |
| 57 | VatReport/ItalianLipeReport.php | 312 | ItalianLipeReport |
| 58 | DashboardMetricsService.php | 310 | DashboardMetricsService |
| 59 | VatReport/SpanishModelo303Report.php | 310 | SpanishModelo303Report |
| 60 | VerificationService.php | 305 | VerificationService |
| 61 | PermissionService.php | 299 | PermissionService |
| 62 | VatReport/FrenchCA3Report.php | 295 | FrenchCA3Report |
| 63 | VatReport/GermanUstvaReport.php | 284 | GermanUstvaReport |
| 64 | EInvoice/FacturXBuilder.php | 282 | FacturXBuilder |
| 65 | VatReport/UkMtdReport.php | 276 | UkMtdReport |
| 66 | TwoFactorService.php | 273 | TwoFactorService |
| 67 | FileValidationService.php | 272 | FileValidationService |
| 68 | EInvoice/FatturaPABuilder.php | 270 | FatturaPABuilder |
| 69 | EmailQueueService.php | 256 | EmailQueueService |
| 70 | PaymentScheduleService.php | 254 | PaymentScheduleService |
| 71 | ExpenseCategoryService.php | 241 | ExpenseCategoryService |
| 72 | B2BackupService.php | 236 | B2BackupService |
| 73 | AiEscalationService.php | 227 | AiEscalationService |
| 74 | StorageService.php | 224 | StorageService |
| 75 | AdminDashboardService.php | 223 | AdminDashboardService |
| 76 | SessionService.php | 215 | SessionService |
| 77 | TeamService.php | 208 | TeamService |
| 78 | FileService.php | 209 | FileService |
| 79 | AdminSystemService.php | 206 | AdminSystemService |
| 80 | OAuthService.php | 172 | OAuthService |
| 81 | EInvoice/ChorusProBuilder.php | 171 | ChorusProBuilder |
| 82 | UserBootstrapService.php | 160 | UserBootstrapService |
| 83 | TelegramService.php | 160 | TelegramService |
| 84 | CustomFieldService.php | 154 | CustomFieldService |
| 85 | AiMemoryService.php | 152 | AiMemoryService |
| 86 | InvoiceService.php | 142 | InvoiceService |
| 87 | NotificationService.php | 135 | NotificationService |
| 88 | EInvoice/XRechnungBuilder.php | 128 | XRechnungBuilder |
| 89 | PdfExportService.php | 124 | PdfExportService |
| 90 | AdminBillingService.php | 121 | AdminBillingService |
| 91 | SiteTemplateService.php | 111 | SiteTemplateService |
| 92 | JwtService.php | 106 | JwtService |
| 93 | SecurityService.php | 105 | SecurityService |
| 94 | FeatureFlagService.php | 94 | FeatureFlagService |
| 95 | UserService.php | 90 | UserService |
| 96 | AuditService.php | 84 | AuditService |
| 97 | FeedbackService.php | 76 | FeedbackService |
| 98 | CronLogger.php | 73 | CronLogger |
| 99 | EventDispatcher.php | 62 | EventDispatcher |
| 100 | VatReport/VatReportInterface.php | 56 | *interface* |
| 101 | EInvoice/EInvoiceBuilderInterface.php | 55 | *interface* |
| 102 | PipelineService.php | 47 | PipelineService |
| 103 | ActivityLogService.php | 46 | ActivityLogService |

---

## Dettaglio Servizi

---

### 1. AiAssistantService.php — 1577 LOC

**Classe**: `AiAssistantService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private NotificationService $notificationService,
    private LoggerInterface $logger,
    private array $geminiConfig,
    private ?TelegramService $telegram = null,
    private ?AiMemoryService $memoryService = null,
    private ?AiEscalationService $escalationService = null
)
```

**Costanti chiave**:
- `AI_LIMITS_BY_PLAN`: starter=25, pro=200, pro_plus=1000, elite=5000, enterprise=-1
- `MODEL_COSTS_PER_1M`: per modello (gemini-2.0-flash, gemini-1.5-pro, gpt-4o-mini)
- `SPENDING_CAP_FRACTION = 0.30` (30% del prezzo del piano)
- `SYSTEM_PROMPT`: ~3000 char di knowledge base piattaforma in italiano
- `ESCALATION_KEYWORDS`: array di keyword per escalation automatica

**Metodi pubblici** (17):
- `getBotUserId(): int`
- `checkAiAccess(int $userId, ?int $projectId): array`
- `processMessage(int $chatId, int $userId, string $messageBody, ?int $projectId): ?array`
- `processMessageWithContext(int $chatId, int $userId, string $messageBody, ?int $projectId, array $userContext): ?array`
- `getEscalationStats(): array`
- `listEscalations(array $filters, int $page, int $perPage): array`
- `resolveEscalation(int $escalationId, int $adminId, string $resolution): bool`
- `getChatHistory(int $chatId, int $limit): array`
- `storeMemory(int $userId, string $key, string $value, ?int $projectId, string $category, string $source): int`
- `recallMemory(int $userId, ?int $projectId, ?string $category): array`
- `deleteMemory(int $userId, string $key, ?int $projectId): bool`
- `clearMemory(int $userId, ?int $projectId): int`
- `static calculateCostCents(string $model, int $inputTokens, int $outputTokens): int`
- `static shouldTrigger(string $message): bool`

**Metodi privati** (17):
- `checkSpendingCap()`, `buildDynamicPrompt()`, `callOpenAIWithActions()`, `callOpenAIFallback()`, `callOpenAI()`, `callGemini()`, `getUpsellMessage()`, `ensureBotUser()`, `buildConversationContext()`, `stripMention()`, `checkEscalationKeywords()`, `checkRepeatedMessages()`, `storeInteraction()`, `sendBotMessage()`, `escalateToAdmin()`, `determineEscalationReason()`, `updateConversationSummary()`, `processMemoryActions()`

**Tabelle DB**: `auth_users`, `subscriptions`, `plans`, `consumption_addons`, `addon_packs`, `ai_interactions`, `ai_spending_config`, `ai_conversation_summaries`, `chat_messages`, `chat_participants`, `chats`

**API esterne**:
- Google Gemini (`generativelanguage.googleapis.com/v1beta/models/{model}:generateContent`)
- OpenAI GPT-4o-mini (`api.openai.com/v1/chat/completions`) — fallback

**Business logic**:
- Limiti AI per piano con spending cap (30% prezzo piano)
- Tracking costi token per modello
- Fallback multi-modello: Gemini → OpenAI
- Escalation automatica su keyword + messaggi ripetuti
- Summarization conversazione ogni 5 interazioni
- Memoria AI persistente (UPSERT per user/project/key)
- Action system ("save_note", "create_task", "memory_store")

---

### 2. StripeService.php — 1440 LOC

**Classe**: `StripeService implements PaymentGatewayInterface`

**Constructor**:
```php
__construct(
    PDO $db,
    array $stripeSettings,
    MailService $mailService,
    ReferralService $referralSvc,
    ?PlatformInvoiceService $platformInvoiceSvc = null,
    ?TelegramService $telegram = null
)
```

**Metodi pubblici** (17):
- `createCheckoutSession(int $userId, string $itemType, string $itemSlug, string $billingCycle, string $successUrl, string $cancelUrl, string $couponCode, ?int $projectId): array`
- `createMultiItemCheckoutSession(int $userId, array $items, string $billingCycle, string $successUrl, string $cancelUrl, string $couponCode): array`
- `constructWebhookEvent(string $payload, string $signature): \Stripe\Event`
- `processWebhookEvent(\Stripe\Event $event): array`
- `getUserSubscription(int $userId): ?array`
- `syncPlanUsageLimits(int $userId, string $planSlug): void`
- `cancelStripeSubscription(string $stripeSubId, bool $atPeriodEnd): array`
- `modifyStripeSubscription(string $stripeSubId, string $newPlanSlug, string $billingCycle): array`
- `createBillingPortalSession(int $userId, string $returnUrl): array`
- `getOrCreateStripeCustomer(int $userId): string`
- `validateCoupon(string $code, string $itemType, ?string $itemSlug, ?int $userId): ?array`
- `createCartCheckoutSession(int $userId, array $cartItems, string $couponCode, string $successUrl, string $cancelUrl): array`
- `recordCouponUsage(...)`, `getDefaultPaymentMethod(...)`, `getSecretKey()`, `getUpcomingInvoice(...)`, `syncBillingDataToStripe(...)`, `listStripeInvoices(...)`

**Metodi privati** (16):
- `handleCheckoutCompleted()`, `handleCartCheckoutCompleted()`, `activateSubscription()`, `activateHosting()`, `activateAddon()`, `activateBundle()`, `handleSubscriptionUpdated()`, `handleSubscriptionCancelled()`, `handleTrialWillEnd()`, `handlePaymentFailed()`, `handlePaymentSucceeded()`, `handleInvoicePaid()`, `resolveItem()`, `calculateAmountCents()`, `calculateDiscountCents()`, `getOrCreateStripeCoupon()`

**Tabelle DB**: `stripe_checkout_sessions`, `stripe_webhook_events`, `subscriptions`, `plans`, `auth_users`, `projects`, `user_purchases`, `coupon_usage`, `coupons`, `hosting_plans`, `project_hosting`, `addons`, `project_addons`, `bundles`, `entitlements`, `plan_usage`, `cart_items`, `products`

**API esterne**: Stripe SDK (Checkout, Webhook, Subscription, BillingPortal, Customer, PaymentMethod, Invoice, Coupon)

**Business logic**:
- Idempotent webhook processing (de-duplication via `stripe_webhook_events`)
- Multi-item checkout e cart checkout
- Coupon validation con limiti per utente
- Trial management (14 giorni per piani Pro)
- Lifecycle abbonamento: active → past_due → cancelled
- Email escalation su payment failure (3 livelli)
- Freezing progetto su cancellazione (12 mesi data retention)
- Referral qualification automatica
- Alert Telegram su eventi critici

---

### 3. EInvoiceService.php — 1132 LOC

**Classe**: `EInvoiceService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private InvoiceServiceV2 $invoiceService
)
```

**Metodi pubblici** (18):
- `registerBuilder(EInvoiceBuilderInterface $builder): void`
- `getBuilder(string $format): EInvoiceBuilderInterface`
- `getBuilders(): array`
- `resolveFormat(int $projectId): string`
- `generate(int $projectId, int $invoiceId, ?string $formatOverride): array`
- `getConfig(int $projectId): ?array`
- `saveConfig(int $projectId, array $data): array`
- `generateFatturaPA(...)` (deprecated)
- `generatePeppolBIS3(...)` (deprecated)
- `submit(int $projectId, int $submissionId): array`
- `handleNotification(int $projectId, array $data): array`
- `listSubmissions(...)`, `getSubmission(...)`, `getXmlContent(...)`, `deleteSubmission(...)`
- `parsePassiveInvoice(string $xmlContent): array`
- `getStats(...)`, `getUntransmittedInvoices(...)`, `validateXml(...)`

**Metodi privati** (16):
- `registerDefaultBuilders()`, `buildFatturaPAXml()`, `buildPeppolXml()`, `sendToGateway()`, `sendViaAruba()`, `sendViaInfocert()`, `sendViaCustomEndpoint()`, `getConfigInternal()`, `getProjectInfo()`, `getContactInfo()`, `generateFilename()`, `encryptApiKey()`, `decryptApiKey()`, `sanitizeXml()`, `addElement()`, `addElementNS()`, `xpathValue()`, `nodeValue()`, `parseEsitoFromXml()`

**Tabelle DB**: `einvoice_configs`, `einvoice_submissions`, `invoices`, `projects`, `contacts`, `country_tax_profiles`

**API esterne**:
- Aruba SDI (`ws.fatturazioneelettronica.aruba.it/services/invoice/v2`)
- Infocert SDI (`fattura-pa.infocert.it/api/v1/invoice/upload`)
- Custom SDI endpoint (configurabile)

**Business logic**:
- Strategy Pattern con `EInvoiceBuilderInterface`
- Formati: FatturaPA 1.2.2 (IT), PEPPOL BIS 3.0, XRechnung (DE), Factur-X/ZUGFeRD (FR/DE), Chorus Pro (FR B2G)
- Country-to-format mapping automatico
- SDI notification workflow (RC/MC/NS/AT/DT/NE/EC)
- FatturaPA XML DOM construction compliant
- PEPPOL UBL 2.1 XML construction
- Passive invoice XML parsing (ricezione)
- AES-256-CBC encryption per API key SDI

---

### 4. PlatformInvoiceService.php — 1124 LOC

**Classe**: `PlatformInvoiceService implements InvoiceServiceInterface`

**Constructor**:
```php
__construct(
    PDO $db,
    array $platformBilling,
    ?OssTrackingService $ossService = null
)
```

**Metodi pubblici** (16):
- `createFromStripeInvoice(int $userId, object $stripeInvoice): array`
- `createManual(int $userId, array $data): array`
- `list(array $filters): array`, `get(int $id): ?array`, `stats(): array`
- `regenerateXml(int $id): array`
- `markSubmitted(int $id, ?string $sdiId): array`
- `handleSdiResponse(int $id, string $status, ?string $errorMessage): void`
- `getPendingSdiSubmissions(int $maxAgeDays): array`
- `getPendingByGateway(): array`
- `logGatewayAction(...)`, `getXml(...)`, `generateDocument(...)`, `getBuyerCountry(...)`, `getApplicableVatRate(...)`, `getOssService()`, `statsMultiCountry()`

**Metodi privati** (8):
- `mapToBuilderData()`, `buildPdfHtml()`, `buildFatturaPAXml()`, `getBuyerData()`, `nextInvoiceNumber()`, `getTransmissionProgressive()`, `generateFilename()`, `addEl()`, `sanitize()`

**Tabelle DB**: `platform_invoices`, `platform_invoice_sequences`, `platform_invoice_gateway_log`, `auth_users`

**Business logic**:
- Fatturazione piattaforma → clienti Kacinka
- Multi-country VAT (EU reverse charge, OSS regime, out-of-scope)
- Numerazione progressiva atomica (KAC-YYYY-NNNNN)
- Format selection per paese (FatturaPA per IT, PDF per non-EU)
- Multi-gateway SDI submission cron
- HTML invoice generation per PDF (non-EU)

---

### 5. OpenBankingService.php — 971 LOC

**Classe**: `OpenBankingService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    array $openBankingSettings
)
```

**Metodi pubblici** (17):
- `listInstitutions(string $country): array`
- `createRequisition(int $projectId, string $institutionId, string $redirectUrl): array`
- `completeConnection(int $projectId, int $connectionId): array`
- `completeConnectionWithCode(int $projectId, int $connectionId, string $code, string $redirectUri): array`
- `linkToBankAccount(int $projectId, int $connectionId, int $bankAccountId): array`
- `syncTransactions(int $projectId, int $connectionId, string $syncType): array`
- `listConnections(...)`, `getConnection(...)`, `syncHistory(...)`, `revokeConnection(...)`
- `getOverviewStats(int $projectId): array`
- `getExpiringConsents(int $days): array`, `getExpiredConsents(): array`, `processExpiredConsents(): int`
- `categorizeTransactionsWithAI(int $projectId, int $bankAccountId, int $limit): int`
- `getProjectCountry(...)`, `getProjectBaseCurrency(...)`, `getBankAccountCurrency(...)`, `convertCurrency(...)`, `getSupportedCountries()`

**Metodi privati** (8):
- `updateStatus()`, `getAppAccessToken()`, `exchangeCode()`, `refreshUserToken()`, `ensureValidToken()`, `tokenRequest()`, `apiRequest()`, `triggerAutoReconciliation()`, `fetchAndStoreAccounts()`

**Tabelle DB**: `open_banking_connections`, `open_banking_sync_log`, `bank_transactions`, `bank_accounts`, `projects`, `exchange_rates`, `auth_users`

**API esterne**:
- TrueLayer Auth (`auth.truelayer.com`) + Data (`api.truelayer.com` / sandbox)
- Google Gemini (`generativelanguage.googleapis.com`) — categorizzazione AI transazioni

**Business logic**:
- PSD2 Open Banking via TrueLayer
- OAuth2 code exchange + token refresh
- Multi-account banks
- Transaction deduplication su external_id
- Auto-reconciliation trigger post-sync
- PSD2 consent management (scadenza, rinnovo)
- AI-based transaction categorization via Gemini
- Currency conversion per conti multi-valuta

---

### 6. CampaignService.php — 965 LOC

**Classe**: `CampaignService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private EmailQueueService $emailQueue
)
```

**Metodi pubblici** (30):
- `list(...)`, `getOverviewStats(...)`, `get(...)`, `create(...)`, `update(...)`, `delete(...)`
- `send(int $id, int $userId): array` — invio campagna
- `sendTest(...)`, `getStats(...)`
- **Liste**: `listLists()`, `getList()`, `createList()`, `updateList()`, `deleteList()`, `getListMembers()`, `addToList()`, `removeFromList()`, `importContacts()`
- **Unsubscribe**: `unsubscribe()`, `isUnsubscribed()`
- **CRM sync**: `syncCrmContactsToList()`, `onDealStageChanged()`
- **A/B Test**: `createAbTest()`, `getAbTest()`, `sendAbTest()`, `pickAbTestWinner()`
- **Sequenze drip**: `listSequences()`, `getSequence()`, `createSequence()`, `updateSequence()`, `deleteSequence()`, `addSequenceStep()`, `deleteSequenceStep()`, `enrollContact()`, `processSequenceQueue()`
- **Analytics**: `recordEvent()`, `getDeliverabilityDashboard()`, `getSpamComplaints()`

**Metodi privati** (3):
- `processOneEnrollment()`, `advanceEnrollment()`, `getNextStep()`

**Tabelle DB**: `email_campaigns`, `email_lists`, `email_list_members`, `campaign_recipients`, `email_unsubscribes`, `contacts`, `email_templates`, `ab_test_variants`, `email_sequences`, `email_sequence_steps`, `email_sequence_enrollments`, `deals`, `crm_email_sync_rules`, `email_events`, `auth_users`

**Business logic**:
- Email marketing completo con liste, template, personalizzazione
- A/B testing con split automatico e winner picking
- Drip sequences / automation email
- CRM-to-email sync basato su deal stage
- Deliverability dashboard e spam complaint tracking

---

### 7. ExpenseService.php — 920 LOC

**Classe**: `ExpenseService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private JournalEntryService $journalService,
    private ?NotificationService $notificationService = null
)
```

**Metodi pubblici** (15):
- `list(...)`, `count(...)`, `get(...)`, `create(...)`, `update(...)`
- `submitForApproval(...)`, `approve(...)`, `reject(...)`, `markAsPaid(...)`, `void(...)`
- `getBillableExpenses(...)`, `markAsBilled(...)`, `attachReceipt(...)`
- `getSummary(...)`, `getByCategory(...)`

**Metodi privati** (8):
- `generateJournalEntry()`, `generatePaymentJournalEntry()`, `resolveAccountWithFallback()`, `insertLineItems()`, `resolveExpenseAccount()`, `resolveAccount()`, `nextExpenseNumber()`, `auditLog()`

**Tabelle DB**: `expenses`, `expense_line_items`, `expense_categories`, `contacts`, `chart_of_accounts`, `accounting_audit_log`, `auth_users`, `tax_codes`, `bank_transactions`

**Business logic**:
- Workflow spese: draft → submitted → approved → paid (con reject e void)
- Auto-generazione journal entry su approval
- Line items con tax code
- Receipt attachment con OCR data
- Billable expenses per re-fatturazione
- Audit log contabile completo

---

### 8. PaymentService.php — 884 LOC

**Classe**: `PaymentService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private JournalEntryService $journalService
)
```

**Metodi pubblici** (14):
- `list(...)`, `count(...)`, `get(...)`, `create(...)`, `update(...)`
- `allocate(int $paymentId, array $allocations, int $userId): array`
- `deallocate(int $allocationId, int $userId): bool`
- `getUnallocated(...)`, `getByInvoice(...)`
- `refund(int $paymentId, int $userId, array $data): array`
- `listRefunds(...)`, `listPaymentMethods(...)`, `createPaymentMethod(...)`, `updatePaymentMethod(...)`, `deletePaymentMethod(...)`

**Metodi privati** (7):
- `generateJournalEntry()`, `generateRefundJournalEntry()`, `allocateToInvoice()`, `recalculateInvoicePaymentStatus()`, `resolveAccount()`, `nextPaymentNumber()`, `auditLog()`

**Tabelle DB**: `payments`, `payment_allocations`, `payment_methods_config`, `invoices`, `refunds`, `chart_of_accounts`, `accounting_audit_log`, `auth_users`

**Business logic**:
- Allocazione pagamento multi-fattura
- Ricalcolo automatico stato fattura (paid/partial/unpaid)
- Rimborsi con journal entry inversivo
- Metodi di pagamento configurabili per progetto
- Numerazione automatica (PAY-YYYY-NNNNN / REF-YYYY-NNNNN)

---

### 9. WorkflowService.php — 874 LOC

**Classe**: `WorkflowService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private EmailQueueService $emailQueue,
    private NotificationService $notifications
)
```

**Metodi pubblici** (14):
- `list(...)`, `toggleActive(...)`, `getOverviewStats(...)`, `get(...)`, `create(...)`, `update(...)`, `delete(...)`, `duplicate(...)`
- `fireTrigger(string $triggerType, array $context, ?int $userId): int`
- `executeWorkflow(array $workflow, array $context): array`
- `fireWebhookTrigger(string $webhookSlug, array $payload, ?string $sourceIp): int`
- `listExecutions(...)`, `getPresetTemplates()`, `getNodeOptions(...)`

**Metodi privati** (7):
- `executeNode()`, `evaluateCondition()`, `executeUpdateField()`, `interpolate()`, `executeForEachNode()`, `executeTryCatchNode()`, `executeUpdateDeal()`, `defaultNodes()`

**Tabelle DB**: `workflows`, `workflow_executions`, `deals`, `tasks`, `email_templates`, `auth_users`, `project_members`, `standalone_forms`

**Business logic**:
- Visual workflow builder con nodi: condition, action, for-each, try-catch
- Trigger types: deal_stage_changed, invoice_created, task_completed, webhook, schedule
- Action types: send_email, create_notification, update_field, create_task, update_deal
- Template interpolation `{{variable}}` con contesto
- Preset templates pronti all'uso
- Execution logging per audit

---

### 10. ChartOfAccountsService.php — 819 LOC

**Classe**: `ChartOfAccountsService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (11):
- `list(...)`, `get(...)`, `create(...)`, `update(...)`, `delete(...)`
- `getTree(int $projectId): array` — albero gerarchico
- `getAccountBalance(int $accountId): array`
- `getBalances(int $projectId, ?string $fromDate, ?string $toDate): array`
- `getAccountLedger(int $accountId, ?string $from, ?string $to, int $limit, int $offset): array`
- `importItalianTemplate(int $projectId): int`
- `importStandardChart(string $countryCode, int $projectId): int`
- `createAccountMappings(...)`, `getAccountMappings(...)`

**Metodi privati** (10):
- `buildTree()`, `getAccountMappingDefinitions()`, `getInsertIgnoreKeyword()`, `getItalianTemplate()`, `getGermanTemplate()`, `getFrenchTemplate()`, `getSpanishTemplate()`, `getUKTemplate()`, `getUSTemplate()`, `getGenericTemplate()`

**Tabelle DB**: `chart_of_accounts`, `journal_entries`, `journal_entry_lines`, `journal_account_mappings`

**Business logic**:
- Piano dei conti gerarchico (parent-child tree)
- Template country-specific: IT, DE, FR, ES, UK, US, generico EU
- Account mappings per automazione journal entries
- Saldi calcolati live da journal entries

---

### 11. JournalEntryService.php — 795 LOC

**Classe**: `JournalEntryService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (12):
- `list(...)`, `count(...)`, `get(...)`, `create(...)`, `update(...)`, `post(...)`, `void(...)`, `delete(...)`
- `generateFromInvoice(int $projectId, int $userId, array $invoice, array $accountMap): array`
- `generateFromBankTransaction(int $projectId, int $userId, array $transaction): array`
- `generateClosingEntries(int $projectId, int $userId, int $fiscalYearId): array`
- `getTrialBalance(int $projectId, ?string $fromDate, ?string $toDate): array`

**Metodi privati** (6):
- `validateBalance()`, `nextEntryNumber()`, `insertLines()`, `assertPeriodOpen()`, `auditLog()`, `resolveAccountByMapping()`

**Tabelle DB**: `journal_entries`, `journal_entry_lines`, `accounting_audit_log`, `accounting_periods`, `fiscal_years`, `chart_of_accounts`, `journal_account_mappings`, `bank_transactions`, `invoices`, `auth_users`

**Business logic**:
- Partita doppia con validazione D/C balance
- Workflow: draft → posted → voided
- Generazione automatica da fatture e transazioni bancarie
- Chiusure d'esercizio automatiche
- Bilancio di verifica
- Assertion periodo contabile aperto

---

### 12. EmailConfigService.php — 794 LOC

**Classe**: `EmailConfigService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private array $platformMailConfig = []
)
```

**Metodi pubblici** (16):
- `listConfigs(...)`, `getConfig(...)`, `saveConfig(...)`, `deleteConfig(...)`
- `testConnection(int $configId, int $ownerId, string $recipientEmail): array`
- `resolveSmtpConfig(int $projectId): ?array`
- `ownerHasVerifiedSmtp(int $ownerId): bool`
- **Domini**: `listDomains()`, `addDomain()`, `removeDomain()`, `verifyDomain()`, `getDnsInstructions()`
- `validateFromDomain(...)`, `getBrandedMailConfig(...)`, `getEmailTemplatePreview(...)`, `getSmtpHealthOverview(...)`

**Metodi privati** (3):
- `getConfigRaw()`, `formatResolvedConfig()`, `renderTestEmailHtml()`

**Tabelle DB**: `email_configs`, `email_domains`, `agency_branding`, `projects`, `plans`, `subscriptions`

**Business logic**:
- SMTP config multi-provider custom
- Domain verification via DNS TXT record
- Risoluzione SMTP gerarchica: user config > owner config > platform default
- White-label email con branded templates
- Test connessione SMTP con invio effettivo

---

### 13. InvoiceServiceV2.php — 785 LOC

**Classe**: `InvoiceServiceV2`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private TaxService $taxService,
    private InvoiceNumberingService $numberingService,
    private JournalEntryService $journalService
)
```

**Metodi pubblici** (21):
- `list(...)`, `get(...)`, `createWithLines(...)`, `update(...)`, `finalize(...)`, `markSent(...)`
- `createCreditNote(int $originalInvoiceId, int $userId, ?array $partialLines): array`
- `duplicate(...)`, `convertToInvoice(int $proformaId, int $userId): array`
- `getAgingReport(int $projectId, string $type): array`
- `markPaid(...)`, `markOverdue(...)`, `voidInvoice(...)`
- **Template**: `listTemplates()`, `getTemplate()`, `createTemplate()`, `updateTemplate()`, `deleteTemplate()`
- `getAuditLog(...)`, `delete(...)`

**Metodi privati** (5):
- `insertLineItems()`, `getLineItems()`, `getRelations()`, `getCurrentFiscalYearId()`, `resolveAccountId()`

**Tabelle DB**: `invoices`, `invoice_line_items`, `invoice_relations`, `invoice_templates`, `document_snapshots`, `accounting_audit_log`, `chart_of_accounts`, `fiscal_years`, `contacts`, `tax_codes`, `auth_users`

**Business logic**:
- Fatture con line items, tax codes, sconti
- Workflow: draft → finalized → sent → paid/overdue/voided
- Note di credito (totali e parziali)
- Conversione proforma → fattura
- Aging report (current/30/60/90/120+ giorni)
- Template personalizzabili
- Document snapshot per immutabilità post-finalizzazione
- Journal entry automatico su finalizzazione

---

### 14. BudgetService.php — 771 LOC

**Classe**: `BudgetService`

**Constructor**:
```php
__construct(
    PDO $pdo,
    ?NotificationService $notificationService = null
)
```

**Metodi pubblici** (19):
- **Cost Centers**: `listCostCenters()`, `getCostCenter()`, `createCostCenter()`, `updateCostCenter()`, `deleteCostCenter()`
- **Budgets**: `listBudgets()`, `getBudget()`, `createBudget()`, `updateBudget()`, `deleteBudget()`, `approveBudget()`, `activateBudget()`, `closeBudget()`
- **Budget Lines**: `addBudgetLine()`, `updateBudgetLine()`, `deleteBudgetLine()`
- **Analisi**: `varianceReport()`, `varianceByCostCenter()`, `checkBudgetAlerts()`
- **Time billing**: `getUninvoicedTime()`, `timeToInvoiceLines()`, `getStats()`

**Metodi privati** (3):
- `saveBudgetLines()`, `recalcTotal()`, `checkSingleBudgetAlerts()`, `createBudgetAlert()`

**Tabelle DB**: `budgets`, `budget_lines`, `cost_centers`, `chart_of_accounts`, `journal_entries`, `journal_entry_lines`, `fiscal_years`, `projects`, `auth_users`, `notifications`, `tasks`, `time_entries`

**Business logic**:
- Budget con workflow (draft → approved → active → closed)
- Cost center management
- Variance report (budget vs actual da journal entries)
- Alert automatici su sforamento soglia
- Time-to-invoice conversion

---

### 15. CalendarService.php — 690 LOC

**Classe**: `CalendarService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private ?EmailQueueService $emailQueue = null,
    private ?NotificationService $notifications = null
)
```

**Metodi pubblici** (15):
- **Eventi**: `createEvent()`, `listByProject()`, `listByUser()`, `updateEvent()`, `deleteEvent()`
- **Unified Calendar**: `unifiedCalendar(int $projectId, string $from, string $to, array $sources): array` — merges events + tasks + invoices + deals
- **Disponibilità**: `setAvailability()`, `getAvailability()`, `getAvailableSlots()`
- **Booking pages**: `setBookingPage()`, `getBookingPage()`, `getBookingPageBySlug()`, `createBooking()`
- **Reminders**: `getUpcomingReminders()`, `markReminderSent()`, `sendPendingReminders()`
- `crossProjectCalendar(int $userId, string $from, string $to, array $sources): array`

**Metodi privati** (2):
- `taskPriorityColor()`, `invoiceStatusColor()`, `dealStageColor()`

**Tabelle DB**: `calendar_events`, `calendar_availability`, `calendar_booking_pages`, `contacts`, `tasks`, `invoices`, `deals`, `auth_users`, `project_members`, `projects`, `recurring_invoices`

**Business logic**:
- Calendario unificato cross-entity (task, fatture, deal, eventi)
- Booking pages pubbliche con slug personalizzabile
- Slot availability con buffer tra appuntamenti
- Reminder automatici via email queue
- Cross-project calendar per utente

---

### 16. AnalyticsService.php — 672 LOC

**Classe**: `AnalyticsService`

**Constructor**: `__construct(private PDO $db)`

**Metodi pubblici** (4):
- `overview(int $uid, string $from, string $to): array`
- `projectAnalytics(int $projectId, int $uid, string $from, string $to): array`
- `trends(int $uid, string $from, string $to, string $granularity): array`
- `exportCsv(int $uid, string $type, string $from, string $to): string`

**Metodi privati** (19):
- `projectIds()`, `inClause()`, `taskMetrics()`, `emptyTaskMetrics()`, `dealMetrics()`, `emptyDealMetrics()`, `invoiceMetrics()`, `fileMetrics()`, `contactMetrics()`, `activityUnionSql()`, `activityUnionParams()`, `activityMetrics()`, `projectTimeline()`, `teamMetrics()`, `taskTrend()`, `dealTrend()`, `activityTrend()`, `fileTrend()`, `expenseMetrics()`, `timeMetrics()`, `taskBurndown()`, `chatResponseMetrics()`

**Tabelle DB**: `projects`, `tasks`, `deals`, `invoices`, `contacts`, `project_files`, `activity_log`, `chat_messages`, `chats`, `calendar_events`, `project_members`, `expenses`, `time_entries`, `auth_users`, `information_schema`

**Business logic**:
- Overview dashboard multi-progetto
- Analytics per progetto con task burndown
- Trend temporali con granularità configurabile (day/week/month)
- Export CSV
- Team productivity metrics

---

### 17. BankReconciliationService.php — 670 LOC

**Classe**: `BankReconciliationService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private JournalEntryService $journalEntryService,
    private ?PaymentService $paymentService = null,
    private ?ExpenseService $expenseService = null
)
```

**Metodi pubblici** (11):
- `autoMatch(int $projectId, int $bankAccountId): array`
- `matchTransaction(...)`, `unmatchTransaction(...)`, `acceptSuggestion(...)`
- `startReconciliation(...)`, `completeReconciliation(...)`, `getReconciliation(...)`, `listReconciliations(...)`
- `getMatchDetails(...)`, `getSummary(...)`

**Metodi privati** (6):
- `findMatch()`, `findPaymentMatches()`, `findInvoiceMatches()`, `findExpenseMatches()`, `handleInvoicePayment()`, `handleExpensePayment()`, `calculateBookBalance()`

**Tabelle DB**: `bank_transactions`, `bank_accounts`, `bank_reconciliations`, `invoices`, `payments`, `payment_allocations`, `expenses`, `expense_categories`, `contacts`, `auth_users`

**Business logic**:
- Auto-matching transazioni bancarie ↔ fatture/pagamenti/spese
- Match per importo + data + reference
- Creazione automatica pagamento su match fattura
- Reconciliation formale con saldo di chiusura
- Book balance calculation

---

### 18. HostingResellerService.php — 651 LOC

**Classe**: `HostingResellerService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private array $hostingerConfig = []
)
```

**Metodi pubblici** (16):
- `listPlans()`, `listAccounts(...)`, `getAccount(...)`
- `provisionAccount(int $userId, int $projectId, string $plan, string $domain): array`
- `changePlan(...)`, `cancelAccount(...)`
- `getPanelOverview(...)`, `changePhpVersion(...)`, `getSslStatus(...)`, `getStorageUsage(...)`
- `createEmailAccount(...)`, `getBillingSummary(...)`, `getAccountBilling(...)`, `getExpiringAccounts(...)`, `renewAccount(...)`, `getHostingChecklist(...)`

**Metodi privati** (3):
- `runProvisioningSteps()`, `listEmailAccounts()`, `formatAccount()`, `getEmailCap()`

**Tabelle DB**: `hosting_accounts`, `hosting_email_accounts`

**Business logic**:
- Hosting reseller (Hostinger white-label)
- Provisioning automatico con SSL auto-config
- Panel overview, PHP version management
- Email hosting con cap per piano

---

### 19. DunningService.php — 610 LOC

**Classe**: `DunningService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private ?StripeService $stripeService = null
)
```

**Metodi pubblici** (7):
- `getFailedPayments(array $filters, int $page, int $perPage): array`
- `getDunningDetail(int $subscriptionId): ?array`
- `getDunningStats(): array`
- `forceRetry(int $subscriptionId, int $adminId): array`
- `extendGrace(int $subscriptionId, int $adminId, int $days): array`
- `cancelSubscription(int $subscriptionId, int $adminId, string $reason): array`
- `getDunningTimeline(int $subscriptionId): array`
- `cancelExpiredGrace(int $adminId): array`

**Metodi privati** (4):
- `enrichDunningRecord()`, `getEscalationEmailsSent()`, `getSubscriptionForAction()`, `logDunningAction()`

**Tabelle DB**: `subscriptions`, `auth_users`, `plans`, `audit_log`

**Business logic**:
- Gestione pagamenti falliti Stripe
- Dunning timeline con escalation
- Force retry / extend grace / cancel
- Admin audit log per tutte le azioni

---

### 20. UsageMeteringService.php — 602 LOC

**Classe**: `UsageMeteringService`

**Constructor**:
```php
__construct(
    PDO $db,
    ?NotificationService $notificationService = null,
    ?EmailQueueService $emailQueue = null,
    ?MailService $mailer = null
)
```

**Metodi pubblici** (8):
- `recordUsage(int $userId, string $metric, int $delta): array`
- `recordUsageForProject(int $projectId, string $metric, int $delta): array`
- `getProjectOwner(int $projectId): int`
- `setAbsoluteValue(...)`, `getAllMetrics(...)`, `getMetricStatus(...)`, `wouldExceedLimit(...)`, `getUpgradeSuggestion(...)`
- `checkAndNotify(...)`, `syncAllMetrics(...)`

**Metodi privati** (10):
- `wasAlreadyNotified()`, `markAsNotified()`, `queueThresholdEmail()`, `getCurrentPeriod()`, `getMaxAllowed()`, `getSubscriptionOverride()`, `calculateLiveValue()`, `countQuery()`, `formatValue()`, `formatBytes()`

**Tabelle DB**: `usage_metering`, `usage_events`, `usage_threshold_alerts`, `subscription_overrides`, `subscriptions`, `plans`, `auth_users`, `projects`, `contacts`, `deals`, `tasks`, `invoices_v2`, `email_campaigns`, `project_files`, `project_members`, `api_keys`, `api_key_usage`, `ai_interactions`

**Business logic**:
- Metering multi-metrica (progetti, contatti, fatture, storage, AI calls, API calls, etc.)
- Soglie notifica a 50%, 75%, 90%, 100%
- Email queue per threshold alerts
- Live value calculation da conteggi effettivi
- Override per abbonamento
- Upgrade suggestion basata su metrica più utilizzata

---

### 21. WebhookService.php — 602 LOC

**Classe**: `WebhookService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private LoggerInterface $logger
)
```

**Metodi pubblici** (12):
- `list(...)`, `get(...)`, `create(...)`, `update(...)`, `delete(...)`, `testPing(...)`
- `dispatch(int $userId, string $eventType, array $eventData, ?int $projectId, ?array $previousData): int`
- `getDeliveries(...)`, `getDeliveryDetail(...)`
- `processRetries(int $batchSize): int`
- `getStats(...)`, `getSupportedEvents()`

**Metodi privati** (6):
- `deliver()`, `sign()`, `generateSecret()`, `validateEvents()`, `createDeliveryLog()`, `updateDeliveryLog()`, `scheduleRetry()`

**Tabelle DB**: `webhooks`, `webhook_deliveries`

**Business logic**:
- HMAC-SHA256 signature verification
- Retry mechanism con backoff
- Delivery log completo (request/response)
- Event subscription filtering

---

### 22. VatReportService.php — 590 LOC

**Classe**: `VatReportService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (18):
- **Strategy**: `registerReporter()`, `getReporter()`, `getReporters()`, `resolveCountry()`
- **Country reports**: `generateCountryReport()`, `exportCountryReport()`, `checkDataReadiness()`, `getReportMetadata()`
- **VAT liquidation**: `calculateVatLiquidation(int $projectId, string $fromDate, string $toDate): array`
- **CRUD**: `listReports()`, `getReport()`, `createReport()`, `fileReport()`, `markPaid()`, `deleteReport()`
- **LIPE**: `generateLipe(int $projectId, int $year, int $quarter): array`
- **Registri**: `vatRegister(int $projectId, string $fromDate, string $toDate, string $registerType): array`
- **Ritenute**: `listWithholdingConfigs()`, `getWithholdingConfig()`, `createWithholdingConfig()`, `updateWithholdingConfig()`, `deleteWithholdingConfig()`
- **Regimi fiscali**: `listTaxRegimes()`, `getActiveRegime()`, `createTaxRegime()`, `updateTaxRegime()`, `deleteTaxRegime()`
- `vatSummary(int $projectId, int $year): array`

**Metodi privati** (2):
- `registerDefaultReporters()`, `getPriorPeriodCredit()`

**Tabelle DB**: `vat_reports`, `accounting_periods`, `journal_entries`, `journal_entry_lines`, `chart_of_accounts`, `tax_codes`, `tax_regime_configs`, `withholding_tax_configs`, `contacts`, `projects`

**Business logic**:
- Strategy Pattern con `VatReportInterface` per country-specific reports
- Reporter registrati: IT (LIPE), DE (Ustva), FR (CA3), ES (Modelo303), GB (MTD)
- VAT liquidation trimestrale
- Registro IVA vendite/acquisti
- File report con workflow (draft → filed → paid)
- Ritenute d'acconto e regimi fiscali

---

### 23. GoogleContactsService.php — 590 LOC

**Classe**: `GoogleContactsService`

**Constructor**:
```php
__construct(
    PDO $db,
    GoogleIntegrationService $googleService,
    LoggerInterface $logger
)
```

**Metodi pubblici** (3):
- `sync(int $userId, int $projectId): array`
- `pushToGoogle(...)`, `pullFromGoogle(...)`

**Metodi privati** (11):
- `processPulledContact()`, `createGoogleContact()`, `updateGoogleContact()`, `localToGoogleFormat()`, `googleToLocalFormat()`, `findLocalByGoogleId()`, `findLocalByEmail()`, `markSynced()`, `createLocalFromGoogle()`, `updateLocalFromGoogle()`, `extractGoogleUpdatedAt()`

**Tabelle DB**: `contacts`

**API esterne**: Google People API (via GoogleIntegrationService)

**Business logic**: Sync bidirezionale contatti locali ↔ Google Contacts con conflict resolution

---

### 24. LimitGraceService.php — 584 LOC

**Classe**: `LimitGraceService`

**Constructor**:
```php
__construct(
    PDO $db,
    NotificationService $notifications,
    EmailQueueService $emailQueue,
    MailService $mail
)
```

**Metodi pubblici** (6):
- `getActiveGrace(...)`, `getAllActiveGracePeriods(...)`, `createGracePeriod(...)`, `resolveGracePeriod(...)`, `autoResolveGracePeriods(...)`, `auditAndCreateGracePeriods(...)`, `processExpiredGracePeriods()`, `sendGraceReminders()`

**Metodi privati** (10):
- `enrichGraceRow()`, `countCurrentUsage()`, `getUserPlanFeatures()`, `getUserGraceDays()`, `sendGraceNotification()`, `sendGraceExpiredNotification()`, `queueGraceStartEmail()`, `queueGraceReminderEmail()`, `queueGraceExpiredEmail()`, `getUserEmailInfo()`

**Tabelle DB**: `limit_grace_periods`, `usage_metering`, `subscriptions`, `plans`, `auth_users`, `projects`, `contacts`, `deals`, `tasks`, `invoices`, `email_campaigns`, `project_files`, `project_members`

**Business logic**:
- Grace period per superamento limiti piano
- Notifiche + email a inizio grace, reminder, scadenza
- Auto-resolve quando usage rientra nei limiti
- Processing cron scadenze

---

### 25. GoogleDriveService.php — 579 LOC

**Classe**: `GoogleDriveService`

**Constructor**:
```php
__construct(
    PDO $db,
    GoogleIntegrationService $googleService,
    LoggerInterface $logger,
    string $storagePath = ''
)
```

**Metodi pubblici** (4):
- `sync(...)`, `pushToDrive(...)`, `pullFromDrive(...)`, `getPreviewUrl(...)`

**Metodi privati** (11):
- `uploadToDrive()`, `simpleUpload()`, `resumableUpload()`, `updateOnDrive()`, `getOrCreateKacinkaFolder()`, `findLocalByExternalId()`, `markFileSynced()`, `createLocalFromDrive()`, `updateLocalFromDrive()`, `resolveLocalPath()`, `isGoogleNativeFormat()`

**Tabelle DB**: `project_files`

**API esterne**: Google Drive API v3 (`googleapis.com/drive/v3`, `googleapis.com/upload/drive/v3`)

**Business logic**:
- Sync bidirezionale file ↔ Google Drive
- Simple upload (<5MB) e resumable upload (>5MB)
- Auto-creazione folder "Kacinka" su Drive
- Preview URL per file Google nativi (Docs, Sheets, etc.)

---

### 26. FinancialReportService.php — 530 LOC

**Classe**: `FinancialReportService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (8):
- `trialBalance(int $projectId, ?string $fromDate, ?string $toDate, array $opts): array`
- `profitAndLoss(int $projectId, string $fromDate, string $toDate, array $opts): array`
- `balanceSheet(int $projectId, string $asOfDate, array $opts): array`
- `cashFlowStatement(int $projectId, string $fromDate, string $toDate): array`
- `generalLedger(int $projectId, int $accountId, ...): array`
- `accountStatement(int $projectId, int $contactId, ...): array`
- `periodBreakdown(int $projectId, string $fromDate, string $toDate, string $granularity): array`
- `agedReport(int $projectId, string $type, string $asOfDate): array`
- `financialSnapshot(int $projectId, string $fromDate, string $toDate): array`

**Metodi privati** (3):
- `calculateRetainedEarnings()`, `isOperatingItem()`, `isInvestingItem()`

**Tabelle DB**: `chart_of_accounts`, `journal_entries`, `journal_entry_lines`, `contacts`, `invoices`

**Business logic**:
- Report finanziari completi: bilancio verifica, C/E, stato patrimoniale, cash flow
- Metodo indiretto per cash flow statement
- Aged receivables/payables (0-30, 31-60, 61-90, 90+)
- General ledger per conto
- Statement per contatto (estratto conto cliente/fornitore)
- Period breakdown con granularità mensile/trimestrale

---

### 27. OssTrackingService.php — 510 LOC

**Classe**: `OssTrackingService`

**Constructor**: `__construct(PDO $db)`

**Metodi pubblici** (11):
- `getApplicableVatRate(string $buyerCountry, bool $hasValidVat): array`
- `getBuyerCountry(int $userId): string`
- `recordSale(string $buyerCountry, int $taxableCents, int $vatCents, float $vatRate, string $period): void`
- `isThresholdExceeded(int $year): bool`
- `getAnnualB2cTotal(int $year): int`, `getThresholdStatus(...)`, `getQuarterlyDeclaration(...)`, `getCountryBreakdown(...)`
- `isEuCountry(string $countryCode): bool`
- `validateVatNumber(int $userId): bool`
- `resolveInvoiceFormat(...)`, `resolveGatewayType(...)`

**Metodi privati** (3):
- `updateThreshold()`, `updateCountryTotals()`, `periodToQuarter()`

**Tabelle DB**: `oss_vat_tracking`, `oss_thresholds`, `oss_country_totals`, `auth_users`

**Business logic**:
- EU OSS (One Stop Shop) regime €10.000 threshold
- Country-specific VAT rates per B2C cross-border
- VAT number validation
- Quarterly declaration generation
- Country breakdown per anno

---

### 28. TaxService.php — 508 LOC

**Classe**: `TaxService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (16):
- **Tax Codes CRUD**: `listTaxCodes()`, `getTaxCode()`, `createTaxCode()`, `updateTaxCode()`, `deleteTaxCode()`
- `calculateLineTax(...)`, `calculateInvoiceTotals(...)`
- **Withholding**: `listWithholdingConfigs()`, `getWithholdingConfig()`, `createWithholdingConfig()`, `updateWithholdingConfig()`
- **Tax Regime**: `getTaxRegime()`, `setTaxRegime()`
- **Import defaults**: `importDefaults(string $countryCode, int $projectId): int`
- Country-specific imports: `importItalianDefaults()`, `importGermanDefaults()`, `importFrenchDefaults()`, `importSpanishDefaults()`, `importUKDefaults()`, `importUSDefaults()`, `importDutchDefaults()`, `importAustrianDefaults()`, `importBelgianDefaults()`, `importPortugueseDefaults()`, `importPolishDefaults()`, `importGenericEUDefaults()`

**Tabelle DB**: `tax_codes`, `tax_regime_configs`, `withholding_tax_configs`

**Business logic**:
- Calcolo imposte per riga e totali fattura
- Seed tax codes per 12 paesi
- Ritenuta d'acconto configurabile
- Regime fiscale (ordinario, forfettario, etc.)

---

### 29. CountryTaxProfileService.php — 506 LOC

**Classe**: `CountryTaxProfileService`

**Constructor**:
```php
__construct(
    private \PDO $pdo,
    private CountryTaxProfileSeeder $seeder,
    private ?TaxService $taxService = null,
    private ?InvoiceNumberingService $numberingService = null
)
```

**Metodi pubblici** (18):
- `getProfile(...)`, `listProfiles()`, `getVatRates(...)`, `validateVatNumber(...)`, `validateFiscalCode(...)`
- `getRequiredFields(...)`, `getEInvoiceFormat(...)`, `getVatReportingFormat(...)`, `isEInvoiceMandatory(...)`, `getEInvoiceGateway(...)`, `isEuCountry(...)`
- `getTaxRegimes(...)`, `getProjectCountry(...)`, `setProjectCountry(...)`, `setUserCountry(...)`
- `importDefaults(string $countryCode, int $projectId): array`
- `setProjectFiscalYear(...)`, `saveFiscalData(...)`, `getFiscalData(...)`, `seedProfiles()`, `getSupportedCountries()`

**Tabelle DB**: `country_tax_profiles`, `auth_users`, `projects`, `fiscal_years`

**Business logic**:
- Profili fiscali per 11+ paesi
- Validazione P.IVA e codice fiscale country-aware
- Mapping paese → formato e-invoice e gateway
- Import default automatico (tax codes, numerazione, piano conti)

---

### 30. PlanService.php — 503 LOC

**Classe**: `PlanService`

**Constructor**: `__construct(PDO $db)`

**Metodi pubblici** (14):
- `listPlans()`, `getPlanBySlug(...)`, `getUpgradePaths(...)`, `validatePlanChange(...)`
- `calculateProration(int $userId): array`
- `isInGracePeriod(int $userId): array`, `startGracePeriod(...)`, `processExpiredGracePeriods(): int`
- `downgradeToFree(int $userId, ?int $oldSubId): void`
- `getUserLimits(int $userId): array`, `getFeatureComparison()`, `recommendPlan(int $userId): array`
- `getTestimonials()`, `getPlatformStats()`, `getActiveSubscription(...)`

**Metodi privati** (1): `syncPlanUsage()`

**Tabelle DB**: `plans`, `subscriptions`, `plan_usage`, `entitlements`, `upgrade_paths`, `platform_stats`, `testimonials`, `audit_log`, `projects`, `project_members`

**Business logic**:
- Gestione piani (starter, pro, pro_plus, elite, enterprise)
- Proration calculation per upgrade/downgrade
- Grace period 14 giorni pre-downgrade
- Plan recommendation basata su usage
- Feature comparison matrix

---

### 31. FormService.php — 493 LOC

**Classe**: `FormService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (17):
- `list(...)`, `get(...)`, `getBySlug(...)`, `create(...)`, `update(...)`, `delete(...)`, `duplicate(...)`
- `submit(string $slug, array $formData, ?string $ip): array`
- `listSubmissions(...)`, `deleteSubmission(...)`, `getStats(...)`, `getOverviewStats(...)`
- `evaluateFieldVisibility(...)`, `saveDraft(...)`, `getDraft(...)`
- `createDealFromSubmission(...)`, `getEmbedCode(...)`

**Metodi privati** (3): `createContactFromSubmission()`, `generateSlug()`, `defaultFields()`

**Tabelle DB**: `forms`, `standalone_form_submissions`, `contacts`, `deals`, `deal_pipelines`, `deal_stages`

**Business logic**:
- Form builder con campi dinamici e conditional visibility
- Public form via slug
- Auto-creazione contatto e deal da submission
- Multi-step forms con draft saving
- Embed code generation

---

### 32. ApiKeyService.php — 491 LOC

**Classe**: `ApiKeyService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private LoggerInterface $logger
)
```

**Metodi pubblici** (14):
- `list(...)`, `get(...)`, `create(...)`, `update(...)`, `revoke(...)`, `delete(...)`, `rotate(...)`
- `authenticate(string $apiKey, string $clientIp): ?array`
- `hasScope(array $key, string $scope): bool`
- `checkRateLimit(int $keyId, int $limitPerHour, int $burstPerMinute): array`
- `trackUsage(...)`, `getUsageStats(...)`, `getStats(...)`
- `cleanupRateLimits()`, `cleanupUsage(...)`, `checkMonthlyPlanLimit(...)`

**Metodi privati** (3): `incrementWindow()`, `resolveProjectOwner()`, `getMonthlyApiLimit()`

**Tabelle DB**: `api_keys`, `api_key_usage`, `api_key_rate_limits`, `plans`, `subscriptions`, `projects`

**Business logic**:
- API key con scope-based access control
- Rate limiting (hourly + burst per minute)
- Monthly plan limit enforcement
- Key rotation senza downtime
- Usage tracking e stats

---

### 33. ReferralService.php — 481 LOC

**Classe**: `ReferralService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private NotificationService $notifSvc
)
```

**Metodi pubblici** (5):
- `getActiveCampaign()`, `getOrCreateCode(int $userId): ?array`, `validateCode(...)`, `registerReferral(...)`, `qualifyReferral(...)`, `getReferralStats(...)`

**Metodi privati** (5): `generateUniqueCode()`, `createReferralCoupon()`, `applyReferrerStripeCoupon()`, `checkTierUpgrade()`, `getUserName()`

**Tabelle DB**: `referral_campaigns`, `referral_codes`, `referrals`, `coupons`, `auth_users`, `subscriptions`

**Business logic**:
- Campagne referral con tier di ricompensa
- Codice referral unico per utente
- Qualification automatica su primo pagamento
- Coupon creazione per referrer e referee
- Tier upgrade (es. 5 referral = sconto maggiore)

---

### 34. BankImportService.php — 477 LOC

**Classe**: `BankImportService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private BankAccountService $bankAccountService
)
```

**Metodi pubblici** (3):
- `import(int $projectId, int $bankAccountId, string $content, string $format): array`
- `getImportHistory(...)`, `deleteImportBatch(...)`

**Metodi privati** (6): `parseOFX()`, `parseMT940()`, `parseCSV()`, `parseCAMT053()`, `isDuplicate()`, `findColumn()`, `parseDate()`, `parseAmount()`

**Tabelle DB**: `bank_transactions`, `bank_accounts`

**Business logic**:
- Import multi-formato: OFX, MT940, CSV, CAMT.053
- Deduplication transazioni
- Batch tracking per undo import

---

### 35. FiscalYearService.php — 457 LOC

**Classe**: `FiscalYearService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (15):
- CRUD: `listFiscalYears()`, `getFiscalYear()`, `createFiscalYear()`, `updateFiscalYear()`, `deleteFiscalYear()`
- Workflow: `lockFiscalYear()`, `closeFiscalYear()`, `reopenFiscalYear()`
- Periodi: `listPeriods()`, `getPeriod()`, `closePeriod()`, `reopenPeriod()`, `listPeriodsByFiscalYearIds()`
- `getCurrentFiscalYear(...)`, `getCurrentPeriod(...)`, `getLocalizedMonthNames(...)`

**Metodi privati** (4): `assertNoOverlap()`, `generatePeriods()`, `updateStatus()`, `getProjectCountry()`

**Tabelle DB**: `fiscal_years`, `accounting_periods`, `journal_entries`, `projects`

**Business logic**:
- Anni fiscali con workflow (open → locked → closed)
- Auto-generazione periodi contabili (mensili/trimestrali)
- Validazione non-sovrapposizione periodi
- Periodi country-aware (mesi localizzati)

---

### 36. CustomDomainService.php — 456 LOC

**Classe**: `CustomDomainService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (13):
- `listDomains(...)`, `getDomain(...)`, `resolveByDomain(...)`, `addDomain(...)`, `removeDomain(...)`
- `verifyDomain(...)`, `verifyPendingDomains()`, `provisionSsl(...)`, `getSslStatus(...)`, `checkSslStatuses()`
- `getSeoConfig(...)`, `getDomainChecklist(...)`, `hasFeature(...)`

**Metodi privati** (8): `getDomainRow()`, `formatRow()`, `getDnsRecords()`, `checkTxtRecord()`, `checkDnsRecords()`, `checkSslActive()`, `getTargetMeta()`, `generateRobotsTxt()`

**Tabelle DB**: `custom_domains`, `addons`, `plans`, `subscriptions`, `client_portal_config`, `sites`, `user_addons`

**Business logic**:
- Custom domain per client portal / site builder
- DNS verification (TXT record)
- SSL provisioning check
- SEO config per dominio (robots.txt auto-generato)

---

### 37. JobQueueService.php — 454 LOC

**Classe**: `JobQueueService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private ?LoggerInterface $logger = null
)
```

**Metodi pubblici** (13):
- `push(string $queue, string $jobType, array $payload, ...): int`
- `pop(string|array $queues): ?array`
- `markRunning(...)`, `complete(...)`, `fail(...)`, `retry(...)`, `delete(...)`, `purge(...)`
- `releaseStale(int $timeoutMinutes): int`
- `stats()`, `list(...)`, `listFailed(...)`, `size(...)`

**Metodi privati** (2): `ensureTable()`, `log()`

**Tabelle DB**: `jobs`, `failed_jobs`

**Business logic**:
- Job queue database-backed con priorità
- Retry con max attempts
- Stale job release dopo timeout
- Failed jobs tracking separato
- Auto-migrate (ensureTable)

---

### 38. GoogleIntegrationService.php — 451 LOC

**Classe**: `GoogleIntegrationService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private LoggerInterface $logger,
    array $googleConfig
)
```

**Metodi pubblici** (8):
- `getAuthUrl(array $features, string $state): string`
- `exchangeAndStore(string $code, int $userId, ?int $projectId, array $requestedFeatures): array`
- `getAccessToken(int $userId, ?int $projectId): ?string`
- `getStatus(...)`, `disconnect(...)`, `hasFeature(...)`
- `apiRequest(int $userId, ?int $projectId, string $method, string $url, ?array $body, array $headers): ?array`
- `logSync(...)`

**Metodi privati** (5): `getIntegration()`, `exchangeCode()`, `refreshToken()`, `fetchUserInfo()`, `revokeToken()`

**Tabelle DB**: `user_integrations`, `google_sync_log`

**API esterne**:
- Google OAuth2 (`accounts.google.com`, `oauth2.googleapis.com`)
- Google UserInfo (`googleapis.com/oauth2/v3/userinfo`)
- Scopes: calendar, contacts, drive.file, spreadsheets

**Business logic**:
- OAuth2 flow centralizzato per tutte le integrazioni Google
- Feature-based scope selection (calendar, contacts, drive, sheets)
- Token refresh automatico
- Sync logging per audit
- API request wrapper con auth auto-injection

---

### 39. DomainRegistrarService.php — 435 LOC

**Classe**: `DomainRegistrarService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private array $registrarConfig = []
)
```

**Metodi pubblici** (14):
- `searchDomain(...)`, `registerDomain(...)`, `listRegistrations(...)`, `getRegistration(...)`
- `toggleAutoRenew(...)`, `initiateTransfer(...)`, `getTransferStatus(...)`, `updateTransferStatus(...)`
- **DNS**: `listDnsRecords()`, `addDnsRecord()`, `deleteDnsRecord()`, `autoConfigureDns()`
- **Email**: `getEmailHostingStatus()`, `createMailbox()`, `getExpiringDomains()`

**Metodi privati** (4): `formatRegistration()`, `getDomainPrice()`, `hasEmailHosting()`, `listMailboxes()`

**Tabelle DB**: `domain_registrations`, `domain_dns_records`, `domain_mailboxes`, `addons`, `plans`, `subscriptions`, `user_addons`

**Business logic**:
- Domain registration e transfer
- DNS record management
- Auto-configure DNS per siti Kacinka
- Email hosting per dominio
- Domain expiration tracking

---

### 40. CountryTaxProfileSeeder.php — 418 LOC

**Classe**: `CountryTaxProfileSeeder`

**Constructor**: `__construct(private \PDO $pdo)`

**Metodi pubblici** (4):
- `seed(): int`, `getProfiles()`, `getProfileData(string $countryCode): ?array`, `getSupportedCountries()`

**Tabelle DB**: `country_tax_profiles`

**Business logic**: Seed profili fiscali per 11 paesi prioritari con aliquote IVA, formati e-invoice, gateway, campi obbligatori

---

### 41. InvoiceNumberingService.php — 417 LOC

**Classe**: `InvoiceNumberingService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (9):
- `getNextNumber(int $projectId, int $fiscalYearId, string $sequenceType): string`
- `validateNumber(...)`, `configureSequence(...)`, `listSequences(...)`, `configureForCountry(...)`
- `getCountryDefaults(...)`, `isGapCheckRequired(...)`, `validateSequenceIntegrity(...)`, `previewNextNumber(...)`, `getSupportedCountries()`

**Metodi privati** (3): `getSequencePrefix()`, `extractNumericPart()`, `formatNumber()`

**Tabelle DB**: `invoice_sequences`, `invoices`, `fiscal_years`

**Business logic**:
- Numerazione progressiva thread-safe (SELECT FOR UPDATE)
- Pattern country-specific (IT: FT-YYYY-NNNNN, DE: RE-YYYY-NNNNN, etc.)
- Gap check per paesi che lo richiedono (IT, FR)
- Sequence integrity validation

---

### 42. AdminAnalyticsService.php — 412 LOC

**Classe**: `AdminAnalyticsService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (5):
- `getConversionFunnel(...)`, `getFeatureAdoption(...)`, `getCohortRetention(...)`, `getChurnScores(...)`, `getUserChurnScore(...)`

**Metodi privati** (1): `calculateChurnScore()`

**Tabelle DB**: `auth_users`, `subscriptions`, `plans`, `projects`, `activity_log`, `login_history`, `invoices`, `user_feedback`

**Business logic**:
- Funnel conversione (registration → project → subscription)
- Feature adoption tracking
- Cohort retention (12 mesi)
- Churn score predittivo per utente

---

### 43. InvoicePdfService.php — 404 LOC

**Classe**: `InvoicePdfService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private InvoiceServiceV2 $invoiceService
)
```

**Metodi pubblici** (4): `generateHtml(...)`, `generatePdf(...)`, `generateBatch(...)`, `preview(...)`

**Metodi privati** (8): `buildTemplateVars()`, `renderDefaultTemplate()`, `renderTemplate()`, `typeLabel()`, `getProjectInfo()`, `updatePdfUrl()`, `sanitizeFilename()`, `getOwnerBranding()`, `renderLogoHtml()`, `findExecutable()`

**Tabelle DB**: `invoices`, `invoice_templates`, `projects`, `agency_branding`

**Business logic**:
- HTML → PDF generation (wkhtmltopdf / chrome headless)
- Template personalizzabili con variabili
- Batch generation
- White-label branding applicato

---

### 44. CrmAccountingBridge.php — 398 LOC

**Classe**: `CrmAccountingBridge`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (7):
- `linkContact(...)`, `unlinkContact(...)`, `getLink(...)`, `listLinkedContacts(...)`
- `getContactAccounting(int $projectId, int $contactId): array`
- `overdueContacts(...)`, `generateInvoiceFromDeal(...)`, `onDealWon(...)`, `suggestLinks(...)`

**Tabelle DB**: `crm_accounting_links`, `contacts`, `deals`, `invoices`, `invoice_line_items`, `payments`, `payment_allocations`

**Business logic**:
- Ponte CRM ↔ Contabilità
- Auto-fatturazione su deal won
- Suggerimento link contatti CRM → accounting
- Vista consolidated fatture+pagamenti per contatto

---

### 45. BrandingService.php — 395 LOC

**Classe**: `BrandingService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (10):
- `get(...)`, `save(...)`, `resolveTheme(...)`, `resolveOwnerBranding(...)`, `setBrandOwner(...)`
- `getTeamMembers(...)`, `getLoginBranding(...)`, `listDomains(...)`, `addDomain(...)`, `verifyDomain(...)`, `removeDomain(...)`, `hasWhiteLabel(...)`

**Metodi privati** (3): `defaultLoginBranding()`, `getRaw()`, `defaultBranding()`, `validateThemeJson()`

**Tabelle DB**: `agency_branding`, `auth_users`, `custom_domains`, `projects`, `plans`, `subscriptions`

**Business logic**:
- White-label branding per agency partners
- Custom theme (colors, logo, favicon)
- Login page personalizzata per dominio
- Brand propagation ai team members

---

### 46. AiSuggestionService.php — 375 LOC

**Classe**: `AiSuggestionService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private array $geminiConfig,
    private LoggerInterface $logger
)
```

**Metodi pubblici** (5):
- `suggest(int $projectId, int $userId, string $module, string $action, array $context): ?array`
- `suggestDealFollowUp(...)`, `categorizeExpense(...)`, `suggestInvoiceItems(...)`, `estimateTaskTime(...)`

**Metodi privati** (5): `getSystemPrompt()`, `buildPrompt()`, `callGemini()`, `hasAiAccess()`, `recordUsage()`

**Tabelle DB**: `ai_interactions`, `auth_users`, `subscriptions`, `plans`, `projects`, `contacts`, `deals`, `invoices`, `expenses`, `expense_categories`, `tasks`, `time_entries`, `notes`

**API esterne**: Google Gemini (`generativelanguage.googleapis.com/v1beta/models/{model}:generateContent`)

**Business logic**:
- AI suggestions contestuali per modulo (deal follow-up, categorizzazione spese, stima tempo task)
- System prompt module-specific
- Usage tracking per piano

---

### 47. SiteBuilderService.php — 367 LOC

**Classe**: `SiteBuilderService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (8):
- `listSites(...)`, `getSite(...)`, `createSite(...)`, `updateSite(...)`, `deleteSite(...)`, `duplicateSite(...)`, `togglePublish(...)`, `listSubmissions(...)`, `getOverviewStats(...)`

**Tabelle DB**: `user_sites`, `site_templates`, `site_forms`, `site_page_view_daily`, `form_submissions`, `agency_branding`, `contacts`, `projects`

**Business logic**: Site builder con template, pubblicazione, form submissions, page view analytics

---

### 48. GoogleSheetsService.php — 366 LOC

**Classe**: `GoogleSheetsService`

**Constructor**:
```php
__construct(
    PDO $db,
    GoogleIntegrationService $googleService,
    LoggerInterface $logger
)
```

**Metodi pubblici** (2): `export(int $userId, int $projectId, string $entity): array`, `listTemplates()`

**Metodi privati** (8): `createSpreadsheet()`, `writeData()`, `formatSheet()`, `fetchEntityData()`, `fetchInvoices()`, `fetchContacts()`, `fetchExpenses()`, `fetchTasks()`, `formatRow()`, `formatCents()`

**Tabelle DB**: `auth_users`, `contacts`, `expenses`, `project_invoices`, `tasks`, `task_lists`

**API esterne**: Google Sheets API (via GoogleIntegrationService)

**Business logic**: Export entità (fatture, contatti, spese, task) come Google Spreadsheet

---

### 49. OcrService.php — 355 LOC

**Classe**: `OcrService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    array $ocrSettings
)
```

**Metodi pubblici** (7):
- `createReceipt(...)`, `processReceipt(...)`, `linkToExpense(...)`, `batchProcess(...)`, `listReceipts(...)`, `getReceipt(...)`, `deleteReceipt(...)`, `reviewReceipt(...)`, `getOverviewStats(...)`

**Metodi privati** (5): `callOcrApi()`, `callMindee()`, `callVeryfi()`, `parseOcrResult()`, `mockOcrResult()`, `updateStatus()`

**Tabelle DB**: `ocr_receipts`, `expenses`

**API esterne**:
- Mindee (`api.mindee.net/v1`)
- Veryfi (`api.veryfi.com/api/v8/partner/documents/`)

**Business logic**:
- OCR multi-provider (Mindee primary, Veryfi fallback)
- Batch processing
- Link ricevuta → spesa con dati estratti
- Review workflow per correzioni manuali

---

### 50. TimeTrackingService.php — 348 LOC

**Classe**: `TimeTrackingService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private ?InvoiceService $invoiceService = null
)
```

**Metodi pubblici** (8):
- `list(...)`, `create(...)`, `update(...)`, `delete(...)`
- `setRateCard(...)`, `getRateCards(...)`, `getEffectiveRate(...)`
- `getBillableSummary(...)`, `generateInvoiceFromEntries(...)`

**Tabelle DB**: `time_entries`, `rate_cards`, `tasks`, `auth_users`, `project_settings`

**Business logic**:
- Time tracking con task association
- Rate cards per utente/progetto/task
- Effective rate calculation gerarchica
- Billable summary e auto-generazione fattura da time entries

---

### 51–56. EInvoice Builders

#### PeppolBIS3Builder.php — 342 LOC
- Formato: PEPPOL BIS Billing 3.0 (UBL 2.1)
- Metodi: `generate()`, `validate()`, `supports()`, `getFormat()`, `getMimeType()`, `getFilename()`
- Metodi protected: `getCustomizationId()`, `addAdditionalDocumentReferences()`, `buildSupplierParty()`, `buildCustomerParty()`, `buildPaymentMeans()`, `buildTaxTotal()`, `buildMonetaryTotal()`, `buildLines()`, `addCbc()`, `addCac()`, `sanitizeXml()`

#### FacturXBuilder.php — 282 LOC
- Formato: Factur-X / ZUGFeRD (CII / EN16931)
- Profile configurabile (MINIMUM, BASIC, EN16931)
- Metodi: `generate()`, `validate()`, party builder, line item builder

#### FatturaPABuilder.php — 270 LOC
- Formato: FatturaPA 1.2.2 (Italia SDI)
- DOM-based XML construction
- Metodi: `generate()`, `validate()`, `addElement()`, `sanitizeXml()`

#### XRechnungBuilder.php — 128 LOC
- Formato: XRechnung 3.0 (Germania, estende PeppolBIS3Builder)
- CustomizationID: `urn:cen.eu:en16931:2017#compliant#urn:xoev-de:kosit:standard:xrechnung_3.0`
- Override: `getCustomizationId()`, `buildPaymentMeans()`

#### ChorusProBuilder.php — 171 LOC
- Formato: Chorus Pro (Francia B2G, basato su Factur-X)
- Aggiunge metadata specifici Chorus Pro (service code, engagement)

#### EInvoiceBuilderInterface.php — 55 LOC
- Interface: `supports()`, `generate()`, `validate()`, `getFilename()`, `getMimeType()`, `getFormat()`

---

### 57–62. VatReport Implementations

#### ItalianLipeReport.php — 312 LOC
- Report: Liquidazione Periodica IVA (LIPE) Italia
- Export: JSON, XML (formato AdE), CSV
- Business logic: calcolo liquidazione trimestrale, interessi 1% per ritardato versamento, credito periodo precedente

#### SpanishModelo303Report.php — 310 LOC
- Report: Modelo 303 (IVA trimestrale Spagna)
- Export: JSON, XML (formato AEAT), CSV
- Business logic: mapping casillas, compensazione periodo precedente

#### FrenchCA3Report.php — 295 LOC
- Report: CA3 / 3310 (TVA Francia)
- Export: JSON, XML, CSV
- Business logic: mapping lignes CA3, crédit de TVA période précédente

#### GermanUstvaReport.php — 284 LOC
- Report: Umsatzsteuervoranmeldung (UStVA Germania)
- Export: JSON, Elster XML, CSV
- Business logic: mapping Kennziffer, Elster-compliant XML

#### UkMtdReport.php — 276 LOC
- Report: Making Tax Digital VAT Return (UK)
- Export: JSON, XML, CSV
- Business logic: 9-Box format (standard HMRC), period key generation

#### VatReportInterface.php — 56 LOC
- Interface: `supports()`, `generate()`, `getRequiredData()`, `export()`, `getReportName()`, `getFrequency()`, `getCountryCode()`

---

### 63. MailService.php — 340 LOC

**Classe**: `MailService`

**Constructor**:
```php
__construct(
    array $mailConfig,
    string $frontendUrl = 'https://app.kacinka.it',
    array $companyInfo = []
)
```

**Metodi pubblici** (4):
- `buildBrandedHtml(string $to, string $subject, string $title, string $bodyHtml, ?string $ctaUrl, ?string $ctaText, array $brand): string`
- `sendBrandedEmail(string $to, string $subject, string $title, string $bodyHtml, ?string $ctaUrl, ?string $ctaText, array $brand): bool`
- `sendPurchaseReceipt(string $to, string $userName, array $items, int $totalCents, string $currency, ?string $invoiceNumber): bool`
- `sendPasswordReset(string $to, string $token): bool`

**Business logic**: SMTP email sending con branded HTML template, purchase receipt, password reset

---

### 64. GoogleCalendarService.php — 340 LOC

**Classe**: `GoogleCalendarService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private GoogleIntegrationService $googleService,
    private LoggerInterface $logger
)
```

**Metodi pubblici** (1): `sync(int $userId, int $projectId): array`

**Metodi privati** (7): `pushToGoogle()`, `pullFromGoogle()`, `createGoogleEvent()`, `updateGoogleEvent()`, `localToGoogleFormat()`, `googleToLocalFormat()`, `createLocalFromGoogle()`, `updateLocalFromGoogle()`

**Tabelle DB**: `calendar_events`

**API esterne**: Google Calendar API (via GoogleIntegrationService)

**Business logic**: Sync bidirezionale eventi ↔ Google Calendar

---

### 65. CurrencyService.php — 327 LOC

**Classe**: `CurrencyService`

**Constructor**: `__construct(PDO $pdo)`

**Metodi pubblici** (10):
- CRUD: `listCurrencies()`, `getCurrency()`, `createCurrency()`, `updateCurrency()`, `deleteCurrency()`
- `getRate(...)`, `convert(...)`, `listRates(...)`, `saveRate(...)`
- `fetchECBRates()`, `fetchECBHistoricalRates()`
- `calculateFxGainLoss(...)`, `revalueOpenItems(...)`, `getStats()`

**Tabelle DB**: `currencies`, `exchange_rates`, `invoices`

**API esterne**: ECB (European Central Bank) — tassi di cambio

**Business logic**:
- Multi-valuta con tassi ECB automatici
- Conversione con rate per data
- FX gain/loss calculation
- Revaluation open items a fine periodo

---

### 66. BankAccountService.php — 324 LOC

**Classe**: `BankAccountService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (12):
- `list(...)`, `get(...)`, `create(...)`, `update(...)`, `deactivate(...)`, `delete(...)`
- `listTransactions(...)`, `countTransactions(...)`, `getTransaction(...)`, `createTransaction(...)`, `excludeTransaction(...)`, `restoreTransaction(...)`
- `updateAccountBalance(...)`, `getAccountSummary(...)`

**Tabelle DB**: `bank_accounts`, `bank_transactions`, `chart_of_accounts`

**Business logic**: Conti bancari con transazioni, saldo aggiornato, link a piano dei conti

---

### 67. ClientPortalService.php — 313 LOC

**Classe**: `ClientPortalService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private MailService $mail
)
```

**Metodi pubblici** (11):
- `getConfig(...)`, `updateConfig(...)`, `sendMagicLink(...)`, `validateSession(...)`
- `clientInvoices(...)`, `clientInvoice(...)`, `listMessages(...)`, `sendMessage(...)`, `markMessagesRead(...)`, `unreadCount(...)`
- `listDocuments(...)`, `shareDocument(...)`, `deleteDocument(...)`

**Tabelle DB**: `client_portal_config`, `client_portal_tokens`, `client_portal_sessions`, `client_portal_documents`, `client_portal_messages`, `invoices`, `contacts`, `projects`, `agency_branding`

**Business logic**:
- Portale clienti con magic link auth
- Vista fatture per il cliente
- Messaggistica bidirezionale (accountant ↔ client)
- Condivisione documenti sicura

---

### 68. DashboardMetricsService.php — 310 LOC

**Classe**: `DashboardMetricsService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (11):
- `getDashboard(...)`, `revenueMetrics(...)`, `expenseMetrics(...)`, `profitabilityMetrics(...)`, `cashMetrics(...)`, `receivablesMetrics(...)`, `payablesMetrics(...)`, `vatMetrics(...)`, `invoicingMetrics(...)`, `expensesPendingMetrics(...)`, `bankMetrics(...)`, `topAccounts(...)`

**Tabelle DB**: `invoices`, `expenses`, `bank_accounts`, `bank_transactions`, `chart_of_accounts`, `journal_entries`, `journal_entry_lines`, `vat_reports`

**Business logic**: Dashboard KPI contabili in real-time (revenue, expense, profitability, cash, receivables, payables, VAT)

---

### 69. VerificationService.php — 305 LOC

**Classe**: `VerificationService`

**Constructor**:
```php
__construct(
    private PDO $pdo,
    private array $mailSettings,
    private string $frontendUrl = 'https://app.kacinka.it'
)
```

**Metodi pubblici** (7):
- `sendEmailOtp(...)`, `verifyEmailOtp(...)`, `consumeEmailOtpToken(...)`
- `sendEmailVerification(...)`, `verifyEmailToken(...)`
- `sendPhoneVerification(...)`, `verifyPhoneCode(...)`, `getVerificationStatus(...)`

**Tabelle DB**: `email_verification_codes`, `email_verification_tokens`, `phone_verification_codes`, `auth_users`

**Business logic**: Email OTP (pre-registration), email verification (post-registration), phone verification

---

### 70. PermissionService.php — 299 LOC

**Classe**: `PermissionService`

**Constructor**: `__construct(private PDO $pdo)`

**Metodi pubblici** (14):
- `listRoles()`, `getRole(...)`, `listPermissions()`, `allPermissionSlugs()`
- `getRolePermissions(...)`, `getMemberPermissions(...)`, `can(...)`, `canAny(...)`, `outranks(...)`
- `setMemberOverrides(...)`, `isPlatformAdmin(...)`, `isSuperAdmin(...)`, `roleHierarchy(...)`, `isValidRole(...)`, `isValidPermission(...)`, `buildPermissionsPayload(...)`

**Tabelle DB**: `roles`, `permissions`, `role_permissions`, `project_members`, `auth_users`, `projects`

**Business logic**:
- RBAC (Role-Based Access Control) con override per membro
- Gerarchia ruoli: owner > admin > editor > viewer
- Permission check con grants/revokes

---

### 71–103. Servizi Restanti (< 300 LOC)

#### TwoFactorService.php — 273 LOC
- 2FA TOTP con backup codes
- AES-256-CBC encryption per TOTP secret
- Tabelle: `two_factor_auth`, `auth_users`

#### FileValidationService.php — 272 LOC
- Validazione file upload (extension, MIME, size, magic bytes)
- Validazione immagini con SVG sanitization
- Nessuna dipendenza DB

#### EmailQueueService.php — 256 LOC
- Constructor: `PDO + MailService + ?EmailConfigService`
- Email queue con scheduling e template
- Tabelle: `email_queue`, `email_templates`

#### PaymentScheduleService.php — 254 LOC
- Piani di pagamento rateali
- Tabelle: `payment_schedules`, `invoices`, `payments`, `contacts`

#### ExpenseCategoryService.php — 241 LOC
- Categorie spese gerarchiche (tree)
- Seed defaults per progetto
- Tabelle: `expense_categories`, `expenses`, `chart_of_accounts`

#### B2BackupService.php — 236 LOC
- Constructor: `array $b2Settings, ?LoggerInterface`
- Backup su Backblaze B2
- API: `api.backblazeb2.com/b2api/v2/b2_authorize_account`

#### AiEscalationService.php — 227 LOC
- Constructor: `PDO + NotificationService + LoggerInterface + ?TelegramService`
- Escalation AI → admin umano
- Tabelle: `ai_escalations`, `ai_interactions`, `auth_users`, `chats`

#### StorageService.php — 224 LOC
- Calcolo usage storage (file + DB)
- Tabelle: `project_files`, `projects`, `subscriptions`, `plans`, `system_settings` + molte per dimensione DB

#### AdminDashboardService.php — 223 LOC
- KPI admin: user growth, revenue trend, plan distribution, conversion funnel, cohort data
- Tabelle: `auth_users`, `subscriptions`, `plans`, `projects`, `contacts`, `deals`, `invoices`, `login_history`, `revenue_snapshots`

#### SessionService.php — 215 LOC
- Gestione sessioni con refresh token rotation
- Grace period per token invalidation
- Tabelle: `user_sessions`

#### FileService.php — 209 LOC
- Constructor: `PDO + string $storagePath`
- Upload, folder management, file categorization
- Tabelle: `project_files`, `file_folders`, `auth_users`

#### TeamService.php — 208 LOC
- Invite, accept, update role, remove member
- Tabelle: `project_members`, `projects`, `auth_users`

#### AdminSystemService.php — 206 LOC
- Health report, DB stats, PHP info, disk usage, session stats, performance metrics
- Tabelle: `audit_log`, `auth_users`, `login_history`, `information_schema`, `system_health_snapshots`

#### OAuthService.php — 172 LOC
- Google OAuth + Apple OAuth login
- API: Google OAuth2, Apple Auth
- Tabelle: `auth_users`

#### ChorusProBuilder.php — 171 LOC
(vedi sezione EInvoice Builders sopra)

#### UserBootstrapService.php — 160 LOC
- Constructor: `PDO + NotificationService`
- Bootstrap nuovo utente: progetto default, chat admin, onboarding
- Tabelle: `projects`, `subscriptions`, `plans`, `chats`, `chat_participants`, `chat_sections`, `chat_messages`, `deals`, `tasks`, `project_onboarding`, `onboarding_templates`

#### TelegramService.php — 160 LOC
- Constructor: `?array $telegramSettings, ?LoggerInterface`
- Alert: generic, KPI, health, backup, error
- API: `api.telegram.org/bot{token}/{method}`

#### CustomFieldService.php — 154 LOC
- Custom field definitions per entity type
- Tabelle: `custom_field_defs`

#### AiMemoryService.php — 152 LOC
- Constructor: `PDO + LoggerInterface`
- UPSERT memory per user/project/key
- Tabelle: `ai_memory`

#### InvoiceService.php — 142 LOC
- Legacy invoice service (v1)
- Tabelle: `invoices`, `auth_users`, `projects`

#### NotificationService.php — 135 LOC
- Notifiche in-app con preferenze
- Tabelle: `notifications`, `notification_preferences`, `project_members`, `chat_participants`, `chats`

#### PdfExportService.php — 124 LOC
- Constructor: `AnalyticsService`
- Export analytics come PDF

#### AdminBillingService.php — 121 LOC
- MRR breakdown, churn trend, plan changes, revenue trend, top customers
- Tabelle: `subscriptions`, `plans`, `auth_users`, `revenue_snapshots`, `audit_log`

#### SiteTemplateService.php — 111 LOC
- Template per site builder con customization per utente
- Tabelle: `site_templates`, `user_site_customizations`

#### JwtService.php — 106 LOC
- Constructor: `array $jwtSettings, array $cookieSettings`
- JWT encode/decode, cookie builder, TTL management (access/refresh/remember)

#### SecurityService.php — 105 LOC
- Login attempt recording, account lockout, brute force protection
- Tabelle: `login_attempts`, `account_lockouts`

#### FeatureFlagService.php — 94 LOC
- Feature flag globali + override per utente
- Tabelle: `feature_flags`, `feature_flag_overrides`

#### UserService.php — 90 LOC
- Profile CRUD, change password, deactivate
- Tabelle: `auth_users`

#### AuditService.php — 84 LOC
- Security event logging
- Tabelle: `audit_log`, `activity_log`, `auth_users`

#### FeedbackService.php — 76 LOC
- User feedback submission con rate limiting (countToday)
- Tabelle: `user_feedback`, `auth_users`

#### CronLogger.php — 73 LOC
- Constructor: `PDO + string $cronName`
- Log start/success/fail per cron job
- Tabelle: `cron_runs`

#### EventDispatcher.php — 62 LOC
- Constructor: `WebhookService + LoggerInterface`
- Domain event → webhook dispatch
- Metodi: `fire()`, `fireOrFail()`

#### PipelineService.php — 47 LOC
- Document pipeline con step-based workflow
- Tabelle: `document_pipelines`, `document_pipeline_log`, `project_files`, `auth_users`

#### ActivityLogService.php — 46 LOC
- Activity logging per user/project
- Tabelle: `activity_log`, `auth_users`, `projects`

---

## Mappa Dipendenze (Constructor Injection)

```
PDO (onnipresente)
├── AiAssistantService → NotificationService, LoggerInterface, TelegramService?, AiMemoryService?, AiEscalationService?
├── StripeService → MailService, ReferralService, PlatformInvoiceService?, TelegramService?
├── EInvoiceService → InvoiceServiceV2
├── PlatformInvoiceService → OssTrackingService?
├── OpenBankingService → (array settings)
├── CampaignService → EmailQueueService
├── ExpenseService → JournalEntryService, NotificationService?
├── PaymentService → JournalEntryService
├── WorkflowService → EmailQueueService, NotificationService
├── InvoiceServiceV2 → TaxService, InvoiceNumberingService, JournalEntryService
├── BudgetService → NotificationService?
├── CalendarService → EmailQueueService?, NotificationService?
├── BankReconciliationService → JournalEntryService, PaymentService?, ExpenseService?
├── DunningService → StripeService?
├── UsageMeteringService → NotificationService?, EmailQueueService?, MailService?
├── WebhookService → LoggerInterface
├── LimitGraceService → NotificationService, EmailQueueService, MailService
├── GoogleDriveService → GoogleIntegrationService, LoggerInterface
├── GoogleContactsService → GoogleIntegrationService, LoggerInterface
├── GoogleCalendarService → GoogleIntegrationService, LoggerInterface
├── GoogleSheetsService → GoogleIntegrationService, LoggerInterface
├── GoogleIntegrationService → LoggerInterface, (array config)
├── BankImportService → BankAccountService
├── InvoicePdfService → InvoiceServiceV2
├── ClientPortalService → MailService
├── EmailConfigService → (array config)
├── EmailQueueService → MailService, EmailConfigService?
├── CountryTaxProfileService → CountryTaxProfileSeeder, TaxService?, InvoiceNumberingService?
├── ApiKeyService → LoggerInterface
├── ReferralService → NotificationService
├── AiSuggestionService → LoggerInterface, (array config)
├── AiEscalationService → NotificationService, LoggerInterface, TelegramService?
├── AiMemoryService → LoggerInterface
├── UserBootstrapService → NotificationService
├── EventDispatcher → WebhookService, LoggerInterface
├── JobQueueService → LoggerInterface?
├── DomainRegistrarService → (array config)
├── HostingResellerService → (array config)
├── VerificationService → (array settings)
├── OcrService → (array settings)
├── B2BackupService → (array settings), LoggerInterface?
├── MailService → (array config)
├── TelegramService → (array settings), LoggerInterface?
├── CronLogger → string $cronName
├── JwtService → (array settings x2)
├── ChorusProBuilder → FacturXBuilder?
├── FacturXBuilder → string $profile
├── PdfExportService → AnalyticsService
├── TimeTrackingService → InvoiceService?
└── FileService → string $storagePath
```

---

## API Esterne — Riepilogo

| API | Servizio/i | Scopo |
|-----|-----------|-------|
| **Stripe** | StripeService, DunningService | Pagamenti, abbonamenti, webhook, billing portal |
| **Google Gemini** | AiAssistantService, AiSuggestionService, OpenBankingService | AI chat, suggerimenti, categorizzazione transazioni |
| **OpenAI GPT-4o-mini** | AiAssistantService | Fallback AI chat |
| **TrueLayer** | OpenBankingService | PSD2 Open Banking, transaction sync |
| **Aruba SDI** | EInvoiceService | Invio fatture elettroniche (IT) |
| **Infocert SDI** | EInvoiceService | Invio fatture elettroniche (IT, alternativo) |
| **Google OAuth2** | OAuthService, GoogleIntegrationService | Login, authorization |
| **Google Calendar** | GoogleCalendarService | Sync eventi |
| **Google Contacts** | GoogleContactsService | Sync contatti |
| **Google Drive** | GoogleDriveService | Sync file |
| **Google Sheets** | GoogleSheetsService | Export dati |
| **Apple OAuth** | OAuthService | Login Apple |
| **Mindee** | OcrService | OCR ricevute |
| **Veryfi** | OcrService | OCR ricevute (fallback) |
| **Telegram** | TelegramService | Alert e notifiche admin |
| **Backblaze B2** | B2BackupService | Backup cloud |
| **ECB** | CurrencyService | Tassi di cambio EUR |

---

## Tabelle DB — Le Più Referenziate

| Tabella | Referenziata da N servizi |
|---------|--------------------------|
| `auth_users` | ~40+ |
| `projects` | ~25+ |
| `subscriptions` | ~15+ |
| `plans` | ~15+ |
| `invoices` | ~12+ |
| `contacts` | ~10+ |
| `chart_of_accounts` | ~8+ |
| `journal_entries` | ~7+ |
| `journal_entry_lines` | ~7+ |
| `deals` | ~7+ |
| `tasks` | ~6+ |
| `expenses` | ~5+ |
| `bank_transactions` | ~5+ |
| `bank_accounts` | ~4+ |

---

*Fine audit — 110+ servizi analizzati esaustivamente.*

---

## SWOT Analysis Aggiornata (Post-Roadmap v3.0)

### Strengths (Punti di Forza)
- **Copertura funzionale completa**: 26+ moduli business in un'unica piattaforma
- **Multi-country compliance**: 5 formati e-invoice, 5 report IVA nazionali, VIES validation
- **AI integrato**: Gemini 2.0 Flash Lite + GPT-4o-mini fallback, support chat AI con escalation
- **Testing robusto**: 2579+ PHPUnit tests, 55+ service test files, 9 E2E Playwright suites
- **Documentazione**: PHPDoc 95.3%, JSDoc 98.6%, API docs aggiornati
- **Real-time**: Pusher per live updates senza WebSocket server
- **Internazionalizzazione**: 20 lingue, RTL arabo, namespace split per performance
- **Accessibility**: WCAG 2.1 AA, axe-core validation

### Weaknesses (Debolezze)
- **Shared hosting**: Limitazioni su Redis, WebSocket, Docker, risorse condivise
- **Monolita PHP**: 80K+ LOC in singola applicazione
- **No staging separato**: Testing su subdirectory dello stesso server
- **Admin panel**: Non completamente responsive, no design system condiviso

### Opportunities (Opportunità)
- **Migration to VPS**: Possibile upgrade a VPS per Redis, container, più risorse
- **Mobile app**: PWA già funzionale, React Native possibile
- **Marketplace integrations**: Plugin system per Shopify, WooCommerce, etc.
- **AI expansion**: Aggiunta modelli specializzati per vertical specifici

### Threats (Minacce)
- **Competitori SaaS**: FreshBooks, Zoho, Wave — budget marketing superiore
- **Hosting vendor lock-in**: Dipendenza da Hostinger pricing
- **AI cost scaling**: Costi Gemini/GPT crescono con utenti attivi
- **Regulatory changes**: Frequenti cambiamenti normative fiscali EU
