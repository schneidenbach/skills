# Anti-patterns: bad-vs-good

Concrete pairs showing the default "AI frontend" failure mode and the Spencer-approved replacement. Useful when refactoring an existing AI-generated UI toward Spencer's style.

## Hero section

**Bad** — the dashboard opens with this:

```tsx
<section className="bg-gradient-to-br from-indigo-500 to-purple-600 px-8 py-16 text-white">
  <h1 className="text-5xl font-bold">Welcome back, Spencer</h1>
  <p className="mt-4 text-xl text-indigo-100">Here's what's happening today</p>
</section>
```

**Good** — the dashboard just shows the data:

```tsx
<header className="border-b border-line px-6 py-4">
  <h1 className="text-[15px] font-semibold tracking-tight text-ink">Dashboard</h1>
</header>
```

Spencer is logged in. He knows it's him. No greeting, no gradient, no oversized type.

## Stat card

**Bad**:

```tsx
<div className="rounded-2xl bg-white p-8 shadow-lg ring-1 ring-gray-200">
  <p className="text-sm font-medium text-gray-500">Open issues</p>
  <p className="mt-2 text-4xl font-bold text-gray-900">42</p>
  <p className="mt-1 text-sm text-emerald-600">+12% from last week</p>
</div>
```

**Good**:

```tsx
<div className="rounded-lg border border-line bg-surface px-4 py-3">
  <p className="tracked-label">Open issues</p>
  <p className="mt-1 text-[15px] font-semibold tabular-nums text-ink">42</p>
  <p className="text-[12px] text-ink-muted">+12% week over week</p>
</div>
```

Smaller, flatter, denser, neutral palette, no shadow.

## Button

**Bad**:

```tsx
<button className="rounded-full bg-gradient-to-r from-blue-500 to-purple-500 px-6 py-3 text-white shadow-md transition hover:shadow-lg">
  Get Started →
</button>
```

**Good**:

```tsx
<button className="inline-flex items-center justify-center rounded-lg bg-ink px-2.5 py-1.5 text-[12px] font-medium text-white transition hover:bg-ink-soft disabled:opacity-50">
  Continue
</button>
```

Solid ink fill, no gradient, no shadow, no arrow glyph, no rounded-full, dense padding.

## Form input

**Bad**:

```tsx
<input
  type="text"
  placeholder="Enter your email"
  className="w-full rounded-2xl border-2 border-gray-300 px-6 py-4 text-lg shadow-sm focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200"
/>
```

**Good**:

```tsx
<input
  type="email"
  placeholder="email"
  className="w-full rounded-lg border border-line bg-surface px-2.5 py-1.5 text-[12px] text-ink outline-none transition placeholder:text-ink-faint focus:border-ink"
/>
```

1px border, no ring, neutral focus color, lowercase placeholder, dense padding.

## Empty state

**Bad**:

```tsx
<div className="flex flex-col items-center justify-center py-24">
  <img src="/empty-state.svg" alt="" className="h-48 w-48" />
  <h2 className="mt-6 text-2xl font-bold text-gray-900">No items yet!</h2>
  <p className="mt-2 text-gray-600">Create your first item to get started on your journey.</p>
  <button className="mt-6 rounded-full bg-indigo-600 px-6 py-3 text-white">
    + Create Item
  </button>
</div>
```

**Good**:

```tsx
<div className="flex flex-col items-start gap-2 p-6">
  <p className="text-[13px] text-ink-muted">No items.</p>
  <button className={secondaryButtonClass}>New item</button>
</div>
```

One line of muted text, one secondary button, no illustration, no exclamation point.

## Loading state

**Bad**:

```tsx
<div className="flex items-center justify-center py-32">
  <div className="h-12 w-12 animate-spin rounded-full border-4 border-indigo-200 border-t-indigo-600" />
</div>
```

**Good** — for a list, render skeleton rows that match the real row geometry:

```tsx
<div>
  {Array.from({ length: 6 }).map((_, i) => (
    <div key={i} className="border-b border-line px-3 py-2">
      <div className="h-3 w-1/3 rounded bg-surface-deep" />
    </div>
  ))}
</div>
```

Or for a button mid-submit:

```tsx
<button className={primaryButtonClass} disabled={saving}>
  {saving ? "Saving…" : "Save"}
</button>
```

No giant spinner, no centered loading screen.

## Card grid

**Bad**:

```tsx
<div className="grid gap-6 p-8 md:grid-cols-3">
  {items.map((item) => (
    <div key={item.id} className="rounded-2xl bg-white p-6 shadow-md transition hover:scale-105 hover:shadow-xl">
      <div className="mb-4 flex h-12 w-12 items-center justify-center rounded-full bg-indigo-100">
        <Icon className="h-6 w-6 text-indigo-600" />
      </div>
      <h3 className="text-xl font-bold">{item.title}</h3>
      <p className="mt-2 text-gray-600">{item.description}</p>
    </div>
  ))}
</div>
```

**Good**:

```tsx
<div className="grid gap-3 p-4 md:grid-cols-3">
  {items.map((item) => (
    <article key={item.id} className="rounded-lg border border-line bg-surface p-3 transition hover:border-line-strong">
      <h3 className="text-[13px] font-semibold text-ink">{item.title}</h3>
      <p className="mt-1 text-[12px] text-ink-muted">{item.description}</p>
    </article>
  ))}
</div>
```

No icon circle, no scale-on-hover, 1px border instead of shadow, smaller type, tighter gaps.

## Table

**Bad**:

```tsx
<table className="min-w-full divide-y divide-gray-200">
  <thead className="bg-gray-50">
    <tr>
      <th className="px-6 py-4 text-left text-xs font-medium uppercase tracking-wider text-gray-500">Name</th>
    </tr>
  </thead>
  <tbody className="divide-y divide-gray-200 bg-white">
    {rows.map((r) => (
      <tr key={r.id} className="hover:bg-gray-50">
        <td className="whitespace-nowrap px-6 py-4 text-sm text-gray-900">{r.name}</td>
      </tr>
    ))}
  </tbody>
</table>
```

**Good**:

```tsx
<table className="w-full text-[12px]">
  <thead>
    <tr className="border-b border-line">
      <th className="tracked-label px-3 py-1.5 text-left">Name</th>
    </tr>
  </thead>
  <tbody>
    {rows.map((r) => (
      <tr key={r.id} className="border-b border-line transition hover:bg-surface-hover">
        <td className="px-3 py-1.5 text-ink">{r.name}</td>
      </tr>
    ))}
  </tbody>
</table>
```

Borders on rows, not a `divide-y` rule. `tracked-label` headers. No oversized cell padding.

## Color

**Bad**: Tailwind's `gray-*`, `slate-*`, `zinc-*`, `neutral-*`, `indigo-*`, `purple-*`, `emerald-*`, `rose-*` classes scattered across the app.

**Good**: Only the project's defined tokens — `ink`, `surface`, `line`, plus the one semantic ramp (`risk`, `sev`, `status`, …). If a Tailwind preset color is appearing in a Spencer UI, it's a smell.

## Typography

**Bad**:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
```

**Good**: nothing. The system font stack is in `tailwind.config.ts` and that's it. No font fetch, no FOUT.
