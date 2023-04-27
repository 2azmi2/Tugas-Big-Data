<html>
  <head>
  </head>
  <body>
    <h1>Chapter 4</h1>
    <h2>Pengantar</h2>
    <p>Memulai spark dan mengakses "http://quickstart.cloudera:8080/" </p>
    <hr/>

      // Contoh code accumulator
      myaccum = sc.accumulator(0)
      myrdd = sc.parallelize(range(1,100))
      myrdd.foreach(lambda value: myaccum.add(value))
      print myaccum.value

    <hr/>
    <img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/00_Pengantar/00Quickstart%208080.png")>

    <h2>Analitik Dengan Dataframe</h2>
    <p>Mengizinkan tugas untuk menulis data ke variabel bersama alih-alih memiliki variabel terpisah untuk setiap tugas. </p><br>

          // Contoh code accumulator
          myaccum = sc.accumulator(0)
          myrdd = sc.parallelize(range(1,100))
          myrdd.foreach(lambda value: myaccum.add(value))
          print myaccum.value

    <br><p>Berikut adalah penjelasan kodenya : <br>
     1. sc = berguna untuk membuat suatu object dari spark context.<br>
     2. accumulator = suatu variabel yang hanya bisa ditambahkan dengan operasi matematika yang memiliki sifat asosiatif dan komutatif (penjumlahan, pengurangan, perkalian, dan pembagian)<br>
     3. parallelize = metode pada spark context yang digunakan untuk membuat sebuah kumpulan data yang terdistribusi.<br>
     4. lambda = digunakan untuk melakukan pemetaan data atau transformasi data pada RDD (Resilient Distributed Dataset).<br>
     5. value = sebuah variabel yang menyimpan suatu nilai atau objek yang digunakan dalam proses pemrosesan data.<br>
    </p>

  </body>
</html>
