# Optimasi TabNet untuk Credit Card Fraud Detection dengan Temporal Data Drift

## 1. Dataset

### Informasi Dataset

| Informasi | Keterangan |
|---|---|
| Nama | Credit Card Fraud Detection with Drift 2026 |
| Pembuat | Mohan Krishna Thalla |
| Sumber | [Kaggle Dataset](https://www.kaggle.com/datasets/mohankrishnathalla/credit-card-fraud-detection-with-drift-2026) |
| Periode data | Januari–Juni 2026 |
| Ukuran yang dilaporkan | 120.000 baris dan 73 kolom |
| Fraud rate yang dilaporkan | 9,49% |
| Kategori fraud | CNP, ATO, SYN-ID, CP, dan FF |


### Deskripsi

Dataset berisi data transaksi kartu kredit yang disiapkan untuk penelitian deteksi fraud dengan perubahan pola data dari waktu ke waktu (*temporal data drift*). Dataset mencakup informasi transaksi, karakteristik entitas, label fraud, serta metadata terkait periode dan perubahan distribusi.

Kategori fraud yang tersedia:

- **CNP (Card Not Present):** transaksi tanpa kehadiran kartu fisik.
- **ATO (Account Takeover):** pengambilalihan akun.
- **SYN-ID (Synthetic Identity):** penggunaan identitas sintetis.
- **CP (Card Present):** fraud pada transaksi dengan kartu fisik.
- **FF (Friendly Fraud):** transaksi yang dipersengketakan oleh pemegang kartu.

### Target dan Fitur

Target utama penelitian adalah `is_fraud` untuk klasifikasi biner:

- `0`: transaksi legitimate / non-fraud.
- `1`: transaksi fraud.

---

## 2. Tahapan Penelitian

Penelitian dilakukan melalui tahapan berikut:

1. **Dataset Understanding:** memahami struktur, fitur, target, dan karakteristik dataset.
2. **Exploratory Data Analysis (EDA):** menganalisis distribusi data, target, missing values, dan perubahan pola antarperiode.
3. **Data Preprocessing:** melakukan pembersihan data, pemilihan fitur, encoding, scaling, dan penanganan class imbalance.
4. **Temporal Data Splitting:** membagi data berdasarkan urutan waktu dan fase drift.
5. **Modelling:** membangun model pembanding dan melakukan optimasi pada TabNet.
6. **Evaluation:** membandingkan performa model dan menganalisis ketahanannya terhadap perubahan pola data.

---

## 3. Exploratory Data Analysis (EDA)

EDA difokuskan pada pemeriksaan berikut:

1. **Data Overview:** jumlah baris dan kolom, tipe data, statistik deskriptif, dan duplikasi.
2. **Target Distribution:** distribusi fraud dan non-fraud serta tingkat ketidakseimbangan kelas.
3. **Missing Values:** jumlah dan persentase nilai kosong pada setiap fitur.
4. **Feature Distribution:** distribusi fitur numerik dan kategorikal serta identifikasi nilai ekstrem.
5. **Feature Relationship:** hubungan fitur dengan target dan korelasi antarfitur.
6. **Temporal Analysis:** distribusi transaksi dan fraud rate berdasarkan bulan serta fase drift.
7. **Data Drift Exploration:** membandingkan distribusi fitur antarperiode untuk melihat perubahan pola data.

**Output EDA:** ringkasan karakteristik dataset, distribusi target, missing values, hubungan fitur, dan gambaran perubahan data antar fase.

---

## 4. Data Preprocessing

Tahapan preprocessing yang dilakukan:

1. **Data Cleaning:** memperbaiki tipe data, menangani duplikasi, dan memeriksa nilai tidak valid.
2. **Target Preparation:** menggunakan `is_fraud` sebagai target klasifikasi biner.
3. **Feature Selection:** memilih fitur yang relevan dan tersedia pada saat transaksi diprediksi.
4. **Data Leakage Prevention:** mengeluarkan target, identifier yang tidak relevan, serta informasi pascatransaksi atau hasil investigasi.
5. **Missing Value Handling:** menangani nilai kosong sesuai karakteristik masing-masing fitur.
6. **Categorical Encoding:** mengubah fitur kategorikal ke format yang dapat diproses model.
7. **Numerical Scaling:** melakukan normalisasi atau standardisasi sesuai kebutuhan model.
8. **Class Imbalance Handling:** mempertimbangkan class weighting atau metode penanganan imbalance lainnya pada data training.
9. **Temporal Data Splitting:** membagi data berdasarkan fase waktu.

### Rencana Pembagian Data

| Dataset | Fase | Fungsi |
|---|---|---|
| Training | Phase 1 | Melatih model |
| Validation | Phase 2 | Pemilihan konfigurasi dan tuning model |
| Testing 1 | Phase 3 | Evaluasi pada kondisi peak drift |
| Testing 2 | Phase 4 | Evaluasi pada kondisi stabilisasi |

Pembagian dilakukan secara temporal, bukan random split. Seluruh proses preprocessing yang mempelajari parameter dari data hanya di-fit pada training set, kemudian diterapkan pada validation dan testing set.

---

## 5. Modelling

Penelitian membandingkan lima algoritma Machine Learning berikut:

| Model | Keterangan |
|---|---|
| Logistic Regression | Baseline model berbasis regresi logistik |
| Random Forest | Ensemble learning berbasis decision tree |
| XGBoost | Gradient boosting |
| LightGBM | Gradient boosting berbasis histogram |
| TabNet | Deep learning untuk data tabular |

TabNet akan diuji dalam dua konfigurasi:

- **TabNet Baseline:** model TabNet dengan konfigurasi awal.
- **Optimized TabNet:** model TabNet setelah hyperparameter optimization.

Optimasi TabNet dilakukan menggunakan **Optuna** untuk mencari konfigurasi hyperparameter yang memberikan performa terbaik pada validation set. Model pembanding digunakan sebagai baseline untuk mengetahui posisi performa Optimized TabNet terhadap algoritma lainnya.

---

## 6. Evaluation

Evaluasi dilakukan pada validation set dan dua periode testing untuk mengukur performa klasifikasi serta perubahan performa pada kondisi temporal yang berbeda.

### Metrik Evaluasi Utama

| Metrik | Tujuan |
|---|---|
| **PR-AUC** | Mengukur kualitas deteksi kelas fraud pada data yang tidak seimbang |
| **Precision** | Mengukur ketepatan prediksi transaksi fraud |
| **Recall** | Mengukur kemampuan model menemukan transaksi fraud |
| **F1-Score** | Mengukur keseimbangan precision dan recall |
| **ROC-AUC** | Mengukur kemampuan model membedakan kelas fraud dan non-fraud |
| **MCC** | Mengukur kualitas klasifikasi dengan mempertimbangkan seluruh komponen confusion matrix |

### Evaluasi Temporal

Performa setiap model dibandingkan pada:

- **Phase 2 (Validation):** evaluasi untuk pemilihan konfigurasi model.
- **Phase 3 (Peak Drift):** pengujian utama untuk melihat performa saat perubahan pola data paling tinggi.
- **Phase 4 (Stabilising):** pengujian lanjutan untuk melihat performa pada fase stabilisasi.

Hasil evaluasi digunakan untuk mengetahui model dengan performa deteksi fraud terbaik dan model yang paling mampu mempertahankan performanya ketika terjadi perubahan distribusi data.

---

## 7. Research Questions

Penelitian ini diarahkan untuk menjawab pertanyaan berikut:

1. Bagaimana performa Optimized TabNet dibandingkan Logistic Regression, Random Forest, XGBoost, dan LightGBM dalam mendeteksi transaksi fraud?
2. Apakah optimasi hyperparameter dapat meningkatkan performa TabNet dibandingkan TabNet Baseline?
3. Bagaimana perubahan temporal data drift memengaruhi performa setiap model?
4. Model mana yang memiliki performa paling baik dan paling stabil pada fase peak drift dan stabilising?

---

## 8. Referensi

1. [Credit Card Fraud Detection with Drift 2026 – Kaggle](https://www.kaggle.com/datasets/mohankrishnathalla/credit-card-fraud-detection-with-drift-2026)
2. [Dataset Documentation – GitHub](https://github.com/mohan13krishna/Credit-Card-Fraud-Detection-with-Drift-2026)
3. Arik, S. Ö., & Pfister, T. (2021). *TabNet: Attentive Interpretable Tabular Learning*. AAAI. [Paper](https://ojs.aaai.org/index.php/AAAI/article/view/16826)
4. Gama, J., et al. (2014). *A Survey on Concept Drift Adaptation*. ACM Computing Surveys. [DOI](https://doi.org/10.1145/2523813)
