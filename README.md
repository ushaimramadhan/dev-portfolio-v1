# Fullstack Portfolio

## Overview

Repository ini berisi source code untuk Web Application Portofolio Fullstack berstandar industri. Proyek ini dirancang bukan sekadar sebagai *showcase* UI, tetapi sebagai pembuktian kompetensi arsitektur software yang scalable, secure, dan type-safe.

Fokus utama adalah performa, keamanan data, dan integritas data.

## Architecture & Tech Stack

### Core Stack

- **Framework:** Next.js (App Router)
- **Language:** TypeScript
- **Styling:** Tailwind CSS
- **Database:** Supabase (PostgreSQL)
- **Auth:** Supabase Auth
- **AI/ML:** Gemini Pro(**RAG** Implementation)
- **Validation:** Zod (Schema-first validation)

### Key Architectural Decisions (Backend Perspective)

Untuk memahami flow aplikasi ini dari sudut pandang Backend Engineer:

1. **React Server Components (**RSC**):**
    * *Analogi:* Bayangkan ini sebagai **PHP**/**JSP** modern. Component di-render di server, dan hanya **HTML** yang dikirim ke client. Database query dilakukan langsung di dalam component (Server-side), menghilangkan kebutuhan untuk `useEffect` data fetching atau mengekspos **API** endpoint publik untuk data *read-only*.
2. **Server Actions:**
    * *Analogi:* Pengganti **REST** **API** Endpoint tradisional. Ini adalah fungsi *Remote Procedure Call (**RPC**)* yang berjalan di server tetapi dipanggil seperti fungsi JavaScript biasa dari client. Tidak perlu membuat route handler manual (`**POST** /api/bla`).
3. **Supabase & **RLS** (Row Level Security):**
    * *Analogi:* Logic otorisasi dipindahkan dari Application Layer (Node.js middleware) langsung ke Database Layer (Postgres Policy). Meskipun client melakukan query langsung via **SDK**, DB akan menolak akses jika user tidak memenuhi policy **RLS**.
4. **Vector Search (pgvector):**
    * Menggunakan embedding untuk fitur **RAG** (Retrieval-Augmented Generation), memungkinkan AI menjawab pertanyaan berdasarkan konteks data portofolio (resume, projects).

## Getting Started

### 1. Prerequisites

- Node.js (**LTS** Version)
- pnpm (Recommended) atau npm
- Akun Supabase

### 2. Installation

```bash git
clone https://github.com/ushaimramadhan/dev-portfolio-v1.git
cd dev-portfolio-v1
pnpm install

```

### 3. Environment Setup

****PENTING**:** Jangan pernah commit file `.env` atau `.env.local`. Salin template konfigurasi:

```bash
cp .env.example .env.local

```

Isi `.env.local` dengan kredensial Supabase dan AI Anda. File `.env.example` hanya berisi *nama key*, bukan *value* rahasia.

Variabel yang dibutuhkan:

```env
NEXT_PUBLIC_SUPABASE_URL=your-project-url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key (Hanya untuk scripts/admin tasks, jangan expose ke client)
GOOGLE_GENERATIVE_AI_API_KEY=your-gemini-key

```

### 4. Database Migration

Sinkronisasi schema lokal ke remote database Supabase:

```bash
pnpm supabase db push

```

### 5. Running Development Server

```bash
pnpm dev

```

Akses di `[http://localhost:**3000**`.](http://localhost:**3000**`.)

## Security Standards

- ****RLS** (Row Level Security):** **WAJIB** aktif untuk semua tabel. Default policy adalah `deny all`. Akses dibuka secara eksplisit per role.
- **Input Validation:** Semua input user (Form, **URL** Params) divalidasi menggunakan **Zod** sebelum diproses.
- **Type Safety:** `noExplicitAny` diaktifkan di `tsconfig.json`. Jangan gunakan `any`.

## Project Structure

```
/app
    /actions    # Server Actions (Mutations/**RPC**)
    /(routes)   # Page Routes (Server Components)
    /api        # Route Handlers (Edge Cases only, use Actions primarily)
/components
    /ui         # Reusable atomic components
    /features   # Feature-specific components
/lib
    /db         # Supabase client & types
    /utils      # Helper functions
    /validations # Zod schemas
/supabase     # Migrations & config

```

## AI/RAG Implementation

Fitur Chatbot menggunakan teknik **RAG**:

Data portofolio di-chunk dan di-generate embedding-nya.

Disimpan di tabel `documents` dengan kolom vector. ## Query user dicocokkan menggunakan `pgvector` (cosine similarity). ## Context yang relevan di-inject ke prompt Gemini.
