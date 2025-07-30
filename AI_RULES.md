# 🧠 AI Development Rules

This document defines the tech stack, library usage patterns, architectural principles, and best practices for developing and scaling this Next.js application. By following these standards, we ensure consistency, maintainability, high performance, and a smooth developer experience across the frontend and backend codebase.

---

## 🚀 Tech Stack Overview

This project leverages a modern, scalable, and developer-friendly stack:

| Layer | Tech |
|-------|------|
| Framework | **Next.js (App Router)** |
| Language | **TypeScript** |
| Styling | **Tailwind CSS** + `tailwindcss-animate` |
| UI Components | **Shadcn/UI** (built on Radix UI) |
| Forms & Validation | **react-hook-form** + **zod** |
| Icons | **Lucide React** |
| State Management | React Hooks (`useState`, `useReducer`, Context API) |
| Notifications | **Sonner** |
| Charts | **Recharts** |
| Backend API | **Next.js Route Handlers**, Server Actions |
| Database ORM | _(Optional but recommended)_ **Drizzle ORM** |
| Auth | _(Flexible)_ NextAuth.js / Clerk / Custom JWT |
| File Uploads | `uploadthing`, `next-cloudinary`, or custom handlers |
| Hosting | Vercel / Railway / Render (discuss if self-hosted) |

---

## 🧩 UI & Component Guidelines

### 1. **Use Shadcn/UI First**
- Use only components from `src/components/ui/` unless a component doesn’t exist.
- Build custom components with **Radix primitives + Tailwind CSS** to match Shadcn style.
- Avoid random third-party UI kits (e.g., MUI, Chakra) unless explicitly discussed.

### 2. **Tailwind-First Styling**
- Prefer Tailwind utility classes.
- Avoid inline styles or `style={{}}` unless dynamically necessary.
- No `styled-components`, Emotion, or other CSS-in-JS libs.
- Add base resets and variables only in `src/app/globals.css`.

---

## 📦 Icon System

- All icons must come from [`lucide-react`](https://lucide.dev/).
- Wrap icons in components if reused frequently.

---

## 📝 Forms & Validation

### Use:
- `react-hook-form` for form state management.
- `zod` for schema validation and TypeScript inference.
- `@hookform/resolvers/zod` to bind validation.

### Structure:
```
src/components/forms/
src/lib/validations/[entity].ts
```

---

## 🧠 State Management Rules

| Scope | Tool |
|-------|------|
| Local component | `useState`, `useReducer` |
| Shared across components | `Context API` |
| App-wide complex state | ✅ *Discuss first:* Zustand, Jotai, or Redux Toolkit |

Avoid unnecessary global state bloat.

---

## 🧭 Routing & Navigation

- Use **Next.js App Router** (`src/app/`) exclusively.
- Organize routes clearly with layout files (`layout.tsx`, `page.tsx`).
- Dynamic routes: `app/products/[id]/page.tsx` etc.
- Use `redirect`, `notFound`, and `generateStaticParams` for clean routing.

---

## 🧪 API Development Rules

### Server Logic Options:
| Method | Use When |
|--------|----------|
| **Route Handlers** (`src/app/api/**/route.ts`) | Classic REST-style APIs |
| **Server Actions** (with `use server`) | Form submissions, mutations, SSR logic |

### Best Practices:
- ✅ Keep route handlers **thin** – delegate logic to `src/lib/api/` or `src/server/services/`.
- ✅ Validate request payloads with `zod`.
- ✅ Use `Response.json()` with proper HTTP status codes.
- ✅ Sanitize input/output for security.
- ❌ Never mix business logic directly in route files.

### Suggested Structure:
```
src/app/api/
├── auth/
│   └── login/route.ts
│
src/server/
├── services/         # Business logic lives here
├── db/               # Database access (Drizzle/Prisma)
├── utils/            # Shared utilities
```

---

## 🔐 Authentication & Authorization

- Prefer **NextAuth.js** for session management.
- For edge use cases, use JWT + middleware.
- Auth routes live under `api/auth/**`.

Example middleware for protected routes:
```ts
export async function middleware(req: NextRequest) {
  const token = req.cookies.get("token");
  if (!token) return NextResponse.redirect("/login");
  return NextResponse.next();
}
```

---

## ☁️ API Calls & Data Fetching

| Type | Use |
|------|-----|
| Client-side | `fetch` or SWR (for revalidation) |
| Server-side | `fetch`, `cache()`, Server Actions |
| Mutations | Server Actions preferred |

### Rules:
- Avoid axios — native `fetch` is preferred.
- Use `revalidatePath()` or `revalidateTag()` when mutating cached data.
- Avoid `getServerSideProps` or `getStaticProps` (legacy features).

---

## 📊 Charts

- Use **Recharts** only.
- Create reusable chart wrappers in `src/components/ui/chart.tsx`.

---

## 🔔 Toasts & Feedback

- Use **Sonner** (from `src/components/ui/sonner.tsx`).
- Keep toast copy concise and actionable.

---

## 🧪 Directory Structure Standards

```
src/
├── app/               # App Router pages, layouts, etc.
├── components/
│   ├── ui/            # Shadcn/UI components
│   └── [feature]/     # Feature-specific components
├── hooks/             # Custom React hooks
├── lib/
│   ├── utils.ts       # Helper functions
│   ├── validations/   # Zod schemas
│   └── api/           # API wrappers or logic
├── server/
│   ├── db/            # DB layer (Drizzle / Prisma)
│   ├── services/      # Business logic
│   └── auth/          # Auth helpers
└── types/             # Global TypeScript types
```

---

## 🧰 Utility Functions

- All general-purpose utils live in `src/lib/utils.ts`.
- Must be:
  - Strongly typed
  - Reusable across the app
  - Accompanied by meaningful names and JSDoc comments

---

## 🪝 Custom Hooks

- All custom hooks live in `src/hooks/`.
- Use `use-` prefix and descriptive names (e.g., `use-mobile.ts`, `use-auth.ts`).
- Keep them lean and scoped.

---

## 🔡 TypeScript Rules

- Write **everything** in TypeScript.
- Avoid `any`. Prefer `unknown` with type guards.
- Use `z.infer<typeof schema>` to mirror schema types.
- Use `readonly`, `as const`, and type aliases where needed.

---

## 🧙🏽‍♂️ Pro Tips

- Use `strict mode` in `tsconfig.json`.
- Prefer composition over inheritance.
- Keep API responses consistent: always `{ success, data, error }`.
- Use `env.ts` to safely access `process.env`.
- Auto-format with `prettier` + `eslint` on save.

---

## 🛡️ Security Guidelines

- Validate all external input using Zod.
- Sanitize HTML content if rendering rich text.
- Never trust `req.body` blindly.
- Store secrets in `.env.local` (never commit it!).

---

## 🧼 Linting & Formatting

- Run `pnpm lint` and `pnpm format` before pushing.
- Enable auto-format on save in VSCode.
- Follow the `.eslintrc` and `.prettierrc` configs strictly.

---

## ✅ Checklist Before Merge

- [ ] ✅ All new components use Shadcn/UI
- [ ] ✅ Types are strict, no `any`
- [ ] ✅ API logic moved to service layer
- [ ] ✅ Zod validation added
- [ ] ✅ Lint and format pass
- [ ] ✅ Minimal diff in unrelated files

---

This doc is your bestie. Stick to it like tailwind to a `div`, and we’ll keep the codebase slick, scalable, and AI-friendly.
