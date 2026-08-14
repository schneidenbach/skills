# Full `src/index.css`

Imported once from `main.tsx`. Defines CSS variables consumed by the soft background classes (`bg-[var(--risk-high-soft)]` etc.), the `.clean-app` reset, and a few utilities Tailwind doesn't carry.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

:root {
  color-scheme: light;

  --bg: #ffffff;
  --ink: #0a0a0a;
  --ink-soft: #404040;
  --ink-muted: #737373;
  --ink-faint: #a3a3a3;
  --line: #eaeaea;
  --line-strong: #d4d4d4;

  --risk-critical-soft: #fef2f2;
  --risk-high-soft:     #fee2e2;
  --risk-medium-soft:   #fef3c7;
  --risk-low-soft:      #dbeafe;

  --font-sans: -apple-system, BlinkMacSystemFont, "SF Pro Text", "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
}

html, body, #root {
  height: 100%;
}

body {
  margin: 0;
  background: var(--bg);
  color: var(--ink);
  font-family: var(--font-sans);
  font-size: 14px;
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

button, input, textarea, select {
  font: inherit;
  color: inherit;
}

/* The clean-app wrapper kills default shadows/outlines globally so we
   can opt back in only where wanted (the modal). */
.clean-app * {
  box-shadow: none;
}

.clean-app input:not([type="range"]):not([type="file"]):not([type="checkbox"]),
.clean-app textarea,
.clean-app select {
  outline: none;
}

.tracked-label {
  font-size: 11px;
  font-weight: 500;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--ink-muted);
}

.scrollbar-thin {
  scrollbar-width: thin;
  scrollbar-color: var(--line-strong) transparent;
}

.scrollbar-thin::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

.scrollbar-thin::-webkit-scrollbar-thumb {
  background: var(--line-strong);
  border-radius: 4px;
}

.scrollbar-thin::-webkit-scrollbar-track {
  background: transparent;
}

/* Used on <select> elements so the dropdown chevron matches ink color. */
.select-ink {
  background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='10' height='6' viewBox='0 0 10 6'><path d='M1 1l4 4 4-4' stroke='%23737373' stroke-width='1.5' fill='none' stroke-linecap='round' stroke-linejoin='round'/></svg>");
  background-position: right 0.85rem center;
  background-repeat: no-repeat;
  padding-right: 2rem;
}
```

The pattern: the *saturated* color lives in `tailwind.config.ts` so you can write `text-risk-high`, and the *soft* variant lives as a CSS variable so you can write `bg-[var(--risk-high-soft)]`. Soft backgrounds aren't worth a Tailwind theme entry because they're only used in one place (badges).
