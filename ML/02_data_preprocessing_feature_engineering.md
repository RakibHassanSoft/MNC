# অধ্যায় ২: ডেটা প্রিপ্রসেসিং এবং ফিচার ইঞ্জিনিয়ারিং (Data Preprocessing & Feature Engineering)

> *"Garbage In, Garbage Out: আপনি মডেলে যত নিখুঁত অ্যালগরিদমই ব্যবহার করুন না কেন, যদি ইনপুট ডেটা অশুদ্ধ বা ত্রুটিযুক্ত হয়, মডেলের ফলাফল কখনোই নির্ভরযোগ্য হবে না।"*

বাস্তব জগতের ডেটা সাধারণত অসম্পূর্ণ (Incomplete), কোলাহলপূর্ণ (Noisy), অসংলগ্ন (Inconsistent) এবং ভারসাম্যহীন (Imbalanced) থাকে। এই অধ্যায়ে আমরা শিখব কীভাবে কাঁচা ডেটাকে মেশিন লার্নিং অ্যালগরিদমের উপযুক্ত গাণিতিক বিন্যাসে রূপান্তর করা যায়।

---

## ২.১ মিসিং ডেটা হ্যান্ডলিং (Handling Missing Data)

### ২.১.১ মিসিং ডেটার প্রকারভেদ
1. **MCAR (Missing Completely at Random):** ডেটা হারানোর সাথে অন্য কোনো ফিচারের কোনো সম্পর্ক নেই (যেমন: সার্ভার গ্লিচের কারণে কিছু ফর্ম জমা পড়েনি)।
2. **MAR (Missing at Random):** ডেটা হারানোর কারণ অন্য কোনো পর্যবেক্ষণযোগ্য ফিচারের ওপর নির্ভর করে (যেমন: নারীরা প্রায়ই বয়স জানাতে অনীহা প্রকাশ করেন, এখানে জেন্ডারের সাথে সম্পর্কের কারণে বয়স মিসিং)।
3. **MNAR (Missing Not at Random):** যে ফিচারটি মিসিং, তার নিজস্ব মূল্যের কারণেই সেটি অনুপস্থিত (যেমন: অত্যন্ত উচ্চ আয়ের ব্যক্তিরা তাদের আসল আয় গোপন করেন)।

### ২.১.২ ইম্পিউটেশন কৌশল ও গাণিতিক তুলনা

| পদ্ধতি | উপযুক্ত ক্ষেত্র | গাণিতিক নিয়ম | সীমাবদ্ধতা |
| :--- | :--- | :--- | :--- |
| **Mean Imputation** | নরমাল ডিস্ট্রিবিউশন | $\hat{x}_i = \frac{1}{N}\sum x_j$ | আউটলায়ারের প্রতি অত্যন্ত সংবেদনশীল; ভ্যারিয়্যান্স কমিয়ে দেয় |
| **Median Imputation** | স্কিউড (Skewed) ডেটা | $\hat{x}_i = \text{Median}(X)$ | আউটলায়ার প্রতিরোধী, তবে ফিচারের সম্পর্ক রক্ষা করতে পারে না |
| **Mode Imputation** | ক্যাটাগরিকাল ডেটা | $\hat{x}_i = \arg\max_c \text{Count}(c)$ | ডেটার বৈচিত্র্য হ্রাস পেতে পারে |
| **KNN Imputation** | পারস্পরিক নির্ভরশীল ডেটা | নিকটবর্তী $K$ প্রতিবেশীর দূরত্বের ভারযুক্ত গড় | কম্পিউটেশনাল সময় বেশি ($O(N^2)$) |

#### কে-এন-এন ইম্পিউটার দূরত্ব সমীকরণ (Nan-Euclidean Distance):
দুটি পর্যবেক্ষণ $\mathbf{x}$ এবং $\mathbf{y}$-এর মধ্যে যেখানে কিছু মান মিসিং:
$$d(\mathbf{x}, \mathbf{y}) = \sqrt{\frac{p}{p_{common}} \sum_{i \in \text{common}} (x_i - y_i)^2}$$
এখানে $p$ হলো মোট ফিচার সংখ্যা এবং $p_{common}$ হলো উভয়ের মধ্যে উপস্থিত সাধারণ ফিচার সংখ্যা।

---

## ২.২ আউটলায়ার ডিটেকশন ও ট্রিটমেন্ট (Outlier Detection & Treatment)

আউটলায়ার হলো এমন কিছু অস্বাভাবিক মান যা ডেটাসেটের সামগ্রিক প্যাটার্ন থেকে বিচ্ছিন্ন।

```
    Normal Distribution
           │
         ╭─┴─╮
       ╭─╯   ╰─╮
  ───╭─╯       ╰─╮───  ● (Outlier)
    -3σ  0   +3σ
```

