<h2>Stateless Streaming</h2>
<p>Stateless Streaming menerapkan fungsi pada setiap kumpulan DStream dan menghasilkan keluaran. Mereka tidak bergantung pada 
  batch sebelumnya untuk membuat batch baru.</p><br>

<h3>Kode 1</h3>

      // Contoh code accumulator
      from __future__ import print_function

      import sys

      from pyspark import SparkContext
      from pyspark.streaming import StreamingContext

      if __name__ == "__main__":
          if len(sys.argv) != 3:
              print("Usage: network_wordcount.py <hostname> <port>", file=sys.stderr)
              exit(-1)
          sc = SparkContext(appName="PythonStreamingNetworkWordCount")
          ssc = StreamingContext(sc, 1)

          lines = ssc.socketTextStream(sys.argv[1], int(sys.argv[2]))
          counts = lines.flatMap(lambda line: line.split(" "))\
                        .map(lambda word: (word, 1))\
                        .reduceByKey(lambda a, b: a+b)
          counts.pprint()

          ssc.start()
          ssc.awaitTermination()

<br><p>Berikut adalah penjelasan kodenya : <br>
 1. sys.argv = suatu array untuk argumen baris perintah dengan Python.<br>
 2. sys.stderr = digunakan untuk mencetak pesan kesalahan khusus atau melakukan penanganan kesalahan tambahan dalam skrip.<br>
 3. StreamingContext = suatu kelas dalam modul PySpark yang menyediakan fungsionalitas pemrograman streaming.<br>
 4. sc = suatu variabel yang mengacu pada SparkContext dalam PySpark.<br>
 5. socketTextStream = fungsi dalam modul PySpark Streaming yang digunakan untuk membuat DStream (Discretized Stream) dari sumber data streaming soket.<br>
 6. reduceByKey = suatu metode dalam RDD PySpark yang digunakan untuk mengelompokkan elemen berdasarkan kunci dan menerapkan fungsi reduksi pada nilai-nilai yang sesuai dengan kunci yang sama.<br>
 7. lambda line = ekspresi lambda dalam Python yang digunakan untuk mendefinisikan fungsi anonim yang mengambil argumen "line".<br>
 8. awaitTermination = metode dalam StreamingContext PySpark yang meminta konteks streaming untuk menunggu terminasi eksekusi sampai dihentikan secara eksplisit. <br>
  
</p>

<h3>Kode 2</h3>
<p>argumen yang digunakan untuk membuka soket di port 9999 dan mendengarkan (listening) untuk koneksi masuk.</p><br>

    //terminal netcut
    nc -lk 9999

<br><p>Berikut adalah penjelasan kodenya : <br>
 1. nc = nc mengacu pada perintah nc (netcat) dalam sistem operasi Unix/Linux yang digunakan untuk membaca atau menulis data melalui koneksi jaringan. <br>
 2. lk = -l (atau --listen) digunakan untuk membuat netcat berfungsi dalam mode mendengarkan (listening) dan -k (atau --keep-open) digunakan untuk menjaga soket tetap terbuka setelah koneksi klien terputus..<br>
</p>

<h3>Kode 3</h3>
<p>Perintah untuk menjalankan "network_wordcount.py".</p><br>

    // Menjalankan stateless wordcount
    spark-submit --master local[*] network_wordcount.py localhost 9999

<br>
<p>Berikut adalah penjelasan kodenya : <br>
 1. spark-submit = perintah yang digunakan untuk mengirimkan dan menjalankan aplikasi PySpark ke kluster Spark. <br>
 2. master = digunakan untuk menentukan mode manajemen kluster yang akan digunakan saat menjalankan aplikasi Spark.<br>
 3. local[*] = salah satu opsi untuk master dalam mode lokal Spark yang menunjukkan bahwa aplikasi Spark akan dijalankan secara lokal dan akan menggunakan semua core atau thread yang tersedia pada mesin tersebut.<br>
</p>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Spark%20Streaming/01_stateless_part1.png">
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Spark%20Streaming/01_stateless_part2.png">

<h2>Statefull Streaming (ERROR)</h2>
<p>Transformasi stateful membuat status untuk DStream dan akan diperbarui dengan kumpulan DStream yang masuk.</p><br>

