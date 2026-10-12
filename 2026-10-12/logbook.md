# Laporan Logbook Harian Magang — 2026-10-12

- **Tanggal Pelaporan**: Senin, 12 Oktober 2026
- **Jam Pelaporan**: 16:30 WIB
- **Proyek Acuan**: AVI Culture (`avi-culture-mysql`)
- **Web Live**: [https://culture.yudhapratama.my.id/](https://culture.yudhapratama.my.id/)
- **Cabang Git (Branch)**: `redesign/ui-design-system` / `refactor/achpi`
- **Bukti Dukung (Screenshot)**: [screenshot.png](screenshot.png)

---

## 1. Kegiatan yang Dilakukan (Deskripsi Pekerjaan)
Menyelesaikan Milestone 194 hingga Milestone 204 pada platform pengukuran budaya korporat AVI Culture (A-CHPI), yang mencakup:
1. **Fase 1 (Keamanan, Privasi & K-Anonymity Guard)**: Memproteksi rute registri karyawan (`/employees`, `/registry`) dengan middleware otorisasi, mengeliminasi salt statis `'PEER-SALT'` menjadi token kriptografis dinamis via `GenerateBlindTokenService`, penegakan ambang batas privasi $k$-anonymity ($n \ge 5$) secara menyeluruh di backend dan ekspor CSV, serta server-side masking NIK rekan kerja.
2. **Fase 2 (Higienitas Arsitektur MVC & Eliminasi N+1 Query)**: Mengekstraksi logika bisnis dari model Eloquent (`EmployeeCultureProfile`) dan controller ke Domain Services mandiri, mengimplementasikan 6 FormRequest terdedikasi, mengamankan mutasi multi-tabel dalam database transaction, serta mengeliminasi N+1 query loop pada komputasi skor dan profil registry.
3. **Fase 3 (Integritas Scoring Engine & Metodologi Kuesioner)**: Menghapus label "Butir Terbalik" dari UI pertanyaan kuesioner untuk mencegah bias respons psikometri (*straight-lining*), mengimplementasikan renormalisasi dinamis bobot komposit A-CHPI ($W_{ABMI}' = 0.45/0.85$, $W_{OHCI}' = 0.40/0.85$) saat data observasi CBOI kosong/null, serta mendinamisasikan cakupan unit kerja BOD Cockpit berbasis hierarki direktorat pengguna.
4. **Fase 4 (Dekomposisi Komponen Raksasa Frontend & Lokalisasi Modular)**: Memecah komponen Vue 3 monolitik raksasa (>1.000 baris) pada `BodCockpit.vue` (ekstraksi `BodUnitMasterTable.vue`) dan `ActionPlan/Index.vue` (ekstraksi `YearlyAgendaTab.vue` dan `KanbanBoardTab.vue`), serta memisahkan 456 frasa kata kamus dwibahasa `useLocale.js` ke file JSON statis terpisah (`Locales/id.json` dan `en.json`).
5. **Penyederhanaan Tampilan Dashboard**: Mengeliminasi banner non-esensial (`AttentionSummaryCard` dan `CulturePulseCard`) pada dashboard utama untuk mereduksi *card fatigue* dan mengembalikan fokus pengguna langsung ke kanvas metrik inti.
6. **Integrasi Skill Conditional UI Auditor**: Menambahkan skill operasional `.agents/skills/conditional-ui-auditor/SKILL.md` serta mengeksekusi audit logika render kondisional, pencegahan status invalid, dan integritas antarmuka.
7. **Resolusi QC Batch Fix Q-01 s/d Q-05**: Menuntaskan harmonisasi batas band kematangan A-CHPI (Q-01), validasi prasyarat modal pengesahan inisiatif (Q-02), penyelarasan nilai tunggal headline 69.6 & defisit -10.4 poin (Q-03), eliminasi kontradiksi banner aman vs unit watchlist CFRI > 14% (Q-04), dan sinkronisasi 100% data responden sensus unit CSL $n=7$ (Q-05).

---

## 2. Rincian Capaian & Modul yang Dikerjakan

### A. Fase 1: Keamanan, Privasi & K-Anonymity Guard (Milestone 194)
- **Proteksi Rute Registri & Penegakan Otorisasi**:
  - Memasang middleware `EnsureCanViewScores` pada grup rute `employees.*` dan `registry.*` di [`routes/web.php`](file:///c:/laragon/www/avi-culture-mysql/routes/web.php), menolak akses staf non-manajerial dan mengarahkan otomatis ke portal sensus.
  - Menulis test suite regresi keamanan otomatis [`RegistryRouteProtectionTest.php`](file:///c:/laragon/www/avi-culture-mysql/tests/Feature/Security/RegistryRouteProtectionTest.php) untuk memverifikasi penolakan akses dan izin role manajerial/admin.
- **Kriptografi Blind Token & Masking Server-Side**:
  - Mengeliminasi total salt statis `'PEER-SALT'` pada pembuatan blind token di seluruh alur survei dan seeder; beralih ke token kriptografis dinamis menggunakan [`GenerateBlindTokenService`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Identity/Services/GenerateBlindTokenService.php).
  - Melakukan masking NIK rekan sejawat (`substr($nik, 0, 4) . '****'`) langsung di layer backend sebelum diteruskan ke props Inertia/Vue.
- **Penegakan Ambang K-Anonymity ($n \ge 5$)**:
  - Menyensor identitas, skor dimensi, dan rapor individual pada [`GetEmployeeCultureRegistryService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Identity/Services/GetEmployeeCultureRegistryService.php), ekspor CSV [`EmployeeCultureRegistryController.php`](file:///c:/laragon/www/avi-culture-mysql/app/Http/Controllers/Employee/EmployeeCultureRegistryController.php), dan leaderboard SK-115 [`GenerateSk115ReportService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Reporting/Services/GenerateSk115ReportService.php) jika responden $< 5$.

### B. Fase 2: Higienitas Arsitektur MVC & Eliminasi N+1 Query (Milestone 195)
- **Ekstraksi Domain Services & Single Responsibility**:
  - Membangun [`ComputeSelfOtherGapService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Identity/Services/ComputeSelfOtherGapService.php) untuk kalkulasi kesenjangan persepsi diri dan rekan, kuadran blind-spot, serta klasifikasi *Under/Over-Estimator*.
  - Membangun [`ClassifyEmployeeBehaviorService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Identity/Services/ClassifyEmployeeBehaviorService.php) untuk kuadran kinerja perilaku (*Culture Champion*, *Solid Performer*, dll.).
  - Membangun [`GetSurveyLandingDataService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Survey/Services/GetSurveyLandingDataService.php) dan [`GetActionPlanWorkspaceService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/ActionPlan/Services/GetActionPlanWorkspaceService.php) untuk mereduksi beban query dari controller.
- **Form Requests & Transaksi Basis Data**:
  - Mengganti validasi inline di controller dengan 6 FormRequest terdedikasi: `SimulateRolloverRequest`, `SignApprovalRequest`, `RejectApprovalRequest`, `SaveSurveyProgressRequest`, `SubmitSurveyRequest`, dan `SubmitPeerEvaluationRequest`.
  - Mengamankan mutasi approval pada [`DigitalSignOffService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Approval/Services/DigitalSignOffService.php) dalam database transaction atomik.
- **Optimasi N+1 Query**:
  - Mengeliminasi query loop pada [`ComputeCompositeScoreService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Scoring/Services/ComputeCompositeScoreService.php) dengan pre-fetching agregat indikator aktif.
  - Mengoptimalkan pembacaan profil karyawan pada [`GetEmployeeCultureRegistryService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Identity/Services/GetEmployeeCultureRegistryService.php) menggunakan eager filtering kolektif `whereIn`.

### C. Fase 3: Integritas Scoring Engine & Metodologi Kuesioner (Milestone 196)
- **Pencegahan Bias Respons Kuesioner**:
  - Menghapus badge "Reverse Item / Butir Terbalik" pada kartu pertanyaan kuesioner ([`SurveyQuestionCard.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Components/Employee/SurveyQuestionCard.vue)) guna menjaga objektivitas responden dan standar psikometri ilmiah.
- **Renormalisasi Matematis Dinamis Bobot A-CHPI**:
  - Mengimplementasikan formula redistribusi bobot proporsional pada [`ComputeCompositeScoreService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Scoring/Services/ComputeCompositeScoreService.php) ketika data indikator CBOI belum tersedia (`null`), sehingga tidak menimbulkan penalti skor palsu 15%.
  - Menambahkan unit test verifikasi renormalisasi pada [`ScoringEngineTest.php`](file:///c:/laragon/www/avi-culture-mysql/tests/Unit/Scoring/ScoringEngineTest.php).
- **Dinamisasi Scoping BOD Cockpit**:
  - Mengganti konstanta unit CEO statis dengan scoping dinamis berbasis hierarki direktorat pengguna ([`GetBodCockpitDataService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Reporting/Services/GetBodCockpitDataService.php)).

### D. Fase 4: Dekomposisi Komponen Raksasa & Lokalisasi Modular (Milestone 197)
- **Modularisasi Lokalisasi Multi-Bahasa**:
  - Mengekstrak 456 frasa kata kamus bahasa dari monolit `useLocale.js` (1.055 baris) ke file JSON statis [`Locales/id.json`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Locales/id.json) dan [`Locales/en.json`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Locales/en.json), merampingkan composable reaktif menjadi 65 baris murni (reduksi >93%).
- **Dekomposisi Komponen Halaman Besar**:
  - Memecah [`BodCockpit.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/BodCockpit.vue) dengan mengekstrak [`BodUnitMasterTable.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/Dashboard/Partials/BodUnitMasterTable.vue) (merampingkan dari 1.214 baris menjadi 970 baris).
  - Memecah [`ActionPlan/Index.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/ActionPlan/Index.vue) dengan mengekstrak [`YearlyAgendaTab.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/ActionPlan/Partials/YearlyAgendaTab.vue) dan [`KanbanBoardTab.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/ActionPlan/Partials/KanbanBoardTab.vue) (mereduksi dari 1.902 baris menjadi 930 baris, reduksi ~51%).
- **Sentralisasi Composable Budaya**:
  - Mengonsolidasikan logika warna semantik AKHLAK & status kanban ke [`useCultureColor.js`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Composables/useCultureColor.js) serta pemformatan NIK/skor ke [`useCultureFormat.js`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Composables/useCultureFormat.js).

### E. Penyederhanaan Dashboard & Audit Kondisional (Milestone 198 & 199)
- Mengeliminasi banner dekoratif non-esensial (`AttentionSummaryCard` dan `CulturePulseCard`) pada BOD Cockpit, Unit Manager, dan Overview untuk menghemat ruang vertikal dan mencegah *card fatigue*.
- Mengintegrasikan skill operasional [`.agents/skills/conditional-ui-auditor/SKILL.md`](file:///c:/laragon/www/avi-culture-mysql/.agents/skills/conditional-ui-auditor/SKILL.md) dan mengaudit alur logika kondisional komponen form dan aksi antarmuka.

### F. QC Batch Fix Q-01 s/d Q-05 (Milestone 200 s/d 204)
- **Q-01 (Harmonisasi Batas Band Kematangan A-CHPI)**: Mengonsolidasikan penentuan band ke fungsi tunggal `determineAchpiBand()` di [`utils.js`](file:///c:/laragon/www/avi-culture-mysql/resources/js/lib/utils.js) dengan batas baku 4-tier (Guardian > 90.0, Role Model > 80.0, Good $\ge 75.0$, Need Improvement $< 75.0$), mengeliminasi kontradiksi label badge header vs tachometer gauge.
- **Q-02 (Validasi Prasyarat Modal Rencana Aksi)**: Memperbarui [`ValidateItemModal.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Pages/ActionPlan/Partials/ValidateItemModal.vue) dengan computed `isReadyForValidation` yang mewajibkan status inisiatif berada pada `submitted_for_review` ("Verifikasi Eviden"), mengunci tombol simpan jika belum memenuhi syarat, serta menampilkan pesan error responsif.
- **Q-03 (Harmonisasi Nilai Tunggal Headline & Akumulasi Pilar)**: Menyelaraskan kalkulasi komposit korporat di backend [`GetBodCockpitDataService.php`](file:///c:/laragon/www/avi-culture-mysql/app/Domain/Reporting/Services/GetBodCockpitDataService.php) bersumber dari Formula Baku F1 ($0.45 \text{ ABMI} + 0.40 \text{ OHCI} + 0.15 \text{ CBOI} = 69.6$), meneruskan prop `:composite` ke [`CompositionBar.vue`](file:///c:/laragon/www/avi-culture-mysql/resources/js/Components/Charts/CompositionBar.vue), dan menyamakan angka defisit target menjadi tepat **-10.4 Poin**.
- **Q-04 (Sinkronisasi Ambang Pengaman & Eliminasi Kontradiksi Banner)**: Menegakkan definisi aman SK-115 (Safety Gate $\ge 60$ DAN $\text{CFRI} \le 14\%$). Banner "100% AMAN" disembunyikan jika terdapat unit Watchlist (CSD CFRI 16.4%), digantikan panel pengawasan unit berisiko, serta menyelaraskan badge tabel portofolio menjadi Watchlist.
- **Q-05 (Sinkronisasi Responden Sensus Unit CSL $n=7$)**: Menyelaraskan seeder pilot [`CEOOfficePilotSeeder.php`](file:///c:/laragon/www/avi-culture-mysql/database/seeders/CEOOfficePilotSeeder.php) dengan mengikutsertakan akun manajer unit ke daftar responden sensus valid dan selesai ($n=7$), menyelaraskan 100% data kartu BOD, tabel portofolio, dan registri unit kerja CSL (7 Personel).

---

## 3. Hasil / Output
- **Domain Services & Requests**: 4 Domain Services baru terintegrasi, 6 Form Requests aktif, dan mutasi multi-tabel terbungkus database transactions.
- **UI Modular & Terdekomposisi**: File raksasa `BodCockpit.vue` dan `ActionPlan/Index.vue` terpecah bersih menjadi subkomponen terfokus; kamus lokalisasi `id.json` & `en.json` terpisah modular dari kode reaktif runtime.
- **Scoring Engine Kokoh**: Renormalisasi matematis CBOI null aktif dan teruji; bias butir terbalik pada kartu kuesioner tereliminasi; scoping BOD cockpit dinamis berbasis hierarki direktorat.
- **QC Batch Fix Q-01 s/d Q-05 Tuntas**: Seluruh anomali disparitas angka, kontradiksi status pengaman, validasi modal inisiatif, dan disparitas hitungan responden terselesaikan tanpa celah.
- **Hasil Pengujian Backend**: Seluruh 348 unit dan feature tests (`php artisan test`) lulus 100% (0 failures, 2435 assertions).
- **Hasil Build Frontend**: Vite production build (`npm run build`) sukses 0 error (durasi build ~3.74s - 4.57s).
- **Dokumentasi Lengkap**: Catatan riwayat Milestone 194–204 tertera rinci pada `CHANGELOG.md`.

---

## 4. Kendala / Catatan
- Tidak ada kendala fatal yang menghambat sistem. Penyelarasan ambang pengaman dan konsistensi perhitungan komposit lintas komponen memerlukan penelusuran mendalam pada alur data backend DTO ke props Inertia Vue guna memastikan prinsip Single Source of Truth terpenuhi tanpa selisih pembulatan desimal.

---

## 5. Refleksi / Yang Dipelajari
- Memelihara pemisahan tanggung jawab (*Separation of Concerns*) melalui Domain Services terdedikasi sangat esensial untuk menjaga keberlanjutan kode dan kemudahan pengujian unit independen.
- Dalam sistem pengukuran performa dan budaya korporat, konsistensi representasi angka (Single Source of Truth) antara kartu ringkasan, grafik akumulasi, dan tabel detail adalah fondasi kepercayaan pengguna eksekutif terhadap data yang disajikan.
- Dekomposisi komponen antarmuka yang besar ke subkomponen modular dan pemisahan file lokalisasi ke format JSON statis mempercepat proses *development*, mengurangi beban kompilasi Vite, dan mempermudah pelacakan bug status tampilan.
