# Multi-Bank Mutasi Analyzer

Frontend di-host di **Vercel**, backend tetap di **Google Apps Script** (Google Sheets sebagai database).

---

## Struktur Repo

```
/
├── index.html        ← frontend (deploy ke Vercel)
├── vercel.json       ← konfigurasi Vercel
├── Code.gs           ← backend GAS (deploy ke Google Apps Script, BUKAN ke Vercel)
└── README.md
```

> `Code.gs` tidak di-deploy ke Vercel. File ini hanya disimpan di repo sebagai backup/version control.

---

## Setup (sekali aja)

### 1. Deploy Code.gs ke Google Apps Script

1. Buka [script.google.com](https://script.google.com) → buka project GAS kamu
2. Copy-paste isi `Code.gs` ke editor
3. Di bagian atas `Code.gs`, ubah `ALLOWED_ORIGINS`:
   ```javascript
   var ALLOWED_ORIGINS = [
     'https://nama-app-kamu.vercel.app',  // ← URL Vercel kamu
     'http://localhost:3000'               // untuk dev lokal
   ];
   ```
4. Klik **Deploy → New Deployment**
   - Type: **Web App**
   - Execute as: **Me**
   - Who has access: **Anyone**
5. Copy URL deployment (bentuknya: `https://script.google.com/macros/s/AKfycb.../exec`)

### 2. Set GAS_URL di index.html

Buka `index.html`, cari baris:
```javascript
var GAS_URL = window.GAS_URL || '';
```
Ganti dengan URL GAS kamu:
```javascript
var GAS_URL = window.GAS_URL || 'https://script.google.com/macros/s/AKfycb.../exec';
```

### 3. Deploy ke Vercel

**Cara A — via GitHub (recommended):**
1. Push repo ini ke GitHub
2. Buka [vercel.com](https://vercel.com) → New Project → Import repo
3. Framework preset: **Other**
4. Klik Deploy — selesai!

**Cara B — via Vercel CLI:**
```bash
npm i -g vercel
vercel
```

---

## Alur Komunikasi

```
Browser (Vercel)
    │
    │  fetch POST ke GAS_URL
    │  body: { action: 'loginUser', args: ['admin', 'pass'] }
    │
    ▼
Google Apps Script (doPost)
    │
    │  routing ke fungsi yang sesuai
    │  return JSON response
    │
    ▼
Google Spreadsheet (database)
```

---

## CORS

GAS Web App dengan akses **Anyone** otomatis mengizinkan request dari semua origin.
`ALLOWED_ORIGINS` di `Code.gs` adalah untuk validasi tambahan di sisi GAS (opsional).

---

## Development Lokal

Buka `index.html` langsung di browser, atau pakai Live Server VS Code.
Pastikan GAS_URL sudah diisi dan GAS sudah di-deploy dengan akses **Anyone**.
