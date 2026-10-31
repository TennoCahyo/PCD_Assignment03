# PCD Assignment 03 — Image Segmentation and Morphological Processing

Repositori ini berisi implementasi segmentasi citra grayscale menggunakan **Otsu Thresholding** dan pengolahan citra biner menggunakan operasi morfologi dasar maupun lanjutan. Implementasi algoritma inti dibuat secara manual menggunakan perulangan Python.

## Tujuan

1. Melakukan segmentasi citra grayscale menggunakan metode global thresholding Otsu.
2. Mengimplementasikan operasi morfologi biner: Dilation, Erosion, Opening, Closing, Hit-and-Miss Transform, Thinning, Thickening, dan Skeletonization.
3. Membandingkan hasil segmentasi sebelum dan sesudah pemrosesan morfologi secara visual dan berdasarkan jumlah piksel foreground.

## Teknologi dan Library

- **Python** — implementasi perhitungan dan operasi citra.
- **Pillow (PIL)** — membaca, mengonversi, mengubah ukuran, dan menyimpan citra.
- **Matplotlib** — menampilkan citra dan hasil perbandingan.

Perhitungan histogram, pencarian threshold Otsu, segmentasi biner, serta operasi morfologi inti diimplementasikan menggunakan list dan perulangan Python. Notebook tidak menggunakan library pengolahan citra seperti OpenCV untuk algoritma inti.

## Persiapan dan Cara Menjalankan

Notebook dirancang untuk dijalankan di **Google Colab**.

1. Buka file `PCD_Assignment03.ipynb` menggunakan Google Colab.
2. Upload citra yang akan diproses ke runtime Colab.
3. Pada bagian konfigurasi notebook, sesuaikan `IMAGE_PATH` dengan lokasi dan nama file citra yang di-upload. Contoh:

   ```python
   IMAGE_PATH = "/content/nama_gambar.jpg"
   ```

4. Periksa konfigurasi `FOREGROUND` dan parameter lainnya jika diperlukan.
5. Jalankan seluruh cell notebook secara berurutan dari awal hingga akhir.

Jika menjalankan notebook di lingkungan Python lokal/Jupyter, pastikan library yang diperlukan sudah terpasang:

```bash
pip install pillow matplotlib
```

Sesuaikan `IMAGE_PATH` dan `OUTPUT_DIR` dengan lokasi file pada komputer yang digunakan.

## Konfigurasi Utama

| Parameter | Nilai awal | Keterangan |
|---|---|---|
| `IMAGE_PATH` | `/content/20260327114112792_DZQX5PGY.jpg` | Lokasi file citra input. Sesuaikan dengan file yang di-upload. |
| `FOREGROUND` | `"bright"` | `"bright"` menjadikan piksel dengan intensitas lebih besar dari threshold sebagai foreground; `"dark"` menjadikan piksel dengan intensitas lebih kecil atau sama dengan threshold sebagai foreground. |
| `MAX_IMAGE_SIDE` | `600` | Membatasi sisi terpanjang citra menjadi maksimal 600 piksel. Gunakan `None` untuk mempertahankan ukuran asli. |
| `STRUCTURING_ELEMENT` | Matriks 3 × 3 berisi `1` | Structuring element untuk operasi morfologi dasar. |
| `MORPHOLOGY_SEQUENCE` | `["opening", "closing"]` | Urutan operasi yang digunakan untuk menghasilkan citra morfologi akhir. |
| `OUTPUT_DIR` | `/content` | Folder untuk menyimpan hasil citra. |

## Metode yang Diimplementasikan

### 1. Grayscale dan Otsu Thresholding

Citra input dibaca dan dikonversi menjadi grayscale dengan rentang intensitas 0–255. Histogram grayscale dihitung secara manual, kemudian nilai threshold dipilih menggunakan metode Otsu dengan memaksimalkan variansi antar-kelas.

Hasil threshold digunakan untuk membentuk citra biner yang terdiri dari foreground (`1`) dan background (`0`).

