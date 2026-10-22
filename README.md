# Scraping Ulasan Google Play Store

Notebook Python untuk mengambil ulasan aplikasi dari Google Play Store menggunakan Google Colab dan menyimpan hasilnya sebagai CSV. Pengaturan dikumpulkan di satu bagian dengan petunjuk berbahasa Indonesia agar mudah digunakan.

**Notebook:** [scrapper_for_google_colab.ipynb](scrapper_for_google_colab.ipynb)

## Fitur

- Memilih aplikasi berdasarkan ID pada URL Play Store.
- Mengatur jumlah ulasan, bahasa, negara, dan urutan pengambilan.
- Mengambil data bertahap dengan tampilan progres.
- Menghindari duplikat berdasarkan `reviewId` dan membatasi hasil sesuai target.
- Mempertahankan hasil parsial saat terjadi error yang ditangkap atau interupsi pengguna.
- Menyimpan seluruh kolom hasil scraping beserta informasi asal data ke CSV.
- Membuat nama file otomatis sesuai ID aplikasi dan waktu UTC.

## Persiapan

Siapkan akun untuk menggunakan Google Colab, koneksi internet, dan URL halaman aplikasi yang ingin diambil ulasannya. Tidak perlu memasang Python di komputer atau menggunakan GPU.

Paket `google-play-scraper` dan `pandas` dipasang melalui sel pertama notebook.

## Cara menggunakan

### 1. Buka notebook di Colab

