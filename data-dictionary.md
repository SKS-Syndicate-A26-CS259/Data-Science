# Data Dictionary (Kamus Data) — Divisi Data Science (A26-CS259 SKS Syndicate)

Dokumen ini merupakan **Kamus Data (*Data Dictionary*)** resmi untuk proyek Capstone **UMKM Kedai Tujuan Semata** yang memuat informasi **Sumber & Metodologi Pembuatan Data**, **Definisi Kolom Dataset Bersih**, **Riwayat Transformasi *Data Wrangling***, serta **Skema Pembagian Data & Keterbatasan Penggunaan Data** bagi tim **Data Science (DS)**, **AI Engineer (AE)**, dan **Full-Stack Developer (FS)**.

## 1. Informasi Sumber Data, Metodologi Pembuatan, & Keterbatasan Data

| Atribut Dokumentasi | Keterangan Detail |
| :--- | :--- |
| **Sumber Data Primer (Dataset 1)** | **Data Asli Katalog Mitra UMKM (`raw_katalog_menu_asli_tujuan_semata.csv`)**: Diperoleh langsung dari papan menu, daftar cup visual, dan konfirmasi wawancara bersama pemilik **UMKM Kedai Tujuan Semata** (Ibu Shinta) atas izin khusus untuk keperluan pengembangan dan demonstrasi Capstone Asah 2026. |
| **Sumber Data Sintetis / Dummy (Dataset 2 & Dataset 3)** | **Data Publik & Data Sintetis Terkontrol (*Controlled Synthetic Dataset*)**: Disusun berdasarkan sampel ulasan publik Google Review Kedai Tujuan Semata yang diperkaya secara terprogram menggunakan skrip **Python (`numpy` & `pandas`)** berbasis aturan bisnis (*Rule-Based Simulation*) karena data riwayat transaksi harian dan laporan keuangan/HPP asli milik UMKM bersifat rahasia (*confidential*). |
| **Cara Pembuatan Data Dummy (*Generation Methodology*)** | 1. **Pemetaan Katalog & Atribut**: Mengacu pada 120 menu aktif Kedai Tujuan Semata (`MN-001` s.d. `MN-123`, di luar 3 menu yang dicoret silang/tidak tersedia sementara) dengan rentang harga Rp4.000–Rp22.000.<br>2. **Simulasi Skenario Operasional**: Membentuk 2.500 log interaksi ulasan dan chat pelanggan (`2026-05-01` s.d. `2026-10-23`) yang mencerminkan kendala nyata kedai (waktu tunggu `4–26 menit`, insiden *double order*, *salah dengar* pencatatan manual, *kelelahan pelayanan*, serta 5 kategori *intent* chat).<br>3. **Injeksi Pengotor Data (*Noise Injection*)**: Menambahkan *missing values* (`NaN`, `N/A`, `-`), baris duplikat (`11 baris` duplikat papan cup pada Dataset 1 dan `81 baris` duplikat log pada Dataset 3), format harga/rating/boolean tidak seragam (`"12 / 16rb"`, `"4 Bintang"`, `"Positif (+)"`), serta *noise* teks (`<br>`, URL, huruf kapital berlebih, dan *slang*) pada data mentah (`raw_*.csv`) untuk pengujian *pipeline Data Wrangling*. |
| **Skema Pembagian Data Model AI (*Train-Test Split* & *Anti-Data Leakage*)** | • **Dataset 1 (`katalog_menu_tujuan_semata.csv`)**: Tidak dibagi (*unsplit* 100% / 120 menu) karena berfungsi sebagai tabel referensi *master* produk untuk katalog web, sistem rekomendasi, dan *Knowledge Base* RAG Chatbot.<br>• **Dataset 3 (`ulasan_dan_chat_tujuan_semata.csv`)**: Dibagi menggunakan *Stratified Split* **80% Data Training (`2.000 baris` — `train_ulasan_dan_chat_ai.csv`)** dan **20% Data Testing (`500 baris` — `test_ulasan_dan_chat_ai.csv`)** dengan `0` irisan `ID_Log` (*zero data leakage*), serta ekstraksi kamus kata/IDF hanya di-*fit* pada *Training Set*. |
| **Keterbatasan Penggunaan Data (*Dataset Limitations*)** | 1. **Keterbatasan Variasi Bahasa Organik**: Data teks ulasan dan chat yang dibangkitkan dari pola skenario terkontrol belum mencakup seluruh variasi salah ketik (*typo*) ekstrem, bahasa daerah, atau sarkasme implisit di dunia nyata.<br>2. **Ketiadaan Data Keuangan Riil**: Tidak memuat data HPP atau pembukuan keuangan internal UMKM sesuai batasan privasi mitra.<br>3. **Kebutuhan *Retraining* Berkala**: Model klasifikasi perlu dilatih ulang (*re-fitting*) secara berkala ketika aplikasi mulai mengumpulkan ulasan organik baru dari form member. |