### ২.২.১ জেড-স্কোর পদ্ধতি (Z-Score Method)
যদি ডেটা স্বাভাবিক বিন্যাস (Normal Distribution) মেনে চলে:
$$z = \frac{x - \mu}{\sigma}$$
- সাধারণ নিয়ম: যদি $|z| > 3$ হয়, তবে বিন্দুটিকে আউটলায়ার হিসেবে চিহ্নিত করা হয় (যেহেতু ৯৯.৭% ডেটা $\mu \pm 3\sigma$-এর মধ্যে থাকে)।

### ২.২.২ আইকিউআর পদ্ধতি (Interquartile Range - IQR Method)
অপ্রতিসম বা স্কিউড ডেটার ক্ষেত্রে বক্সপ্লট নীতি ব্যবহার করা হয়:
1. প্রথম কোয়ার্টাইল ($Q_1$ = ২৫তম পার্সেন্টাইল) এবং তৃতীয় কোয়ার্টাইল ($Q_3$ = ৭৫তম পার্সেন্টাইল) বের করা।
2. $\text{IQR} = Q_3 - Q_1$
3. গ্রহণযোগ্য সীমানা নির্ধারণ:
   $$\text{Lower Bound} = Q_1 - 1.5 \times \text{IQR}$$
   $$\text{Upper Bound} = Q_3 + 1.5 \times \text{IQR}$$
- সীমানার বাইরের যেকোনো মানকে আউটলায়ার বিবেচনা করে ক্যাপ (Winsorization) বা অপসারণ করা হয়।

---

## ২.৩ ফিচার স্কেলিং ও রূপান্তর (Feature Scaling & Transformations)

### ২.৩.১ প্রধান স্কেলিং মেথডস

1. **স্ট্যান্ডার্ডাইজেশন (StandardScaler):**
   $$x' = \frac{x - \mu}{\sigma}$$
   - গড় $\mu=0$ এবং ভ্যারিয়্যান্স $\sigma^2=1$ হবে। অপটিমাইজেশন অ্যালগরিদম (যেমন গ্রেডিয়েন্ট ডিসেন্ট) দ্রুত কনভার্জ করে।
2. **মিন-ম্যাক্স নরমালাইজেশন (MinMaxScaler):**
   $$x' = \frac{x - x_{min}}{x_{max} - x_{min}}$$
   - মানসমূহ $[0, 1]$-এর মধ্যে সীমাবদ্ধ থাকে। নিউরাল নেটওয়ার্ক এবং ইমেজ প্রসেসিংয়ে আদর্শ।
3. **রোবাস্ট স্কেলার (RobustScaler):**
   $$x' = \frac{x - \text{Median}(X)}{\text{IQR}}$$
   - যখন ডেটাতে প্রচুর আউটলায়ার থাকে, তখন এটি আউটলায়ারের প্রভাব মুক্ত স্কেলিং নিশ্চিত করে।

### ২.৩.২ পাওয়ার ট্রান্সফর্মেশন (Power Transformations)
যদি ফিচারগুলো অতিরিক্ত স্কিউড (Right/Left skewed) হয়, তবে লিনিয়ার মডেল ভালো কাজ করতে পারে না।
- **লগ ট্রান্সফর্মেশন:** $y = \ln(x + 1)$ (শুধুমাত্র ধনাত্মক মানের জন্য)।
- **বক্স-কক্স ট্রান্সফর্মেশন (Box-Cox Transformation):**
  $$y^{(\lambda)} = \begin{cases} \frac{x^\lambda - 1}{\lambda}, & \text{if } \lambda \neq 0 \\ \ln(x), & \text{if } \lambda = 0 \end{cases} \quad (x > 0)$$
  ম্যাক্সিমাম লাইকলিহুড দিয়ে সেরা $\lambda$ মান বের করা হয় যাতে ডেটা নরমাল ডিস্ট্রিবিউশনে পরিণত হয়।

---

## ২.৪ ক্যাটাগরিকাল ভ্যারিয়েবল এনকোডিং

### ২.৪.১ ওয়ান-হট এনকোডিং ও ডামি ভ্যারিয়েবল ট্র্যাপ (Dummy Variable Trap)
যদি কোনো ক্যাটাগরিতে $K$ টি ভিন্ন মান থাকে, তবে $K$ টি বাইনারি কলাম তৈরি হয়।
- **বহুরৈখিকতা সমস্যা (Multicollinearity):**
  প্রথম $K-1$ টি কলামের মান জানা থাকলে $K$-তম কলামের মান স্বয়ংক্রিয়ভাবে অনুমান করা যায়:
  $$x_K = 1 - \sum_{j=1}^{K-1} x_j$$
  এর ফলে ডিজাইন ম্যাট্রিক্স $X^T X$ সিঙ্গুলার (Non-invertible) হয়ে পড়ে।
