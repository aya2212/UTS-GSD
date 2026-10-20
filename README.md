# UTS Graf Sains Data: Neo4j dan Sistem Rekomendasi (RetailRocket)

Proyek ini merupakan pengerjaan Ujian Tengah Semester (UTS) mata kuliah **Graf Sains Data**, Program Studi S1 Sains Data, Universitas Negeri Surabaya, Semester Gasal Tahun Akademik 2026/2027.

Proyek ini menggunakan **Python**, **Neo4j**, dan dataset **RetailRocket** untuk membangun graf interaksi e-commerce, melakukan *wrangling* dan *Exploratory Data Analysis* (EDA), membentuk *Vector Space Model* (VSM), serta mengembangkan dan mengevaluasi sistem rekomendasi berbasis kemiripan item.

## Isi Repositori

| File atau Folder | Deskripsi |
|---|---|
| `Jawaban_soal_pendahuluan` | Pengerjaan tugas pendahuluan bagian 1.1–1.3, meliputi persiapan environment, penggunaan driver Neo4j, dan impor data. |
| `Jawaban_UTS_Graf_Sains_Data_2023D` | Pengerjaan soal UTS bagian 2.1 (Wrangling dan EDA) serta 2.2 (Analytics). |
| `data/events_p1.csv` | Data interaksi periode P1 yang digunakan sebagai data *training*. |
| `data/events_p2.csv` | Data interaksi periode P2 yang digunakan sebagai data *testing* dan *ground truth* evaluasi. |
| `output/` | Folder untuk menyimpan gambar dan hasil visualisasi yang dihasilkan saat notebook dijalankan. |
| `requirements.txt` | Daftar dependensi Python yang diperlukan untuk menjalankan proyek. |

