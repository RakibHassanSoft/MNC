# অধ্যায় ২: ডেটা প্রিপ্রসেসিং এবং ফিচার ইঞ্জিনিয়ারিং (Data Preprocessing & Feature Engineering Masterclass)

> *"Feature engineering is the art of turning raw data into the fuel that powers machine learning models. Coming up with features is difficult, time-consuming, and requires domain knowledge. 'Applied machine learning' is basically feature engineering." — Andrew Ng*
> 
> বাস্তব জগতের ডেটা সাধারণত অসম্পূর্ণ (Incomplete), কোলাহলপূর্ণ (Noisy), অসংলগ্ন (Inconsistent) এবং চরম ভারসাম্যহীন (Imbalanced) থাকে। এই অধ্যায়ে কাঁচা ডেটাকে মেশিন লার্নিং অ্যালগরিদমের উপযুক্ত গাণিতিক ম্যাট্রিক্সে রূপান্তর করার প্রতিটি পদ্ধতি, ডামি ভ্যারিয়েবল ট্র্যাপ, আধুনিক টার্গেট স্মুথিং, WoE ও IV, সাইক্লিক এনকোডিং, পাওয়ার ট্রান্সফর্মেশন, TF-IDF, SMOTE, মিউচুয়াল ইনফরমেশন, পারমিউটেশন ইম্পর্ট্যান্স এবং SHAP-এর **হাতে-কলমে সংখ্যাভিত্তিক প্রতিটি ধাপের গণনা (Step-by-Step Numerical Calculations)** বিস্তারিতভাবে সন্নিবেশিত হয়েছে।

---

