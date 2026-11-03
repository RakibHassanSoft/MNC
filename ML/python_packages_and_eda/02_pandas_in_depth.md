# মাস্টার গাইড: মেশিন লার্নিংয়ের জন্য অ্যাডভান্সড Pandas ও ডেটা ম্যানিপুলেশন (Pandas Mastery)

> **"৮০% মেশিন লার্নিং কাজই হলো ডেটা প্রিপারেশন, ক্লিনিং ও ফিচার ইঞ্জিনিয়ারিং। আর পাইথন ইকোসিস্টেমে এই কাজের অবিসংবাদিত ভিত্তি হলো Pandas।"**

---

## 📑 সম্পূর্ণ বিষয়সূচি (Table of Contents)
1. [অভ্যন্তরীণ মেমরি আর্কিটেকচার (BlockManager & Columnar Internals)](#১-অভ্যন্তরীণ-মেমরি-আর্কিটেকচার)
2. [ফাইল ফরম্যাটস ডিপ ডাইভ: CSV বনাম Parquet বনাম Feather](#২-ফাইল-ফরম্যাটস-ডিপ-ডাইভ)
3. [মেমরি অপটিমাইজেশন ও টাইপ ডাউনকাস্টিং (Reducing Memory by 80%)](#৩-মেমরি-অপটিমাইজেশন-ও-টাইপ-ডাউনকাস্টিং)
4. [সিলেকশন, ফিল্টারিং ও কুয়েরি অপটিমাইজেশন (`.loc`, `.iloc`, `.query`)](#৪-সিলেকশন-ফিল্টারিং-ও-কুয়েরি-অপটিমাইজেশন)
5. [মিসিং ডেটা ও ডুপ্লিকেট প্রসেসিং](#৫-মিসিং-ডেটা-ও-ডুপ্লিকেট-প্রসেসিং)
6. [GroupBy স্প্লিট-অ্যাপ্লাই-কম্বাইন আর্কিটেকচার ও ফিচার ইঞ্জিনিয়ারিং (`agg`, `transform`)](#৬-groupby-স্প্লিট-অ্যাপ্লাই-কম্বাইন-আর্কিটেকচার)
7. [মার্জিং, জয়েন ও এন্টি-জয়েন (`merge`, `indicator=True`)](#৭-মার্জিং-জয়েন-ও-এন্টি-জয়েন)
8. [টাইম-সিরিজ ও ডেটটাইম ফিচার ইঞ্জিনিয়ারিং (`.dt`, Resampling, Rolling Windows)](#৮-টাইম-সিরিজ-ও-ডেটটাইম-ফিচার-ইঞ্জিনিয়ারিং)
9. [পিভট টেবিল ও ক্রস-ট্যাব ম্যানিপুলেশন (`pivot_table`, `crosstab`)](#৯-পিভট-টেবিল-ও-ক্রস-ট্যাব-ম্যানিপুলেশন)

---

## ১. অভ্যন্তরীণ মেমরি আর্কিটেকচার (BlockManager & Columnar Internals)

Pandas DataFrame দেখতে সাধারণ ২D স্প্রেডশিটের মতো হলেও ব্যাকএন্ডে এটি **BlockManager** নামক অবজেক্ট দিয়ে পরিচালিত হয়।
- সমজাতীয় ডেটা টাইপগুলো (যেমন সব `float64` কলাম) একটি একক ২D NumPy ব্লকে একত্রিত থাকে।
- সব `int64` কলাম আরেকটি ব্লকে থাকে।
- সব `object` (স্ট্রিং) কলাম আলাদা পয়েন্টির ব্লকে থাকে (যা সবচেয়ে বেশি মেমরি খায়)।

```
DataFrame:
  col_A (float64)  ┐
  col_B (float64)  ┴──► Block 1: 2D NumPy Float Array [2 x N]
  col_C (int32)    ────► Block 2: 1D NumPy Int Array   [1 x N]
  col_D (object)   ────► Block 3: Python Pointers Array [1 x N] (মেমরি ব্লাস্ট!)
```

---

## ২. ফাইল ফরম্যাটস ডিপ ডাইভ: CSV বনাম Parquet বনাম Feather

মেশিন লার্নিং ডেটাসেট সংরক্ষণে CSV ব্যবহার করা একটি বাজে অভ্যাস।

| মেট্রিক | CSV | Apache Parquet | Feather (Arrow) |
| :--- | :--- | :--- | :--- |
| **স্টোরেজ ফরম্যাট** | রো-ভিত্তিক (Row-based, Text) | কলাম-ভিত্তিক (Columnar, Binary) | মেমরি-ম্যাপড অ্যারো (Binary) |
| **কম্প্রেশন** | নেই (বড় ফাইল সাইজ) | স্ন্যাপি / ZSTD (৭০-৮৫% ছোট) | এলজেড৪ / আনকম্প্রেসড |
| **ডেটা টাইপ সংরক্ষণ** | হয় না (টাইপ অনুমান করতে হয়) | মেটাডেটায় টাইপ সংরক্ষিত থাকে | হুবহু সংরক্ষিত থাকে |
| **I/O গতি** | অত্যন্ত ধীর (Parsing overhead) | অত্যন্ত দ্রুত (পড়তে ৩-৫ গুণ দ্রুত) | অবিশ্বাস্য দ্রুত (RAM এ সরাসরি লোড) |
| **প্রেডিকেট পুশডাউন** | অসম্ভব (পুরো ফাইল পড়তে হয়) | **সম্ভব** (ডিস্ক থেকেই ফিল্টার করে) | আংশিক |

### Parquet দিয়ে নির্দিষ্ট কলাম ও রো পড়া (Predicate Pushdown):
```python
import pandas as pd

# ১০ জিবি ফাইলের মধ্য থেকে শুধু ২টি কলাম এবং নির্দিষ্ট রেঞ্জ পড়া (RAM বাঁচে!)
df_filtered = pd.read_parquet(
    "large_dataset.parquet",
    columns=["user_id", "transaction_amount"],
    filters=[("transaction_amount", ">", 500.0)]
)
```

---

## ৩. মেমরি অপটিমাইজেশন ও টাইপ ডাউনকাস্টিং (Reducing Memory by 80%)

ডিফল্টভাবে Pandas প্রতিটি ইন্টিজারকে `int64` (৮ বাইট) এবং ফ্লোটকে `float64` (৮ বাইট) এ রাখে। ক্যাটাগরিকাল স্ট্রিংকে অবজেক্টে রাখে। সঠিক ডাউনকাস্টিংয়ের মাধ্যমে ১ জিবির ডেটাফ্রেমকে নিমিষেই ১৮০ এমবিতে নামিয়ে আনা যায়।

```python
def optimize_dataframe_memory(df: pd.DataFrame) -> pd.DataFrame:
    """
    স্বয়ংক্রিয়ভাবে কলামগুলোর মান পর্যবেক্ষণ করে ক্ষুদ্রতম মেমরি টাইপে ডাউনকাস্ট করে।
    """
    initial_mem = df.memory_usage(deep=True).sum() / 1024**2
    print(f"প্রাথমিক মেমরি ব্যবহার: {initial_mem:.2f} MB")

    for col in df.columns:
        col_type = df[col].dtype

        # ইন্টিজার ডাউনকাস্টিং
        if np.issubdtype(col_type, np.integer):
            df[col] = pd.to_numeric(df[col], downcast='integer')
        
        # ফ্লোট ডাউনকাস্টিং
        elif np.issubdtype(col_type, np.floating):
            df[col] = pd.to_numeric(df[col], downcast='float')
            
        # স্ট্রিং অবজেক্টকে ক্যাটাগরিতে রূপান্তর (যদি ইউনিক ভ্যালু < ৫০% হয়)
        elif col_type == 'object':
            num_unique = df[col].nunique()
            num_total = len(df[col])
            if num_unique / num_total < 0.5:
                df[col] = df[col].astype('category')

    final_mem = df.memory_usage(deep=True).sum() / 1024**2
    reduction = ((initial_mem - final_mem) / initial_mem) * 100
    print(f"অপটিমাইজড মেমরি: {final_mem:.2f} MB (মেমরি হ্রাস পেয়েছে: {reduction:.2f}%)")
    return df
```

---

## ৪. সিলেকশন, ফিল্টারিং ও কুয়েরি অপটিমাইজেশন

- **`.iloc`:** ইনটিজার পজিশন ভিত্তিক (০ থেকে $N-1$)।
- **`.loc`:** লেবেল বা ইন্ডেক্স নাম ভিত্তিক।
- **`.query()`:** ব্র্যাকেট ওভারহেড ছাড়া দ্রুত ফিল্টারিং (C-লেভেলে অপটিমাইজড)।

```python
# কমপ্লেক্স ফিল্টারিং
# শর্ত: বয়স ২৫ থেকে ৪০ এবং দেশ বাংলাদেশ অথবা আয় > ৫০,০০০
subset = df.query("(25 <= age <= 40 and country == 'Bangladesh') or income > 50000")
```

---

## ৫. মিসিং ডেটা ও ডুপ্লিকেট প্রসেসিং

```python
# ১. কলামভিত্তিক মিসিং হার
missing_ratio = df.isna().mean().sort_values(ascending=False)

# ২. ডুপ্লিকেট হ্যান্ডলিং (ইউজার আইডি ধরে সর্বশেষ রেকর্ড রাখা)
df_dedup = df.drop_duplicates(subset=['user_id'], keep='last')

# ৩. কন্ডিশনাল ফরোয়ার্ড ফিল (টাইম-সিরিজ ডেটায়)
df['stock_price'] = df['stock_price'].ffill().bfill()
```

---

## ৬. GroupBy স্প্লিট-অ্যাপ্লাই-কম্বাইন আর্কিটেকচার

### ৬.১ `.agg()` বনাম `.transform()` এর মৌলিক পার্থক্য:
- **`.agg()`:** প্রতিটি গ্রুপের জন্য একটি একক মান বের করে সারি সংখ্যা কমিয়ে দেয়।
- **`.transform()`:** গ্রুপের হিসাব বের করে মূল ডেটাফ্রেমের প্রতিটি সারির সাথে হুবহু সমান দৈর্ঘ্যে সম্প্রসারিত করে ফিরিয়ে দেয় (ফিচার তৈরিতে অপরিহার্য!)।

```python
# ফিচার ইঞ্জিনিয়ারিং উদাহরণ:
# ইউজারের বর্তমান ট্রানজ্যাকশন তার নিজস্ব গড়ের তুলনায় কত শতাংশ বেশি?
df['user_avg_spend'] = df.groupby('user_id')['amount'].transform('mean')
df['user_spend_std'] = df.groupby('user_id')['amount'].transform('std')

# Z-score ফিচার তৈরি
df['spend_zscore'] = (df['amount'] - df['user_avg_spend']) / (df['user_spend_std'] + 1e-6)
```

---

## ৭. মার্জিং, জয়েন ও এন্টি-জয়েন

```python
# এন্টি-জয়েন (Anti-Join): এমন গ্রাহক যারা কখনো কোনো কমপ্লেন করেনি
users = pd.DataFrame({'user_id': [1, 2, 3, 4], 'name': ['A', 'B', 'C', 'D']})
complaints = pd.DataFrame({'user_id': [2, 4], 'issue': ['Slow', 'Bug']})

# indicator=True দিয়ে মার্জ
merged = pd.merge(users, complaints, on='user_id', how='left', indicator=True)
non_complaining_users = merged[merged['_merge'] == 'left_only'].drop(columns=['_merge'])
print("Anti-join Result (No complaints):\n", non_complaining_users)
```

---

## ৮. টাইম-সিরিজ ও ডেটটাইম ফিচার ইঞ্জিনিয়ারিং

```python
# স্ট্রিং থেকে ডেটটাইম পার্স
df['timestamp'] = pd.to_datetime(df['timestamp'])

# ১. সাইক্লিক ও ক্যালেন্ডার ফিচার এক্সট্র্যাক্ট
df['hour'] = df['timestamp'].dt.hour
df['day_of_week'] = df['timestamp'].dt.dayofweek
df['is_weekend'] = df['day_of_week'].isin([5, 6]).astype(int)

# ২. রোলিং উইন্ডো (রোলিং ৭ দিনের এভারেজ)
df = df.sort_values('timestamp').reset_index(drop=True)
df['rolling_7d_sales'] = df['amount'].rolling(window=7, min_periods=1).mean()
```

---

## ৯. পিভট টেবিল ও ক্রস-ট্যাব ম্যানিপুলেশন

```python
# পিভট টেবিল: বিভিন্ন বয়সের গ্রাহকরা বিভিন্ন পেমেন্ট মেথডে গড়ে কত টাকা খরচ করে?
pivot_res = pd.pivot_table(
    df,
    values='amount',
    index='age_group',
    columns='payment_method',
    aggfunc=['mean', 'count'],
    fill_value=0
)
print("Pivot Table Summary:\n", pivot_res)
```
