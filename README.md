# LAPORAN PROYEK MANDIRI MAGANG
## Analisis Deret Waktu (Time Series) Data Overtime Karyawan

| | |
|---|---|
| **Nama Mahasiswa** | *Akhmad Dany* |
| **Tempat Magang** | PT Japfa Comfeed Indonesia Tbk |
---

## BAB I — PENDAHULUAN

### 1.1 Latar Belakang

PT Japfa Comfeed Indonesia Tbk merupakan salah satu perusahaan agribisnis terkemuka di Indonesia yang bergerak di bidang produksi pakan ternak, pengolahan unggas, dan produk turunannya. Dengan skala operasional yang besar dan jumlah karyawan yang mencapai ribuan orang, manajemen sumber daya manusia (SDM) — khususnya pengelolaan jam lembur (*overtime*) — menjadi aspek kritis dalam efisiensi operasional perusahaan.

Selama periode magang, penulis mendapatkan tugas mandiri untuk melakukan **Analisis Deret Waktu (Time Series Analysis)** terhadap data lembur karyawan sepanjang tahun 2024. Analisis ini bertujuan untuk memahami pola jam lembur dan membangun model peramalan (*forecasting*) yang dapat digunakan sebagai dasar perencanaan anggaran lembur dan kebutuhan SDM di masa mendatang.

### 1.2 Rumusan Masalah

1. Bagaimana pola (trend, musiman, dan residual) data lembur karyawan PT Japfa Comfeed Indonesia Tbk sepanjang tahun 2024?
2. Apakah data deret waktu jam lembur bersifat stasioner?
3. Model peramalan manakah yang paling akurat untuk memprediksi total jam lembur harian?

### 1.3 Tujuan

1. Memahami karakteristik dan pola deret waktu data lembur karyawan.
2. Menguji stasioneritas data menggunakan uji Augmented Dickey-Fuller (ADF).
3. Membangun dan membandingkan model peramalan: **ARIMA**, **SARIMA**, dan **Simple Moving Average (SMA)**.
4. Menghasilkan rekomendasi model terbaik untuk peramalan jam lembur.

### 1.4 Manfaat

- **Bagi Perusahaan**: Menyediakan alat bantu prediksi jam lembur yang dapat digunakan HRD untuk perencanaan anggaran dan rotasi kerja.
- **Bagi Akademik**: Menunjukkan penerapan nyata metode Analisis Time Series pada data industri skala besar.

---

## BAB II — PROFIL PERUSAHAAN DAN DESKRIPSI PEKERJAAN

### 2.1 Profil PT Japfa Comfeed Indonesia Tbk

PT Japfa Comfeed Indonesia Tbk (kode saham: JPFA) adalah perusahaan yang berdiri sejak 1971 dan kini menjadi salah satu pemain terbesar di industri agribisnis Asia Tenggara. Perusahaan bergerak di segmen:

- **Pakan Ternak** (*Animal Feed*): Produksi pakan unggas dan akuakultur
- **Unggas Komersial** (*Commercial Farm*): Pembibitan dan budidaya ayam broiler
- **Pengolahan Makanan** (*Consumer Food*): Produk olahan daging bermerek

Pabrik yang berlokasi di **Sidoarjo, Jawa Timur** merupakan salah satu fasilitas produksi terbesar, mengelola ribuan karyawan dari berbagai divisi operasional.

### 2.2 Deskripsi Tugas Magang

Selama magang, penulis ditempatkan di divisi terkait SDM/HRD dan mendapatkan akses terhadap data lembur karyawan dari sistem informasi perusahaan. Tugas mandiri yang diberikan adalah:

> **"Melakukan analisis deret waktu terhadap data Surat Perintah Lembur (SPL) karyawan tahun 2024 untuk memahami pola lembur dan membangun model peramalan jam lembur."**

---

## BAB III — METODOLOGI DAN LANGKAH PENGERJAAN

### 3.1 Dataset

