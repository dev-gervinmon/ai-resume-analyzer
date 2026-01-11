# Resumind — AI Resume Analyzer

Resumind is a single-page web app that lets you upload a resume PDF, provide a target job title + description, and receive structured AI feedback with an overall score, ATS score, and category-by-category improvement tips.

It uses Puter (via `https://js.puter.com/v2/`) for authentication, file storage, a simple KV database, and AI chat.

## What the app does

- **Sign in** with Puter to access your personal workspace.
- **Upload a resume PDF** (max ~20MB).
- **Convert the first page to an image** (for preview) using `pdfjs-dist`.
- **Store artifacts in Puter**:
  - PDF file in Puter FS
  - Preview image (PNG) in Puter FS
  - Resume metadata + AI feedback JSON in Puter KV (`resume:<uuid>`)
- **Show a dashboard** of past uploads with the overall score.
- **Show a detailed review page** with:
  - Overall score
  - ATS score + quick tips
  - Detailed tips for Tone & Style, Content, Structure, Skills
- **Optional utility**: a `/wipe` route to delete uploaded files and flush KV data (useful during development).

## Routes

- `/` — Dashboard (lists analyzed resumes stored in KV)
- `/auth` — Puter login/logout
- `/upload` — Resume upload + analysis form
- `/resume/:id` — Detailed review for a specific upload
- `/wipe` — Dev utility to clear Puter files + KV

## Tech stack

- React 19 + React Router v7 (configured as SPA: `ssr: false`)
- Vite 7
- Tailwind CSS v4
- Zustand (client-side store)
- `pdfjs-dist` for PDF → image conversion (loads worker from `public/pdf.worker.min.mjs`)
- Puter.js for auth, storage, KV, and AI

## Local development

Prereqs:

- Node.js 20+ (Dockerfile uses Node 20)
- A modern browser
- Internet access (loads Puter.js from Puter CDN)

Install dependencies:

```bash
npm install
```

Start dev server:

```bash
npm run dev
```

Open `http://localhost:5173`.

Notes:

- The app redirects to `/auth` when you’re not signed in.
- Resumes and feedback are stored under your Puter account.

## Production build

```bash
npm run build
npm run start
```

`npm run start` serves the built app (typically on port `3000`).

## Docker

```bash
docker build -t ai-resume-analyzer .
docker run -p 3000:3000 ai-resume-analyzer
```

## Data model

- KV key pattern: `resume:<uuid>`
- Value: JSON containing:
  - `resumePath` (Puter FS path to PDF)
  - `imagePath` (Puter FS path to preview PNG)
  - `companyName`, `jobTitle`, `jobDescription`
  - `feedback` (parsed JSON from the AI response)

## Privacy / safety notes

- Uploaded files are stored in Puter FS.
- The analysis is performed via Puter AI chat (configured to use the `claude-sonnet-4` model in code).
- Avoid uploading sensitive information unless you’re comfortable storing it in your Puter account.

## Live Link

- https://puter.com/app/ai-resume-analyzer-124