### 2. Operasi Morfologi Dasar

Operasi dasar menggunakan structuring element 3 × 3 dengan seluruh elemen aktif dan origin pada posisi tengah.

- **Dilation** — memperluas area foreground.
- **Erosion** — memperkecil area foreground.
- **Opening** — erosion yang dilanjutkan dengan dilation; dapat menghilangkan komponen foreground kecil.
- **Closing** — dilation yang dilanjutkan dengan erosion; dapat menutup celah atau lubang kecil.

Piksel di luar batas citra diperlakukan sebagai background (`0`).

### 3. Operasi Morfologi Lanjutan

- **Hit-and-Miss Transform** — mendeteksi pola foreground dan background secara bersamaan. Pattern menggunakan nilai `1` untuk foreground, `0` untuk background, dan `-1` untuk posisi *don't care*.
- **Thinning** — menipiskan objek menggunakan algoritma Zhang–Suen. Setiap iterasi utama terdiri dari dua sub-iterasi.
- **Thickening** — menebalkan objek dengan mengomplemenkan citra, menerapkan thinning pada komplemennya, lalu mengomplemenkan hasilnya kembali.
- **Skeletonization** — membentuk kerangka objek menggunakan pendekatan morphological skeleton melalui pengulangan erosion dan opening.

## Hasil Eksperimen

Hasil berikut merupakan keluaran dari konfigurasi dan citra input yang digunakan pada notebook.

### Segmentasi dan Morfologi Dasar

- Ukuran citra setelah penyesuaian: **600 × 384 piksel** (230.400 piksel).
- Mode foreground: **bright**.
- Threshold Otsu: **78**.
- Urutan morfologi akhir: **Opening → Closing**.

| Metrik | Sebelum morfologi | Sesudah morfologi |
|---|---:|---:|
| Jumlah piksel foreground | 103.677 | 101.236 |
| Persentase foreground | 45,00% | 43,94% |
| Jumlah piksel yang berubah | — | 5.025 (2,18%) |

### Operasi Morfologi Lanjutan

| Operasi | Jumlah piksel foreground | Persentase |
|---|---:|---:|
| Segmentasi awal | 103.677 | 45,00% |
| Hit-and-Miss | 1.066 | 0,46% |
| Thinning | 96.877 | 42,05% |
| Thickening | 109.452 | 47,51% |
| Skeletonization | 8.731 | 3,79% |

Pada hasil eksperimen ini, Thinning dijalankan dengan **1 iterasi utama** (dua sub-iterasi Zhang–Suen), Thickening menggunakan **1 iterasi**, sedangkan Skeletonization dijalankan hingga **43 iterasi** pada konfigurasi notebook.

Jumlah piksel menunjukkan perubahan area foreground, bukan ukuran akurasi segmentasi. Penilaian akurasi memerlukan citra referensi (*ground truth*) sebagai pembanding.

## File Hasil

Setelah seluruh cell dijalankan, notebook menyimpan tiga file berikut ke folder `OUTPUT_DIR`:

- `PCD_Assignment03_grayscale.png` — citra grayscale hasil persiapan.
- `PCD_Assignment03_segmentation_before.png` — hasil segmentasi biner sebelum morfologi.
- `PCD_Assignment03_segmentation_after.png` — hasil segmentasi setelah urutan morfologi diterapkan.

## Kesimpulan

Otsu Thresholding menghasilkan segmentasi biner secara otomatis berdasarkan histogram grayscale. Operasi morfologi dasar mengubah luas dan struktur foreground, sedangkan operasi lanjutan memiliki fungsi khusus untuk mendeteksi pola, menipiskan, menebalkan, dan membentuk kerangka objek. Eksperimen ini memperlihatkan bahwa setiap operasi memberikan perubahan sesuai karakteristiknya. Namun, perubahan jumlah piksel dan tampilan visual saja belum membuktikan peningkatan akurasi segmentasi karena evaluasi akurasi memerlukan *ground truth*.
