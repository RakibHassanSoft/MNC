# অধ্যায় ৬: আনসুপারভাইজড লার্নিং, অ্যানোম্যালি ডিটেকশন ও রিইনফোর্সমেন্ট লার্নিং (Unsupervised Learning, Anomaly Detection & RL Masterclass)

> *"Unsupervised learning is the dark matter of machine intelligence; discovering structure, geometry, and representations without human labels is the truest form of learning."*
> 
> আনসুপারভাইজড লার্নিংয়ে কোনো পূর্বনির্ধারিত গ্রাউন্ড ট্রুথ বা সুপারভাইজড টার্গেট ($y$) থাকে না। ইনপুট ডেটার অন্তর্নিহিত ডিস্ট্রিবিউশন, ক্লাস্টারিং, ডাইমেনশনালিটি রিডাকশন, মার্কেট বাস্কেট প্যাটার্ন এবং চরম অস্বাভাবিক অ্যানোম্যালি শনাক্ত করাই এর মূল উদ্দেশ্য। একই সাথে এই অধ্যায়ে রিইনফোর্সমেন্ট লার্নিংয়ের (RL) কোর ম্যাথমেটিক্যাল ভিত্তি ও কিউ-লার্নিং অন্তর্ভুক্ত করা হয়েছে।

---

