# Laporan Logbook Harian Magang — 2026-10-13

- **Tanggal Pelaporan**: Selasa, 13 Oktober 2026
- **Jam Pelaporan**: 16:30 WIB
- **Proyek Acuan**: AVI Culture (`avi-culture-mysql`)
- **Web Live**: [https://culture.yudhapratama.my.id/](https://culture.yudhapratama.my.id/)
- **Cabang Git (Branch)**: `refactor/achpi`
- **Bukti Dukung (Screenshot)**: [screenshot.png](screenshot.png)

---

## 1. Kegiatan yang Dilakukan (Deskripsi Pekerjaan)
Menyelesaikan Milestone 205 hingga Milestone 211 pada platform pengukuran budaya korporat AVI Culture (A-CHPI) yang berfokus pada penyelarasan komprehensif metodologi Manual A-CHPI v1.0 (Metodologi Fix M-01 s/d M-06):
1. **Penyederhanaan UI Portal Karyawan (Milestone 205)**: Mengeliminasi banner "Denyut Budaya Pekan Ini" (`CulturePulseCard`) pada tab Beranda portal karyawan (`SurveyIndex.vue`) guna merampingkan tata letak antarmuka dan memusatkan atensi visual karyawan langsung ke instrumen sensus utama.
2. **Metodologi Fix M-01 / Checklist E1+E3 (Milestone 206)**: Menghapus komposit organisasi A-CHPI dan angka default sintetis OHCI/CBOI pada tingkat individu; rapor karyawan kini murni menyajikan profil perilaku ABMI 360 riil, evaluasi refleksi mandiri, analisis *self-other gap*, dimensi unggul/defisit, serta konteks OHCI unit kerja agregat tanpa memengaruhi skor individu.
3. **Metodologi Fix M-02 / Checklist A6 (Milestone 207)**: Menegakkan Aturan R3 dan Formula F10 dengan mengisolasi penilaian mandiri (*self-report*, bobot 0.00) dari perhitungan komposit formal ABMI organisasi (bobot 45%), serta mengalihkan sumber data resmi ke evaluasi *other-rater* 360 via `ProcessPeerEvaluationService`.
4. **Metodologi Fix M-03 / Checklist A3 (Milestone 208)**: Menonaktifkan total jalur metrik NPS legacy (`nps_score` dan ratings NPS) dan menstandarkan 100% instrumen evaluasi rekan kerja ke 24 butir baku perilaku AKHLAK ABMI (skala Likert 1–5 / N/O), serta memperbaiki kode fallback pilar Kompeten dari `ABMI-KM` menjadi `ABMI-KO`.
5. **Metodologi Fix M-04 / Checklist A4+A5 (Milestone 209)**: Menyelaraskan bobot jenjang jabatan multi-rater 360 persis sesuai Tabel 9.3 Manual Metodologi A-CHPI v1.0, menyediakan katalog transparan status rater group (aktif vs unimplemented), serta mengimplementasikan supresi metodologis (Aturan A9) dengan redistribusi bobot proporsional yang menghasilkan status evaluasi `valid`.
6. **Metodologi Fix M-05 / Checklist C1–C3, D4 (Milestone 210)**: Menstandarisasi 18 KPI CBOI resmi kebandarudaraan Manual §8 pada 6 domain KPI (`sp`, `sq`, `or`, `ws`, `gc`, `pe`) dengan rumus normalisasi F7 & F8; serta menghadirkan transparansi metodologis Aturan D4 pada antarmuka web saat data CBOI belum tersedia (redistribusi dinamis ke ABMI 52.9% & OHCI 47.1% disertai banner peringatan metodologi).
7. **Metodologi Fix M-06 / Checklist H1+H2 (Milestone 211)**: Membangun modul kualitatif Fenomena Budaya 3 butir terbuka (`FEN-01`, `FEN-02`, `FEN-03`) sesuai Manual §9.6 dengan isolasi mutlak dari scoring engine (Aturan R11), skema database terisolasi di `response_store`, konsol moderasi human review & redaksi PII, serta penegakan ambang batas privasi $k$-anonymity ($n \ge 5$).

---

## 2. Rincian Capaian & Modul yang Dikerjakan

### A. Penyederhanaan UI Portal Karyawan (Milestone 205)
- **Eliminasi Banner Denyut Budaya Mingguan**:
  - Menghapus rendering dan impor `<CulturePulseCard>` dari tab Beranda sensus karyawan di [`resources/js/Pages/Survey/SurveyIndex.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Survey/SurveyIndex.vue).
  - Merampingkan *first-screen view* portal karyawan sehingga langsung menampilkan instrumen survei aktif tanpa distraksi visual.

### B. Metodologi Fix M-01: Pencabutan Komposit A-CHPI Individu & Default Sintetis (Milestone 206)
- **Kepatuhan Manual Metodologi A-CHPI v1.0 §8.6**:
  - Untuk entitas individu, metrik resmi $V$ adalah murni skor perilaku ABMI; komposit A-CHPI, OHCI, CBOI, dan tingkat maturitas hanya berlaku pada tingkat unit, direktorat, dan korporat.
- **Pembersihan Model & Service**:
  - Mencabut virtual accessors komposit organisasi individu (`achpi_composite`, `final_score`, `peer_score`, `maturity_band`, `safety_gate_breached`) dari [`EmployeeCultureProfile.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Identity/Models/EmployeeCultureProfile.php).
  - Menghapus seluruh default angka sintetis OHCI/CBOI (`80.0`, `85.0`, `82.0`, `78.0`, `84.0`) dari [`GetEmployeeCultureRegistryService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Identity/Services/GetEmployeeCultureRegistryService.php).
  - Menyajikan konteks OHCI unit kerja (`unit_ohci_context`) dari data agregasi riil tabel `unit_scores` sebagai referensi lingkungan kerja tanpa mencemari kalkulasi rapor individu.
  - Menghapus kolom komposit organisasi dan final score dari ekspor CSV pegawai di [`EmployeeCultureRegistryController.php`](file:///c:/laragon/www/avi-culture-mysql/app/Http/Controllers/Employee/EmployeeCultureRegistryController.php).

### C. Metodologi Fix M-02: Pencabutan Self-Report dari ABMI Komposit Formal (Milestone 207)
- **Kepatuhan Manual Metodologi A-CHPI v1.0 §8.6, §11.1 & Aturan R3**:
  - Kaki ABMI (45%) dari komposit formal organisasi wajib dihitung dari penilaian multi-rater 360 (*other-rater*: superior, peer, subordinate, stakeholder) menggunakan Formula F10 ($q_p = 1$).
  - Penilaian mandiri karyawan (*self-report*) diisolasi penuh (bobot 0.00) dari komposit formal dan hanya digunakan sebagai instrumen diagnostik serta analisis kesenjangan persepsi (*self-other gap*).
- **Refaktorisasi Scoring Engine**:
  - Mengintegrasikan `ProcessPeerEvaluationService` ke dalam [`ComputeCompositeScoreService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Scoring/Services/ComputeCompositeScoreService.php) dan mengimplementasikan `computeFormalAbmiFrom360()`.
  - Menambahkan properti diagnostik `selfAbmiScore` dan `selfDimensionScores` pada [`CompositeScoreDTO.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Scoring/DTOs/CompositeScoreDTO.php).
  - Menyesuaikan urutan seeder [`CEOOfficePilotSeeder.php`](file:///c:/laragon/www/avi-culture-mysql/database/seeders/CEOOfficePilotSeeder.php) agar data evaluasi rekan 360 di `response_store` telah siap saat agregasi skor unit dihitung.

### D. Metodologi Fix M-03: Penonaktifan Jalur NPS Legacy & Standardisasi 24 Butir ABMI (Milestone 208)
- **Kepatuhan Manual Metodologi A-CHPI v1.0 §7 & Lampiran A**:
  - Evaluasi perilaku 360 wajib menggunakan instrumen baku 24 butir perilaku AKHLAK skala Likert 1–5 / N/O.
- **Pembersihan & Pengamanan Endpoint**:
  - Menghapus aturan validasi `ratings` dan `nps_score` dari [`SubmitPeerEvaluationRequest.php`](file:///c:/laragon/www/avi-culture-mysql/app/Http/Requests/Survey/SubmitPeerEvaluationRequest.php).
  - Menghentikan pengiriman katalog pertanyaan NPS ke frontend pada [`PeerSurveyController.php`](file:///c:/laragon/www/avi-culture-mysql/app/Http/Controllers/Survey/PeerSurveyController.php).
  - Mengunci pemrosesan legacy NPS di [`ProcessPeerEvaluationService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Services/ProcessPeerEvaluationService.php) di balik flag proteksi `allowLegacyNps: false` (otomatis melempar `InvalidArgumentException`).
  - Memperbaiki bank butir fallback pilar Kompeten di [`PeerAssessment.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Survey/PeerAssessment.vue) dari `ABMI-KM` ke kode standar `ABMI-KO`.

### E. Metodologi Fix M-04: Standardisasi Bobot 360 Tabel 9.3 & Status Rater Group (Milestone 209)
- **Kepatuhan Manual Metodologi A-CHPI v1.0 Tabel 9.3**:
  - Supervisor / Manager: Supervisor 30%, Peers 25%, Direct Reports 30%, Stakeholders 15%, Self 0% (Diagnostic).
  - Senior Management: Supervisor 25%, Peers 20%, Direct Reports 30%, Stakeholders 25%, Self 0% (Diagnostic).
  - Staff / Pelaksana: Supervisor 45%, Peers 35%, Stakeholders 20%, Self 0% (Diagnostic).
- **Penanganan Status Metodologis (Aturan A9 & §9.4)**:
  - Mengimplementasikan `getRaterGroupCatalog()` pada [`ProcessPeerEvaluationService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Services/ProcessPeerEvaluationService.php) untuk memetakan kelompok aktif (`self`, `peer`) vs belum diimplementasikan (`superior`, `subordinate`, `stakeholder`).
  - Menjalankan supresi metodologis pada rater group yang belum aktif dan meredistribusikan bobot nominal secara proporsional ke rater aktif yang valid (peer 100% pada pilot saat ini), menghasilkan status evaluasi `valid`.
  - Meneruskan prop `raterGroups` ke view Inertia [`PeerAssessment.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Survey/PeerAssessment.vue).

### F. Metodologi Fix M-05: Penyelarasan 18 KPI CBOI & Transparansi Renormalisasi D4 (Milestone 210)
- **Kepatuhan Manual Metodologi A-CHPI v1.0 §6 & §8**:
  - Mengganti 17 indikator legacy dengan 18 KPI CBOI kebandarudaraan baku pada 6 domain KPI:
    1. Safety Performance (`sp`): `CBOI-SP-01` s/d `03` (Incident Rate, Near-Miss Reporting, Safety Audit Score).
    2. Service Quality (`sq`): `CBOI-SQ-01` s/d `03` (ASQ Satisfaction, Baggage Handling, Service SLA).
    3. Operational Reliability (`or`): `CBOI-OR-01` s/d `03` (On-Time Performance, Turnaround Time, Equipment Uptime).
    4. Workforce Sustainability (`ws`): `CBOI-WS-01` s/d `03` (Voluntary Turnover, Absenteeism, Training Completion).
    5. Governance & Compliance (`gc`): `CBOI-GC-01` s/d `03` (Audit Finding Closure, Zero Tolerance, Compliance Verification).
    6. Productivity & Efficiency (`pe`): `CBOI-PE-01` s/d `03` (Revenue per FTE, Cost Variance, Automation Velocity).
  - Mengintegrasikan normalisasi tipe spec `'cboi'` (Formula F7 higher is better, F8 lower is better) pada [`IndicatorCatalogService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Scoring/Services/IndicatorCatalogService.php).
- **Transparansi Renormalisasi Dinamis D4 di Antarmuka**:
  - Mengalirkan metadata `is_cboi_renormalized` dan `cboi_notice` dari backend [`ComputeCompositeScoreService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Scoring/Services/ComputeCompositeScoreService.php) dan [`GetBodCockpitDataService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Reporting/Services/GetBodCockpitDataService.php) ke frontend.
  - Memperbarui komponen visual [`AchpiGauge.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Components/Charts/AchpiGauge.vue), [`CompositionBar.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Components/Charts/CompositionBar.vue), [`Overview.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/Overview.vue), [`BodCockpit.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/BodCockpit.vue), dan [`ManagerFormulaCatalogWorkspace.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Components/Manager/ManagerFormulaCatalogWorkspace.vue) untuk menampilkan bobot dinamis (ABMI 52.9%, OHCI 47.1%, CBOI 0% / n/a) dan banner pemberitahuan transparansi: *"CBOI belum ada data — komposit dihitung dari ABMI+OHCI"*.

### G. Metodologi Fix M-06: Modul Fenomena Budaya FEN-01..03, Redaksi PII & Human Review (Milestone 211)
- **Kepatuhan Manual Metodologi A-CHPI v1.0 §9.6 & Aturan R11**:
  - Menghadirkan instrumen kualitatif 3 butir terbuka: `FEN-01` (Positive cultural enabler), `FEN-02` (Cultural barrier/friction), dan `FEN-03` (Forward-looking priority).
  - **Isolasi Mutlak Scoring Engine (Aturan R11)**: Data naratif fenomena tidak pernah memengaruhi skor komposit A-CHPI/ABMI/OHCI/CBOI atau nilai kuantitatif sistem.
- **Skema Database & Domain Services**:
  - Migrasi tabel [`culture_phenomena`](file:///c:/laragon/www/avi-culture-mysql/database/migrations/2026_10_13_161501_response_store_create_culture_phenomena_table.php) di schema `response_store` tanpa relasi foreign key ke tabel identitas.
  - Model [`CulturePhenomenon.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Models/CulturePhenomenon.php), DTOs ([`SaveCulturePhenomenonDTO.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/DTOs/SaveCulturePhenomenonDTO.php), [`ReviewCulturePhenomenonDTO.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/DTOs/ReviewCulturePhenomenonDTO.php)), dan Service ([`SaveCulturePhenomenonService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Services/SaveCulturePhenomenonService.php), [`ReviewCulturePhenomenonService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Services/ReviewCulturePhenomenonService.php), [`GetCulturePhenomenaService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Services/GetCulturePhenomenaService.php)).
  - Controller [`CulturePhenomenaController.php`](file:///c:/laragon/www/avi-culture-mysql/app/Http/Controllers/Survey/CulturePhenomenaController.php) untuk endpoint penyimpanan responden dan konsol moderasi penelaah.
- **Antarmuka Pengguna & Privasi**:
  - Tab navigasi baru "Fenomena Budaya (3 Butir)" di [`EmployeeHeader.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Components/Employee/EmployeeHeader.vue) dan workspace pengisian 3 tab di [`SurveyPhenomenaWorkspace.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Components/Employee/SurveyPhenomenaWorkspace.vue) dengan autosave dan panduan operasional kebandarudaraan.
  - Konsol moderasi admin [`CulturePhenomenaReview.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Admin/CulturePhenomenaReview.vue) untuk persetujuan narasi, penolakan, penyensoran nama individu (`redacted_text`), dan jejak audit.
  - Penegakan ambang batas privasi $k$-anonymity ($n \ge 5$) pada tampilan agregat narasi unit kerja.

---

## 3. Hasil / Output
- **Penyelarasan Metodologi Komprehensif**: Metodologi Fix M-01 s/d M-06 tuntas diimplementasikan sesuai Manual Metodologi A-CHPI v1.0.
- **Isolasi Mutlak Scoring & Skema Basis Data**: Pemisahan tegas antara data evaluasi diagnostik (self-report & fenomena kualitatif) dari skor komposit formal organisasi; schema `response_store` terisolasi tanpa foreign key ke `identity_store`.
- **Katalog & Formula KPI CBOI Terstandarisasi**: 18 KPI CBOI operasional bandara terintegrasi di 6 domain, didukung formula normalisasi F7 & F8 dan transparansi visual renormalisasi D4.
- **Modul Kualitatif Fenomena Budaya Aktif**: Antarmuka pengisian karyawan FEN-01..03, konsol human review redaksi PII admin, dan perlindungan $k$-anonymity ($n \ge 5$) berfungsi penuh.
- **Hasil Pengujian Backend**: Seluruh 364 unit dan feature tests (`php artisan test`) lulus 100% (0 failures, 2775 assertions).
- **Hasil Build Frontend**: Vite production build (`npm run build`) sukses 0 error (durasi build ~3.92s).
- **Dokumentasi Lengkap**: Catatan riwayat Milestone 205–211 terdokumentasi terperinci pada `CHANGELOG.md`.

---

## 4. Kendala / Catatan
- Tidak ada kendala fatal yang menghambat sistem. Penyelarasan metodologi Manual A-CHPI v1.0 membutuhkan ketelitian tinggi dalam memisahkan kalkulasi individual (murni ABMI) dari kalkulasi agregat organisasi (komposit tri-pillar A-CHPI), serta memastikan penegakan aturan privasi K-Anonymity dan isolasi scoring engine pada modul fenomena kualitatif berjalan tanpa celah.

---

## 5. Refleksi / Yang Dipelajari
- Kepatuhan ketat terhadap spesifikasi metodologis resmi (*Scientific & Methodological Rigor*) sangat krusial dalam sistem asesmen budaya korporat.
- Memisahkan domain evaluasi diagnostik (self-report & fenomena kualitatif) dari komposit formal melindungi integritas evaluasi organisasi dari bias subjektif, sementara transparansi renormalisasi di antarmuka membangun kepercayaan pengguna terhadap validitas data.
- Penerapan proses Human Review dan redaksi PII membuktikan bahwa data kualitatif narasi dapat dihimpun secara mendalam tanpa mengorbankan keamanan psikologis dan kerahasiaan identitas responden di lingkungan kerja aviasi.
