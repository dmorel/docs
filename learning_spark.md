# Learning Spark

## Chapter 1. Introduction to Apache Spark: A Unified Analytics Engine

- reduces the complexity of MR
- less disk I/O, more in memory
- execution plan is an efficient DAG
- code generator produces bytecode running in the JVM, language agnostic
- modularity (varied languages available, 4 typical core modules)
- extensibility (multiple sources and outputs)
- highly fault tolerant
- Catalyst optimizer for SQL
- Tungsten code generator
- Produces RDD (resilient distributed dataset, low level), DataFrames and DataSets (high level abstraction): 3 APIs

### unified analytics: batch, streaming, SQL, and graph

- Spark SQL: build permanent or temp tables
- Spark MLlib (newer spark.ml): build pipelines and persist models
- Spark structured streaming: views a stream as a continuously growing table, can be queried with SQL
- GraphX: PageRank, Connected Components, and Triangle counting

### Distributed execution

- driver program orchestrates parallel execution
- creates a SparkSession to access the executors and cluster manager
- sets JVM parameters
- uses the cluster manager to initiate resources, then communicates directly with them
- SparkSession is now unique entrypoint (no more SQLContext, HiveContext etc)
- context is created automatically when using Spark shell
- cluster manager can be of 4 types: YARN, K8s, standalone and Mesos
- typically one executor per physical node

### YARN

- client or cluster mode (client: driver runs on host, cluster, runs on a node)
- YARN resource manager works with YARN application master to allocate containers on NodeManagers
- Cluster manager YARN RM + AppMaster

### K8s

- driver on a pod
- each worker in a pod
- Cluster manager is K8s master

### Distributed data an partitions

- tries to respect locality
- each partiton is treated like a DataFrame in memory

  ```python
  # make 8 partitions
  log_df = spark.read.text("path_to_large_text_file").repartition(8)
  print(log_df.rdd.getNumPartitions())

  # create a DF of 10k integers and make 8 partitions in memory
  df = spark.range(0, 10000, 1, 8)
  print(df.rdd.getNumPartitions())
  ```

- tune and change partitioning config to use maximum parallelism based on # of cores in executors

## Chapter 2: Getting started

- for python, simply `pip install pyspark[sql,ml,mllib]`
- only set JAVA_HOME in env
- for scripts, set SPARK_HOME in env
- fire up spark shell: `pyspark`
- Spark computations are expressed as operations. These operations are then converted into low-level RDD-based bytecode as tasks, which are distributed to Spark’s executors for execution.
- every computation operates on RDDs, inaccessible to the user, by generating Scala code

### Spark application concepts

- Application: program uses driver and executors
- SparkSession: object which is point of entry to use the API
- Job: parallel computation, multiple tasks
- Stage: job divided in smaller dependent set of tasks
- Task: unit of work sent to executor

#### Spark application and SparkSession

- shell creates the driver which instantiates the SparkSession
- 1 task per core

#### Spark Jobs

- driver converts Spark application to one or more jobs
- each job is transformed into a DAG; each node in the DAG can be one or more stages

#### Spark Stages

- operations that cannot be parallelized run in multiple stages
- stages often delineated on operator's computation boundaries -> data transfer to other executors

#### Spark Tasks

- sub-unit of a Stage
- each task maps to a single core and a single partition

#### Transformations, Actions and Lazy evaluation

- Transformations turn a DF into a new one, without altering the original data (like select, filter, and such)
- Lazy evaluation: results are not computed immediately, but recorded as lineage, which allows for rearranging and optimizing the plan at runtime; computation triggered by actions, not transformations
- fault tolerant as each transformation is recorded in lineage and DFs are immutable between transformations
- example transformations: orderBy, groupBy, filter, select, join
- example actions: show, take, count, collect, save
- Transformations can have narrow or wide dependencies (single partition for input and output, or multiple, inducing shuffling, for instance a sort)

#### The Spark UI

- when launching a shell, part of the output gives the web UI address

#### Standalone app

example, counting items in a CSV file:

