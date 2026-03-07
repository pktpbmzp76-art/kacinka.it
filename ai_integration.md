# AI Integration

> **Data**: 2026-03-07 | **Versione**: 1.0 | **Classificazione**: Confidenziale

---

## 1. Overview

| Metrica | Valore |
|---------|--------|
| **Total AI Code** | ~5,286 LOC (backend) |
| **Primary Model** | Google Gemini 2.0 Flash |
| **Fallback Model** | OpenAI GPT-4o-mini |
| **OCR Model** | Google Gemini Vision |
| **AI Features** | Chat Assistant, OCR, Support Agent, Content Suggestions |
| **Rate Control** | Plan-based limits + spending caps |
| **Interaction Tracking** | Per-call token/cost logging |

---

## 2. Architecture

```
┌──────────────────────────────────────────────────────┐
│                    FRONTEND (React)                   │
│  ┌─────────┐  ┌──────────┐  ┌─────────────────────┐ │
│  │ AI Chat │  │ OCR UI   │  │ AI Suggestions      │ │
│  │ Panel   │  │ (Upload) │  │ (Inline tooltips)   │ │
│  └────┬────┘  └────┬─────┘  └──────────┬──────────┘ │
└───────┼────────────┼───────────────────┼─────────────┘
        │            │                   │
        ▼            ▼                   ▼
┌──────────────────────────────────────────────────────┐
│                    BACKEND (PHP)                      │
│                                                       │
│  ┌──────────────────┐  ┌───────────────────────────┐ │
│  │   AiPolicy.php   │  │   AiContextBuilder.php    │ │
│  │   (227 LOC)      │  │   (646 LOC)              │ │
│  │ • Input sanitize  │  │ • User profile ctx       │ │
│  │ • Output sanitize │  │ • Subscription ctx       │ │
│  │ • Content policy  │  │ • Projects ctx           │ │
│  │ • Jailbreak detect│  │ • Financial ctx          │ │
│  └────────┬─────────┘  │ • CRM ctx                │ │
│           │             │ • Calendar ctx            │ │
│           ▼             │ • Task ctx                │ │
│  ┌──────────────────┐   │ • Team ctx               │ │
│  │ AiAssistantSvc   │◄──└───────────────────────────┘ │
│  │ (1,930 LOC)      │                                 │
│  │ • Chat logic      │   ┌──────────────────────────┐ │
│  │ • Action system   │   │   OcrService.php         │ │
│  │ • Model routing   │   │   (1,045 LOC)            │ │
│  │ • Cost tracking   │   │ • Multi-provider OCR     │ │
│  │ • Escalation      │   │ • Gemini Vision          │ │
│  │ • Memory          │   │ • QR pre-processing      │ │
│  └────────┬─────────┘   │ • Fiscal validation      │ │
│           │              └───────────┬──────────────┘ │
│           ▼                          ▼                 │
│  ┌──────────────────┐   ┌──────────────────────────┐ │
│  │ SupportAiService │   │ ExpenseOcrWorkflowSvc    │ │
│  │ (738 LOC)        │   │ (668 LOC)                │ │
│  │ • L1 triage      │   │ • Expense auto-creation  │ │
│  │ • RAG/KB search  │   │ • Category matching      │ │
│  │ • Escalation     │   │ • Duplicate detection    │ │
│  └────────┬─────────┘   └──────────────────────────┘ │
└───────────┼──────────────────────────────────────────┘
            ▼
┌──────────────────────────────────────────────────────┐
│               EXTERNAL AI PROVIDERS                   │
│                                                       │
│  ┌─────────────────┐    ┌──────────────────────────┐ │
│  │ Google Gemini    │    │ OpenAI (Fallback)        │ │
│  │ • 2.0 Flash      │    │ • GPT-4o-mini           │ │
│  │ • 2.0 Flash-lite │    │ • api.openai.com        │ │
│  │ • 2.5 Flash      │    │ • Activated only on     │ │
│  │ • Vision (OCR)   │    │   Gemini failure/429    │ │
│  └─────────────────┘    └──────────────────────────┘ │
└──────────────────────────────────────────────────────┘
```

