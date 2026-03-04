# Audit UX/UI — Kacinka.it Platform

> **Data**: Marzo 2026  
> **Versione**: 2.0 — Rigenerato da analisi completa del codice sorgente  
> **Scope**: Analisi esaustiva del design system, componenti UI, accessibilità, animazioni, responsive design  
> **Metodo**: Lettura diretta di tutti i ~54 componenti, CSS, config Tailwind, lib patterns  

---

## 1. Panoramica Design System

### 1.1 Stack Tecnologico UI

| Layer | Tecnologia | Versione | Ruolo |
|-------|-----------|---------|-------|
| CSS Framework | Tailwind CSS | v4.1.4 | Utility-first, tema via `@theme` |
| Icons | Lucide React | latest | Set iconografico unified |
| Animazioni | Framer Motion | v12.33.0 | Physics-based animation |
| Form | react-hook-form + @hookform/resolvers | latest | Gestione form + validazione |
| Virtualizzazione | @tanstack/react-virtual | latest | Virtual scrolling |
| Drag & Drop | @dnd-kit | latest | Kanban, riordinamento |
| Rich Text | @tiptap | latest | Editor WYSIWYG |
| Calendario | FullCalendar | v6 | Vista calendario |
| Grafici | Recharts | latest | Charts dashboard/analytics |
| Toast | react-hot-toast | latest | Notifiche toast |
| Error Tracking | @sentry/react | latest | Crash reporting UI |

### 1.2 Approccio Design

