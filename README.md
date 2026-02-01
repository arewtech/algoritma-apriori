# ANALISIS PENGELOLAAN STOK KUE MENGGUNAKAN ALGORITMA APRIORI PADA TOKO KUE

Sistem manajemen stok kue berbasis web menggunakan algoritma Apriori untuk menganalisis pola pembelian dan memberikan rekomendasi stok produk berdasarkan data transaksi historis.

## 📚 Deskripsi Sistem

Sistem ini dirancang untuk membantu toko kue dalam:

-   Mengelola data produk (kue dan topping)
-   Mencatat transaksi penjualan
-   Menganalisis pola pembelian menggunakan Algoritma Apriori
-   Memberikan rekomendasi stok berdasarkan aturan asosiasi
-   Visualisasi data penjualan dan analisis

## 🔧 Teknologi yang Digunakan

-   **Framework**: Laravel 10.x
-   **Database**: MySQL
-   **Frontend**: Bootstrap 5, Chart.js, jQuery
-   **PHP**: 8.1+
-   **Authentication**: Laravel Built-in Auth

## 📊 Struktur Database

### 1. Tabel Products

Menyimpan data produk kue dan topping.

```sql
- id (Primary Key)
- name (varchar)
- type (enum: 'cake', 'topping')
- price (integer)
- stock (integer)
- timestamps
```

### 2. Tabel Transactions

Menyimpan data transaksi penjualan.

```sql
- id (Primary Key)
- transaction_code (varchar, unique)
- transaction_date (date)
- customer_name (varchar)
- user_id (Foreign Key to users)
- timestamps
```

### 3. Tabel Transaction_Items

Menyimpan detail item dalam transaksi.

```sql
- id (Primary Key)
- transaction_id (Foreign Key to transactions)
- product_id (Foreign Key to products)
- quantity (integer)
- price (integer)
- timestamps
```

### 4. Tabel Apriori_Processes

Menyimpan log proses analisis Apriori.

```sql
- id (Primary Key)
- start_date (date)
- end_date (date)
- min_support (decimal)
- min_confidence (decimal)
- total_transactions (integer)
- total_rules (integer)
- status (enum: 'processing', 'completed', 'failed')
- timestamps
```

### 5. Tabel Apriori_Rules

Menyimpan hasil aturan asosiasi dari Algoritma Apriori.

```sql
- id (Primary Key)
- apriori_process_id (Foreign Key)
- antecedent (text) - Produk X
- consequent (text) - Produk Y
- support (decimal)
- confidence (decimal)
- timestamps
```

---

## 🧮 CARA PERHITUNGAN ALGORITMA APRIORI

### A. KONSEP DASAR ALGORITMA APRIORI

Algoritma Apriori adalah algoritma data mining untuk menemukan pola asosiasi (association rules) antara item-item dalam dataset transaksi. Algoritma ini bekerja dengan prinsip "apriori" yang menyatakan bahwa **jika suatu itemset frequent, maka semua subset-nya juga frequent**.

**Tujuan**: Menemukan aturan asosiasi dalam bentuk **"Jika membeli X, maka akan membeli Y"**

### B. PARAMETER UTAMA

#### 1. **Support (Dukungan)**

Support mengukur seberapa sering suatu item atau kombinasi item muncul dalam keseluruhan transaksi.

**Rumus:**

```
Support(A) = (Jumlah transaksi yang mengandung A) / (Total seluruh transaksi) × 100%

Support(A,B) = (Jumlah transaksi yang mengandung A dan B) / (Total seluruh transaksi) × 100%
```

**Interpretasi:**

-   Support tinggi = Item tersebut sering dibeli
-   Support rendah = Item tersebut jarang dibeli

#### 2. **Confidence (Kepercayaan)**

Confidence mengukur seberapa kuat hubungan antara dua item dalam aturan asosiasi.

**Rumus:**

```
Confidence(A → B) = Support(A,B) / Support(A) × 100%
```