## 2. Ringkasan File Dataset dalam Repositori

| Kode Dataset | Nama File Mentah (`data/raw/`) | Nama File Bersih (`data/processed/`) | Dimensi Bersih | Penanggung Jawab (PIC DS) |
| :--- | :--- | :--- | :--- | :--- |
| **Dataset 1** | `raw_katalog_menu_asli_tujuan_semata.csv` | `katalog_menu_tujuan_semata.csv` | 120 baris × 14 kolom | Patrick & Ilman |
| **Dataset 2** | `raw_transaksi_dan_member_tujuan_semata.csv` | `transaksi_dan_member_tujuan_semata.csv` | 2.500 baris | Muhammad Ilman Alfarizi (`DS009B6Y1186`) |
| **Dataset 3** | `raw_ulasan_dan_chat_tujuan_semata.csv` | `ulasan_dan_chat_tujuan_semata.csv` | 2.500 baris × 15 kolom | Patrick Jonox Sihombing (`DS009B6Y1422`) |

## 3. Data Dictionary — Dataset 1: Katalog Menu (`katalog_menu_tujuan_semata.csv`)

File `katalog_menu_tujuan_semata.csv` merupakan hasil akhir tahap *Data Wrangling* dari `raw_katalog_menu_asli_tujuan_semata.csv` (134 baris mentah → dikurangi 11 baris duplikat papan cup visual → dikurangi 3 menu `DICORET SILANG (X) / Tidak Tersedia Sementara` yaitu `MN-075`, `MN-076`, dan `MN-083` → menghasilkan tepat **120 menu aktif × 14 kolom**, 0% *missing values*).

### A. Definisi Kolom Dataset 1 Bersih (14 Kolom)

