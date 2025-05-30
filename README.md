**Laporan Proyek Machine Learning - Sistem Rekomendasi Buku**

**Nama:** [Fina Alfianti]

### **1. Project Overview**

Sistem rekomendasi menjadi komponen penting dalam era digital saat ini, khususnya dalam bidang e-commerce dan platform literasi digital. Dengan banyaknya pilihan buku yang tersedia, pengguna kesulitan menemukan bacaan yang sesuai dengan preferensi mereka. Oleh karena itu, dibutuhkan sistem rekomendasi yang mampu menyarankan buku yang relevan dengan selera pengguna.

Pendekatan awal dalam pengembangan sistem rekomendasi adalah collaborative filtering, seperti yang diperkenalkan oleh Goldberg et al. (1992), yang bekerja berdasarkan perilaku pengguna lain. Namun, pendekatan ini memiliki keterbatasan dalam hal cold-start dan kebutuhan akan data interaksi pengguna yang besar. Untuk mengatasi hal tersebut, pendekatan content-based filtering mulai banyak digunakan, di mana sistem memberikan rekomendasi berdasarkan kemiripan konten item yang pernah disukai pengguna (Ricci et al., 2015).

Tujuan dari proyek ini adalah membangun sistem rekomendasi buku berbasis content-based filtering dengan menggunakan data Book-Crossing. Sistem ini diharapkan dapat memberikan rekomendasi buku kepada pengguna berdasarkan konten atau atribut buku yang pernah pembaca sukai.

### **2. Business Understanding**

#### **Problem Statements**

1. Bagaimana cara merekomendasikan buku yang relevan kepada pengguna berdasarkan buku yang mereka sukai?
2. Bagaimana mengukur kemiripan antar buku berdasarkan kontennya?

#### **Goals**

1. Mengembangkan sistem rekomendasi buku yang memberikan rekomendasi top-N berdasarkan histori preferensi pengguna.
2. Mengimplementasikan metode *content-based filtering* menggunakan TF-IDF dan cosine similarity.

#### **Solution Approach**

* Menggunakan pendekatan *content-based filtering* berbasis TF-IDF terhadap metadata buku (judul, penulis, penerbit, tahun, URL).
* Mengukur kemiripan antar buku menggunakan cosine similarity.

### **3. Data Understanding**

Dataset yang digunakan adalah Book-Crossing dataset yang terdiri dari tiga file utama:

* **Books.csv**: Informasi metadata buku
* **Ratings.csv**: Rating yang diberikan oleh pengguna terhadap buku
* **Users.csv**: Data demografis pengguna

