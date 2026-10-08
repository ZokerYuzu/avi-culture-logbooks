# Laporan Logbook Harian Magang — 2026-10-08

- **Tanggal Pelaporan**: Kamis, 08 Oktober 2026
- **Jam Pelaporan**: 16:30 WIB
- **Proyek Acuan**: AVI Culture (`avi-culture-mysql`)
- **Web Live**: [https://culture.yudhapratama.my.id/](https://culture.yudhapratama.my.id/)
- **Cabang Git (Branch)**: `refactor/achpi`
- **Bukti Dukung (Screenshot)**: [screenshot.png](screenshot.png)

---

## 1. Kegiatan yang Dilakukan (Deskripsi Pekerjaan)
Menyelesaikan implementasi sistem lokalisasi bilingual (ID/EN) menyeluruh dari Portal Karyawan, Ruang Kerja Manajer, hingga Executive BOD Cockpit (Milestone 181–184), memperluas kamus `useLocale` dengan interpolasi parameter dinamis, menegakkan aturan privasi kuorum $n \ge 10$, mengeksekusi perbaikan keamanan kritis prioritas P0 dari temuan QA (penghapusan dev hint password, pembatasan simulasi peran/impersonasi, standardisasi masking NIK), serta menambahkan perintah pembersihan data uji (`avi:clean-test-data`) dan otomatisasi tangkapan layar web live.

---

## 2. Rincian Capaian & Modul yang Dikerjakan

### A. Lokalisasi Bilingual Penuh (ID / EN)
- **Milestone 181 (Infrastruktur Kamus & Interpolasi)**:
  - Perluasan kamus bilingual terpusat di `resources/js/Composables/useLocale.js` dengan 100+ frasa operasional aviasi dan BUMN.
  - Implementasi interpolasi parameter dinamis (`{target}`, `{score}`, `{count}`, `{gap}`, `{cycle}`, `{date}`, `{level}`).
- **Milestone 182 (Portal Partisipasi Karyawan)**:
  - Lokalisasi penuh modul `/survey`: sapaan dinamis waktu, kartu bento tahapan sensus, dialog panduan, modal privasi blind token, kartu konfirmasi rampung, dan tanda terima resmi SK-115.
  - Penegakan baku ambang batas privasi responden $n \ge 10$ (Aturan R8 / Guardrail 4).
- **Milestone 183 (Ruang Kerja Manajer & Admin)**:
  - Lokalisasi modul `/dashboard/unit`: 4 Bento Cards (Tachometer A-CHPI, Sebaran Tim, Ribbon 3 Pilar, Radar Diagnostik AKHLAK), 4 KPI eksekutif, filter toolbar, dan tabel master divisi / grid card view dengan penyamaran privasi.
- **Milestone 184 (Executive BOD Cockpit & Subcomponents)**:
  - Lokalisasi modul `/dashboard/bod`: banner metodologi empiris §4.3, panel risiko korporat, A-CHPI gauge, donut behavior mix, Culture Priority Charter, radar keselamatan penerbangan, tabel master unit, serta slide-over drawer `BodUnitDetailDrawer.vue`.
  - Pembuatan script PowerShell otomatisasi tangkapan layar live web (`capture_screenshot.ps1`).

### B. Mitigasi Keamanan & Perbaikan P0 (QA 8 Okt 2026)
- **Milestone 185 (Keamanan & Masking Data)**:
  - **T-01**: Menghapus hint password dan auto-prefill kredensial pada halaman login (`resources/js/Pages/Auth/Login.vue`).
  - **T-02**: Membatasi fitur "Simulasi Peran" (role impersonation) pada endpoint `/demo-switch` dan komponen navigasi agar hanya dapat diakses oleh admin sistem dan akun penguji resmi (`config('auth.tester_emails')`). Akses luar di-reject dengan HTTP 403 Forbidden.
  - **T-03**: Menyeragamkan format masking NIK (`maskNik` / `ADM0****`) pada tanda terima sensus karyawan (`SurveyParticipationReceiptCard.vue`), modul registri karyawan, dan manajemen akun.
  - **T-22 & Pembersihan Data**: Penyesuaian batas rekomendasi sesuai defisit nyata, null-safety pada `BuildUserAdministrationService`, serta perintah artisan `avi:clean-test-data` untuk sanitasi data uji `TEST%` dan `TINY%`.

---

## 3. Hasil / Output
- Seluruh antarmuka web (Portal Karyawan, Ruang Kerja Manajer, BOD Cockpit, dan Header Admin) dapat berganti bahasa ID/EN secara instan dan reaktif tanpa reload.
- Celah keamanan dev hint dan unauthorized role switching berhasil ditutup rapat.
- Format penyamaran identitas NIK seragam di seluruh portal aplikasi.
- Seluruh 337 unit dan feature tests (`php artisan test`) lulus 100%.
- Build aset produksi Vite (`npm run build`) sukses tanpa peringatan kompilasi.
- Dokumentasi perubahan tercatat detail pada `CHANGELOG.md` (Milestone 181–185).

---

## 4. Kendala / Catatan
- Tidak ada kendala fatal. Penanganan isolasi transaksi database lintas koneksi (`identity_store` dan `response_store`) pada pengujian end-to-end telah diselesaikan dengan integrasi rollback yang tepat.

---

## 5. Refleksi / Yang Dipelajari
- Menekankan pentingnya prinsip *defense-in-depth* pada fitur-fitur administratif seperti role switching / impersonation agar tidak dieksploitasi di lingkungan produksi.
- Konsistensi perlindungan data pribadi (masking NIK dan $n \ge 10$ k-anonymity) harus ditegakkan secara serentak di level backend (DTO/query suppression) maupun frontend display helper.
