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
</ul>
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

<h4>Read File yang ada di hdfs (Code 5)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/01_dataframes/03ReadFile.png")>
<hr/>
<hr/>

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

<h4>Dataframe dari data mysql cara 1 (Code 7)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/02_dataframe_mysql/05Metode1.png")>
<hr/>
<hr/>
<h4>Code 8</h4>

      // Code 8
      // Data diambil dari url yang disebutkan di bawah
      df2 = spark.read.format('jdbc').options(url='jdbc:mysql://ebt-polinema.id:3306/polinema_pln', dbtable='t_wind_turbine', user='ebt', password='EBT@2022@pltb').load()
      df2.show()

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

<h4>Mengubah dataframe menjadi RDDs (Code 9)</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/03_convert_df_rdd/07DataFrame%20ke%20RDDs.png")>
<hr/>
