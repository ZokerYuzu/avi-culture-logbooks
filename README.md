# AVI Culture — Daily Internship Logbooks

Repositori publik khusus arsip laporan logbook harian magang dan tangkapan layar bukti kerja proyek **AVI Culture** (`avi-culture-mysql`).

> [!NOTE]
> Repositori ini diperbarui secara otomatis setiap hari kerja pada pukul **16:30 WIB** oleh background automation Antigravity dan dikonsumsi langsung oleh AI agent personal (Rin) melalui GitHub API untuk pencatatan otomatis ke Google Sheets.

## Struktur Repositori (Per-Folder Harian)
```
.
├── README.md
├── 2026-10-08/
│   ├── logbook.md           # Laporan harian human-readable (Markdown)
│   ├── logbook.json         # Payload terstruktur machine-readable (JSON untuk Rin)
│   └── screenshot.png       # Screenshot bukti kerja harian
└── YYYY-MM-DD/
    ├── logbook.md
    ├── logbook.json
    └── screenshot.png
```

## Akses GitHub API untuk Rin
- **JSON Endpoint**: `https://api.github.com/repos/ZokerYuzu/avi-culture-logbooks/contents/{YYYY-MM-DD}/logbook.json`
- **Raw JSON**: `https://raw.githubusercontent.com/ZokerYuzu/avi-culture-logbooks/main/{YYYY-MM-DD}/logbook.json`
- **Screenshot**: `https://raw.githubusercontent.com/ZokerYuzu/avi-culture-logbooks/main/{YYYY-MM-DD}/screenshot.png`
