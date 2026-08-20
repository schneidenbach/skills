---
name: spencer-approved-frontends
description: >
  Spencer's house style for UIs. DO NOT INVOKE THIS SKILL UNLESS SPECIFICALLY ASKED. Use whenever Spencer asks for a frontend, page, screen, component, dashboard, app shell, modal, form, table, or any kind of web visual — including phrasings like "build a UI for X", "throw together a screen", "make a dashboard", "I need a quick app". Also use when starting a brand-new React project from scratch on this machine. Overrides any general "frontend-design" skill: Spencer dislikes the default shiny-AI aesthetic and wants tight, neutral, dense, system-font UIs that look like the due-diligence and security-review apps. If Spencer is editing an existing repo with a clearly different stack (e.g. Next.js with shadcn already wired up), follow that repo's conventions, but still apply Spencer's typography, density, and tone preferences from this skill.
---

# Spencer-Approved Frontends

The default "AI-generated frontend" look is wrong for Spencer. Glassy gradients, oversized hero text, generous padding, drop shadows on every card, blue-to-purple linear gradients, rounded-2xl everything, lucide icons everywhere — none of that. Spencer ships dense, neutral, system-font UIs that read like serious internal tooling. Think Linear, Notion's admin views, the macOS settings panel — not a SaaS landing page.

When in doubt, fewer pixels of decoration, smaller text, tighter spacing, and one neutral color ramp.

## When this overrides other skills

If `frontend-design` (or anything similar) is also active, this skill wins on style decisions. The other skill's process is fine; the visual defaults are not. Spencer's frontends look like the `due-diligence` and `security-review` repos in `~/Repos/`. If you have access to those repos and the user is starting something new, you may peek at them as ground truth. Otherwise, follow the patterns in this file.

## Stack defaults for new projects

For a new project from scratch, start here unless Spencer says otherwise:

