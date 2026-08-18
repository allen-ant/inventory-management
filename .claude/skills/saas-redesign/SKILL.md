---
name: saas-redesign
description: Redesign a Vue 3 application's UI into a modern SaaS-style interface — vertical left sidebar navigation (replacing any top nav bar), a consistent spacing/typography system, and a polished professional look. Use when the user asks to modernize, redesign, or "SaaS-ify" the app UI, add a sidebar, or fix inconsistent spacing/styling. Works incrementally: app shell first, then view-by-view.
---

# SaaS-Style UI Redesign for Vue 3

Transform a Vue 3 app's UI into a modern SaaS interface (think Linear, Stripe Dashboard, Vercel): left sidebar nav, quiet surfaces, tight typographic hierarchy, generous consistent spacing.

Follow the phases in order. Do NOT restyle views before the shell and design tokens exist — otherwise every view gets restyled twice.

## Phase 0 — Survey (read, don't write)

1. Find the current layout root (usually `App.vue`): where the nav lives (top bar? inline links?), where `<router-view>` renders, and where global styles are defined.
2. List all routed views (`main.js` / router file) and shared components. Note views NOT in the router — flag them to the user rather than silently styling dead code.
3. Inventory existing styles: global CSS, per-component `<style scoped>`, any existing CSS variables. Note the current color palette — if the project has a documented design system (check CLAUDE.md), **respect it**; the redesign changes layout and consistency, not brand colors, unless the user asks.
4. Check project rules that constrain how you work (e.g., a CLAUDE.md mandate to delegate `.vue` edits to a subagent) and follow them.

## Phase 1 — Design tokens

