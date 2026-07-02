# Hummingbird Clinical Dashboard — Design System

Brand-coded token layer + flattened MUI theme + polished StatCard.
Stack: **Vite · React (JSX) · Tailwind v3 · MUI (kept for DataGrid/forms)**.

Brand colors extracted from hummingbirdbioscience.com:
- **Indigo `#333674`** — workhorse accent. Allowed on: primary buttons, active nav, links, primary chart series, focus rings, selected rows, progress bars.
- **Magenta `#AF3791`** — the rare pop. Allowed on: ONE hero moment, one emphasized metric, second chart series. Nothing else.
- **Azure `#2D64A5`** — info / third chart series.
- Everything else is grayscale. Color only where it means something.

---

## 1. `src/styles/tokens.css`

Single source of truth. Global CSS reads it natively, Tailwind maps it in config,
MUI reads it via `var()` in style overrides.

```css
:root {
  /* ── Surfaces ─────────────────────────────────────────────
     App bg is the brand off-white (whisper of indigo), cards
     are pure white. That ~2% difference is what lets a 1px
     hairline border separate cards with NO shadow. */
  --bg:          #F9F9FD;
  --surface:     #FFFFFF;
  --surface-alt: #F1F2F8;   /* table headers, hover fills, icon chips */

  /* ── Hairline borders (indigo-tinted) ───────────────────── */
  --border:        #E5E6F0;
  --border-strong: #D2D4E2;  /* hover state for interactive cards */

  /* ── Text — near-black leaning indigo, never pure #000 ──── */
  --text-strong: #1C1E33;   /* headings, big stat numbers */
  --text:        #34374D;   /* body */
  --text-muted:  #696C84;   /* labels, secondary text */
  --text-subtle: #9B9DB2;   /* hints, placeholders, disabled */

  /* ── BRAND indigo — data + actions only ─────────────────── */
  --brand:       #333674;
  --brand-hover: #2A2C61;
  --brand-fg:    #FFFFFF;
  --brand-tint:  rgba(51, 54, 116, 0.10);   /* active nav bg, selected row */

  /* ── ACCENT magenta — the one hero pop ──────────────────── */
  --accent:       #AF3791;
  --accent-hover: #972F7D;
  --accent-fg:    #FFFFFF;
  --accent-tint:  rgba(175, 55, 145, 0.10);

  /* ── Azure — info / 3rd chart series ────────────────────── */
  --info:      #2D64A5;
  --info-tint: rgba(45, 100, 165, 0.10);

  /* ── Status — ONLY for status, never decoration ─────────── */
  --success: #12B76A; --success-text: #027A48; --success-tint: rgba(18, 183, 106, .12);
  --warning: #F79009; --warning-text: #B54708; --warning-tint: rgba(247, 144, 9, .12);
  --error:   #D92D20; --error-text:   #B42318; --error-tint:   rgba(217, 45, 32, .12);

  /* ── Radius scale ────────────────────────────────────────── */
  --radius-sm: 6px;    /* pills, small chips */
  --radius-md: 10px;   /* buttons, inputs, icon chips */
  --radius-lg: 14px;   /* cards */
  --radius-xl: 20px;   /* hero card, modals */

  /* ── Spacing (4px base) — pick from this, never random px ── */
  --space-1: 4px;  --space-2: 8px;  --space-3: 12px; --space-4: 16px;
  --space-5: 20px; --space-6: 24px; --space-8: 32px; --space-10: 40px;

  /* ── ONE shadow, floating things only (menus/modals), NEVER cards ── */
  --shadow-pop: 0 1px 2px rgba(28, 30, 51, .04), 0 4px 12px rgba(28, 30, 51, .08);
}

/* Base — put in the same file or index.css after the :root block */
html {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
body {
  background: var(--bg);
  color: var(--text);
  font-family: Inter, system-ui, -apple-system, sans-serif;
}
/* Digits line up in columns — uneven number widths are a subtle
   "amateur" signal in a data UI */
.tabular { font-variant-numeric: tabular-nums; }

/* Respect users who turn animation off (clinical = accessibility matters) */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## 2. `tailwind.config.js` (v3) — map tokens to utilities

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./index.html", "./src/**/*.{js,jsx}"],
  theme: {
    extend: {
      colors: {
        bg:            "var(--bg)",
        surface:       "var(--surface)",
        "surface-alt": "var(--surface-alt)",
        border:        "var(--border)",
        "border-strong": "var(--border-strong)",
        strong:  "var(--text-strong)",
        body:    "var(--text)",
        muted:   "var(--text-muted)",
        subtle:  "var(--text-subtle)",
        brand:   { DEFAULT: "var(--brand)", hover: "var(--brand-hover)" },
        accent:  { DEFAULT: "var(--accent)", hover: "var(--accent-hover)" },
        info:    "var(--info)",
      },
      borderRadius: {
        sm: "var(--radius-sm)", md: "var(--radius-md)",
        card: "var(--radius-lg)", xl: "var(--radius-xl)",
      },
      boxShadow: { pop: "var(--shadow-pop)" },
      fontFamily: { sans: ["Inter", "system-ui", "sans-serif"] },
    },
  },
  plugins: [],
};
```

