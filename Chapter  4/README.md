<h1>Chapter 4</h1>
<h2>01. Pengantar</h2>
<p>Memulai spark dan mengakses "http://quickstart.cloudera:8080/". <br>
      Selain itu diperlukan untuk memulai beberapa package yaitu HDFS, Hive, Hue, Spark yang ada di halaman website cloudera manager.  <br>
      Hal ini dilakukan agar praktikum dapat dijalankan dengan lancar</p>
<hr/>

      // Start Spark Daemon
      // Masuk ke Cloudera -> Command Prompt
      
      cd /home/cloudera/spark-2.0.0-bin-hadoop2.7/sbin
      sudo ./start-all.sh

<hr/>
<h3>Hasil Screenshot halaman website quickstart</h3>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/00_Pengantar/00Quickstart%208080.png")>
<hr/>

<h2>02. Analitik dengan DataFrames</h2>
<p>Membuat sebuah dataframe di dalam pyspark</p>
<hr/>
<h4>Code 1</h4>
      // Code 1
      // Masuk ke dalam pyspark shell
      
      cd /home/cloudera/spark-2.0.0-bin-hadoop2.7/sbin
      pyspark
      
      // Setelah masuk ke pyspark shell, import fungsi yang diperlukan
      SparkSession.builder.enableHiveSupport().getOrCreate()
      from pyspark import *
      from pyspark.sql import *
      spark = SparkSession.builder.appName("praktikum1").getOrCreate()
      sc = spark.sparkContext
      
      // Kemudian inisiasi variabel awal yang digunakan untuk data
      mylist = [(50, "DataFrame"),(60, "pandas")]
      myschema = ['col1', 'col2']

<ul>
      <li>mylist = variabel yang menampung sebuah list yang berisikan data 2 dimensi.</li>
      <li>myschema = variabel yang menampung sebuah list yang akan digunakan sebagai nama dari sebuah kolom yang nantinya digunakan di pembuatan dataframe.</li>
</ul>
<hr/>
<hr/>
<h4>Code 2</h4>

      // Code 2
      // Membuat sebuah dataframe dengan fungsi createDataFrame
      df1 = spark.createDataFrame(mylist, myschema)

<ul>
      <li>spark.createDataFrame = sebuah method yang ada di pyspark yang berguna untuk membuat sebuah dataframe dari data yang tersedia.</li>
</ul>
<h4>Membuat Dataframe dengan createDataFrame (Code 1 + Code 2)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/01_dataframes/01ObjectList.png")>
<hr/>
<hr/>
<h4>Code 3</h4>

      // Code 3
      // Membuat DataFrame dengan parallelizing list dan konversi RDD ke DataFram
      df2 = sc.parallelize(mylist).toDF(myschema)

<ul>
      <li>parallelize = sebuah method pada pyspark yang digunakan untuk membuat sebuah RDDs.</li>
      <li>toDF = sebuah method pada pyspark yang digunakan untuk mengubah sebuah sebuah data RDDs menjadi dataframe.</li>
</ul><br>

<h4>Membuat Dataframe dengan Parallelizing List (Code 1 + Code 3)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/01_dataframes/02Parallelize.png")>
<hr/>
<hr/>
<h4>Code 4</h4>

      // Code 4
      // Copy files dari local ke hdfs
      hadoop fs -put spark-2.0.0-bin-hadoop2.7/examples/src/main/resources/people.txt people.txt

<ul>
      <li>hadoop = code yang digunakan untuk menjalankan perintah hadoop.</li>
      <li>fs = sebuah sub-command yang digunakan untuk menjalankan perintah yang berhubungan dengan file Hadoop seperti menghapus atau menyalin file.</li>
      <li>put = sebuah sub-command dari fs yang digunakan untuk menyalin file dari sistem lokal ke sistem file Hadoop (HDFS)</li>