**Interpretasi:**

-   Confidence tinggi = Jika membeli A, kemungkinan besar akan membeli B
-   Confidence rendah = Hubungan antara A dan B lemah

---

### C. TAHAPAN PERHITUNGAN APRIORI

#### **TAHAP 1: Persiapan Data Transaksi**

Kumpulkan data transaksi dalam periode tertentu.

**Contoh Data Transaksi:**

| No  | Kode Transaksi | Tanggal    | Produk yang Dibeli                        |
| --- | -------------- | ---------- | ----------------------------------------- |
| 1   | TRX-001        | 2025-01-01 | Donat Coklat, Topping Meses               |
| 2   | TRX-002        | 2025-01-02 | Donat Keju, Topping Keju                  |
| 3   | TRX-003        | 2025-01-02 | Donat Coklat, Topping Meses, Topping Keju |
| 4   | TRX-004        | 2025-01-03 | Donat Keju, Topping Meses                 |
| 5   | TRX-005        | 2025-01-03 | Donat Coklat, Topping Keju                |
| 6   | TRX-006        | 2025-01-04 | Donat Coklat, Topping Meses               |
| 7   | TRX-007        | 2025-01-04 | Donat Keju, Topping Keju, Topping Meses   |
| 8   | TRX-008        | 2025-01-05 | Donat Coklat, Topping Meses               |
| 9   | TRX-009        | 2025-01-05 | Donat Keju, Topping Keju                  |
| 10  | TRX-010        | 2025-01-06 | Donat Coklat, Topping Meses, Topping Keju |

**Total Transaksi: 10**

---

#### **TAHAP 2: Menghitung Support untuk Item Tunggal (1-itemset)**

Hitung berapa kali setiap produk muncul dalam transaksi.

**Perhitungan:**

| Produk        | Jumlah Kemunculan | Support (%)           |
| ------------- | ----------------- | --------------------- |
| Donat Coklat  | 6                 | 6/10 × 100% = **60%** |
| Donat Keju    | 4                 | 4/10 × 100% = **40%** |
| Topping Meses | 7                 | 7/10 × 100% = **70%** |
| Topping Keju  | 5                 | 5/10 × 100% = **50%** |

**Contoh Perhitungan Detail:**

```
Support(Donat Coklat) = 6/10 × 100% = 60%
- Muncul di transaksi: TRX-001, TRX-003, TRX-005, TRX-006, TRX-008, TRX-010

Support(Topping Meses) = 7/10 × 100% = 70%
- Muncul di transaksi: TRX-001, TRX-003, TRX-004, TRX-006, TRX-007, TRX-008, TRX-010
```

**Filter dengan Min Support (misalnya 30%):**

-   Semua item lolos karena support ≥ 30%

---

#### **TAHAP 3: Menghitung Support untuk Kombinasi 2 Item (2-itemset)**

Hitung support untuk setiap pasangan produk yang dibeli bersamaan.

**Perhitungan:**

| Kombinasi Produk             | Jumlah Kemunculan | Support (%)           |
| ---------------------------- | ----------------- | --------------------- |
| Donat Coklat + Topping Meses | 5                 | 5/10 × 100% = **50%** |
| Donat Coklat + Topping Keju  | 2                 | 2/10 × 100% = **20%** |
| Donat Keju + Topping Meses   | 2                 | 2/10 × 100% = **20%** |
| Donat Keju + Topping Keju    | 3                 | 3/10 × 100% = **30%** |
| Topping Meses + Topping Keju | 3                 | 3/10 × 100% = **30%** |

**Contoh Perhitungan Detail:**

```
Support(Donat Coklat, Topping Meses) = 5/10 × 100% = 50%
- Muncul bersamaan di transaksi: TRX-001, TRX-003, TRX-006, TRX-008, TRX-010

Support(Donat Keju, Topping Keju) = 3/10 × 100% = 30%
- Muncul bersamaan di transaksi: TRX-002, TRX-007, TRX-009
```

