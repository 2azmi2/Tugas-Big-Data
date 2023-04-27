<h1>Chapter 4</h1>
<h2>00. Pengantar</h2>
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
<h2>01. Analitik dengan DataFrames</h2>
<p>Membuat sebuah dataframe di dalam pyspark</p>
<hr/>

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

<hr/>
<hr/>

      // Code 2
      // Membuat sebuah dataframe dengan fungsi createDataFrame
      df1 = spark.createDataFrame(mylist, myschema)

<h4>Membuat Dataframe dengan createDataFrame</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/01_dataframes/01ObjectList.png")>
<hr/>
<hr/>

      // Code 3
      // Membuat DataFrame dengan parallelizing list dan konversi RDD ke DataFram
      df2 = sc.parallelize(mylist).toDF(myschema)

<h4>Membuat Dataframe dengan Parallelizing List</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/01_dataframes/02Parallelize.png")>
<hr/>
<hr/>

      // Code 4
      // Copy files dari local ke hdfs
      hadoop fs -put spark-2.0.0-bin-hadoop2.7/examples/src/main/resources/people.txt people.txt

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

<h4>Read File yang ada di hdfs</h4>
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

<h4>Read file dan assign schema</h4>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/01_dataframes/04AssignSchema.png")>
<hr/>