</ul>
<hr/>
<hr/>
<h4>Code 5</h4>

      // Code 5
      // Membaca data dari file yang ada di hdfs dan membuat dataframe
      from pyspark import *
      from pyspark.sql import *
      from pyspark.sql import SQLContext, Row
      peopleRDD = sc.textFile("people.txt")
      people_sp = peopleRDD.map(lambda l: l.split(","))
      people = people_sp.map(lambda p: Row(name=p[0], age=int(p[1])))
      df_people = spark.createDataFrame(people)
      df_people.createOrReplaceTempView("people")
      spark.sql("SHOW TABLES").show()
      spark.sql("SELECT name,age FROM people where age > 19").show() 

<ul>
      <li>pyspark.sql = modul di PySpark yang digunakan untuk pemrosesan data dengan SQL (sebuah libarary pyspark untuk pemrosesan sql).</li>
      <li>SQLContext = bagian dari library pyspark.sql yang digunakan untuk membuat objek SQLContext di PySpark.</li>
      <li>createOrReplaceTempView = method pada objek DataFrame di PySpark untuk membuat sebuah temporary view (pandangan sementara) dari suatu dataframe.</li>
      <li>show = sebuah fungsi yang digunakan untuk menampilkan isi dari dataframe.</li>
</ul><br>

<h4>Read File yang ada di hdfs (Code 5)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/01_dataframes/03ReadFile.png")>
<hr/>
<hr/>
<h4>Code 6</h4>

      // Code 6
      // Membaca data dari file, lalu assign schema secara programmatically
      from pyspark import *
      from pyspark.sql import *
      from pyspark.sql import SQLContext, Row
      peopleRDD = sc.textFile("people.txt")
      people_sp = peopleRDD.map(lambda l: l.split(","))
      people = people_sp.map(lambda p: Row(name=p[0], age=int(p[1])))
      df_people = people_sp.map(lambda p: (p[0], p[1].strip()))
      schemaStr = "name age"
      fields = [StructField(field_name, StringType(), True) \
      for field_name in schemaStr.split()]
      schema = StructType(fields)
      
      // Create dan Show Dataframe
      df_people = spark.createDataFrame(people,schema)
      df_people.show()
      df_people.createOrReplaceTempView("people")
      spark.sql("select * from people").show() 

<ul>
      <li>textFile = sebuah method di PySpark yang digunakan untuk membaca file teks.</li>
      <li>map = sebuha method pada RDD di PySpark untuk menerapkan sebuah fungsi pada setiap elemen RDD. </li>
      <li>lambda = sintaks untuk mendefinisikan sebuah fungsi anonim di Python.</li>
      <li>strip = method pada string di Python yang digunakan untuk menghapus spasi atau karakter tertentu di awal atau akhir string.</li>
      <li>StructField = sebuah fungsi yang digunakan untuk mendefinisikan sebuah kolom pada sebuah schema. </li>
      <li>StringType = sebuah fungsi yang digunakan untuk mendefinisikan tipe data string pada sebuah schema.</li>
</ul><br>

<h4>Read file dan assign schema (Code 6)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/01_dataframes/04AssignSchema.png")>
<hr/>

<h2>03. Membuat DataFrame dari Database Eksternal</h2>
<p>Untuk membuat dataframe dari database eksternal menggunakan perintah atau API sqlContext.read dengan jdbc sebagai format dan memasukkan data koneksi, nama tabel, user, dan password dibutuhkan sebuah connector yaitu mysql-connector-java.jar yang dimasukkan ke dalam file jars yang ada di cloudera.<br> Untuk mendaatkan mysql-connector-java.jar dapat dengan cara berikut : </p>

      // Download file mysql-connector-java.jar
      wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz
      
      // Extract
      tar zxvf mysql-connector-java-5.1.46.tar.gz
      
      // Setelah extract dapat mengcopy file secara langsung di file download ke file jar yang ada di file spark.
      // Untuk langkah ini saya mengubah nama dari jar tersebut menjadi mysql-connector-java.jar

