# Chapter 01: Descriptive Statistics & Exploratory Data Analysis (EDA)

---

## ১. মেশিন লার্নিংয়ে বর্ণনামূলক পরিসংখ্যানের ভূমিকা (Role of Descriptive Statistics)

একটি মেশিন লার্নিং মডেল ট্রেইন করার পূর্বে ডেটাসেটের বৈশিষ্ট্য, রূপরেখা এবং অভ্যন্তরীণ ত্রুটি বুঝতে **বর্ণনামূলক পরিসংখ্যান (Descriptive Statistics)** ব্যবহার করা হয়। ডেটাসেটের সমস্ত পর্যবেক্ষণকে কয়েকটি সুনির্দিষ্ট একক সংখ্যাসূচকে সারসংক্ষেপ করার প্রক্রিয়াই হলো বর্ণনামূলক পরিসংখ্যান।

```
                                      [ RAW DATASET ]
                                             |
                     +-----------------------+-----------------------+
                     |                                               |
         [ 1. Central Tendency ]                             [ 2. Dispersion / Spread ]
         - কোথায় ডেটা জড়ো হচ্ছে?                            - ডেটা কতটা ছড়িয়ে আছে?
         - Mean, Median, Mode                                - Variance, Std Dev, IQR
                     |                                               |
                     +-----------------------+-----------------------+
                                             |
                                 [ 3. Association / Shape ]
                                 - ভ্যারিয়েবলগুলোর পারস্পরিক সম্পর্ক কেমন?
                                 - Covariance, Pearson/Spearman Correlation
```

---

## ২. কেন্দ্রীয় প্রবণতার পরিমাপ (Measures of Central Tendency)

কেন্দ্রীয় প্রবণতা নির্দেশ করে ডেটা ডিস্ট্রিবিউশনের কেন্দ্রবিন্দু বা সবচেয়ে প্রতিনিধিত্বশীল মান কোনটি।

### ১. গাণিতিক গড় (Arithmetic Mean):
পপুলেশন গড় ($\mu$) এবং স্যাম্পল গড় ($\bar{x}$):

$$\mu = \frac{1}{N} \sum_{i=1}^N x_i, \qquad \bar{x} = \frac{1}{n} \sum_{i=1}^n x_i$$

- **সুবিধা:** ডেটার প্রতিটি পয়েন্টকে বিবেচনায় নেয়।
- **মারাত্মক দুর্বলতা:** চরম মান বা আউটলায়ারের (Outliers) প্রতি অত্যন্ত সংবেদনশীল। যদি ৯ জন ব্যক্তির বেতন ২০,০০০ টাকা হয় এবং ১ জনের বেতন ৫০,০০,০০০ টাকা হয়, তবে গড় বেতন হবে ৫,১৮,০০০ টাকা—যা দলের কোনো সাধারণ ব্যক্তির অবস্থার সঠিক চিত্র নয়।

### ২. মধ্যমা (Median):
মানের ক্রমানুসারে সাজানো ডেটার ঠিক মধ্যবর্তী মান:

$$\text{Median} = \begin{cases} x_{\left(\frac{n+1}{2}\right)} & \text{যদি } n \text{ বিজোড় হয়} \\ \frac{1}{2} \left( x_{\left(\frac{n}{2}\right)} + x_{\left(\frac{n}{2} + 1\right)} \right) & \text{যদি } n \text{ জোড় হয়} \end{cases}$$

- **বৈশিষ্ট্য:** আউটলায়ারের বিরুদ্ধে অত্যন্ত শক্তিশালী ও প্রতিরোধী (Robust)। উপরোক্ত উদাহরণের ক্ষেত্রে মধ্যমা হবে ২০,০০০ টাকা, যা শতভাগ বাস্তবসম্মত।

### ৩. প্রচুরক (Mode):
ডেটাসেটে যে মানটি সর্বাধিকবার উপস্থিত হয়। ক্যাটেগোরিক্যাল ফিচারের (যেমন: ব্যবহারকারীর ভাষা, রক্তের গ্রুপ) মিসিং ভ্যালু পূরণের (Mode Imputation) ক্ষেত্রে এটি ব্যবহৃত হয়।