Dataset dapat diunduh di [Book Recommendation Dataset](https://www.kaggle.com/datasets/arashnic/book-recommendation-dataset)

#### **Fitur-Fitur Data:**

* **Books**:

  * ISBN
  * Book-Title
  * Book-Author
  * Year-Of-Publication
  * Publisher
  * Image-URL-S/M/L
* **Ratings**:

  * User-ID
  * ISBN
  * Book-Rating
* **Users**:

  * User-ID
  * Location
  * Age

### **4. Data Preparation**

Pada tahap ini, dilakukan serangkaian proses *data preparation* untuk memastikan data siap digunakan dalam pengembangan sistem rekomendasi berbasis content-based filtering. Tahapan ini sangat penting untuk meningkatkan kualitas representasi konten buku serta efisiensi pemrosesan model.

#### **Langkah-langkah Data Preparation:**

1. **Menghapus Missing Values**
   Dilakukan pengecekan dan penghapusan data yang memiliki nilai kosong (null) pada ketiga dataset (Books, Ratings, Users).
   Tujuannya agar data yang tidak lengkap dapat menyebabkan error saat penggabungan dan menurunkan kualitas input fitur.

2. **Menggabungkan Fitur-Fitur Buku**
   Kolom seperti `Book-Title`, `Book-Author`, `Publisher`, `Year-Of-Publication`, dan `Image-URL` digabung menjadi satu kolom teks deskriptif untuk setiap buku.
   Tujuan penggabungan ini digunakan sebagai input untuk TF-IDF agar vektor fitur dapat merepresentasikan konten buku secara menyeluruh.

3. **Sampling Buku Populer**
   Diambil 1000 buku yang paling banyak menerima rating dari pengguna.
   Tujuannya untuk meningkatkan efisiensi komputasi, terutama saat menghitung cosine similarity antar item.

4. **Join Dataset Rating dan Buku**
   Dataset `Ratings` digabungkan dengan metadata dari `Books` berdasarkan kolom `ISBN`.
   Tujuannya untuk mengetahui buku mana saja yang telah dirating oleh pengguna serta mempermudah proses identifikasi buku favorit user.

5. **Preprocessing Teks**
   Seluruh teks judul buku dikonversi ke huruf kecil (lowercase). Selain itu, karakter non-alfabet dibersihkan jika diperlukan.
   Tujuannya agar normalisasi teks membantu menghindari duplikasi dan meningkatkan akurasi perhitungan kemiripan teks.

### **5. Modeling**

Tahap ini membahas pembuatan model sistem rekomendasi berdasarkan pendekatan content-based filtering. Selain itu, diberikan juga alternatif pendekatan dan analisis kelebihan serta kekurangannya.

#### **Model 1: Content-Based Filtering (TF-IDF + Cosine Similarity)**

* **TF-IDF Vectorization**: Mengubah konten buku menjadi vektor fitur dengan menghilangkan stop words.
* **Cosine Similarity**: Mengukur kemiripan antar vektor buku untuk menentukan buku yang mirip.

##### **Rekomendasi Top-N**

Fungsi utama:

* Mengambil semua rating user.
* Menentukan buku favorit (rating >= 6).
* Menghitung rata-rata similarity antar buku favorit.
* Mengambil N buku dengan skor similarity tertinggi yang belum dirating.

Contoh:

```python
recommend_for_user(user_id=278418, num_recommendations=5)
```

#### **Model 2: Popularity-Based Recommendation (Baseline)**

* Menghitung rata-rata rating tertinggi dari seluruh buku.
* Menyajikan daftar buku terpopuler secara umum sebagai rekomendasi.

##### **Kelebihan dan Kekurangan:**

| Pendekatan              | Kelebihan                                    | Kekurangan                                                |
| ----------------------- | -------------------------------------------- | --------------------------------------------------------- |
| Content-Based Filtering | Personal dan relevan dengan histori pengguna | Tidak bisa merekomendasikan item baru yang belum dirating |
| Popularity-Based        | Sederhana dan cepat                          | Tidak personal, bisa bias ke buku lama                    |

### **6. Evaluation**

Untuk evaluasi sistem rekomendasi berbasis konten, metrik yang digunakan adalah:

#### **1. Precision\@N**

Mengukur berapa banyak dari N rekomendasi yang relevan.

#### **2. Coverage**

Berapa persen dari item yang bisa direkomendasikan ke user.

#### **3. Qualitative Analysis**

Melihat apakah rekomendasi sesuai secara logis dengan buku favorit user.

Karena data rating tidak memiliki label eksplisit untuk relevansi, maka evaluasi dilakukan secara manual dan melalui contoh rekomendasi dari pengguna aktif untuk memverifikasi relevansi konten.

### **Penutup**

Sistem rekomendasi berbasis konten dapat memberikan hasil yang baik untuk pengguna dengan preferensi jelas, tanpa membutuhkan interaksi banyak pengguna lain. Namun, sistem ini juga memiliki keterbatasan dalam menangani cold start dan diversifikasi rekomendasi.

Pengembangan selanjutnya dapat menggabungkan pendekatan hybrid yang memanfaatkan collaborative filtering atau eksplisit fitur demografis pengguna untuk menghasilkan hasil yang lebih personal dan akurat.