```python
# $SPARK_HOME/bin/spark-submit mnmcount.py data/mnm_dataset.csv
import sys
from pyspark.sql import SparkSession

if __name__ == "__main__":
   if len(sys.argv) != 2:
       print("Usage: mnmcount <file>", file=sys.stderr)
       sys.exit(-1)

   spark = (SparkSession
     .builder
     .appName("PythonMnMCount")
     .getOrCreate())
   mnm_file = sys.argv[1]
   mnm_df = (spark.read.format("csv")
     .option("header", "true")
     .option("inferSchema", "true")
     .load(mnm_file))

   count_mnm_df = (mnm_df
     .select("State", "Color", "Count")
     .groupBy("State", "Color")
     .sum("Count")
     .orderBy("sum(Count)", ascending=False))
   count_mnm_df.show(n=60, truncate=False)
   print("Total Rows = %d" % (count_mnm_df.count()))

   ca_count_mnm_df = (mnm_df
     .select("State", "Color", "Count")
     .where(mnm_df.State == "CA")
     .groupBy("State", "Color")
     .sum("Count")
     .orderBy("sum(Count)", ascending=False))
   ca_count_mnm_df.show(n=10, truncate=False)

   spark.stop()
```

- Note: to avoid having verbose INFO messages printed to the console, copy the `log4j.properties.template` file to `log4j.properties` and set `log4j.rootCategory=WARN` in the `conf/log4j.properties file`.
- For Scala, nearly the same, just use sbt first

## Chapter 3: Apache Spark’s Structured APIs

- RDD are dumb, not open to optimization (compute function is opaque to spark, just returns an Iterator[T], no explicit intent, plus generic object in Python)
- DF API allows for much more readable code (not passing lambda functions like in RDD)
- Same API across all languages

### The DataFrame API

- Like distributed in-memory tables with named columns and schemas, columns having a specified data type

- DataFrames are immutable, you change them by creating new ones, lineage is kept

- classic data types, basic and structured

- better to specify a schema upfront than to infer from schema on read

- schemas can be defined in code or in a DSL string

  ```python
  from pyspark.sql.types import *
  schema = StructType([StructField("author", StringType(), False),
      StructField("title", StringType(), False),
      StructField("pages", IntegerType(), False)])
  # or...
  schema = "author STRING, title STRING, pages INT"
  data = [[1, "Jules", "Damji", "https://tinyurl.1", "1/4/2016", 4535, ["twitter",
  "LinkedIn"]], ...
  spark = (SparkSession.builder.appName("Example-3_6").getOrCreate())
  blogs_df = spark.createDataFrame(data, schema)
  ```

- reading from a file (scala version):

  ```scala
  val blogsDF = spark.read.schema(schema).json(jsonFile)
  ```

- operations on columns

  ```scala
  // In Scala
  scala> import org.apache.spark.sql.functions._
  scala> blogsDF.columns
  res2: Array[String] = Array(Campaigns, First, Hits, Id, Last, Published, Url)

  // Access a particular column with col and it returns a Column type
  scala> blogsDF.col("Id")
  res3: org.apache.spark.sql.Column = id

  // Use an expression to compute a value
  scala> blogsDF.select(expr("Hits * 2")).show(2)
  // or use col to compute value
  scala> blogsDF.select(col("Hits") * 2).show(2)

  // Use an expression to compute big hitters for blogs
  // This adds a new column, Big Hitters, based on the conditional expression
  blogsDF.withColumn("Big Hitters", (expr("Hits > 10000"))).show()

  // Concatenate three columns, create a new column, and show the
  // newly created concatenated column
  blogsDF
  .withColumn("AuthorsId", (concat(expr("First"), expr("Last"), expr("Id"))))
  .select(col("AuthorsId"))
  .show(4)

  // These statements return the same value, showing that
  // expr is the same as a col method call
  blogsDF.select(expr("Hits")).show(2)
  blogsDF.select(col("Hits")).show(2)
  blogsDF.select("Hits").show(2)

  // Sort by column "Id" in descending order
  blogsDF.sort(col("Id").desc).show()
  blogsDF.sort($"Id".desc).show()
  ```

- $ before the name of the column which is a function in Spark that converts column named Id to a Column.

- row in Spark is a generic Row object, ordered collection of fields, starting with index 0

- rows can be used to create a DF quickly:

  ```python
  rows = [Row("Matei Zaharia", "CA"), Row("Reynold Xin", "CA")]
  authors_df = spark.createDataFrame(rows, ["Authors", "State"])
  authors_df.show()
  ```

