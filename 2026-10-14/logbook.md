# Laporan Logbook Harian Magang — 2026-10-14

- **Tanggal Pelaporan**: Rabu, 14 Oktober 2026
- **Jam Pelaporan**: 16:30 WIB
- **Proyek Acuan**: AVI Culture (`avi-culture-mysql`)
- **Web Live**: [https://culture.yudhapratama.my.id/](https://culture.yudhapratama.my.id/)
- **Cabang Git (Branch)**: `restore/sidebar-layout`
- **Bukti Dukung (Screenshot)**: [screenshot.png](screenshot.png)

---

## 1. Kegiatan yang Dilakukan (Deskripsi Pekerjaan)

Menyelesaikan Milestone 213 hingga Milestone 222 pada platform pengukuran dan transformasi budaya korporat AVI Culture (A-CHPI), mencakup penguatan integritas metodologis Manual A-CHPI v1.0 (Metodologi Fix B4, I1, I2, I3), penyelarasan telemetri visual 4 tingkat maturitas korporat, serta pemulihan menyeluruh tata letak antarmuka ke standar Sidebar Klasik (Batch 1 s/d Batch 6):

1. **Metodologi Fix B4 / Checklist B4 & E4 (Milestone 213)**: Mengimplementasikan indeks kesepakatan kelompok $r_{wg}$ (Formula F11) dan korelasi intra-kelas korporat (*Intraclass Correlation Coefficient* / ICC(1) & ICC(2)) untuk menguji kelayakan agregasi skor unit kerja. Sistem menetapkan ambang kelolosan $r_{wg} \ge 0.70$ serta mendeteksi polarisasi pandangan internal tim bandara.
2. **Metodologi Fix I1 / Checklist I1 & Aturan R13 (Milestone 215)**: Membangun mekanisme pembekuan skor imutabel berbasis snapshot tabel `score_runs` di schema `response_store`. Melindungi skor terbit dari mutasi langsung (`DomainException`), menyimpan konteks komputasi utuh dengan tanda tangan SHA-256 *idempotency hash*, serta menyediakan alur *restatement* historis resmi (`restate()`).
3. **Metodologi Fix I2 / Checklist I2 & §9.5 (Milestone 216)**: Mengembangkan arsitektur versioning instrumen survei dan alur *copy-draft* siklus baru (`CopyDraftInstrumentService`). Butir instrumen baru diwariskan dengan penelusuran lineage sumber (`source_instrument_id`), penomoran versi semantik, status validasi `draft`, serta proteksi pemblokiran jika siklus telah memiliki respons responden.
4. **Metodologi Fix I3 / Checklist I3, §13.3 & Tabel 13.1 (Milestone 217)**: Menerapkan tata kelola *Maker-Checker Approval* dan jejak audit restatement historis dengan penegakan prinsip empat mata (*Four-Eyes Principle*). Maker dilarang keras menyetujui pengajuannya sendiri, setiap keputusan mengikat *cryptographic signature hash* SHA-256, dan peristiwa audit terekam pada rantai tak terbantahkan `HashChainAuditService`.
5. **Penyelarasan Telemetri Maturitas Korporat (Milestone 214 & 218)**: Menyelaraskan kembali gauge telemetri dan tampilan status maturitas ke 4 kategori korporat baku yang intuitif (*Guardian*, *Role Model*, *Good*, *Need Improvement*). Menjamin keselarasan dengan baseline strategis korporat 80.0 serta penegakan otomatis predikat *Need Improvement* saat terjadi penahanan *Safety Gate*.
6. **Pemulihan Menyeluruh Tata Letak Sidebar Navigasi Klasik (Batch 1 s/d Batch 6 — Milestone 219 s/d 222)**:
   - Mengeliminasi komponen banner hero duplikatif dan monolitik (`AdminHeader`, `BodHeader`, `ManagerHeader`, `EmployeeHeader`) beserta tumpukan negative margin (`-mt-20 sm:-mt-24 lg:-mt-26`).
   - Mengembalikan arsitektur layout terpadu berbasis Sidebar (`SidebarNav.vue`) dan Topbar (`TopNavbar.vue`) pada seluruh modul aplikasi:
     - **Batch 1**: Fondasi arsitektur `<AppLayout>` dan navigasi topbar/sidebar.
     - **Batch 2**: Konsol Utama Admin ([`Overview.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/Overview.vue), [`UserManagement.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Employee/UserManagement.vue), [`AuditDashboard.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/AuditDashboard.vue)).
     - **Batch 3**: Modul Tata Kelola Admin (Rencana Aksi 12 Bulan, Berita Acara Approval Desk, Hub Pelaporan SK-115, Katalog Formula 71 Indikator, Manajemen Siklus Sensus, Budaya Journey, dan Registri Budaya Karyawan).
     - **Batch 4**: Ruang Eksekutif BOD Cockpit ([`BodCockpit.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/BodCockpit.vue)).
     - **Batch 5**: Ruang Kerja Manajer Divisi ([`UnitManager.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/UnitManager.vue)).
     - **Batch 6**: Portal Partisipasi Karyawan ([`SurveyIndex.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Survey/SurveyIndex.vue)) & Bukti Partisipasi ([`SurveyComplete.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Survey/SurveyComplete.vue)).

---

## 2. Rincian Capaian & Modul yang Dikerjakan

### A. Metodologi Fix B4: Indeks Agreement Kelompok ($r_{wg}$) & Corporate ICC (Milestone 213)
- **Kepatuhan Manual Metodologi A-CHPI v1.0 §11.2 & Formula F11**:
  - Menghitung nilai kesepakatan intra-kelompok $r_{wg}$ dan multi-item $r_{wg(J)}$ menggunakan distribusi null seragam ($\sigma_e^2 = 2.0$ untuk skala Likert 5-titik):
    $$r_{wg} = 1 - \frac{S_x^2}{\sigma_e^2}$$
  - Evaluasi gate $r_{wg} \ge 0.70$: unit dengan kesepakatan kuat dinyatakan valid untuk diagregasi, sedangkan unit dengan nilai di bawah ambang diberi penanda dispersi dan peringatan polarisasi internal.
  - Menjalankan analisis varians satu arah (One-Way Random Effects ANOVA) untuk menghitung korelasi intra-kelas korporat ICC(1) dan reliabilitas rata-rata kelompok ICC(2) $\ge 0.70$.
- **Backend & Database**:
  - Migrasi `2026_10_14_091501_response_store_add_rwg_and_agreement_to_unit_scores_table.php` menambahkan kolom `rwg_score`, `rwg_status`, dan `agreement_data` pada tabel `unit_scores`.
  - Service domain [`ComputeGroupAgreementService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Scoring/Services/ComputeGroupAgreementService.php) dan DTO [`GroupAgreementDTO.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Scoring/DTOs/GroupAgreementDTO.php).
  - Feature test [`GroupAgreementIndexTest.php`](file:///c:/laragon/www/avi-culture-mysql/tests/Feature/Scoring/GroupAgreementIndexTest.php) (8/8 skenario passed, 54 assertions).

### B. Metodologi Fix I1: Pembekuan Skor Imutabel & Snapshot score_runs (Milestone 215)
- **Kepatuhan Aturan R13 & §13.3**:
  - Skor historis dilarang keras dihitung ulang secara diam-diam. Setiap skor yang diterbitkan dibekukan imutabel bersama snapshot bobot, threshold, gates, dan ringkasan skor unit.
- **Backend & Database**:
  - Migrasi `2026_10_14_094501_response_store_create_score_runs_table.php` membuat tabel `score_runs` di schema `response_store`.
  - Model [`ScoreRun.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Scoring/Models/ScoreRun.php) dengan event hook proteksi pembekuan (melempar `DomainException` jika dicoba update/delete saat `is_frozen = true`).
  - Service domain [`CreateScoreRunSnapshotService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Scoring/Services/CreateScoreRunSnapshotService.php) dengan deteksi idempotensi hash SHA-256 dan alur `restate()`.
  - Feature test [`ScoreRunSnapshotTest.php`](file:///c:/laragon/www/avi-culture-mysql/tests/Feature/Scoring/ScoreRunSnapshotTest.php) (7/7 skenario passed, 47 assertions).

### C. Metodologi Fix I2: Versioning Instrumen & Alur Copy-Draft (Milestone 216)
- **Kepatuhan Aturan R13 & §9.5**:
  - Penambahan siklus survei baru menyalin instrumen dari siklus acuan dengan nomor versi semantik dan status awal `draft`. Butir instrumen mempertahankan lineage asal (`source_instrument_id`).
  - Menghalangi perubahan retrospektif instrumen bila siklus telah mengoleksi respons responden riil.
- **Backend & Database**:
  - Migrasi `2026_10_14_095501_response_store_add_versioning_and_lineage_to_survey_instruments_table.php`.
  - Service domain [`CopyDraftInstrumentService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Services/CopyDraftInstrumentService.php) dan pengintegrasian pada [`CreateSurveyCycleService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Services/CreateSurveyCycleService.php).
  - Feature test [`InstrumentVersioningAndCopyDraftTest.php`](file:///c:/laragon/www/avi-culture-mysql/tests/Feature/Survey/InstrumentVersioningAndCopyDraftTest.php) (4/4 passed, 25 assertions).

### D. Metodologi Fix I3: Maker-Checker Approval & Jejak Audit Restatement (Milestone 217)
- **Kepatuhan Prinsip Empat Mata (Four-Eyes Principle, §13.3, §15.4, & Tabel 13.1)**:
  - Usulan perubahan instrumen atau restatement skor wajib diajukan oleh Maker dan disahkan oleh Checker independen yang berbeda (`checkerId !== maker_id`).
  - Pengesahan menghasilkan segel digital SHA-256 dan mencatat peristiwa audit ke `HashChainAuditService`.
- **Backend & Database**:
  - Migrasi `2026_10_14_100501_response_store_create_maker_checker_requests_table.php` membuat tabel `maker_checker_requests`.
  - Service domain [`MakerCheckerService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Approval/Services/MakerCheckerService.php), Model [`MakerCheckerRequest.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Approval/Models/MakerCheckerRequest.php), dan Controller [`MakerCheckerController.php`](file:///c:/laragon/www/avi-culture-mysql/app/Http/Controllers/Dashboard/MakerCheckerController.php).
  - Feature test [`MakerCheckerAndRestatementAuditTest.php`](file:///c:/laragon/www/avi-culture-mysql/tests/Feature/Approval/MakerCheckerAndRestatementAuditTest.php) (6/6 passed, 38 assertions).

### E. Penyelarasan Telemetri Maturitas Korporat (Milestone 214 & 218)
- Menyelaraskan komponen visual [`AchpiGauge.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Components/Charts/AchpiGauge.vue) dan utilities [`utils.js`](file:///c:/laragon/www/avi-culture-mysql/resources/js/lib/utils.js) ke 4 tingkat kategori korporat baku:
  1. **Guardian** (90.1 – 100.0, Deep Teal `#005257`)
  2. **Role Model** (80.1 – 90.0, Teal `#00A1AD`)
  3. **Good** (75.0 – 80.0, Kuning Emas `#F2B946`)
  4. **Need Improvement** (0.0 – 74.9 atau Safety Gate Breached, Merah Coral `#BC4131`)
- Menjamin unit kerja yang terkena penahanan gerbang keselamatan (*Safety Gate Breached*) secara otomatis diberi label dan styling *Need Improvement*.

### F. Pemulihan Tata Letak Sidebar Navigasi Klasik (Batch 1 s/d Batch 6)
- **Eliminasi Desain Header Bertingkat / Hero Monolitik**:
  - Menghapus komponen hero banner `<AdminHeader>`, `<BodHeader>`, `<ManagerHeader>`, dan `<EmployeeHeader>` beserta negative margin tumpang tindih.
- **Konsistensi Hierarki UI & Navigasi**:
  - Seluruh halaman utama kini menggunakan layout tunggal yang konsisten berbasis `<AppLayout>` dengan Sidebar Navigasi terstruktur di sisi kiri dan Top Navbar di bagian atas.
  - Menempatkan komponen ringkas [`PageHeader.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Components/PageHeader.vue) untuk menyajikan breadcrumbs, judul halaman kontekstual, indikator siklus aktif, dan tombol aksi terstandarisasi.
  - Segmented navigation tabs disusun rapi di bawah header tanpa tabrakan layout vertikal pada:
    - Konsol Admin ([`Overview.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/Overview.vue), [`UserManagement.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Employee/UserManagement.vue), [`AuditDashboard.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/AuditDashboard.vue)).
    - Modul Tata Kelola Admin (Rencana Aksi, Approval Desk, SK-115 Report Hub, Katalog Formula, Manajemen Sensus, Culture Journey, Registri Budaya).
    - Ruang Eksekutif BOD Cockpit ([`BodCockpit.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/BodCockpit.vue)).
    - Workspace Manajer Divisi ([`UnitManager.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/UnitManager.vue)).
    - Portal Partisipasi Karyawan ([`SurveyIndex.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Survey/SurveyIndex.vue)) & Halaman Tanda Terima Selesai ([`SurveyComplete.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Survey/SurveyComplete.vue)).

---

## 3. Hasil / Output

- **Integritas Metodologis Terlindungi Penuh**: Implementasi lengkap Metodologi Fix B4, I1, I2, dan I3 berhasil menegakkan keabsahan ilmiah asesmen budaya korporat sesuai Manual Metodologi A-CHPI v1.0.
- **Sistem Pembekuan Skor & Jejak Audit Kriptografis**: Skor komposit resmi dibekukan imutabel pada `score_runs`, perubahan instrumen terlacak lineage versinya, dan pengesahan maker-checker terikat hash SHA-256.
- **Desain UI/UX Konsisten & Ergonomis**: Tata letak sidebar navigasi klasik berhasil dipulihkan secara terpadu di 6 batch pada seluruh peran pengguna (Direksi, Manajer, Admin, dan Karyawan), mengeliminasi kekacauan visual banner hero dan negative margin.
- **Hasil Pengujian Backend**: Seluruh 399 unit dan feature tests (`php artisan test`) lulus 100% tanpa kegagalan (0 failures, 3033 assertions).
- **Hasil Build Frontend**: Vite production build (`npm run build`) sukses 0 error (durasi build ~4.02s).
- **Dokumentasi Lengkap**: Riwayat Milestone 213–222 tercatat rinci pada `CHANGELOG.md`.

---

## 4. Kendala / Catatan

- Tidak ada kendala fatal yang menghambat sistem. Pemulihan tata letak sidebar pada seluruh halaman aplikasi memerlukan ketelitian visual tinggi agar integrasi tab navigasi segmented, filter unit dinamis, dan komponen gauge tidak terpotong pada berbagai resolusi layar serta tetap memenuhi standar kontras WCAG.

---

## 5. Refleksi / Yang Dipelajari

- **Pentingnya Pemisahan Kewenangan (Four-Eyes Principle)**: Penegakan Maker-Checker pada sistem pengukuran korporat merupakan safeguard mutlak untuk menjamin akuntabilitas data, mencegah manipulasi skor sepihak, dan memenuhi standar kepatuhan audit BUMN/penerbangan.
- **Imutabilitas Data Historis**: Menyimpan snapshot kalkulasi lengkap yang dibekukan (`is_frozen = true`) jauh lebih unggul dan transparan daripada menghitung ulang skor masa lalu secara dinamis, karena menjamin hasil audit masa lalu selalu dapat direkonstruksi persis sama.
- **Konsistensi Desain Tata Letak (Design System Consistency)**: Menggunakan arsitektur layout tunggal berbasis Sidebar (`AppLayout.vue`) memberikan kepastian navigasi kognitif bagi pengguna di berbagai level jabatan serta memudahkan pemeliharaan kode frontend jangka panjang dibandingkan penggunaan banner hero monolitik yang berbeda-beda di tiap halaman.
