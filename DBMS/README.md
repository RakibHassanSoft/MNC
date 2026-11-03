# Complete Master Handbook: Database Management Systems (DBMS) for MNC Interviews

স্বাগতম **MNC-Grade Database Management Systems (DBMS) Masterclass Handbook**-এ।
গুগল (Google), মেটা (Meta), অ্যামাজন (Amazon), মাইক্রোসফট (Microsoft), অ্যাপল (Apple), উবার (Uber) এবং নেটফ্লিক্স (Netflix)-এর মতো টিয়ার-১ বহুজাতিক প্রযুক্তি প্রতিষ্ঠানগুলোতে সিনিয়র সফটওয়্যার ইঞ্জিনিয়ার, ব্যাকএন্ড আর্কিটেক্ট, ইনফ্রাস্ট্রাকচার ইঞ্জিনিয়ার এবং সিস্টেম ডিজাইন ইন্টারভিউতে ডেটাবেসের অভ্যন্তরীণ মেকানিজম (Storage Engine, Indexing, Concurrency, Distributed Consistency, Recovery, Query Optimization) নিয়ে অত্যন্ত গভীর, তাত্ত্বিক ও সুনির্দিষ্ট সিস্টেম লেভেল প্রশ্ন করা হয়।

এই হ্যান্ডবুকটি এমনভাবে সংকলন করা হয়েছে যাতে প্রতিটি অধ্যায় সম্পূর্ণ স্বয়ংসম্পূর্ণ, গাণিতিক প্রমাণ (Mathematical Proofs), অ্যালগরিদমিক বিশ্লেষণ (Big-O Time & Space), অভ্যন্তরীণ আর্কিটেকচার ব্লুপ্রিন্ট (ASCII Diagrams), বাস্তবসম্মত সংখ্যাভিত্তিক হ্যান্ড-ক্যালকুলেশন এবং প্রোডাকশন এসকিউএল ও পাইথন ট্রেইস কোড সহ নিখুঁতভাবে বোঝা যায়।

---

## 🗺️ সম্পূর্ণ ডিবিএমএস ১০-ফেজ কারিকুলাম ম্যাপ (Master Curriculum Map)

