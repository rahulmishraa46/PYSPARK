

---

# Introduction to PySpark: Distributed Data Processing with Python  

![PySpark Logo](https://spark.apache.org/images/spark-logo-trademark.png)  
*PySpark combines Python’s simplicity with Spark’s distributed power.*

## Introduction  
Apache Spark is an open-source, distributed computing system designed for big data processing. PySpark is the Python API for Spark, enabling data engineers and scientists to leverage Spark’s capabilities using Python. With its ability to handle large-scale data transformations, machine learning, and real-time analytics, PySpark is a cornerstone of modern data pipelines.  

📹 **Watch Intro**: [What is PySpark? (YouTube)](https://www.youtube.com/watch?v=VZ7EHLdrVo0&pp=0gcJCdgAo7VqN5tD)  

### Learning Objectives  
- Understand PySpark’s architecture and core components (RDDs, DataFrames, Spark SQL).  
- Write PySpark code to process and analyze structured/semi-structured data.  
- Optimize jobs using partitioning, caching, and parallel execution.  
- Integrate PySpark with other tools (e.g., Pandas, Hadoop, cloud storage).  

---

## Core Components of PySpark  

### 1. Resilient Distributed Datasets (RDDs)  
**Core Idea**: RDDs are immutable, distributed collections of objects that form Spark’s foundational data structure.  





### Key Notes for GitHub:
1. **RDD Navigation image** :
   ```mermaid
   graph LR
       A[RDD: myRDD] --> B[Partitions]
       B --> C[Partition 1]
       B --> D[Partition 2]
       B --> E[Partition 3]
       B --> F[...]
       
       C --> G[Memory]
       D --> H[Memory]
       E --> I[Disk]
       
       style A fill:#f9f,stroke:#333
       style G fill:#9f9
       style H fill:#9f9
       style I fill:#f99
   
  
**Key Features**:  
- Fault-tolerant via lineage (recomputes lost partitions).  
- Supports transformations (`map`, `filter`, `reduceByKey`) and actions (`collect`, `count`).  
- Low-level control for custom operations.  

**Example**:  
```python
rdd = sc.parallelize([1, 2, 3])  # Create RDD
squared = rdd.map(lambda x: x * x)  # Transformation
print(squared.collect())  # Action: [1, 4, 9]
```

📹 **RDD Tutorial**: [PySpark RDDs Explained (YouTube)](https://www.youtube.com/watch?v=VlZYq0oHCk4)  

---

### 2. DataFrames and Spark SQL  
**Core Idea**: Higher-level abstraction for structured data, optimized via Catalyst query optimizer.  

 
*DataFrames provide schema enforcement and SQL-like queries.*

**Key Features**:  
- SQL-like syntax for queries (e.g., `df.filter("age > 30")`).  
- Integration with Parquet, JSON, CSV, and Hive.  
- Schema enforcement for error handling.  

**Example**:  
```python
df = spark.read.json("data.json")  # Load data
df.select("name", "age").show()  # Query
```

📹 **DataFrame Tutorial**: [PySpark DataFrames (YouTube)](https://www.youtube.com/watch?v=ragDjDfwWGM)  

---

### 3. Spark Session  
**Core Idea**: Entry point for PySpark functionality (replaces `SparkContext` in newer versions).  

**Key Features**:  
- Configures application settings (e.g., `master("local[*]")`).  
- Manages DataFrames, SQL, and streaming contexts.  

**Example**:  
```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("Demo").getOrCreate()
```

---

## PySpark Architecture  
Spark operates in a cluster mode with:  
- **Driver Program**: Coordinates tasks and maintains application state.  
- **Executors**: Worker nodes that execute tasks in parallel.  
- **Cluster Manager**: Manages resources (e.g., YARN, Kubernetes, standalone).  

![Spark Architecture](https://spark.apache.org/docs/latest/img/cluster-overview.png)  

📹 **Architecture Deep Dive**: [How Spark Works (YouTube)](https://www.youtube.com/watch?v=F3-8jFI_OPY)  

---

## Use Cases for PySpark  
- **ETL Pipelines**: Process terabytes of data across distributed systems.  
- **Machine Learning**: Scale scikit-learn workflows via `MLlib`.  
- **Real-Time Analytics**: Use `Structured Streaming` for Kafka or Kinesis.  
- **Data Lake Querying**: Analyze data in Delta Lake or Iceberg.  

 

---

## Building a PySpark Job: 5-Step Guide  

| Step | Description | Example |  
|------|-------------|---------|  
| 1. Load Data | Read from source (HDFS, S3, JDBC). | `df = spark.read.csv("s3://bucket/data.csv")` |  
| 2. Transform | Clean, filter, aggregate. | `df.groupBy("department").avg("salary")` |  
| 3. Optimize | Cache, repartition, broadcast joins. | `df.cache()` |  
| 4. Execute | Trigger actions (write, collect). | `df.write.parquet("output/")` |  
| 5. Monitor | Check Spark UI for stages/tasks. | `http://localhost:4040` |  

 

---

## Key Optimisation Techniques  
- **Partitioning**: Balance data skew with `repartition()` or `coalesce()` to avoid over-partitioning.  
- **Broadcast Variables**: Use `broadcast()` for small datasets in joins to reduce shuffling.  
- **Avoid Shuffles**: Minimize expensive operations like `groupByKey`; prefer `reduceByKey` for aggregations.  
- **Caching**: Persist frequently used DataFrames/RDDs in memory with `df.cache()` or `df.persist()`.  
- **Predicate Pushdown**: Leverage Spark SQL’s optimizer to filter data early in queries (e.g., `df.filter("date > '2023-01-01'")`).  
- **Tungsten Optimizations**: Enable off-heap memory and optimized serialization via `spark.sql.tungsten.enabled=true`.  
- **Join Strategies**: Use broadcast joins for small tables (`spark.conf.set("spark.sql.autoBroadcastJoinThreshold", "10MB")`) and bucketing for large tables.  
- **Parallelism**: Adjust `spark.default.parallelism` to match cluster cores (e.g., `spark.conf.set("spark.default.parallelism", "200")`).  
- **Memory Management**: Tune `spark.executor.memory` and `spark.memory.fraction` to avoid spills to disk.  
- **File Formats**: Use columnar formats (Parquet/ORC) with predicate pushdown and partition pruning.  

📹 **Deep Dive**: [Advanced PySpark Tuning (YouTube)](https://www.youtube.com/watch?v=5peQThvQmQk)  

--- 

### Why These Additions?  
1. **Coverage**: Expands from basic to advanced optimizations (Tungsten, bucketing, memory tuning).  
2. **Practicality**: Directly actionable configs (e.g., `autoBroadcastJoinThreshold`).  
3. **Performance Focus**: Addresses common bottlenecks (shuffles, spills, joins).  
  

📹 **Optimization Tips**: [PySpark Performance (YouTube)](https://www.youtube.com/watch?v=E7GSNb9rJHs)  

---

## PySpark vs. Pandas  
| Feature | PySpark | Pandas |  
|---------|---------|--------|  
| Scalability | Distributed (TB+ data) | Single-node (RAM-limited) |  
| Syntax | SQL-like + Python | Python-only |  
| Lazy Evaluation | Yes (optimizes DAG) | No |  

 

---

## Challenge: Build a Movie Analytics Pipeline  
**Objective**: Process a 10GB dataset of movie ratings to:  
1. Calculate average ratings per genre.  
2. Identify top 10 movies by revenue.  
3. Write results to Parquet.  

**Dataset**: [MovieLens Dataset](https://grouplens.org/datasets/movielens/)  

📹 **Tutorial**: [PySpark Project Walkthrough (YouTube)](https://www.youtube.com/watch?v=W4mjfJ6XmMA)  

---

## Resources  
- **Documentation**: [Spark Official Docs](https://spark.apache.org/docs/latest/api/python/)  
- **Books**: *Learning PySpark* by Tomasz Drabas.  
- **Tutorials**: [Databricks PySpark Guide](https://docs.databricks.com/spark/latest/dataframes-datasets/introduction-to-dataframes-python.html).  
- **YouTube Playlist**: [PySpark for Beginners](https://www.youtube.com/watch?v=EB8lfdxpirM).  

--- 