| Atribut | Keterangan |
|---|---|
| **Sumber Data** | Sistem informasi HR PT Japfa (file: `OVERTIME ALL 2024.csv`) |
| **Jumlah Record** | 42.778 baris |
| **Jumlah Kolom** | 17 kolom |
| **Periode** | 1 Januari 2024 — 31 Desember 2024 |
| **Tools** | Python (Pandas, Statsmodels, Matplotlib) di Jupyter Notebook |

**Variabel utama yang digunakan:**

| Kolom | Deskripsi |
|---|---|
| `SPL Date` | Tanggal pengajuan lembur |
| `Total HoursBAL` | Total jam lembur aktual yang terealisasi |
| `BAL Status` | Status realisasi lembur (Completed / Generated) |
| `SPL Status` | Status persetujuan (Approved / Rejected / Drafted) |
| `Reason` | Kategori alasan lembur (14 kategori) |
| `ID_Employee` | ID unik karyawan |

---

### 3.2 Alur Pengerjaan (Pipeline)

```
RAW DATA (CSV)
     │
     ▼
[STEP 1] Load & Eksplorasi Awal
     │
     ▼
[STEP 2] Data Preprocessing
   ├── Menghapus tag HTML pada kolom status
   ├── Memisahkan ID dan Nama (Supervisor & Employee)
   ├── Konversi tipe data string → datetime
   └── Strip whitespace
     │
     ▼
[STEP 3] Feature Engineering
   ├── Ekstraksi: Tahun, Bulan, Minggu, Hari
   ├── Klasifikasi: Weekday vs Weekend
   └── Klasifikasi: Shift Pagi / Siang / Malam
     │
     ▼
[STEP 4] Filtering Data
   └── Hanya OT berstatus "Approved" + "BAL Completed/Generated"
       (38.342 records dari 42.778)
     │
     ▼
[STEP 5] Pembentukan Deret Waktu Harian
   └── Agregasi: Total Jam Lembur per Hari (366 titik data)
     │
     ▼
[STEP 6] Exploratory Data Analysis (EDA)
   ├── Statistik Deskriptif
   ├── Visualisasi Trend Harian & Mingguan
   ├── Distribusi per Bulan & Hari
   └── Analisis per Kategori Alasan Lembur
     │
     ▼
[STEP 7] Dekomposisi Deret Waktu
   └── Trend + Seasonality + Residual
     │
     ▼
[STEP 8] Uji Stasioneritas (ADF Test)
     │
     ▼
[STEP 9] Analisis ACF & PACF
     │
     ▼
[STEP 10] Pemodelan & Peramalan
   ├── Simple Moving Average (SMA)
   ├── ARIMA
   └── SARIMA
     │
     ▼
[STEP 11] Evaluasi & Perbandingan Model
   └── MAE, MSE, RMSE, MAPE
```

---

### 3.3 Detail Setiap Langkah

#### STEP 1 — Load & Eksplorasi Awal Data

Dataset dimuat menggunakan library `pandas`. Eksplorasi awal dilakukan dengan:
- `data.info()` → 17 kolom, sebagian besar bertipe `object` (string)
- `data.isnull().sum()` → Ditemukan missing values signifikan di kolom `BAL Status` (4.430), `Remarks` (38.883), `Clock In/Out` (4.590)

#### STEP 2 — Data Preprocessing

Empat langkah pembersihan data dilakukan:

1. **Hapus tag HTML**: Kolom `BAL Status` dan `SPL Status` mengandung HTML tag seperti `<span style="color:green">`. Dibersihkan menggunakan regex.
2. **Pisahkan ID dan Nama**: Format "10006668 - Samsul A" dipisahkan menjadi dua kolom menggunakan `.str.split()` dengan regex.
3. **Konversi tipe data datetime**: Semua kolom tanggal dikonversi dari `string` ke `datetime64` menggunakan format eksplisit `'%d-%b-%Y %H:%M'` untuk menghindari *parsing error* dan *UserWarning*.
4. **Strip whitespace**: Spasi berlebih pada nilai string dihapus menggunakan `.map()`.