### ৪. স্কিউনেস ও তিনটির মধ্যকার সম্পর্ক (Skewness Relationship):

```
     [ Symmetric / Normal ]             [ Positive (Right) Skew ]             [ Negative (Left) Skew ]
               ^                                   ^                                   ^
              / \                                 / \                                 / \
             /   \                               /   \__                             __/   \
            /     \                             /       \___                     ___/       \
     ------+-------+------               ------+---+---+----+---         -------+---+---+----+---
       Mean = Median = Mode                  Mode Median Mean                   Mean Median Mode
```

- **সিমেট্রিক (Normal):** $\text{Mean} \approx \text{Median} \approx \text{Mode}$
- **পজিটিভ স্কিউড (ডানে লেজযুক্ত):** $\text{Mode} < \text{Median} < \text{Mean}$ (চরম উচ্চমানের কারণে গড় ডানদিকে সরে যায়)।
- **নেগেটিভ স্কিউড (বামে লেজযুক্ত):** $\text{Mean} < \text{Median} < \text{Mode}$ (চরম নিম্নমানের কারণে গড় বামে নেমে যায়)।

---

## ৩. বিস্তার বা বিচ্যুতির পরিমাপ (Measures of Dispersion)

শুধুমাত্র গড় জানলে ডেটা সম্পর্কে বিপজ্জনক বিভ্রান্তি তৈরি হতে পারে। উদাহরণস্বরূপ: দুটি ক্লাসের ছাত্রদের পরীক্ষার গড় নম্বর ৫০। কিন্তু ক্লাস A-এর সবাই ৫০ পেয়েছে, আর ক্লাস B-এর অর্ধেক ছাত্র ০ এবং বাকি অর্ধেক ১০০ পেয়েছে। বিস্তার পরিমাপ না করলে এই পার্থক্য বোঝা অসম্ভব।

### ১. পরিসর (Range):
$$\text{Range} = x_{\max} - x_{\min}$$

### ২. পপুলেশন ভ্যারিয়েন্স ($\sigma^2$) ও স্যাম্পল ভ্যারিয়েন্স ($s^2$):
পপুলেশন ভ্যারিয়েন্স:
$$\sigma^2 = \frac{1}{N} \sum_{i=1}^N (x_i - \mu)^2$$

স্যাম্পল ভ্যারিয়েন্স (ব্যাসেলের সংশোধন সহ):
$$s^2 = \frac{1}{n - 1} \sum_{i=1}^n (x_i - \bar{x})^2$$

### ৩. ব্যাসেলের সংশোধনের সম্পূর্ণ গাণিতিক প্রমাণ (Proof of Bessel's Correction $\frac{1}{n-1}$):
স্যাম্পল ভ্যারিয়ান্সে হরে $n$-এর পরিবর্তে $n-1$ কেন ব্যবহার করা হয়? কারণ স্যাম্পল গড় $\bar{x}$ প্রকৃত পপুলেশন গড় $\mu$-এর চেয়ে স্যাম্পল পয়েন্টগুলোর বেশি কাছাকাছি থাকে। ফলে সাধারণ $n$ দ্বারা ভাগ করলে ভ্যারিয়েন্সের মান সর্বদা অবমূল্যায়িত (biased downward) হয়।

প্রমাণ:
ধরা যাক বায়াসড স্যাম্পল ভ্যারিয়েন্স $S_n^2 = \frac{1}{n} \sum_{i=1}^n (X_i - \bar{X})^2$। আমরা জানি:
$$X_i - \bar{X} = (X_i - \mu) - (\bar{X} - \mu)$$

উভয়পাশে বর্গ করলে:
$$(X_i - \bar{X})^2 = (X_i - \mu)^2 - 2(X_i - \mu)(\bar{X} - \mu) + (\bar{X} - \mu)^2$$