**Filter dengan Min Support (30%):**

-   Donat Coklat + Topping Meses: **50%** ✅ LOLOS
-   Donat Keju + Topping Keju: **30%** ✅ LOLOS
-   Topping Meses + Topping Keju: **30%** ✅ LOLOS
-   Donat Coklat + Topping Keju: 20% ❌ TIDAK LOLOS
-   Donat Keju + Topping Meses: 20% ❌ TIDAK LOLOS

---

#### **TAHAP 4: Menghitung Confidence untuk Aturan Asosiasi**

Dari kombinasi yang lolos, buat aturan asosiasi dan hitung confidence-nya.

**Formula:**

```
Confidence(A → B) = Support(A,B) / Support(A) × 100%
```

**Perhitungan Aturan 1:**

```
Aturan: Donat Coklat → Topping Meses

Confidence = Support(Donat Coklat, Topping Meses) / Support(Donat Coklat) × 100%
Confidence = 50% / 60% × 100%
Confidence = 0.833 × 100%
Confidence = 83.33%

Interpretasi:
Jika pelanggan membeli Donat Coklat, ada kemungkinan 83.33%
mereka juga akan membeli Topping Meses.
```

**Perhitungan Aturan 2:**

```
Aturan: Topping Meses → Donat Coklat

Confidence = Support(Donat Coklat, Topping Meses) / Support(Topping Meses) × 100%
Confidence = 50% / 70% × 100%
Confidence = 0.714 × 100%
Confidence = 71.43%

Interpretasi:
Jika pelanggan membeli Topping Meses, ada kemungkinan 71.43%
mereka juga akan membeli Donat Coklat.
```

**Perhitungan Aturan 3:**

```
Aturan: Donat Keju → Topping Keju

Confidence = Support(Donat Keju, Topping Keju) / Support(Donat Keju) × 100%
Confidence = 30% / 40% × 100%
Confidence = 0.75 × 100%
Confidence = 75%

Interpretasi:
Jika pelanggan membeli Donat Keju, ada kemungkinan 75%
mereka juga akan membeli Topping Keju.
```

**Perhitungan Aturan 4:**

```
Aturan: Topping Keju → Donat Keju

Confidence = Support(Donat Keju, Topping Keju) / Support(Topping Keju) × 100%
Confidence = 30% / 50% × 100%
Confidence = 0.6 × 100%
Confidence = 60%

Interpretasi:
Jika pelanggan membeli Topping Keju, ada kemungkinan 60%
mereka juga akan membeli Donat Keju.
```

---

#### **TAHAP 5: Filter Aturan Berdasarkan Min Confidence**

**Misalkan Min Confidence = 60%**

**Hasil Aturan Asosiasi yang Valid:**

| No  | Antecedent (Jika) | Consequent (Maka) | Support | Confidence | Status   |
| --- | ----------------- | ----------------- | ------- | ---------- | -------- |
| 1   | Donat Coklat      | Topping Meses     | 50%     | 83.33%     | ✅ VALID |
| 2   | Topping Meses     | Donat Coklat      | 50%     | 71.43%     | ✅ VALID |
| 3   | Donat Keju        | Topping Keju      | 30%     | 75%        | ✅ VALID |
| 4   | Topping Keju      | Donat Keju        | 30%     | 60%        | ✅ VALID |

**Semua aturan lolos karena Confidence ≥ 60%**

---

### D. IMPLEMENTASI DALAM SISTEM

#### **1. Input Parameter Analisis**

Dalam sistem, admin dapat mengatur:

-   **Start Date**: Tanggal mulai periode analisis (contoh: 2025-01-01)
-   **End Date**: Tanggal akhir periode analisis (contoh: 2025-01-31)
-   **Min Support**: Nilai minimum support (contoh: 30%)
-   **Min Confidence**: Nilai minimum confidence (contoh: 60%)

#### **2. Proses Analisis**

