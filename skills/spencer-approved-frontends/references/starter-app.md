# Starter files for a new project

Use these when scaffolding a brand-new Spencer-style React app. Run `npm create vite@latest <name> -- --template react-ts`, then replace the generated files with what's below and add the styling stack.

## `index.html`

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>App</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

No favicons, no preconnect to fonts.googleapis, no extra meta tags. Add them when there's a real reason.

## `src/main.tsx`

```tsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import { App } from "./App";
import "./index.css";

createRoot(document.getElementById("root")!).render(
  <StrictMode>
    <App />
  </StrictMode>,
);
```

## `src/App.tsx`

Manual routing for small apps. Bump to a real router if you cross ~3 routes.

```tsx
import { useEffect, useState } from "react";

type Route =
  | { screen: "home" }
  | { screen: "settings" };

function parseRoute(pathname: string): Route {
  if (pathname === "/settings") return { screen: "settings" };
  return { screen: "home" };
}

export function App() {
  const [route, setRoute] = useState<Route>(() => parseRoute(window.location.pathname));

  useEffect(() => {
    const onPop = () => setRoute(parseRoute(window.location.pathname));
    window.addEventListener("popstate", onPop);
    return () => window.removeEventListener("popstate", onPop);
  }, []);

  return (
    <div className="clean-app flex h-screen flex-col bg-surface pt-7 text-[13px] text-ink">
      <div className="min-h-0 flex-1 overflow-y-auto">
        {route.screen === "home" && <Home />}
        {route.screen === "settings" && <Settings />}
      </div>
    </div>
  );
}

function Home() {
  return (
    <main className="mx-auto max-w-4xl px-6 py-8">
      <h1 className="text-[15px] font-semibold tracking-tight text-ink">Home</h1>
    </main>
  );
}

function Settings() {
  return (
    <main className="mx-auto max-w-4xl px-6 py-8">
      <h1 className="text-[15px] font-semibold tracking-tight text-ink">Settings</h1>
    </main>
  );
}
```

## `src/lib/cx.ts`

```ts
export function cx(...parts: Array<string | false | null | undefined>): string {
  return parts.filter(Boolean).join(" ");
}
```

## `src/lib/api.ts` (typical shape)

```ts
const BASE = "/api";

async function request<T>(path: string, init?: RequestInit): Promise<T> {
  const res = await fetch(`${BASE}${path}`, {
    headers: { "Content-Type": "application/json", ...(init?.headers ?? {}) },
    ...init,
  });
  if (!res.ok) {
    const text = await res.text().catch(() => "");
    throw new Error(text || `${res.status} ${res.statusText}`);
  }
  return res.json() as Promise<T>;
}

export const api = {
  list: () => request<Item[]>("/items"),
  create: (body: NewItem) => request<Item>("/items", { method: "POST", body: JSON.stringify(body) }),
};
```

## `tsconfig.json` essentials

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "jsx": "react-jsx",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitOverride": true,
    "skipLibCheck": true,
    "paths": {
      "@shared/*": ["shared/*"]
    }
  },
  "include": ["src", "shared"]
}
```

## Install commands

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install -D tailwindcss@^3.4.17 postcss@^8.5.3 autoprefixer@^10.4.20
npx tailwindcss init -p
```

Then replace the generated `tailwind.config.js` with the `tailwind.config.ts` from `full-tailwind-config.md`, and replace `src/index.css` with the file from `full-index-css.md`.

Do NOT install: `clsx`, `classnames`, `lucide-react`, `framer-motion`, `react-router-dom` (for tiny apps), `@tanstack/react-query`, `zustand`, `react-hook-form`, `zod`, any shadcn or Radix package, any Headless UI package, any icon set, `next` (this is Vite, not Next).

If Spencer asks for one of those explicitly, install it. Otherwise, don't.