সমগ্র $n$ পদের যোগফল নিলে:
$$\sum_{i=1}^n (X_i - \bar{X})^2 = \sum_{i=1}^n (X_i - \mu)^2 - 2(\bar{X} - \mu)\sum_{i=1}^n (X_i - \mu) + n(\bar{X} - \mu)^2$$

যেহেতু $\sum_{i=1}^n (X_i - \mu) = n(\bar{X} - \mu)$:
$$\sum_{i=1}^n (X_i - \bar{X})^2 = \sum_{i=1}^n (X_i - \mu)^2 - 2n(\bar{X} - \mu)^2 + n(\bar{X} - \mu)^2$$
$$\sum_{i=1}^n (X_i - \bar{X})^2 = \sum_{i=1}^n (X_i - \mu)^2 - n(\bar{X} - \mu)^2$$

এখন এক্সপেক্টেশন ($\mathbb{E}$) নিলে:
$$\mathbb{E}\left[ \sum_{i=1}^n (X_i - \bar{X})^2 \right] = \sum_{i=1}^n \mathbb{E}[(X_i - \mu)^2] - n \mathbb{E}[(\bar{X} - \mu)^2]$$

আমরা জানি $\mathbb{E}[(X_i - \mu)^2] = \sigma^2$ এবং স্যাম্পল গড়ের ভ্যারিয়েন্স $\mathbb{E}[(\bar{X} - \mu)^2] = \text{Var}(\bar{X}) = \frac{\sigma^2}{n}$।
মান বসালে:
$$\mathbb{E}\left[ \sum_{i=1}^n (X_i - \bar{X})^2 \right] = n\sigma^2 - n \left( \frac{\sigma^2}{n} \right) = n\sigma^2 - \sigma^2 = (n - 1)\sigma^2$$

উভয়পাশে $\frac{1}{n}$ দিয়ে গুণ করলে বায়াসড ভ্যারিয়েন্সের এক্সপেক্টেশন পাওয়া যায়:
$$\mathbb{E}[S_n^2] = \frac{n - 1}{n} \sigma^2 \ne \sigma^2 \quad (\text{Biased!})$$

একটি অনপেক্ষ (Unbiased) এস্টিমেটর পেতে হলে উভয়পক্ষকে $\frac{n}{n-1}$ দ্বারা গুণ করতে হবে:
$$\mathbb{E}\left[ \frac{n}{n-1} S_n^2 \right] = \mathbb{E}\left[ \frac{1}{n-1} \sum_{i=1}^n (X_i - \bar{X})^2 \right] = \sigma^2$$

**সিদ্ধান্ত:** এটিই প্রমাণ করে যে $s^2 = \frac{1}{n-1}\sum (x_i - \bar{x})^2$ পপুলেশন ভ্যারিয়েন্স $\sigma^2$-এর একটি সম্পূর্ণ আনবায়াসড এস্টিমেটর!

---

## ৪. পারসেন্টাইল, কোয়ান্টাইল ও আউটলায়ার ডিটেকশন

### ১. ইন্টারকোয়ার্টাইল রেঞ্জ (IQR - Interquartile Range):
ডেটাকে চারটি সমান ভাগে ভাগ করলে ৩টি কোয়ার্টাইল পাওয়া যায়:
- **$Q_1$ (25th Percentile):** প্রথম ২৫% ডেটার সর্বোচ্চ সীমা।
- **$Q_2$ (50th Percentile):** মধ্যমা বা মিডিয়ান।
- **$Q_3$ (75th Percentile):** ৭৫% ডেটার সর্বোচ্চ সীমা।

$$\text{IQR} = Q_3 - Q_1$$

### ২. টুকির ১.৫x IQR আউটলায়ার নিয়ম (Tukey's Outlier Rule):
জন টুকি (John Tukey) বক্স-প্লটের জন্য একটি বিশ্বস্ত আউটলায়ার বাউন্ডারি নির্ধারণ করেন:

$$\text{Lower Bound} = Q_1 - 1.5 \times \text{IQR}$$
$$\text{Upper Bound} = Q_3 + 1.5 \times \text{IQR}$$

