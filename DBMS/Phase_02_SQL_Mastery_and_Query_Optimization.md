# Phase 02: Advanced SQL Mastery, Execution Plans & Window Functions

---

## ১. এসকিউএল ভাষা উপসেট ও অভ্যন্তরীণ স্টেট পরিবর্তন (SQL Language Subsets)

এসকিউএল (SQL) কেবল ডেটা কুয়েরির মাধ্যম নয়; এটি ডেটাবেস ট্রানজাকশন, সিকিউরিটি এবং স্ট্রাকচারের পূর্ণাঙ্গ নিয়ন্ত্রক।

```
                                  [ SQL COMMAND SUBSETS ]
                                             |
      +---------------+---------------+------+--------+---------------+
      |               |               |               |               |
   [ DDL ]         [ DML ]         [ DQL ]         [ DCL ]         [ TCL ]
Data Definition Data Manipulation Data Query     Data Control    Transaction
- CREATE        - INSERT        - SELECT        - GRANT         - COMMIT
- ALTER         - UPDATE                        - REVOKE        - ROLLBACK
- DROP          - DELETE                                        - SAVEPOINT
- TRUNCATE
```

### DDL বনাম DML-এর গভীর তাত্ত্বিক পার্থক্য:
1. **TRUNCATE বনাম DELETE (MNC Classic):**
   - `DELETE` হলো DML অপারেশন। এটি প্রতি রো-এর জন্য এক্সক্লুসিভ লক নেয় এবং ট্রানজাকশন লগে (WAL) প্রতি রো-এর পরিবর্তন রেকর্ড করে। ফলে কোটি রো ডিলিট করতে বিশাল ট্রানজাকশন লগ তৈরি হয় এবং প্রচুর ডিস্ক I/O হয়।
   - `TRUNCATE` হলো DDL অপারেশন। এটি ডেটাবেস ক্যাটালগে টেবিলের মেটাডাটা আপডেট করে এবং টেবিলের সাথে যুক্ত সমস্ত ফিজিক্যাল ডেটা পেজ (Data Pages) এক আঘাতে ওএস বা ফাইল সিস্টেমে ডিঅ্যালোকেট (Deallocate) করে দেয়। এটি ট্রানজাকশন লগে কেবল পেজ ডিঅ্যালোকেশন লগ করে। ফলে কোটি রো মাত্র কয়েক মিলিসেকেন্ডে সাফ হয়ে যায়!

---

## ২. অ্যাডভান্সড উইন্ডো ফাংশন ও ফ্রেম স্পেসিফিকেশন (`ROWS` বনাম `RANGE`)

উইন্ডো ফাংশন টেবিলের মৌলিক রোগুলোর পরিচয় অক্ষুণ্ণ রেখে কোনো গ্রুপিং বা পার্টিশনের ওপর হিসাব-নিকাশ চালায়।

```
FUNCTION() OVER (
    PARTITION BY partition_col
    ORDER BY sort_col [ASC | DESC]
    [ROWS | RANGE BETWEEN frame_start AND frame_end]
)
```

```
                                [ WINDOW FRAME SPECIFICATION ]
                                               |
        +--------------------------------------+--------------------------------------+
        |                                                                             |
[ ROWS Frame (Physical Row Count) ]                                   [ RANGE Frame (Logical Value Range) ]
  - কঠোরভাবে ফিজিক্যাল রো-এর সংখ্যা গণে।                                - সর্টিং কলামের মানের লজিক্যাল রেঞ্জ গণে।
  - ROWS BETWEEN 1 PRECEDING AND CURRENT ROW                          - যদি একাধিক রো-তে একই মান (Duplicate) থাকে,
  - সর্বদা সুনির্দিষ্ট ও প্রেডিক্টেবল পারফরম্যান্স।                       তবে তারা একটি একক গ্রুপ হিসেবে একসাথে প্রসেস হয়!
```

