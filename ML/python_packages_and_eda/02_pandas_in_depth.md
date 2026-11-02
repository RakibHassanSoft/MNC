# Pandas মাস্টার নোট: মেশিন লার্নিংয়ের জন্য ডেটা ম্যানিপুলেশন (Pandas Mastery)

> **"৮০% মেশিন লার্নিং কাজই হলো ডেটা প্রিপারেশন এবং ক্লিনিং। আর এই কাজের অবিসংবাদিত রাজা হলো Pandas।"**

---

## ১. কোর ডেটা স্ট্রাকচার: Series বনাম DataFrame

1. **Series:** ১-মাত্রিক লেবেলযুক্ত অ্যারে (একটি কলামের সমতুল্য)।
2. **DataFrame:** ২-মাত্রিক টেবুলার ডেটা স্ট্রাকচার (সারি ও কলাম সমন্বিত)।

```python
import pandas as pd
import numpy as np

# ডিকশনারি থেকে ডেটাফ্রেম তৈরি
data = {
    'name': ['Sakib', 'Tamim', 'Mushfiq', 'Mahmudullah', 'Mustafiz'],
    'age': [35, 34, 35, 36, 27],
    'role': ['All-rounder', 'Batter', 'Wicketkeeper', 'All-rounder', 'Bowler'],
    'matches': [240, 235, 250, 220, 90],
    'runs': [7200, 8300, 7100, 5100, 150]
}
df = pd.DataFrame(data)
print(df.head())
```

---

## ২. দ্রুত ডেটা পরিদর্শনের অপরিহার্য মেথডসমূহ (Inspection)

মেশিন লার্নিং শুরুর আগে ডেটাসেটের স্বাস্থ্য পরীক্ষা করা আবশ্যক:

```python
# ১. সারসংক্ষেপ ও মেমরি ব্যবহার
df.info()

# ২. নিউমেরিক্যাল কলামের পরিসংখ্যান (গড়, এসডি, মিনিমাম, পার্সেন্টাইল, ম্যাক্সিমাম)
print(df.describe().T)

# ৩. কলামের আকার ও ডেটা টাইপ
print("Shape (Rows, Columns):", df.shape)
print("Data Types:\n", df.dtypes)

# ৪. ক্যাটাগরিকাল ফিচারের ডিস্ট্রিবিউশন
print("Role Distribution:\n", df['role'].value_counts(normalize=True))
```

---

## ৩. ফিল্টারিং ও সিলেকশন: `.loc` বনাম `.iloc` বনাম `.query`

- **`.iloc` (Integer-location):** শুধুমাত্র পূর্ণসংখ্যা সূচক দিয়ে সিলেক্ট করে (০-ভিত্তিক)।
- **`.loc` (Label-based location):** সারি এবং কলামের লেবেল বা নাম দিয়ে সিলেক্ট করে।
- **`.query()`:** জটিল ফিল্টারিং সহজে এসকিউএল (SQL)-এর মতো লেখার জন্য।

```python
# প্রথম ৩টি সারি এবং 'name' ও 'runs' কলাম (loc)
subset1 = df.loc[:2, ['name', 'runs']]

# প্রথম ২টি সারি এবং প্রথম ৩টি কলাম (iloc)
subset2 = df.iloc[:2, :3]

# মাল্টিপল শর্ত দিয়ে ফিল্টারিং
# শর্ত: বয়স > ৩০ এবং রান > ৬০০০
veteran_run_scorers = df[(df['age'] > 30) & (df['runs'] > 6000)]
print("Veteran Scorers:\n", veteran_run_scorers)

# একই কাজ query() দিয়ে
veteran_query = df.query("age > 30 and runs > 6000")
```

---

## ৪. মিসিং ডেটা প্রসেসিং (Handling Missing Values)

```python
# মিসিং ডেটাসহ উদাহরণ
df_missing = df.copy()
df_missing.loc[1, 'runs'] = np.nan
df_missing.loc[3, 'role'] = np.nan

# ১. মিসিং ভ্যালু গণনা
print("Missing Counts:\n", df_missing.isna().sum())
print("Missing Percentage:\n", df_missing.isna().mean() * 100)

# ২. ইম্পিউটেশন বা ড্রপ
# কলামের গড় দিয়ে ফিল করা
df_clean = df_missing.copy()
df_clean['runs'] = df_clean['runs'].fillna(df_clean['runs'].median())
# ক্যাটাগরিকাল মোড দিয়ে ফিল করা
df_clean['role'] = df_clean['role'].fillna(df_clean['role'].mode()[0])
```

---

## ৫. গ্রুপবাই ও ট্রান্সফর্মেশন (`groupby`, `agg`, `transform`)

মেশিন লার্নিংয়ে ফিচার ইঞ্জিনিয়ারিংয়ের অন্যতম শক্তিশালী হাতিয়ার হলো গ্রুপ পরিসংখ্যান তৈরি করা।

```python
# ১. এগ্রিগেশন (গ্রুপ ভেঙে ছোট করা)
role_summary = df.groupby('role').agg(
    total_runs=('runs', 'sum'),
    avg_matches=('matches', 'mean'),
    player_count=('name', 'count')
).reset_index()
print("Role Summary:\n", role_summary)

# ২. ট্রান্সফর্ম (Transform - অত্যন্ত গুরুত্বপূর্ণ ML ফিচার ইঞ্জিনিয়ারিং!)
# প্রতিটি রোল-এর গড় রান মূল ডেটাফ্রেমের প্রতিটি সারিতে নতুন ফিচার হিসেবে যুক্ত করা:
df['role_avg_runs'] = df.groupby('role')['runs'].transform('mean')
# খেলোয়াড়ের রান রোল-এর গড় থেকে কত কম বা বেশি
df['runs_above_role_avg'] = df['runs'] - df['role_avg_runs']
print("Engineered DataFrame:\n", df[['name', 'role', 'runs', 'runs_above_role_avg']])
```

---

## ৬. মেমরি অপটিমাইজেশন (Large Dataset Optimization)

বিশাল ডেটাসেটে র‍্যাম বাঁচানোর তিনটি সোনালী নিয়ম:
1. `int64` কে উপযুক্ত রেঞ্জ অনুসারে `int32`, `int16` বা `int8`-এ ডাউনকাস্ট করা।
2. `float64` কে `float32`-এ রূপান্তর করা।
3. কম ইউনিক মানবিশিষ্ট টেক্সট কলামকে `category` ডাটাতাইপে নেওয়া।

```python
def optimize_memory(df):
    start_mem = df.memory_usage(deep=True).sum() / 1024**2
    for col in df.columns:
        col_type = df[col].dtype
        if col_type == 'object':
            if df[col].nunique() / len(df[col]) < 0.5:
                df[col] = df[col].astype('category')
        elif str(col_type).startswith('int'):
            df[col] = pd.to_numeric(df[col], downcast='integer')
        elif str(col_type).startswith('float'):
            df[col] = pd.to_numeric(df[col], downcast='float')
    end_mem = df.memory_usage(deep=True).sum() / 1024**2
    print(f"Memory decreased from {start_mem:.2f} MB to {end_mem:.2f} MB ({(start_mem - end_mem)/start_mem * 100:.1f}% reduction)")
    return df
```
