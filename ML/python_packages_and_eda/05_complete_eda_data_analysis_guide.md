# মাস্টার গাইড: বাংলায় সম্পূর্ণ এক্সপ্লোরেটরি ডেটা অ্যানালাইসিস (Complete Exploratory Data Analysis - EDA)

> **"Torture the data, and it will confess to anything." — Ronald Coase**
>
> এক্সপ্লোরেটরি ডেটা অ্যানালাইসিস (EDA) হলো ডেটা সায়েন্সের সবচেয়ে গুরুত্বপূর্ণ গোয়েন্দাগিরি। মডেলিং শুরু করার আগে ডেটার চরিত্র, অদ্ভুত অসংগতি, লুকানো সম্পর্ক এবং ব্যবসায়িক অন্তর্দৃষ্টি উন্মোচন করাই এর মূল লক্ষ্য।

---

## 🧭 ডেটা অ্যানালাইসিসের ধাপে ধাপে বাস্তব রোডম্যাপ (EDA Framework)

```
┌────────────────────────────────────────────────────────┐
│ ধাপ ১: ব্যবসায়িক সমস্যা ও ডোমেইন আন্ডারস্ট্যান্ডিং    │
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│ ধাপ ২: প্রাথমিক স্ক্যান ও স্বাস্থ্য পরীক্ষা (Sanity) │
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│ ধাপ ৩: মিসিং ডেটা ও ডুপ্লিকেট অ্যানালাইসিস            │
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│ ধাপ ৪: একক চলক বিশ্লেষণ (Univariate Analysis)          │
│   • নিউমেরিকাল: Mean, Median, Skewness, Kurtosis       │
│   • ক্যাটাগরিকাল: Frequency, Cardinality, Imbalance     │
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│ ধাপ ৫: দ্বি-চলক বিশ্লেষণ (Bivariate Analysis)           │
│   • Num vs Num (Scatter, Correlation)                  │
│   • Cat vs Num (Boxplot, Violin, ANOVA)                │
│   • Cat vs Cat (Crosstab, Chi-square Test)             │
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│ ধাপ ৬: বহু-চলক ও মাল্টিকোলিনিয়ারিটি (VIF & Heatmap)   │
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│ ধাপ ৭: আউটলায়ার অ্যানালাইসিস (IQR & Isolation)         │
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│ ধাপ ৮: পরিসংখ্যানিক হাইপোথিসিস টেস্টিং ও সিদ্ধান্ত   │
└────────────────────────────────────────────────────────┘
```

---

## ধাপ ১: ডোমেইন ও ব্যবসায়িক সমস্যা বোঝা
ডেটা অ্যানালাইসিসের প্রথম কাজ হলো প্রশ্নের উত্তর খোঁজা:
- **টার্গেট ভ্যারিয়েবল কী?** আমরা কি কিছু প্রেডিক্ট করতে চাচ্ছি (Supervised) নাকি কোনো লেবেল নেই (Unsupervised)?
- **টার্গেট কোন ধরনের?** কন্টিনিউয়াস সংখ্যা (Regression) নাকি নির্দিষ্ট ক্যাটাগরি (Classification)?
- **ডেটার প্রতিটি কলামের বাস্তব অর্থ কী (Data Dictionary)?** কোনো কলাম কি ভবিষ্যৎ থেকে আসা তথ্য (Data Leakage ঝুঁকি)?

---

## ধাপ ২: প্রাথমিক স্ক্যান ও স্বাস্থ্য পরীক্ষা (Initial Data Sanity Check)

```python
import pandas as pd
import numpy as np

# ডেটা লোড
df = pd.read_csv("your_dataset.csv")

# ১. আকার ও নমুনা দেখা
print(f"Dataset Shape: {df.shape[0]} সারি, {df.shape[1]} কলাম")
print("\nপ্রথম ৫টি সারি:")
display(df.head())

# ২. কলাম টাইপ ও মেমরি পরীক্ষা
print("\nডেটা টাইপ ও মেমরি:")
df.info()

# ৩. ডুপ্লিকেট সারি চেক
duplicate_rows = df.duplicated().sum()
print(f"\nডুপ্লিকেট সারির সংখ্যা: {duplicate_rows} ({(duplicate_rows/len(df))*100:.2f}%)")
if duplicate_rows > 0:
    df = df.drop_duplicates().reset_index(drop=True)
    print("ডুপ্লিকেট সারি মুছে ফেলা হয়েছে।")
```

---

## ধাপ ৩: মিসিং ডেটা বিশ্লেষণ ও প্রোফাইলিং

মিসিং ডেটা দেখা মাত্রই গড়ে বা মধ্যমায় ফিল করে দেওয়া একটি মারাত্মক ভুল। প্রথমে এর প্রকৃতি বুঝতে হবে:

