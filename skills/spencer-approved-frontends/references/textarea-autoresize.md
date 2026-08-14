# Auto-resizing textarea

Pattern from Spencer's repos. Cap the height somewhere sensible (160px in the original) so a long paste doesn't push the page around.

```tsx
import { useEffect, useRef, useState } from "react";

const inputClass =
  "w-full resize-none rounded-lg border border-line bg-surface px-2.5 py-1.5 text-[12px] text-ink outline-none transition placeholder:text-ink-faint focus:border-ink";

export function CommentBox({ onSubmit }: { onSubmit: (body: string) => Promise<void> }) {
  const [text, setText] = useState("");
  const [saving, setSaving] = useState(false);
  const ref = useRef<HTMLTextAreaElement>(null);

  useEffect(() => {
    const el = ref.current;
    if (!el) return;
    el.style.height = "auto";
    el.style.height = `${Math.min(el.scrollHeight, 160)}px`;
  }, [text]);

  async function submit(e: React.FormEvent) {
    e.preventDefault();
    if (!text.trim() || saving) return;
    setSaving(true);
    try {
      await onSubmit(text);
      setText("");
    } finally {
      setSaving(false);
    }
  }

  return (
    <form onSubmit={submit} className="flex flex-col gap-2">
      <textarea
        ref={ref}
        className={inputClass}
        placeholder="comment"
        value={text}
        onChange={(e) => setText(e.target.value)}
        disabled={saving}
        rows={1}
      />
      <button
        type="submit"
        className="self-end inline-flex items-center justify-center rounded-lg bg-ink px-2.5 py-1.5 text-[12px] font-medium text-white transition hover:bg-ink-soft disabled:opacity-50"
        disabled={saving || !text.trim()}
      >
        {saving ? "Posting…" : "Post"}
      </button>
    </form>
  );
}
```

Why `auto` then `scrollHeight`: setting back to `auto` lets the browser shrink the box if the user deletes content. Without that step, the box only grows.