| No | Nama Kolom | Tipe Data | Contoh Nilai / Rentang Nilai | Deskripsi & Aturan Nilai | Pengguna (Target Divisi) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `Kode_Menu` | `string` | `MN-001` s.d. `MN-123` *(120 kode aktif)* | *Primary Key* kode unik produk pada katalog Kedai Tujuan Semata (penomoran asli dipertahankan setelah 3 menu nonaktif `MN-075`, `MN-076`, `MN-083` disaring). | DS, FS, AE |
| 2 | `Nama_Menu` | `string` | `Coffee Latte`, `Tujuan Signature`, `Coklat Kuwentel` | Nama produk makanan/minuman yang telah dinormalisasi (*Title Case*) dan bebas dari salah ketik (*typo*). | DS, FS (Katalog Web), AE (RAG Chatbot) |
| 3 | `Kategori_Menu` | `string` (Kategorikal) | • `Snack & Eatery` (18)<br>• `Coffee` (13)<br>• `Soda & Refresher` (12)<br>• `Creamy & Non-Coffee` (11)<br>• `Dessert` (11)<br>• `Main Course` (11)<br>• `Black Coffee` (9)<br>• `Tea` (9)<br>• `Matcha` (8)<br>• `Coklat & 2Tone` (8)<br>• `Topping & Add-on` (4)<br>• `Yakult Series` (3)<br>• `Creamy Jumbo` (3) | Kategori menu utama hasil standarisasi dari 47 variasi penulisan pada data mentah menjadi 13 kategori resmi. | DS, FS (Filter Katalog), AE |
| 4 | `Sub_Kategori` | `string` (Kategorikal) | `Kopi Susu`, `Kopi Hitam / Espresso`, `Minuman Teh`, `Minuman Matcha`, `Minuman Creamy / Non-Kopi`, `Minuman Segar / Dingin`, `Topping & Add-on`, `Cemilan / Snack`, `Makanan Berat`, `Dessert & Kue` | Pengelompokan sub-jenis produk (10 sub-kategori standar, 0 nilai kosong). | DS, FS, AE |
| 5 | `Harga_Jual` | `integer` | `2000` s.d. `22000` | Harga jual dasar/reguler per porsi dalam satuan Rupiah bulat (`int64`) untuk perhitungan statistik dan filter rentang harga (*budget*). | DS, FS, AE |
| 6 | `Varian_Harga_Teks` | `string` | `"Rp12.000 / Rp16.000"`, `"Rp5.000 / Rp10.000 / Rp15.000"`, `"Rp18.000"` | Teks informasi harga lengkap untuk menu yang memiliki beberapa ukuran cup (*Small/Regular/Large*) agar siap ditampilkan pada kartu produk UI Web. | FS (Tampilan Kartu Menu), AE |
| 7 | `Komposisi_Bahan` | `string` | `"Espresso, susu segar, gula cair"` | Daftar komposisi bahan baku utama (*ingredients*) untuk pencarian berbasis bahan dan pengecekan alergen pada Chatbot. | DS, AE, FS |
| 8 | `Karakter_Rasa` | `string` | `"Kopi, Creamy, Manis Seimbang"`, `"Pedas, Gurih, Berbumbu"` | Profil rasa dan tekstur menu yang telah distandarisasi sebagai fitur utama model rekomendasi menu. | DS, AE, FS |
| 9 | `Badge_Menu_Baru` | `boolean` | `True` (12 menu) / `False` (108 menu) | Penanda apakah menu termasuk produk baru (*New Arrival*) untuk diberi *badge* khusus di halaman Katalog Web. | DS, FS, AE |
| 10 | `Indikasi_Terlaris` | `boolean` | `True` (13 menu) / `False` (107 menu) | Penanda apakah menu merupakan produk unggulan terlaris (*Best Seller*) kedai. | DS, FS, AE |
| 11 | `Mengandung_Esens_Rum` | `boolean` | `True` (2 menu: `MN-112`, `MN-113`) / `False` (118 menu) | Fitur hasil *feature engineering* penanda menu yang menggunakan perisa *esens rum* (non-alkohol/halal) yang wajib diinformasikan oleh Chatbot ke pelanggan. | DS, AE (Guardrail Info Menu), FS |
| 12 | `Ramah_Anak` | `boolean` | `True` (40 menu) / `False` (80 menu) | Fitur hasil *feature engineering* penanda menu yang aman untuk anak-anak (bebas kafein kopi, bebas esens rum, dan tidak pedas). | DS, AE, FS |
| 13 | `Segmen_Pelanggan` | `string` (Kategorikal) | • `Mahasiswa` (60 menu)<br>• `Anak SD/SMP` (26 menu)<br>• `Pekerja` (18 menu)<br>• `Ibu-Ibu` (16 menu) | Hasil pemetaan segmen pelanggan utama berdasarkan karakteristik rasa, harga, kategori, dan catatan pemilik. | DS, AE, FS |
| 14 | `Catatan_Khusus_Owner` | `string` | `"Mengandung Kafein"`, `"Ramah Anak (Tanpa Kafein & Tidak Pedas)"`, `"Promo All Variant Coklat Free Ice Tea (Size Large)"` | Keterangan operasional penting dari pemilik kedai terkait kandungan kafein, esens rum, promo, atau ketersediaan *dine-in/takeaway*. | DS, FS, AE |