- **React 18** with **Vite 5+** (not Next.js, not CRA)
- **TypeScript** strict mode + `noUncheckedIndexedAccess` + `noImplicitOverride`
- **Tailwind CSS 3.4** (NOT Tailwind 4 by default — Spencer's repos pin to 3.4)
- **npm** (not pnpm/yarn/bun)
- **No component library.** No shadcn, no Radix, no MUI, no Mantine, no Chakra, no Headless UI. Build the few primitives you need by hand (Modal, Badge, Button, InlinePicker).
- **No icon library** by default. Use small SVG inline or text glyphs (`::` for a drag handle, `×` for close). If Spencer asks for icons, ask which library before adding one.
- **No form library.** `useState` + a plain `<form onSubmit>` is enough.
- **No state library.** `useState` / `useReducer` / `useContext` is enough until proven otherwise. No Redux, Zustand, Jotai, Recoil.
- **No data fetching library.** Plain `fetch` wrapped in a small `lib/api.ts` module. No React Query, SWR, axios.
- **No animation library.** No framer-motion, no react-spring. CSS `transition` on hover/focus is the whole animation budget.
- **No router library** for small apps — manual `window.location.pathname` parsing in `App.tsx`. If the app grows past ~3 routes, ask Spencer if it's time for a router.

If Spencer asks for a different stack, follow Spencer. But propose this stack first.

## Project layout

```
src/
  App.tsx               root, manual routing
  main.tsx              entry
  index.css             global tokens + utilities
  components/           one .tsx per component, PascalCase
  lib/                  cx.ts, format.ts, api.ts, hooks
shared/                 types shared with backend, aliased as @shared/*
```

No barrel exports. No `pages/` directory for small apps. No co-located CSS files — Tailwind utilities only.

## The visual contract

Every Spencer UI obeys these defaults. Memorize them.

### Color tokens

Define these in `tailwind.config.ts` and `index.css` as CSS variables. Do not skip this step — Tailwind's default neutral palette is too cool and too saturated.

```ts
// tailwind.config.ts (excerpt)
colors: {
  surface: {
    DEFAULT: "#ffffff",
    soft:    "#fafafa",
    deep:    "#f5f5f4",
    hover:   "#f4f4f4",
  },
  ink: {
    DEFAULT: "#0a0a0a",
    soft:    "#404040",
    muted:   "#737373",
    faint:   "#a3a3a3",
  },
  line: {
    DEFAULT: "#eaeaea",
    strong:  "#d4d4d4",
  },
  // domain-specific severity ramp — rename per project
  // e.g. "risk" for due-diligence, "sev" for security-review,
  // "status" or "tier" for whatever the app is about
  risk: {
    critical: "#7f1d1d",
    high:     "#b91c1c",
    medium:   "#b45309",
    low:      "#1d4ed8",
  },
},
```

Pair each semantic color with a *soft background variant* via CSS variable, e.g. `--risk-high-soft`, used as `bg-[var(--risk-high-soft)]`. Soft backgrounds + saturated text = the badge look.

### Typography

```ts
fontFamily: {
  sans: ["-apple-system", "BlinkMacSystemFont", "'SF Pro Text'", "'Segoe UI'", "Roboto", "Helvetica", "Arial", "sans-serif"],
  mono: ["'SFMono-Regular'", "'SF Mono'", "Menlo", "Consolas", "monospace"],
},
```

System fonts only. No Google Fonts, no Inter, no Geist, no font imports.

**Allowed sizes** (use `text-[Npx]` arbitrary values where Tailwind defaults don't fit):

- `11px` — tracked labels, badges, dense buttons
- `12px` — primary buttons, dense controls
- `12.5px` — secondary buttons
- `13px` — body text (this is the base — the `<body>` itself is `font-size: 14px` with `line-height: 1.5`, but most app chrome is 13px)
- `15px` — modal titles, section headings

That is the whole scale. No `text-base`, no `text-lg`, no `text-2xl`, no hero text. If you need emphasis, use `font-semibold tracking-tight` at 15px, not bigger type.

For form/section labels, use this utility (defined in `index.css`):

```css
.tracked-label {
  font-size: 11px;
  font-weight: 500;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--ink-muted);
}
```

### Spacing & density

Spencer's UIs are *dense*. Allowed spacing values, in order of frequency:

- `gap-1`, `gap-1.5`, `gap-2`, `gap-3`
- `px-2`, `px-2.5`, `px-3`
- `py-0.5` (badges/pills), `py-1`, `py-1.5`, `py-2`

Do NOT default to `p-4`, `p-6`, `p-8`, `gap-4`, `gap-6`. Those exist for a different design system.

Buttons end up ~28–32px tall. Badges end up ~18–20px tall. That's correct.

### Borders, radii, shadows

- 1px borders everywhere there's a divider: `border border-line` or `border-b border-line`.
- Radii: `rounded-md` for inputs, `rounded-lg` for buttons and cards, `rounded-xl` for modals, `rounded-full` for pills/badges. No `rounded-2xl`.
- **No shadows on regular elements.** Spencer's `index.css` includes `.clean-app * { box-shadow: none; }`. The single exception is the modal: `shadow-[0_20px_60px_-24px_rgba(0,0,0,0.25)]`.
- **No gradients.** Not on backgrounds, not on buttons, not on text. Flat fills only.

### Animation

Hover and focus transitions only, via Tailwind's default `transition` class. No `animate-pulse`, no `animate-spin` (unless it's a real loading spinner inside an SVG), no entrance/exit animations, no framer-motion.

## The four primitives you actually need

Ship these four hand-rolled components and you have 80% of any app. Copy these patterns; do not import from a library.

### `lib/cx.ts`

```ts
export function cx(...parts: Array<string | false | null | undefined>): string {
  return parts.filter(Boolean).join(" ");
}
```

This replaces `clsx` and `classnames`. Do not install either. `cx` is what every component uses for conditional classes.

### Button (extracted const, not a component)

In most files you don't even build a `<Button />` component. You hoist the class string to a const at the top of the file and use it on a `<button>`:

```tsx
const primaryButtonClass =
  "inline-flex items-center justify-center rounded-lg bg-ink px-2.5 py-1.5 text-[12px] font-medium text-white transition hover:bg-ink-soft disabled:cursor-not-allowed disabled:opacity-50";

const secondaryButtonClass =
  "inline-flex items-center justify-center rounded-lg border border-line bg-surface px-3 py-1.5 text-[12.5px] font-medium text-ink-soft transition hover:border-line-strong hover:text-ink disabled:cursor-not-allowed disabled:opacity-40";

const dangerButtonClass =
  "inline-flex items-center justify-center rounded-md border border-line bg-surface px-2 py-1 text-[11px] font-medium text-risk-high transition hover:border-risk-high";
```

Use these strings directly. If three files end up using the same one, *then* extract to a shared module — not before.

### Input

```tsx
const inputClass =
  "w-full rounded-lg border border-line bg-surface px-2.5 py-1.5 text-[12px] text-ink outline-none transition placeholder:text-ink-faint focus:border-ink";
```

Same pattern: hoist to const, use on plain `<input>` or `<textarea>`.

### Badge / pill

```tsx
const pill = "inline-flex items-center gap-1.5 rounded-full px-2.5 py-0.5 text-[11px] font-medium";

const riskTone: Record<Risk, { text: string; bg: string; dot: string }> = {
  critical: { text: "text-risk-critical", bg: "bg-[var(--risk-critical-soft)]", dot: "bg-risk-critical" },
  high:     { text: "text-risk-high",     bg: "bg-[var(--risk-high-soft)]",     dot: "bg-risk-high" },
  medium:   { text: "text-risk-medium",   bg: "bg-[var(--risk-medium-soft)]",   dot: "bg-risk-medium" },
  low:      { text: "text-risk-low",      bg: "bg-[var(--risk-low-soft)]",      dot: "bg-risk-low" },
};

export function RiskBadge({ value }: { value: Risk }) {
  const tone = riskTone[value];
  return (
    <span className={cx(pill, tone.text, tone.bg)}>
      <span className={cx("h-1.5 w-1.5 rounded-full", tone.dot)} aria-hidden />
      {value}
    </span>
  );
}
```

The 1.5px dot + soft background + saturated text + capitalized value is *the* Spencer badge.

### Modal

```tsx
export function Modal({
  open, title, children, onClose, width = "max-w-3xl",
}: {
  open: boolean;
  title: string;
  children: ReactNode;
  onClose: () => void;
  width?: string;
}) {
  if (!open) return null;
  return (
    <div
      className="fixed inset-0 z-50 flex items-start justify-center overflow-y-auto bg-black/30 px-4 py-10"
      onClick={onClose}
    >
      <div
        className={`w-full ${width} overflow-hidden rounded-xl border border-line bg-surface shadow-[0_20px_60px_-24px_rgba(0,0,0,0.25)]`}
        onClick={(e) => e.stopPropagation()}
      >
        <div className="flex items-center justify-between border-b border-line px-6 py-4">
          <h2 className="text-[15px] font-semibold tracking-tight text-ink">{title}</h2>
          <button type="button" onClick={onClose} className="text-sm text-ink-muted transition hover:text-ink">
            Close
          </button>
        </div>
        <div className="max-h-[78vh] overflow-y-auto scrollbar-thin">{children}</div>
      </div>
    </div>
  );
}
```

That's the only modal you need.

### App shell

```tsx
export function App() {
  return (
    <div className="clean-app flex h-screen flex-col bg-surface pt-7 text-[13px] text-ink">
      {bannerError && <ErrorBanner message={bannerError} onDismiss={() => setBannerError(null)} />}
      <div className="min-h-0 flex-1">
        {/* routed content */}
      </div>
    </div>
  );
}
```

`min-h-0 flex-1` on the scroll container is non-negotiable — without it nested flex columns won't scroll.

## Component-writing conventions

- One component per file, PascalCase filename. No barrels, no index.ts re-exports.
- Functional components only. No `React.FC`. No `forwardRef` unless you actually need a ref.
- **Inline object types for props**, not a separate `interface`:

  ```tsx
  // do
  export function Badge({ value }: { value: Risk }) { ... }
  // not
  interface BadgeProps { value: Risk }
  export function Badge({ value }: BadgeProps) { ... }
  ```

  Exception: when props are reused or the type is generic (like the `InlinePicker<T>` pattern below), name the interface.

- Use `cx()` for any conditional class. Never template-literal-concat classes.
- Never use `any`. Spencer's TS settings are strict and `noUncheckedIndexedAccess` is on, so handle `T | undefined` from array indexing.
- Imports order: React → `@shared/*` types → `lib/` utilities → relative components.
- Comments: explain *why*, not *what*. No JSDoc. No "// TODO: refactor" without a real reason.

## The generic InlinePicker pattern

When you have a select-like control with semantic colors (status, severity, risk, tier), use this generic. Don't write a one-off `<select>` for each.

```tsx
interface InlinePickerProps<T extends string> {
  value: T;
  disabled?: boolean;
  saving?: boolean;
  onChange: (next: T) => void;
  options: readonly T[];
  toneClass: (value: T) => string;
  ariaLabel: string;
  label?: (value: T) => string;
}

function InlinePicker<T extends string>({
  value, disabled, saving, onChange, options, toneClass, ariaLabel, label = (v) => v,
}: InlinePickerProps<T>) {
  return (
    <label className="relative inline-flex">
      <span className="sr-only">{ariaLabel}</span>
      <select
        value={value}
        disabled={disabled || saving}
        onChange={(e) => onChange(e.target.value as T)}
        className={cx(
          "cursor-pointer appearance-none rounded-full border border-transparent bg-no-repeat py-0.5 pl-2.5 pr-6 text-[11px] font-medium capitalize",
          toneClass(value),
          saving && "opacity-60",
          disabled && "cursor-not-allowed opacity-50",
        )}
      >
        {options.map((o) => <option key={o} value={o}>{label(o)}</option>)}
      </select>
    </label>
  );
}
```

Then specialize:

```tsx
export function RiskPicker(props: Omit<InlinePickerProps<Risk>, "options" | "toneClass" | "ariaLabel">) {
  return <InlinePicker {...props} options={risks} toneClass={(v) => `${riskTone[v].text} ${riskTone[v].bg}`} ariaLabel="Risk" />;
}
```

## Forms

- Plain `<form onSubmit={...}>`. Call `event.preventDefault()`. No react-hook-form, no formik.
- One `saving` boolean per form. Disable the submit button while it's true.
- Errors surface via an `ErrorBanner` at the top of the screen or via a `setError` callback into a parent. No toast library.
- Validation: do it inline in the submit handler. If it grows, extract to a `validate()` function in the same file. Don't reach for zod unless Spencer asks for it.
- Auto-resize textareas with the `useEffect`-on-scrollHeight trick (see `references/textarea-autoresize.md`).

## Loading, empty, error states

Three rules:

1. **Loading** is a small inline marker, not a full-screen spinner. Disabled form + `Saving…` text on the button. For lists, render skeleton rows that match the row height (1px border, same padding) — not pulsing gradient blobs.
2. **Empty** states are one line of `text-ink-muted` text and maybe a single secondary button. No illustrations, no clip art, no big "Get started" cards.
3. **Error** states use `bg-[var(--risk-high-soft)] text-risk-high` with the `ErrorBanner` pattern. Always include a dismiss button.

## What to never put in a Spencer UI

If you find yourself reaching for any of these, stop:

- Gradient backgrounds, gradient text, gradient buttons.
- Glass / blur / backdrop-filter effects.
- Drop shadows on cards, buttons, inputs (modal is the one exception).
- `rounded-2xl` or larger. Anything bigger than `rounded-xl` looks like a marketing site.
- Hero sections, big centered headlines, "Welcome to ___" splash screens.
- Stock illustrations, undraw-style SVGs, mascots.
- Lucide icons sprinkled on every label "for visual interest."
- Animated entrances, page transitions, scroll-triggered animations.
- Color anywhere except: ink ramp, surface ramp, line ramp, and the one semantic ramp (risk/sev/status).
- Tailwind's `gray-*`, `slate-*`, `zinc-*`, `neutral-*` classes — use the custom `ink` and `line` tokens instead so the palette stays consistent.
- Tailwind's `blue-500`, `indigo-600`, `purple-*`, `pink-*` accents.
- "Modern SaaS" font choices: Inter, Geist, Plus Jakarta Sans, Manrope. System fonts only.
- `dark:` variants unless Spencer explicitly asks for dark mode.

## Working with an existing repo

If Spencer is editing a repo that already has a different stack — Next.js with shadcn, a Mantine app, whatever — do NOT rewrite it to match this skill. Follow the repo's conventions. But still apply the *taste* defaults from this skill: smaller type, denser spacing, neutral palette, no gratuitous gradients or shadows. Push the existing components in that direction rather than around it.

## Reference files

When you need more detail than this file holds, read:

- `references/full-tailwind-config.md` — complete `tailwind.config.ts` with CSS variables.
- `references/full-index-css.md` — the global CSS including `.clean-app`, `.tracked-label`, `.scrollbar-thin`, `.select-ink`.
- `references/starter-app.md` — minimal `App.tsx`, `main.tsx`, `index.html` for a brand-new project.
- `references/anti-patterns.md` — extended "do not do this" with concrete bad-vs-good code pairs, useful when reviewing or refactoring an existing AI-generated frontend toward Spencer's style.
