# Segmentasi Citra Glioma

Proyek ini berisi dua notebook untuk segmentasi citra glioma dan evaluasi hasil segmentasi terhadap mask reference binary manual.

## Struktur Folder

```text
.
├── Glioma/
│   └── citra glioma asli
├── Reference/
│   └── mask reference binary manual
├── Output/
│   ├── KMeans/
│   ├── FCM/
│   ├── GMM/
│   ├── Overlay/
│   └── Metrics/
├── 01_segmentasi_glioma.ipynb
├── 02_evaluasi_metrik_segmentasi.ipynb
├── README.md
└── LICENSE
```

## Instalasi Dependency

Gunakan Python 3.10 atau lebih baru, lalu install dependency berikut:

```bash
pip install numpy pandas matplotlib pillow scipy scikit-learn scikit-image jupyter
```

## Notebook 1: Segmentasi

Jalankan `01_segmentasi_glioma.ipynb` untuk memproses semua citra di folder `Glioma`.

Metode yang digunakan:

- K-Means Clustering
- Fuzzy C-Means Clustering
- Gaussian Mixture Models

Sebelum clustering, notebook melakukan skull stripping sederhana menggunakan head mask dan distance transform. Setelah clustering, hanya connected component yang paling menyerupai tumor yang dipertahankan berdasarkan ukuran, intensitas, compactness, dan jarak dari border/skull.

Output yang dibuat:

- `Output/KMeans/<nama_file>.png`
- `Output/FCM/<nama_file>.png`
- `Output/GMM/<nama_file>.png`
- `Output/Overlay/<nama_file>_<metode>_overlay.png`

Mask hasil segmentasi disimpan sebagai citra binary dengan foreground bernilai putih dan background bernilai hitam.

## Notebook 2: Evaluasi Metrik

Sebelum menjalankan `02_evaluasi_metrik_segmentasi.ipynb`, buat mask reference binary secara manual menggunakan Fiji/ImageJ dan simpan di folder `Reference`.

Ketentuan reference:

- Nama dasar file harus sama dengan file citra asli.
- Contoh: `Glioma/Te-gl_115.jpg` dapat dipasangkan dengan `Reference/Te-gl_115.jpg` atau `Reference/Te-gl_115.png`.
- Area glioma harus putih, background harus hitam.

Metrik yang dihitung:

- Pixel Accuracy
- Dice Coefficient
- Intersection over Union (IoU)

Output evaluasi:

- `Output/Metrics/segmentation_metrics.csv`
- `Output/Metrics/summary_metrics.csv`
- `Output/Metrics/metrics_comparison.png`
- `Output/Metrics/missing_predictions.csv` jika ada hasil segmentasi yang belum ditemukan

## Parameter Penting

Parameter utama berada di bagian konfigurasi notebook segmentasi:

- `N_CLUSTERS`: jumlah cluster untuk K-Means, FCM, dan GMM.
- `CLUSTER_SELECTION`: strategi memilih cluster sebagai area glioma. Default `tumor_like`.
- `USE_CLAHE`: mengaktifkan peningkatan kontras lokal.
- `USE_SPATIAL_FEATURES`: menambahkan fitur koordinat piksel untuk clustering.
- `SKULL_STRIP_EROSION_RATIO`: besar erosi head mask untuk menghilangkan tengkorak.
- `MAX_TUMOR_COMPONENTS`: jumlah connected component tumor yang dipertahankan.
- `MIN_COMPONENT_SOLIDITY` dan `MIN_COMPONENT_EXTENT`: filter bentuk agar struktur bercabang/jaringan sehat tidak ikut masuk.

Jika lesi pada citra tampak lebih gelap daripada jaringan sekitarnya, ubah `CLUSTER_SELECTION` dari `tumor_like` menjadi `darkest`, tetapi gunakan ini hanya sebagai pembanding karena mode `darkest` tidak memakai scoring komponen tumor.

## Lisensi

Kode ini menggunakan lisensi MIT. Lihat file `LICENSE`.