Sistem akan:

1. Mengambil semua transaksi dalam periode yang ditentukan
2. Menghitung support untuk setiap item dan kombinasi item
3. Filter itemset dengan support ≥ min_support
4. Membuat aturan asosiasi dari itemset yang lolos
5. Menghitung confidence untuk setiap aturan
6. Filter aturan dengan confidence ≥ min_confidence
7. Menyimpan hasil ke database

#### **3. Output Hasil Analisis**

Sistem menampilkan:

-   **Daftar Aturan Asosiasi**: Aturan "Jika X, maka Y"
-   **Nilai Support**: Seberapa sering kombinasi muncul
-   **Nilai Confidence**: Seberapa kuat hubungan antara X dan Y
-   **Visualisasi**: Grafik distribusi aturan

---

### E. CONTOH KASUS PENGGUNAAN UNTUK SKRIPSI

#### **Kasus: Analisis Stok Bulan Januari 2025**

**Data:**

-   Total Transaksi: 100
-   Periode: 1-31 Januari 2025
-   Min Support: 25%
-   Min Confidence: 60%

**Hasil Analisis:**

| Aturan                       | Support | Confidence | Rekomendasi                                                |
| ---------------------------- | ------- | ---------- | ---------------------------------------------------------- |
| Donat Coklat → Topping Meses | 45%     | 85%        | Stok Topping Meses tinggi jika stok Donat Coklat tinggi    |
| Donat Keju → Topping Keju    | 35%     | 78%        | Pastikan Topping Keju tersedia saat stok Donat Keju banyak |
| Topping Meses → Donat Coklat | 45%     | 72%        | Promosi bundling Donat Coklat + Topping Meses              |

**Kesimpulan untuk BAB 4:**

1. **Support 45%**:

    - Dari 100 transaksi, 45 transaksi membeli Donat Coklat dan Topping Meses bersamaan
    - Ini menunjukkan kombinasi populer

2. **Confidence 85%**:

    - 85% pelanggan yang membeli Donat Coklat juga membeli Topping Meses
    - Hubungan sangat kuat antara kedua produk

3. **Rekomendasi Bisnis**:
    - Jaga stok Topping Meses selalu tersedia saat Donat Coklat banyak
    - Buat paket bundling untuk meningkatkan penjualan
    - Letakkan kedua produk berdekatan di display

---

### F. VALIDASI HASIL PERHITUNGAN

#### **Cara Memvalidasi:**

1. **Manual Checking**:

    ```
    Ambil 10 transaksi sebagai sampel
    Hitung manual support dan confidence
    Bandingkan dengan hasil sistem
    ```

2. **Cross Validation**:

    ```
    Bagi data menjadi 2 periode
    Jalankan analisis di kedua periode
    Lihat konsistensi aturan yang dihasilkan
    ```

3. **Business Validation**:
    ```
    Konsultasi dengan pemilik toko
    Apakah aturan sesuai dengan pengalaman bisnis?
    Implementasi rekomendasi dan ukur hasilnya
    ```

---

### G. KELEBIHAN DAN KETERBATASAN

#### **Kelebihan:**

-   ✅ Mudah dipahami dan diimplementasikan
-   ✅ Tidak memerlukan data training
-   ✅ Menghasilkan aturan yang actionable
-   ✅ Cocok untuk analisis keranjang belanja (market basket analysis)

#### **Keterbatasan:**

-   ⚠️ Menghasilkan banyak aturan jika min support dan confidence terlalu rendah
-   ⚠️ Tidak memperhitungkan faktor waktu atau musiman
-   ⚠️ Hanya menangkap pola frekuensi, bukan kausalitas

---

## 🚀 Instalasi dan Setup

### 1. Clone Repository

```bash
git clone <repository-url>
cd donat-app
```

### 2. Install Dependencies

```bash
composer install
npm install
```

### 3. Konfigurasi Environment

```bash
cp .env.example .env
php artisan key:generate
```

