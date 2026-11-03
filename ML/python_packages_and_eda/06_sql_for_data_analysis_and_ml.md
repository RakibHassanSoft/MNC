# মাস্টার গাইড: মেশিন লার্নিং ও ডেটা অ্যানালাইসিসের জন্য অ্যাডভান্সড SQL (SQL for Data Analysis & ML Engineering)

> **"Data is the new oil, but SQL is the refinery pipeline."**
> 
> মেশিন লার্নিং ইঞ্জিনিয়ারের কাজের প্রায় ৬০-৭০% ফিচার তৈরি হয় ডেটাবেজ লেভেলে। পাইথনে গিগাবাইট ডেটা টেনে এনে ক্র্যাশ করানোর চেয়ে SQL ইঞ্জিনে (PostgreSQL, Snowflake, BigQuery, DuckDB) সরাসরি এগ্রিগেশন, উইন্ডো ক্যালকুলেশন এবং হিস্টোরিক্যাল রোলিং ফিচার তৈরি করা শতগুণ বেশি মেমরি-এফিশিয়েন্ট ও দ্রুতগতির।

---

## 📑 বিষয়সূচি (Table of Contents)
1. [মডার্ন SQL আর্কিটেকচার ও ডেটা প্রসেসিং পাইপলাইন](#১-মডার্ন-sql-আর্কিটেকচার-ও-ডেটা-প্রসেসিং-পাইপলাইন)
2. [কোর কোয়েরি এক্সিকিউশন অর্ডার (SQL Logical Order of Execution)](#২-কোর-কোয়েরি-এক্সিকিউশন-অর্ডার-sql-logical-order-of-execution)
3. [অ্যাডভান্সড ফিল্টারিং ও গ্রুপ এগ্রিগেশন (GROUP BY & HAVING)](#৩-অ্যাডভান্সড-ফিল্টারিং-ও-গ্রুপ-এগ্রিগেশন-group-by--having)
4. [সকল প্রকার JOIN-এর গভীর ব্যবচ্ছেদ ও এন্টি-জয়েন (Anti-Joins)](#৪-সকল-প্রকার-join-এর-গভীর-ব্যবচ্ছেদ-ও-এন্টি-জয়েন-anti-joins)
5. [উইন্ডো ফাংশনস ইন-ডেপথ (Window Functions Deep Dive)](#৫-উইন্ডো-ফাংশনস-ইন-ডেপথ-window-functions-deep-dive)
   - ৫.১ `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `NTILE()` এর নিখুঁত গাণিতিক পার্থক্য
   - ৫.২ নেভিগেশনাল ফাংশন: `LAG()`, `LEAD()`, `FIRST_VALUE()`, `LAST_VALUE()`
   - ৫.৩ উইন্ডো ফ্রেমিং: `ROWS BETWEEN` বনাম `RANGE BETWEEN` (হাতে-কলমে গণনা)
6. [Common Table Expressions (CTEs) ও সাবকোয়েরি দিয়ে ফিচার পাইপলাইন](#৬-common-table-expressions-ctes-ও-সাবকোয়েরি-দিয়ে-ফিচার-পাইপলাইন)
7. [মেশিন লার্নিং ফিচার স্টোর তৈরিতে SQL প্যাটার্নস (Real-world Feature Engineering)](#৭-মেশিন-লার্নিং-ফিচার-স্টোর-তৈরিতে-sql-প্যাটার্নস-real-world-feature-engineering)
   - RFM (Recency, Frequency, Monetary) কাস্টমার মেট্রিক্স
   - ৭-দিনের রোলিং এভারেজ ও ট্রানজ্যাকশন ভেলোসিটি ফিচার
   - ইউজার সেশনালাইজেশন (Sessionization Windowing)
8. [SQL কোয়েরি অপটিমাইজেশন ও ইনডেক্সিং (Query Optimization for ML)](#৮-sql-কোয়েরি-অপটিমাইজেশন-ও-ইনডেক্সিং-query-optimization-for-ml)

---

## ১. মডার্ন SQL আর্কিটেকচার ও ডেটা প্রসেসিং পাইপলাইন

মেশিন লার্নিং পাইপলাইনে SQL-এর ভূমিকা চিত্র:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        Transactional DB / Data Lake                    │
│             (PostgreSQL / MySQL / Snowflake / BigQuery / DuckDB)        │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                                    ▼ SQL Pushdown (Filtering, Joins, Windowing)
┌────────────────────────────────────────────────────────────────────────┐
│                 Engineered Feature Store (Analytical SQL)               │
│        • User 30-day Spend Velocity     • Churn Signals (Last 90 days)  │
│        • RFM Scores                     • Rolling Click Count           │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Clean Analytical Table (Parquet / Arrow)
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│               Python / ML Environment (NumPy, Pandas, Scikit-learn)     │
│        • Model Training (XGBoost, PyTorch)  • Evaluation & Inference   │
└────────────────────────────────────────────────────────────────────────┘
```

---

## ২. কোর কোয়েরি এক্সিকিউশন অর্ডার (SQL Logical Order of Execution)

SQL কোড যেভাবে লেখা হয়, ডেটাবেজ ইঞ্জিন সেভাবে এক্সিকিউট করে **না**। এক্সিকিউশন অর্ডার না বুঝলে `WHERE` ক্লজে উইন্ডো ফাংশন ব্যবহার করে এরর খাওয়া বা `ALIAS` খুঁজে না পাওয়ার মতো সাধারণ ভুল ঘটে।

| ধাপ | SQL ক্লজ | ইঞ্জিনের অভ্যন্তরীণ কাজ | কেন গুরুত্বপূর্ণ? |
| :---: | :--- | :--- | :--- |
| **১** | `FROM` & `JOIN` | টেবিল নির্বাচন ও কার্তেসীয় গুণন/জয়েন সম্পাদন | ডেটার বেস টেবিল তৈরি |
| **২** | `WHERE` | সারি পর্যায়ের ফিল্টারিং (Row-level filter) | এগ্রিগেশনের আগেই অপ্রয়োজনীয় ডেটা বাদ দেওয়া |
| **৩** | `GROUP BY` | একই ক্যাটাগরির সারিগুলোকে বাকেটে দলবদ্ধ করা | এগ্রিগেশন পয়েন্ট নির্ধারণ |
| **৪** | `HAVING` | গ্রুপ পর্যায়ের ফিল্টারিং (Group-level filter) | এগ্রিগেশন ফলাফলের ওপর শর্ত আরোপ |
| **৫** | `SELECT` | কলাম প্রজেকশন ও এক্সপ্রেশন গণনা | শুধুমাত্র প্রয়োজনীয় ফিচার নির্বাচন |
| **৬** | **Window Functions** | `OVER(PARTITION BY ...)` গণনা | `SELECT` ধাপের সাথে উইন্ডো গণনা সম্পন্ন হয় |
| **৭** | `DISTINCT` | ডুপ্লিকেট সারি অপসারণ | মেমরি কমানো |
| **৮** | `ORDER BY` | চূড়ান্ত ফলাফল সাজানো | সর্টিং অ্যালগরিদম চালানো |
| **৯** | `LIMIT` / `OFFSET` | নির্দিষ্ট সংখ্যক সারি রিটার্ন করা | পেজিনেশন |

> [!IMPORTANT]
> **কেন `WHERE` ক্লজে `AVG()` বা `ROW_NUMBER()` কাজ করে না?**
> কারণ `WHERE` রান হয় ধাপ ২-এ, অথচ `GROUP BY` রান হয় ধাপ ৩-এ এবং উইন্ডো ফাংশন রান হয় ধাপ ৬-এ! উইন্ডো ফাংশনের ওপর ফিল্টার করতে চাইলে অবশ্যই **CTE (`WITH`)** বা **Subquery** ব্যবহার করতে হয়।

---

## ৩. অ্যাডভান্সড ফিল্টারিং ও গ্রুপ এগ্রিগেশন (GROUP BY & HAVING)

ধরা যাক, আমাদের একটি ই-কমার্স ট্রানজ্যাকশন টেবিল `transactions` রয়েছে:

```sql
-- ডেমো টেবিল স্কিমা
CREATE TABLE transactions (
    transaction_id INT,
    user_id INT,
    category VARCHAR(50),
    amount NUMERIC(10, 2),
    tx_time TIMESTAMP
);
```

### ৩.১ ক্যাটাগরিভিত্তিক এগ্রিগেশন ও ফিল্টারিং:
যেসব ইউজারের মোট স্পেন্ড ১০০০ টাকার বেশি এবং কমপক্ষে ৩টি কেনাকাটা রয়েছে:

```sql
SELECT 
    user_id,
    COUNT(transaction_id) AS total_orders,
    ROUND(SUM(amount), 2) AS total_spent,
    ROUND(AVG(amount), 2) AS avg_basket_size,
    ROUND(STDDEV(amount), 2) AS spend_volatility
FROM transactions
WHERE tx_time >= '2026-01-01'  -- ধাপ ২: সারি ফিল্টার
GROUP BY user_id               -- ধাপ ৩: ইউজারভিত্তিক গ্রুপিং
HAVING COUNT(transaction_id) >= 3 AND SUM(amount) > 1000.00 -- ধাপ ৪: গ্রুপ ফিল্টার
ORDER BY total_spent DESC;     -- ধাপ ৮: সাজানো
```

---

## ৪. সকল প্রকার JOIN-এর গভীর ব্যবচ্ছেদ ও এন্টি-জয়েন (Anti-Joins)

মেশিন লার্নিংয়ে ফিচার ইঞ্জিনিয়ারিংয়ের প্রধান কাজ হলো একাধিক সোর্স টেবিলকে মার্জ করা।

```
        INNER JOIN                     LEFT JOIN                     FULL OUTER JOIN
    ┌───────┬───────┐              ┌───────┬───────┐              ┌───────┬───────┐
    │       │ █ █ █ │              │ █ █ █ │ █ █ █ │              │ █ █ █ │ █ █ █ │
    │   A   │ █ █ █ │   B          │ █ █ █ │ █ █ █ │   B          │ █ █ █ │ █ █ █ │   B
    │       │ █ █ █ │              │ █ █ █ │ █ █ █ │              │ █ █ █ │ █ █ █ │
    └───────┴───────┘              └───────┴───────┘              └───────┴───────┘
     Matching Only                  All of A + Match               Everything from Both
```

### ৪.১ এন্টি-জয়েন (Anti-Join): চার্ন প্রেডিকশনে অত্যন্ত কার্যকরী
**উদ্দেশ্য:** যেসব গ্রাহক একাউন্ট খুলেছে কিন্তু গত ৯০ দিনে কোনো ট্রানজ্যাকশন করেনি (ইনঅ্যাক্টিভ গ্রাহক):

```sql
-- LEFT ANTI-JOIN প্যাটার্ন (WHERE B.key IS NULL)
SELECT 
    u.user_id,
    u.signup_date,
    u.country
FROM users u
LEFT JOIN transactions t 
    ON u.user_id = t.user_id 
    AND t.tx_time >= CURRENT_DATE - INTERVAL '90 days'
WHERE t.user_id IS NULL; -- যেসব ইউজারের কোনো ট্রানজ্যাকশন ম্যাচ করেনি
```

### ৪.২ সেলফ-জয়েন (Self Join): সিকোয়েন্সিয়াল ইভেন্ট ডিটেকশন
একজন ইউজার প্রথম ক্রয়ের কত দিনের মাথায় দ্বিতীয় ক্রয় করেছে:

```sql
SELECT 
    t1.user_id,
    t1.transaction_id AS first_order_id,
    t1.tx_time AS first_order_date,
    t2.transaction_id AS second_order_id,
    t2.tx_time AS second_order_date,
    EXTRACT(DAY FROM (t2.tx_time - t1.tx_time)) AS days_to_repeat_purchase
FROM transactions t1
JOIN transactions t2 
    ON t1.user_id = t2.user_id 
    AND t2.tx_time > t1.tx_time
WHERE t1.transaction_id = (
    SELECT MIN(transaction_id) FROM transactions WHERE user_id = t1.user_id
);
```

---

## ৫. উইন্ডো ফাংশনস ইন-ডেপথ (Window Functions Deep Dive)

> **উইন্ডো ফাংশন বনাম GROUP BY-এর পার্থক্য:**
> `GROUP BY` একাধিক সারিকে দুমড়ে-মুচড়ে একটি একক সারিতে পরিণত করে। কিন্তু `WINDOW FUNCTION` মূল সারির সংখ্যা অপরিবর্তিত রেখে প্রতিটি সারির পাশে তার পার্টিশনের ক্যালকুলেশন বসিয়ে দেয়!

### ৫.১ `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `NTILE()` এর নিখুঁত গাণিতিক ব্যবচ্ছেদ

ধরা যাক, একই ডিপার্টমেন্টের ৫ জন কর্মচারীর বেতনের টেবিল:

| employee_name | salary |
| :--- | :---: |
| Alice | 10,000 |
| Bob | 8,000 |
| Charlie | 8,000 |
| David | 7,000 |
| Eva | 5,000 |

```sql
SELECT 
    employee_name,
    salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num,
    RANK()       OVER (ORDER BY salary DESC) AS rnk,
    DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rnk,
    NTILE(2)     OVER (ORDER BY salary DESC) AS bucket_quantile
FROM employees;
```

#### গাণিতিক ফলাফলের তুলনা টেবিল:

| employee_name | salary | `row_num` | `rnk` | `dense_rnk` | `bucket_quantile` | ব্যাখ্যা (Logic & Calculation) |
| :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| **Alice** | 10,000 | **1** | **1** | **1** | **1** | সর্বোচ্চ বেতন, প্রতিটি ফাংশনে ১ম |
| **Bob** | 8,000 | **2** | **2** | **2** | **1** | টাই (Tie): Bob ও Charlie সমান বেতন |
| **Charlie** | 8,000 | **3** | **2** | **2** | **1** | `ROW_NUMBER` টাই মানে না (ক্রমিক ৩), কিন্তু `RANK` ও `DENSE_RANK` উভয়ে ২য় দেয় |
| **David** | 7,000 | **4** | **4** | **3** | **2** | **পার্থক্য:** `RANK` ৩ বাদ দিয়ে ৪ এ লাফ দিয়েছে (গ্যাপ থাকে)! `DENSE_RANK` কোনো গ্যাপ না রেখে ৩ দিয়েছে! |
| **Eva** | 5,000 | **5** | **5** | **4** | **2** | `NTILE(2)` ৫টি স্যাম্পলকে ২টি সমান বাকেটে (৩+২) ভাগ করেছে |

- **মেশিন লার্নিং সিদ্ধান্ত:** 
  - ইউজারদের শীর্ষ $K$ ট্রানজ্যাকশন ডিডুপ্লিকেট করতে: `ROW_NUMBER() = 1` ব্যবহার করবেন।
  - টাই সহ পার্সেন্টাইল বের করতে: `DENSE_RANK()` বা `NTILE(10)` (Deciles) ব্যবহার করবেন।

---

### ৫.২ নেভিগেশনাল ফাংশন: `LAG()` ও `LEAD()`

মেশিন লার্নিংয়ে টাইম-সিরিজ ফিচার তৈরিতে (যেমন: পূর্ববর্তী ট্রানজ্যাকশনের সময়ের ব্যবধান):

```sql
SELECT 
    user_id,
    tx_time,
    amount,
    -- আগের লেনদেনের পরিমাণ
    LAG(amount, 1) OVER (PARTITION BY user_id ORDER BY tx_time) AS prev_amount,
    -- আগের লেনদেন ও বর্তমান লেনদেনের মধ্যে সময়ের ব্যবধান (ঘণ্টায়)
    ROUND(
        EXTRACT(EPOCH FROM (tx_time - LAG(tx_time, 1) OVER (PARTITION BY user_id ORDER BY tx_time))) / 3600.0, 
        2
    ) AS hours_since_last_tx,
    -- ভবিষ্যৎ লেনদেন (Next Amount)
    LEAD(amount, 1) OVER (PARTITION BY user_id ORDER BY tx_time) AS next_amount
FROM transactions;
```

---

### ৫.৩ উইন্ডো ফ্রেমিং: `ROWS BETWEEN` বনাম `RANGE BETWEEN` (হাতে-কলমে হিসাব)

**সিনট্যাক্স:**
```sql
SUM(amount) OVER (
    PARTITION BY user_id 
    ORDER BY tx_time 
    ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
)
```

#### হাতে-কলমে গাণিতিক সিমুলেশন:
ধরা যাক ইউজারের ক্রমান্বয়িক ৫টি কেনাকাটা:
- সারি ১: amount = $10$
- সারি ২: amount = $20$
- সারি ৩: amount = $30$
- সারি ৪: amount = $40$
- সারি ৫: amount = $50$

উইন্ডো শর্ত: `ROWS BETWEEN 2 PRECEDING AND CURRENT ROW` (বর্তমান সারি এবং তার আগের সর্বোচ্চ ২টি সারি নিয়ে যোগফল):
- **সারি ১ ($10$):** আগের কোনো সারি নেই। উইন্ডো = $[10] \Rightarrow \mathbf{10}$
- **সারি ২ ($20$):** আগের ১টি সারি আছে। উইন্ডো = $[10, 20] \Rightarrow \mathbf{30}$
- **সারি ৩ ($30$):** আগের ২টি সারি আছে। উইন্ডো = $[10, 20, 30] \Rightarrow \mathbf{60}$
- **সারি ৪ ($40$):** আগের ২টি সারি = $[20, 30]$ এবং বর্তমান $40$। উইন্ডো = $[20, 30, 40] \Rightarrow \mathbf{90}$
- **সারি ৫ ($50$):** আগের ২টি সারি = $[30, 40]$ এবং বর্তমান $50$। উইন্ডো = $[30, 40, 50] \Rightarrow \mathbf{120}$

> [!NOTE]
> `ROWS` ফিজিক্যাল সারির সংখ্যা গুনে কাজ করে, আর `RANGE` লজিক্যাল ভ্যালুর দূরত্ব (যেমন: `RANGE BETWEEN INTERVAL '7' DAY PRECEDING AND CURRENT ROW`) অনুযায়ী উইন্ডো নির্ধারণ করে।

---

## ৬. Common Table Expressions (CTEs) ও সাবকোয়েরি দিয়ে ফিচার পাইপলাইন

CTEs (`WITH` ক্লজ) জটিল SQL কোডকে মডুলার, রিডেবল এবং ডিবাগযোগ্য করে তোলে।

```sql
WITH user_daily_metrics AS (
    -- ধাপ ১: প্রতিদিনের ইউজার মেট্রিক্স
    SELECT 
        user_id,
        DATE(tx_time) AS tx_date,
        COUNT(transaction_id) AS daily_tx_count,
        SUM(amount) AS daily_spend
    FROM transactions
    GROUP BY user_id, DATE(tx_time)
),
user_rolling_features AS (
    -- ধাপ ২: ৭ দিনের মুভিং এভারেজ এবং ভেলোসিটি
    SELECT 
        user_id,
        tx_date,
        daily_spend,
        AVG(daily_spend) OVER (
            PARTITION BY user_id 
            ORDER BY tx_date 
            ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
        ) AS moving_avg_spend_7d,
        MAX(daily_spend) OVER (
            PARTITION BY user_id 
            ORDER BY tx_date 
            ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
        ) AS max_spend_7d
    FROM user_daily_metrics
)
-- ধাপ ৩: চূড়ান্ত ফিচার সিলেকশন
SELECT 
    user_id,
    tx_date,
    daily_spend,
    ROUND(moving_avg_spend_7d, 2) AS moving_avg_spend_7d,
    ROUND(daily_spend / NULLIF(moving_avg_spend_7d, 0), 2) AS spend_spike_ratio
FROM user_rolling_features
ORDER BY user_id, tx_date;
```

---

## ৭. মেশিন লার্নিং ফিচার স্টোর তৈরিতে SQL প্যাটার্নস (Real-world Feature Engineering)

### ৭.১ RFM (Recency, Frequency, Monetary) কাস্টমার সেগমেন্টেশন কুয়েরি

মেশিন লার্নিং ক্ল্যাস্টারিং বা LTV (Customer Lifetime Value) প্রেডিকশনে RFM একটি গোল্ড স্ট্যান্ডার্ড ফিচার সেট:

```sql
-- রেফারেন্স সময়: '2026-11-01'
WITH rfm_raw AS (
    SELECT 
        user_id,
        -- Recency: শেষ কেনাকাটার দিন থেকে কত দিন পার হয়েছে
        EXTRACT(DAY FROM ('2026-11-01'::timestamp - MAX(tx_time))) AS recency_days,
        -- Frequency: মোট কতবার ট্রানজ্যাকশন করেছে
        COUNT(transaction_id) AS frequency,
        -- Monetary: মোট কত টাকা খরচ করেছে
        SUM(amount) AS monetary_value
    FROM transactions
    WHERE tx_time <= '2026-11-01'
    GROUP BY user_id
)
SELECT 
    user_id,
    recency_days,
    frequency,
    ROUND(monetary_value, 2) AS monetary_value,
    -- NTILE দিয়ে ১-৫ স্কেলে স্কোরিং
    NTILE(5) OVER (ORDER BY recency_days DESC) AS r_score, -- কম দিন হলে বেশি স্কোর
    NTILE(5) OVER (ORDER BY frequency ASC)     AS f_score, -- বেশি ট্রানজ্যাকশনে বেশি স্কোর
    NTILE(5) OVER (ORDER BY monetary_value ASC) AS m_score -- বেশি স্পেন্ডে বেশি স্কোর
FROM rfm_raw;
```

---

### ৭.২ ইউজার সেশনালাইজেশন (Sessionization Windowing Pattern)

ইউজাররা প্ল্যাটফর্মে কখন নতুন সেশন শুরু করে তা ট্র্যাক করা (যেমন: ৩০ মিনিটের বেশি বিরতি দিলে নতুন সেশন):

```sql
WITH event_lags AS (
    SELECT 
        user_id,
        event_time,
        LAG(event_time) OVER (PARTITION BY user_id ORDER BY event_time) AS prev_event_time
    FROM clickstream_events
),
session_flags AS (
    SELECT 
        user_id,
        event_time,
        CASE 
            WHEN prev_event_time IS NULL THEN 1
            WHEN EXTRACT(EPOCH FROM (event_time - prev_event_time)) / 60 > 30 THEN 1
            ELSE 0 
        END AS is_new_session
    FROM event_lags
)
SELECT 
    user_id,
    event_time,
    -- কিউমুলেটিভ যোগফলের মাধ্যমে সেশন আইডি তৈরি
    SUM(is_new_session) OVER (PARTITION BY user_id ORDER BY event_time) AS session_id
FROM session_flags;
```

---

## ৮. SQL কোয়েরি অপটিমাইজেশন ও ইনডেক্সিং (Query Optimization for ML)

বিলিয়ন সারির ডেটাসেটে কোয়েরি অপটিমাইজ না করলে মডেল ট্রেনিংয়ের আগেই ঘণ্টার পর ঘণ্টা সময় অপচয় হবে।

### ৮.১ B-Tree ও Composite Indexিং
```sql
-- দুর্বল পদ্ধতি: ইনডেক্স ছাড়া ফুল টেবিল স্ক্যান
-- সমাধান: যে কলামগুলোতে জয়েন এবং রেঞ্জ ফিল্টার বেশি হয়
CREATE INDEX idx_transactions_user_time ON transactions(user_id, tx_time DESC);
```

### ৮.২ সারগেইট প্রেডিকেট সমস্যা পরিহার (Avoid Non-Sargable Predicates)
❌ **ভুল (Index কাজ করবে না - ফুল স্ক্যান):**
```sql
SELECT * FROM transactions WHERE DATE(tx_time) = '2026-10-15';
```
✅ **সঠিক (Sargable Query - ইনডেক্স রেঞ্জ স্ক্যান হবে):**
```sql
SELECT * FROM transactions 
WHERE tx_time >= '2026-10-15 00:00:00' AND tx_time < '2026-10-16 00:00:00';
```

### ৮.৩ `EXPLAIN ANALYZE` এর মাধ্যমে কুয়েরি প্ল্যান পর্যবেক্ষণ
```sql
EXPLAIN ANALYZE
SELECT user_id, SUM(amount)
FROM transactions
WHERE tx_time >= '2026-01-01'
GROUP BY user_id;
```
- **Sequential Scan (Seq Scan):** পুরো ডিস্ক রিড হচ্ছে। সমাধান: ইনডেক্স তৈরি বা পার্টিশনিং।
- **Index Scan / Bitmap Index Scan:** দ্রুত বি-ট্রি ট্রাভার্সাল হচ্ছে।
- **HashAggregate vs GroupAggregate:** পর্যাপ্ত মেমরি (`work_mem`) থাকলে HashAggregate বিদ্যুৎগতির হয়।

---
> **নেক্সট স্টেপ:** এই SQL কোয়েরিগুলো থেকে এক্সট্র্যাক্ট করা ফিচার টেবিল সরাসরি [complete_eda_data_analysis_guide.md](file:///g:/MNC/ML/python_packages_and_eda/05_complete_eda_data_analysis_guide.md) এ নিয়ে এক্সপ্লোরেটরি অ্যানালাইসিস ও ফিচার ভ্যালিডেশন করা হয়।