- **Dark-first design**: il tema scuro è il default, con override light completo
- **Glass morphism**: `.glass` e `.glass-strong` con `backdrop-filter: saturate() blur()`
- **Brand violet**: palette primaria violet (#7c3aed)
- **Mobile-first responsive**: breakpoint Tailwind standard + componenti dedicati mobile

---

## 2. Design Tokens (Tailwind v4 `@theme`)

### 2.1 Palette Colori Completa

Definita in `app/src/index.css` via Tailwind v4 `@theme` block:

**Base / Background (Dark)**:

| Token | Hex | Utilizzo |
|-------|-----|---------|
| `--color-base-950` | `#060910` | Sfondo root |
| `--color-base-900` | `#0d1117` | Sfondo panel |
| `--color-base-850` | `#111827` | Sfondo card |
| `--color-base-800` | `#151b28` | Sfondo input |
| `--color-base-750` | `#1a2233` | Sfondo hover |
| `--color-base-700` | `#1e293b` | Bordi |
| `--color-base-600` | `#334155` | Bordi secondari |
| `--color-base-500` | `#475569` | Testo disabilitato |
| `--color-base-400` | `#64748b` | Testo secondario |
| `--color-base-300` | `#94a3b8` | Testo terziario |
| `--color-base-200` | `#e2e8f0` | Testo primario |
| `--color-base-100` | `#c4d4e8` | Testo luminoso |

**Brand (Violet)**:

| Token | Hex | Utilizzo |
|-------|-----|---------|
| `--color-brand-50` | `#f5f3ff` | Background highlight |
| `--color-brand-100` | `#ede9fe` | Badge background |
| `--color-brand-200` | `#ddd6fe` | Hover light |
| `--color-brand-300` | `#c4b5fd` | Active state |
| `--color-brand-400` | `#a78bfa` | Icon tint |
| `--color-brand-500` | `#8b5cf6` | Primary button |
| `--color-brand-600` | `#7c3aed` | Primary accent |
| `--color-brand-700` | `#6d28d9` | Hover dark |
| `--color-brand-800` | `#5b21b6` | Active dark |
| `--color-brand-900` | `#4c1d95` | Deep brand |

**Accent (Cyan)**:

| Token | Hex |
|-------|-----|
| `--color-accent-300` | `#67e8f9` |
| `--color-accent-400` | `#22d3ee` |
| `--color-accent-500` | `#06b6d4` |
| `--color-accent-600` | `#0891b2` |
| `--color-accent-700` | `#0e7490` |

**Semantici**:

| Gruppo | 400 | 500 | 600 |
|--------|-----|-----|-----|
| Success | `#4ade80` | `#22c55e` | `#16a34a` |
| Warning | `#fbbf24` | `#f59e0b` | `#d97706` |
| Danger | `#f87171` | `#ef4444` | `#dc2626` |
| Info | `#60a5fa` | `#3b82f6` | `#2563eb` |

### 2.2 Tema Light

Override completo via `[data-theme="light"]`:
- Inverte tutta la palette base (950↔50, 900↔100, etc.)
- Remap ~60+ override per utility (text, bg, border, hover, placeholder)
- Glass morphism adattato per sfondo chiaro
- Scrollbar personalizzata per entrambi i temi
- Toggle via `themeStore` Zustand + `localStorage`

### 2.3 Tipografia

| Token | Valore | Utilizzo |
|-------|--------|---------|
| Font family | `Inter, ui-sans-serif, system-ui, -apple-system, sans-serif` | Tutto il testo |
| `--text-hero` | `clamp(2rem, 5vw, 3.5rem)` | Titoli hero |
| `--text-h1` | `30px` | Titoli pagina |
| `--text-h2` | `24px` | Titoli sezione |
| `--text-h3` | `20px` | Sottotitoli |
| `--text-body` | `14px` | Corpo testo |
| `--text-small` | `12px` | Testo secondario |
| `--text-caption` | `11px` | Label, hint |

### 2.4 Spaziatura e Forma

| Pattern | Valore | Componenti |
|---------|--------|-----------|
| Input border radius | `rounded-lg` (0.75rem) | Tutti gli input (`.input-field`) |
| Card border radius | `rounded-xl` (1rem) | Card, panel (`.card-cls`) |
| Modal border radius | `rounded-2xl` (1.5rem) | Modal, sheet, drawer |
| Input padding | `px-3.5 py-2.5` | Tutti gli input |
| Card padding | `p-6` | Card contenuto |
| Sezione gap | `gap-4` / `gap-6` | Liste, grid layout |

---

## 3. Inventario Componenti Completo

### 3.1 Form Controls (7 componenti)

| Componente | LOC | Funzionalità |
|-----------|-----|-------------|
| `Button.jsx` | 84 | **7 varianti** (primary, secondary, ghost, link, danger, success, brand) × **8 taglie** + stato loading + icona + disabled |
| `Input.jsx` | 98 | Label, errore, icona sx/dx, clearable, helper text |
| `Checkbox.jsx` | 57 | Stilizzato con animazione check |
| `CurrencyInput.jsx` | 120 | Formattazione valuta in tempo reale |
| `DatePicker.jsx` | 230 | Selettore data con calendario dropdown |
| `SmartSelect.jsx` | 317 | **Autocomplete API-linked** — debounce 300ms, abort controller, backoff 429, keyboard nav, cache risultati |
| `FilterBar.jsx` | 193 | Barra ricerca + tab status + dropdown tipo + range date — **componente composito riutilizzabile** |

### 3.2 Data Display (8 componenti)

| Componente | LOC | Funzionalità |
|-----------|-----|-------------|
| `KpiBar.jsx` | 155 | Griglia KPI card con color theming, indicatori trend (↑/↓), effetto glow su hover |
| `StatusBadge.jsx` | 92 | Pill colorata per stati (paid, draft, overdue, sent, void, etc.) |
| `Badge.jsx` | 44 | Badge generico colorato |
| `NewBadge.jsx` | 53 | Indicatore "Novità" per feature nuove |
| `Avatar.jsx` | 120 | Avatar utente con fallback iniziali, indicatore online, 4 taglie |
| `Breadcrumb.jsx` | 60 | Trail breadcrumb da URL path |
| `UsageCounter.jsx` | 89 | Progress bar utilizzo piano (X / Y) con colore dinamico |
| `EmptyState.jsx` | 116 | Placeholder lista vuota con icona, titolo, descrizione, CTA |

### 3.3 Overlay & Panel (10 componenti)

| Componente | LOC | Funzionalità |
|-----------|-----|-------------|
| `Overlay.jsx` | 194 | **Modal + Drawer compound** — focus trap, backdrop click, Escape, responsive bottom-sheet su mobile |
| `Drawer.jsx` | 113 | Side panel slide-in — focus trap, spring animation, dimensioni configurabili |
| `BottomSheet.jsx` | 81 | Bottom sheet mobile, modal centrato su desktop |
| `DrawerStackHost.jsx` | 148 | **Stack di drawer** — push/pop con animazione push-back e dimming progressivo |
| `ConfirmDialog.jsx` | 126 | `alertdialog` WCAG AA — focus trap, variante distruttiva (rosso), auto-focus |
| `WelcomeModal.jsx` | 174 | Guida primo accesso 4 step — localStorage permanenza |
| `AIAssistantDrawer.jsx` | 768 | **Il componente UI più grande** — Chat AI con streaming, suggerimenti contestuali, esecuzione azioni, rendering markdown |
| `CommandPalette.jsx` | 508 | **Ctrl+K** — fuzzy search, tab categorie, ricerche recenti, ricerca entità API, navigazione keyboard |
| `ShortcutOverlay.jsx` | 122 | Overlay scorciatoie (tasto `?`) |
| `NotificationDropdown.jsx` | 278 | Dropdown notifiche con conteggio unread real-time |

### 3.4 Navigation (4 componenti)

| Componente | LOC | Funzionalità |
|-----------|-----|-------------|
| `Sidebar.jsx` | 668 | **Navigazione principale** — 3 modalità responsive (fisso/collassato/overlay), resize handle drag, quick-create, feedback, persona-gated |
| `Header.jsx` | 337 | Top bar sticky — breadcrumb, project switcher, avatar dropdown, theme toggle, search trigger, notifiche |
| `MobileBottomNav.jsx` | 142 | Bottom tab bar mobile — context-aware (workspace vs. global), indicatore attivo animato `layoutId` |
| `LanguageDropdown.jsx` | 168 | Switcher lingua con 20 lingue, variante header/sidebar |

### 3.5 Feedback & Status (7 componenti)

| Componente | LOC | Funzionalità |
|-----------|-----|-------------|
| `ToastUndo.jsx` | 111 | Toast con barra countdown e pulsante Undo (5s) |
| `GlobalLoadingBar.jsx` | 89 | Barra progresso top YouTube/GitHub-style durante navigazione + API |
| `NetworkStatusBanner.jsx` | 55 | Banner offline con `role="alert"` |
| `Spinner.jsx` | 69 | Loading spinner animato |
| `SkeletonLoader.jsx` | 202 | **9 preset skeleton**: table-row, card, kpi-bar, kanban-column, list-item, text, circle, rect, chart |
| `LimitBanner.jsx` | 28 | Banner limite piano raggiunto |
| `LimitPreWarning.jsx` | 94 | Banner pro-attivo prossimità al limite piano |

### 3.6 Interaction (4 componenti)

| Componente | LOC | Funzionalità |
|-----------|-----|-------------|
| `DropdownMenu.jsx` | 155 | Dropdown keyboard-navigabile con separatori, shortcut keys, item distruttivi |
| `Tooltip.jsx` | 75 | 4 posizioni (top/bottom/left/right) con animazione, hover + focus trigger |
| `SwipeableRow.jsx` | 155 | Swipe touch per azioni riga (elimina/approva) via Framer Motion drag |
| `VirtualList.jsx` | 107 | Virtual scrolling `@tanstack/react-virtual` con pull-to-refresh e infinite scroll |

### 3.7 Media (2 componenti)

| Componente | LOC | Funzionalità |
|-----------|-----|-------------|
| `LazyImage.jsx` | 37 | IntersectionObserver (200px margin) + blur placeholder + fade-in |
| `Logo.jsx` | 46 | Logo brand con varianti dimensione |

### 3.8 Infrastructure (4 componenti)

| Componente | LOC | Funzionalità |
|-----------|-----|-------------|
| `LiveRegion.jsx` | 52 | Screen reader `aria-live` — dual region (polite + assertive), hook `useAnnounce()` |
| `ModuleErrorBoundary.jsx` | 62 | Error boundary per crash recovery a livello modulo |
| `RequireAuth.jsx` | 74 | Auth guard per route protette |
| `UpgradeGate.jsx` | 117 | Wrapper plan-gated — mostra upgrade prompt se feature non inclusa nel piano |

### 3.9 Domain Components

| Componente | LOC | Funzionalità |
|-----------|-----|-------------|
| `FiscalOnboardingStep.jsx` | 419 | Wizard onboarding fiscale italiano — P.IVA, regime, codice destinatario SDI |
| `CustomFieldDefinitionManager.jsx` | 258 | Gestione tipi campi custom — text, number, date, select, multi-select, boolean |
| `CustomFieldEditor.jsx` | 130 | Editor inline campi personalizzati |
| `CustomFieldRenderer.jsx` | 92 | Renderizzazione valori campi custom |
| `TrialCountdownBadge.jsx` | 61 | Badge countdown giorni trial |

**Totale: ~54 componenti, ~7,700+ LOC**

---

## 4. Architettura Layout (AppShell)

### 4.1 Struttura Gerarchica

```
<div className="min-h-dvh">
  ┌──────────────────────────────────────────────────────┐
  │ GlobalLoadingBar                                      │ ← z-50 top progress
  ├──────────────────────────────────────────────────────┤
  │ <a> Skip Link (#main-content)                         │ ← a11y, visible on focus
  ├────────┬─────────────────────────────────────────────┤
  │        │ Header (sticky top-0, z-30)                  │
  │        ├─────────────────────────────────────────────┤
  │        │ {Banners contestuali}                        │
  │        │  └ Impersonation / Grace / Trial / Limit     │
  │ Side   ├─────────────────────────────────────────────┤
  │ bar    │ NetworkStatusBanner                          │
  │        ├─────────────────────────────────────────────┤
  │ (fixed │ <main id="main-content">                     │
  │  left) │   <Suspense fallback={SkeletonLoader}>       │
  │        │     <Outlet /> ← React Router page content   │
  │        │   </Suspense>                                │
  │        │ </main>                                      │
  ├────────┴─────────────────────────────────────────────┤
  │ {Global Overlays — portaled above everything}         │
  │  ├ CommandPalette (Ctrl+K)                            │
  │  ├ AIAssistantDrawer                                  │
  │  ├ DrawerStackHost                                    │
  │  ├ ShortcutOverlay (?)                                │
  │  └ WelcomeModal                                       │
  ├──────────────────────────────────────────────────────┤
  │ MobileBottomNav (fixed bottom, md:hidden)             │ ← mobile only
  └──────────────────────────────────────────────────────┘
```

### 4.2 Sidebar Responsive Behavior

| Viewport | Sidebar State | Larghezza | Interazione |
|----------|-------------|-----------|-------------|
| **< 768px** (mobile) | **Hidden** → overlay drawer | Full-width | Hamburger in header attiva slide-in da sx, backdrop click chiude |
| **768–1023px** (tablet) | **Collapsed** (solo icone) | 64px | Hover espande temporaneamente, click naviga |
| **≥ 1024px** (desktop) | **Expanded + resizable** | 200–400px (drag handle) | Toggle collapse/expand via pulsante, resize drag handle |

### 4.3 Banners Contestuali (nell'ordine di rendering)

| Banner | Condizione | Aspetto |
|--------|-----------|---------|
| Impersonation | Admin impersonating another user | giallo/arancione con "Acting as {user}" |
| Grace Period | Subscription in grace period | warning con countdown |
| Trial | Piano trial attivo | info con giorni rimanenti |
| Limit Grace | Limiti piano in grace | warning con utilizzo |
| Network Offline | `navigator.onLine === false` | rosso con `role="alert"` |

---

## 5. Accessibilità (a11y) — Analisi Completa

### 5.1 Inventario Feature di Accessibilità

| # | Feature | Implementazione | Standard |
|---|---------|----------------|----------|
| 1 | **Skip link** | `<a href="#main-content" className="skip-link">` con CSS focus-reveal (`translate-y` da -100% a 0) | WCAG 2.4.1 |
| 2 | **LiveRegion** | `LiveRegionProvider` con dual region `aria-live="polite"` + `aria-live="assertive"`, hook `useAnnounce()` per annunci programmatici | WCAG 4.1.3 |
| 3 | **Focus trap** | Hook `useFocusTrap.js` — Tab/Shift+Tab cycling, Escape→close, auto-focus primo elemento, restore focus on close | WCAG 2.4.3 |
| 4 | **`role="dialog"` + `aria-modal`** | Tutti i modal, drawer, bottom sheet, command palette, drawer stack host, AI drawer | WCAG 4.1.2 |
| 5 | **`role="alertdialog"`** | `ConfirmDialog` con `aria-labelledby` + `aria-describedby` | WCAG 4.1.2 |
| 6 | **`role="alert"`** | `NetworkStatusBanner` per stato offline | WCAG 4.1.3 |
| 7 | **`role="menu"` + `role="menuitem"`** | `DropdownMenu` con navigazione Arrow keys, Enter, Escape, restore focus | WCAG 4.1.2 |
| 8 | **`role="listbox"` + `role="option"`** | Project switcher in `Header` con `aria-selected` | WCAG 4.1.2 |
| 9 | **`role="tooltip"`** | `Tooltip` component | WCAG 4.1.2 |
| 10 | **`aria-expanded` + `aria-haspopup`** | Tutti i trigger dropdown (user menu, project switcher, dropdown menu) | WCAG 4.1.2 |
| 11 | **`aria-label`** | Tutti i pulsanti icon-only (hamburger, search, close, theme, notifications, logout) | WCAG 4.1.2 |
| 12 | **`.sr-only`** | Classe utility per testo screen-reader-only | WCAG 1.3.1 |
| 13 | **Focus visible ring** | Global `focus-visible: outline 2px solid brand-400 + 2px offset` | WCAG 2.4.7 |
| 14 | **`prefers-contrast: more`** | Aumenta opacità testo basso contrasto a WCAG 4.5:1, bordi a 3:1, placeholder più scuri | WCAG 1.4.3 |
| 15 | **`prefers-reduced-motion: reduce`** | Azzera tutte le animazioni/transizioni a `0.01ms`, ferma shimmer skeleton | WCAG 2.3.3 |
| 16 | **Touch target sizing** | `@media (pointer: coarse)`: min 44×44px per button/link, 24×24px per checkbox/radio | WCAG 2.5.8 |
| 17 | **Keyboard navigation completa** | Command palette, dropdown, sidebar, tabs, modal tutti navigabili via tastiera | WCAG 2.1.1 |

### 5.2 Dettaglio Focus Trap (`useFocusTrap.js`)

```javascript
// Comportamento reale dal codice:
// 1. Trova tutti gli elementi focusabili nel container
// 2. Tab → focus next; Shift+Tab → focus prev (cycling)
// 3. Escape → chiama onClose callback
// 4. Mount → auto-focus primo elemento focusabile
// 5. Unmount → restore focus all'elemento precedentemente attivo
```

**Componenti che usano focus trap**: Drawer, Overlay, ConfirmDialog, BottomSheet, CommandPalette, DrawerStackHost, AIAssistantDrawer (7 componenti)

### 5.3 Dettaglio `prefers-contrast: more`

```css
@media (prefers-contrast: more) {
  /* Testo: opacità minima 90% per AA contrast */
  .text-base-400 { opacity: 0.9; }
  
  /* Bordi: opacità minima 60% per 3:1 ratio */
  .border-base-700 { opacity: 0.6; }
  
  /* Placeholder: opacità 70% */
  ::placeholder { opacity: 0.7; }
  
  /* Badge/pill: bordo aggiuntivo */
  .badge { border: 1px solid; }
}
```

### 5.4 Dettaglio `prefers-reduced-motion`

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
  }
  .skeleton-shimmer { animation: none; }
}
```

---

## 6. Sistema Animazioni

### 6.1 Framer Motion — Patterns Utilizzati

| Pattern | Config | Componenti |
|---------|--------|-----------|
| **AnimatePresence** | Mount/unmount con enter/exit | Ogni modal, drawer, dropdown, tooltip, bottom sheet, command palette |
| **Spring physics** | `type: 'spring', damping: 25–30, stiffness: 300–400` | Sidebar slide, drawer, mobile nav, bottom sheet |
| **Scale-in** | `scale: 0.95 → 1` + opacity | Dropdown, command palette, confirm dialog |
| **Slide-in right** | `x: '100%' → 0` | Drawer, AI drawer, detail panel |
| **Slide-in left** | `x: '-100%' → 0` | Sidebar mobile |
| **Slide-up** | `y: '100%' → 0` | Bottom sheet |
| **Layout animation** | `layoutId="mobile-nav-indicator"` | Indicatore attivo MobileBottomNav |
| **Drag** | `useMotionValue`, `useTransform`, d&d | SwipeableRow (swipe touch) |
| **whileTap** | `{ scale: 0.97 }` | pressAnimation preset |
| **Stagger** | `staggerChildren: 0.05, delayChildren: 0.02` | Container stagger reveal |

### 6.2 Micro-Interazioni (`microInteractions.js`)

Libreria di preset di animazione riutilizzabili:

| Preset | Tipo | Dettaglio |
|--------|------|-----------|
| `fadeInUp` | Variant | `opacity: 0, y: 10 → visible` con spring |
| `scaleIn` | Variant | `scale: 0.96, opacity: 0 → visible` |
| `staggerContainer` | Variant container | `staggerChildren: 0.05, delayChildren: 0.02` |
| `staggerItem` | Variant item | `opacity: 0, y: 10 → visible` spring |
| `pressAnimation` | Interaction | `whileTap: { scale: 0.97 }` |
| `tabSlideVariants` | Direction-aware | `enter/center/exit` con slide left/right |
| `useCountUp(target, duration)` | Hook | Contatore animato con easeOutQuart via rAF |
| `useScrollReveal()` | Hook | IntersectionObserver + fade reveal |
| `ScrollReveal` | Component | Wrapper per scroll-triggered animation |

### 6.3 Animazioni CSS (`index.css`)

| Animation | Keyframe | Utilizzo |
|-----------|---------|---------|
| `fade-in` | `opacity: 0 → 1` | Transizioni generiche |
| `slide-up` | `translateY(10px) + opacity: 0 → 0` | Entrata dal basso |
| `slide-down` | `translateY(-10px) → 0` | Entrata dall'alto |
| `scale-in` | `scale(0.95) + opacity → 1` | Entrata con scala |
| `shake` | `translateX(-4px/+4px)` oscillante | Errore campo form |
| `shimmer` | `translateX(-100% → 100%)` gradiente | Skeleton loader |

---

## 7. Responsive Design

### 7.1 Strategia

**Mobile-first** con breakpoint Tailwind standard:
- `sm`: 640px
- `md`: 768px
- `lg`: 1024px
- `xl`: 1280px
- `2xl`: 1536px

**Nessun hook `useMediaQuery`** trovato — l'app si affida interamente a:
- Classi CSS responsive (`md:hidden`, `lg:flex`)
- Componenti dedicati per mobile (`MobileBottomNav`, `BottomSheet`)
- CSS `@media (pointer: coarse)` per touch target

### 7.2 Adattamenti per Viewport

| Componente | Mobile (< 768px) | Tablet (768–1023px) | Desktop (≥ 1024px) |
|-----------|-------------------|--------------------|--------------------|
| **Sidebar** | Hidden → overlay drawer | Collapsed (icone sole) | Expanded + resizable (200-400px) |
| **MobileBottomNav** | Visibile (5 tab) | Hidden | Hidden |
| **Header search** | Solo icona | Barra ridotta | Barra completa |
| **Modal (Overlay)** | Bottom-aligned, full width | Centrato | Centrato con max-width |
| **BottomSheet** | Sheet dal basso | Modal centrato | Modal centrato |
| **Drawer** | Full-width | ~400px laterale | ~400px laterale |
| **Table/List** | Scroll orizzontale | Adattivo | Full-width |
| **KpiBar** | Stack verticale | Grid 2 col | Grid 3-4 col |

### 7.3 Touch Optimization

```css
@media (pointer: coarse) {
  /* Target minimo 44×44px per WCAG AAA */
  button, a, [role="button"] {
    min-height: 44px;
    min-width: 44px;
  }
  
  /* Checkbox/radio almeno 24×24px */
  input[type="checkbox"], input[type="radio"] {
    min-height: 24px;
    min-width: 24px;
  }
  
  /* Pulsanti icon-only con padding addizionale */
  .icon-btn { padding: 10px; }
}
```

### 7.4 Safe Area (Mobile)

AppShell applica `padding-bottom: env(safe-area-inset-bottom)` per iPhone con notch/dynamic island, particolarmente critico con `MobileBottomNav` fisso in basso.

---

## 8. Classe Preset Condivise (`classPresets.js`)

Sistema di costanti CSS per consistenza cross-modulo:

| Preset | Classi Tailwind | Utilizzo |
|--------|---------------|---------|
| `inputCls` | `w-full rounded-lg bg-base-800 border border-base-700 px-3.5 py-2.5 text-sm text-base-200 placeholder:text-base-500 focus:ring-2 focus:ring-brand-500/40 focus:border-brand-500 transition` | Tutti gli input form |
| `selectCls` | `rounded-lg bg-base-800 border border-base-700 px-3 py-2.5 text-sm ...` | Tutti i select |
| `textareaCls` | Come `inputCls` + `resize-y` | Textarea |
| `labelCls` | `text-xs font-semibold text-base-400 uppercase tracking-wider` | Label form |
| `btnPrimary` | `bg-brand-600 hover:bg-brand-500 text-white rounded-lg px-4 py-2.5 font-medium shadow-lg shadow-brand-600/20 transition` | CTA primari |
| `btnSecondary` | `bg-base-800 border border-base-700 hover:border-base-600 text-base-200 rounded-lg ...` | Azioni secondarie |
| `btnDanger` | `bg-danger-600 hover:bg-danger-500 text-white rounded-lg ...` | Azioni distruttive |
| `btnGhost` | `bg-transparent hover:bg-base-800/50 text-base-300 rounded-lg ...` | Azioni terziarie |
| `btnIcon` | `p-2 rounded-lg text-base-400 hover:text-base-200 hover:bg-base-800/50 ...` | Pulsanti icon-only |
| `tabCls` | `px-3 py-1.5 text-sm rounded-md transition` | Tab base |
| `tabActiveCls` | `tabCls + bg-brand-600/10 text-brand-400 font-medium` | Tab attivo |
| `tabInactiveCls` | `tabCls + text-base-400 hover:text-base-200 hover:bg-base-800` | Tab inattivo |
| `cardCls` | `bg-base-900 border border-base-700/50 rounded-xl p-6` | Card contenuto |
| `panelCls` | `bg-base-900/50 border border-base-700/50 rounded-xl` | Panel senza padding |
| `thCls` | `text-xs font-semibold text-base-400 uppercase tracking-wider px-4 py-3 text-left` | Header tabella |
| `tdCls` | `px-4 py-3 text-sm text-base-300` | Cella tabella |
| `trHoverCls` | `hover:bg-base-800/30 transition` | Riga tabella hover |

---

## 9. Virtual Scrolling & Performance UI

### 9.1 VirtualList Component

```
VirtualList.jsx (107 LOC)
├── @tanstack/react-virtual
├── Config: estimateSize configurabile, overscan=5
├── maxHeight configurabile (default: viewport)
├── onEndReached(callback, threshold=200px)  → Infinite scroll
├── onRefresh(callback)                       → Pull-to-refresh
│   └── Touch events: touchStart/Move/End
│   └── 40% dampening + 50px threshold
│   └── Spinner animation durante refresh
└── Render: items virtualizzati, padding top/bottom calcolato
```

### 9.2 Lazy Loading Assets

| Pattern | Componente | Tecnica |
|---------|-----------|---------|
| **Lazy images** | `LazyImage.jsx` | `IntersectionObserver` con `rootMargin: '200px'` + blur placeholder + fade-in |
| **Route chunk prefetch** | `Sidebar.jsx` | `onMouseEnter` su nav item → crea `<link rel="prefetch">` per il chunk JS del route |
| **Module lazy loading** | `WorkspaceShell` | 27 moduli workspace caricati via `dynamic import()` on-demand |
| **Suspense fallback** | `AppShell.jsx` | `<Suspense>` wrappa `<Outlet>` con skeleton (kpi-bar + table-row preset) |

### 9.3 Skeleton System (9 preset)

| Preset | Layout | Utilizzo |
|--------|--------|---------|
| `table-row` | Righe simulata con celle | Tabelle dati |
| `card` | Rettangolo con header + body | Card modules |
| `kpi-bar` | 4 blocchi con icona + numero | Dashboard KPI |
| `kanban-column` | Colonne con card | Board Kanban |
| `list-item` | Avatar + 2 righe testo | Liste contatti/chat |
| `text` | Righe testo larghezza variabile | Contenuto generico |
| `circle` | Cerchio pulsante | Avatar, icone |
| `rect` | Rettangolo pulsante | Immagini, chart |
| `chart` | Rettangolo grande | Grafici Recharts |

Animazione: shimmer CSS gradient traslato (`translateX(-100% → 100%)`)

---

## 10. Glass Morphism & Effetti Visivi

### 10.1 Glass Classes

```css
.glass {
  backdrop-filter: saturate(140%) blur(12px);
  background: linear-gradient(135deg, rgba(13,17,23,0.7), rgba(21,27,40,0.5));
  border: 1px solid rgba(255,255,255,0.06);
}