**Catatan dataset:** File `events.csv` asli dari RetailRocket tidak disertakan dalam repositori karena ukurannya besar. Dataset dapat diperoleh melalui [RetailRocket E-commerce Dataset di Kaggle](https://www.kaggle.com/datasets/retailrocket/ecommerce-dataset).

## Periode Data

Data interaksi dibagi menjadi dua periode yang terpisah untuk membedakan data *training* dan *testing*.

| Dataset | Periode | Jumlah Event | Fungsi |
|---|---|---:|---|
| P1 | 15–28 Mei 2015 | 304.718 | Training |
| P2 | 29 Mei–4 Juni 2015 | 145.321 | Testing dan ground truth |

Kolom `timestamp` pada dataset menggunakan format **Unix epoch dalam milidetik**. Timestamp dikonversi dan diperlakukan sebagai waktu UTC.

## Persyaratan

Sebelum menjalankan proyek, pastikan perangkat telah memiliki:

- Python dengan versi yang kompatibel dengan dependensi proyek.
- Neo4j Desktop atau Neo4j Server.
- Git, jika ingin mengelola atau mengunggah proyek melalui GitHub.
- Dataset RetailRocket yang memuat file `events.csv`.
- Jupyter Notebook atau Visual Studio Code dengan ekstensi Python dan Jupyter.

## Cara Menjalankan Proyek

### 1. Menyiapkan Neo4j

1. Buka Neo4j Desktop.
2. Buat atau jalankan instance database Neo4j.
3. Catat URI, username, dan password database yang digunakan.
4. Pastikan database aktif sebelum menjalankan notebook yang memerlukan koneksi Neo4j.

### 2. Menyiapkan Environment Python

Buka terminal atau PowerShell pada folder utama repositori, kemudian jalankan:

```powershell
python -m venv myenv
```

Aktifkan environment pada Windows PowerShell:

```powershell
.\myenv\Scripts\Activate.ps1
```

Perbarui pip dan instal dependensi:

```powershell
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 3. Menyiapkan Dataset

1. Unduh dataset RetailRocket dari Kaggle.
2. Ekstrak dataset dan pastikan file `events.csv` tersedia.
3. Simpan file tersebut pada lokasi yang dapat diakses oleh notebook.
4. Periksa dan sesuaikan variabel `SRC` pada sel konfigurasi di `02_Soal_UTS.ipynb` dengan lokasi file yang sebenarnya.
5. Pastikan file `data/events_p1.csv` dan `data/events_p2.csv` tersedia apabila notebook menggunakannya secara langsung.

### 4. Menjalankan Notebook

1. Buka folder repositori melalui Visual Studio Code.
2. Buka notebook `01_Soal_Pendahuluan.ipynb` dan jalankan sel sesuai urutan jika ingin menjalankan proses pendahuluan dan impor data.
3. Buka `02_Soal_UTS.ipynb`.
4. Pilih kernel Python dari environment `myenv`.
5. Pastikan konfigurasi lokasi dataset dan koneksi Neo4j sudah sesuai.
6. Lakukan **Restart Kernel**, kemudian jalankan **Run All**.
7. Masukkan password Neo4j ketika diminta oleh notebook.

> **Penting:** Jangan menuliskan password database, token, atau informasi rahasia lainnya secara langsung pada notebook yang akan diunggah ke GitHub.

## Ringkasan Metode

### 1. Pemodelan Graf

Graf interaksi dibangun dengan dua jenis simpul, yaitu `Visitor` dan `Item`, serta tiga jenis relasi berdasarkan perilaku pengguna:

- `(:Visitor)-[:VIEWED]->(:Item)` — pengguna melihat item.
- `(:Visitor)-[:ADDED_TO_CART]->(:Item)` — pengguna menambahkan item ke keranjang.
- `(:Visitor)-[:BOUGHT]->(:Item)` — pengguna membeli item.

Struktur tersebut merepresentasikan interaksi antara pengunjung dan item pada platform e-commerce.

### 2. Vector Space Model (VSM)

VSM digunakan untuk merepresentasikan interaksi pengguna dan item dalam bentuk matriks dengan bobot:

| Jenis Interaksi | Bobot |
|---|---:|
| `view` | 1 |
| `addtocart` | 3 |
| `transaction` | 5 |

Kemiripan antarvektor dihitung menggunakan **cosine similarity** untuk mengidentifikasi item yang memiliki pola interaksi serupa.

### 3. Proyeksi Graf

Proyeksi **Item–Item** dipilih sebagai pendekatan utama karena berfokus pada kemiripan antaritem yang dapat digunakan untuk menghasilkan rekomendasi berdasarkan item yang pernah diinteraksikan pengguna.

### 4. Sistem Rekomendasi

Sistem rekomendasi menggunakan pendekatan **item-based K-Nearest Neighbors (KNN)** dengan parameter:

- `k = 5` untuk jumlah tetangga item yang dipertimbangkan.
- Top-5 rekomendasi untuk evaluasi.
- Tiga item teratas untuk ditampilkan sebagai rekomendasi utama.

### 5. Evaluasi

Kinerja rekomendasi dievaluasi menggunakan:

- **Hit@5** — mengukur apakah item yang relevan ditemukan dalam lima rekomendasi teratas.
- **NDCG@5** — mengukur kualitas urutan rekomendasi dengan mempertimbangkan posisi item yang relevan.

Ground truth dibentuk dari item baru yang diinteraksikan pengguna pada periode P2, sesuai dengan definisi yang digunakan dalam notebook.

### 6. Reproducibility

Untuk membantu menghasilkan pemilihan pengguna acak yang konsisten, digunakan:

```python
numpy.random.default_rng(42)
```

Hasil eksperimen tetap bergantung pada dataset yang digunakan, proses preprocessing, konfigurasi database, serta urutan eksekusi notebook.

## Catatan Keamanan dan Penggunaan

- Jangan mengunggah password Neo4j, file `.env`, token, atau credential lainnya.
- Periksa ukuran dataset dan file hasil sebelum melakukan commit.
- Jika file terlalu besar untuk batas GitHub, gunakan Git LFS atau sediakan petunjuk pengunduhan dataset.
- Berhati-hatilah saat menjalankan query yang menghapus node dan relasi. Pastikan query dijalankan hanya pada database latihan yang sesuai.

## Penutup

Repositori ini mendokumentasikan penerapan graf untuk merepresentasikan interaksi e-commerce dan penggunaan kemiripan item dalam membangun sistem rekomendasi. Notebook menyediakan tahapan pengerjaan, mulai dari persiapan data hingga evaluasi rekomendasi berdasarkan periode training dan testing.