> **[!CAUTION] মারাত্মক পারফরম্যান্স ফাঁদ (The Default Window Frame Trap):**
> আপনি যদি কোনো ফ্রেম স্পেসিফিকেশন না দিয়ে কেবল `ORDER BY` লেখেন, তবে SQL স্ট্যান্ডার্ড অনুযায়ী ডেটাবেস স্বয়ংক্রিয়ভাবে এই ফ্রেমটি প্রয়োগ করে:
> `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`
> `RANGE` ফ্রেম ব্যবহার করার কারণে ডেটাবেস ইঞ্জিনকে ডুপ্লিকেট মান চেক করতে অতিরিক্ত মেমোরি ও সিপিইউ খরচ করতে হয়।
> **MNC Best Practice:** রানিং টোটাল গণনায় সর্বদা এক্সপ্লিসিটলি `ROWS` ফ্রেম লিখুন:
> `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` (এটি ৫ থেকে ১০ গুণ দ্রুত এক্সিকিউট হয়!)

---

## ৩. সমস্ত উইন্ডো ফাংশনের সম্পূর্ণ রেফারেন্স

```
Table: Employee Salaries
+--------+---------------+------------+--------+
| emp_id | name          | department | salary |
+--------+---------------+------------+--------+
| 1      | Alice         | Tech       | 100000 |
| 2      | Bob           | Tech       | 80000  |
| 3      | Charlie       | Tech       | 80000  |
| 4      | David         | Tech       | 70000  |
+--------+---------------+------------+--------+
```

### ১. র‍্যাঙ্কিং ফাংশনসমূহের আউটপুট ম্যাট্রিক্স:

```sql
SELECT 
    name, salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) as row_num,
    RANK() OVER (ORDER BY salary DESC) as rnk,
    DENSE_RANK() OVER (ORDER BY salary DESC) as dense_rnk,
    NTILE(2) OVER (ORDER BY salary DESC) as ntile_val,
    PERCENT_RANK() OVER (ORDER BY salary DESC) as pct_rank,
    CUME_DIST() OVER (ORDER BY salary DESC) as cume_dist
FROM EmployeeSalaries;
```

| Name | Salary | `row_num` | `rnk` | `dense_rnk` | `ntile_val` | `pct_rank` | `cume_dist` |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Alice** | 100,000 | 1 | 1 | 1 | 1 | 0.00 | 0.25 (1/4) |
| **Bob** | 80,000 | 2 | 2 | 2 | 1 | 0.33 | 0.75 (3/4) |
| **Charlie** | 80,000 | 3 | 2 | 2 | 2 | 0.33 | 0.75 (3/4) |
| **David** | 70,000 | 4 | 4 *(গ্যাপ)* | 3 *(নো গ্যাপ)*| 2 | 1.00 | 1.00 (4/4) |

- `PERCENT_RANK()`: $\frac{\text{Rank} - 1}{\text{Total Rows} - 1}$।
- `CUME_DIST()`: $\frac{\text{Number of rows with value } \le \text{ current value}}{\text{Total Rows}}$ (কিউমুলেটিভ ডিস্ট্রিবিউশন)।

### ২. ভ্যালু উইন্ডো ফাংশন:
- `LEAD(col, n, default)`: বর্তমান রো থেকে $n$ ধাপ সামনের মান অ্যাক্সেস করে।
- `LAG(col, n, default)`: বর্তমান রো থেকে $n$ ধাপ পেছনের মান অ্যাক্সেস করে।
- `FIRST_VALUE(col)`: উইন্ডো ফ্রেমের প্রথম মান।
- `LAST_VALUE(col)`: উইন্ডো ফ্রেমের শেষ মান।
- `NTH_VALUE(col, n)`: উইন্ডো ফ্রেমের $n$-তম মান।

---

## ৪. রিকার্সিভ সিটিই ও সাইকেল ডিটেকশন (Recursive CTEs & Cycle Prevention)

গ্রাফ বা নেটওয়ার্ক ডেটাবেসে সাইকেল (Cycle/Infinite Loop) থাকলে সাধারণ রিকার্সিভ সিটিই অনন্তকালের জন্য হ্যাং হয়ে ক্র্যাশ করে।

### সাইকেল-প্রতিরোধক রিকার্সিভ সিটিই (Cycle-Safe Recursive CTE in PostgreSQL):
ধরা যাক একটি ফ্লাইট নেটওয়ার্কে সিটি থেকে সিটিতে ফ্লাইট চলে:

```sql
WITH RECURSIVE FlightPath AS (
    -- ১. Anchor Member: Dhaka থেকে শুরু
    SELECT 
        origin, 
        destination, 
        cost, 
        ARRAY[origin] AS visited_cities, 
        false AS is_cycle
    FROM Flights
    WHERE origin = 'DAC'

    UNION ALL

    -- ২. Recursive Member: পরবর্তী কানেক্টিং ফ্লাইট
    SELECT 
        f.origin, 
        f.destination, 
        p.cost + f.cost, 
        p.visited_cities || f.destination, 
        f.destination = ANY(p.visited_cities) -- Check if already visited
    FROM Flights f
    INNER JOIN FlightPath p ON f.origin = p.destination
    WHERE NOT p.is_cycle -- Stop recursive expansion if cycle detected!
)
SELECT * FROM FlightPath WHERE destination = 'JFK';
```

---

## ৫. কুয়েরি এক্সিকিউশন প্ল্যান বিশ্লেষণ (`EXPLAIN ANALYZE BUFFERS`)

MNC পারফরম্যান্স টিউনিংয়ের প্রধান অস্ত্র হলো কুয়েরি প্ল্যান ডিকোড করা।

```sql
EXPLAIN (ANALYZE, BUFFERS, COSTS, VERBOSE)
SELECT customer_id, SUM(amount)
FROM orders
WHERE created_at >= '2024-01-01'
GROUP BY customer_id;
```

```
QUERY PLAN:
HashAggregate  (cost=1420.50..1520.50 rows=1000 width=16) 
               (actual time=12.450..14.200 rows=850 loops=1)
  Group Key: customer_id
  Batches: 1  Memory Usage: 128kB
  Buffers: shared hit=420 read=15 dirtied=2
  ->  Index Scan using idx_orders_created on orders  
      (cost=0.42..1250.00 rows=34100 width=16) 
      (actual time=0.040..8.100 rows=34210 loops=1)
        Index Cond: (created_at >= '2024-01-01'::date)
        Buffers: shared hit=380 read=15
Planning Time: 0.180 ms
Execution Time: 14.520 ms
```

### বাফার মেট্রিক্সের ব্যবচ্ছেদ (Buffer Analysis):
- `shared hit=420`: ৪২০টি পেজ সরাসরি র্যামের **বাফার পুল** থেকে রিড করা হয়েছে (Zero Disk I/O - Ultra Fast!)।
- `read=15`: ১৫টি পেজ র্যামে ছিল না, সরাসরি ফিজিক্যাল ডিস্ক (SSD/HDD) থেকে রিড করতে হয়েছে।
- `dirtied=2`: ২টি মেমোরি পেজ পরিবর্তন হয়েছে যা পরবর্তীতে ব্যাকগ্রাউন্ডে ডিস্কে ফ্লাশ হবে।

---

## ৬. ৫টি ক্যানোনিকাল MNC এসকিউএল ইন্টারভিউ সমস্যা

### সমস্যা ১: ডিপার্টমেন্টভিত্তিক ৩য় সর্বোচ্চ বেতন (Handling Ties Perfectly)

```sql
WITH RankedSalaries AS (
    SELECT 
        emp_id, 
        name, 
        department_id, 
        salary,
        DENSE_RANK() OVER (
            PARTITION BY department_id 
            ORDER BY salary DESC
        ) AS rank_order
    FROM Employees
)
SELECT department_id, emp_id, name, salary
FROM RankedSalaries
WHERE rank_order = 3;
```

---

### সমস্যা ২: ইউজার লগইন স্ট্রিক / কনসিকিউটিভ দিন (Gaps and Islands)

```sql
WITH UniqueLogins AS (
    SELECT DISTINCT user_id, login_date 
    FROM UserLogins
),
GroupedSequences AS (
    SELECT 
        user_id,
        login_date,
        login_date - (ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) * INTERVAL '1 day') AS island_id
    FROM UniqueLogins
)
SELECT 
    user_id,
    MIN(login_date) AS streak_start,
    MAX(login_date) AS streak_end,
    COUNT(*) AS streak_length
FROM GroupedSequences
GROUP BY user_id, island_id
HAVING COUNT(*) >= 5
ORDER BY streak_length DESC;
```

---

### সমস্যা ৩: বিগত ৭ দিনের মুভিং এভারেজ (7-Day Rolling Moving Average)

```sql
SELECT 
    transaction_date,
    daily_revenue,
    AVG(daily_revenue) OVER (
        ORDER BY transaction_date
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS rolling_7day_avg
FROM DailyFinancials;
```