Atur konfigurasi database di file `.env`:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=donat_app
DB_USERNAME=root
DB_PASSWORD=
```

### 4. Migrasi Database dan Seeding

```bash
php artisan migrate:fresh --seed
```

### 5. Jalankan Aplikasi

```bash
php artisan serve
```

Akses aplikasi di: `http://localhost:8000`

### 6. Login Credentials

```
Email: admin@donatstore.com
Password: password123
```

---

## 📱 Fitur Utama

### 1. Manajemen Produk

-   Tambah, edit, hapus produk
-   Monitoring stok produk
-   Filter berdasarkan tipe (cake/topping)

### 2. Manajemen Transaksi

-   Input transaksi multi-produk
-   Generate kode transaksi otomatis
-   Riwayat transaksi detail

### 3. Analisis Apriori

-   Set parameter (min support, min confidence)
-   Proses analisis otomatis
-   View hasil aturan asosiasi
-   Visualisasi data dengan Chart.js

### 4. Dashboard

-   Statistik penjualan
-   Grafik trend penjualan
-   Top produk terlaris

### 5. Autentikasi

-   Login admin
-   Manajemen profil
-   Ganti password

---

## 📖 Referensi untuk Skripsi

### Jurnal dan Buku:

1. Agrawal, R., & Srikant, R. (1994). Fast algorithms for mining association rules.
2. Han, J., & Kamber, M. (2006). Data Mining: Concepts and Techniques.
3. Tan, P. N., Steinbach, M., & Kumar, V. (2005). Introduction to Data Mining.

### Dataset:

-   Data transaksi real dari toko kue (periode dapat disesuaikan)
-   Minimal 3 bulan data untuk analisis yang valid

### Metode Pengujian:

1. **Functional Testing**: Testing semua fitur sistem
2. **Algorithm Validation**: Validasi hasil perhitungan manual vs sistem
3. **User Acceptance Testing**: Testing dengan pemilik toko

---

## 📝 Struktur BAB 4 Skripsi (Saran)

### BAB IV - IMPLEMENTASI DAN PENGUJIAN SISTEM

#### 4.1 Implementasi Sistem

-   4.1.1 Lingkungan Pengembangan
-   4.1.2 Struktur Database
-   4.1.3 Implementasi Algoritma Apriori

#### 4.2 Perhitungan Algoritma Apriori

-   4.2.1 Persiapan Data Transaksi
-   4.2.2 Perhitungan Support
-   4.2.3 Perhitungan Confidence
-   4.2.4 Pembentukan Aturan Asosiasi

#### 4.3 Contoh Kasus dan Hasil Analisis

-   4.3.1 Data Sampel
-   4.3.2 Proses Perhitungan Manual
-   4.3.3 Hasil Analisis Sistem
-   4.3.4 Validasi Hasil

#### 4.4 Pengujian Sistem

-   4.4.1 Pengujian Fungsional
-   4.4.2 Pengujian Algoritma
-   4.4.3 User Acceptance Testing

#### 4.5 Analisis Hasil

-   4.5.1 Interpretasi Aturan Asosiasi
-   4.5.2 Rekomendasi Pengelolaan Stok
-   4.5.3 Evaluasi Sistem

---

## 👨‍💻 Developer

**Nama**: [Nama Anda]
**NIM**: [NIM Anda]
**Judul Skripsi**: Analisis Pengelolaan Stok Kue Menggunakan Algoritma Apriori pada Toko Kue
**Pembimbing**: [Nama Pembimbing]

---

## 📄 License

This project is developed for educational purposes (Skripsi/Thesis).

---

## 📞 Contact

Untuk pertanyaan atau diskusi lebih lanjut mengenai implementasi sistem, silakan hubungi:

-   Email: [email-anda]
-   GitHub: [username-anda]

---

**© 2025 - Sistem Analisis Pengelolaan Stok Kue dengan Algoritma Apriori**
