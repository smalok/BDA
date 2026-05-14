# 🚀 UNIT 2: Big Data Processing

> **Goal**: Understand Big Data, Hadoop, HDFS, MapReduce, and Apache Spark — explained with fun analogies!

---

## 📌 TABLE OF CONTENTS
1. [What is Big Data?](#1-what-is-big-data)
2. [Big Data Characteristics (5 V's)](#2-big-data-characteristics-5-vs)
3. [Evolution of Big Data](#3-evolution-of-big-data)
4. [Big Data Applications](#4-big-data-applications)
5. [Hadoop – Introduction & Architecture](#5-hadoop--introduction--architecture)
6. [HDFS – Hadoop Distributed File System](#6-hdfs--hadoop-distributed-file-system)
7. [MapReduce Programming Model](#7-mapreduce-programming-model)
8. [Loading Data into HDFS](#8-loading-data-into-hdfs)
9. [Apache Spark – Introduction](#9-apache-spark--introduction)
10. [Spark Architecture](#10-spark-architecture)
11. [Spark Components](#11-spark-components)
12. [RDD – Resilient Distributed Datasets](#12-rdd--resilient-distributed-datasets)
13. [Data Sharing using Spark RDD](#13-data-sharing-using-spark-rdd)
14. [Spark Word Count Example](#14-spark-word-count-example)

---

## 1. What is Big Data?

### 🍕 Real-Life Analogy: The Traffic Problem
Imagine you're the traffic commissioner of a mega-city. Every second:
- 10 million cars are moving
- 1 million CCTV cameras are recording
- 500,000 GPS devices are sending locations
- 200,000 traffic lights are logging data

Can you store all this in ONE computer? **NO!** That's Big Data — data so massive and fast that traditional tools can't handle it.

### Definition
> **Big Data** refers to extremely large and complex datasets that cannot be processed, stored, or analyzed using traditional database tools within a reasonable time.

---

## 2. Big Data Characteristics (5 V's)

### 🍕 Analogy: Running a Mega Shopping Mall

| V | Meaning | Mall Analogy | Example |
|---|---------|-------------|---------|
| **Volume** 📦 | Massive amounts of data | Millions of products in the mall | Facebook generates 4 PB of data daily |
| **Velocity** ⚡ | Speed of data generation | 1000 customers entering per minute | Stock market generates millions of trades/second |
| **Variety** 🎨 | Different types of data | Clothes, food, electronics — all different | Text, images, videos, sensor data, logs |
| **Veracity** ✅ | Quality/trustworthiness | Some products are genuine, some are fake | Fake reviews, incomplete sensor readings |
| **Value** 💎 | Usefulness of data | What products sell most? | Fraud detection, customer behavior insights |

```
        ┌──────────┐
        │  VOLUME  │ ← HOW MUCH data?
        └────┬─────┘
             │
    ┌────────┼────────┐
    │        │        │
┌───┴──┐ ┌──┴───┐ ┌──┴────┐
│VELOC │ │VARIE │ │VERAC  │
│-ITY  │ │-TY   │ │-ITY   │
└───┬──┘ └──┬───┘ └──┬────┘
    │        │        │
    └────────┼────────┘
             │
        ┌────┴─────┐
        │  VALUE   │ ← WHAT'S USEFUL?
        └──────────┘
```

### Types of Data:
- **Structured**: Tables with rows and columns (SQL databases) → Like a perfectly organized spreadsheet
- **Semi-structured**: JSON, XML → Like a semi-organized notebook
- **Unstructured**: Videos, images, tweets → Like random sticky notes everywhere

---

## 3. Evolution of Big Data

```
1970s: Flat files & databases (few KBs)
         ↓
1990s: Data Warehouses (GBs)
         ↓
2000s: Web 2.0, Social Media explosion (TBs)
         ↓
2006: Hadoop born at Yahoo! (PBs)
         ↓
2010s: Real-time processing, Spark (PBs in seconds)
         ↓
2020s+: AI + Big Data + Cloud (Exabytes)
```

---

## 4. Big Data Applications

### 🍕 Where is Big Data Used?

| Industry | How Big Data Helps |
|----------|-------------------|
| **Education** | Personalized learning, grade prediction |
| **Healthcare** | Patient monitoring, drug discovery |
| **Banking** | Fraud detection, risk analysis |
| **E-Commerce** | Product recommendations (Amazon: "Customers also bought...") |
| **Government** | Welfare programs, cybersecurity |
| **Social Media** | News feed ranking, ad targeting |
| **Insurance** | Claim fraud detection, customer insights |

---

## 5. Hadoop – Introduction & Architecture

### 🍕 Analogy: An Army of Ants
One ant can carry one grain of rice. But a **colony of 10,000 ants** can move an entire mountain of rice! That's Hadoop — it takes a massive job and distributes it across thousands of cheap computers working together.

### What is Hadoop?
> **Apache Hadoop** is an open-source framework for storing and processing large datasets across clusters of commodity (cheap) computers.

### Key Features:
- **Scalable**: Add more machines → handle more data
- **Fault-tolerant**: If one machine dies, data is safe on other copies
- **Cost-effective**: Uses cheap commodity hardware, not expensive servers
- **Open-source**: Free to use

### 4 Core Modules of Hadoop:

```
┌─────────────────────────────────────────┐
│           HADOOP ECOSYSTEM              │
├─────────────┬───────────────────────────┤
│ Hadoop      │ Libraries & utilities     │
│ Common      │ needed by other modules   │
├─────────────┼───────────────────────────┤
│ HDFS        │ Distributed file storage  │
│             │ (where data lives)        │
├─────────────┼───────────────────────────┤
│ YARN        │ Resource management       │
│             │ (who gets to run what)    │
├─────────────┼───────────────────────────┤
│ MapReduce   │ Data processing engine    │
│             │ (how work gets done)      │
└─────────────┴───────────────────────────┘
```

---

## 6. HDFS – Hadoop Distributed File System

### 🍕 Analogy: A School Library System

Imagine you have a **1000-page textbook** but only **small lockers** (each fits 128 pages):
- You **tear the book into 8 chunks** (128 pages each)
- You put each chunk in a **different locker across different buildings**
- You keep a **master register** noting which chunk is in which locker
- You make **3 photocopies of each chunk** and put them in different buildings (in case one building catches fire)

That's exactly how HDFS works!

### HDFS Architecture (Master-Slave):

```
          ┌─────────────────────┐
          │     NAMENODE         │  ← MASTER (Librarian)
          │  (Stores metadata)   │     Knows WHERE everything is
          │  File names, block   │     Does NOT store actual data
          │  locations, perms    │
          └──────────┬──────────┘
                     │
        ┌────────────┼────────────┐
        │            │            │
  ┌─────┴─────┐ ┌───┴───┐ ┌─────┴─────┐
  │ DATANODE 1│ │DATAN 2│ │ DATANODE 3│  ← SLAVES (Lockers)
  │ Block A   │ │Block B│ │ Block C   │     Store actual data
  │ Block B'  │ │Block C│ │ Block A'  │     Send heartbeats to
  │           │ │Block A│ │ Block B'  │     NameNode
  └───────────┘ └───────┘ └───────────┘
```

### Key Concepts:

| Concept | Meaning | Analogy |
|---------|---------|---------|
| **Block** | Data is split into 128MB chunks | Pages of a book |
| **NameNode** | Master — stores metadata (file names, locations) | Librarian with the register |
| **DataNode** | Slave — stores actual data blocks | Lockers in different buildings |
| **Replication** | Each block is copied 3 times (default) | 3 photocopies of each chunk |
| **Heartbeat** | DataNodes send "I'm alive" signals to NameNode | Students checking in with the librarian |
| **Fault Tolerance** | If one DataNode fails, data is retrieved from replica | If one building burns, copies exist elsewhere |
| **Data Locality** | Process data where it's stored (avoid moving data) | Read the book in the building where it's stored |

### HDFS Advantages:
- ✅ Handles massive datasets (Petabytes)
- ✅ Highly scalable — just add more DataNodes
- ✅ Fault tolerant — data is replicated
- ✅ Cost-effective — runs on commodity hardware

---

## 7. MapReduce Programming Model

### 🍕 Analogy: Counting Votes in an Election

Imagine India's election — 1 billion votes! Can ONE person count them all? NO!

**MapReduce approach:**
1. **MAP Phase**: Send ballot boxes to 1000 different rooms. Each room counts their own box.
2. **SHUFFLE Phase**: Group all counts by candidate name
3. **REDUCE Phase**: Add up the counts for each candidate

```
ELECTION ANALOGY:

Ballot Box 1: [Modi, Rahul, Modi, Modi]
Ballot Box 2: [Rahul, Modi, Rahul, Rahul]
Ballot Box 3: [Modi, Rahul, Modi, Modi]

MAP PHASE (each room counts independently):
  Room 1 → (Modi,1), (Rahul,1), (Modi,1), (Modi,1)
  Room 2 → (Rahul,1), (Modi,1), (Rahul,1), (Rahul,1)
  Room 3 → (Modi,1), (Rahul,1), (Modi,1), (Modi,1)

SHUFFLE PHASE (group by candidate):
  Modi:  [1,1,1,1,1,1,1]
  Rahul: [1,1,1,1,1]

REDUCE PHASE (add up):
  Modi:  7 votes ✅
  Rahul: 5 votes
```

### Word Count Example (Classic MapReduce):

```
INPUT FILE:
  "Hello World Hello Big Data World"

STEP 1: MAP (split into key-value pairs)
  (Hello, 1)
  (World, 1)
  (Hello, 1)
  (Big, 1)
  (Data, 1)
  (World, 1)

STEP 2: SHUFFLE (group by key)
  Hello → [1, 1]
  World → [1, 1]
  Big   → [1]
  Data  → [1]

STEP 3: REDUCE (sum values)
  Hello → 2
  World → 2
  Big   → 1
  Data  → 1

OUTPUT: {Hello:2, World:2, Big:1, Data:1}
```

### MapReduce Architecture:

```
Client submits job
       ↓
┌─────────────────┐
│   JOB TRACKER   │  ← Master: splits job, assigns tasks
│   (Master)      │
└────────┬────────┘
         │
   ┌─────┼─────┐
   ↓     ↓     ↓
┌──┴──┐┌─┴──┐┌─┴──┐
│TASK ││TASK││TASK│  ← Workers: execute map/reduce tasks
│TRACK││TRAK││TRAK│
│ER 1 ││ER 2││ER 3│
└─────┘└────┘└────┘
```

---

## 8. Loading Data into HDFS

### 🍕 Analogy: Moving Furniture into a Warehouse
Before you can organize furniture, you need to **move it into** the warehouse. Similarly, before processing data, you must load it into HDFS.

### Common Methods:

#### 1. HDFS Shell Commands (Most Common):
```bash
# Copy file FROM local TO HDFS
hdfs dfs -put localfile.txt /user/hadoop/

# Another way to copy (same as put)
hdfs dfs -copyFromLocal localfile.txt /user/hadoop/

# MOVE file (deletes from local after transfer)
hdfs dfs -moveFromLocal localfile.txt /user/hadoop/

# Other useful commands:
hdfs dfs -mkdir /user/hadoop/newdir     # Create directory
hdfs dfs -ls /user/hadoop/              # List files
hdfs dfs -cat /user/hadoop/file.txt     # View file content
hdfs dfs -cp /source /destination       # Copy within HDFS
hdfs dfs -mv /source /destination       # Move within HDFS
```

#### 2. Web Interface:
- Access NameNode UI at `http://localhost:9870`
- Upload via browser (good for testing)

#### 3. Apache Sqoop (from databases):
```bash
sqoop import \
  --connect jdbc:mysql://localhost/dbname \
  --username root \
  --table student \
  --target-dir /hdfs/student
```

#### 4. Apache Flume (for streaming data):
- Continuously ingests log files, events into HDFS

---

## 9. Apache Spark – Introduction

### 🍕 Analogy: MapReduce is a Bus, Spark is a Bullet Train

**MapReduce** reads data from disk, processes it, writes back to disk, reads again... (slow bus stopping at every station)

**Spark** keeps data **in memory (RAM)** — processes it 100x faster! (bullet train — non-stop)

```
MapReduce:  Disk → Process → Disk → Process → Disk → Process → Disk
                (slow, lots of disk I/O)

Spark:      RAM → Process → RAM → Process → RAM → Output
                (fast, in-memory processing)
```

### What is Apache Spark?
> Apache Spark is an open-source, **in-memory** cluster computing framework for fast, general-purpose big data processing.

### Key Features:

| Feature | Description |
|---------|-------------|
| **Speed** ⚡ | 100x faster than MapReduce (in-memory) |
| **Easy to Use** 📝 | Supports Java, Scala, Python, R, SQL |
| **General Purpose** 🔧 | Batch processing, streaming, ML, graph analysis |
| **Fault Tolerant** 🛡️ | Automatically recovers lost data |
| **Runs Everywhere** 🌐 | Hadoop, Mesos, Kubernetes, standalone, cloud |

### Uses of Spark:
- **Data Integration**: ETL processes (faster & cheaper)
- **Stream Processing**: Real-time log analysis, fraud detection
- **Machine Learning**: Training ML models on large datasets
- **Interactive Analytics**: Quick exploratory queries

---

## 10. Spark Architecture

### 🍕 Analogy: A Movie Production

```
Director (Driver Program)  → Plans the entire movie
          ↓
Producer (Cluster Manager) → Arranges actors, sets, equipment
          ↓
Actors (Worker Nodes)      → Actually perform the scenes
  └── Scenes (Executors)   → Individual tasks each actor does
       └── Takes (Tasks)   → Smallest unit of work
```

### Architecture Diagram:

```
┌──────────────────────┐
│   DRIVER PROGRAM     │  ← Your main() program
│   (SparkContext)     │     Creates the execution plan
└──────────┬───────────┘
           │
    ┌──────┴──────┐
    │   CLUSTER   │  ← Manages resources
    │   MANAGER   │     (YARN/Mesos/Standalone)
    └──────┬──────┘
           │
    ┌──────┼──────────┐
    │      │          │
┌───┴──┐ ┌┴────┐ ┌───┴──┐
│WORKER│ │WORK │ │WORKER│  ← Machines in the cluster
│NODE 1│ │ER 2 │ │NODE 3│
├──────┤ ├─────┤ ├──────┤
│Exec  │ │Exec │ │Exec  │  ← Processes that run tasks
│Task 1│ │Task2│ │Task 3│  ← Units of work
└──────┘ └─────┘ └──────┘
```

### Key Components:

| Component | Role | Analogy |
|-----------|------|---------|
| **Driver Program** | Runs main(), creates SparkContext | Director of a movie |
| **SparkContext** | Entry point, coordinates everything | Director's megaphone |
| **Cluster Manager** | Allocates resources (CPU, RAM) | Producer who arranges everything |
| **Worker Node** | Machine that runs the work | Actor performing scenes |
| **Executor** | Process on worker that runs tasks | A specific scene being performed |
| **Task** | Smallest unit of work | A single take/shot |

### Two Key Abstractions:
1. **RDD** (Resilient Distributed Dataset) — the data structure
2. **DAG** (Directed Acyclic Graph) — the execution plan

---

## 11. Spark Components

```
┌─────────────────────────────────────────┐
│              SPARK ECOSYSTEM            │
├─────────────────────────────────────────┤
│                                         │
│  ┌─────────┐ ┌──────────┐ ┌─────────┐  │
│  │Spark SQL│ │ Spark    │ │  MLlib  │  │
│  │(SQL     │ │Streaming │ │(Machine │  │
│  │queries) │ │(Real-time│ │Learning)│  │
│  └────┬────┘ └────┬─────┘ └────┬────┘  │
│       │           │            │        │
│  ┌────┴───────────┴────────────┴────┐   │
│  │          SPARK CORE              │   │
│  │  (Task scheduling, Memory mgmt, │   │
│  │   Fault recovery, I/O)          │   │
│  └──────────────────────────────────┘   │
│                                         │
│  ┌──────────┐                           │
│  │  GraphX  │  (Graph processing)       │
│  └──────────┘                           │
└─────────────────────────────────────────┘
```

| Component | What It Does | Example Use Case |
|-----------|-------------|-----------------|
| **Spark Core** | Foundation — scheduling, memory, fault recovery | Running any Spark job |
| **Spark SQL** | SQL queries on structured data | `SELECT * FROM sales WHERE amount > 1000` |
| **Spark Streaming** | Process real-time data streams | Live Twitter sentiment analysis |
| **MLlib** | Machine learning algorithms | Recommendation engine (Netflix) |
| **GraphX** | Graph processing & analytics | Social network analysis (Facebook friends) |

---

## 12. RDD – Resilient Distributed Datasets

### 🍕 Analogy: A Deck of Cards Split Among Friends

Imagine you have a deck of 52 cards. You give:
- 13 cards to Friend A (Node 1)
- 13 cards to Friend B (Node 2)
- 13 cards to Friend C (Node 3)
- 13 cards to Friend D (Node 4)

Now, each friend can sort their cards **simultaneously** (parallel processing). If Friend B drops their cards, you can re-deal from the original deck (**fault tolerance**). That deck is an **RDD**!

### What is RDD?
> **RDD (Resilient Distributed Dataset)** is Spark's fundamental data structure — an immutable, distributed collection of objects that can be processed in parallel.

- **Resilient**: Can recover from failures (re-compute lost partitions)
- **Distributed**: Data spread across multiple nodes
- **Dataset**: Collection of records

### Creating RDDs (2 Ways):

```python
# Way 1: Parallelize existing data
data = [1, 2, 3, 4, 5]
rdd = sc.parallelize(data)

# Way 2: Load from external file
rdd = sc.textFile("hdfs://path/to/file.txt")
```

### RDD Operations (2 Types):

#### 1️⃣ Transformations (Lazy — don't execute immediately)
Creates a NEW RDD from an existing one. Spark just **records the recipe** but doesn't cook yet.

| Transformation | What It Does | Example |
|---------------|-------------|---------|
| `map()` | Apply function to each element | `rdd.map(x => x * 2)` → [2,4,6,8,10] |
| `filter()` | Keep only matching elements | `rdd.filter(x => x > 3)` → [4,5] |
| `flatMap()` | Map + flatten (one-to-many) | Split sentences into words |
| `reduceByKey()` | Combine values with same key | Sum up word counts |
| `union()` | Combine two RDDs | Merge two datasets |

#### 2️⃣ Actions (Eager — execute immediately)
Triggers actual computation and returns a result.

| Action | What It Does | Example |
|--------|-------------|---------|
| `collect()` | Return all elements to driver | `rdd.collect()` → [1,2,3,4,5] |
| `count()` | Count number of elements | `rdd.count()` → 5 |
| `first()` | Return first element | `rdd.first()` → 1 |
| `take(n)` | Return first n elements | `rdd.take(3)` → [1,2,3] |
| `saveAsTextFile()` | Save to file | Write output to HDFS |

### Lazy Evaluation Analogy:
🍕 Ordering a pizza:
- **Transformations** = Writing down your order ("large pizza, extra cheese, mushrooms")
- **Action** = Saying "Please deliver!" — THEN the kitchen starts cooking

```
rdd1 = sc.textFile("data.txt")        ← Lazy (recorded)
rdd2 = rdd1.filter(line => ...)       ← Lazy (recorded)
rdd3 = rdd2.map(word => ...)          ← Lazy (recorded)
result = rdd3.collect()               ← ACTION! Now everything executes!
```

---

## 13. Data Sharing using Spark RDD

### RDD Persistence (Caching)
If you use an RDD multiple times, recomputing it each time is wasteful. **Caching** stores it in memory for reuse.

```python
rdd = sc.textFile("big_data.txt")
rdd.cache()  # or rdd.persist()

# Now these two operations reuse the cached RDD:
print(rdd.count())        # First use — computes and caches
print(rdd.first())        # Second use — reads from cache (instant!)
```

### Storage Levels:

| Level | Where Stored | Speed | Space |
|-------|-------------|-------|-------|
| `MEMORY_ONLY` | RAM only | ⚡ Fastest | 💾 Uses most RAM |
| `MEMORY_AND_DISK` | RAM first, overflow to disk | ⚡ Fast | 💾 Flexible |
| `DISK_ONLY` | Disk only | 🐢 Slower | 💾 Saves RAM |
| `MEMORY_ONLY_SER` | RAM (serialized — compressed) | ⚡ Fast | 💾 Less RAM |

### Shared Variables (2 Types):

#### 1️⃣ Broadcast Variables (Read-Only)
- Send a **large read-only dataset** to all workers ONCE
- Example: A lookup table of country codes

```python
country_codes = {"IN": "India", "US": "USA", "UK": "United Kingdom"}
broadcast_codes = sc.broadcast(country_codes)

# Now every worker can access broadcast_codes.value
```

🍕 **Analogy**: Instead of photocopying a reference book for each student, the teacher puts ONE copy on the projector for everyone to see.

#### 2️⃣ Accumulators (Write-Only Counters)
- Workers can **add** to it, only driver can **read** it
- Used for counters and sums

```python
error_count = sc.accumulator(0)

def count_errors(line):
    if "ERROR" in line:
        error_count.add(1)

rdd.foreach(count_errors)
print(error_count.value)  # Total errors across all workers
```

🍕 **Analogy**: A donation box at a charity event — everyone puts money IN, only the organizer counts the TOTAL.

---

## 14. Spark Word Count Example

### Step-by-Step (with Scala):

```scala
// Step 1: Create RDD from text file
val data = sc.textFile("sparkdata.txt")

// Step 2: Split lines into words
val splitdata = data.flatMap(line => line.split(" "))

// Step 3: Map each word to (word, 1)
val mapdata = splitdata.map(word => (word, 1))

// Step 4: Reduce by key (sum counts)
val reducedata = mapdata.reduceByKey(_ + _)

// Step 5: View results
reducedata.collect()
```

### Visual Flow:
```
INPUT: "Hello World Hello Big Data World"

flatMap(split by space):
  ["Hello", "World", "Hello", "Big", "Data", "World"]

map(word → (word, 1)):
  [("Hello",1), ("World",1), ("Hello",1), ("Big",1), ("Data",1), ("World",1)]

reduceByKey(sum):
  [("Hello",2), ("World",2), ("Big",1), ("Data",1)]

OUTPUT: Hello=2, World=2, Big=1, Data=1  ✅
```

---

## 📝 UNIT 2 — IMPORTANT QUESTIONS & ANSWERS

### 🔴 8-Mark Questions:

**Q1: Explain Big Data Characteristics (5 V's).**
→ See [Section 2](#2-big-data-characteristics-5-vs) — Volume, Velocity, Variety, Veracity, Value with mall analogy.

**Q2: Explain MapReduce Programming Model.**
→ See [Section 7](#7-mapreduce-programming-model) — Election vote counting analogy, Map-Shuffle-Reduce phases, word count example.

**Q3: Explain Hadoop Architecture and HDFS Commands.**
→ See [Section 5](#5-hadoop--introduction--architecture) & [Section 6](#6-hdfs--hadoop-distributed-file-system) — NameNode/DataNode, replication, and [Section 8](#8-loading-data-into-hdfs) for commands.

**Q4: Explain Data Sharing using Spark RDD.**
→ See [Section 13](#13-data-sharing-using-spark-rdd) — Caching, Storage Levels, Broadcast Variables, Accumulators.

**Q5: Explain Spark Architecture with Word Count Example.**
→ See [Section 10](#10-spark-architecture) for architecture and [Section 14](#14-spark-word-count-example) for word count.

### 🟢 2-Mark Questions:

**Q: What is Big Data?**
> Extremely large, complex datasets that traditional tools can't handle, characterized by 5 V's.

**Q: Name the 5 V's of Big Data.**
> Volume, Velocity, Variety, Veracity, Value.

**Q: What is Hadoop?**
> Open-source framework for distributed storage (HDFS) and processing (MapReduce) of big data.

**Q: What is HDFS?**
> Hadoop Distributed File System — stores large files across multiple machines with replication for fault tolerance.

**Q: What is a NameNode?**
> The master server in HDFS that stores metadata (file names, block locations) but NOT actual data.

**Q: What is MapReduce?**
> A programming model with Map phase (parallel processing) and Reduce phase (aggregation) for large-scale data.

**Q: What is Apache Spark?**
> An in-memory cluster computing framework, 100x faster than MapReduce for big data processing.

**Q: What is an RDD?**
> Resilient Distributed Dataset — Spark's core data structure, immutable, distributed, and fault-tolerant.

**Q: Difference between Transformation and Action in RDD?**
> Transformations are lazy (create new RDD, don't execute). Actions trigger execution and return results.

**Q: What are Broadcast Variables?**
> Read-only shared variables sent to all workers once, avoiding repeated data transfer.

---

> ✅ **Unit 2 Complete!** Next up: Unit 3 — Big Data Storage Systems (Camel, Ignite, Cassandra, Kafka)!