```python
# মিসিং শতাংশের টেবিল
missing_df = pd.DataFrame({
    'Missing_Count': df.isna().sum(),
    'Percentage (%)': np.round((df.isna().sum() / len(df)) * 100, 2)
})
missing_df = missing_df[missing_df['Missing_Count'] > 0].sort_values(by='Percentage (%)', ascending=False)
print("মিসিং ভ্যালুর পরিসংখ্যান:\n", missing_df)

# ভিজ্যুয়ালাইজেশন: মিসিং হিটম্যাপ
import seaborn as sns
import matplotlib.pyplot as plt

plt.figure(figsize=(10, 4))
sns.heatmap(df.isna(), cbar=False, cmap='viridis', yticklabels=False)
plt.title("Missing Value Distribution Pattern")
plt.show()
```

**সিদ্ধান্তের নীতি:**
- মিসিং $> 60\%$: কলামটি ফেলে দেওয়া যায়, যদি না এটি অত্যন্ত গুরুত্বপূর্ণ ডোমেইন ফিচার হয়।
- মিসিং $< 5\%$: সারিগুলো ড্রপ করা অথবা সাধারণ ইম্পিউটেশন (Mean/Median/Mode) নিরাপদ।
- মিসিং $5\% - 60\%$: রিগ্রেশন, KNN বা ডোমেইন-স্পেসিফিক শর্তের মাধ্যমে ইম্পিউট করা।

---

## ধাপ ৪: একক চলক বিশ্লেষণ (Univariate Analysis)

প্রতিটি ফিচারকে আলাদা আলাদাভাবে নিয়ে তার নিজস্ব বৈশিষ্ট্য যাচাই করা।

### ৪.১ নিউমেরিক্যাল ফিচারের বিশ্লেষণ
```python
num_cols = df.select_dtypes(include=[np.number]).columns

for col in num_cols:
    # পরিসংখ্যানিক পরিমাপ
    mean_val = df[col].mean()
    median_val = df[col].median()
    skew_val = df[col].skew()
    kurt_val = df[col].kurtosis()

    fig, axes = plt.subplots(1, 2, figsize=(12, 3.5))
    
    # হিস্টোগ্রাম ও KDE
    sns.histplot(df[col], kde=True, ax=axes[0], color='#1f77b4')
    axes[0].axvline(mean_val, color='red', linestyle='--', label=f'Mean: {mean_val:.2f}')
    axes[0].axvline(median_val, color='green', linestyle='-', label=f'Median: {median_val:.2f}')
    axes[0].set_title(f"{col} Distribution\nSkewness: {skew_val:.2f} | Kurtosis: {kurt_val:.2f}")
    axes[0].legend()

    # বক্সপ্লট (আউটলায়ার দেখতে)
    sns.boxplot(x=df[col], ax=axes[1], color='#aec7e8')
    axes[1].set_title(f"{col} Boxplot (Outliers)")
    plt.tight_layout()
    plt.show()
```

**স্কিউনেস (Skewness) বোঝার নিয়ম:**
- **$|\text{Skewness}| < 0.5$:** ডেটা প্রায় সিমেট্রিক বা স্বাভাবিক।
- **$\text{Skewness} > +1.0$ (Right-skewed / ধনাত্মক):** ডানদিকে লম্বা লেজ রয়েছে (যেমন: মানুষের বেতন, বাড়ির দাম)। এখানে $\text{Mean} > \text{Median}$। এর জন্য **লগ ট্রান্সফর্মেশন ($\log(1+x)$)** বা বক্স-কক্স প্রয়োজন।
- **$\text{Skewness} < -1.0$ (Left-skewed / ঋণাত্মক):** বামদিকে লম্বা লেজ।

### ৪.২ ক্যাটাগরিকাল ফিচারের বিশ্লেষণ
```python
cat_cols = df.select_dtypes(include=['object', 'category']).columns

for col in cat_cols:
    cardinality = df[col].nunique()
    print(f"\n--- {col} (ইউনিক ক্যাটাগরি: {cardinality}) ---")
    
    # ফ্রিকোয়েন্সি টেবিল
    freq_table = df[col].value_counts(dropna=False)
    perc_table = df[col].value_counts(normalize=True, dropna=False) * 100
    summary = pd.DataFrame({'Count': freq_table, 'Percentage (%)': np.round(perc_table, 2)})
    display(summary.head(10)) # শীর্ষ ১০টি

    # টপ ক্যাটাগরিগুলোর বারচার্ট
    plt.figure(figsize=(8, 3))
    sns.countplot(data=df, x=col, order=df[col].value_counts().index[:10], palette='viridis')
    plt.title(f"{col} Frequency (Top 10)")
    plt.xticks(rotation=45)
    plt.show()
```

---

## ধাপ ৫: দ্বি-চলক বিশ্লেষণ (Bivariate Analysis: Feature vs Target)

### ৫.১ কন্টিনিউয়াস বনাম কন্টিনিউয়াস (Scatter Plot & Pearson Correlation)
```python
# লক্ষ্য: আয় বাড়লে কি খরচ বাড়ে?
sns.jointplot(data=df, x='Income', y='Spend', kind='reg', color='#2ca02c')
plt.show()
```

