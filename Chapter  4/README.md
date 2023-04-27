<h1>Chapter 4</h1>
<h2>00. Pengantar</h2>
<p>Memulai spark dan mengakses "http://quickstart.cloudera:8080/" </p>
<hr/>

      // Start Spark Daemon
      // Masuk ke Cloudera -> Command Prompt
      
      cd /home/cloudera/spark-2.0.0-bin-hadoop2.7/sbin
      sudo ./start-all.sh

<hr/>
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

<hr/>
<hr/>

      // Code 3
      // Membuat DataFrame dengan parallelizing list dan konversi RDD ke DataFram
      df2 = sc.parallelize(mylist).toDF(myschema)

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

<hr/>
<img src="https://github.com/2azmi2/Tugas-Big-Data/blob/main/Chapter%20%204/00_Pengantar/00Quickstart%208080.png")>
<hr/>