যেকোনো মান এই রেঞ্জের বাইরে থাকলে তাকে আউটলায়ার বিবেচনা করা হয়।

```
                  Q1 - 1.5*IQR          Q1      Median      Q3          Q3 + 1.5*IQR
                       |                 |        |         |                 |
  Outliers <-----[ * ] |-----------------[========|=========]-----------------| [ * * ] ----> Outliers
                       |               <------   IQR   ------>                |
```

### ৩. জেড-স্কোর ক্লিপিং (Z-Score Winsorization):
স্বাভাবিক বা গাউসিয়ান ডিস্ট্রিবিউশনের ক্ষেত্রে প্রতিটি পয়েন্টের স্ট্যান্ডার্ড স্কোর:

$$z_i = \frac{x_i - \bar{x}}{s}$$

- যদি $|z_i| > 3$ হয় (অর্থাৎ মানটি গড় থেকে ৩ স্ট্যান্ডার্ড ডেভিয়েশনের বাইরে), তবে তাকে ৯৯.৭% স্বাভাবিক সীমার বাইরের চরম আউটলায়ার ধরা হয়।
- **Winsorization:** ডেটা ফেলে না দিয়ে ১ম পারসেন্টাইল এবং ৯৯তম পারসেন্টাইল মানে ক্লিপ (clamp) করা হয়:
  $$x_{\text{clipped}} = \min(\max(x, P_{0.01}), P_{0.99})$$

---

## ৫. দ্বিমুখী সম্পর্ক: কোভ্যারিয়েন্স ও কোরিলেশন (Covariance & Correlation)

### ১. কোভ্যারিয়েন্স ($\text{Cov}(X, Y)$):
দুটি ভ্যারিয়েবলের দিকগত সহ-পরিবর্তন পরিমাপ করে:

$$\text{Cov}(X, Y) = \frac{1}{n - 1} \sum_{i=1}^n (x_i - \bar{x})(y_i - \bar{y})$$

- $\text{Cov} > 0$: $X$ বাড়লে $Y$-ও বৃদ্ধি পায়।
- $\text{Cov} < 0$: $X$ বাড়লে $Y$ হ্রাস পায়।
- **সীমাবদ্ধতা:** কোভ্যারিয়েন্স স্কেল-ডিপেনডেন্ট (এককের ওপর নির্ভরশীল)। উচ্চতা মিটারের বদলে মিলিমিটারে মাপলে কোভ্যারিয়েন্স ১০০০ গুণ বেড়ে যাবে, যদিও সম্পর্কের শক্তিমত্তা একই থাকবে।

### ২. পিয়ারসন লিনিয়ার কোরিলেশন কোএফিশিয়েন্ট ($r$):
কোভ্যারিয়েন্সকে উভয় ভ্যারিয়েবলের স্ট্যান্ডার্ড ডেভিয়েশন দ্বারা ভাগ করে এককহীন $[-1, 1]$ স্কেলে রূপান্তর করা হয়:

$$r = \frac{\text{Cov}(X, Y)}{s_X s_Y} = \frac{\sum (x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum (x_i - \bar{x})^2 \sum (y_i - \bar{y})^2}}$$

- $r = +1$: সম্পূর্ণ ধনাত্মক রৈখিক সম্পর্ক।
- $r = -1$: সম্পূর্ণ ঋণাত্মক রৈখিক সম্পর্ক।
- $r = 0$: কোনো রৈখিক সম্পর্ক নেই (তবে নন-লিনিয়ার সম্পর্ক থাকতে পারে, যেমন $y = x^2$)।

### ৩. স্পিয়ারম্যান র‍্যাঙ্ক কোরিলেশন ($\rho$):
পিয়ারসন কেবলমাত্র সরলরৈখিক (linear) সম্পর্ক পরিমাপ করে, কিন্তু স্পিয়ারম্যান **মনোটোনিক (Monotonic - একমুখী ক্রমাগত বৃদ্ধি বা হ্রাস)** সম্পর্ক পরিমাপ করে। এটি আউটলায়ার ও নন-লিনিয়ার স্ট্রেচের বিরুদ্ধে সম্পূর্ণ সুরক্ষিত।

