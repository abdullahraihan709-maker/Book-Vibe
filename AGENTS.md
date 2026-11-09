# AGENTS.md

## Project

Book Vibe is a Next.js 16 **App Router** app (React 19, TypeScript, Tailwind CSS 4 + daisyUI). Routes live in `src/app`. The parent folder name mentions "Pages Routing" and `README.md` is the unmodified create-next-app boilerplate; both are stale. Trust the code and this file.

## Commands

```bash
npm run dev      # dev server, http://localhost:3000
npm run build
npm start
npm run lint     # runs `eslint` (flat config), not `next lint`
npx tsc --noEmit # typecheck (no npm script for it)
```

- Lint a single file: `npx eslint src/app/books/page.tsx`.
- There are no tests, CI workflows, formatter config, or pre-commit hooks in this repo.

## Environment

- `.env` is gitignored but required. It must define:
  `NEXT_PUBLIC_SERVER_BASE_URL=http://localhost:3000`
- Server components fetch books over HTTP from `${NEXT_PUBLIC_SERVER_BASE_URL}/booksData.json`, which serves `public/booksData.json`. The app must be reachable at that URL; if the dev port changes, update this env var too. Fetch errors are swallowed, so a missing/unreachable URL renders empty book lists rather than failing loudly.
- The same `getBooks` helper is duplicated in `src/components/homepage/Books.tsx`, `src/app/books/page.tsx`, and `src/app/books/[id]/page.tsx`. Update all three when changing the fetch or data shape.

## Architecture

- `src/app/layout.tsx` mounts `BooksProvider`, `Navbar`, `Footer`, and `ToastContainer` around every route.
- `BooksProvider` (`src/context/BooksContext.tsx`) holds `readBooks` and `wishlist` in React state only: data resets on page refresh and is not persisted or sent to an API. Components/pages using this state are `'use client'`.
- `Book` and `BooksContextType` are defined in `src/components/types/bookDataTypes.ts`. `public/booksData.json` is the canonical sample data, keyed by `bookId`.
- Routes: `/` (banner + first 9 books), `/books`, `/books/[id]`, `/listed-books` (read/wishlist tabs + sorting), `/pages-to-read` (Recharts bar chart).
- Next.js 16 passes route `params` as a Promise; await it before use (see `src/app/books/[id]/page.tsx`).
- Tailwind 4 is CSS-first: `src/app/globals.css` contains the entire setup (`@import "tailwindcss"; @plugin "daisyui";`). There is no `tailwind.config.*`. daisyUI classes (`btn`, `tabs`, `select`) are used throughout, and the theme is set via `<html data-theme="light">`.
- Cover images are remote from `i.ibb.co.com`, whitelisted in `next.config.ts`. Any new remote image host must be added there.
- Import via the `@/` alias (maps to `src/`); TypeScript is strict.