Now `bg-surface`, `border-border`, `text-strong`, `text-muted`, `bg-brand`,
`rounded-card`, `shadow-pop` all resolve to the same tokens MUI uses.

---

## 3. `src/theme.js` — flattened MUI theme (kills the Material look)

De-Materials every existing MUI component (DataGrid included) with zero rewrites:
no elevation shadows, no UPPERCASE buttons, no Roboto, 10px radius, hairline borders.

```js
import { createTheme } from "@mui/material/styles";

// MUI needs literal hex for its contrast-text math (it can't resolve a
// var() string in palette). Keep these 4 in sync with tokens.css.
// Everything in `components` below uses var() and stays live-linked.
export const theme = createTheme({
  palette: {
    primary:    { main: "#333674" },   // --brand
    secondary:  { main: "#AF3791" },   // --accent
    info:       { main: "#2D64A5" },   // --info
    error:      { main: "#D92D20" },
    background: { default: "#F9F9FD", paper: "#FFFFFF" },
    text:       { primary: "#1C1E33", secondary: "#696C84" },
    divider:    "#E5E6F0",
  },
  shape: { borderRadius: 10 },
  typography: {
    fontFamily: "Inter, system-ui, sans-serif",
    button: { textTransform: "none", fontWeight: 600 },  // kill UPPERCASE
  },
  components: {
    MuiPaper: {
      defaultProps: { elevation: 0 },
      styleOverrides: {
        root: {
          backgroundImage: "none",
          border: "1px solid var(--border)",
          boxShadow: "none",
        },
      },
    },
    MuiCard: {
      defaultProps: { elevation: 0 },
      styleOverrides: {
        root: { border: "1px solid var(--border)", borderRadius: 14 },
      },
    },
    MuiButton: {
      defaultProps: { disableElevation: true },
    },
    MuiSelect: {
      styleOverrides: { root: { borderRadius: 10 } },
    },
    MuiAutocomplete: {
      styleOverrides: {
        paper: { boxShadow: "var(--shadow-pop)", border: "1px solid var(--border)" },
      },
    },
    // DataGrid — flatten border, quiet the header row
    MuiDataGrid: {
      styleOverrides: {
        root: { border: "1px solid var(--border)", borderRadius: 14 },
        columnHeaders: { backgroundColor: "var(--surface-alt)" },
      },
    },
  },
});
```

Wire-up in `main.jsx`:

```jsx
import { ThemeProvider, CssBaseline } from "@mui/material";
import { theme } from "./theme";
import "./styles/tokens.css";

<ThemeProvider theme={theme}>
  <CssBaseline />
  <App />
</ThemeProvider>
```

**Rule that keeps the codebase sane:** one element, one system. Never MUI `sx`
AND Tailwind classes on the same element — they fight over specificity and CSS
injection order. Tokens are shared; styling authority per element is not.

---

## 4. `src/components/ui/StatCard.jsx` — refined

Replaces the old `Box > Card > CardBody > Typography` nest with one component.
Five cards, one shell: counts are pure grayscale; rates get a thin indigo bar
(color only on data). What each refinement is doing is annotated below the code.

