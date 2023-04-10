<h2>Kode 1 : Accumulator</h2>
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
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/accumulator_azmi.png")>

<h2>Kode 2 : Broadcast</h2>
<p>Memungkinkan variabel hanya dibaca (read-only) yang telah disimpan (cached) pada setiap mesin pekerja, alih-alih mengirimkannya bersama setiap tugas.</p><br>

    // Contoh code Broadcast
    broadcastVar = sc.broadcast(list(range(1, 100)))
    broadcastVar.value

<br><p>Berikut adalah penjelasan kodenya : <br>
 1. broadcast = suatu fitur yang digunakan untuk mendistribusikan variabel secara efisien ke seluruh node dalam cluster.<br>
 2. list = salah satu jenis struktur data yang digunakan untuk menyimpan kumpulan elemen atau nilai.<br>
 3. range = salah satu struktur data yang digunakan untuk menyimpan kumpulan nilai atau elemen yang terdiri dari serangkaian nilai berturut-turut.<br>
</p>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/broadcast_azmi.png">

<h2>Kode 3 : Log Analytic</h2>
<p>Memungkinkan variabel hanya dibaca (read-only) yang telah disimpan (cached) pada setiap mesin pekerja, alih-alih mengirimkannya bersama setiap tugas.</p><br>

    // Contoh code Log Analytic
    # Get the lines from the textfile, create 4 partitions
    access_log = sc.textFile("path/folder/anda", 4)

    #Filter Lines with ERROR only
    error_log = access_log.filter(lambda x: "ERROR" in x)

    # Cache error log in memory
    cached_log = error_log.cache()

    # Now perform an action -  count
    print "Total number of error records are %s" % (cached_log.count())

    # Now find the number of lines with 
    print "Number of product pages visited that have Errors is %s" % (cached_log.filter(lambda x: "product" in x).count())

<br>
<p>Berikut adalah penjelasan kodenya : <br>
 1. textFile = alah satu metode untuk membaca data dari file teks pada sistem file distribusi Hadoop. Metode ini digunakan untuk membaca file teks dalam format yang umum digunakan seperti CSV, TSV, JSON, XML, dan lainnya.<br>
 2. filter = operasi transformasi yang digunakan untuk memfilter RDD (Resilient Distributed Dataset) berdasarkan kriteria tertentu.<br>
 3. cache = operasi yang digunakan untuk menyimpan RDD (Resilient Distributed Dataset) di memori pada setiap node dalam cluster.<br>
 4. count = operasi yang digunakan untuk menghitung jumlah elemen pada RDD (Resilient Distributed Dataset) yang diberikan.<br>
</p>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/LogAnalitic_azmi.png">

<h2>Kode 4 : Pair RDD</h2>
<br>

    // Contoh code Pair RDD
    mylist = ["my", "pair", "rdd"]
    myRDD = sc.parallelize(mylist)
    myPairRDD = myRDD.map(lambda s: (s, len(s)))
    myPairRDD.collect()
    myPairRDD.keys().collect()
    myPairRDD.values().collect()

<br>
<p>Berikut adalah penjelasan kodenya : <br>
 1. map = operasi yang digunakan untuk mengubah setiap elemen dalam RDD (Resilient Distributed Dataset) menjadi nilai baru dengan menggunakan sebuah fungsi tertentu.<br>
 2. collect = operasi yang digunakan untuk mengumpulkan seluruh elemen dari RDD (Resilient Distributed Dataset) dan mengembalikannya sebagai sebuah array pada driver program.<br>
 3. len = digunakan untuk menghitung jumlah elemen dalam sebuah list atau array.<br>
 4. keys = metode pada tipe data Map  yang digunakan untuk mengembalikan semua kunci (key) yang ada dalam Map tersebut.<br>
 5. values = digunakan untuk mengembalikan semua nilai (value) yang ada dalam Map tersebut.<br>
</p>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/PairRDD_azmi.png">