---

## 3. Service Inventory

### 3.1 AiAssistantService (1,930 LOC)

**Ruolo**: Core conversational AI engine.

| Feature | LOC Range | Dettaglio |
|---------|-----------|----------|
| **Plan-based limits** | L42-52 | 100 (starter) → ∞ (enterprise) + addon packs |
| **Spending cap** | L75 | 50% of plan monthly price; project-level override via `ai_spending_config.monthly_cap_cents` |
| **Access control** | L370-510 | 7-step check: admin bypass → plan → DB limit → addons → unlimited → usage → spending → AI toggle |
| **Dynamic prompt** | L885-1005 | Injects: AI memory, identity, subscription, projects, work metrics, financials, CRM, calendar, notifications, nav context |
| **Gemini API** | L1030-1200 | `generativelanguage.googleapis.com/v1beta/models/{model}:generateContent` |
| **Model config** | — | `temperature=0.3`, `maxOutputTokens=600`, `responseMimeType=application/json` |
| **Fallback** | L1280-1370 | If Gemini fails → GPT-4o-mini via `api.openai.com/v1/chat/completions` |
| **Conversation ctx** | L1430 | Last 10 messages + progressive summary (updated every 5 interactions) |
| **Escalation** | — | 10 keyword detection (IT/EN), repeated message threshold=3, delegates to `AiEscalationService` |
| **Action system** | — | AI suggests typed actions: navigate, create_invoice, create_task, create_contact, create_deal, search, export_csv, store_memory, delete_memory |
| **Logging** | L1660 | Every call → `ai_interactions` with model, tokens, cost_cents |
| **Upsell** | — | 3 randomized persuasive messages on limit exhaustion |

### 3.2 OcrService (1,045 LOC)

**Ruolo**: Receipt/invoice scanning via AI vision.

| Feature | Dettaglio |
|---------|----------|
| **Providers** | Mindee, Veryfi, Gemini Vision (configurable) |
| **Gemini Vision** | Base64 image → structured extraction prompt → JSON schema |
| **Country detection** | 11 country-specific regex patterns per VAT/fiscal ID (IT, PL, RO, TR, GR, SA, IN, DE, FR, ES, PT) |
| **QR pre-processing** | Decodes QR codes before OCR; merges structured data (ZATCA TLV, GST) |
| **Fiscal validation** | `net + tax = total` check (±1 cent); auto-computes missing values; snaps to common EU tax rates |
| **Confidence score** | ≥0.80 → `completed`, else → `review_needed` |
| **Universal schema** | Normalized output: supplier/buyer, document, totals, line items, country fields, QR data |

### 3.3 SupportAiService (738 LOC)

**Ruolo**: L1 automated support agent.

| Feature | Dettaglio |
|---------|----------|
| **Triage** | Categories: user_config, billing_error, plan_limit, bug_platform, unknown |
| **RAG** | `FULLTEXT MATCH AGAINST` on `support_knowledge_base` (title, content_text, tags), top 5 |
| **Rich context** | Uses `AiContextBuilder::buildFullContext()` for user profile/subscriptions/projects |
| **Pre-escalation** | Requires gathering browser, steps-to-reproduce, screenshots before human escalation |
| **Tiered escalation** | `needs_l2 → ai_l2` (deeper diagnostics), `needs_human → human` (Telegram + email alert) |
| **Model** | Gemini primary + GPT-4o-mini fallback |
| **Output** | Structured JSON: reply, category, confidence, needs_l2, needs_human, escalation_summary, actions, kb_references |

### 3.4 AiPolicy (227 LOC)

**Ruolo**: Security guardrails per tutti gli AI services.

| Feature | Dettaglio |
|---------|----------|
| **Content policy** | Appended to every system prompt — defines allowed/forbidden topics |
| **Input sanitization** | 18 regex patterns: "ignore previous", "pretend you are", "DAN", "jailbreak", "sql inject", "xss", "shell exec" + special char ratio check |
| **Output sanitization** | Strips: internal URLs, DB table references, SQL queries, server paths, credential patterns, PHP code blocks, non-whitelisted URLs |
| **Information hiding** | Architecture, endpoints, DB schema, credentials NEVER revealed |