```
                                      [ MNC DBMS MASTER CURRICULUM ]
                                                     |
      +----------------------------------------------+----------------------------------------------+
      |                                                                                             |
[ Phase 01: Architecture, Relational Model & Keys ]             [ Phase 02: Advanced SQL & Execution Engine ]
  - File System vs DBMS (Internal OS Bottlenecks)                 - Window Functions (ROWS vs RANGE frames)
  - 3-Schema ANSI-SPARC & Data Independence                       - Recursive CTEs (Cycle detection & graphs)
  - Codd's 12 Rules & Relational Completeness                     - Query Parsing, Rewriting & CBO Pipeline
  - Relational Algebra, TRC & DRC Formalisms                      - EXPLAIN ANALYZE Cost Estimation Math
  - Comprehensive Key Taxonomy (Natural vs Surrogate)             - Complex MNC SQL Interview Patterns
      |                                                                                             |
      +----------------------------------------------+----------------------------------------------+
      |                                                                                             |
[ Phase 03: Normalization & Schema Design ]                     [ Phase 04: Storage Architecture & Buffer Pool ]
  - Functional Dependencies & Armstrong's Axioms Proofs           - Hardware Hierarchy & Storage Latency Numbers
  - Canonical Cover & Minimal Cover Algorithm                     - Row-Store (NSM) vs Column-Store (DSM) vs PAX
  - Normal Forms: 1NF, 2NF, 3NF, BCNF, 4NF, 5NF, DKNF             - Slotted-Page Layout & Record Identification
  - Heath's Theorem Lossless-Join & Dependency Preservation       - Data Compression (Dictionary, RLE, Bit-packing)
  - OLTP (3NF) vs OLAP (Star/Snowflake) Trade-offs                - Buffer Pool Management (LRU, Clock, 2Q)
      |                                                                                             |
      +----------------------------------------------+----------------------------------------------+
      |                                                                                             |
[ Phase 05: Indexing Mastery & B+ Trees ]                       [ Phase 06: LSM-Trees & High-Write Engines ]
  - Why BST/AVL Fails on Disk (Height & I/O Math)                 - Random vs Sequential Disk Write Physics
  - B-Tree vs B+ Tree (Node split, merge & borrow math)           - LSM-Tree Components: MemTable, WAL, SSTables
  - Clustered, Secondary & Covering Indexes                       - Bloom Filters Math (Optimal Hash & Bit size)
  - Extendible & Linear Hash Indexing                             - Compaction: Leveled vs Size-Tiered
  - Specialized Indexes: Bitmap, GIN, GiST, BRIN, Spatial (H3)    - Write, Read & Space Amplification Trade-offs
      |                                                                                             |
      +----------------------------------------------+----------------------------------------------+
      |                                                                                             |
[ Phase 07: Query Optimization & Join Mechanics ]               [ Phase 08: Transactions, ACID & Concurrency ]
  - Relational Algebra Equivalence Transformation Rules           - Formal ACID Definitions & Failure Modes
  - Cost-Based Optimizer (CBO) Selectivity Math                   - The 5 Concurrency Anomalies (Dirty to Write Skew)
  - Join Enumeration: System-R Dynamic Programming                - Precedence Graph & Conflict Serializability
  - Physical Joins: Nested Loop, Hash, Sort-Merge                 - ANSI Isolation Levels vs Snapshot Isolation (SSI)
  - Cost Model Equations & Execution Trace                        - 2PL, Multi-Granularity Locking & MVCC
      |                                                                                             |
      +----------------------------------------------+----------------------------------------------+
      |                                                                                             |
[ Phase 09: Crash Recovery, WAL & ARIES ]                       [ Phase 10: Distributed DBs, NoSQL & System Design ]
  - Buffer Management: Steal/No-Force Policies                    - CAP & PACELC Theorems Formal Analysis
  - Write-Ahead Logging (WAL) Protocol Invariants                 - Consistent Hashing with Virtual Nodes Math
  - ARIES Protocol: Analysis, Redo, Undo with CLRs                - Replication & Quorum Consensus (R + W > N)
  - Checkpointing (Naive vs Fuzzy) & Crash Traces                 - Distributed Consensus: Raft vs Paxos
  - Replication: Physical Streaming vs Logical CDC                - Distributed Tx: 2PC vs Saga; Spanner TrueTime
```

---

## 📚 অধ্যায়ভিত্তিক সূচিপত্র ও ডিরেক্ট ফাইল লিঙ্ক

