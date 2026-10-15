# Laporan Logbook Harian Magang — 2026-10-15

- **Tanggal Pelaporan**: Kamis, 15 Oktober 2026
- **Jam Pelaporan**: 16:30 WIB
- **Proyek Acuan**: AVI Culture (`avi-culture-mysql`)
- **Web Live**: [https://culture.yudhapratama.my.id/](https://culture.yudhapratama.my.id/)
- **Cabang Git (Branch)**: `restore/sidebar-layout`
- **Bukti Dukung (Screenshot)**: [screenshot.png](screenshot.png)

---

## 1. Kegiatan yang Dilakukan (Deskripsi Pekerjaan)

Menyelesaikan Milestone 223 hingga Milestone 229 pada platform pengukuran dan transformasi budaya korporat AVI Culture (A-CHPI), mencakup perbaikan siklus hidup Vue dan pembersihan navigasi UI, integrasi paket skill agen dan standarisasi panduan operasional 3-layer di `AGENTS.md`, remediasi hasil audit mutu kode komprehensif Ponytail, eliminasi lebih dari 1.500 baris kode mati (*dead code*), serta eksekusi bertahap remediasi data *hardcoded* (Tahap 1, 2, dan 3):

1. **Pembersihan Navigasi UI & Perbaikan Lifecycle Hook Vue (Milestone 223)**:
   - Memperbaiki bug kritis runtime crash (`Uncaught ReferenceError: onMounted is not defined`) pada halaman Registri Budaya Karyawan ([`EmployeeCultureRegistry.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Employee/EmployeeCultureRegistry.vue)) yang menyebabkan menu sidebar "Data Karyawan" tidak dapat dibuka.
   - Menghapus deretan tombol sub-tab horizontal redundan di [`SurveyIndex.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Survey/SurveyIndex.vue), [`UnitManager.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/UnitManager.vue), dan [`BodCockpit.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/BodCockpit.vue) karena seluruh alur pergerakan halaman telah dipusatkan melalui SidebarNav terpadu.
   - Menambahkan item menu `Suara Insan Aviasi` (`/survey?tab=phenomena`) pada [`useNavigation.js`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Components/Layout/useNavigation.js).

2. **Integrasi Paket Skill Arsitektur & TDD (Milestone 224)**:
   - Mengintegrasikan paket skill agen AI (`mattpocock/skills`) ke dalam direktori `.agents/skills/` (mencakup `code-review`, `codebase-design`, `diagnosing-bugs`, `domain-modeling`, `git-guardrails-claude-code`, `tdd`, dll.) serta mengunci integritas dependensi pada `skills-lock.json`.

3. **Integrasi Suite Ponytail & Standardisasi Panduan 3-Layer di AGENTS.md (Milestone 225)**:
   - Mengadopsi filosofi *lazy senior developer* melalui integrasi paket skill `DietrichGebert/ponytail` (`ponytail`, `ponytail-audit`, `ponytail-debt`, `ponytail-gain`, `ponytail-help`, `ponytail-review`) guna mengutamakan solusi tuntas terkecil (*smallest complete change*) dan memangkas over-engineering (*YAGNI*).
   - Menyusun standardisasi operasional 3-Layer pada [`AGENTS.md`](file:///c:/laragon/www/avi-culture-mysql/AGENTS.md):
     - **Layer 1: Always Active Baseline** (Caveman, Ponytail, Anti-Slop, 21st UI).
     - **Layer 2: Lifecycle Trigger Matrix** (Penyelarasan & Rencana, Investigasi Bug, Logika Domain/Engine, Antarmuka/Komponen Vue, Audit Pra-Commit, Rilis & Dokumentasi).
     - **Layer 3: On-Demand Utility Commands** (`ponytail-audit`, `ponytail-review`, `ponytail-debt`, dll.).

4. **Eksekusi Remediasi Komprehensif Hasil Audit Mutu Ponytail (Milestone 226)**:
   - Mengisolasi rute pengujian `/demo-switch` di [`routes/web.php`](file:///c:/laragon/www/avi-culture-mysql/routes/web.php) ke dalam blok environment `local` dan `testing` agar tertutup sepenuhnya di staging/produksi.
   - Menegakkan Aturan Keras No. 6 pada [`GetBodCockpitDataService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Reporting/Services/GetBodCockpitDataService.php) dengan mengganti bobot literal statis (`0.45`, `0.40`, `0.15`) menjadi pembacaan dinamis dari konfigurasi aktif (`ScoringConfig::getActive()`).
   - Memitigasi *race condition* validasi kuota 5 rekan (FR-1.9) pada [`ProcessPeerEvaluationService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Services/ProcessPeerEvaluationService.php) ke dalam transaksi basis data `response_store`.
   - Mengganti `UnitScore::create()` menjadi `firstOrCreate()` pada [`GetUnitDashboardDataService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Reporting/Services/GetUnitDashboardDataService.php) untuk mencegah galat duplikasi SQL (500 Server Error) saat request konkuren pada dashboard GET.
   - Mengeliminasi potensi query N+1 pada pengecekan k-anonymity (`meetsKAnonymity()`) via eager loading `with('scoringConfig')`.
   - Mendinamisasikan tahun rollover pada `ProcessMonthlyRolloverJob.php`, `ProcessMonthlyRolloverService.php`, dan `ActionPlanController.php` menjadi `now()->year`.
   - Menghapus 5 berkas komponen header/hero gelap legacy yang tidak terpakai (`AdminHeader.vue`, `BodHeader.vue`, `ManagerHeader.vue`, `EmployeeHeader.vue`, `EmployeeParticleWave.vue`) memangkas lebih dari 1.540 baris kode mati (*dead code*, -79 KB).

5. **Eksekusi Remediasi Hardcode Tahap 1: Keamanan & Integritas Data (Milestone 227)**:
   - Menutup celah bypass otorisasi admin via pencocokan string alamat email di 6 titik backend: [`EnsureRole.php`](file:///c:/laragon/www/avi-culture-mysql/app/Http/Middleware/EnsureRole.php), [`HandleInertiaRequests.php`](file:///c:/laragon/www/avi-culture-mysql/app/Http/Middleware/HandleInertiaRequests.php), [`RedirectByRole.php`](file:///c:/laragon/www/avi-culture-mysql/app/Http/Middleware/RedirectByRole.php), [`CustomLoginResponse.php`](file:///c:/laragon/www/avi-culture-mysql/app/Http/Responses/CustomLoginResponse.php), [`routes/web.php`](file:///c:/laragon/www/avi-culture-mysql/routes/web.php), dan [`GetEmployeeCultureRegistryService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Identity/Services/GetEmployeeCultureRegistryService.php), menegakkan otorisasi murni berbasis Spatie DB role `$user->hasRole('admin')`.
   - Membersihkan seluruh perbandingan string email statis pada 10 komponen Vue dan utility navigasi frontend (`useNavigation.js`, `CommandPalette.vue`, `ActionPlan/Index.vue`, `ApprovalDesk.vue`, `Overview.vue`, `UnitManager.vue`, `EmployeeCultureRegistry.vue`, `CultureJourney.vue`, `Sk115ReportHub.vue`, `FormulaCatalog.vue`).
   - Menghapus sintesis data persentase perilaku palsu (*Guardian 25%, Role Model 45%, Good 22%, Needs Improvement 8%*) pada [`GetUnitDashboardDataService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Reporting/Services/GetUnitDashboardDataService.php) dan [`UnitManager.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/UnitManager.vue) jika profil karyawan kosong, mengembalikan nilai riil `count: 0` dan `0.0%`.
   - Menghapus mock unit CEO Office statis (`defaultCeoUnits`) pada [`BodCockpit.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/BodCockpit.vue) dan menggantikannya dengan pemetaan dinamis berbasis `props.unitScores` disertai *clean empty state*.

6. **Eksekusi Remediasi Hardcode Tahap 2: Katalog & Seeder Runtime (Milestone 228)**:
   - Menghapus impor dan pemanggilan seeder database (`PeerQuestionCatalogSeeder`) di lingkungan produksi.
   - Membangun Domain Service resmi baru [`PeerQuestionCatalogService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Services/PeerQuestionCatalogService.php) untuk menaungi kamus butir perilaku bercabang NPS AKHLAK dan 24 butir evaluasi ABMI 360 multi-rater.
   - Merefaktor `PeerQuestionCatalogSeeder`, `GetBodCockpitDataService`, `GetUnitDashboardDataService`, `GetSurveyLandingDataService`, dan `SurveyController` agar murni mengonsumsi domain service tersebut.
   - Mendinamisasikan penentuan unit kerja di bawah lingkup Direksi/DIR pada [`User.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Identity/Models/User.php) dan memperkaya deteksi struktur direktorat secara dinamis pada [`BuildCultureDashboardService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Reporting/Services/BuildCultureDashboardService.php).
   - Mendinamisasikan katalog butir terbuka fenomena budaya melalui [`CulturePhenomenon::getItemsCatalog($cycleId)`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Models/CulturePhenomenon.php) yang memprioritaskan instrumen kustom per siklus dari tabel `survey_instruments`.
   - Menegakkan Aturan Keras No. 6 pada [`IndicatorCatalogService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Scoring/Services/IndicatorCatalogService.php) dengan mengambil bobot indeks dinamis dari `ScoringConfig::getActive()`.

7. **Eksekusi Remediasi Hardcode Tahap 3: Kriptografi & Metadata Pelaporan (Milestone 229)**:
   - Mengganti literal salt kriptografi statis (`'AVI-SECRET-STAMP'` dan `'AVI-MAKER-CHECKER-SEAL'`) pada [`DigitalApproval.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Approval/Models/DigitalApproval.php) dan [`MakerCheckerRequest.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Approval/Models/MakerCheckerRequest.php) dengan konfigurasi dinamis aman aplikasi `(string) config('app.key')`.
   - Mendinamisasikan tahun dan penanggalan tenggat pelaporan kepatuhan SK-115 Dekom (31 Januari) dan KBUMN (28 Februari) pada [`GenerateSk115ReportService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Reporting/Services/GenerateSk115ReportService.php) berbasis tahun siklus survei aktif (`$cycleYear`).
   - Mendinamisasikan nomor docket berita acara pengesahan komitmen budaya unit (`BA.01/KNO/{unit}/{year}`) dan payload tanda tangan digital manajer unit pada [`GetUnitDashboardDataService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Reporting/Services/GetUnitDashboardDataService.php) berbasis `$cycleYear`.

---

## 2. Rincian Capaian & Modul yang Dikerjakan

### A. Perbaikan Runtime Lifecycle & Higienitas UI (Milestone 223)
- Menuntaskan *white screen error* pada halaman Registri Karyawan dengan mengimpor hooks `onMounted` dan `onBeforeUnmount` di `EmployeeCultureRegistry.vue`.
- Menghapus tab navigasi sekunder yang menduplikasi menu samping pada halaman partisipasi survei, dashboard manajer, dan cockpit direksi.
- Mengintegrasikan rute query `/survey?tab=phenomena` langsung ke sidebar navigasi karyawan.

### B. Standardisasi Framework Agen 3-Layer & Integrasi Ponytail (Milestone 224 & 225)
- Memasang paket skill `mattpocock/skills` dan `DietrichGebert/ponytail` ke dalam repositori.
- Menetapkan aturan keras No. 9 di `AGENTS.md` yang mewajibkan penerapan 4 pilar utama:
  1. **Caveman Mode**: Komunikasi ultra-ringkas dan hemat token.
  2. **Ponytail Philosophy**: Solusi tuntas terkecil (*smallest complete change*), pencegahan over-engineering (*YAGNI*).
  3. **Anti-Slop Core Filter**: Kualitas visual UI, copy operasional korporat/penerbangan nyata, aksesibilitas WCAG, reflow mobile, dan higienitas komentar kode.
  4. **21st UI Standards**: Standar komponen antarmuka modern.
- Membangun tabel *Lifecycle Trigger Matrix* yang mengikat tahapan pengembangan ke tool skill spesifik.

### C. Remediasi Mutu Kode & Eliminasi Dead Code (Milestone 226)
- **Security**: Isolasi rute `/demo-switch` ke lingkungan non-produksi.
- **Scoring Engine**: Kalkulasi komposit korporat kini 100% dinamis membaca bobot ABMI, OHCI, dan CBOI dari `ScoringConfig` (mematuhi Aturan Keras No. 6).
- **Konkurensi & DB**: Validasi kuota 5 evaluasi rekan kini dibungkus transaksi basis data atomik; penulisan skor unit pada dashboard GET menggunakan `firstOrCreate()` guna mencegah SQL duplicate error; eliminasi query N+1 pada evaluasi k-anonymity.
- **Dead Code**: Menghapus 5 file banner header legacy (`AdminHeader.vue`, `BodHeader.vue`, `ManagerHeader.vue`, `EmployeeHeader.vue`, `EmployeeParticleWave.vue`), memangkas 1.540 baris kode yang tak lagi digunakan.

### D. Remediasi Hardcode Tahap 1: Keamanan & Integritas Data (Milestone 227)
- Menghapus celah *hardcoded email bypass* pada 6 file backend middleware/controller dan 10 komponen Vue frontend, memastikan otorisasi admin murni berbasis relasi role basis data Spatie.
- Mengeliminasi rekaan persentase sebaran perilaku manajer unit jika data kosong, mengembalikan nilai riil `0` dan `0.0%`.
- Menghapus mock data angka statis unit CEO Office di BOD Cockpit, beralih ke komputasi reaktif berbasis `props.unitScores` dengan tampilan state kosong yang bersih.

### E. Remediasi Hardcode Tahap 2: Domain Catalog & Dynamic Scoping (Milestone 228)
- Memisahkan katalog butir perilaku NPS dan ABMI 360 ke domain service resmi `PeerQuestionCatalogService`, membebaskan runtime aplikasi dari pemanggilan class seeder.
- Memperbarui `User::getDirectorateUnitCodes` dan `BuildCultureDashboardService` agar mendeteksi struktur unit kerja dan direktorat secara dinamis dari tabel pengguna.
- Mendinamisasikan penarikan instrumen survei fenomena budaya via `CulturePhenomenon::getItemsCatalog($cycleId)`.
- Menyelaraskan pohon kalkulasi indikator di `IndicatorCatalogService` agar mengambil bobot dinamis dari `ScoringConfig::getActive()`.

### F. Remediasi Hardcode Tahap 3: Kriptografi Dinamis & Metadata Pelaporan (Milestone 229)
- Menggantikan string salt statis tanda tangan digital `DigitalApproval` dan `MakerCheckerRequest` dengan salt dinamis berbasis konfigurasi aplikasi `config('app.key')`.
- Mendinamisasikan tahun dan tenggat pelaporan kepatuhan SK-115 Dekom & KBUMN pada `GenerateSk115ReportService` berbasis tahun siklus survei aktif (`$cycleYear`).
- Mendinamisasikan penomoran berita acara pengesahan komitmen budaya (`BA.01/KNO/{unit}/{year}`) dan payload tanda tangan digital manajer unit pada `GetUnitDashboardDataService`.

---

## 3. Hasil / Output

- **Keamanan & Otorisasi Kokoh**: Celah bypass otorisasi string email admin tertutup 100% dan ditegakkan murni melalui sistem Spatie DB role.
- **Kriptografi & Segel Digital Terproteksi**: Salt kriptografi digital approval dan maker-checker menggunakan salt dinamis berbasis `config('app.key')`.
- **Eliminasi Kode Mati (Dead Code)**: 5 komponen header banner legacy berhasil dihapus, memangkas lebih dari 1.540 baris kode dan menghemat ukuran bundle frontend.
- **Arsitektur Runtime Bersih**: Tidak ada lagi pemanggilan seeder database di lingkungan produksi runtime; seluruh kamus instrumen dikelola domain service resmi `PeerQuestionCatalogService`.
- **Kepatuhan Aturan Keras No. 6**: Seluruh bobot agregasi skor korporat dan pohon kalkulasi indikator membaca konfigurasi versioned dari `ScoringConfig`.
- **Ketahanan Konkurensi**: Kuota peer review dan pembentukan record skor unit terlindungi dari *race condition* dan SQL duplicate error.
- **Hasil Pengujian Backend**: Seluruh 399 unit dan feature tests (`php artisan test`) lulus 100% (3.033 assertions, 0 failures).
- **Hasil Build Frontend**: Kompilasi aset Vite (`npm run build`) sukses tanpa error (3.028 modul, durasi ~3.96s).
- **Dokumentasi Lengkap**: Rincian Milestone 223 s/d Milestone 229 terdokumentasi terinci di `CHANGELOG.md`.

---

## 4. Kendala / Catatan

- Tidak ada kendala fatal yang menghambat sistem. Pembersihan string email statis dan parameter hardcode tahunan di belasan file frontend/backend memerlukan verifikasi regresi yang sangat teliti agar otorisasi berbasis role database dan penomoran dokumen berita acara tetap berjalan mulus tanpa mengganggu alur survei maupun tampilan dashboard.

---

## 5. Refleksi / Yang Dipelajari

- **Pentingnya Pendekatan Ponytail (Smallest Complete Change)**: Menyelesaikan masalah secara tuntas dengan perubahan sekecil dan sebersih mungkin terbukti jauh lebih aman dan mudah diuji dibandingkan melakukan refactoring besar-besaran yang berisiko merusak stabilitas sistem.
- **Pembersihan Data Statis (Zero Hardcode Policy)**: Ketergantungan pada nilai konstan (seperti email admin, bobot skor, nama seeder, atau salt string) dalam kode produksi adalah utang teknis yang berbahaya. Mengalihkan seluruh data operasional ke tabel konfigurasi terversioning, domain service, dan environment key menjamin keandalan sistem dalam jangka panjang.
- **Pemisahan Seeder vs Domain Service**: File seeder semestinya hanya digunakan untuk populasi data awal development/testing. Memindahkan logika katalog ke domain service resmi merupakan langkah fundamental dalam menjaga batas arsitektur (*architectural boundary*) yang bersih.