## 📑 সম্পূর্ণ বিষয়সূচি (Table of Contents)
1. [মিসিং ডেটা মেকানিজম ও ইম্পিউটেশন কৌশল (MCAR, MAR, MNAR, KNN ও MICE বাস্তব হিসাব)](#১-মিসিং-ডেটা-মেকানিজম-ও-ইম্পিউটেশন-কৌশল)
2. [আউটলায়ার সনাক্তকরণ, উইনসোরাইজেশন ও রূপান্তর (IQR ও Z-Score সংখ্যাসহ গণনা)](#২-আউটলায়ার-সনাক্তকরণ-উইনসোরাইজেশন-ও-রূপান্তর)
3. [ফিচার স্কেলিং ও পাওয়ার ট্রান্সফর্মেশন (Standard, MinMax, Robust, Box-Cox, Yeo-Johnson)](#৩-ফিচার-স্কেলিং-ও-পাওয়ার-ট্রান্সফর্মেশন)
4. [ক্যাটেগরিক্যাল এনকোডিং ও ডামি ভ্যারিয়েবল ট্র্যাপ (One-Hot, Target Smoothing, WoE ও IV)](#৪-ক্যাটেগরিক্যাল-এনকোডিং-ও-ডামি-ভ্যারিয়েবল-ট্র্যাপ)
5. [তারিখ, সময় ও সাইক্লিক ফিচার ইঞ্জিনিয়ারিং (Sine/Cosine রূপান্তরের দূরত্ব প্রমাণ)](#৫-তারিখ-সময়-ও-সাইক্লিক-ফিচার-ইঞ্জিনিয়ারিং)
6. [ইন্টারেকশন ফিচার, পলিনোমিয়াল ও টেক্সট TF-IDF (৩টি ডকুমেন্টের পূর্ণাঙ্গ গণনা)](#৬-ইন্টারেকশন-ফিচার-পলিনোমিয়াল-ও-টেক্সট-tf-idf)
7. [ক্লাস ইমব্যালান্স ম্যানেজমেন্ট (SMOTE সিন্থেটিক ইন্টারপোলেশন ও ক্লাস ওয়েট হিসাব)](#৭-ক্লাস-ইমব্যালান্স-ম্যানেজমেন্ট)
8. [ফিচার সিলেকশন টেকনিকস ও SHAP এক্সপ্লেইনেবিলিটি (Mutual Info, RFE, শ্যাপলি ভ্যালু)](#৮-ফিচার-সিলেকশন-টেকনিকস-ও-shap-এক্সপ্লেইনেবিলিটি)
9. [ডেটা লিকেজ প্রতিরোধ ও প্রোডাকশন পাইপলাইন (Pipeline & ColumnTransformer)](#৯-ডেটা-লিকেজ-প্রতিরোধ-ও-প্রোডাকশন-পাইপলাইন)
10. [স্ক্র্যাচ পাইথন ও Scikit-Learn প্রোডাকশন কোড](#১০-স্ক্র্যাচ-পাইথন-ও-scikit-learn-প্রোডাকশন-কোড)

---

## ১. মিসিং ডেটা মেকানিজম ও ইম্পিউটেশন কৌশল

### ১.১ মিসিং ডেটার প্রকারভেদ
1. **MCAR (Missing Completely at Random):** মিসিং হওয়া কোনো ভ্যারিয়েবলের ওপর নির্ভর করে না (যেমন: ল্যাব টেস্টের টিউব হঠাৎ হাত থেকে পড়ে ভেঙে ডেটা নষ্ট হওয়া)।
2. **MAR (Missing at Random):** মিসিং হওয়ার কারণ অন্য কোনো পর্যবেক্ষণযোগ্য ফিচারের ওপর নির্ভরশীল (যেমন: নারীরা পুরুষদের তুলনায় আয়ের তথ্য প্রকাশে অনীহা দেখায়)।
3. **MNAR (Missing Not at Random):** যে মানটি মিসিং, তার নিজস্ব তীব্রতার কারণেই তা গোপন করা হয়েছে (যেমন: অতি উচ্চ আয়ের মানুষ সচেতনভাবে ট্যাক্স সার্ভে এড়ানো)।

---

### ১.২ KNN ইম্পিউটার দূরত্ব সমীকরণ (Nan-Euclidean Distance)
$$\text{dist}(\mathbf{x}, \mathbf{y}) = \sqrt{\frac{p}{p_{\text{present}}} \sum_{i \in \text{both}} (x_i - y_i)^2}$$
যেখানে $p$ হলো মোট কলাম সংখ্যা এবং $p_{\text{present}}$ হলো যেসব কলামে উভয় পয়েন্টেই মান উপস্থিত আছে।

#### 🔢 হাতে-কলমে KNN ইম্পিউটেশন হিসাব:
ধরি ৩টি কলাম: [বয়স, আয়, ক্রেডিট স্কোর] ($p = 3$)
- ডেটা পয়েন্ট ১ (যার মান মিসিং): $\mathbf{x} = [30, \text{NaN}, 700]$
- ডেটা পয়েন্ট ২ (প্রতিবেশী A): $\mathbf{y}_A = [32, 60000, 710]$
- ডেটা পয়েন্ট ৩ (প্রতিবেশী B): $\mathbf{y}_B = [45, 90000, 650]$

1. **$\mathbf{x}$ ও $\mathbf{y}_A$-এর মধ্যকার দূরত্ব ($p_{\text{present}} = 2$):**
   $$\text{dist}(\mathbf{x}, \mathbf{y}_A) = \sqrt{\frac{3}{2} \left[ (30 - 32)^2 + (700 - 710)^2 \right]} = \sqrt{1.5 \times [4 + 100]} = \sqrt{156} \approx \mathbf{12.49}$$
2. **$\mathbf{x}$ ও $\mathbf{y}_B$-এর মধ্যকার দূরত্ব:**
   $$\text{dist}(\mathbf{x}, \mathbf{y}_B) = \sqrt{\frac{3}{2} \left[ (30 - 45)^2 + (700 - 650)^2 \right]} = \sqrt{1.5 \times [225 + 2500]} = \sqrt{4087.5} \approx \mathbf{63.93}$$
3. **ফলাফল:** প্রতিবেশী A সবচেয়ে কাছে হওয়ায় মিসিং আয় ফিল হবে: **৬০,০০০ টাকা**।

---

### ১.৩ MICE (Multivariate Imputation by Chained Equations)
MICE প্রতিটি মিসিং কলামকে পর্যায়ক্রমে টার্গেট ধরে বাকি কলামগুলোর ওপর রিগ্রেশন মডেল চালায়:
- ধাপ ১: সব মিসিং কলাম প্রথমে সাধারণ মিডিয়ান দিয়ে সাময়িক ফিল করা হয়।
- ধাপ ২: কলাম ১-এর আদি মিসিং মানগুলোকে ফাঁকা করে বাকি সব কলামের সাহায্যে রিগ্রেশন করে নতুন মান প্রেডিক্ট করা হয়।
- ধাপ ৩: একইভাবে কলাম ২, ৩-এর জন্য পুনরাবৃত্তি ঘটে। এই চক্র ১০-২০ বার ঘুরিয়ে কনভার্জেন্সে পৌঁছানো হয়।

---

## ২. আউটলায়ার সনাক্তকরণ, উইনসোরাইজেশন ও রূপান্তর

```
                         IQR (Interquartile Range)
              ┌─────────────────────┴─────────────────────┐
              │                                           │
  ────[───────┼───────────────[       │       ]───────────┼───────]────  ● (Outlier)
      │       │               │       │       │           │       │
  Lower Bound │              Q1     Median   Q3           │  Upper Bound
  (Q1 - 1.5*IQR)                                      (Q3 + 1.5*IQR)
```

#### 🔢 হাতে-কলমে IQR আউটলায়ার গণনা:
ধরি ৯টি ডেটা পয়েন্ট: $[10, 12, 14, 15, 17, 18, 20, 22, 100]$
1. মিডিয়ান ($Q_2$): $17$ (পঞ্চম উপাদান)
2. প্রথম কোয়ার্টাইল ($Q_1$): লোয়ার হাফের মিডিয়ান $= \frac{12 + 14}{2} = \mathbf{13}$
3. তৃতীয় কোয়ার্টাইল ($Q_3$): আপার হাফের মিডিয়ান $= \frac{20 + 22}{2} = \mathbf{21}$
4. $\text{IQR} = Q_3 - Q_1 = 21 - 13 = \mathbf{8}$
5. লোয়ার ফেন্স $= 13 - (1.5 \times 8) = 13 - 12 = \mathbf{1}$
6. আপার ফেন্স $= 21 + (1.5 \times 8) = 21 + 12 = \mathbf{33}$
7. **ফলাফল:** $100 > 33$, সুতরাং ১০০ একটি চরম আউটলায়ার!
8. **উইনসোরাইজেশন (Winsorization):** ডেটা পয়েন্ট ১০০-কে ড্রপ না করে সর্বোচ্চ সীমানায় আবদ্ধ করে দেওয়া $\to 33$।

---

## ৩. ফিচার স্কেলিং ও পাওয়ার ট্রান্সফর্মেশন

### ৩.১ স্কেলারের গাণিতিক তুলনা:

| স্কেলার | সমীকরণ | আউটপুট পরিসীমা | আউটলায়ার সহনশীলতা | ব্যবহারের ক্ষেত্র |
| :--- | :---: | :---: | :---: | :--- |
| **StandardScaler** | $z = \frac{x - \mu}{\sigma}$ | $\mu=0, \sigma=1$ | **কম** (আউটলায়ার $\mu, \sigma$ নষ্ট করে) | লিনিয়ার মডেল, লজিস্টিক, নিউরাল নেটওয়ার্ক, PCA |
| **MinMaxScaler** | $x' = \frac{x - x_{\min}}{x_{\max} - x_{\min}}$ | $[0, 1]$ | **চরম সংবেদনশীল** | ইমেজ প্রসেসিং (Pixel 0-255), বাউন্ডেড মডেল |
| **RobustScaler** | $x' = \frac{x - Q_2}{\text{IQR}}$ | মিডিয়ান ০ | **খুব উচ্চ (Best for Outliers)** | আউটলায়ারযুক্ত ফিনান্সিয়াল ডেটা |

---

### ৩.২ পাওয়ার ট্রান্সফর্মেশন (Non-linear Gaussian Converters):
1. **বক্স-কক্স (Box-Cox Transform):** কঠোরভাবে ধনাত্মক ডেটায় ($x > 0$):
   $$y^{(\lambda)} = \begin{cases} \frac{x^\lambda - 1}{\lambda}, & \text{if } \lambda \neq 0 \\ \ln(x), & \text{if } \lambda = 0 \end{cases}$$
2. **ইয়েও-জনসন (Yeo-Johnson Transform):** ঋণাত্মক বা শূন্য মানযুক্ত ডেটাতেও কাজ করে:
   $$y^{(\lambda)} = \begin{cases} \frac{(x + 1)^\lambda - 1}{\lambda}, & \text{if } \lambda \neq 0, x \ge 0 \\ \ln(x + 1), & \text{if } \lambda = 0, x \ge 0 \\ -\frac{(-x + 1)^{2 - \lambda} - 1}{2 - \lambda}, & \text{if } \lambda \neq 2, x < 0 \\ -\ln(-x + 1), & \text{if } \lambda = 2, x < 0 \end{cases}$$

---

## ৪. ক্যাটেগরিক্যাল এনকোডিং ও ডামি ভ্যারিয়েবল ট্র্যাপ

### ৪.১ ডামি ভ্যারিয়েবল ট্র্যাপ ও সমাধান:
যদি একটি ক্যাটাগরিতে ৩টি লেভেল থাকে (যেমন: Dhaka, Chittagong, Sylhet), তবে One-Hot Encoding-এ ৩টি কলাম তৈরি করলে $\text{Dhaka} + \text{Chittagong} + \text{Sylhet} = 1$ হয়ে নিখুঁত মাল্টিকোলিনিয়ারিটি তৈরি হয় ($X^T X$ ইনভার্ট করা যায় না)।
- **সমাধান:** সর্বদা `drop='first'` ব্যবহার করে প্রথম কলামটি বাদ দেওয়া (২টি কলাম যথেষ্ট)।

---

### ৪.২ টার্গেট এনকোডিংয়ে বেইজিয়ান স্মুথিং (Target Encoding with Smoothing)
হাই-কার্ডিনালিটি ক্যাটাগরিতে সরাসরি গড় নিলে ওভারফিটিং হয়। বেইজিয়ান স্মুথিং সমীকরণ:
$$\hat{S}_i = \frac{n_i \cdot \bar{y}_i + m \cdot \bar{y}_{\text{global}}}{n_i + m}$$
যেখানে $n_i$ = ক্যাটাগরির স্যাম্পল সংখ্যা, $\bar{y}_i$ = ক্যাটাগরির গড়, $\bar{y}_{\text{global}}$ = সমগ্র ডেটাসেটের গড়, $m$ = স্মুথিং ওয়েট।

#### 🔢 হাতে-কলমে টার্গেট এনকোডিং হিসাব:
ধরি সমগ্র ডেটাসেটের চার্ন রেট $\bar{y}_{\text{global}} = 0.05$ (৫%), এবং $m = 10$।
- একটি ছোট পোস্টকোড "1212" থেকে স্যাম্পল এসেছে মাত্র ২টি ($n_i = 2$), যার মধ্যে ১ জন চার্ন করেছে ($\bar{y}_i = 0.50$)।
- **অপরিণত টার্গেট এনকোডিং:** $0.50$ (মাত্র ২টি স্যাম্পলে ৫০% চার্ন রেট দেখানো চরম ওভারফিটিং!)
- **বেইজিয়ান স্মুথড মান:**
  $$\hat{S}_{1212} = \frac{(2 \times 0.50) + (10 \times 0.05)}{2 + 10} = \frac{1.0 + 0.5}{12} = \frac{1.5}{12} = \mathbf{0.125 \text{ (১২.৫\%)}}$$
- **ফলাফল:** স্যাম্পল কম থাকায় মানটি স্বয়ংক্রিয়ভাবে গ্লোবাল গড়ের দিকে সংকুচিত হয়ে মডেলকে ওভারফিটিং থেকে রক্ষা করেছে!

---

### ৪.৩ Weight of Evidence (WoE) ও Information Value (IV)
ক্রেডিট রিস্ক ও ব্যাংকিং মডেলে ক্যাটাগরিক্যাল ফিচারের প্রেডিক্টিভ ক্ষমতা পরিমাপে WoE ও IV আন্তর্জাতিক গোল্ড স্ট্যান্ডার্ড:
$$\text{WoE}_i = \ln\left( \frac{\% \text{ of Non-Events (Goods)}}{\% \text{ of Events (Bads)}} \right) = \ln\left( \frac{N_{0i} / N_0}{N_{1i} / N_1} \right)$$
$$\text{IV} = \sum_{i=1}^k \left( \frac{N_{0i}}{N_0} - \frac{N_{1i}}{N_1} \right) \times \text{WoE}_i$$
- **IV ইন্টারপ্রিটেশন:**
  - $\text{IV} < 0.02$: সম্পূর্ণ অকেজো প্রেডিক্টর।
  - $0.02 \le \text{IV} < 0.1$: দুর্বল প্রেডিক্টর।
  - $0.1 \le \text{IV} < 0.3$: মাঝারি শক্তিশালী।
  - $0.3 \le \text{IV} < 0.5$: অত্যন্ত শক্তিশালী প্রেডিক্টর।

---

## ৫. তারিখ, সময় ও সাইক্লিক ফিচার ইঞ্জিনিয়ারিং

ঘণ্টা $t \in [0, 23]$ কে সাধারণ সংখ্যায় রাখলে রাত ২৩:০০ এবং রাত ০১:০০-এর মধ্যকার দূরত্ব দাঁড়ায় ২২ ঘণ্টা! সাইক্লিক রূপান্তর:
$$x_{\sin} = \sin\left(\frac{2\pi \cdot t}{24}\right), \quad x_{\cos} = \cos\left(\frac{2\pi \cdot t}{24}\right)$$

#### 🔢 ইউক্লিডিয়ান দূরত্বের গাণিতিক প্রমাণ:
- রাত ২৩:০০ ($t=23$): $x_{\sin} \approx -0.2588, \quad x_{\cos} \approx +0.9659$
- রাত ০১:০০ ($t=1$): $x_{\sin} \approx +0.2588, \quad x_{\cos} \approx +0.9659$
- দূরত্ব:
  $$d = \sqrt{(-0.2588 - 0.2588)^2 + (0.9659 - 0.9659)^2} = \sqrt{(-0.5176)^2 + 0} = \mathbf{0.5176}$$
- মডেল এখন বুঝতে পারে যে রাত ১১:০০ এবং রাত ১:০০ সময়ের বৃত্তাকার চাকায় একে অপরের ঠিক গা ঘেঁষে অবস্থান করছে!

---

## ৬. ইন্টারেকশন ফিচার, পলিনোমিয়াল ও টেক্সট TF-IDF

### ৬.১ পলিনোমিয়াল ইন্টারেকশন মাত্রা:
$n$ টি ফিচারের ডিগ্রি $d$ পলিনোমিয়াল ফিচারের সংখ্যা:
$$N_{\text{poly}} = \binom{n + d}{d} = \frac{(n + d)!}{n! d!}$$
যেমন: ১০০টি ফিচারের জন্য ডিগ্রি ২ পলিনোমিয়ালে মোট ফিচার সংখ্যা হবে $\binom{102}{2} = \frac{102 \times 101}{2} = \mathbf{5,151}$ টি! তাই যথেচ্ছ পলিনোমিয়াল ব্যবহার মেমরি ধ্বংস করে।

---

### ৬.২ TF-IDF (Term Frequency - Inverse Document Frequency)
ধরি কর্পাসে ৩টি ছোট ডকুমেন্ট আছে ($|D| = 3$):
- $d_1$: "AI is transforming healthcare"
- $d_2$: "AI is transforming finance"
- $d_3$: "Doctors work in healthcare"

আমরা $d_1$-এ **"healthcare"** শব্দের TF-IDF বের করব:
1. **Term Frequency (TF):**
   $$\text{TF}(\text{"healthcare"}, d_1) = \frac{1}{4} = \mathbf{0.25}$$
2. **Inverse Document Frequency (IDF):**
   "healthcare" শব্দটি ৩টি ডকুমেন্টের মধ্যে ২টিতে আছে ($d_1$ ও $d_3$)।
   $$\text{IDF} = \ln\left( \frac{1 + |D|}{1 + \text{DF}} \right) + 1 = \ln\left( \frac{1 + 3}{1 + 2} \right) + 1 = \ln\left( \frac{4}{3} \right) + 1 = 0.2877 + 1 = \mathbf{1.2877}$$
3. **TF-IDF স্কোর:**
   $$\text{TF-IDF} = 0.25 \times 1.2877 = \mathbf{0.3219}$$

---

## ৭. ক্লাস ইমব্যালান্স ম্যানেজমেন্ট

### ৭.১ ক্লাস ওয়েট গণনা সমীকরণ (Balanced Class Weights):
$$w_j = \frac{N}{K \cdot n_j}$$
যেখানে $N$ = মোট স্যাম্পল ($10,000$), $K$ = ক্লাস সংখ্যা ($2$), $n_j$ = নির্দিষ্ট ক্লাসের স্যাম্পল।
- যদি $n_0 = 9,000$ এবং $n_1 = 1,000$ হয়:
  $$w_0 = \frac{10000}{2 \times 9000} = \mathbf{0.555}, \quad w_1 = \frac{10000}{2 \times 1000} = \mathbf{5.000}$$
- মডেল সংখ্যালঘু ক্লাসের ভুলের ওপর ৯ গুণ বেশি পেনাল্টি আরোপ করবে।

### ৭.২ SMOTE (Synthetic Minority Over-sampling Technique):
সংখ্যালঘু ক্লাসের দুটি প্রতিবেশী পয়েন্ট $\mathbf{x}_1 = [2.0, 4.0]$ এবং $\mathbf{x}_2 = [4.0, 8.0]$।
র‍্যান্ডম স্কেলার $\lambda = 0.40$ হলে নতুন সিন্থেটিক পয়েন্ট:
$$\mathbf{x}_{\text{syn}} = \mathbf{x}_1 + \lambda(\mathbf{x}_2 - \mathbf{x}_1) = \begin{bmatrix} 2.0 \\ 4.0 \end{bmatrix} + 0.40 \begin{bmatrix} 2.0 \\ 4.0 \end{bmatrix} = \begin{bmatrix} 2.0 + 0.8 \\ 4.0 + 1.6 \end{bmatrix} = \begin{bmatrix} \mathbf{2.8} \\ \mathbf{5.6} \end{bmatrix}$$

---

## ৮. ফিচার সিলেকশন ও SHAP এক্সপ্লেইনেবিলিটি

### ৮.১ মিউচুয়াল ইনফরমেশন (Mutual Information):
$$I(X; Y) = \sum_{x \in X} \sum_{y \in Y} p(x, y) \log \left( \frac{p(x, y)}{p(x)p(y)} \right)$$
- পিয়ারসন কোরিলেশন কেবল লিনিয়ার সম্পর্ক ধরে, কিন্তু মিউচুয়াল ইনফরমেশন যেকোনো জটিল নন-লিনিয়ার নির্ভরতা মাপতে পারে।

### ৮.২ Recursive Feature Elimination (RFE):
মডেলের প্রতিটি ইটারেশনে সবচেয়ে কম গুরুত্বপূর্ণ ফিচারটি ছাঁটাই করে পুনরায় ট্রেইন করা হয়।

### ৮.৩ শ্যাপলি ভ্যালু (Shapley Value) গাণিতিক বণ্টন:
বেসলাইন প্রেডিকশন = **$৩০০,০০০**। একটি নির্দিষ্ট বাড়ির প্রেডিকশন = **$৩৫০,০০০** (পার্থক্য +$৫০,০০০)।
ফিচার ২টি: $F = \{\text{Size}, \text{Location}\}$
- $f(\emptyset) = 300$, $f(\{\text{Size}\}) = 330$, $f(\{\text{Loc}\}) = 310$, $f(\{\text{Size, Loc}\}) = 350$
- **Size ফিচারের অবদান:**
  - $\emptyset$-তে Size যোগ হলে মার্জিনাল অবদান $= 330 - 300 = +30$ (ওজন $0.5$)
  - Location-এ Size যোগ হলে মার্জিনাল অবদান $= 350 - 310 = +40$ (ওজন $0.5$)
  $$\phi_{\text{Size}} = (0.5 \times 30) + (0.5 \times 40) = \mathbf{+\$35,000}$$
- **Location ফিচারের অবদান:** $\phi_{\text{Loc}} = 50 - 35 = \mathbf{+\$15,000}$
- **যোগফল যাচাই:** $300 + 35 + 15 = \mathbf{350}$ হাজার ডলার!

---

## ৯. ডেটা লিকেজ প্রতিরোধ ও প্রোডাকশন পাইপলাইন

```
                   Train/Test Split (Never fit before split!)
                                  │
           ┌──────────────────────┴──────────────────────┐
           ▼ Train Set                                   ▼ Test Set
┌──────────────────────────────┐              ┌──────────────────────────────┐
│ Pipeline.fit(X_train)        │              │ Pipeline.transform(X_test)   │
│ • Learn Median Imputer       │ ──Freeze──►  │ • Apply Frozen Median        │
│ • Learn Mean/Std Scaler      │              │ • Apply Frozen Scaler        │
│ • Train XGBoost Classifier   │              │ • Predict & Evaluate         │
└──────────────────────────────┘              └──────────────────────────────┘
```

---

## ১০. স্ক্র্যাচ পাইথন ও Scikit-Learn প্রোডাকশন কোড

```python
"""
production_feature_pipeline.py
Complete Leak-free Preprocessing & Feature Engineering Pipeline
"""
import numpy as np
import pandas as pd
from sklearn.base import BaseEstimator, TransformerMixin
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder

class CyclicalTimeEncoder(BaseEstimator, TransformerMixin):
    def __init__(self, col_name: str, cycle_max: float = 24.0):
        self.col_name = col_name
        self.cycle_max = cycle_max

    def fit(self, X, y=None):
        return self

    def transform(self, X):
        X_df = X.copy() if isinstance(X, pd.DataFrame) else pd.DataFrame(X)
        time_series = X_df[self.col_name]
        sin_feat = np.sin(2 * np.pi * time_series / self.cycle_max)
        cos_feat = np.cos(2 * np.pi * time_series / self.cycle_max)
        return pd.concat([X_df.drop(columns=[self.col_name]), sin_feat, cos_feat], axis=1)

if __name__ == "__main__":
    raw_df = pd.DataFrame({
        'income': [50000, np.nan, 75000, 120000],
        'age': [25, 45, 35, 52],
        'channel': ['Web', 'App', 'Web', 'Store']
    })
    
    num_cols = ['income', 'age']
    cat_cols = ['channel']

    preprocessor = ColumnTransformer(transformers=[
        ('num', Pipeline([('imp', SimpleImputer(strategy='median')), ('scl', StandardScaler())]), num_cols),
        ('cat', OneHotEncoder(drop='first', sparse_output=False), cat_cols)
    ])

    transformed_data = preprocessor.fit_transform(raw_df)
    print("Transformed Feature Matrix Shape:", transformed_data.shape)
    print("Preprocessed Output:\n", np.round(transformed_data, 3))
```