1. Unduh [scrapper_for_google_colab.ipynb](scrapper_for_google_colab.ipynb) dari repositori ini.
2. Buka [Google Colab](https://colab.research.google.com/).
3. Pilih **File → Upload notebook / Unggah notebook**, lalu pilih berkas tersebut.
4. Hubungkan notebook ke runtime jika diminta.

### 2. Jalankan sel secara berurutan

Klik tombol **▶** di sebelah kiri sel kode. Tunggu sel selesai sebelum melanjutkan.

| Langkah | Tindakan |
| --- | --- |
| 1. Pasang paket dan impor alat | Jalankan sekali setiap memulai sesi Colab baru. |
| 2. Pilih aplikasi dan jumlah ulasan | Ubah pengaturan sesuai kebutuhan, lalu jalankan sel. |
| 3. Ambil ulasan | Tunggu proses selesai dan baca status akhirnya. |
| 4. Lihat dan simpan hasil | Tampilkan lima baris pertama dan buat CSV. |
| 5. Unduh CSV | Simpan hasil ke komputer. |

**Mulai dengan 100 ulasan.** Setelah hasil sesuai, naikkan target menjadi 1.000 atau 10.000 pada langkah 2.

## Mengganti aplikasi

Buka halaman aplikasi di Play Store melalui browser. Salin teks setelah `id=` sampai sebelum `&`, jika ada.

Contoh URL:

```text
https://play.google.com/store/apps/details?id=com.whatsapp&hl=id
```

ID pada contoh tersebut adalah `com.whatsapp`. Pada langkah 2 notebook, ubah:

```python
APP_ID = "com.whatsapp"  # Ganti dengan ID aplikasi dari URL Play Store.
```

Masukkan **ID aplikasi**, bukan nama tampilan atau URL lengkap. Nilai awal notebook adalah `com.capitalx.blissfully`; sesuaikan sebelum menjalankan.

Setelah mengganti aplikasi, jalankan kembali **langkah 2–5**. Setiap pengambilan dimulai dari awal dan tidak digabung dengan hasil sebelumnya. Nama CSV menyesuaikan ID aplikasi secara otomatis.

## Pengaturan

Contoh konfigurasi pada langkah 2:

```python
APP_ID = "com.whatsapp"     # GANTI dengan ID aplikasi yang dituju.
JUMLAH_ULASAN = 100         # GANTI target, misalnya 1000 atau 10000.
BAHASA = "id"               # "id": bahasa Indonesia; "en": bahasa Inggris.
NEGARA = "id"               # "id": wilayah Indonesia; "us": Amerika Serikat.
URUTAN = Sort.NEWEST        # Pilihan lain: Sort.MOST_RELEVANT.

UKURAN_BATCH = 200          # Jumlah per pemanggilan; rentang 1–200.
JEDA_DETIK = 1.0            # Jeda antarpemanggilan; rentang 0–60 detik.
```

| Pengaturan | Fungsi | Nilai awal notebook |
| --- | --- | --- |
| `APP_ID` | Aplikasi sumber ulasan. | `"com.capitalx.blissfully"` |
| `JUMLAH_ULASAN` | Batas maksimum ulasan unik yang dikumpulkan. | `100` |
| `BAHASA` | Bahasa permintaan. | `"id"` |
| `NEGARA` | Wilayah permintaan. | `"id"` |
| `URUTAN` | Ulasan terbaru atau paling relevan. | `Sort.NEWEST` |
| `UKURAN_BATCH` | Ukuran pengambilan per pemanggilan. | `200` |
| `JEDA_DETIK` | Jeda untuk mengurangi laju permintaan. | `1.0` |

`JUMLAH_ULASAN` adalah target, bukan jaminan jumlah hasil. Pengaturan `en` dan `us` bukan mode “seluruh dunia”, dan pilihan negara tidak membuktikan negara asal penulis ulasan.

## Hasil CSV

CSV disimpan tanpa kolom indeks tambahan dengan encoding `utf-8-sig` (UTF-8 dengan BOM). Nama file mengikuti pola:

```text
ulasan_<APP_ID>_<tanggal>_<waktu>_<mikrodetik>_UTC.csv
ulasan_<APP_ID>_<tanggal>_<waktu>_<mikrodetik>_UTC_parsial.csv
```

Penanda `_parsial` digunakan ketika proses berhenti sebelum status “Target tercapai”. Ini tidak selalu berarti terjadi error; data yang tersedia untuk permintaan tersebut mungkin lebih sedikit daripada target. Waktu UTC pada nama file menunjukkan waktu penyimpanan, bukan zona waktu tanggal ulasan.

Seluruh kolom yang dikembalikan paket dipertahankan. Beberapa kolom penting:

| Kolom | Isi |
| --- | --- |
| `reviewId` | ID unik ulasan; digunakan untuk menghindari duplikat. |
| `content` | Teks ulasan. |
| `score` | Nilai bintang ulasan. |
| `at` | Tanggal/waktu ulasan dari sumber. |
| `thumbsUpCount` | Jumlah tanda membantu. |
| `app_id` | ID aplikasi saat pengambilan. |
| `bahasa` | Bahasa permintaan saat pengambilan. |
| `negara` | Wilayah permintaan saat pengambilan. |
| `urutan` | Metode pengurutan yang dipakai. |
| `status_proses` | Alasan proses selesai atau berhenti. |

Jika tidak ada ulasan, CSV tidak dibuat. File yang dibuat berada di runtime Colab; unduh melalui langkah 5 agar tersimpan di komputer.

## Menghentikan proses

Tekan tombol stop pada sel pengambilan. Jika interupsi berhasil ditangkap, lanjutkan ke langkah 4 dan 5 untuk menyimpan data yang sudah terkumpul.

Hasil parsial berada di memori sampai langkah 4 dijalankan. Memutus atau mereset runtime sebelum menyimpan dapat menghilangkan hasil. Notebook belum menyediakan fitur melanjutkan pengambilan dari CSV sebelumnya.

## Pemecahan masalah

| Masalah | Langkah yang dapat dicoba |
| --- | --- |
| `NameError` atau variabel belum dikenal | Jalankan sel berurutan mulai langkah 1. |
| Paket belum tersedia | Jalankan ulang langkah 1 dan pastikan instalasi selesai. |
| Hasil kosong | Periksa ID aplikasi, koneksi, bahasa, negara, dan pesan langkah 3. |
| Hasil kurang dari target | Baca `status_proses`; respons kosong atau tidak adanya halaman berikutnya belum membuktikan semua ulasan sudah diperoleh. |
| Tidak ada ulasan baru dalam satu batch | Proses berhenti untuk menghindari pengulangan data. Periksa hasil yang sudah terkumpul. |
| Unduhan tidak muncul | Buka panel **Files / File** di kiri Colab, lalu pilih **Download / Unduh** pada CSV. |
| Ingin mengganti aplikasi | Ubah `APP_ID`, lalu jalankan kembali langkah 2–5. |
| CSV tampil sebagai satu kolom di Excel | Impor sebagai Text/CSV, lalu pilih pemisah koma dan encoding UTF-8. |

## Batasan

- Hasil bukan jaminan seluruh ulasan aplikasi; cakupannya bergantung pada permintaan dan respons yang tersedia.
- Proses berhenti saat target tercapai, respons kosong, penanda halaman berikutnya habis, atau satu batch tidak menambah ID ulasan baru.
- Paket dapat menangani sebagian kegagalan secara internal. Respons kosong tidak selalu berarti tidak ada ulasan.
- Jeda antarpemanggilan mengurangi laju permintaan, tetapi tidak menjamin permintaan berhasil.
- Perubahan Google Play atau paket dapat memengaruhi hasil.
- Notebook melakukan deduplikasi ID, tetapi belum melakukan pembersihan teks atau analisis sentimen.

## Referensi

- [Dokumentasi google-play-scraper](https://github.com/JoMingyu/google-play-scraper)
- [Implementasi pengambilan ulasan](https://github.com/JoMingyu/google-play-scraper/blob/master/google_play_scraper/features/reviews.py)