- DataFrameReader and DataFrameWriter to read/write DFs from files from many origins

- Schema inference, speedup:

  ```scala
  val sampleDF = spark
      .read
      .option("samplingRatio", 0.001)
      .option("header", true)
      .csv("/databricks-datasets/learning-spark-v2/sf-fire/sf-fire-calls.csv")
  ```

- if schema isn't inferred but specified:

  ```scala
  sf_fire_file = "/databricks-datasets/learning-spark-v2/sf-fire/sf-fire-calls.csv"
  fire_df = spark.read.csv(sf_fire_file, header=True, schema=fire_schema)
  ```

- create a schema programmatically

  ```python
  from pyspark.sql.types import *
  fire_schema = StructType([StructField('CallNumber', IntegerType(), True),
      StructField('UnitID', StringType(), True),
      ...
  ```

- use DataFrameWriter to save the file, default is Parquet+snappy, schema is saved in files so no need to declare it again for subseqent opens

  ```python
  parquet_path = ...
  fire_df.write.format("parquet").save(parquet_path)
  ```

- files can be saved as tables registered with the hive metastore:

  ```python
  parquet_table = ... # name of the table
  fire_df.write.format("parquet").saveAsTable(parquet_table)
  ```

- projections and filters are done with select() and filter() or where():

  ```python
  few_fire_df = (fire_df
    .select("IncidentNumber", "AvailableDtTm", "CallType")
    .where(col("CallType") != "Medical Incident"))
  few_fire_df.show(5, truncate=False)

  # return number of distinct types of calls using countDistinct()
  from pyspark.sql.functions import *
  fire_df
    .select("CallType")
    .where(col("CallType").isNotNull())
    .agg(countDistinct("CallType").alias("DistinctCallTypes"))
    .show()

  # filter for only distinct non-null CallTypes from all the rows
  fire_df
    .select("CallType")
    .where(col("CallType").isNotNull())
    .distinct()
    .show(10, False)
  ```

- renaming, adding and dropping columns
    - by using StructField like above, we change the columns name at creation time
    - use the `withColumnRenamed` method: `new_fire_df = fire_df.withColumnRenamed("Delay", "ResponseDelayedinMins")` which creates a copy of the DataFrame
    
- modify the type of a column with `spark.sql.functions` methods and drop the original column:

  ```python
  fire_ts_df = (new_fire_df
    .withColumn("IncidentDate", to_timestamp(col("CallDate"), "MM/dd/yyyy"))
    .drop("CallDate")
    .withColumn("OnWatchDate", to_timestamp(col("WatchDate"), "MM/dd/yyyy"))
    .drop("WatchDate")
    .withColumn("AvailableDtTS", to_timestamp(col("AvailableDtTm"),
    "MM/dd/yyyy hh:mm:ss a"))
    .drop("AvailableDtTm"))
  (fire_ts_df
    .select(year('IncidentDate'))
    .distinct()
    .orderBy(year('IncidentDate'))
    .show())
  ```

- aggregations

  ```python
  (fire_ts_df
    .select("CallType")
    .where(col("CallType").isNotNull())
    .groupBy("CallType")
    .count()
    .orderBy("count", ascending=False)
    .show(n=10, truncate=False))
  ```

- more filtering, and using `spark.sql.functions` functions to process some columns

  ```python
  (fire_ts_df
    .select(year('IncidentDate'))
    .distinct()
    .orderBy(year('IncidentDate'))
    .show())
  ```

- `collect()` is like a `select(*)` which can lead to OOM, better use `take(n)` which returns a limited number of rows

- statistical functions available: `min(), max(), sum(), and avg()`

  ```python
  import pyspark.sql.functions as F
  (fire_ts_df
    .select(F.sum("NumAlarms"), F.avg("ResponseDelayedinMins"),
      F.min("ResponseDelayedinMins"), F.max("ResponseDelayedinMins"))
    .show())
  
  +--------------+--------------------------+--------------------------+---------+
  |sum(NumAlarms)|avg(ResponseDelayedinMins)|min(ResponseDelayedinMins)|max(...) |
  +--------------+--------------------------+--------------------------+---------+
  |       4403441|         3.902170335891614|               0.016666668|1879.6167|
  +--------------+--------------------------+--------------------------+---------+
  ```
  
