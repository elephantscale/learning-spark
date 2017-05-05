# Processing CSV files with Spark2

This is an example of using new Spark 2 APIs to process CSV files.

[More explanation and more details are here](http://elephantscale.com/2017/05/processing-csv-files-spark-2-part-1/)

[Our Spark training program](http://elephantscale.com/training/). ([free trainings available for active job seekers](http://elephantscale.com/training/free))

## Data: Spark commit logs
Let's use Spark to analyze Spark commit logs!


This data is in [data/spark-commits](data/spark-commits)

format:  
SHA, committer, email, date, comment


**Sample data**
```
sha|committer|email|date|comment

8f2142cfd2ca632a4afb0cc29cc358edbb21f8d|Dilip Biswal|dbiswal@us.ibm.com|Sat Feb 25 23:56:57 2017 -0800|[SQL] Duplicate test exception in SQLQueryTestSuite due to meta files(.DS_Store) on Mac

89608cf26226e28f331a4695fee89394d0d38ea0|Wenchen Fan|wenchen@databricks.com|Sat Feb 25 23:01:44 2017 -0800|[SPARK-17075][SQL][FOLLOWUP] fix some minor issues and clean up the code

```


**How to generate this data**  
[See here](http://elephantscale.com/2017/05/processing-csv-files-spark-2-part-1/)

**Question**  
Find the top-10 committers with most number of commits


## Code

Launch Spark-shell
```bash
  # assumes Spark is installed in ~/apps/spark
  $  ~/apps/spark/bin/spark-shell
```

The following in Spark spark-shell

```scala
// in spark shell

// header + custom delimiter
val commits = spark.read.
               option("header", "true").
               option("delimiter", "|").
               csv("data/spark-commits/spark-commits.csv")

commits.printSchema
commits.count
commits.show(false)

// find commits from databricks
val db = commits.filter(commits("email").contains("databricks.com"))
db.count
db.show

// find top committers
commits.groupBy("email")
commits.groupBy("email").count
commits.groupBy("email").count.show
commits.groupBy("email").count.printSchema
// sort by decsc
commits.groupBy("email").count.orderBy(desc("count"))
commits.groupBy("email").count.orderBy(desc("count")).show
// show top-10
commits.groupBy("email").count.orderBy(desc("count")).show(10)

```

output
```
+--------------------+-----+
|               email|count|
+--------------------+-----+
|matei@eecs.berkel...| 1341|
|  pwendell@gmail.com|  791|
| rxin@databricks.com|  700|
|tathagata.das1565...|  502|
|     rxin@apache.org|  430|
```