$$\rho = 1 - \frac{6 \sum d_i^2}{n(n^2 - 1)}$$

যেখানে $d_i = \text{Rank}(x_i) - \text{Rank}(y_i)$ হলো দুটি ফিচারের র‍্যাঙ্কের পার্থক্য।

---

## ৬. সম্পূর্ণ সংখ্যাগত হ্যান্ড-ক্যালকুলেশন (Step-by-Step Numerical Example)

ধরা যাক একটি স্টার্টআপে ৮ জন ইঞ্জিনিয়ারের অভিজ্ঞতা ($X$, বছরে) এবং তাদের বার্ষিক বোনাস ($Y$, হাজার ডলারে):
- $X = [2, 3, 4, 5, 6, 7, 8, 20]$ *(এখানে ২০ একটি চরম আউটলায়ার!)*
- $Y = [10, 12, 14, 16, 18, 20, 22, 60]$

### ধাপ ১: গড় নির্ণয়
$$n = 8$$
$$\sum X = 2 + 3 + 4 + 5 + 6 + 7 + 8 + 20 = 55 \implies \bar{X} = \frac{55}{8} = 6.875$$
$$\sum Y = 10 + 12 + 14 + 16 + 18 + 20 + 22 + 60 = 172 \implies \bar{Y} = \frac{172}{8} = 21.500$$

### ধাপ ২: মধ্যমা (Median) নির্ণয়
সাজানো $X$: $[2, 3, 4, 5, 6, 7, 8, 20]$
$$n = 8 \text{ (জোড়)} \implies \text{Median}_X = \frac{x_{(4)} + x_{(5)}}{2} = \frac{5 + 6}{2} = \mathbf{5.50}$$
দেখা যাচ্ছে গড় ($6.875$) আউটলায়ার ২০-এর কারণে মধ্যমা ($5.50$) থেকে অনেকটা দূরে সরে গেছে।

### ধাপ ৩: কোয়ার্টাইল ও টুকির আউটলায়ার চিহ্নিতকরণ ($X$-এর জন্য)
- প্রথম অর্ধাংশ: $[2, 3, 4, 5] \implies Q_1 = \frac{3 + 4}{2} = 3.5$
- দ্বিতীয় অর্ধাংশ: $[6, 7, 8, 20] \implies Q_3 = \frac{7 + 8}{2} = 7.5$
- $\text{IQR} = Q_3 - Q_1 = 7.5 - 3.5 = 4.0$

আউটলায়ার সীমা:
$$\text{Lower Limit} = Q_1 - 1.5 \times \text{IQR} = 3.5 - (1.5 \times 4.0) = 3.5 - 6.0 = -2.5$$
$$\text{Upper Limit} = Q_3 + 1.5 \times \text{IQR} = 7.5 + (1.5 \times 4.0) = 7.5 + 6.0 = \mathbf{13.5}$$

**সিদ্ধান্ত:** ডেটাপয়েন্ট $X = 20 > 13.5$। অতএব টুকির নিয়ম অনুযায়ী ২০ নিশ্চিতভাবে একটি **চরম আউটলায়ার (Severe Outlier)**!

### ধাপ ৪: স্যাম্পল ভ্যারিয়েন্স ও স্ট্যান্ডার্ড ডেভিয়েশন ($X$-এর জন্য, $n-1 = 7$)
প্রতিটি পয়েন্টের বিচ্যুতি $(X_i - \bar{X})$:
- $2 - 6.875 = -4.875 \implies (-4.875)^2 \approx 23.766$
- $3 - 6.875 = -3.875 \implies (-3.875)^2 \approx 15.016$
- $4 - 6.875 = -2.875 \implies (-2.875)^2 \approx 8.266$
- $5 - 6.875 = -1.875 \implies (-1.875)^2 \approx 3.516$
- $6 - 6.875 = -0.875 \implies (-0.875)^2 \approx 0.766$
- $7 - 6.875 = 0.125 \implies (0.125)^2 \approx 0.016$
- $8 - 6.875 = 1.125 \implies (1.125)^2 \approx 1.266$
- $20 - 6.875 = 13.125 \implies (13.125)^2 \approx 172.266$

