# 📊 UNIT 4: Hive, HBase, and Data Analytics

> **Goal**: Understand Hive, HBase, CAP Theorem, Partitioning, and Analytical Platforms — made super simple!

---

## 📌 TABLE OF CONTENTS
1. [Apache Hive – Introduction](#1-apache-hive--introduction)
2. [Hive Architecture](#2-hive-architecture)
3. [Hive Data Types and Operations](#3-hive-data-types-and-operations)
4. [Hive Partitioning and Bucketing](#4-hive-partitioning-and-bucketing)
5. [Apache HBase – Introduction](#5-apache-hbase--introduction)
6. [HBase Architecture](#6-hbase-architecture)
7. [HBase Data Model](#7-hbase-data-model)
8. [CAP Theorem](#8-cap-theorem)
9. [HBase Operations and Shell Commands](#9-hbase-operations-and-shell-commands)
10. [Hive vs HBase](#10-hive-vs-hbase)
11. [Real-Time Analytical Platforms](#11-real-time-analytical-platforms)

---

## 1. Apache Hive – Introduction

### 🍕 Analogy: SQL Translator for Hadoop

Imagine Hadoop is a **powerful but non-English-speaking worker**. You speak SQL, but Hadoop only understands MapReduce (a complicated language).

**Apache Hive** is like a **translator** — you speak SQL, Hive converts it to MapReduce, and Hadoop executes it!

```
YOU (SQL) ──► HIVE (Translator) ──► HADOOP (MapReduce) ──► Result
  "SELECT *      "Converts to          "Executes the
   FROM sales"    MapReduce job"         heavy lifting"
```

### What is Apache Hive?
> Apache Hive is a **data warehouse tool** built on top of Hadoop that allows you to query large datasets using **HiveQL** (a SQL-like language).

### Key Facts:
- Developed by **Facebook** (to query petabytes of data)
- NOT a database — it's a query engine on top of HDFS
- Best for **batch processing** (not real-time)
- HiveQL = SQL-like queries
- Converts SQL → MapReduce jobs automatically

### When to Use Hive?
- ✅ Analyzing large datasets with SQL-like queries
- ✅ ETL (Extract, Transform, Load) operations
- ✅ Data summarization and reporting
- ❌ NOT for real-time queries (too slow)
- ❌ NOT for small datasets (overhead not worth it)

---

## 2. Hive Architecture

```
┌─────────────────────────────────────────────┐
│                HIVE ARCHITECTURE            │
│                                             │
│  ┌─────────┐   ┌──────────┐   ┌─────────┐  │
│  │  CLI /  │   │  JDBC /  │   │   Web   │  │
│  │Terminal │   │  ODBC    │   │   UI    │  │
│  └────┬────┘   └────┬─────┘   └────┬────┘  │
│       │              │              │       │
│       └──────────────┼──────────────┘       │
│                      ↓                      │
│  ┌──────────────────────────────────────┐   │
│  │          HIVE SERVER (Driver)        │   │
│  │                                      │   │
│  │  ┌──────────┐  ┌──────────────────┐  │   │
│  │  │ Parser & │  │  Query Optimizer │  │   │
│  │  │ Compiler │  │  & Execution     │  │   │
│  │  └──────────┘  └──────────────────┘  │   │
│  └──────────────────┬───────────────────┘   │
│                     │                       │
│        ┌────────────┼────────────┐          │
│        ↓            ↓            ↓          │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐    │
│  │METASTORE │ │  HDFS    │ │MapReduce │    │
│  │(Schema   │ │(Data     │ │(Execution│    │
│  │ info)    │ │ storage) │ │ engine)  │    │
│  └──────────┘ └──────────┘ └──────────┘    │
└─────────────────────────────────────────────┘
```

### Components Explained:

| Component | Role | Analogy |
|-----------|------|---------|
| **Driver** | Receives queries, manages execution | Restaurant manager |
| **Compiler** | Converts HiveQL to execution plan | Chef reading the recipe |
| **Optimizer** | Finds the most efficient execution plan | Chef finding shortcuts |
| **Executor** | Runs the optimized plan | Kitchen staff cooking |
| **Metastore** | Stores table metadata (schema, location) | Menu card (what dishes exist) |
| **HDFS** | Stores actual data | The warehouse/fridge |
| **MapReduce/Tez** | Execution engine for processing | The cooking equipment |

### Metastore — The Brain of Hive:
The Metastore knows:
- Table names, columns, data types
- Where data files are stored in HDFS
- Partitioning information
- File format (text, ORC, Parquet)

---

## 3. Hive Data Types and Operations

### Data Types:

| Category | Types | Example |
|----------|-------|---------|
| **Numeric** | INT, BIGINT, FLOAT, DOUBLE, DECIMAL | `42`, `3.14` |
| **String** | STRING, VARCHAR, CHAR | `'Hello World'` |
| **Date/Time** | TIMESTAMP, DATE | `'2025-01-15'` |
| **Boolean** | BOOLEAN | `TRUE`, `FALSE` |
| **Complex** | ARRAY, MAP, STRUCT | `[1,2,3]`, `{'key':'val'}` |

### HiveQL Examples:

```sql
-- Create a database
CREATE DATABASE university;
USE university;

-- Create a table
CREATE TABLE students (
    id INT,
    name STRING,
    department STRING,
    gpa FLOAT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;

-- Load data from a file
LOAD DATA LOCAL INPATH '/home/data/students.csv'
INTO TABLE students;

-- Query data
SELECT department, AVG(gpa) as avg_gpa
FROM students
GROUP BY department
ORDER BY avg_gpa DESC;

-- Join tables
SELECT s.name, c.course_name
FROM students s
JOIN enrollments e ON s.id = e.student_id
JOIN courses c ON e.course_id = c.id;
```

### Internal vs External Tables:

| Feature | Internal (Managed) | External |
|---------|-------------------|----------|
| **Data ownership** | Hive owns the data | Hive only knows location |
| **DROP TABLE** | Deletes data + metadata | Deletes only metadata, data stays |
| **Use Case** | Temporary/intermediate tables | Production data you don't want to accidentally delete |

```sql
-- Internal table (Hive manages data)
CREATE TABLE internal_students (...);

-- External table (data stays even if table is dropped)
CREATE EXTERNAL TABLE external_students (...)
LOCATION '/user/hadoop/students/';
```

---

## 4. Hive Partitioning and Bucketing

### 🍕 Analogy: Organizing a Massive Library

#### Partitioning = Organizing by Section
Imagine a library with **1 million books**. Instead of searching through ALL books:
- **Partition by Genre**: Fiction, Non-Fiction, Science, History
- **Partition by Year**: 2020, 2021, 2022, 2023

Now, to find "Science books from 2023", you go DIRECTLY to that section!

#### Bucketing = Sub-organizing Within Sections
Within the "Science 2023" section, you further organize:
- **Bucket by Author's first letter**: A-F, G-M, N-S, T-Z

### Partitioning in Hive:

```sql
-- Create a partitioned table
CREATE TABLE sales (
    product_id INT,
    product_name STRING,
    amount FLOAT
)
PARTITIONED BY (year INT, month INT);

-- Load data into specific partition
INSERT INTO TABLE sales PARTITION (year=2025, month=1)
SELECT product_id, product_name, amount
FROM raw_sales
WHERE year = 2025 AND month = 1;
```

### How Partitioning Works in HDFS:

```
Without Partitioning:
  /user/hive/warehouse/sales/
    → ALL data in one folder (scan everything!)

With Partitioning:
  /user/hive/warehouse/sales/year=2024/month=1/   ← Only scan this!
  /user/hive/warehouse/sales/year=2024/month=2/
  /user/hive/warehouse/sales/year=2025/month=1/
  /user/hive/warehouse/sales/year=2025/month=2/
```

**Query**: `SELECT * FROM sales WHERE year=2025 AND month=1`
- Without partitioning: Scans **entire table** 😰
- With partitioning: Scans **only year=2025/month=1 folder** ⚡

### Types of Partitioning:

| Type | How It Works | When to Use |
|------|-------------|-------------|
| **Static** | YOU specify partition values during load | When you know partition values |
| **Dynamic** | Hive figures out partition values automatically | When partition values come from data |

```sql
-- Dynamic Partitioning
SET hive.exec.dynamic.partition = true;
SET hive.exec.dynamic.partition.mode = nonstrict;

INSERT INTO TABLE sales PARTITION (year, month)
SELECT product_id, product_name, amount, year, month
FROM raw_sales;
```

### Bucketing in Hive:

```sql
-- Create a bucketed table
CREATE TABLE student_bucketed (
    id INT,
    name STRING,
    department STRING
)
CLUSTERED BY (id) INTO 4 BUCKETS;
```

```
Bucketing with 4 buckets (hash of id % 4):
  Bucket 0: IDs where hash % 4 = 0
  Bucket 1: IDs where hash % 4 = 1
  Bucket 2: IDs where hash % 4 = 2
  Bucket 3: IDs where hash % 4 = 3
```

### Partitioning vs Bucketing:

| Feature | Partitioning | Bucketing |
|---------|-------------|-----------|
| **How it splits** | By column values | By hash of column |
| **Creates** | Subdirectories in HDFS | Fixed-size files |
| **Best for** | Filtering (WHERE clause) | JOINs and sampling |
| **Number of parts** | Based on unique values | Fixed number (you specify) |

---

## 5. Apache HBase – Introduction

### 🍕 Analogy: A Giant Spreadsheet in the Cloud

Imagine Google Sheets, but:
- It can handle **BILLIONS of rows**
- Each row can have **different columns** (flexible schema)
- You can read/write **one cell at a time** in milliseconds
- It's stored across 1000 machines automatically

That's **HBase** — a distributed, column-oriented NoSQL database!

### What is Apache HBase?
> Apache HBase is an open-source, **distributed, column-oriented NoSQL database** built on top of HDFS, modeled after Google's Bigtable.

### Key Properties:
- ✅ **Column-oriented** — stores data by column families
- ✅ **Real-time read/write** — millisecond access
- ✅ **Scalable** — handles billions of rows
- ✅ **Versioned** — stores multiple versions of data
- ✅ **Built on HDFS** — inherits fault tolerance

---

## 6. HBase Architecture

```
┌──────────────────────────────────────────────┐
│                  CLIENT                       │
└──────────────────┬───────────────────────────┘
                   │
         ┌─────────┼─────────┐
         ↓                   ↓
┌────────────────┐  ┌────────────────┐
│   HMASTER      │  │   ZOOKEEPER    │
│ (Master Server)│  │ (Coordinator)  │
│ - Assigns      │  │ - Tracks which │
│   regions      │  │   servers are  │
│ - Load balance │  │   alive        │
│ - Schema ops   │  │ - Stores root  │
└───────┬────────┘  │   table info   │
        │           └────────────────┘
        │
  ┌─────┼────────────────┐
  ↓     ↓                ↓
┌────┐ ┌────┐         ┌────┐
│RS 1│ │RS 2│   ...   │RS N│    RS = Region Server
├────┤ ├────┤         ├────┤
│Reg │ │Reg │         │Reg │    Each RS handles
│A   │ │C   │         │F   │    multiple regions
│Reg │ │Reg │         │Reg │
│B   │ │D   │         │G   │
└──┬─┘ └──┬─┘         └──┬─┘
   │      │              │
   └──────┼──────────────┘
          ↓
   ┌──────────────┐
   │     HDFS     │   (Actual data storage)
   └──────────────┘
```

### Components:

| Component | Role | Analogy |
|-----------|------|---------|
| **HMaster** | Manages table creation, region assignment | School principal |
| **Region Server** | Serves read/write requests for regions | Classroom teacher |
| **Region** | A chunk of table data (range of rows) | One section of students |
| **ZooKeeper** | Tracks server health, coordinates | School attendance register |
| **HDFS** | Stores actual data files | School's record room |

### Inside a Region Server:

```
Region Server
├── Region 1 (rows "a" to "m")
│   ├── MemStore (in-memory write buffer)
│   ├── BlockCache (read cache)
│   └── HFiles (on HDFS — permanent storage)
│
├── Region 2 (rows "n" to "z")
│   ├── MemStore
│   ├── BlockCache
│   └── HFiles
│
└── WAL (Write-Ahead Log — for crash recovery)
```

### Write Process:
```
1. Client writes data
   ↓
2. Written to WAL (Write-Ahead Log) first → crash recovery
   ↓
3. Written to MemStore (in memory) → fast!
   ↓
4. When MemStore fills up → flushed to HFile on HDFS
```

---

## 7. HBase Data Model

### 🍕 Analogy: A Smart Contact Book

Unlike a regular phone book (fixed columns: Name, Phone, Address), HBase is like a **smart contact book** where each person can have DIFFERENT types of info:
- Person 1: Name, Phone, Email
- Person 2: Name, Phone, Instagram, Twitter
- Person 3: Name, Phone, Address, Birthday, 3 Email addresses

### Data Model Structure:

```
┌──────────────────────────────────────────────────────────┐
│  TABLE: student_info                                      │
├──────────┬─────────────────────┬─────────────────────────┤
│ ROW KEY  │ Column Family:      │ Column Family:           │
│          │ "personal"          │ "academic"               │
├──────────┼─────────────────────┼─────────────────────────┤
│ row1     │ personal:name=Surya │ academic:dept=CSE        │
│          │ personal:age=21     │ academic:gpa=9.2         │
│          │                     │ academic:year=4          │
├──────────┼─────────────────────┼─────────────────────────┤
│ row2     │ personal:name=Rahul │ academic:dept=ECE        │
│          │ personal:city=Delhi │ academic:gpa=8.5         │
└──────────┴─────────────────────┴─────────────────────────┘
```

### Key Concepts:

| Concept | Meaning | Example |
|---------|---------|---------|
| **Row Key** | Unique identifier for each row (sorted) | `"student001"` |
| **Column Family** | Group of related columns (must be predefined) | `"personal"`, `"academic"` |
| **Column Qualifier** | Specific column within a family | `"personal:name"`, `"academic:gpa"` |
| **Cell** | Intersection of row + column | Value at specific row & column |
| **Timestamp** | Version of the data | Each cell can have multiple versions |

### Versioning:
```
Row: "student001", Column: "academic:gpa"
  Version 3 (latest): 9.2   (timestamp: 2025-05-01)
  Version 2:          8.8   (timestamp: 2025-01-01)
  Version 1:          8.5   (timestamp: 2024-06-01)
```

---

## 8. CAP Theorem

### 🍕 Analogy: The Impossible Restaurant

Imagine you want a restaurant that is:
1. **C**onsistent — Every waiter gives the exact same menu
2. **A**vailable — The restaurant is ALWAYS open, never closed
3. **P**artition Tolerant — Works even if the kitchen loses phone connection with waiters

**CAP Theorem says: You can only pick 2 out of 3!**

```
         CONSISTENCY
            /\
           /  \
          /    \
         / PICK \
        /  ONLY  \
       /   TWO!   \
      /____________\
AVAILABILITY ── PARTITION
                TOLERANCE
```

### What Does Each Mean?

| Property | Meaning | Example |
|----------|---------|---------|
| **Consistency (C)** | Every read gets the latest write | Bank balance shows same amount on all ATMs |
| **Availability (A)** | Every request gets a response | Website always responds (never "Server Down") |
| **Partition Tolerance (P)** | System works even if network splits | Two data centers can't talk but both keep working |

### The Trade-offs:

| Type | What You Get | What You Lose | Example System |
|------|-------------|---------------|----------------|
| **CA** | Consistency + Availability | Partition Tolerance | Traditional RDBMS (MySQL, PostgreSQL) |
| **CP** | Consistency + Partition Tolerance | Availability | HBase, MongoDB, Redis |
| **AP** | Availability + Partition Tolerance | Consistency | Cassandra, DynamoDB, CouchDB |

### Real-Life Examples:

**CA (MySQL)**: Your bank — always correct, always accessible, BUT only works within one data center.

**CP (HBase)**: Your Aadhaar system — must be 100% correct. If network splits, some users may get "try again later" (availability sacrificed for correctness).

**AP (Cassandra)**: Social media "likes" count — shows approximately correct number. Always available globally. If network splits, counts might be slightly different in different regions temporarily.

---

## 9. HBase Operations and Shell Commands

### Basic HBase Shell Commands:

```bash
# Start HBase shell
hbase shell

# Create a table with column families
create 'students', 'personal', 'academic'

# Insert data (put)
put 'students', 'row1', 'personal:name', 'Surya'
put 'students', 'row1', 'personal:age', '21'
put 'students', 'row1', 'academic:dept', 'CSE'
put 'students', 'row1', 'academic:gpa', '9.2'

# Read single row (get)
get 'students', 'row1'

# Scan entire table
scan 'students'

# Scan with filter
scan 'students', {COLUMNS => ['personal:name']}

# Delete a cell
delete 'students', 'row1', 'personal:age'

# Delete entire row
deleteall 'students', 'row1'

# Disable table (required before dropping)
disable 'students'

# Drop (delete) table
drop 'students'

# List all tables
list

# Describe table structure
describe 'students'

# Count rows
count 'students'
```

---

## 10. Hive vs HBase

| Feature | Hive | HBase |
|---------|------|-------|
| **Type** | Data Warehouse (SQL engine) | NoSQL Database |
| **Processing** | Batch (slow) | Real-time (fast) |
| **Query Language** | HiveQL (SQL-like) | Shell commands / Java API |
| **Best For** | Complex analytics, reporting | Fast random read/write |
| **Schema** | Schema-on-read | Schema-less (flexible) |
| **Latency** | High (minutes) | Low (milliseconds) |
| **Built On** | HDFS + MapReduce | HDFS + ZooKeeper |
| **Operations** | SELECT, JOIN, GROUP BY | GET, PUT, SCAN, DELETE |

### When to Use What?

```
Need complex SQL queries on historical data?  → HIVE
Need real-time read/write for individual rows? → HBASE
Need both?                                     → Use BOTH together!
```

---

## 11. Real-Time Analytical Platforms

### Overview of Key Platforms:

| Platform | What It Does | Use Case |
|----------|-------------|----------|
| **Apache Storm** | Real-time stream processing | Processing live tweets |
| **Apache Flink** | Stream + batch processing | Real-time fraud detection |
| **Apache Druid** | Real-time OLAP database | Real-time dashboards |
| **Presto/Trino** | Distributed SQL query engine | Query across multiple data sources |
| **Apache Impala** | Real-time SQL on Hadoop | Fast interactive queries on HDFS |

### Lambda Architecture:

```
┌─────────────────────────────────────────┐
│           LAMBDA ARCHITECTURE            │
│                                          │
│  Data Source                             │
│      │                                   │
│      ├──► BATCH LAYER (MapReduce/Hive)   │
│      │    (processes ALL data, slower)    │
│      │         │                         │
│      │    Batch Views                    │
│      │         │                         │
│      │    ┌────┴─────┐                   │
│      └──► │ SERVING  │ ◄── Merged view   │
│           │  LAYER   │                   │
│      ┌──► └──────────┘                   │
│      │                                   │
│      └──► SPEED LAYER (Spark/Storm)      │
│           (processes NEW data, real-time) │
│                │                         │
│           Real-time Views                │
└─────────────────────────────────────────┘
```

---

## 📝 UNIT 4 — IMPORTANT QUESTIONS & ANSWERS

### 🔴 8-Mark Questions:

**Q1: Explain Hive Architecture with Components.**
→ See [Section 2](#2-hive-architecture) — Driver, Compiler, Optimizer, Executor, Metastore, HDFS, MapReduce.

**Q2: Explain Partitioning and Bucketing in Hive.**
→ See [Section 4](#4-hive-partitioning-and-bucketing) — Static/Dynamic partitioning, bucketing with examples.

**Q3: Explain HBase Architecture with Components.**
→ See [Section 6](#6-hbase-architecture) — HMaster, Region Servers, ZooKeeper, HDFS, write process.

**Q4: Explain CAP Theorem with Examples.**
→ See [Section 8](#8-cap-theorem) — Consistency, Availability, Partition Tolerance, CA/CP/AP trade-offs.

**Q5: Compare Hive vs HBase.**
→ See [Section 10](#10-hive-vs-hbase) — Detailed comparison table.

### 🟢 2-Mark Questions:

**Q: What is Apache Hive?**
> A data warehouse tool on top of Hadoop that lets you query big data using SQL-like HiveQL.

**Q: What is a Metastore in Hive?**
> A central repository that stores metadata about Hive tables (schema, location, data types).

**Q: What is Partitioning in Hive?**
> Dividing table data into subdirectories based on column values for faster queries.

**Q: What is Bucketing?**
> Dividing data into fixed-size files using hash function, useful for JOINs and sampling.

**Q: What is HBase?**
> A distributed, column-oriented NoSQL database on top of HDFS for real-time read/write.

**Q: What is CAP Theorem?**
> States that a distributed system can guarantee only 2 of 3: Consistency, Availability, Partition Tolerance.

**Q: What is a Column Family in HBase?**
> A group of related columns stored together. Must be defined at table creation time.

**Q: What is a Region in HBase?**
> A contiguous range of rows in an HBase table, served by a Region Server.

---

> ✅ **Unit 4 Complete!** Final unit coming: Unit 5 — NoSQL, Data Analytics Lifecycle, and Use Cases!