### B. Riwayat Transformasi *Data Wrangling* Dataset 1

| Nama Kolom Mentah (`raw_katalog_menu_asli_tujuan_semata.csv`) | Masalah Kualitas Data Mentah (*Assessing*) | Hasil Transformasi pada Data Bersih (`katalog_menu_tujuan_semata.csv`) |
| :--- | :--- | :--- |
| `Kode_Menu` | Format tidak seragam (`mn-001`, `MN011`, `MN-041`), 11 baris duplikat papan cup visual, & 3 menu dicoret silang (`MN-075`, `MN-076`, `MN-083`) | `Kode_Menu` (120 baris menu aktif unik dalam format `MN-XXX`) |
| `Nama_Menu` | Mengandung spasi berlebih, huruf kecil semua, & salah ketik (`Butterscoth Latte`, `Nutela Matcha Latee`, `Martabk Tahu`, `Spagheti`) | `Nama_Menu` (Bersih, *Title Case*, & ejaan terstandarisasi) |
| `Kategori_Menu` | Terpecah menjadi 47 variasi penulisan akibat spasi dan salah ketik (`COFEE`, `DESERT`, `addt`) | `Kategori_Menu` (13 kategori standar) |
| `Sub_Kategori` | 24 baris `NaN` dan perbedaan huruf besar/kecil | `Sub_Kategori` (10 sub-kategori standar, 0 `NaN`) |
| `Harga_Jual` | Teks multi-ukuran dan simbol campuran (`"12/16"`, `"12 / 16rb"`, `"Rp 5/10/15k"`, `"only 18.000"`, `"+2rb"`) | Dipecah menjadi `Harga_Jual` (`int64` harga dasar Rupiah) dan `Varian_Harga_Teks` (`string` daftar harga lengkap) |
| `Komposisi_Bahan` | 16 baris `NaN` dan nilai `"N/A"` | `Komposisi_Bahan` (100% terisi sesuai profil menu) |
| `Karakter_Rasa` | 8 baris `NaN` dan 7 baris `"-"` | `Karakter_Rasa` (100% terisi & *Title Case*) |
| `Badge_Menu_Baru` | 11 variasi nilai campuran (`false`, `true`, `0`, `1`, `No`, `Tidak`, `Ya`, `YES`, `Baru!`, `-`, `NaN`) | `Badge_Menu_Baru` (`bool`: `True` / `False`) |
| `Indikasi_Terlaris` | 10 variasi nilai campuran (`0`, `Tidak`, `No`, `-`, `TRUE`, `Best Seller`, `best seller`, `1`, `Ya`, `NaN`) | `Indikasi_Terlaris` (`bool`: `True` / `False`) |
| `Catatan_Khusus_Owner` | 7 baris `NaN` dan teks `"Duplikat dari Papan Cup Visual"` | `Catatan_Khusus_Owner` + diekstrak menjadi fitur `Mengandung_Esens_Rum`, `Ramah_Anak`, & `Segmen_Pelanggan` |

## 4. Data Dictionary — Dataset 3: Ulasan & Chat (`ulasan_dan_chat_tujuan_semata.csv`)

File `ulasan_dan_chat_tujuan_semata.csv` merupakan hasil akhir tahap *Data Wrangling* dari `raw_ulasan_dan_chat_tujuan_semata.csv` (2.581 baris mentah × 10 kolom → dikurangi 81 baris duplikat `ID_Log` → menghasilkan **2.500 baris interaksi unik × 15 kolom**, 0% *missing values*).

### A. Definisi Kolom Dataset 3 Bersih (15 Kolom)

