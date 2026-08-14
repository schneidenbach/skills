# Full `tailwind.config.ts`

Drop this in the project root. It expects CSS variables defined in `index.css` (see `full-index-css.md`).

The `risk` ramp is domain-specific. Rename to `sev`, `status`, `tier`, etc., to match the app. Keep the same shape: four levels, each with a saturated text color and a soft background variant.

```ts
import type { Config } from "tailwindcss";

const config: Config = {
  content: ["./index.html", "./src/**/*.{ts,tsx}"],
  theme: {
    extend: {
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
        risk: {
          critical: "#7f1d1d",
          high:     "#b91c1c",
          medium:   "#b45309",
          low:      "#1d4ed8",
        },
      },
      fontFamily: {
        sans: ["-apple-system", "BlinkMacSystemFont", "'SF Pro Text'", "'Segoe UI'", "Roboto", "Helvetica", "Arial", "sans-serif"],
        mono: ["'SFMono-Regular'", "'SF Mono'", "Menlo", "Consolas", "monospace"],
      },
    },
  },
  plugins: [],
};

export default config;
```

`postcss.config.js`:

```js
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
```

`package.json` deps for the styling stack (pin Tailwind to 3.4):

```json
{
  "devDependencies": {
    "tailwindcss": "^3.4.17",
    "postcss": "^8.5.3",
    "autoprefixer": "^10.4.20"
  }
}
```