---

### সমস্যা ৪: বছরভিত্তিক প্রবৃদ্ধি হার (Year-over-Year Growth using LAG)

```sql
WITH YearlyRevenue AS (
    SELECT 
        EXTRACT(YEAR FROM order_date) AS order_year,
        SUM(total_amount) AS revenue
    FROM Orders
    GROUP BY EXTRACT(YEAR FROM order_date)
)
SELECT 
    order_year,
    revenue,
    LAG(revenue, 1) OVER (ORDER BY order_year) AS prev_year_revenue,
    ROUND(
        ((revenue - LAG(revenue, 1) OVER (ORDER BY order_year)) / 
        LAG(revenue, 1) OVER (ORDER BY order_year)) * 100, 
        2
    ) AS yoy_growth_percentage
FROM YearlyRevenue;
```

---

### সমস্যা ৫: ডাইনামিক পিভট টেবিল (Pivot without Hardcoding)

```sql
SELECT 
    product_category,
    SUM(CASE WHEN quarter = 'Q1' THEN sales_amount ELSE 0 END) AS Q1_Sales,
    SUM(CASE WHEN quarter = 'Q2' THEN sales_amount ELSE 0 END) AS Q2_Sales,
    SUM(CASE WHEN quarter = 'Q3' THEN sales_amount ELSE 0 END) AS Q3_Sales,
    SUM(CASE WHEN quarter = 'Q4' THEN sales_amount ELSE 0 END) AS Q4_Sales,
    SUM(sales_amount) AS Total_Yearly_Sales
FROM QuarterlySales
GROUP BY product_category;
```

---

## ৭. ইন্টারভিউ প্রশ্নোত্তর (MNC Interview Checklist)

1. **প্রশ্ন: `WHERE` ক্লজের ফিল্টারিং এবং `HAVING` ক্লজের ফিল্টারিংয়ের মধ্যে মৌলিক এক্সিকিউশন পার্থক্য কী?**
   - **উত্তর:** `WHERE` ক্লজ প্রয়োগ করা হয় `GROUP BY` অপারেশনের পূর্বে, অর্থাৎ টেবিলের কাঁচা রোগুলো ফিল্টার করতে। আর `HAVING` ক্লজ প্রয়োগ করা হয় `GROUP BY` অপারেশনের পরে, অ্যাগ্রিগেশন থেকে প্রাপ্ত সামারি ডেটা ফিল্টার করতে। যদি কোনো শর্ত সাধারণ কলামের ওপর হয়, তবে তা সর্বদা `WHERE`-এ রাখা উচিত যাতে ডেটাবেস ইঞ্জিন আগেই অপ্রয়োজনীয় রো বাদ দিয়ে গ্রুপ বাই-এর মেমোরি লোড কমাতে পারে।
2. **প্রশ্ন: PostgreSQL-এ `WITH ... AS MATERIALIZED` বনাম নন-ম্যাটেরিয়ালাইজড CTE-এর পার্থক্য কী?**
   - **উত্তর:** সাধারণ CTE-কে অপ্টিমাইজার প্রায়শই মূল কুয়েরির সাথে ইনলাইন (Inline / Flatten) করে ফেলে এবং বাইরের কুয়েরির শর্ত CTE-এর ভেতরে পুশডাউন করে। কিন্তু `MATERIALIZED` লিখলে ডেটাবেস বাধ্য হয় CTE-এর কুয়েরিটি পৃথকভাবে সম্পূর্ণ রান করে মেমোরিতে একটি টেম্পোরারি টেবিল বানাতে। যদি একই CTE মূল কোয়েরিতে একাধিকবার রেফারেন্স হয়, তবে `MATERIALIZED` কুয়েরি স্পিড বহুগুণ বাড়ায়।
3. **প্রশ্ন: `COUNT(*)` এবং `COUNT(column_name)` এর মধ্যে পার্থক্য কী?**
   - **উত্তর:** `COUNT(*)` টেবিলের মোট সারির সংখ্যা গণে (এমনকি যে সমস্ত সারির সব কলাম `NULL`, সেগুলোকেও গণে)। কিন্তু `COUNT(column_name)` কেবল ঐ নির্দিষ্ট কলামের যে সমস্ত ঘরে নন-নাল (`NOT NULL`) মান রয়েছে তাদের গণে।