| No | Nama Kolom | Tipe Data | Contoh Nilai / Rentang Nilai | Deskripsi & Aturan Nilai | Pengguna (Target Divisi) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `ID_Log` | `string` | `RAW-LOG-0001` s.d. `RAW-LOG-2500` | *Primary Key* unik untuk setiap baris log ulasan dan interaksi chat pelanggan (bebas duplikat). | DS, FS, AE |
| 2 | `Ref_Nota_Transaksi` | `string` | `TRX-TS-2026-0001` | *Foreign Key* nomor referensi nota pembelian untuk proses *merge* dengan **Dataset 2 (Transaksi & Member)**. | DS, FS |
| 3 | `Tanggal_Masuk` | `date` (`YYYY-MM-DD`) | `2026-05-01` s.d. `2026-10-23` | Tanggal masuknya ulasan atau pesan chat pelanggan dalam format standar ISO-8601. | DS, FS |
| 4 | `Sumber_Ulasan` | `string` (Kategorikal) | • `Chat WhatsApp Kedai` (631)<br>• `DM Instagram @tujuansemata` (627)<br>• `Form Website Member` (624)<br>• `Google Review Maps` (618) | Kanal komunikasi asal tempat pelanggan mengirimkan ulasan atau pesan chat. | DS, FS |
| 5 | `Skor_Rating` | `integer` | `1`, `2`, `3`, `4`, `5` | Skor penilaian bintang pelanggan dalam skala bulat 1 sampai 5 (`int64`). | DS, FS (Dasbor Admin) |
| 6 | `Label_Sentimen` | `string` (Kategorikal) | • `positif` (1.925 baris)<br>• `netral` (384 baris)<br>• `negatif` (191 baris) | Target klasifikasi sentimen ulasan pelanggan dalam huruf kecil terstandarisasi. | DS, AE, FS |
| 7 | `Catatan_Komplain` | `string` (Kategorikal) | • `Tidak Ada Keluhan` (1.925)<br>• `Kapasitas Tempat / Variasi Menu` (363)<br>• `Double Order / Pencatatan Manual` (79)<br>• `Salah Dengar / Salah Order Manual` (73)<br>• `Waktu Tunggu Saat Ramai` (40)<br>• `Sikap Pelayanan Saat Kelelahan` (20) | Klasifikasi akar masalah operasional pada ulasan pelanggan untuk ringkasan evaluasi *closing* pemilik. | DS, FS (Dasbor Evaluasi) |
| 8 | `Menu_Disebut_Ulasan` | `string` | Nama menu spesifik, `Tidak Disebutkan (Umum/Tempat)`, atau `Tidak Disebutkan` | Hasil ekstraksi nama produk menu yang disebutkan secara eksplisit di dalam teks ulasan pelanggan. | DS, AE, FS |
| 9 | `Estimasi_Waktu_Tunggu_Menit` | `integer` | `0` (tidak menyebut durasi), atau `4` s.d. `26` | Hasil ekstraksi durasi waktu tunggu pesanan (dalam menit) yang disebutkan pelanggan di dalam teks ulasan. | DS, FS |
| 10 | `Flag_Imputasi_Ulasan` | `integer` | `0` (Teks asli: 2.413) / `1` (Imputasi: 87) | Penanda transparansi apakah `Teks_Ulasan` berasal dari imputasi berbasis konteks (`Skor_Rating` + `Catatan_Komplain`). | DS |
| 11 | `Teks_Ulasan` | `string` | `"Rasa croffle choco almond (isi 2) juara banget..."` | Teks ulasan pelanggan yang sudah bersih dari tag HTML (`<br>`), tautan URL, dan tanda baca berlebih untuk tampilan UI Web. | FS (UI Web & Admin) |
| 12 | `Teks_Ulasan_NLP` | `string` | `"rasa croffle choco almond isi 2 juara banget..."` | Teks ulasan hasil *preprocessing* NLP (*lowercase*, normalisasi kata *slang*, tanpa tanda baca) sebagai fitur input model sentimen. | DS, AE |
| 13 | `Pesan_Chat` | `string` | `"Halo kak, Saya punya budget di bawah Rp25.000..."` | Kalimat pesan chat pelanggan yang telah dibersihkan dari karakter pengotor. | FS, AE |
| 14 | `Pesan_Chat_NLP` | `string` | `"halo kak saya punya budget di bawah rp25 000..."` | Teks pesan chat pelanggan hasil normalisasi NLP untuk pelatihan model klasifikasi *intent* & *guardrail* Chatbot. | DS, AE |
| 15 | `Label_Intent` | `string` (Kategorikal) | `rekomendasi_dan_membership`, `tanya_produk_dan_menu_baru`, `pemesanan_menu`, `keluhan_dan_ulasan`, `out_of_context` (@500 baris) | Target klasifikasi intensi (*intent*) pesan chat pelanggan (termasuk deteksi pesan *spam* `out_of_context`). | DS, AE |