<hr/>
<h4>Code 7</h4>

      // Code 7
      // Data diambil dari url yang disebutkan di bawah
      df1 = spark.read.format('jdbc').options(url='jdbc:mysql://ebt-polinema.id:3306/polinema_pln?user=ebt&password=EBT@2022@pltb', dbtable='t_wind_turbine').load()
      df1.show()

<ul>
      <li>spark.read.format = sebuah method di PySpark yang digunakan untuk membaca data dari sumber eksternal dengan format tertentu.  </li>
      <li>jdbc = sebuah method di PySpark yang digunakan untuk membaca data dari database dengan menggunakan JDBC (Java Database Connectivity) driver.</li>
      <li>options = sebuah method di PySpark yang digunakan untuk mengatur opsi pembacaan data dari sumber eksternal. </li>
      <li>load = sebuah method di PySpark yang digunakan untuk membaca data dari sumber eksternal. </li>
</ul><br>

<h4>Dataframe dari data mysql cara 1 (Code 7)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/02_dataframe_mysql/05Metode1.png")>
<hr/>
<hr/>
<h4>Code 8</h4>

      // Code 8
      // Data diambil dari url yang disebutkan di bawah
      df2 = spark.read.format('jdbc').options(url='jdbc:mysql://ebt-polinema.id:3306/polinema_pln', dbtable='t_wind_turbine', user='ebt', password='EBT@2022@pltb').load()
      df2.show()

<ul>
      <li>show = sebuah fungsi yang digunakan untuk menampilkan isi dari dataframe.</li>
</ul><br>
<h4>Dataframe dari data mysql cara 2 (Code 8)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/02_dataframe_mysql/06Metode2.png")>
<hr/>

<h2>04. Mengonversi DataFrames ke RDDs</h2>
<p>Sama seperti mengkonversi RDDs ke DataFrames, proses sebaliknya dapat dilakukan yaitu mengubah Dataframe ke RDDs</p>
<hr/>
<h4>Code 9</h4>

      # Create DataFrame
      mylist = [(1, "Nama-NIM"),(3, "Big Data 2023")]
      myschema = ['col1', 'col2']
      df = spark.createDataFrame(mylist, myschema)

      #Convert DF to RDD
      df.rdd.collect()

      df2rdd = df.rdd
      df2rdd.take(2)

<ul>
      <li>collect = sebuah method yang digunakan untuk mengambil seluruh data dari sebuah data frame dan mengembalikannya dalam bentuk list. </li>
      <li>rdd = sebuah method yang digunakan untuk mengubah sebuah data frame menjadi sebuah RDD (Resilient Distributed Dataset) di PySpark.</li>
      <li>take = sebuah method yang digunakan untuk mengambil sebagian data dari sebuah data frame dan mengembalikannya dalam bentuk list.</li>
</ul><br>
<h4>Mengubah dataframe menjadi RDDs (Code 9)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/03_convert_df_rdd/07DataFrame%20ke%20RDDs.png")>
<hr/>

<h2>05. Membuat Datasets</h2>
<p>Untuk membuat dataset dan dataframe dari RDDs maka perlu untuk masuk ke scala shell terlebih dahulu</p>
<hr/>
<h4>Code 10</h4>

      case class Dept(dept_id: Int, dept_name: String)

      val deptRDD = sc.makeRDD(Seq(Dept(1,"Sales"),Dept(2,"HR")))

      val deptDS = spark.createDataset(deptRDD)

      val deptDF = spark.createDataFrame(deptRDD)

<ul>
      <li>makeRDD = sebuah method yang digunakan untuk membuat RDD dari suatu list atau tuple yang diberikan. </li>
      <li>Seq = sebuah method yang digunakan untuk membuat RDD dari sebuah sequence di Python. Sequence ini bisa berupa list, tuple, atau set.</li>
      <li>createDataset = sebuah method yang digunakan untuk membuat Dataset dari suatu list atau tuple yang diberikan.</li>