- Other stat functions: `stat(), describe(), correlation(), covariance(), sampleBy(), approxQuantile(), frequentItems()` etc

### The DataSet API

<img src="learning_spark/structured_apis.png" alt="structured_apis" style="zoom:67%;" />

A `DataFrame` is an alias **in Scala** (or java?) for a collection of generic objects, `Dataset[Row]`, whereas a `DataSet[T]` is

> a strongly typed collection of domain-specific objects that can be transformed in parallel using functional or relational operations. Each Dataset [in Scala] also has an untyped view called a DataFrame, which is a Dataset of Row.

>In Spark’s supported languages, Datasets make sense only in Java and Scala, whereas in Python and R only DataFrames make sense.

- Define the schema using a case class

  ```scala
  case class DeviceIoTData (battery_level: Long, c02_level: Long, 
  cca2: String, cca3: String, cn: String, device_id: Long, 
  device_name: String, humidity: Long, ip: String, latitude: Double,
  lcd: String, longitude: Double, scale:String, temp: Long, 
  timestamp: Long)
  ```

- read the data

  ```scala
  val ds = spark.read
   .json("/databricks-datasets/learning-spark-v2/iot-devices/iot_devices.json")
   .as[DeviceIoTData]
  ```

- operations just like on DataFrames, passing lambda functions

  ```scala
  val filterTempDS = ds.filter({d => {d.temp > 30 && d.humidity > 70})
  ```

- another example (select is semantically the same as map)

  ```scala
  case class DeviceTempByCountry(temp: Long, device_name: String, device_id: Long, 
    cca3: String)
  val dsTemp = ds
    .filter(d => {d.temp > 25})
    .map(d => (d.temp, d.device_name, d.device_id, d.cca3))
    .toDF("temp", "device_name", "device_id", "cca3")
    .as[DeviceTempByCountry]
  ```

- DataSets are similar to RDD but with a nicer interface; they offer compile-time safety but are easier to read



### DataSet vs DataFrame

<img src="learning_spark/structured_apis_in_spark.png" alt="structured_apis_in_spark" style="zoom:67%;" />

- need compile time safety, don't mind creating multiple case classes for a specific `DataSet[T]`, then use DataSet
- If processing requires transformations with SQL-like queries, use `DataFrame`
- if you want more efficient serialization, use `DataSet`
- if you want code unification, use `DataFrame`
- speed and space efficiency, use `DataFrame`
- using RDDs is discouraged except in some specific cases like the need to instruct Spark exactly how to run a query
- access to the underlying rdd is always possible using `df.rdd`

### Spark SQL and the underlying engine

<img src="learning_spark/spark_sql.png" alt="spark_sql" style="zoom: 67%;" />

#### The Catalyst Optimizer

<img src="learning_spark/query_planner.png" alt="query_planner" style="zoom:67%;" />

- To check the query plan of a dataFrame, use `df.explain(True)`
- Typically the plan for a DF and the same in plain SQL should be equivalent
- **Catalyst** begins with generating an AST (abstract syntax tree) using an internal `Catalog`
- then standard rule-based optimization produces a set of plans, the best one is chosen based on CBO; this is where constant folding, predicate pushdown and column pruning is applied
- this allows the creation of the physical plan
- then code generation happens; the **Tungsten** engine produces optimal code at this stage

## Chapter 4: Spark SQL and DataFrames: Introduction to Built-in Data Sources

To issue any SQL query, use the `sql()` method on the SparkSession instance, spark, such as `spark.sql("SELECT * FROM myTableName")`. All `spark.sql` queries executed in this manner return a **DataFrame**

```python
from pyspark.sql import SparkSession        
# Create a SparkSession
spark = (SparkSession
  .builder
  .appName("SparkSQLExampleApp")
  .getOrCreate())

# Path to data set
csv_file = "/databricks-datasets/learning-spark-v2/flights/departuredelays.csv"

# Read and create a temporary view
# Infer schema (note that for larger files you 
# may want to specify the schema)
df = (spark.read.format("csv")
  .option("inferSchema", "true")
  .option("header", "true")
  .load(csv_file))
df.createOrReplaceTempView("us_delay_flights_tbl")
```