| ফেজ ও ফাইল লিঙ্ক | মূল বিষয়বস্তু ও প্রযুক্তিগত গভীরতা |
| :--- | :--- |
| [Phase 01: Core Architecture, Relational Model & Keys](file:///g:/MNC/DBMS/Phase_01_Architecture_and_Relational_Model.md) | ফাইল সিস্টেমের ৭টি অভ্যন্তরীণ দুর্বলতা, ৩-লেয়ার ANSI-SPARC আর্কিটেকচার, কডের ১২টি গোল্ডেন রুলস, রিলেশনাল অ্যালজেব্রা, টাপল রিলেশনাল ক্যালকুলাস (TRC), ডোমেইন রিলেশনাল ক্যালকুলাস (DRC), এবং কি-সমূহের সম্পূর্ণ শ্রেণিবিন্যাস (Natural, Composite, Surrogate: UUID v4 vs v7, ULID, Snowflake)। |
| [Phase 02: Advanced SQL & Execution Engine](file:///g:/MNC/DBMS/Phase_02_SQL_Mastery_and_Query_Optimization.md) | উইন্ডো ফাংশন (`ROW_NUMBER`, `RANK`, `DENSE_RANK`, `NTILE`, `LEAD`, `LAG`, `ROWS` বনাম `RANGE`), রিকার্সিভ সিটিই (গ্রাফ ট্রাভার্সাল ও সাইকেল ডিটেকশন), কুয়েরি এক্সিকিউশন প্ল্যান ও `EXPLAIN (ANALYZE, BUFFERS)`, এবং জটিল ইন্টারভিউ সমস্যা (Gaps & Islands, YoY Growth, Running Totals)। |
| [Phase 03: Normalization & Schema Design](file:///g:/MNC/DBMS/Phase_03_Database_Normalization_and_Schema_Design.md) | ফাংশনাল ডিপেনডেন্সি (FD), আর্মস্ট্রং-এর স্বতঃসিদ্ধের গাণিতিক প্রমাণ, ক্যানোনিকাল কভার অ্যালগরিদম, 1NF থেকে 5NF ও BCNF ডিকম্পোজিশন, **Heath's Theorem লসলেস জয়েন প্রমাণ**, 3NF বনাম BCNF-এর ডিপেনডেন্সি প্রিজারভেশন দ্বন্দ্ব, এবং প্রোডাকশনে OLTP বনাম OLAP ডিনরমালাইজেশন। |
| [Phase 04: Storage Architecture & Buffer Pool](file:///g:/MNC/DBMS/Phase_04_Physical_Storage_and_Memory_Hierarchy.md) | মেমোরি হায়ারার্কি ও লেটেন্সি সংখ্যা, স্লটেড-পেজ লেআউট (`TID = [Page_ID, Slot]`), Row-Store (NSM) বনাম Column-Store (DSM) বনাম PAX আর্কিটেকচার, ডেটা কম্প্রেশন (Dictionary, Bit-packing, RLE, Frame-of-Reference), এবং বাফার পুল ম্যানেজার (LRU, Clock, 2Q ইভিকশন)। |
| [Phase 05: Indexing Mastery & B+ Trees](file:///g:/MNC/DBMS/Phase_05_Indexing_Architectures_and_B_Trees.md) | কেন বাইনারি ট্রি ডিস্কে অকেজো, B-Tree বনাম B+ Tree-এর গাণিতিক ফ্যান-আউট ($B \approx 512$) ও নোড স্প্লিট/মার্জ অ্যালগরিদম, ক্লাস্টার্ড বনাম সেকেন্ডারি ইনডেক্স, কভারিং ইনডেক্স, এক্সটেন্ডিবল ও লিনিয়ার হ্যাশিং, স্পেশালাইজড ইনডেক্স (Bitmap, GIN, GiST, BRIN), এবং স্প্যাশিয়াল ইনডেক্সিং (R-Tree, Uber H3)। |
| [Phase 06: LSM-Trees & High-Write Storage Engines](file:///g:/MNC/DBMS/Phase_06_LSM_Trees_and_Storage_Engines.md) | র্যান্ডম বনাম সিকোয়েনশিয়াল ডিস্ক রাইট ফিজিক্স, LSM-Tree আর্কিটেকচার (MemTable, WAL, Immutable SSTables), ব্লুম ফিল্টারের গাণিতিক অপটিমাইজেশন ($k = \frac{m}{n} \ln 2$), কম্প্যাকশন কৌশল (Leveled vs Size-Tiered), এবং Write, Read ও Space Amplification ট্রেড-অফ (RocksDB, Cassandra)। |
| [Phase 07: Query Optimization & Join Mechanics](file:///g:/MNC/DBMS/Phase_07_Query_Optimization_and_Execution_Engines.md) | রিলেশনাল অ্যালজেব্রা ইকুইভ্যালেন্স রুলস (সিলেকশন ও প্রজেকশন পুশডাউন), Cost-Based Optimizer (CBO) হিস্টোগ্রাম ও সিলেক্টির গাণিতিক সূত্র, System-R ডাইনামিক প্রোগ্রামিং জয়েন অর্ডারিং (Left-Deep vs Bushy Trees), এবং ফিজিক্যাল জয়েন মেকানিক্স (Nested Loop, Hash, Sort-Merge Join)। |
| [Phase 08: Transactions, ACID & Concurrency Control](file:///g:/MNC/DBMS/Phase_08_Transactions_ACID_and_Concurrency_Control.md) | ফরমাল এসিড প্রোপার্টিজ, ৫টি মারাত্মক কনকারেন্সি অ্যানোমালি (Dirty Read থেকে Write Skew), প্রিসিডেন্স গ্রাফ ও কনফ্লিক্ট সিরিয়ালাইজেবিলিটি, ANSI আইসোলেশন লেভেল বনাম স্ন্যাপশট আইসোলেশন (SSI), টু-ফেজ লকিং (2PL, Strict 2PL), মাল্টি-গ্র্যানুলারিটি ইনটেনশন লকিং (IS, IX, SIX), ডেডলক (Wait-Die, Wound-Wait), এবং PostgreSQL/MySQL-এর MVCC। |
| [Phase 09: Crash Recovery, WAL & ARIES Protocol](file:///g:/MNC/DBMS/Phase_09_Database_Crash_Recovery_and_Replication.md) | বাফার ম্যানেজমেন্ট পলিসি (Steal/No-Force), Write-Ahead Logging (WAL) ইনভেরিয়েন্টস, IBM ARIES রিকভারি অ্যালগরিদম (Analysis, Redo, Undo with Compensation Log Records - CLR), ফাজি চেকপয়েন্টিং, এবং ডেটাবেস রেপ্লিকেশন মেকানিজম (Physical Streaming vs Logical Replication / CDC)। |
| [Phase 10: Distributed Databases & System Design](file:///g:/MNC/DBMS/Phase_10_Distributed_Databases_NoSQL_and_System_Design.md) | CAP থিওরেম ও PACELC থিওরেমের আনুষ্ঠানিক প্রমাণ, ভার্চুয়াল নোড সহ Consistent Hashing, কোরাম কনসেনসাস ($\mathbf{R + W > N}$), ডিস্ট্রিবিউটেড কনসেনসাস (Raft vs Paxos), গুগল স্প্যানার TrueTime API ও এক্সটার্নাল কনসিস্টেন্সি, 2PC বনাম সাগা প্যাটার্ন, NewSQL, এবং রিয়েল-ওয়ার্ল্ড সিস্টেম ডিজাইন (Stripe Ledger, Uber H3 Spatial DB)। |

---

## 🧠 গ্লোবাল টেক ইন্টারভিউ ডেটাবেস ডিসিশন ফ্রেমওয়ার্ক (Database Decision Matrix)

```
                                  [ What Storage Engine / Database to Choose? ]
                                                        |
        +-----------------------------------------------+-----------------------------------------------+
        |                                                                                               |
[ Highly Structured / ACID Strict ]                                                     [ Massive Scale / High Write / Flexible ]
  - Bank Ledger, Payments, Orders                                                         - Social Feed, Logs, Metrics, Chat History
  -> Relational DB (PostgreSQL / MySQL)                                                   -> Distributed NoSQL / NewSQL
        |                                                                                               |
        +---> Low-Latency Point Reads/Writes?                                                           +---> Ultra-high Write Volume?
        |        -> B+ Tree Storage Engine (InnoDB, Postgres)                                           |        -> LSM-Tree (RocksDB, Cassandra)
        |                                                                                               |
        +---> Analytical OLAP Aggregations?                                                             +---> Document / Dynamic Attributes?
        |        -> Columnar Store (ClickHouse, Snowflake, DuckDB)                                      |        -> MongoDB / DynamoDB
        |                                                                                               |
        +---> High-Throughput Global Distributed ACID?                                                  +---> Highly Connected Graph Data?
                 -> Google Cloud Spanner / CockroachDB (NewSQL)                                         |        -> Neo4j / Amazon Neptune
                                                                                                        |
                                                                                                        +---> Ultra-low Latency In-Memory?
                                                                                                                 -> Redis / Memcached
```