.glass-strong {
  backdrop-filter: saturate(160%) blur(20px);
  background: linear-gradient(135deg, rgba(13,17,23,0.85), rgba(21,27,40,0.7));
  border: 1px solid rgba(255,255,255,0.08);
}
```

### 10.2 Gradient Text

```css
.text-gradient {
  background: linear-gradient(135deg, var(--color-brand-400), var(--color-accent-400));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
```

### 10.3 Glow Effects

- Brand glow: `shadow-lg shadow-brand-600/20` sui CTA
- KPI glow: hover glow su card KPI
- Success/Danger glow: sui badge stato

---

## 11. Pattern UX Notabili

### 11.1 Command Palette (Ctrl+K)

```
CommandPalette.jsx (508 LOC)
├── Trigger: Ctrl+K / Cmd+K
├── Fuzzy search across:
│   ├── Pages/routes (React Router)
│   ├── Entities (API search: tasks, contacts, invoices, etc.)
│   └── Recent searches (localStorage)
├── Category tabs (Tutti, Pagine, Entità, Recenti)
├── Keyboard navigation: Arrow up/down, Enter, Escape
├── Preview panel (desktop): anteprima entità selezionata
└── State: Zustand store con history
```

### 11.2 Drawer Stack (Multi-pannello)

```
DrawerStackHost.jsx
├── Stack LIFO di detail drawer
├── Push: apri nuovo drawer → drawer precedente "push-back" con:
│   ├── scale(0.95)
│   ├── translateX(-20px)
│   └── opacity dimming progressivo (.15 per livello)
├── Pop: chiudi drawer corrente → ripristina precedente
└── Escape: chiude drawer in cima allo stack
```

### 11.3 Swipeable Row (Touch)

```
SwipeableRow.jsx (155 LOC)
├── Framer Motion drag x-axis
├── drag: 'x', dragConstraints, dragElastic: 0.3
├── Swipe left: azione distruttiva (delete) — threshold -80px
├── Swipe right: azione positiva (approve) — threshold +80px
├── Background reveal: rosso (left) / verde (right)
└── Snap-back: spring animation se non supera threshold
```

### 11.4 Pull-to-Refresh

```
VirtualList pull-to-refresh:
├── touchStart: record Y position
├── touchMove: calcola delta con 40% dampening
│   └── Se scrollTop === 0 e delta > 0: mostra spinner
├── touchEnd: se delta > 50px threshold → fire onRefresh()
│   └── Altrimenti: snap-back
└── Spinner: indicatore rotante con transformY proporzionale
```

### 11.5 Toast con Undo

```
ToastUndo.jsx (111 LOC)
├── Appare in basso-centro
├── Messaggio + pulsante "Annulla" 
├── Barra countdown (5 secondi): width 100% → 0%
├── Click Undo: cancella azione, chiudi toast
└── 5s timeout: esegui azione definitiva
```

---

## 12. Admin Panel — UI Separata

### 12.1 Differenze dall'App

| Aspetto | App (app.kacinka.it) | Admin (admin.kacinka.it) |
|---------|---------------------|-------------------------|
| Design system | Tailwind v4 + @theme completo | Tailwind base, meno token custom |
| Tema dark/light | Sì, toggle utente | Solo dark |
| Glass morphism | Sì | No |
| Framer Motion | Estensivo | Minimale |
| Componenti custom | 54 componenti | Inline styling |
| Command Palette | ✅ Ctrl+K | ❌ Non presente |
| AI Drawer | ✅ | ❌ Non presente |
| Skeleton loader | 9 preset | Nessuno |
| a11y features | 17 feature | Basilare |
| Mobile optimization | Bottom nav + responsive | Non ottimizzato mobile |

### 12.2 Pagine Admin più Grandi (LOC)

| Pagina | LOC | Complessità UI |
|--------|-----|---------------|
| `CommercePage.jsx` | 796 | 6 tab (piani, hosting, addon, bundle, prodotti, coupon), ciascuna con CRUD completo |
| `UserDetailPage.jsx` | 788 | Tab dettaglio utente con override limiti, note, tag, impersonation |
| `AdminChatPage.jsx` | 760 | Chat real-time con polling, typing indicator, reactions, pin |
| `PlatformInvoicesPage.jsx` | 642 | Multi-formato invoice, status SDI, OSS dashboard |
| `SubscriptionsPage.jsx` | 459 | Cancel, extend, change plan, coupon, trial |
| `UsersPage.jsx` | 441 | 10+ filtri, bulk actions, CSV export |
| `JobQueuePage.jsx` | 438 | Stats, throughput, filters, purge/release |
| `DunningPage.jsx` | 373 | Grace period, force retry, timeline |

---

## 13. Riepilogo e Metriche

### 13.1 Metriche Design System

| Metrica | Valore |
|---------|--------|
| Componenti UI reusabili | 54 |
| LOC componenti UI totali | ~7,700+ |
| Varianti Button | 7 varianti × 8 taglie |
| Preset skeleton | 9 |
| Preset classe CSS | 17 (classPresets.js) |
| Color token (dark) | 35+ (10 base + 9 brand + 5 accent + 3×4 semantic) |
| Override tema light | ~60+ classi |
| Feature accessibilità | 17 |
| Presets micro-interazione | 10+ |
| Animazioni CSS keyframe | 6 |
| Breakpoint responsive | 5 (sm, md, lg, xl, 2xl) |

### 13.2 Punti di Forza UX

| # | Aspetto | Dettaglio |
|---|---------|-----------|
| 1 | **Accessibilità eccellente** | Focus trap, skip link, LiveRegion, ARIA roles, reduced motion, high contrast, touch targets WCAG |
| 2 | **Animation polish** | Framer Motion spring physics, micro-interactions, layout animations, stagger reveal |
| 3 | **Command Palette** | Navigazione rapida cross-entità via Ctrl+K |
| 4 | **Drawer Stack** | UX multi-pannello sofisticata con push-back animation |
| 5 | **Responsive completo** | Mobile bottom nav, bottom sheet, sidebar 3-mode, safe area |
| 6 | **Design system coerente** | classPresets.js garantisce uniformità cross-modulo |
| 7 | **Dark/Light** | Toggle completo con 60+ override |
| 8 | **Virtual scrolling** | Pull-to-refresh + infinite scroll + virtualizzazione |
| 9 | **Glass morphism** | Estetica moderna con backdrop-filter |
| 10 | **Skeleton loading** | 9 preset per UX perceived performance |

### 13.3 Criticità UX

| # | Criticità | Severità | Dettaglio |
|---|----------|----------|-----------|
| 1 | **Admin non responsive** | 🟡 Media | Pannello admin non ottimizzato per mobile |
| 2 | **Admin senza design system** | 🟡 Media | Styling inline, nessun component reuse dall'app |
| 3 | **No dark/light admin** | 🟠 Bassa | Solo tema dark nell'admin |
| 4 | ~~**RTL non testato**~~ | ✅ Risolto | RTL CSS implementato e testato per arabo (Fase 5) |
| 5 | ~~**Bundle locale 250-515 KB**~~ | ✅ Risolto | Namespace split, ~30KB first load (Fase 8) |
| 6 | **No storybook** | 🟠 Bassa | Componenti senza documentazione interattiva |

---

## Aggiornamento Post-Roadmap (v3.0)

### Miglioramenti UX/UI Implementati
- **ARIA Coverage**: Tutti i 54 componenti UI base con attributi `aria-*` (labels, roles, live regions)
- **Keyboard Navigation**: Tab focus, skip links, focus trapping nei modali
- **Skeleton Loading**: Migliorati skeleton loader per dashboard e liste
- **SWR UX**: Stale-while-revalidate per navigazione fluida senza flash di caricamento
- **RTL Layout**: CSS dedicato per lingue RTL (arabo) con mirror automatico
- **WCAG 2.1 AA**: Verificato con axe-core — 0 violazioni critiche sulle pagine core
- **Support Drawer**: Floating support chat widget accessibile da ogni pagina