```jsx
import { useEffect, useState } from "react";

/**
 * StatCard — clinical stat tile.
 *
 * props:
 *  label    string            "Total Patients"
 *  value    string | number   128 | "62%"
 *  icon     Lucide component  optional
 *  percent  number            0–100 → renders the indigo bar (rate cards)
 *  hint     string            small context line, e.g. "of 128 enrolled"
 *  hero     boolean           ONE card may set this → magenta bar + tinted chip
 *  loading  boolean           skeleton state
 *  onClick  function          optional → card becomes a real button
 */
export function StatCard({
  label, value, icon: Icon, percent, hint,
  hero = false, loading = false, onClick, className = "",
}) {
  const hasBar = typeof percent === "number";
  const clamped = hasBar ? Math.min(100, Math.max(0, percent)) : 0;

  // Bar animates 0 → value on mount (width transition needs a start frame)
  const [barWidth, setBarWidth] = useState(0);
  useEffect(() => {
    if (!hasBar) return;
    const raf = requestAnimationFrame(() => setBarWidth(clamped));
    return () => cancelAnimationFrame(raf);
  }, [hasBar, clamped]);

  const barColor = hero ? "bg-accent" : "bg-brand";
  const chip = hero
    ? "bg-accent/10 text-accent"
    : "bg-surface-alt text-muted";

  const Tag = onClick ? "button" : "div";

  if (loading) {
    return (
      <div className={`rounded-card border border-border bg-surface p-5 ${className}`}>
        <div className="mb-4 h-9 w-9 animate-pulse rounded-md bg-surface-alt" />
        <div className="h-7 w-16 animate-pulse rounded bg-surface-alt" />
        <div className="mt-2 h-3.5 w-24 animate-pulse rounded bg-surface-alt" />
      </div>
    );
  }

  return (
    <Tag
      onClick={onClick}
      className={[
        "group flex flex-col rounded-card border border-border bg-surface p-5 text-left",
        "transition-all duration-200 ease-out",
        onClick
          ? "cursor-pointer hover:-translate-y-0.5 hover:border-border-strong hover:shadow-pop " +
            "focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-brand/40 focus-visible:ring-offset-2"
          : "hover:border-border-strong",
        className,
      ].join(" ")}
    >
      {Icon && (
        <span
          className={`mb-4 inline-flex h-9 w-9 items-center justify-center rounded-md ${chip}
                      transition-colors duration-200 group-hover:text-strong`}
        >
          <Icon size={18} strokeWidth={2} aria-hidden />
        </span>
      )}

      <span className="tabular text-[28px] font-semibold leading-none tracking-[-0.02em] text-strong">
        {value}
      </span>

      <span className="mt-1.5 text-[13px] font-medium leading-snug text-muted">
        {label}
      </span>

      {hasBar && (
        <div
          className="mt-4 h-1.5 w-full overflow-hidden rounded-full bg-surface-alt"
          role="progressbar"
          aria-valuenow={clamped}
          aria-valuemin={0}
          aria-valuemax={100}
          aria-label={label}
        >
          <div
            className={`h-full rounded-full ${barColor} transition-[width] duration-700 ease-out`}
            style={{ width: `${barWidth}%` }}
          />
        </div>
      )}

      {hint && (
        <span className="mt-2 text-[12px] leading-snug text-subtle">{hint}</span>
      )}
    </Tag>
  );
}
```

### Usage — your five cards

```jsx
import { Users, Activity, CheckCircle2, Gauge, Target } from "lucide-react";
import { StatCard } from "./components/ui/StatCard";

const stats = [
  { label: "Total Patients",   value: 128,   icon: Users },
  { label: "Currently Active", value: 47,    icon: Activity,     hint: "of 128 enrolled" },
  { label: "Ever Had PR+",     value: 34,    icon: CheckCircle2, hint: "≥ partial response" },
  { label: "Overall DCR",      value: "62%", icon: Gauge,        percent: 62, hint: "disease control rate" },
  { label: "Overall ORR",      value: "27%", icon: Target,       percent: 27, hint: "objective response rate" },
];

<div className="grid grid-cols-2 gap-4 sm:grid-cols-3 xl:grid-cols-5">
  {stats.map((s) => <StatCard key={s.label} {...s} />)}
</div>
```

### What each refinement is doing (the little details)

| Detail | Why it reads "sleek" |
|---|---|
| `tracking-[-0.02em]` on the number | Big numerals need slight negative letter-spacing or they look loose; every polished dashboard does this |
| `tabular-nums` | Digits are equal-width → values don't jitter or misalign across the row |
| Bar animates 0→value on mount, 700ms ease-out | The one moment of motion; draws the eye to the two rate cards exactly once |
| `hover:-translate-y-0.5` + `shadow-pop` **only when clickable** | Motion = affordance. Static cards get a border-darken only; lift implies "press me", so it's reserved for real buttons |
| `focus-visible:ring-brand/40` | Keyboard focus ring in brand indigo — a11y and polish in one move |
| Icon chip: neutral gray, brightens to `text-strong` on hover | Icons aid scanning but never carry per-card color — that was the "random colors" problem |
| Skeleton `loading` state | Matches exact layout dimensions → no layout shift when data arrives |
| `role="progressbar"` + aria values | Screen readers announce "Overall DCR, 62%" — clinical tools get audited for this |
| `prefers-reduced-motion` kill-switch (in tokens.css) | Animations collapse to instant for users who opt out |
| `Tag = button` when `onClick` | A clickable div is an a11y bug; a real `<button>` gets keyboard + screen-reader behavior for free |
| Grid `2 → 3 → 5` columns | 5 identical cards wrap awkwardly at 4-wide; skipping 4 keeps rows balanced at every breakpoint |

### Rules recap

1. All five cards share one grayscale shell — variety comes from what the metric *is* (count vs rate), not decoration.
2. Indigo appears only on the rate bars. Magenta only if you promote ONE card with `hero` (e.g. the primary endpoint) — never more than one.
3. Green/amber/red are reserved for actual status (e.g. a future ▲/▼ delta badge vs last timepoint), never card decoration.