<h3>Stateful Streaming Network Wordcount</h3>

    // Network Wordcount menggunakan statefull streaming
    from __future__ import print_function

    import sys

    from pyspark import SparkContext
    from pyspark.streaming import StreamingContext

    if __name__ == "__main__":
        if len(sys.argv) != 3:
            print("Usage: stateful_network_wordcount.py <hostname> <port>", file=sys.stderr)
            exit(-1)
        sc = SparkContext(appName="PythonStreamingStatefulNetworkWordCount")
        ssc = StreamingContext(sc, 5)
        ssc.checkpoint("checkpoint")

        # RDD with initial state (key, value) pairs
        initialStateRDD = sc.parallelize([(u'hello', 1), (u'world', 1)])

        def updateFunc(new_values, last_sum):
            return sum(new_values) + (last_sum or 0)

        lines = ssc.socketTextStream(sys.argv[1], int(sys.argv[2]))
        running_counts = lines.flatMap(lambda line: line.split(" "))\
                              .map(lambda word: (word, 1))\
                              .updateStateByKey(updateFunc, initialRDD=initialStateRDD)

        running_counts.pprint()

        ssc.start()
        ssc.awaitTermination()

<br>
<p>Berikut adalah penjelasan kodenya : <br>
 1. ssc.checkpoint = metode yang digunakan dalam PySpark Streaming untuk mengatur titik kontrol (checkpoint) pada StreamingContext (ssc).<br>
 2. parallelize = metode yang digunakan dalam PySpark untuk membuat RDD (Resilient Distributed Dataset) dari koleksi data yang ada di memori.<br>
 3. updateStateByKey = operasi dalam PySpark Streaming yang digunakan untuk melakukan pembaruan (update) keadaan berdasarkan kunci (key). br>
 4. flatMap = operasi dalam PySpark (tidak terbatas pada streaming) yang digunakan untuk menghasilkan nol atau lebih elemen output dari setiap elemen input dengan menggunakan fungsi yang ditentukan.<br>
</p>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Spark%20Streaming/02_statefull_err1.png">
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Spark%20Streaming/02_statefull_err2.png">
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Spark%20Streaming/02_statefull_err3.png">
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Spark%20Streaming/02_statefull_err4.png">

<h2>Transformasi Spark Streaming (ERROR)</h2>

    // Contoh code Understanding RDDs
    from __future__ import print_function

    import sys

    from pyspark import SparkContext
    from pyspark.streaming import StreamingContext


    def print_happiest_words(rdd):
        top_list = rdd.take(5)
        print("Happiest topics in the last 5 seconds (%d total):" % rdd.count())
        for tuple in top_list:
            print("%s (%d happiness)" % (tuple[1], tuple[0]))

    if __name__ == "__main__":
        if len(sys.argv) != 3:
            print("Usage: network_wordjoinsentiments.py <hostname> <port>", file=sys.stderr)
            exit(-1)

        sc = SparkContext(appName="PythonStreamingNetworkWordJoinSentiments")
        ssc = StreamingContext(sc, 5)

        # Read in the word-sentiment list and create a static RDD from it
        word_sentiments_file_path = "data/streaming/AFINN-111.txt"
        word_sentiments = ssc.sparkContext.textFile(word_sentiments_file_path) \
            .map(lambda line: tuple(line.split("\t")))

        lines = ssc.socketTextStream(sys.argv[1], int(sys.argv[2]))

    word_counts = lines.flatMap(lambda line: line.split(" ")) \
            .map(lambda word: (word, 1)) \
            .reduceByKey(lambda a, b: a + b)

        # Determine the words with the highest sentiment values by joining the streaming RDD
        # with the static RDD inside the transform() method and then multiplying
        # the frequency of the words by its sentiment value
        happiest_words = word_counts.transform(lambda rdd: word_sentiments.join(rdd)) \
            .map(lambda (word, tuple): (word, float(tuple[0]) * tuple[1])) \
            .map(lambda (word, happiness): (happiness, word)) \
            .transform(lambda rdd: rdd.sortByKey(False))

        happiest_words.foreachRDD(print_happiest_words)

        ssc.start()
        ssc.awaitTermination()

<br>
<p>Berikut adalah penjelasan kodenya : <br>
 1. rdd.take(5) = metode yang digunakan pada RDD (Resilient Distributed Dataset) di PySpark untuk mengambil sejumlah elemen dari RDD. br>
 2. transform = perasi yang diterapkan pada RDD dalam PySpark untuk mengubah atau memanipulasi data di dalam RDD.<br>
 3. rdd.sortByKey(False) = metode yang digunakan pada RDD di PySpark untuk mengurutkan elemen-elemen RDD berdasarkan kunci (key) dalam urutan menurun (descending order).<br>
</p>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Spark%20Streaming/03_transformasi_word_sentiment_err1.png">
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Spark%20Streaming/03_transformasi_word_sentiment_err2.png">