#### STEP 3 — Feature Engineering

Dari kolom `SPL Date`, diekstrak fitur-fitur turunan:

| Fitur Baru | Deskripsi |
|---|---|
| `Year`, `Month`, `Day` | Komponen waktu |
| `Month_Name` | Nama bulan (January, February, ...) |
| `Week` | Nomor minggu ISO (1–53) |
| `Day_Name` | Nama hari (Monday–Sunday) |
| `Day_Type` | Weekday atau Weekend |
| `Shift_Type` | Morning (< 12:00), Afternoon (12–18), Night (> 18:00) |

#### STEP 4 — Filtering Data

Data difilter hanya untuk catatan lembur yang **sudah disetujui dan terealisasi**:
- `BAL Status` mengandung "Completed" atau "Generated"
- `SPL Status` = "Approved"

**Hasil filter**: 38.342 records (89,6% dari total) digunakan untuk analisis.

#### STEP 5 — Pembentukan Deret Waktu Harian

Data diagregasi per hari menggunakan `groupby('Date')`:

```python
daily_ts = df_filtered.groupby('Date').agg({
    'Total HoursBAL': 'sum',   # Total jam lembur
    'ID_Employee': 'nunique',  # Jumlah karyawan unik
    'SPL No': 'count'          # Jumlah entri lembur
})
```

Deret waktu kemudian di-*reindex* untuk memastikan kesinambungan tanggal (tanggal tanpa lembur diisi 0).

---

## BAB IV — HASIL DAN ANALISIS

### 4.1 Statistik Deskriptif Deret Waktu Harian

| Statistik | Nilai |
|---|---|
| **Periode** | 1 Jan 2024 — 31 Des 2024 |
| **Jumlah Titik Data** | 366 hari |
| **Total Jam Lembur** | 193.806,18 jam |
| **Rata-rata Jam Lembur/Hari** | 529,53 jam |
| **Maksimum Jam Lembur (1 hari)** | 3.393,52 jam |
| **Minimum Jam Lembur (1 hari)** | 4,00 jam |
| **Total Karyawan yang Pernah Lembur** | 842 orang |
| **Total Supervisor** | 70 orang |

### 4.2 Analisis Top Alasan Lembur

| Peringkat | Kategori Lembur | Jumlah Entri |
|---|---|---|
| 1 | Overtime Warehouse | 16.704 |
| 2 | Overtime Day Off | 6.703 |
| 3 | Overtime Substitutions | 6.358 |
| 4 | Overtime Production | 2.805 |
| 5 | Overtime Excess | 1.518 |

> **Interpretasi**: Lembur di area *Warehouse* mendominasi (43,6% dari total), menunjukkan tingginya tekanan operasional di bagian logistik dan penyimpanan pakan ternak. Ini relevan dengan siklus panen dan permintaan pakan yang fluktuatif.

### 4.3 Dekomposisi Deret Waktu

Dekomposisi menggunakan metode *additive* mengungkapkan:
- **Trend**: Terdapat fluktuasi sepanjang tahun tanpa kecenderungan naik/turun yang konsisten, menunjukkan lembur dipengaruhi oleh faktor operasional periodik.
- **Seasonality**: Terlihat pola mingguan — lembur cenderung lebih tinggi di hari-hari tertentu dalam seminggu, serta pola bulanan yang berkaitan dengan siklus produksi.
- **Residual**: Terdapat variasi tak terduga pada beberapa periode, kemungkinan terkait event khusus (libur nasional, puncak produksi musiman).

### 4.4 Uji Stasioneritas (Augmented Dickey-Fuller)

Uji ADF dilakukan pada variabel `Total_Overtime_Hours`:

| Hasil Uji | Nilai |
|---|---|
| **ADF Statistic** | *(nilai dari output notebook)* |
| **p-value** | `< 0.05` |
| **Kesimpulan** | ✅ Data **STASIONER** |