### 3.5 AiContextBuilder (646 LOC)

**Ruolo**: Unified context builder per tutti gli AI features.

| Context Block | Data Source |
|--------------|-------------|
| User profile | auth_users |
| Subscription | subscriptions + plans |
| Projects | projects (list + stats) |
| Addons | project_addons |
| Aggregate stats | COUNT invoices, tasks, contacts |
| Deals summary | deals pipeline snapshot |
| Upcoming events | calendar_events (next 7 days) |
| Task deadlines | tasks WHERE due_date < NOW() + 3 days |
| Team data | project_members |
| Expenses | expenses summary |
| Notifications | unread notifications count |
| Time tracking | time_entries summary |

---

## 4. Model Economics

### 4.1 Cost Per Token

| Model | Input (€¢/1M) | Output (€¢/1M) | Use Case |
|-------|---------------|----------------|----------|
| gemini-2.0-flash | 10 | 40 | Default chat |
| gemini-2.0-flash-lite | 7 | 28 | Light queries |
| gemini-2.5-flash | 15 | 60 | Complex reasoning |
| gpt-4o-mini (fallback) | 14 | 56 | Gemini failure fallback |

### 4.2 Estimated Cost Per Interaction

| Type | Avg Input Tokens | Avg Output Tokens | Cost/Call |
|------|-----------------|-------------------|-----------|
| Chat (simple) | ~500 | ~200 | ~€0.0001 |
| Chat (complex with context) | ~2,000 | ~500 | ~€0.0004 |
| OCR (image + text) | ~5,000 | ~800 | ~€0.0009 |
| Support AI (with RAG) | ~3,000 | ~600 | ~€0.0005 |

### 4.3 Monthly Cost Projections

| Users | Calls/User/Mo | Total Calls | Est. Cost/Mo |
|-------|--------------|-------------|-------------|
| 100 | 20 | 2,000 | ~€0.80 |
| 500 | 30 | 15,000 | ~€6.00 |
| 1,500 | 40 | 60,000 | ~€24.00 |
| 5,000 | 50 | 250,000 | ~€100.00 |

> **AI Cost/Revenue Ratio** molto favorevole: anche a 5,000 utenti, AI costs ~€100/mo vs revenue ~€300K MRR → <0.04% del fatturato.

### 4.4 Plan-Based Limits

| Piano | AI Calls/Mo | Spending Cap | Addons |
|-------|------------|-------------|--------|
| Starter | 5 | €0 (no spending) | — |
| Pro | 100 | €9.50 (50% × €19) | AI 500 €9.90, AI 1000 €14.90 |
| Pro+ | 500 | €19.50 (50% × €39) | AI 500 €9.90, AI 1000 €14.90 |
| Elite | 2,000 | €39.50 (50% × €79) | AI 500 €9.90, AI 1000 €14.90 |
| Enterprise | ∞ | €74.50 (50% × €149) | — |

---

## 5. Data Flow

### 5.1 Chat Flow

```
User Message → AiPolicy::sanitizeInput()
    ↓ (reject if jailbreak detected)
AiAssistantService::checkAiAccess()
    ↓ (7-step access control)
AiContextBuilder::buildFullContext()
    ↓ (12 context blocks from DB)
AiAssistantService::buildDynamicPrompt()
    ↓ (merge: system prompt + content policy + context + memory + conversation history)
AiAssistantService::callOpenAIWithActions()
    ↓ (Gemini API → 200 OK or fallback to GPT-4o-mini)
AiPolicy::sanitizeResponse()
    ↓ (strip internal data)
AiAssistantService::storeInteraction()
    ↓ (log to ai_interactions: model, tokens, cost_cents)
Return JSON Response
    ↓ (reply, actions[], confidence, context)
```

### 5.2 OCR Flow