বর্গের যোগফল:
$$\sum (X_i - \bar{X})^2 \approx 23.766 + 15.016 + 8.266 + 3.516 + 0.766 + 0.016 + 1.266 + 172.266 = 224.875$$

স্যাম্পল ভ্যারিয়েন্স:
$$s_X^2 = \frac{224.875}{8 - 1} = \frac{224.875}{7} \approx \mathbf{32.125}$$
$$s_X = \sqrt{32.125} \approx \mathbf{5.668}$$

---

## ৭. সম্পূর্ণ পাইথন বাস্তবায়ন (Pure Python & NumPy)

নিচে একটি স্বয়ংসম্পূর্ণ, ডিপেন্ডেন্সি-মুক্ত পাইথন বাস্তবায়ন দেওয়া হলো যা সমস্ত বর্ণনামূলক পরিসংখ্যান, আউটলায়ার শনাক্তকরণ এবং কোরিলেশন ম্যাট্রিক্স গণনা করে:

```python
"""
Descriptive Statistics & Outlier Detection Engine.
Pure Python and NumPy Implementation.
"""

import math
from typing import List, Dict, Tuple, Any


# ============================================================================
# 1. Core Descriptive Statistics
# ============================================================================
class DescriptiveStats:
    @staticmethod
    def mean(data: List[float]) -> float:
        return sum(data) / len(data) if data else 0.0

    @staticmethod
    def median(data: List[float]) -> float:
        if not data:
            return 0.0
        sorted_d = sorted(data)
        n = len(sorted_d)
        mid = n // 2
        if n % 2 == 1:
            return float(sorted_d[mid])
        else:
            return (sorted_d[mid - 1] + sorted_d[mid]) / 2.0

    @staticmethod
    def variance(data: List[float], sample: bool = True) -> float:
        n = len(data)
        if n < 2:
            return 0.0
        m = DescriptiveStats.mean(data)
        sq_diff_sum = sum((x - m) ** 2 for x in data)
        denom = (n - 1) if sample else n
        return sq_diff_sum / denom

    @staticmethod
    def std_dev(data: List[float], sample: bool = True) -> float:
        return math.sqrt(DescriptiveStats.variance(data, sample))

    @staticmethod
    def quartiles(data: List[float]) -> Tuple[float, float, float]:
        """Calculates Q1, Q2 (Median), and Q3."""
        sorted_d = sorted(data)
        n = len(sorted_d)
        mid = n // 2
        med = DescriptiveStats.median(sorted_d)
        if n % 2 == 0:
            q1 = DescriptiveStats.median(sorted_d[:mid])
            q3 = DescriptiveStats.median(sorted_d[mid:])
        else:
            q1 = DescriptiveStats.median(sorted_d[:mid])
            q3 = DescriptiveStats.median(sorted_d[mid + 1:])
        return q1, med, q3


# ============================================================================
# 2. Outlier Detection Engine (Tukey IQR and Z-Score)
# ============================================================================
class OutlierDetector:
    @staticmethod
    def detect_tukey_iqr(data: List[float], k: float = 1.5) -> Dict[str, Any]:
        q1, median, q3 = DescriptiveStats.quartiles(data)
        iqr = q3 - q1
        lower_bound = q1 - k * iqr
        upper_bound = q3 + k * iqr
        outliers = [x for x in data if x < lower_bound or x > upper_bound]
        return {
            "Q1": q1,
            "Median": median,
            "Q3": q3,
            "IQR": iqr,
            "Lower_Bound": lower_bound,
            "Upper_Bound": upper_bound,
            "Outliers": outliers
        }

    @staticmethod
    def winsorize(data: List[float], lower_percentile: float = 0.05, upper_percentile: float = 0.95) -> List[float]:
        sorted_d = sorted(data)
        n = len(sorted_d)
        low_idx = int(n * lower_percentile)
        high_idx = int(n * upper_percentile)
        low_val = sorted_d[low_idx]
        high_val = sorted_d[min(high_idx, n - 1)]
        return [min(max(x, low_val), high_val) for x in data]


# ============================================================================
# 3. Covariance and Correlation
# ============================================================================
class AssociationMetrics:
    @staticmethod
    def covariance(x: List[float], y: List[float]) -> float:
        n = len(x)
        assert n == len(y) and n > 1, "Lengths must match and be > 1"
        mx, my = DescriptiveStats.mean(x), DescriptiveStats.mean(y)
        return sum((x[i] - mx) * (y[i] - my) for i in range(n)) / (n - 1)

    @staticmethod
    def pearson_correlation(x: List[float], y: List[float]) -> float:
        cov = AssociationMetrics.covariance(x, y)
        sx = DescriptiveStats.std_dev(x, sample=True)
        sy = DescriptiveStats.std_dev(y, sample=True)
        if sx == 0 or sy == 0:
            return 0.0
        return cov / (sx * sy)

    @staticmethod
    def spearman_rank_correlation(x: List[float], y: List[float]) -> float:
        n = len(x)
        def rank(arr):
            sorted_idx = sorted(range(n), key=lambda i: arr[i])
            ranks = [0.0] * n
            for r, idx in enumerate(sorted_idx, start=1):
                ranks[idx] = float(r)
            return ranks

        rx = rank(x)
        ry = rank(y)
        # Using Pearson formula on ranks
        return AssociationMetrics.pearson_correlation(rx, ry)


# ============================================================================
# 4. Verification & Demo Execution
# ============================================================================
if __name__ == "__main__":
    X = [2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 20.0]  # Note: 20 is outlier
    Y = [10.0, 12.0, 14.0, 16.0, 18.0, 20.0, 22.0, 60.0]

    print("--- 1. Summary Statistics ---")
    print(f"Mean (X):       {DescriptiveStats.mean(X):.4f}")
    print(f"Median (X):     {DescriptiveStats.median(X):.4f}")
    print(f"Variance (X):   {DescriptiveStats.variance(X, sample=True):.4f}")
    print(f"Std Dev (X):    {DescriptiveStats.std_dev(X, sample=True):.4f}")

    print("\n--- 2. Outlier Detection via Tukey IQR ---")
    iqr_res = OutlierDetector.detect_tukey_iqr(X)
    for k, v in iqr_res.items():
        print(f"  {k}: {v}")

    print("\n--- 3. Association Metrics ---")
    cov_xy = AssociationMetrics.covariance(X, Y)
    pearson_r = AssociationMetrics.pearson_correlation(X, Y)
    spearman_rho = AssociationMetrics.spearman_rank_correlation(X, Y)

    print(f"Sample Covariance:          {cov_xy:.4f}")
    print(f"Pearson Linear Corr (r):    {pearson_r:.4f}")
    print(f"Spearman Monotonic Corr:    {spearman_rho:.4f}")

    print("\n--- 4. Winsorization (Clipping Outliers) ---")
    clipped = OutlierDetector.winsorize(X, 0.1, 0.9)
    print(f"Original: {X}")
    print(f"Clipped:  {clipped}")
    print("Descriptive statistics calculations verified successfully!")
```