Karena data stasioner, model ARIMA dapat diterapkan langsung tanpa perlu *differencing* (d=0 dapat dipertimbangkan).

### 4.5 Analisis ACF & PACF

Plot ACF dan PACF digunakan untuk menentukan parameter model ARIMA(p,d,q):
- **PACF** → Menentukan orde *Autoregressive* (**p**)
- **ACF** → Menentukan orde *Moving Average* (**q**)

---

### 4.6 Perbandingan Model Peramalan

Tiga model dibangun dan dibandingkan menggunakan data **uji (Oktober–Desember 2024)**:

| Model | MAE | MSE | RMSE | MAPE |
|---|---|---|---|---|
| ARIMA | 254,04 | 196.798,76 | 443,62 | 43,51% |
| **SARIMA** | **208,39** | **129.212,46** | **359,46** | **35,43%** |
| SMA | 272,57 | 196.135,88 | 442,87 | 49,72% |

> [!IMPORTANT]
> **Model SARIMA memberikan performa terbaik** di semua metrik evaluasi (MAE, MSE, RMSE, dan MAPE terendah). SARIMA unggul karena mampu menangkap **komponen musiman** dalam data lembur yang berpola mingguan/bulanan — sesuatu yang tidak bisa ditangkap oleh ARIMA dan SMA.

**Interpretasi Metrik:**
- **MAE** (Mean Absolute Error): Rata-rata error absolut dalam jam per hari
- **RMSE** (Root Mean Squared Error): Lebih sensitif terhadap outlier
- **MAPE** (Mean Absolute Percentage Error): Error relatif dalam persentase

---

## BAB V — RELEVANSI TERHADAP MATA KULIAH "ANALISIS TIME SERIES"

### 5.1 Pemetaan Topik Akademis

Tugas mandiri ini mencakup seluruh topik inti mata kuliah Analisis Time Series, sebagaimana dipetakan berikut:

| No | Topik Mata Kuliah | Implementasi di Tugas Magang |
|---|---|---|
| 1 | **Konsep & Definisi Deret Waktu** | Data lembur harian (366 observasi) sebagai *univariate time series* |
| 2 | **Komponen Deret Waktu** | Dekomposisi: Trend + Seasonality + Residual |
| 3 | **Stasioneritas** | Uji Augmented Dickey-Fuller (ADF Test) |
| 4 | **Model AR (Autoregressive)** | Bagian dari ARIMA dan SARIMA |
| 5 | **Model MA (Moving Average)** | Bagian dari ARIMA dan SMA |
| 6 | **Model ARIMA** | Diimplementasikan dan dievaluasi |
| 7 | **Model SARIMA** | Model dengan komponen musiman — terpilih sebagai terbaik |
| 8 | **Fungsi Autokorelasi (ACF & PACF)** | Digunakan untuk identifikasi orde model |
| 9 | **Evaluasi Model** | MAE, MSE, RMSE, MAPE |
| 10 | **Forecasting** | Prediksi jam lembur periode berikutnya |

### 5.2 Nilai Akademis Tambahan

Dibandingkan studi kasus berbasis data simulasi, tugas ini memiliki keunggulan akademis:

1. **Data Nyata Berskala Industri**: 42.778 record dari sistem HR perusahaan tbk, bukan dataset artifisial.
2. **Tantangan Data Riil**: Penanganan HTML tag, missing values, format tanggal tidak konsisten, dan data yang perlu difilter berdasarkan logika bisnis.
3. **Konteks Bisnis yang Jelas**: Hasil analisis memiliki implikasi nyata bagi manajemen SDM perusahaan (perencanaan lembur, anggaran).
4. **Multi-Model Comparison**: Membandingkan 3 model berbeda secara kuantitatif menggunakan metrik standar.

### 5.3 Kontribusi Praktis bagi Perusahaan