<h2>Kode 5 : Understanding RDDs</h2>
<p>Adalah unit dasar data dalam Spark dan pemrograman Spark berputar di sekitar pembuatan dan proses operasi pada RDDs. RDD merupakan kumpulan data yang tidak dapat diubah (immutable) dan terbagi menjadi beberapa partisi di dalam klaster yang dapat direkonstruksi (re-computed) kembali jika salah satu partisinya hilang.</p><br>

    // Contoh code Understanding RDDs
    # Check Default Parallelism
    sc.defaultParallelism

    #Let's create a list, parallelize it and let's check the number of partitions. 
    myList = ["big", "data", "analytics", "hadoop" , "spark"]
    myRDD = sc.parallelize(myList)
    myRDD.getNumPartitions()
  
    #To override the default parallelism, provide specific number of partitions needed while creating the RDD. In this case let's create the RDD with 6 partitions.
    myRDDWithMorePartitions = sc.parallelize(myList,6)
    myRDDWithMorePartitions.getNumPartitions()
 
    #Let's issue an action to count the number of elements in the list.
    myRDD.count()

    #Display the data in each partition
    myRDD.mapPartitionsWithIndex(lambda index,iterator: ((index, list(iterator)),)).collect()

    #Increase number of partitions and display contents
    mySixPartitionsRDD = myRDD.repartition(6)
    mySixPartitionsRDD.mapPartitionsWithIndex(lambda index,iterator: ((index, list(iterator)),)).collect()

    #Decrease number of partitions and display contents
    myTwoPartitionsRDD = mySixPartitionsRDD.coalesce(2)
    myTwoPartitionsRDD.mapPartitionsWithIndex(lambda index,iterator: ((index, list(iterator)),)).collect()

    # Check Lineage Graph
    print myTwoPartitionsRDD.toDebugString()

<br>
<p>Berikut adalah penjelasan kodenya : <br>
 1. defaultParallelism = suatu konfigurasi yang ditentukan oleh SparkContext saat pertama kali diinisialisasi.<br>
 2. getNumPartitions = digunakan untuk mengembalikan jumlah partisi (partition) pada sebuah RDD.<br>
 3. mapPartitionsWithIndex = digunakan untuk melakukan transformasi data pada setiap partisi RDD dengan mempertahankan indeks partisi. Metode ini mirip dengan mapPartitions tapi dengan adanya argumen indeks partisi pada setiap pemanggilan fungsi transformasi.<br>
 4. repartition = digunakan untuk mengubah jumlah partisi RDD menjadi jumlah yang baru tanpa mengubah nilai-nilai pada RDD.<br>
 5. coalesce = digunakan untuk mengurangi jumlah partisi RDD menjadi jumlah yang baru tanpa melakukan shuffle data secara besar-besaran.<br>
 6. toDebugString = digunakan untuk mencetak informasi debug terkait partisi RDD, seperti lokasi partisi, ukuran partisi.<br>
</p>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/UnderstandingRDDs_azmi.png">

<h2>Kode 6 : Word Count</h2>
<p>Menghitung setiap jumlah kata yang ada pada file / text.</p><br>

    // Contoh code Word Count
    from operator import add
    lines = sc.textFile("/path/to/README.md")
    counts = lines.flatMap(lambda x: x.split(' ')) \
                  .map(lambda x: (x, 1)) \
                  .reduceByKey(add)
    output = counts.collect()
    for (word, count) in output:
        print("%s: %i" % (word, count))

<br>
<p>Berikut adalah penjelasan kodenya : <br>
 1. flatMap = digunakan untuk mentransformasi sebuah RDD dengan mengaplikasikan sebuah fungsi ke setiap elemen RDD, kemudian mengembalikan nol atau banyak output untuk setiap elemen input.<br>
 2. reduceByKey = digunakan untuk melakukan agregasi pada pasangan key-value.<br>
 3. split = digunakan untuk memisahkan sebuah string menjadi sebuah array of string dengan memanfaatkan sebuah pemisah yang ditentukan.<br>
</p>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/WordCount1_azmi.png">
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/WordCount2_azmi.png">


<h2>SystemCommandsOutput</h2>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/SystemCommandsOutput_azmi.png">

<h2>SystemCommandsReturnCode</h2>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/SystemCommandsReturnCode_azmi.png">