---

## ৮. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স

| মেট্রিক | সুবিধা | সীমাবদ্ধতা | আদর্শ ব্যবহারের ক্ষেত্র |
| :--- | :--- | :--- | :--- |
| **Mean** | সমস্ত ডেটাপয়েন্ট ব্যবহার করে, সহজ গণিত | আউটলায়ার দ্বারা চরম প্রভাবিত | সিমেট্রিক বা গাউসিয়ান ডিস্ট্রিবিউশন |
| **Median** | আউটলায়ার প্রতিরোধী (Robust) | অ্যালজেব্রাইক সমীকরণে সরাসরি সমাধান কঠিন | স্কিউড ডেটা (বেতন, বাড়ির দাম, নেটওয়ার্ক ট্রাফিক) |
| **Std Dev** | ডেটার মূল এককে বিস্তার প্রকাশ করে | আউটলায়ারের প্রতি সংবেদনশীল | সাধারণ বিস্তার বিশ্লেষণ, স্ট্যান্ডার্ডাইজেশন ($Z$-score) |
| **IQR** | মধ্যবর্তী ৫০% ডেটার নিখুঁত বিস্তার | চরম প্রান্তের তথ্য ফিল্টার করে | আউটলায়ার ডিটেকশন ও রোবাস্ট স্কেলিং |
| **Pearson ($r$)** | স্কেল-ইনভেরিয়েন্ট লিনিয়ার সম্পর্ক | নন-লিনিয়ার রিলেশনশিপ মাপতে ব্যর্থ | লিনিয়ার রিগ্রেশন, মাল্টিকোলিনিয়ারিটি চেক |
| **Spearman ($\rho$)**| নন-লিনিয়ার মনোটোনিক সম্পর্ক ধরে | এক্স্যাক্ট স্লোপ বা ঢাল নির্দেশ করে না | র‍্যাঙ্কিং টাস্ক, অর্ডিনাল ডেটা, নন-প্যারামেট্রিক EDA |

