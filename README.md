
# Spark LAB Basics

## Overview
This guide outlines the steps to use a Docker image of Spark in standalone mode.

**Spark Version**: 2.4.4

### Getting Started with the Docker Image

#### To get the image:
1. Pull the Docker image:
   ```
   docker pull galexiou/sparklab:latest
   ```
2. Run the Docker image:
   ```
   docker run -d --name sparklab  -e SPARK_MODE=master galexiou/sparklab
   ```

#### Access to shell:
1. Enter the Docker shell:
   ```
   docker exec -it sparklab /bin/bash
   ```
2. Navigate to the root:
   ```
   cd /
   ```
3. Start the Spark shell:
   ```
   spark-shell
   ```

## Lab Exercises

### 1. RDD Operations - Basics
```scala
val lines = sc.textFile("/labdata/README.md")
val lineLengths = lines.map(s => s.length)
val totalLengths = lineLengths.reduce((a, b) => a + b)
val wordCounts = lines.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey((a, b) => a + b)
wordCounts.collect()
wordCounts.toDebugString
```

### 2. DAG
```scala
val logFile = sc.textFile("/labdata/log.txt")
val errors = logFile.filter(_.startsWith("ERROR"))
val messages = errors.map(_.split("	")).map(r => r(1))
messages.cache()
messages.filter(_.contains("mysql")).count()
messages.filter(_.contains("php")).count()
```

### 3. Scala
```scala
val pair = ('a', 'b')
pair._1
pair._2
```

### 4. Key-value Pairs
```scala
val readme = sc.textFile("/labdata/README.md")
readme.count()
readme.first()
// More operations...
```

### 5. RDD Operations: Analysing a Log File
```scala
val logFile = sc.textFile("/labdata/notebook.log")
val info = logFile.filter(line => line.contains("INFO"))
info.count()
info.filter(line => line.contains("spark")).count()
info.filter(line => line.contains("spark")).collect().foreach(println)
println(info.toDebugString)
```

### 6. Joining RDDs
```scala
val readmeFile = sc.textFile("/labdata/README.md")
val pom = sc.textFile("/labdata/pom.xml")
// More operations...
```

### 7. Shared Variables Broadcast / Accumulator
```scala
val broadcastVar = sc.broadcast(Array(1, 2, 3))
broadcastVar.value
// More operations...
```

### 8. Scala - Example Application
```scala
val taxi = sc.textFile("/labdata/nyctaxi.txt")
taxi.take(5).foreach(println)
// More operations...
```

### Note
Remember to follow each step as outlined for optimal learning and practice. Each section builds upon the previous ones.