### B. Riwayat Transformasi *Data Wrangling* Dataset 3

| Nama Kolom Mentah (`raw_ulasan_dan_chat_tujuan_semata.csv`) | Masalah Kualitas Data Mentah (*Assessing*) | Hasil Transformasi pada Data Bersih (`ulasan_dan_chat_tujuan_semata.csv`) |
| :--- | :--- | :--- |
| `ID_Log` | 81 baris duplikat identik (total awal 2.581 baris) | `ID_Log` (2.500 baris unik) |
| `Ref_Nota_Transaksi` | Spasi berlebih di awal/akhir teks pada sebagian baris | `Ref_Nota_Transaksi` |
| `Tanggal_Masuk` | Format tanggal perlu distandarisasi & diurutkan | `Tanggal_Masuk` (`YYYY-MM-DD`) |
| `Sumber_Ulasan` | Konsisten (4 kanal komunikasi) | `Sumber_Ulasan` |
| `Skor_Rating_Mentah` | Campuran string `"4 Bintang"` (539 baris) dan 85 baris `NaN` | `Skor_Rating` (`int64`, rentang `1–5`) |
| `Teks_Ulasan_Mentah` | 87 baris `NaN`, tag `<br>`, URL, *hashtag*, huruf kapital penuh, & tanda seru berlebih | `Teks_Ulasan`, `Teks_Ulasan_NLP`, `Flag_Imputasi_Ulasan`, `Menu_Disebut_Ulasan`, `Estimasi_Waktu_Tunggu_Menit` |
| `Label_Sentimen_Awal` | 206 baris `NaN` dan format label tidak seragam (`"Positif (+)"`, `"Netral (0)"`, `"Negatif (-)"`) | `Label_Sentimen` (`positif`, `netral`, `negatif`) |
| `Catatan_Komplain_Mentah` | 234 baris bernilai `"Belum Dicek"` | `Catatan_Komplain` (6 kategori tervalidasi) |
| `Pesan_Chat_Pelanggan_Mentah` | Simbol tanda baca, penulisan jam `'15_30'`, dan kata *slang* | `Pesan_Chat` & `Pesan_Chat_NLP` |
| `Label_Intent_Mentah` | 116 baris `NaN` (kosong) | `Label_Intent` (5 kelas *intent* seimbang @500 baris) |

## 5. Panduan Integrasi Antar-Dataset (*Join Keys*)

* **Integrasi Dataset 2 (Transaksi & Member) dengan Dataset 3 (Ulasan & Chat)**: Gunakan kunci penghubung **`Ref_Nota_Transaksi`** (`pd.merge(df_transaksi, df_ulasan, on='Ref_Nota_Transaksi', how='inner')`).
* **Integrasi Dataset 1 (Katalog Menu) dengan Dataset 2 (Transaksi)**: Gunakan kunci **`Kode_Menu`** (`MN-001` s.d. `MN-123`) atau **`Nama_Menu`**.
