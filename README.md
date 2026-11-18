# Data-Science
Data Science UMKM Tujuan Semata — Capstone Project Asah 2026 (A26-CS259 SKS Syndicate)

Repository ini berisi pengolahan data, analisis eksploratif, dan penyediaan dataset bersih untuk platform web *membership*, pemesanan, dan rekomendasi menu UMKM **Tujuan Semata**.

## Struktur Folder
```text
.
├── data/
│   ├── raw/
│   │   ├── raw_katalog_menu_tujuan_semata.csv
│   │   ├── raw_transaksi_dan_member_tujuan_semata.csv
│   │   └── raw_ulasan_dan_chat_tujuan_semata.csv
│   ├── processed/
│   │   ├── katalog_menu_tujuan_semata.csv
│   │   ├── transaksi_member_clean.csv
│   │   └── ulasan_dan_chat_tujuan_semata.csv
│   └── data_dictionary_transaksi_member.md
├── transaksi_dan_member.ipynb
├── requirements.txt
└── README.md
```

## Catatan Sumber & Label Data
> ⚠️ **Data Dummy:** `raw_transaksi_dan_member_tujuan_semata.csv` dan hasil bersihnya (`transaksi_member_clean.csv`) adalah **data dummy buatan tim** untuk simulasi (periode 16 – 22 November 2026) karena riwayat transaksi asli UMKM belum tersedia. Seluruh temuan bersifat ilustratif dan tidak boleh diklaim sebagai kondisi operasional nyata.

## Ringkasan Notebook: `transaksi_dan_member.ipynb`
Menggunakan alur **CRISP-DM** (*Business Understanding → Data Understanding → Data Preparation* + Analisis Data) tanpa tahap *Modeling/Deployment* (model rekomendasi & sentimen menggunakan dataset katalog dan ulasan).

**Pertanyaan Bisnis yang Dijawab:**
1. **Operasional & Pencatatan:** Perbandingan kesalahan pesanan (*salah dengar* & *double order*) antara pencatatan manual dan digital, terutama saat *peak hour*.
2. **Loyalitas Member:** Kontribusi member vs non-member serta hubungan level member dengan frekuensi kunjungan dan nilai belanja.
3. **Performa Menu & Waktu Ramai:** Menu paling laris/menguntungkan dan jam operasional paling ramai.

**Output Utama:**
- `data/processed/transaksi_member_clean.csv` (`2.500` baris × `26` kolom) — siap digunakan sebagai *seed data* dummy panel admin/poin maupun indikator dasbor pemilik.
- `data/data_dictionary_transaksi_member.md` — penjelasan lengkap tiap kolom, cara membaca, dan keterbatasan data.

## Cara Menjalankan
```bash
pip install -r requirements.txt
```
Buka dan jalankan seluruh sel pada `transaksi_dan_member.ipynb`.

**Contoh membaca dataset bersih:**
```python
import pandas as pd
df = pd.read_csv("data/processed/transaksi_member_clean.csv", parse_dates=["Tgl_Order", "Waktu_Transaksi"])
```