this will create a view accessible in the internal Catalog for SQL queries:

```python
spark.sql("""SELECT distance, origin, destination 
FROM us_delay_flights_tbl WHERE distance > 1000 
ORDER BY distance DESC""").show(10)
```

the queries are equivalent to projections and filters on the `DataFrame`

- **NOTE** temp views are not written to the metastore

- The tables metadata are written to the metastore, Hive by default (`/user/hive/warehouse`) can be changed with parameter `spark.sql.warehouse.dir`

- **managed** vs **unmanaged** tables: managed handles both data and metadata, unmanaged only the metadata, storage is left to the user; `DROP TABLE` on managed data sources will delete the data files, for instance

- creating databases

  ```python
  spark.sql("CREATE DATABASE learn_spark_db")
  spark.sql("USE learn_spark_db")
  ```

- creating a managed table is the default

  ```python
  spark.sql("CREATE TABLE managed_us_delay_flights_tbl (date STRING, delay INT,  
    distance INT, origin STRING, destination STRING)")
  ```

- creating an unmanaged table is done by specifying a path for the data file(s)

  ```python
  spark.sql("""CREATE TABLE us_delay_flights_tbl(date STRING, delay INT, 
    distance INT, origin STRING, destination STRING) 
    USING csv OPTIONS (PATH 
    '/databricks-datasets/learning-spark-v2/flights/departuredelays.csv')""")
  ```

  ```python
  (flights_df
    .write
    .option("path", "/tmp/data/us_flights_delay")
    .saveAsTable("us_delay_flights_tbl"))
  ```

- create temp views and global temp views: temp view tied to a `SparkSession` in a Spark application; a global temp view is visible to all sessions in the same application (several sessions can live in an application for instance to access different resources)

  ```sql
  CREATE OR REPLACE GLOBAL TEMP VIEW
  CREATE OR REPLACE TEMP VIEW
  SELECT * FROM global_temp.<global temp view name>
  ```

- accessing the catalog

  ```python
  spark.catalog.listDatabases()
  spark.catalog.listTables()
  spark.catalog.listColumns("us_delay_flights_tbl")
  ```

- caching tables (with lazy option)

  ```sql
  CACHE [LAZY] TABLE <table-name>
  UNCACHE TABLE <table-name>
  ```

- reading tables into dataframes

  ```python
  us_flights_df = spark.sql("SELECT * FROM us_delay_flights_tbl")
  us_flights_df2 = spark.table("us_delay_flights_tbl")
  ```

  

### DataFrameReader

core construct for reading data from a source:

```python
DataFrameReader.format(args <defaults to "parquet">)
  .option("key", "value" <keys can be "mode", "inferSchema", "path", "header", etc)
  .schema(args <DDL or StructType>)
  .load()
```

<https://spark.apache.org/docs/latest/sql-data-sources.html>

**NOTE** no schema needed when reading from Parquet, it's bundled in the file; however for streaming data source, a schema is mandatory

you can only access a DataFrameReader through a SparkSession instance. That is, you cannot create an instance of DataFrameReader. To get an instance handle to it, use either one of: 

```python
SparkSession.read
SparkSession.readStream
```

### DataFrameWriter

examples:

```python
DataFrameWriter.format(args)
  .option(args)
  .bucketBy(args)
  .partitionBy(args)
  .save(path)

DataFrameWriter.format(args)
  .option(args)
  .sortBy(args)
  .saveAsTable(table)

df.write.format("json").mode("overwrite").save(location)
```

### Parquet

Parquet files are stored in multiple parts in a directory together with metadata, example:

```python
file = """/databricks-datasets/learning-spark-v2/flights/summary-data/parquet/
  2010-summary.parquet/"""
df = spark.read.format("parquet").load(file)
```

```sql
-- LOAD directly in SQL
CREATE OR REPLACE TEMPORARY VIEW us_delay_flights_tbl
    USING parquet
    OPTIONS (
      path "/databricks-datasets/learning-spark-v2/flights/summary-data/parquet/
      2010-summary.parquet/" )
```

```python
spark.sql("SELECT * FROM us_delay_flights_tbl").show()
```

```python
(df.write.format("parquet")
  .mode("overwrite")
  .option("compression", "snappy")
  .save("/tmp/data/parquet/df_parquet"))
```

