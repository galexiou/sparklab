
```markdown
# Spark LAB Basics

This README provides instructions and code snippets for using Spark in standalone mode using a Docker image. Make sure you have Docker installed before proceeding.

## Getting Started

To get started, follow these steps:

### 1. Pull the Docker Image

a. Pull the latest Docker image for Spark in standalone mode:

```bash
docker pull galexiou/sparklab:latest
```

### 2. Run the Docker Container

b. Run the Docker container with the image you pulled:

```bash
docker run -d --name sparklab -e SPARK_MODE=master galexiou/sparklab
```

### 3. Access the Shell

c. Access the shell of the running Docker container:

```bash
docker exec -it sparklab /bin/bash
```

d. Change to the root directory:

```bash
cd /
```

e. Start the Spark shell:

```bash
spark-shell
```

## 1. RDD Operations - Basics

a. Load a text file into an RDD:

```scala
val lines = sc.textFile("/labdata/README.md")
```

b. Map the line lengths:

```scala
val lineLengths = lines.map(s => s.length)
```

c. Reduce to find the total length:

```scala
val totalLength = lineLengths.reduce((a, b) => a + b)
```

d. Perform word count:

```scala
val wordCounts = lines.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey((a, b) => a + b)
```

e. Collect and print word counts:

```scala
wordCounts.collect()
```

f. View the RDD's debug information:

```scala
wordCounts.toDebugString
```

## 2. DAG

a. Load a log file into an RDD:

```scala
val logFile = sc.textFile("/labdata/log.txt")
```

b. Filter lines starting with "ERROR":

```scala
val errors = logFile.filter(_.startsWith("ERROR"))
```

c. Extract messages from errors:

```scala
val messages = errors.map(_.split("\t")).map(r => r(1))
```

d. Cache the messages:

```scala
messages.cache()
```

e. Count messages containing "mysql":

```scala
messages.filter(_.contains("mysql")).count()
```

f. Count messages containing "php":

```scala
messages.filter(_.contains("php")).count()
```

## 3. Scala

a. Create a tuple:

```scala
val pair = ('a', 'b')
```

b. Access tuple elements:

```scala
pair._1 // Accesses the first element
pair._2 // Accesses the second element
```

## 4. Key-value Pairs

a. Load the README file:

```scala
val readme = sc.textFile("/labdata/README.md")
```

b. Count lines in the README file:

```scala
readme.count()
```

c. Get the first line of the README file:

```scala
readme.first()
```

d. Filter lines containing "Spark":

```scala
val linesWithSpark = readme.filter(line => line.contains("Spark"))
```

e. Count lines with "Spark":

```scala
linesWithSpark.count()
```

f. Alternative way to count lines with "Spark":

```scala
readme.filter(line => line.contains("Spark")).count()
```

g. Find the maximum number of words in a line:

```scala
readme.map(line => line.split(" ").size).reduce((a, b) => if (a > b) a else b)
```

h. Import Math and use Math.max:

```scala
import java.lang.Math
readme.map(line => line.split(" ").size).reduce((a, b) => Math.max(a, b))
```

i. Perform word count:

```scala
val wordCounts = readme.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey((a, b) => a + b)
```

j. Collect and print word counts:

```scala
wordCounts.collect().foreach(println)
```

## 5. RDD Operations: Analyzing a Log File

a. Load a log file:

```scala
val logFile = sc.textFile("/labdata/notebook.log")
```

b. Filter out lines containing "INFO" or "ERROR":

```scala
val info = logFile.filter(line => line.contains("INFO"))
```

c. Count lines with "INFO":

```scala
info.count()
```

d. Count lines with "spark" (case-insensitive):

```scala
info.filter(line => line.contains("spark")).count()
```

e. Fetch lines with "spark" as an array of Strings:

```scala
info.filter(line => line.contains("spark")).collect().foreach(println)
```

f. View the RDD's debug information:

```scala
println(info.toDebugString)
```

## 6. Joining RDDs

a. Load the README file:

```scala
val readmeFile = sc.textFile("/labdata/README.md")
```

b. Load the POM file:

```scala
val pom = sc.textFile("/labdata/pom.xml")
```

c. Count the occurrences of "Spark" in the README file:

```scala
println(readmeFile.filter(line => line.contains("Spark")).count())
```

d. Count the occurrences of "spark" (case-insensitive) in the POM file:

```scala
println(pom.filter(line => line.contains("spark")).count())
```

e. Perform WordCount on the README file:

```scala
val readmeCount = readmeFile.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
```

f. Perform WordCount on the POM file:

```scala
val pomCount = pom.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
```

g. Join the two RDDs and cache the result:

```scala
val joined = readmeCount.join(pomCount)
joined.cache()
```

h. View the content of the joined RDD:

```scala
joined.collect().foreach(println)
```

i. Combine values to get the total count:

```scala
val joinedSum = joined.map(k => (k._1, (k._2)._1 + (k._2)._2))
```

j. View the content of the joinedSum RDD:

```scala
joinedSum.collect().foreach(println)
```

k. Print the first five elements from the joined and joinedSum RDDs:

```scala
println("Joined Individual\n")
joined.take(5).foreach(println)
println("\n\nJoined Sum\n")
joinedSum.take(5).foreach(println)
```

## 7. Shared Variables: Broadcast / Accumulator

a. Create a broadcast variable:

```scala
val broadcastVar = sc.broadcast(Array(1, 2, 3))
```

b. Access the broadcast variable's value:

```scala
broadcastVar.value
```

c

. Create an accumulator:

```scala
val accum = sc.accumulator(0)
```

d. Use the accumulator to sum values:

```scala
sc.parallelize(Array(1, 2, 3, 4)).foreach(x => accum += x)
```

e. Access the accumulator's value:

```scala
accum.value
```

## 8. Scala - Example Application

This section demonstrates an example application to determine the top 10 medallion numbers based on the number of trips using a taxi dataset.

a. Load the taxi dataset:

```scala
val taxi = sc.textFile("/labdata/nyctaxi.txt")
```

b. View the first five rows of the dataset:

```scala
taxi.take(5).foreach(println)
```

c. Parse the values from each line:

```scala
val taxiParse = taxi.map(line => line.split(","))
```

d. Create key-value pairs for medallion numbers:

```scala
val taxiMedKey = taxiParse.map(vals => (vals(6), 1))
```

e. ReduceByKey to count the number of trips for each medallion:

```scala
val taxiMedCounts = taxiMedKey.reduceByKey((v1, v2) => v1 + v2)
```

f. Print the top 10 medallion numbers with the highest trip counts:

```scala
for (pair <- taxiMedCounts.map(_.swap).top(10))
  println(s"Taxi Medallion ${pair._2} had ${pair._1} Trips")
```

g. An alternative one-liner for the same operation:

```scala
val taxiMedCountsOneLine = taxi.map(line => line.split(",")).map(vals => (vals(6), 1)).reduceByKey(_ + _)
```

h. Cache the taxiMedCountsOneLine RDD for performance comparison:

```scala
taxiMedCountsOneLine.cache()
```

i. Invoke an action to cache the RDD and note the execution time:

```scala
taxiMedCountsOneLine.count()
```

j. Run the count action again to see the difference:

```scala
taxiMedCountsOneLine.count()
```

### Note
Remember to follow each step as outlined for optimal learning and practice. Each section builds upon the previous ones.
```