```
Image Upload → Validate (type, size, dimensions)
    ↓
OcrService::preProcessQr()
    ↓ (decode QR if present → structured data)
OcrService::callGeminiVision()
    ↓ (base64 image + country-specific prompt → Gemini Vision API)
OcrService::toUniversalSchema()
    ↓ (normalize to standard invoice schema)
OcrService::validateFiscalConsistency()
    ↓ (net + tax = total check, auto-fix, snap tax rates)
ExpenseOcrWorkflowService::processOcrResult()
    ↓ (auto-create expense, match category, detect duplicates)
Return: { status, confidence, data, expense_id }
```

---

## 6. Database Tables

| Table | Purpose | Key Columns |
|-------|---------|-------------|
| **ai_interactions** | Usage & cost tracking | user_id, model, input_tokens, output_tokens, cost_cents |
| **ai_conversation_summaries** | Progressive chat summarization | user_id, project_id, summary_text |
| **ai_escalations** | Human escalation queue | interaction_id, reason, status, assigned_to |

---

## 7. Security

### 7.1 Input Security
- 18 regex patterns for prompt injection detection
- Special character ratio threshold
- Message length limits
- Content policy enforcement

### 7.2 Output Security
- Internal URL stripping
- Database table name redaction
- SQL/PHP code block removal
- Server path sanitization
- Credential pattern detection
- Non-whitelisted URL blocking

### 7.3 Data Privacy
- Conversations stored in ai_interactions (plaintext)
- Summaries auto-generated every 5 turns
- No PII forwarded to AI models beyond user-provided message
- AiContextBuilder sends aggregated stats, not raw data (es. "5 invoices unpaid" not actual invoice content)

---

## 8. Competitive Positioning

| Feature | Kacinka | Zoho Zia | QuickBooks AI | Holded |
|---------|---------|----------|---------------|--------|
| Conversational AI | ✅ Full chat | ✅ | ⚠️ Limited | ❌ |
| OCR Receipts | ✅ Multi-country | ✅ | ✅ | ❌ |
| AI Actions (create invoice, task) | ✅ 9 actions | ⚠️ Limited | ❌ | ❌ |
| Progressive Memory | ✅ | ❌ | ❌ | ❌ |
| L1 Support Agent | ✅ RAG-based | ⚠️ | ❌ | ❌ |
| Prompt Injection Protection | ✅ 18 patterns | Unknown | Unknown | N/A |
| Multi-model Fallback | ✅ Gemini → GPT | ❌ (single) | ❌ | N/A |
| Cost Transparency | ✅ Per-call tracking | ❌ | ❌ | N/A |
| Spending Caps | ✅ Plan-based | ❌ | ❌ | N/A |

---

## 9. Gap Analysis & Roadmap

### 9.1 Current Gaps

| Gap | Priorità | Effort |
|-----|----------|--------|
| No streaming responses (SSE per AI chat) | 🟡 Media | 8h |
| No voice input (speech-to-text) | 🟢 Bassa | 16h |
| No RAG for general help (solo support KB) | 🟡 Media | 16h |
| No fine-tuning/embeddings | 🟢 Bassa | 24h |
| No AI-generated reports/summaries | 🟡 Media | 16h |
| No multi-language AI system prompts | 🟡 Media | 8h |

### 9.2 Enhancement Roadmap

| Phase | Feature | Beneficio |
|-------|---------|----------|
| **M1-M3** | Streaming AI responses (SSE) | Real-time UX |
| **M1-M3** | Multi-language system prompts | Full i18n AI |
| **M3-M6** | RAG for platform documentation | Self-service help |
| **M3-M6** | AI-generated financial summaries | Executive insights |
| **M6-M12** | Voice input (Whisper API) | Accessibility |
| **M6-M12** | Custom AI personas per project | White-label AI |

---

*Documento generato il 2026-03-07. LOC da analisi statica: AiAssistantService(1,930), OcrService(1,045), SupportAiService(738), AiContextBuilder(646), AiPolicy(227) = 4,586 backend LOC + ExpenseOcrWorkflowService(668) + SupportDiagnosticService(807) = ~6,061 total AI ecosystem LOC.*
