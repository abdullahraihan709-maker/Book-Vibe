# Book Vibe

A modern book discovery and reading tracker built with **Next.js 16**, **React 19**, **TypeScript**, and **Tailwind CSS 4**. Browse a curated catalog, inspect book details, build a read list and wishlist, sort saved books, and visualize the page counts of books you have read. Check out the ![Live Site](https://my-book-vibe-woad.vercel.app/)

![Next.js](https://img.shields.io/badge/Next.js-16.3.5-000000?logo=nextdotjs&logoColor=white)
![React](https://img.shields.io/badge/React-19.2.8-61DAFB?logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-4.3.3-06B6D4?logo=tailwindcss&logoColor=white)
![daisyUI](https://img.shields.io/badge/daisyUI-5.7.43-5A0EF8)

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Available Scripts](#available-scripts)
- [Routes](#routes)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
- [Customization](#customization)
- [Quality Checks](#quality-checks)
- [Deployment](#deployment)
- [Known Limitations](#known-limitations)
- [Contributing](#contributing)
- [License](#license)

---

## Features

- Browse a curated collection of books with covers, ratings, categories, and tags.
- View detailed book information, including author, review, publisher, publication year, and page count.
- Add books to a **Read** list or **Wishlist** from the details page.
- Remove saved books directly from the listed-books view.
- Sort saved books by **rating**, **number of pages**, or **publication year**.
- Visualize the page counts of read books with an interactive **Recharts** bar chart.
- Receive toast notifications for read, wishlist, and removal actions.
- Responsive layout built with Tailwind CSS and daisyUI components.

## Tech Stack

| Layer | Technology |
| --- | --- |
| Framework | [Next.js 16](https://nextjs.org/) (App Router) |
| UI | [React 19](https://react.dev/), [Tailwind CSS 4](https://tailwindcss.com/), [daisyUI 5](https://daisyui.com/) |
| Language | [TypeScript 5](https://www.typescriptlang.org/) |
| Charts | [Recharts 3](https://recharts.org/) |
| Notifications | [React Toastify](https://fkhadra.github.io/react-toastify/) |
| Icons | Inline SVG / [React Icons](https://react-icons.github.io/react-icons/) |
| Linting | ESLint 9 with `eslint-config-next` |

## Getting Started

### Prerequisites

- **Node.js 20.9 or newer** (required by Next.js 16)
- **npm** (the repository includes `package-lock.json`)

### Installation

1. Clone the repository and move into the project directory:

   ```bash
   git clone <repository-url>
   cd my-book-vibe-app
   ```

2. Install dependencies:

   ```bash
   npm install
   ```

3. Create a `.env` file in the project root:

   ```env
   NEXT_PUBLIC_SERVER_BASE_URL=http://localhost:3000
   ```

4. Start the development server:

   ```bash
   npm run dev
   ```

5. Open [http://localhost:3000](http://localhost:3000) in your browser.

> **Important:** The server components load book data from
> `${NEXT_PUBLIC_SERVER_BASE_URL}/booksData.json`, which maps to
> `public/booksData.json`. The value must match the origin where the app is
> running. If the development server uses a different port, update the env var
> accordingly.

## Environment Variables

| Variable | Required | Example | Description |
| --- | --- | --- | --- |
| `NEXT_PUBLIC_SERVER_BASE_URL` | Yes | `http://localhost:3000` | Origin used to fetch `/booksData.json` from the `public` directory. Must be the app's local or deployed URL. |

`.env*` files are gitignored, so the variable must also be configured in your deployment environment.

## Available Scripts

| Command | Description |
| --- | --- |
| `npm run dev` | Start the development server at `http://localhost:3000`. |
| `npm run build` | Create an optimized production build. |
| `npm start` | Serve the production build (run `npm run build` first). |
| `npm run lint` | Run ESLint across the project using the flat config. |
| `npx tsc --noEmit` | Type-check the project without emitting files. |

There is currently no automated test script.

## Routes

| Route | Description | Rendering |
| --- | --- | --- |
| `/` | Landing page with a hero banner and a preview of the first nine books. | Server component |
| `/books` | Full book catalog. | Server component |
| `/books/[id]` | Book details, read action, and wishlist action. | Server component with client actions |
| `/listed-books` | Read and wishlist tabs, sorting controls, and remove actions. | Client component |
| `/pages-to-read` | Bar chart of page counts for books marked as read. | Client component |

## Project Structure

```text
.
├── public/
│   └── booksData.json          # Static book catalog
├── src/
│   ├── app/
│   │   ├── books/
│   │   │   ├── [id]/
│   │   │   │   ├── loading.tsx
│   │   │   │   └── page.tsx    # Book details route
│   │   │   ├── loading.tsx
│   │   │   └── page.tsx        # Full catalog route
│   │   ├── listed-books/
│   │   │   └── page.tsx        # Read list and wishlist
│   │   ├── pages-to-read/
│   │   │   └── page.tsx        # Reading statistics chart
│   │   ├── globals.css         # Tailwind and daisyUI setup
│   │   ├── layout.tsx          # Root layout and providers
│   │   ├── loading.tsx
│   │   └── page.tsx            # Home page
│   ├── assets/                 # Local images and icons
│   ├── components/
│   │   ├── bookDetails/        # Read and wishlist buttons
│   │   ├── homepage/           # Banner and homepage book grid
│   │   ├── shared/             # Navbar, footer, and book cards
│   │   └── types/              # Shared TypeScript types
│   └── context/
│       └── BooksContext.tsx    # Read list and wishlist state
├── next.config.ts              # Remote image configuration
├── eslint.config.mjs
├── postcss.config.mjs
└── tsconfig.json
```

> Tailwind CSS 4 is configured entirely in `src/app/globals.css`. There is no
> `tailwind.config.*` file.

## How It Works

1. Server components fetch `booksData.json` from the configured base URL with `cache: "no-store"`.
2. The fetched catalog is rendered into reusable `BookCard` components.
3. The `/books/[id]` route resolves a book by its `bookId` and renders its details.
4. Read and wishlist actions update shared state in `BooksProvider` and trigger toast notifications.
5. `/listed-books` and `/pages-to-read` are client components that consume the same context, so saved books are available across navigation.

Book data follows the `Book` interface in `src/components/types/bookDataTypes.ts`:

```ts
interface Book {
  bookId: number;
  bookName: string;
  author: string;
  image: string;
  review: string;
  totalPages: number;
  rating: number;
  category: string;
  tags: string[];
  publisher: string;
  yearOfPublishing: number;
}
```

## Customization

### Update the book catalog

Edit `public/booksData.json`. Keep each entry aligned with the `Book` interface and use unique `bookId` values, because detail links and saved-book operations rely on them.

### Allow new image hosts

Book covers currently come from `i.ibb.co.com`. To use another remote host, add it to `images.remotePatterns` in `next.config.ts`.

### Change the theme

The daisyUI theme is set on the `<html>` element in `src/app/layout.tsx`:

```tsx
<html lang="en" data-theme="light">
```

Global styles and the daisyUI plugin are configured in `src/app/globals.css`.

### Update app metadata

Edit the `metadata` export in `src/app/layout.tsx` to change the page title and description.

## Quality Checks

Run these before opening a pull request:

```bash
npm run lint
npx tsc --noEmit
npm run build
```

## Deployment

The project can be deployed to any platform that supports Next.js, such as [Vercel](https://vercel.com/).

1. Set `NEXT_PUBLIC_SERVER_BASE_URL` to the public deployment origin, for example:

   ```env
   NEXT_PUBLIC_SERVER_BASE_URL=https://your-app.vercel.app
   ```

2. Deploy the application.
3. Verify that `/books` loads data. An unreachable or incorrect base URL results in empty book lists because fetch errors are handled gracefully.

## Known Limitations

- **No persistence:** read and wishlist entries live in React state only and reset on a full page refresh.
- **No backend:** the catalog is a static JSON file; there is no authentication, database, or API layer.
- **Self-hosted data fetch:** server components fetch the catalog over HTTP from the app's own public URL, so `NEXT_PUBLIC_SERVER_BASE_URL` must always be correct.
- **No automated tests:** linting, type-checking, and manual verification are the current quality gates.

## Contributing

Contributions are welcome. Fork the repository, create a focused feature branch, make your changes, run the quality checks above, and open a pull request with a clear description of the change.

## License

No license has been specified for this project. Add a license before distributing or reusing the code publicly.