Define CSS custom properties ONCE at `:root` in the global stylesheet (or App.vue's unscoped style block). Everything downstream uses tokens — never hardcoded values.

```css
:root {
  /* spacing — 4px base scale; use ONLY these steps */
  --sp-1: 4px; --sp-2: 8px; --sp-3: 12px; --sp-4: 16px;
  --sp-5: 20px; --sp-6: 24px; --sp-8: 32px; --sp-10: 40px; --sp-12: 48px;

  /* surfaces & ink (adapt hues to the project's palette) */
  --bg: #f8fafc;            /* app background */
  --surface: #ffffff;       /* cards, sidebar */
  --ink: #0f172a;           /* primary text */
  --muted: #64748b;         /* secondary text */
  --line: #e2e8f0;          /* borders, dividers */
  --accent: /* project's primary */;
  --accent-soft: /* 8-12% tint of accent for active states */;

  /* shape & depth */
  --radius: 8px; --radius-lg: 12px;
  --shadow-sm: 0 1px 2px rgb(0 0 0 / .05);
  --shadow-md: 0 4px 12px rgb(0 0 0 / .08);

  /* type scale */
  --text-xs: 12px; --text-sm: 13.5px; --text-base: 14.5px;
  --text-lg: 17px; --text-xl: 21px; --text-2xl: 26px;

  /* layout */
  --sidebar-w: 240px;
}
```

## Phase 2 — App shell (the big structural change)

Rebuild the layout root as a two-column shell. The sidebar replaces the top nav entirely — do not leave a redundant top nav behind.

```
┌──────────┬────────────────────────────────┐
│ sidebar  │ topbar (page title · actions · │
│ (fixed,  │  profile/search if they exist) │
│ 240px)   ├────────────────────────────────┤
│          │ <router-view> in a padded,     │
│ logo     │ max-width container            │
│ nav      │                                │
│ ─────    │                                │
│ footer   │                                │
│ (user)   │                                │
└──────────┴────────────────────────────────┘
```

Structure:
- `.app-shell { display: grid; grid-template-columns: var(--sidebar-w) 1fr; min-height: 100vh; }`
- Sidebar: `position: sticky; top: 0; height: 100vh; overflow-y: auto; background: var(--surface); border-right: 1px solid var(--line);` — flat surface + hairline border, NOT a drop shadow.
- Sidebar anatomy top→bottom: product name/logo (`--text-lg`, weight 650) → nav sections → `margin-top: auto` footer (user/profile widgets if the app has them — RELOCATE existing profile menus/language switchers here, don't duplicate).
- Nav items: `router-link` with icon (inline SVG, 18px, `stroke: currentColor`) + label. `padding: var(--sp-2) var(--sp-3); border-radius: var(--radius); color: var(--muted); font-size: var(--text-sm); font-weight: 500; gap: var(--sp-3);`
  - Hover: `background: var(--bg); color: var(--ink);`
  - Active (`router-link-active`, use `exact` semantics for `/`): `background: var(--accent-soft); color: var(--accent); font-weight: 600;`
- Main column: `background: var(--bg)`. Content container: `max-width: 1200px; padding: var(--sp-8); margin: 0 auto;` (identical on every view — this alone kills most spacing inconsistency).
- Existing top-bar content that isn't navigation (search, profile, notifications) moves to a slim main-column topbar or the sidebar footer — never deleted silently.

## Phase 3 — Component patterns (apply per view)

Restyle views ONE AT A TIME, verifying each in the browser before the next. Every view gets the same skeleton:

1. **Page header**: title (`--text-2xl`, weight 700, letter-spacing -0.01em) + one-line description (`--muted`, `--text-sm`) + right-aligned primary actions. `margin-bottom: var(--sp-8)`.
2. **Cards**: `background: var(--surface); border: 1px solid var(--line); border-radius: var(--radius-lg); padding: var(--sp-6);` — border + `--shadow-sm`, never heavy shadows. Card grids: `display: grid; gap: var(--sp-4);`
3. **Stat/KPI tiles**: label (`--text-xs`, uppercase, letter-spacing .06em, `--muted`) above value (`--text-2xl`, weight 700, `font-variant-numeric: tabular-nums`).
4. **Tables**: no vertical borders; header row `--text-xs` uppercase muted; row hover `background: var(--bg)`; cell padding `var(--sp-3) var(--sp-4)`; numeric columns right-aligned with tabular-nums; wrap in a card with `overflow-x: auto`.
5. **Buttons**: primary = accent bg, white text; secondary = `--surface` bg + `--line` border; both `border-radius: var(--radius); padding: var(--sp-2) var(--sp-4); font-weight: 500;` with hover + `:focus-visible` ring (`outline: 2px solid var(--accent); outline-offset: 2px`).
6. **Filter bars / toolbars**: single row card, `gap: var(--sp-3)`, selects styled like secondary buttons.
7. **Modals**: overlay `rgb(15 23 42 / .4)`; panel `--radius-lg`, `--shadow-md`, `padding: var(--sp-6)`; header/body/footer separated by `--sp-6`.
8. **Status indicators**: soft pills — tinted background (~10%), strong text color, `--text-xs`, weight 600, `border-radius: 999px; padding: 2px var(--sp-2)`.

## Phase 4 — Polish pass

- Transitions: `transition: background-color .15s, color .15s, border-color .15s, box-shadow .15s` on interactive elements. Nothing animates position or size except deliberate reveals.
- Empty and loading states for every data view (muted centered message in the card, never a bare blank).
- Responsive: below 900px the sidebar collapses to icons-only (64px) or an overlay drawer — pick one, apply everywhere.
- Consistency sweep: grep for hardcoded px paddings/margins/colors in `<style scoped>` blocks and replace with tokens. This is the highest-leverage step for "consistent spacing".
- Keep any project style rules (e.g., "no emojis in UI") intact.

## Phase 5 — Verify

1. Run the dev server and open every route in the browser (browser tools/screenshots if available).
2. Check: sidebar active state correct on each route (including `/` not falsely matching everything), no horizontal scroll at 1280px and 900px, modals still open/close, filters still fire API calls, dark backgrounds didn't break contrast.
3. Show the user before/after screenshots per view, not just a text summary.

## Non-goals

Do not change business logic, API calls, data flow, or component structure beyond what the layout demands. A redesign PR that also refactors logic is unreviewable — if you find logic bugs while restyling, report them, don't fix them in the same change.