</ul><br>
<h4>Mebuat dataset (Code 1o)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/04_datasets/08MembuatDataset1.png")>
<hr/>
<hr/>
<h4>Code 11</h4>

      deptDS.rdd

      deptDF.rdd

      deptDS.filter(x => x.dept_location > 1).show()

<ul>
      <li>filter = fungsi di PySpark yang digunakan untuk melakukan filter atau pemilihan data yang memenuhi kondisi tertentu dari sebuah RDD atau Dataset.</li>
</ul><br>
<h4>Mebuat dataset (Code 11)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/04_datasets/09MembuatDataset2.png")>
<hr/>

<h2>06. Mengonversi DataFrame ke Datasets dan sebaliknya</h2>
<p>Sebuah DataFrame dapat dikonversi ke Dataset dengan menambahkan keyword as sebagai metode pada variabel DataFrame.</p>
<hr/>
<h4>Code 12</h4>

      val newDeptDS = deptDF.as[Dept]

      newDeptDS.show()

      newDeptDS.first()

      // mengonversi ke DataFrame kembali
      newDeptDS.toDF.first()

<ul>
      <li>as = fungsi yang digunakan untuk memberi alias atau mengubah nama kolom pada DataFrame. </li>
      <li>toDF = fungsi yang digunakan untuk mengubah RDD menjadi DataFrame dengan format kolom yang ditentukan.</li>
      <li>first = fungsi yang digunakan untuk mengambil nilai pertama dari RDD atau Dataset.</li>
</ul><br>
<h4>Konversi dataframe ke dataset dan sebaliknya (Code 12)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/05_convert_df_ds/10DataFrame%20ke%20Dataset.png")>
<hr/>

<h2>07. Mengakses Metadata menggunakan Catalog</h2>
<p>kita dapat mengakses metadata sebuah data frame menggunakan objek Catalog. Catalog adalah bagian dari modul pyspark.sql dan digunakan untuk mengakses informasi tentang tabel, database, dan fungsi yang tersedia di SparkSession.</p>
<hr/>
<h4>Code 13</h4>

      spark.catalog.listDatabases().select("name").show()

      spark.catalog.listTables.show()

      spark.catalog.isCached("sample_07")

      spark.catalog.listFunctions().show()

<ul>
      <li>listDatabases = fungsi yang digunakan untuk mendapatkan daftar database yang tersedia di dalam SparkSession.</li>
      <li>listTables = fungsi yang digunakan untuk mendapatkan daftar tabel yang tersedia dalam database yang ditentukan di dalam SparkSession.</li>
      <li>listFunctions = digunakan untuk mendapatkan daftar fungsi yang tersedia di dalam SparkSession.</li>
      <li>isCached = fungsi yang digunakan untuk mengecek apakah RDD atau DataFrame sudah di-cache atau belum. Mengembalikan nilai boolean true jika sudah dicache dan false jika belum</li>
      <li>select = fungsi yang digunakan untuk melakukan pemilihan kolom pada DataFrame.</li>
</ul><br>
<h4>Akses Metadata (Code 13)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/06_access_metadata/11Metadata.png")>
<hr/>

<h2>08. Bekerja dengan berkas teks</h2>
<p>kita dapat mengakses file dengan type .txt dan menampilkan isi dari file tersebut.</p>
<hr/>
<h4>Code 14</h4>

      df_txt = spark.read.text("people.txt")
      df_txt.show()
      df_txt

<ul>
      <li>Read = sebuah method yang digunakan untuk membaca file</li>
      <li>text = sebuah method yang digunakan untuk membaca file teks sebagai RDD dengan setiap baris sebagai sebuah elemen RDD.</li>
</ul><br>
<h4>Akses File text (Code 14)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/07_impor_txt/12ReadText.png")>
<hr/>