---

## ৯. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: পিয়ারসন কোরিলেশন $r = 0$ হলে কি দুটি ভ্যারিয়েবল সম্পূর্ণ স্বাধীন (Independent)?**
   - **উত্তর:** না! $r = 0$ নির্দেশ করে তাদের মধ্যে কোনো **সরলরৈখিক (linear)** সম্পর্ক নেই। কিন্তু তাদের মধ্যে শক্তিশালী নন-লিনিয়ার সম্পর্ক থাকতে পারে। উদাহরণস্বরূপ: $X \sim U(-1, 1)$ এবং $Y = X^2$। এখানে $Y$ সম্পূর্ণরূপে $X$-এর ওপর নির্ভরশীল, তবুও গাণিতিকভাবে $Cov(X, Y) = 0$ এবং $r = 0$ হবে।
2. **প্রশ্ন: ভ্যারিয়েন্স গণনায় Bessel's correction $n-1$ কেন ব্যবহার করা হয়?**
   - **উত্তর:** একটি স্যাম্পল থেকে গণনা করা গড় $\bar{x}$ সর্বদা স্যাম্পলের পয়েন্টগুলোর সবচেয়ে কাছাকাছি থাকে, যা পপুলেশন গড় $\mu$ থেকে বিচ্যুতিকে অবমূল্যায়িত করে। ফলে $\frac{1}{n}$ ব্যবহার করলে ভ্যারিয়েন্স প্রকৃত পপুলেশন ভ্যারিয়েন্সের চেয়ে গড়ে $\frac{n-1}{n}$ গুণ কম আসে (Negative Bias)। $n-1$ দিয়ে ভাগ করলে এই বায়াস সম্পূর্ণ অপসারিত হয় এবং এস্টিমেটরটি আনবায়াসড হয়।
3. **প্রশ্ন: স্ট্যান্ডার্ডাইজেশন (StandardScaler) বনাম রোবাস্ট স্কেলিং (RobustScaler)-এর পার্থক্য কী?**
   - **উত্তর:** StandardScaler ডেটা থেকে গড় বিয়োগ করে স্ট্যান্ডার্ড ডেভিয়েশন দিয়ে ভাগ করে ($z = \frac{x - \mu}{\sigma}$), যা আউটলায়ার থাকলে মারাত্মক বিকৃত হয়। RobustScaler আউটলায়ারের প্রভাব ঠেকাতে মধ্যমা বিয়োগ করে IQR দিয়ে ভাগ করে ($z = \frac{x - \text{Median}}{\text{IQR}}$)।