Hasil analisis ini dapat langsung dimanfaatkan oleh PT Japfa Comfeed Indonesia untuk:

- **Perencanaan Anggaran Lembur**: Prediksi SARIMA menghasilkan estimasi total jam lembur bulanan dengan error rata-rata ~35,4%, yang dapat membantu penyusunan RAB (Rencana Anggaran Biaya) divisi.
- **Identifikasi Periode Puncak**: Dekomposisi musiman mengungkap periode dengan kebutuhan lembur tinggi, memungkinkan penjadwalan proaktif.
- **Deteksi Anomali**: Nilai residual yang tinggi mengindikasikan kejadian tak terduga yang perlu diinvestigasi (misalnya: lonjakan produksi mendadak).

---

## BAB VI — PENUTUP

### 6.1 Kesimpulan

1. Data lembur karyawan PT Japfa Comfeed Indonesia Tbk selama tahun 2024 menunjukkan pola deret waktu yang **stasioner** dengan komponen musiman yang signifikan.
2. Total jam lembur karyawan mencapai **193.806,18 jam** selama setahun, dengan rata-rata **529,53 jam per hari**. Dominasi lembur berada di divisi *Warehouse* (43,6%).
3. Di antara tiga model yang diuji, **SARIMA memberikan performa terbaik** dengan RMSE 359,46 jam dan MAPE 35,43% — lebih unggul dari ARIMA (RMSE 443,62) dan SMA (RMSE 442,87).
4. Tugas magang ini mencakup seluruh siklus analisis deret waktu secara menyeluruh, mulai dari *data wrangling*, eksplorasi, dekomposisi, pemodelan, hingga evaluasi.

### 6.2 Saran

1. **Pengembangan Model**: Untuk akurasi yang lebih tinggi, dapat dikembangkan model **Prophet** (Meta) yang secara native menangani efek hari libur nasional — faktor yang sangat relevan untuk data lembur industri Indonesia.
2. **Granularitas Data**: Analisis dapat diperdalam dengan melakukan *time series per divisi* atau per kategori alasan lembur untuk insight yang lebih spesifik.
3. **Integrasi Sistem**: Rekomendasi jangka panjang adalah mengintegrasikan model forecasting ini langsung ke dalam sistem HR perusahaan untuk pembaruan prediksi secara otomatis.

---

## LAMPIRAN — Ringkasan Kode Python Utama

### A. Pembentukan Deret Waktu Harian
```python
daily_ts = df_filtered.groupby('Date').agg({
    'Total HoursBAL': 'sum',
    'ID_Employee': 'nunique',
    'SPL No': 'count'
}).reset_index()
daily_ts.columns = ['Date', 'Total_Overtime_Hours', 'Unique_Employees', 'Overtime_Entries']
daily_ts = daily_ts.sort_values('Date').set_index('Date')
```

### B. Uji Stasioneritas (ADF)
```python
from statsmodels.tsa.stattools import adfuller
adf_result = adfuller(ts_data)
# Interpretasi: p-value < 0.05 → Data Stasioner
```

### C. Pemodelan SARIMA
```python
from statsmodels.tsa.statespace.sarimax import SARIMAX
sarima_model = SARIMAX(train_data, order=(p,d,q), seasonal_order=(P,D,Q,s))
sarima_fit = sarima_model.fit()
sarima_pred = sarima_fit.forecast(steps=len(test_data))
```

### D. Evaluasi Model
```python
from sklearn.metrics import mean_absolute_error, mean_squared_error
import numpy as np

mae  = mean_absolute_error(actual, predicted)
mse  = mean_squared_error(actual, predicted)
rmse = np.sqrt(mse)
mape = np.mean(np.abs((actual - predicted) / actual)) * 100
```

---

*Laporan ini disusun berdasarkan tugas mandiri selama magang di PT Japfa Comfeed Indonesia Tbk dan digunakan sebagai bukti pelaksanaan konversi mata kuliah Analisis Time Series.*
