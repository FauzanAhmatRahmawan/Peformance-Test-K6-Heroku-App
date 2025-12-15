# Peformance-Test-K6-Heroku-App
Project sederhana untuk pengujian peforma website Heroku APP pada endpoint login menggunakan K6
# 🚀 Performance Test Report: [Heroku APP Load Test]

Ini adalah laporan hasil pengujian kinerja (Load Test) yang dilakukan pada layanan website Heroku APP pada endpoint login menggunakan Grafana k6.

Tujuan pengujian ini adalah untuk mengukur keandalan (reliability) dan waktu respons (latency) layanan di bawah beban hingga 20 Virtual User (VU).

## 🛠️ Tools dan Metodologi

* **Load Testing Tool:** Grafana k6
* **Skenario:** Stages Model (Ramp-up, Sustained Load, dan Ramp-down)
* **Metrik Fokus:** HTTP Request Duration (Latency), Request Rate (Throughput), dan Error Rate.

## 🎯 Skenario Pengujian (Load Test Scenario)

Skenario pengujian dirancang untuk mensimulasikan peningkatan beban secara bertahap, menahannya pada beban puncak, lalu mengakhirinya. 

| Tahap (Stage) | Target VUs | Durasi | Deskripsi |
| :--- | :--- | :--- | :--- |
| **Ramp-up** | Naik dari 0 ke **20 VUs** | 1 menit | Peningkatan beban awal. |
| **Sustained Load** | Tetap pada **20 VUs** | 3 menit 30 detik | Menguji stabilitas sistem di bawah beban puncak. |
| **Ramp-down** | Turun dari 20 ke **0 VUs** | 1 menit | Mengakhiri pengujian secara bertahap. |
| **Total Durasi** | | **5 menit 30 detik** | |

## ✅ Executive Summary

Pengujian menunjukkan bahwa layanan **sangat stabil dan handal** di bawah beban 20 Virtual User (VU).

* **Success Rate Total:** **100.0%** (Semua `checks` lulus dan tidak ada kegagalan HTTP).
* **Throughput (Puncak):** Sistem berhasil menangani rata-rata $\mathbf{15.5 \text{ requests/s}}$.
* **Waktu Respons Rata-rata (Avg Latency):** **550ms**.
* **Waktu Respons P95:** **977ms** (95% dari permintaan diselesaikan dalam waktu kurang dari 1 detik).

Meskipun sistem menunjukkan *Success Rate* sempurna, terdapat beberapa **lonjakan waktu respons** sporadis (hingga 9 detik) selama periode *sustained load* yang perlu diselidiki.

## 📊 Analisis Metrik Utama

Berikut adalah analisis detail dari metrik yang didapatkan selama pengujian:

### 1. Keandalan dan Skalabilitas (Reliability & Scalability)

| Metrik | Nilai | Status | Keterangan |
| :--- | :--- | :--- | :--- |
| **`http_req_failed`** | **0.00%** | **PASS** | Tidak ada kegagalan HTTP (kode status 4xx/5xx) yang tercatat. |
| **`checks`** | **100.0%** | **PASS** | Semua kriteria validasi (misalnya, status respons 200) terpenuhi. |
| **`http_reqs` (Total)** | **5.1K** | | Total permintaan yang berhasil diproses oleh sistem. |
| **`data_received`** | **23.7 MB** | | Data yang diterima sistem (throughput). |

### 2. Waktu Respons (Latency)

Metrik `http_req_duration` menunjukkan kinerja kecepatan layanan secara keseluruhan. 

| Persentil | Nilai | Analisis |
| :--- | :--- | :--- |
| **Average (Avg)** | **550ms** | Waktu respons rata-rata yang sangat baik, menunjukkan kecepatan standar layanan. |
| **Median (Med)** | **395ms** | Setengah dari permintaan diselesaikan dalam waktu kurang dari 395ms. |
| **p95** | **977ms** | **Konsistensi Baik.** 95% dari seluruh permintaan selesai di bawah 1 detik (target umum untuk aplikasi web yang baik). |
| **p99** | **2s** | **Terindikasi Lonjakan.** 99% dari permintaan selesai di bawah 2 detik. |
| **Maximum (Max)** | **5s** | Terdapat *outlier* yang mencapai 5 detik, yang menyebabkan lonjakan p99, namun tidak mempengaruhi performa secara mayoritas. |

### 3. Breakdown Latency (Fokus Peningkatan)

Analisis komponen durasi menunjukkan di mana waktu paling banyak dihabiskan:

| Komponen | Waktu Rata-rata (avg) | Persentase Durasi Total |
| :--- | :--- | :--- |
| **`http_req_waiting`** | **547ms** | ~99.5% |
| **`http_req_receiving`** | **2ms** | ~0.3% |
| **`http_req_sending`** | **50µs** | ~0.0% |

> **Insight:** Mayoritas waktu respons (**547ms**) dihabiskan untuk **menunggu respons dari server**. Ini mengindikasikan bahwa *bottleneck* utama terletak pada *server processing time* (waktu yang dibutuhkan server untuk memproses permintaan) atau *backend query time*, bukan pada transmisi data di jaringan.

---

## 📈 Visualisasi & Analisis Grafik

#### A. Stabilitas Latency
Grafik durasi permintaan menunjukkan bahwa selama fase *Sustained Load*, p95 secara umum stabil di bawah 1 detik, namun terdapat lonjakan mencolok yang mencapai 9 detik pada beberapa titik. Ini mungkin menandakan adanya *garbage collection* atau *resource exhaustion* sesaat di sisi server yang perlu diinvestigasi.

#### B. Pemanfaatan VUs
Grafik VUs (Virtual Users) menunjukkan implementasi skenario yang berhasil, dengan VUs mencapai 20 dan stabil selama periode 3 menit 30 detik. 

## 💡 Rekomendasi Tindak Lanjut

1.  **Optimasi Server:** Fokuskan upaya optimasi pada *backend* (API) untuk mengurangi **`http_req_waiting`** yang merupakan komponen latensi terbesar.
2.  **Investigasi Outliers:** Lakukan *profiling* server pada saat terjadi lonjakan latensi (di atas 5 detik) untuk mengidentifikasi penyebab *bottleneck* sesaat.