<h2>09. Bekerja dengan JSON</h2>
<p>Spark SQL dapat secara otomatis mengenali schema dari dataset JSON ketika dimuat ke dalam sebuah DataFrame.</p>
<hr/>
<h4>Code 15</h4>

      df_json = spark.read.load("people.json", format="json")
      df_json = spark.read.json("people.json")
      df_json.printSchema()

      df_json.show()

<ul>
      <li>load = sebuah method yang digunakan untuk membaca data dari berbagai sumber data yang didukung oleh PySpark seperti CSV, JSON, parquet, dan lainnya.</li>
      <li>json = method yang digunakan untuk menentukan bahwa sumber data adalah file JSON.</li>
      <li>format = method ini digunakan untuk menentukan format data yang akan dibaca.</li>
      <li>printSchema = method ini digunakan untuk mencetak skema dari DataFrame yang dibaca dari file JSON. Skema menunjukkan tipe data dari setiap kolom dalam DataFrame.</li>
</ul><br>
<h4>JSON (Code 15)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/08_impor_json/13JSON1.png")>
<hr/>
<hr/>
<h4>Code 16</h4>

      df_json.write.json("newjson_dir")
      df_json.write.format("json").save("newjson_dir2")

<ul>
      <li>write = method yang digunakan untuk menyimpan DataFrame ke berbagai sumber data yang didukung oleh PySpark seperti CSV, JSON, parquet, dan lainnya.</li>
      <li>save = method yang digunakan untuk menyimpan DataFrame ke berbagai sumber data yang didukung oleh PySpark seperti CSV, JSON, parquet, dan lainnya.</li>
</ul><br>
<h4>JSON (Code 16)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/08_impor_json/14JSON2.png")>
<hr/>
<hr/>
<h4>Code 17 (ERROR)</h4>

      // Disini masih terjadi error dimana "org.sl4j.impl.StaticLoggerBinder" tidak dapat diakses
      df_json.write.parquet("parquet_dir")
      df_json.write.format("parquet").save("parquet_dir2")
      
<ul>
      <li>parquet = salah satu format file data yang didukung oleh PySpark. Format ini juga dapat digunakan untuk menyimpan data dengan skema yang kompleks, seperti data bertingkat, data berulang, dan data yang mengandung tipe data kompleks seperti array atau struct.</li>
</ul><br>
<h4>JSON (Code 17)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/08_impor_json/15JSON3%20(ERROR).png")>
<hr/>

<h2>10. Bekerja dengan CSV</h2>
<p>Sama seperti type .txt, kita juga dapat menggunakan file bertipe CSV dimana data dari file tersebut dapat digunakan. <br>
      Namun sebelum itu diperlukan sebuah dataset csv di dalam hdfs. berikut adalah caranya : </p>


      // Download dataset
      wget https://raw.githubusercontent.com/databricks/spark-csv/master/src/test/resources/cars.csv --no-check-certificate
      
      // Memasukkan data dari local ke hdfs
      hadoop fs -put cars.csv cars.csv

<hr/>
<h4>Code 18</h4>

      csv_df = spark.read.options(header='true',inferSchema='true').csv("cars.csv")

      csv_df.printSchema()

      csv_df.select('year', 'model').write.options(codec="org.apache.hadoop.io.compress.GzipCodec").csv('newcars.csv')

<ul>
      <li>Options = parameter ini digunakan untuk menentukan opsi pembacaan file CSV seperti pemisah kolom, karakter kutipan, dan lainnya. </li>
      <li>inferSchema = parameter ini digunakan untuk menentukan apakah skema dari file CSV harus diinfer oleh PySpark atau tidak.</li>
      <li>csv = parameter ini digunakan untuk menentukan bahwa sumber data adalah file CSV.</li>
      li>header = parameter ini digunakan untuk menentukan bahwa baris pertama dari file CSV adalah header, yang berisi nama kolom. </li>
      <li>codec = parameter ini digunakan untuk menentukan kodek karakter yang digunakan untuk membaca file CSV. </li>
</ul><br>
<h4>Akses file csv (Code 18)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/09_impor_csv/16csv2.png")>
<hr/>