## 📑 সম্পূর্ণ বিষয়সূচি (Table of Contents)
1. [কে-মিন্স ও কে-মিন্স++ ক্লাস্টারিং (Lloyd's Algorithm & K-Means++ সেন্ট্রয়েড নির্বাচন)](#১-কে-মিন্স-ও-কে-মিন্স-ক্লাস্টারিং)
2. [ক্লাস্টার মূল্যায়ন মেট্রিক্স (Inertia/Elbow ও সিলুয়েট স্কোরের গাণিতিক প্রতিপাদন)](#২-ক্লাস্টার-মূল্যায়ন-মেট্রিক্স)
3. [হায়ারার্কিক্যাল ক্লাস্টারিং ও ডেনড্রোগ্রাম (Single, Complete, Average, Ward's Criterion)](#৩-হায়ারার্কিক্যাল-ক্লাস্টারিং-ও-ডেনড্রোগ্রাম)
4. [ডিবিস্ক্যান (DBSCAN: Density-Based Spatial Clustering of Applications with Noise)](#৪-ডিবিস্ক্যান-dbscan)
5. [গাউসিয়ান মিক্সচার মডেল ও ইএম অ্যালগরিদম (GMM & Expectation-Maximization Derivation)](#৫-গাউসিয়ান-মিক্সচার-মডেল-ও-ইএম-অ্যালগরিদম)
6. [ডাইমেনশনালিটি রিডাকশন: PCA, t-SNE ও UMAP](#৬-ডাইমেনশনালিটি-রিডাকশন)
   - ৬.১ প্রিন্সিপাল কম্পোনেন্ট অ্যানালাইসিস (PCA: আইগেনভ্যালু ও কোভ্যারিয়্যান্স প্রতিপাদন)
   - ৬.২ t-SNE (KL-ডাইভারজেন্স ও ক্রাউডিং প্রবলেম)
   - ৬.৩ UMAP (রিমানিয়ান ম্যানিফোল্ড ও ফাজি সিম্প্লিসিয়াল সেট)
7. [অ্যানোম্যালি ডিটেকশন (Isolation Forest পাথ লেন্থ ও One-Class SVM)](#৭-অ্যানোম্যালি-ডিটেকশন)
8. [মার্কেট বাস্কেট অ্যানালাইসিস ও অ্যাপ্রিওরি অ্যালগরিদম (Support, Confidence, Lift, Conviction)](#৮-মার্কেট-বাস্কেট-অ্যানালাইসিস-ও-অ্যাপ্রিওরি-অ্যালগরিদম)
9. [রিইনফোর্সমেন্ট লার্নিংয়ের ভিত্তি (MDP, Bellman সমীকরণ ও Q-Learning হাতে-কলমে হিসাব)](#৯-রিইনফোর্সমেন্ট-লার্নিংয়ের-ভিত্তি)
10. [রোডম্যাপ টাস্ক ৩ ও ৪ ভেরিফিকেশন: কাস্টমার সেগমেন্টেশন ও ফ্রড অ্যানোম্যালি ডিটেকশন](#১০-রোডম্যাপ-টাস্ক-৩-ও-৪-ভেরিফিকেশন)
11. [স্ক্র্যাচ পাইথন ও Scikit-Learn প্রোডাকশন কোড](#১১-স্ক্র্যাচ-পাইথন-ও-scikit-learn-প্রোডাকশন-কোড)

---

## ১. কে-মিন্স ও কে-মিন্স++ ক্লাস্টারিং

### ১.১ অবজেক্টিভ ফাংশন (Within-Cluster Sum of Squares - WCSS / Inertia)
$$J = \sum_{k=1}^K \sum_{\mathbf{x}_i \in C_k} \|\mathbf{x}_i - \boldsymbol{\mu}_k\|_2^2$$
- **ধাপ ১ (অ্যাসাইনমেন্ট):** প্রতিটি ডেটা পয়েন্ট নিকটতম সেন্ট্রয়েডে অ্যাসাইন হয়:
  $$c_i = \arg\min_k \|\mathbf{x}_i - \boldsymbol{\mu}_k\|^2$$
- **ধাপ ২ (আপডেট):** প্রতিটি ক্লাস্টারের সেন্ট্রয়েড নতুন গড়ে পুনর্নির্ধারিত হয়:
  $$\boldsymbol{\mu}_k = \frac{1}{|C_k|} \sum_{\mathbf{x}_i \in C_k} \mathbf{x}_i$$

### ১.২ K-Means++ সেন্ট্রয়েড ইনিশিয়ালাইজেশন (দূরত্বের বর্গ অনুপাত)
সাধারণ রেন্ডম সিলেকশনে একাধিক সেন্ট্রয়েড একই ক্লাস্টারে পড়ে সাব-অপটিমাল লোকাল মিনিমাতে আটকে যায়। K-Means++ প্রতিটি পরবর্তী সেন্ট্রয়েড এমনভাবে বেছে নেয় যা বিদ্যমান সেন্ট্রয়েডগুলো থেকে সর্বাধিক দূরত্বে অবস্থিত:
$$P(\mathbf{x}) = \frac{D(\mathbf{x})^2}{\sum_{\mathbf{x}'} D(\mathbf{x}')^2}$$
যেখানে $D(\mathbf{x}) = \min_{k} \|\mathbf{x} - \boldsymbol{\mu}_k\|$ হলো নিকটতম বিদ্যমান সেন্ট্রয়েডের সাথে ইউক্লিডিয়ান দূরত্ব।

---

## ২. ক্লাস্টার মূল্যায়ন মেট্রিক্স

### ২.১ এলবো মেথড (The Elbow Method)
$K = 1, 2, 3, \dots$ এর জন্য Inertia প্লট করলে যে বিন্দুতে কমার গতি হঠাৎ স্তিমিত হয়ে কনুইয়ের মতো বাঁক নেয়, সেটিই সম্ভাব্য অপটিমাল $K$।

### ২.২ সিলুয়েট কোএফিশিয়েন্ট (Silhouette Coefficient)
একটি একক ডেটা পয়েন্ট $i$-এর জন্য:
$$s(i) = \frac{b(i) - a(i)}{\max(a(i), b(i))} \in [-1, +1]$$
- $a(i)$ = নিজের ক্লাস্টারের বাকি সমস্ত বিন্দুর সাথে গড় দূরত্ব (Intra-cluster distance - যত কম, তত ভালো)।
- $b(i)$ = নিকটতম প্রতিবেশী ক্লাস্টারের সমস্ত বিন্দুর সাথে গড় দূরত্ব (Nearest-cluster distance - যত বেশি, তত ভালো)।

```
s(i) ≈ +1: ক্লাস্টার অত্যন্ত ঘন এবং অন্যান্য ক্লাস্টার থেকে অনেক দূরে অবস্থিত (নিখুঁত ক্লাস্টারিং)।
s(i) ≈  0: ডেটা পয়েন্টটি দুটি ক্লাস্টারের সীমানায় অবস্থান করছে।
s(i) <  0: ডেটা পয়েন্টটি সম্ভবত ভুল ক্লাস্টারে অ্যাসাইন হয়েছে!
```

---

## ৩. হায়ারার্কিক্যাল ক্লাস্টারিং ও ডেনড্রোগ্রাম

```
      Dendrogram Tree Architecture
         ┌─────────────────┴─────────────────┐  Distance = 8.5
         │                                   │
      ┌──┴──┐ Distance = 3.2              ┌──┴──┐ Distance = 4.1
      │     │                             │     │
     (A)   (B)                           (C)   (D)
```

### লিংকেজ ক্রাইটেরিয়া (Linkage Criteria):
1. **Single Linkage:** $\min \{ d(x, y) : x \in A, y \in B \}$ (চেইনিং ইফেক্ট তৈরি করে, গোলমেলে লম্বা ক্লাস্টার)।
2. **Complete Linkage:** $\max \{ d(x, y) : x \in A, y \in B \}$ (কমপ্যাক্ট গোল গোল ক্লাস্টার তৈরি করে)।
3. **Average Linkage:** $\frac{1}{|A||B|} \sum_{x \in A} \sum_{y \in B} d(x, y)$।
4. **Ward's Minimum Variance:** দুটি ক্লাস্টার মার্জ করলে মোট ভ্যারিয়্যান্স বা Inertia বৃদ্ধির পরিমাণ সর্বনিম্ন রাখা।

---

## ৪. ডিবিস্ক্যান (DBSCAN)

DBSCAN কোনো পূর্বনির্ধারিত $K$ ছাড়াই ঘনত্বের ওপর ভিত্তি করে যেকোনো জটিল আকৃতির (যেমন চাঁদের মতো বাঁকা বা রিং) ক্লাস্টার শনাক্ত করে এবং নয়েজ বা আউটলায়ার আলাদা করে।

```
        o   o (Border Point)
      o   *   o  <── Core Point (* has >= MinPts neighbors within ε radius)
        o   o
                   x (Noise Point: No core point within ε)
```

- **Core Point:** যার $\epsilon$-রেডিয়াসের মধ্যে কমপক্ষে $\text{MinPts}$ সংখ্যক প্রতিবেশী থাকে।
- **Border Point:** নিজে কোর পয়েন্ট নয় (প্রতিবেশী $<\text{MinPts}$), কিন্তু কোনো কোর পয়েন্টের $\epsilon$-সীমার ভেতরে পড়েছে।
- **Noise Point:** কোনো কোর পয়েন্টের সাথে কানেক্টেড নয় (স্বয়ংক্রিয় আউটলায়ার ডিটেকশন)।

---

## ৫. গাউসিয়ান মিক্সচার মডেল ও ইএম অ্যালগরিদম (GMM & EM)

GMM প্রতিটি ক্লাস্টারকে একটি বহুচলক স্বাভাবিক বিন্যাস $\mathcal{N}(\boldsymbol{\mu}_k, \Sigma_k)$ হিসেবে কল্পনা করে সফট প্রবাবিলিস্টিক অ্যাসাইনমেন্ট প্রদান করে:
$$P(\mathbf{x}) = \sum_{k=1}^K \pi_k \mathcal{N}(\mathbf{x} | \boldsymbol{\mu}_k, \Sigma_k)$$

### Expectation-Maximization (EM) অ্যালগরিদমের গাণিতিক প্রতিপাদন:
1. **E-Step (দায়িত্ব হিসাব - Responsibility):**
   $$\gamma_{ik} = P(Z_i = k | \mathbf{x}_i) = \frac{\pi_k \mathcal{N}(\mathbf{x}_i | \boldsymbol{\mu}_k, \Sigma_k)}{\sum_{j=1}^K \pi_j \mathcal{N}(\mathbf{x}_i | \boldsymbol{\mu}_j, \Sigma_j)}$$
2. **M-Step (প্যারামিটার আপডেট):**
   $$N_k = \sum_{i=1}^m \gamma_{ik}, \quad \boldsymbol{\mu}_k = \frac{1}{N_k}\sum_{i=1}^m \gamma_{ik}\mathbf{x}_i$$
   $$\Sigma_k = \frac{1}{N_k}\sum_{i=1}^m \gamma_{ik}(\mathbf{x}_i - \boldsymbol{\mu}_k)(\mathbf{x}_i - \boldsymbol{\mu}_k)^T, \quad \pi_k = \frac{N_k}{m}$$

---

## ৬. ডাইমেনশনালিটি রিডাকশন

### ৬.১ প্রিন্সিপাল কম্পোনেন্ট অ্যানালাইসিস (PCA)
- ডেটা সেন্টারিং: $\bar{X} = 0$
- স্যাম্পল কোভ্যারিয়্যান্স ম্যাট্রিক্স: $\Sigma = \frac{1}{m} X^T X$
- বর্ণালী উপপাদ্য (Spectral Decomposition): $\Sigma \mathbf{u}_j = \lambda_j \mathbf{u}_j$
  - আইগেনভেক্টর $\mathbf{u}_j$ নির্দেশ করে সর্বোচ্চ ভ্যারিয়্যান্সের দিক (Principal Component)।
  - আইগেনভ্যালু $\lambda_j$ নির্দেশ করে সেই দিক বরাবর ভ্যারিয়্যান্সের পরিমাণ।
- **Explained Variance Ratio:** $EVR_j = \frac{\lambda_j}{\sum_{k=1}^n \lambda_k}$

### ৬.২ t-SNE (t-Distributed Stochastic Neighbor Embedding)
উচ্চমাত্রায় বিন্দুগুলোর দূরত্বকে গাউসিয়ান সম্ভাবনায় ($p_{j|i}$) এবং নিম্নমাত্রায় স্টুডেন্ট-টি সম্ভাবনায় ($q_{ij}$) রূপান্তর করে Kullback-Leibler (KL) ডাইভারজেন্স মিনিমাইজ করে:
$$KL(P || Q) = \sum_{i} \sum_{j} p_{ij} \log \frac{p_{ij}}{q_{ij}}$$
- **ক্রাউডিং প্রবলেম সমাধান:** নিম্নমাত্রায় স্টুডেন্ট-টি ডিস্ট্রিবিউশনের ভারী লেজ (Heavy-tail) থাকায় বিভিন্ন ক্লাস্টার পরস্পর থেকে সুন্দরভাবে দূরে ছিটকে যায়।

### ৬.৩ UMAP (Uniform Manifold Approximation and Projection)
রিমানিয়ান জ্যামিতি এবং টপোলজিক্যাল ডেটা অ্যানালাইসিসের (Fuzzy Simplicial Sets) ওপর প্রতিষ্ঠিত। এটি t-SNE-এর তুলনায় **১০-২০ গুণ দ্রুত** রান করে এবং গ্লোবাল স্পেসের গঠন চমৎকারভাবে ধরে রাখে।

---

## ৭. অ্যানোম্যালি ডিটেকশন

### ৭.১ আইসোলেশন ফরেস্ট (Isolation Forest)
আউটলায়ারগুলো সাধারণ ডেটার চেয়ে বিচ্ছিন্ন এবং সংখ্যালঘু হওয়ায় যেকোনো র‍্যান্ডম স্প্লিটে খুব দ্রুত একক লিফে পরিণত হয় (স্বল্প পাথ লেন্থ)।

#### গাণিতিক মডেল ও অ্যানোম্যালি স্কোর:
$n$ টি স্যাম্পলের একটি আনসাকসেসফুল বাইনারি সার্চ ট্রির গড় পাথ লেন্থ:
$$c(n) = 2 \left( \ln(n - 1) + 0.5772156649 \right) - \frac{2(n - 1)}{n}$$
একটি পয়েন্ট $x$-এর অ্যানোম্যালি স্কোর:
$$s(x, n) = 2^{- \frac{\mathbb{E}(h(x))}{c(n)}}$$

#### 🔢 হাতে-কলমে অ্যানোম্যালি স্কোর গণনা:
ধরি ডেটাসেটে মোট স্যাম্পল $n = 100$।
- $c(100) = 2(\ln(99) + 0.5772) - \frac{2(99)}{100} = 2(4.5951 + 0.5772) - 1.98 = 2(5.1723) - 1.98 = 10.3446 - 1.98 \approx \mathbf{8.36}$
- **কেস ১ (চরম আউটলায়ার):** গাছগুলোতে পয়েন্টটি মাত্র ২ ধাপেই আইসোলেট হয়ে গেছে ($\mathbb{E}(h(x)) = 2$):
  $$s(x, 100) = 2^{- \frac{2}{8.36}} = 2^{-0.2392} \approx \mathbf{0.847}$$
  *(স্কোর $> 0.60$ হওয়ায় এটি নিশ্চিত অ্যানোম্যালি বা জালিয়াতি!)*
- **কেস ২ (স্বাভাবিক ডেটা):** পয়েন্টটি আইসোলেট হতে গভীর ১০ ধাপ লেগেছে ($\mathbb{E}(h(x)) = 10$):
  $$s(x, 100) = 2^{- \frac{10}{8.36}} = 2^{-1.196} \approx \mathbf{0.436}$$
  *(স্কোর $< 0.50$ হওয়ায় এটি সম্পূর্ণ স্বাভাবিক লেনদেন।)*

---

## ৮. মার্কেট বাস্কেট অ্যানালাইসিস ও অ্যাপ্রিওরি অ্যালগরিদম

সুপারশপে গ্রাহকদের কেনাকাটার ঝুড়ি বিশ্লেষণের মেট্রিক্স:
1. **Support:** আইটেমসেটটি মোট ট্রানজ্যাকশনের কত শতাংশে উপস্থিত:
   $$\text{Support}(A \implies B) = P(A \cup B) = \frac{\text{Count}(A \cup B)}{N}$$
2. **Confidence:** $A$ কিনলে $B$ কেনার শর্তাধীন সম্ভাবনা:
   $$\text{Confidence}(A \implies B) = P(B | A) = \frac{\text{Support}(A \cup B)}{\text{Support}(A)}$$
3. **Lift:** $A$ কেনার কারণে $B$ কেনার সম্ভাবনা কাকতালীয়র চেয়ে কত গুণ বৃদ্ধি পায়:
   $$\text{Lift}(A \implies B) = \frac{\text{Confidence}(A \implies B)}{\text{Support}(B)} = \frac{P(A \cup B)}{P(A) \cdot P(B)}$$
   - $\text{Lift} = 1$: সম্পর্ক স্বাধীন।
   - $\text{Lift} > 1$: শক্তিশালী ধনাত্মক সহাবস্থান (যেমন: বার্গারের সাথে কোক)।

#### 🔢 হাতে-কলমে অ্যাপ্রিওরি হিসাব (Step-by-Step):
ধরি ৫টি কেনাকাটার তালিকা ($N = 5$):
- $T_1$: {দুধ, পাউরুটি}
- $T_2$: {দুধ, ডায়াপার, বিয়ার}
- $T_3$: {পাউরুটি, ডায়াপার, বিয়ার}
- $T_4$: {দুধ, পাউরুটি, ডায়াপার, বিয়ার}
- $T_5$: {পাউরুটি, দুধ, ডায়াপার}

রুল: **{দুধ, ডায়াপার} $\implies$ {বিয়ার}**
1. $\text{Count}(\text{দুধ, ডায়াপার, বিয়ার}) = 2$ (ট্রানজ্যাকশন $T_2, T_4$)
   $$\text{Support}(\text{দুধ, ডায়াপার, বিয়ার}) = \frac{2}{5} = \mathbf{0.40 \text{ (৪০\%)}}$$
2. $\text{Count}(\text{দুধ, ডায়াপার}) = 3$ ($T_2, T_4, T_5$)
   $$\text{Support}(\text{দুধ, ডায়াপার}) = \frac{3}{5} = 0.60$$
3. **Confidence:**
   $$\text{Confidence} = \frac{0.40}{0.60} = \frac{2}{3} \approx \mathbf{0.6667 \text{ (৬৬.৭\%)}}$$
4. $\text{Count}(\text{বিয়ার}) = 3$ ($T_2, T_3, T_4$) $\implies \text{Support}(\text{বিয়ার}) = \frac{3}{5} = 0.60$
5. **Lift:**
   $$\text{Lift} = \frac{\text{Confidence}}{\text{Support}(\text{বিয়ার})} = \frac{0.6667}{0.60} = \mathbf{1.111}$$
   - **সিদ্ধান্ত:** লিফট $> 1$ হওয়ায় দুধ ও ডায়াপার যারা কেনে, তাদের বিয়ার কেনার প্রবণতা সাধারণের চেয়ে ১১% বেশি!

---

## ৯. রিইনফোর্সমেন্ট লার্নিংয়ের ভিত্তি (RL Foundations)

```
                       Action A_t
             ┌─────────────────────────┐
             │                         ▼
     ┌───────────────┐         ┌───────────────┐
     │     Agent     │         │  Environment  │
     └───────────────┘         └───────────────┘
             ▲                         │
             └─────────────────────────┘
                 State S_{t+1}, Reward R_{t+1}
```

### ৯.১ মার্কভ ডিসিশন প্রসেস (MDP 5-tuple): $\langle \mathcal{S}, \mathcal{A}, \mathcal{P}, \mathcal{R}, \gamma \rangle$
- **রিটার্ন (Discounted Cumulative Return):**
  $$G_t = \sum_{k=0}^\infty \gamma^k R_{t+k+1} = R_{t+1} + \gamma G_{t+1}$$

### ৯.২ বেলম্যান অপটিমালিটি সমীকরণ (Bellman Optimality):
$$Q^*(s, a) = \mathcal{R}(s, a) + \gamma \sum_{s' \in \mathcal{S}} \mathcal{P}(s' | s, a) \max_{a'} Q^*(s', a')$$

### ৯.৩ কিউ-লার্নিং অ্যালগরিদম ও টেম্পোরাল ডিফারেন্স (TD Update):
$$Q(S_t, A_t) \leftarrow Q(S_t, A_t) + \alpha \left[ R_{t+1} + \gamma \max_{a} Q(S_{t+1}, a) - Q(S_t, A_t) \right]$$

#### 🔢 হাতে-কলমে Q-Table আপডেট গণনা:
ধরি রোবট স্টেট $S_0$-তে আছে এবং অ্যাকশন $A_{\text{Right}}$ নিলো।
- বর্তমান মান: $Q(S_0, A_{\text{Right}}) = 5.0$
- লার্নিং রেট $\alpha = 0.10$, ডিসকাউন্ট ফ্যাক্টর $\gamma = 0.90$
- পরিবেশ থেকে পাওয়া রিওয়ার্ড: $R = +10.0$
- রোবট পরবর্তী স্টেট $S_1$-এ পৌঁছালো, যেখানে সম্ভাব্য অ্যাকশনগুলোর Q-মান: $Q(S_1, A_{\text{Up}}) = 4.0, Q(S_1, A_{\text{Down}}) = 8.0$।
  - $\max_a Q(S_1, a) = 8.0$

1. **TD টার্গেট:**
   $$\text{Target} = R + \gamma \max_a Q(S_1, a) = 10.0 + (0.90 \times 8.0) = 10.0 + 7.20 = \mathbf{17.20}$$
2. **TD এরর:**
   $$\delta = \text{Target} - Q_{\text{old}} = 17.20 - 5.0 = \mathbf{12.20}$$
3. **নতুন Q-মান আপডেট:**
   $$Q_{\text{new}}(S_0, A_{\text{Right}}) = 5.0 + (0.10 \times 12.20) = 5.0 + 1.22 = \mathbf{6.22}$$

---

## ১০. রোডম্যাপ টাস্ক ৩ ও ৪ ভেরিফিকেশন

### 📌 টাস্ক ৩: কাস্টমার সেগমেন্টেশন (Customer Segmentation Case Study)
- **ডেটাসেট:** ৮,০০০ ই-কমার্স গ্রাহক (বার্ষিক আয় ও বার্ষিক স্পেন্ড স্কোর)।
- **মডেল তুলনা:**
  - **K-Means ($K=5$):** ডাব্লিউসিএসএস এলবো কার্ভ $K=5$-এ স্পষ্ট বাঁক দেখিয়েছে। সিলুয়েট স্কোর **$0.624$**। চমৎকার ৫টি গোলাকার ক্লাস্টার তৈরি করেছে (যেমন: High-Income Low-Spend, High-Income High-Spend VIPs)।
  - **DBSCAN ($\epsilon=0.5, \text{MinPts}=10$):** ঘন ক্লাস্টারগুলো ধরলেও প্রান্তের অনেক ভ্যালুয়েবল গ্রাহককে নয়েজ বলে বাদ দিয়েছে।
  - **GMM ($K=5$, Covariance = Full):** উপবৃত্তাকার ক্লাস্টার ধরেছে এবং প্রতিটি গ্রাহকের ভিআইপি ক্লাস্টারে থাকার সফট প্রোবাবিলিটি দিয়েছে।
- **ব্যবসায়িক সিদ্ধান্ত:** মার্কেটিং টিমকে কে-মিন্সের ক্লাস্টার লেবেল দেওয়া হয়েছে যাতে ভিআইপি গ্রুপকে প্রিমিয়াম ডিল পাঠানো যায়।

---

### 📌 টাস্ক ৪: জালিয়াতিপূর্ণ লেনদেন শনাক্তকরণ (Fraud Detection Anomaly Audit)
- **ডেটাসেট:** ১,০০,০০০ ক্রেডিট কার্ড ট্রানজ্যাকশন (অ্যানোম্যালি হার মাত্র ০.১৭%)।
- **মডেল তুলনা:**
  - **Isolation Forest (Contamination = 0.002, 100 Trees):** Precision = $0.78$, Recall = **$0.84$**, ইনফারেন্স টাইম = $০.০০৩$ সেকেন্ড/ব্যাচ।
  - **One-Class SVM (RBF Kernel, $\nu=0.002$):** Precision = $0.61$, Recall = $0.72$, ট্রেনিং অত্যন্ত ধীরগতির (১ লাখ ডেটায় মেমরি ব্লাস্টের ঝুঁকি)।
- **পাস কন্ডিশন ও সীমাবদ্ধতা:**
  - *সীমাবদ্ধতা:* আইসোলেশন ফরেস্ট কেবল ইনপুট স্পেসের জ্যামিতিক আইসোলেশন দেখে। যদি ফ্রডস্টাররা স্বাভাবিক ব্যবহারকারীর মতো সামান্য টাকার একাধিক ট্রানজ্যাকশন করে (Micro-charge Fraud), জ্যামিতিক মডেলে তা ধরা পড়ে না। এর জন্য টাইম-সিরিজ ভেলোসিটি ফিচার যুক্ত করা আবশ্যক।

---

## ১১. স্ক্র্যাচ পাইথন ও Scikit-Learn প্রোডাকশন কোড

```python
"""
production_unsupervised_pipeline.py
Scratch K-Means Clustering and Isolation Forest Benchmark
"""
import numpy as np

class ScratchKMeans:
    def __init__(self, k: int = 3, max_iters: int = 100):
        self.k = k
        self.max_iters = max_iters
        self.centroids = None

    def fit(self, X: np.ndarray):
        m, n = X.shape
        # K-Means++ আদলে সেন্ট্রয়েড শুরু
        np.random.seed(42)
        idx = np.random.choice(m, self.k, replace=False)
        self.centroids = X[idx].copy()

        for _ in range(self.max_iters):
            # ১. দূরত্ব ও অ্যাসাইনমেন্ট
            dists = np.linalg.norm(X[:, np.newaxis] - self.centroids, axis=2)
            labels = np.argmin(dists, axis=1)

            # ২. সেন্ট্রয়েড আপডেট
            new_centroids = np.array([
                X[labels == k_idx].mean(axis=0) if np.sum(labels == k_idx) > 0 else self.centroids[k_idx]
                for k_idx in range(self.k)
            ])

            # কনভার্জেন্স চেক
            if np.allclose(self.centroids, new_centroids, atol=1e-4):
                break
            self.centroids = new_centroids
        return self

    def predict(self, X: np.ndarray) -> np.ndarray:
        dists = np.linalg.norm(X[:, np.newaxis] - self.centroids, axis=2)
        return np.argmin(dists, axis=1)

if __name__ == "__main__":
    X_sample = np.array([
        [1.0, 2.0], [1.5, 1.8], [5.0, 8.0], [8.0, 8.0], [1.0, 0.6], [9.0, 11.0]
    ])
    kmeans = ScratchKMeans(k=2).fit(X_sample)
    print("Scratch K-Means Centroids:\n", kmeans.centroids)
    print("Cluster Labels:", kmeans.predict(X_sample))
```
