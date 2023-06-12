<h1>MLlib with Spark (menggunakan Python di google collab)</h1>
<h2>01. Movie Lens Recommendations</h2>
<hr/>
<h3>Kode</h3>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/MLlib%20with%20Spark/code/01_example.png")>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/MLlib%20with%20Spark/code/02_example.png")>
<p>Kode di atas mengimplementasikan sistem rekomendasi menggunakan metode ALS (Alternating Least Squares) dalam Apache Spark. Pertama, data rating film dari file "ratings.dat" dibaca dan diproses untuk dibuat menjadi dataframe. Selanjutnya, data dibagi menjadi set pelatihan dan pengujian. Model rekomendasi kemudian dibangun menggunakan ALS pada data pelatihan. Setelah model terlatih, prediksi rating dilakukan pada data pengujian. Evaluasi performa model dilakukan dengan menghitung RMSE (Root Mean Squared Error) untuk memperoleh tingkat akurasi model. Prediksi rating juga disimpan dalam file "ml-predictions" karena menggunakan google collab.</p>
<hr/>
<hr/>
<h3>Hasil Awal</h3>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/MLlib%20with%20Spark/img/01_Movie%20Lens%20Recommendation.png")>
<h3>Hasil Jika ditanya max id</h3>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/MLlib%20with%20Spark/img/02_Movie%20Lens%20Recommendation%20Max%20User%20Id.png")>
<hr/><br>
<h2>02. Movie Lens Recommendation Ver.2</h2>
<hr/>
<h3>Kode dan Hasil</h3>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/MLlib%20with%20Spark/img/03_Movie%20Lens%20Recommendation%20ver%202.png")>
<p>Kode di atas menggunakan metode ALS (Alternating Least Squares) dalam Apache Spark untuk membangun sistem rekomendasi film.</p>
<hr/><br>
<h2>03. Basic Statistic Summary</h2>
<hr/>
<h3>Kode dan Hasil</h3>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/MLlib%20with%20Spark/img/04_Basic%20Statistic%20Summary.png")>
<p>Kode di atas menggunakan Apache Spark untuk menghitung statistik dari sebuah data. Data yang terdiri dari tiga baris dan tiga kolom. Kemudian, fungsi colStats dari modul Statistics digunakan untuk menghitung statistik seperti rata-rata, variansi, dan jumlah nilai non-nol dari setiap kolom dalam matriks.</p>
<hr/><br>
<h2>04. KMeans Model Example</h2>
<p>Contoh menggunakan Apache Spark untuk melakukan analisis clustering dengan algoritma K-Means dengan bahasa python.</p>
<hr/>
<h3>Kode dan Hasil</h3>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/MLlib%20with%20Spark/img/05_KMeans%20Model%20Example.png")>
<hr/>