### ৫.২ ক্যাটাগরিকাল বনাম কন্টিনিউয়াস (Grouped Boxplot & T-test)
```python
# লক্ষ্য: চার্ন করা গ্রাহকদের মাসিক খরচ কি বেশি?
plt.figure(figsize=(6, 4))
sns.boxplot(data=df, x='is_churn', y='monthly_charges', palette='Set1')
plt.title("Monthly Charges by Churn Status")
plt.show()

# হাইপোথিসিস টেস্টিং: দুই গ্রুপের গড় খরচে কি আসলেই পরিসংখ্যানিক পার্থক্য আছে? (Two-sample T-test)
from scipy import stats
group_churn = df[df['is_churn'] == 1]['monthly_charges'].dropna()
group_stay = df[df['is_churn'] == 0]['monthly_charges'].dropna()
t_stat, p_val = stats.ttest_ind(group_churn, group_stay, equal_var=False)
print(f"T-statistic: {t_stat:.4f}, P-value: {p_val:.4e}")
if p_val < 0.05:
    print("সিদ্ধান্ত: দুই দলের খরচে পরিসংখ্যানগতভাবে তাৎপর্যপূর্ণ (Significant) পার্থক্য রয়েছে! (p < 0.05)")
```

### ৫.৩ ক্যাটাগরিকাল বনাম ক্যাটাগরিকাল (Cross-tabulation & Chi-Square Test)
```python
# লক্ষ্য: চুক্তির ধরনের (Contract) সাথে চার্ন হওয়ার সম্পর্ক
cross_tab = pd.crosstab(df['contract'], df['is_churn'], normalize='index') * 100
print("চুক্তির ধরণ অনুযায়ী চার্ন শতকরা হার (%):\n", cross_tab)

# কাই-স্কয়ার টেস্ট (Chi-Square Independence Test)
chi2, p_val, dof, ex = stats.chi2_contingency(pd.crosstab(df['contract'], df['is_churn']))
print(f"Chi2: {chi2:.4f}, P-value: {p_val:.4e}")
if p_val < 0.05:
    print("সিদ্ধান্ত: চুক্তির ধরনের সাথে চার্ন হওয়ার শক্তিশালী নির্ভরশীলতা রয়েছে!")
```

---

## ধাপ ৬: মাল্টিকোলিনিয়ারিটি ও VIF নির্ণয় (Multivariate Analysis)

উচ্চমাত্রিক মাল্টিকোলিনিয়ারিটি লিনিয়ার মডেলের সহগকে অস্থিতিশীল করে ফেলে।

```python
from statsmodels.stats.outliers_influence import variance_inflation_factor

# নিউমেরিক্যাল কলামের VIF নির্ণয়
X_numeric = df.select_dtypes(include=[np.number]).dropna()
# কনস্ট্যান্ট কলাম যোগ
X_numeric_const = pd.DataFrame(X_numeric.copy())

vif_data = pd.DataFrame()
vif_data["Feature"] = X_numeric_const.columns
vif_data["VIF"] = [variance_inflation_factor(X_numeric_const.values, i) for i in range(X_numeric_const.shape[1])]
vif_data = vif_data.sort_values(by="VIF", ascending=False)
print("Variance Inflation Factor (VIF):\n", vif_data)
```
- **VIF > 5 বা 10:** মারাত্মক বহুরৈখিকতা নির্দেশ করে। দুটি অতিরিক্ত কোরিলেটেড ফিচারের যেকোনো একটি বাদ দিতে হবে।

---

## ধাপ ৭: আউটলায়ার ডিটেকশন ও ব্যবসায়িক বিবেচনা

```python
def detect_outliers_iqr(df, column):
    q1 = df[column].quantile(0.25)
    q3 = df[column].quantile(0.75)
    iqr = q3 - q1
    lower_bound = q1 - 1.5 * iqr
    upper_bound = q3 + 1.5 * iqr
    outliers = df[(df[column] < lower_bound) | (df[column] > upper_bound)]
    print(f"{column}: মোট আউটলায়ার সংখ্যা = {len(outliers)} ({len(outliers)/len(df)*100:.2f}%)")
    return lower_bound, upper_bound
```

---

## 📝 EDA সারাংশ ও রিপোর্ট তৈরির চেকলিস্ট (Final Insights Checklist)

ডেটা অ্যানালাইসিস শেষ করে সর্বদা একটি সংক্ষিপ্ত কার্যকরী রিপোর্ট তৈরি করুন:
1. **ডেটার মোট আকার ও ক্লিনড স্যাম্পল সংখ্যা।**
2. **টার্গেট ভ্যারিয়েবলের ডিস্ট্রিবিউশন:** ক্লাস ব্যালেন্সড নাকি ইমব্যালান্সড?
3. **সবচেয়ে প্রভাবশালী শীর্ষ ৩টি ফিচার:** টার্গেটের সাথে যাদের কোরিলেশন বা নির্ভরতা সর্বোচ্চ।
4. **ডেটা ক্লিনিং সিদ্ধান্তসমূহ:** কোন কলাম কীভাবে ইম্পিউট করা হলো, কোন আউটলায়ার কেন রাখা বা সরানো হলো।
5. **ফিচার ইঞ্জিনিয়ারিং প্রস্তাবনা:** যেমন দুটি কলামের অনুপাত, বাকেট করা ইত্যাদি।