- **সমাধান:** সর্বদা প্রথম কলাম ড্রপ করতে হবে (`drop='first'`), ফলে $K-1$ টি ডামি ভ্যারিয়েবল তৈরি হবে।

### ২.৪.২ টার্গেট এনকোডিং (Target / Mean Encoding with Smoothing)
হাই-কার্ডিনালিটি ক্যাটাগরিকাল ফিচারের ক্ষেত্রে (যেমন: পোস্ট কোড যেখানে ১০০০টি ক্যাটাগরি আছে) ওয়ান-হট এনকোডিং ডাইমেনশন বাড়িয়ে দেয়। টার্গেট এনকোডিংয়ে প্রতিটি ক্যাটাগরিকে তার সংশ্লিষ্ট টার্গেটের গড় মান দিয়ে প্রতিস্থাপন করা হয়।

**ওভারফিটিং রোধে স্মুথিং সমীকরণ:**
$$S_i = \frac{n_i \cdot \bar{y}_i + m \cdot \bar{y}_{global}}{n_i + m}$$
- $n_i$: ক্যাটাগরি $i$-এর নমুনার সংখ্যা
- $\bar{y}_i$: ক্যাটাগরি $i$-এর টার্গেটের গড়
- $\bar{y}_{global}$: পুরো ডেটাসেটের টার্গেটের গড়
- $m$: ওজন বা স্মুথিং প্যারামিটার

---

## ২.৫ ফিচার সিলেকশন মেথডস (Feature Selection)

1. **ফিল্টার পদ্ধতি (Filter Methods):**
   - পিয়ারসন কোরিলেশন: কন্টিনিউয়াস ফিচার ও টার্গেটের মধ্যে সম্পর্ক।
   - কাই-স্কয়ার টেস্ট ($\chi^2$ Test): ক্যাটাগরিকাল বনাম ক্যাটাগরিকাল সম্পর্কের জন্য:
     $$\chi^2 = \sum \frac{(O - E)^2}{E}$$
     যেখানে $O$ হলো পর্যবেক্ষিত মান এবং $E$ হলো প্রত্যাশিত মান।
2. **র‍্যাপার পদ্ধতি (Wrapper Methods):**
   - রিকার্সিভ ফিচার এলিমিনেশন (RFE): বারবার মডেল ট্রেন করে সবচেয়ে কম গুরুত্বপূর্ণ ফিচারটি বাদ দেওয়া হয়।
3. **এমবেডেড পদ্ধতি (Embedded Methods):**
   - ল্যাসো রিগ্রেশন ($L_1$ Penalty) অথবা ডিসিশন ট্রির ফিচার ইম্পরট্যান্স।

---

## ২.৬ পাইথন কোড: প্রোডাকশন রেডি প্রিপ্রসেসিং পাইপলাইন

```python
import numpy as np
import pandas as pd
from sklearn.base import BaseEstimator, TransformerMixin
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.impute import SimpleImputer

# কাস্টম আউটলায়ার ক্যাপার (IQR পদ্ধতি)
class IQRCapper(BaseEstimator, TransformerMixin):
    def __init__(self, factor=1.5):
        self.factor = factor
        self.bounds_ = {}

    def fit(self, X, y=None):
        X_df = pd.DataFrame(X)
        for col in X_df.columns:
            q1 = X_df[col].quantile(0.25)
            q3 = X_df[col].quantile(0.75)
            iqr = q3 - q1
            self.bounds_[col] = (q1 - self.factor * iqr, q3 + self.factor * iqr)
        return self

    def transform(self, X):
        X_df = pd.DataFrame(X).copy()
        for col in X_df.columns:
            lower, upper = self.bounds_[col]
            X_df[col] = np.clip(X_df[col], lower, upper)
        return X_df.values

# টেস্ট পাইপলাইন
if __name__ == "__main__":
    df = pd.DataFrame({
        'age': [25, 30, np.nan, 45, 120], # 120 একটি আউটলায়ার
        'income': [50000, 60000, 80000, 110000, 250000],
        'city': ['Dhaka', 'Chittagong', 'Dhaka', 'Sylhet', 'Chittagong']
    })

    num_cols = ['age', 'income']
    cat_cols = ['city']

    num_pipeline = Pipeline([
        ('imputer', SimpleImputer(strategy='median')),
        ('capper', IQRCapper()),
        ('scaler', StandardScaler())
    ])

    cat_pipeline = Pipeline([
        ('imputer', SimpleImputer(strategy='most_frequent')),
        ('encoder', OneHotEncoder(drop='first', sparse_output=False))
    ])

    full_processor = ColumnTransformer([
        ('num', num_pipeline, num_cols),
        ('cat', cat_pipeline, cat_cols)
    ])

    processed_data = full_processor.fit_transform(df)
    print("Preprocessed & Scaled Output Shape:", processed_data.shape)
    print("Transformed Feature Matrix:\n", processed_data)
```
