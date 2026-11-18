# Data Dictionary — `transaksi_member_clean.csv`

> ⚠️ **DATA DUMMY.** Dataset dibuat oleh tim Capstone A26-CS259 untuk mensimulasikan transaksi UMKM Tujuan Semata (1 Mei – 23 Okt 2026). Ini **bukan** data operasional nyata dan tidak memuat data pribadi asli.

- **Sumber:** `raw_transaksi_dan_member_tujuan_semata.csv`, dibersihkan di `ds/transaksi_dan_member.ipynb`
- **Ukuran:** 2.500 baris × 26 kolom (1 baris = 1 nota = 1 jenis menu)
- **Format:** CSV, pemisah koma, UTF-8; tanggal `YYYY-MM-DD`, waktu `YYYY-MM-DD HH:MM:SS`; nilai uang dalam Rupiah (integer)

| Kolom | Tipe | Keterangan | Contoh |
|---|---|---|---|
| `No_Nota_Transaksi` | string | ID nota (unik) | `TS-2026-0001` |
| `Tgl_Order` | date | Tanggal order (diseragamkan dari 3 format) | `2026-07-26` |
| `Waktu_Order` | string | Jam order `HH:MM` | `13:21` |
| `Situasi_Kedai` | kategori | Kondisi kedai; nilai kosong diimputasi dari jam (12–13 & 18–20 = ramai) | `Normal`, `Ramai (Peak Hour)` |
| `ID_Member_Pelanggan` | string | ID member; non-member = `NON-MBR` | `MBR-1350` |
| `Level_Member` | kategori | Level saat transaksi (distandardisasi dari 18 varian label) | `Non-Member`, `Bronze`, `Silver`, `Gold` |
| `Kategori_Pelanggan` | kategori | Segmen pelanggan (distandardisasi dari 21 varian label) | `Mahasiswa`, `Pekerja`, `Ibu Rumah Tangga`, `Anak/Pelajar` |
| `Kunjungan_Bulan_Ini` | int (nullable) | Jumlah kunjungan bulan tersebut (snapshot); 165 kosong | `1`–`20` |
| `Saldo_Poin` | int (nullable) | Saldo poin saat transaksi; non-member = 0; 73 kosong (member) | `34` |
| `Sumber_Order` | kategori | Kanal order | `Website Membership & Order`, `Chatbot WhatsApp/Web`, `Datang Langsung (Walk-in)`, `Link Bio Instagram/TikTok` |
| `Cara_Catat_Kasir` | kategori | Metode pencatatan asli | `Otomatis Digital (Web/Chatbot)`, `Manual Kertas & Kasir Pintar`, `Catat Kertas Manual` |
| `Kode_Menu_Pesanan` | string | Kode menu (uppercase) | `MN057` |
| `Nama_Menu_Terjual` | string | Nama menu | `Strawberry Milkshake Ceria` |
| `Qty_Cup_Porsi` | int | Jumlah porsi/cup (> 0; 3 nilai `-1` dipulihkan dari total) | `1`–`5` |
| `Harga_Satuan` | int | Harga per porsi (Rp) | `16000` |
| `Potongan_Diskon_Member` | int | Diskon (Rp); diskon tak tercatat dihitung ulang dari selisih total | `1600` |
| `Total_Bayar_Kasir` | int | Uang yang dibayar (Rp) = `Subtotal_Kotor − Potongan_Diskon_Member` | `16000` |
| `Catatan_Kesesuaian_Order` | kategori | Status kesesuaian pesanan | `Sesuai Pesanan`, `Salah Dengar Menu (Dibuat Ulang)`, `Double Order (Pesanan Ganda)` |
| `Durasi_Penyajian_Menit` | int (nullable) | Lama penyajian (menit); outlier > 60 dijadikan kosong; 126 kosong | `4`–`26` |
| `Waktu_Transaksi` | datetime | Gabungan tanggal + jam | `2026-07-26 13:21:00` |
| `Is_Member` | bool | `True` jika ID diawali `MBR` | |
| `Jam` | int | Jam transaksi | `10`–`22` |
| `Bulan` | string | Periode bulan `YYYY-MM` | `2026-07` |
| `Subtotal_Kotor` | int | `Qty_Cup_Porsi × Harga_Satuan` (sebelum diskon) | `32000` |
| `Jenis_Pencatatan` | kategori | Ringkasan `Cara_Catat_Kasir` | `Digital`, `Manual` |
| `Is_Pesanan_Bermasalah` | bool | `True` jika salah dengar / double order | |

## Cara membaca
```python
import pandas as pd
df = pd.read_csv("ds/data/transaksi_member_clean.csv", parse_dates=["Tgl_Order", "Waktu_Transaksi"])
```
