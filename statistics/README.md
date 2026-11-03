# Complete Handbook of Statistics for Machine Learning & Data Science

স্বাগতম **Statistics for Machine Learning & Data Science** কমপ্রিহেনসিভ হ্যান্ডবুকে।
মেশিন লার্নিং ও ডিপ লার্নিংয়ের সমস্ত অ্যালগরিদম—লিনিয়ার রিগ্রেশন থেকে শুরু করে আধুনিক এলএলএম ও ভ্যারিয়েশনাল অটো-এনকোডার—সবকিছুর পেছনে চালিকাশক্তি হিসেবে কাজ করে পরিসংখ্যান (Statistics), সম্ভাব্যতা তত্ত্ব (Probability Theory) এবং ইনফরমেশন থিওরি (Information Theory)।

এই হ্যান্ডবুকটি এমনভাবে সংকলন করা হয়েছে যাতে প্রতিটি অধ্যায়ে তাত্ত্বিক পটভূমি, গভীর গাণিতিক ডেরিভেশন (Mathematical Proofs), বাস্তবসম্মত সংখ্যাভিত্তিক স্টেপ-বাই-স্টেপ ক্যালকুলেশন (Hand-Calculated Numerical Examples), ভিজ্যুয়াল আর্কিটেকচার ডায়াগ্রাম এবং রানযোগ্য পাইথন কোড অন্তর্ভুক্ত থাকে।

---

## 🗺️ অধ্যায়ভিত্তিক সূচিপত্র ও রোডম্যাপ (Curriculum Map)

```
                                  [ STATISTICS FOR MACHINE LEARNING ]
                                                   |
      +--------------------------------------------+--------------------------------------------+
      |                                            |                                            |
[ 1. Descriptive Statistics ]           [ 2. Probability & Distributions ]           [ 3. Inferential & Hypothesis ]
  - Central Tendency (Mean, Med, Mode)    - Axioms & Conditional Prob                  - CLT & Sampling Distributions
  - Dispersion (Var, Std, IQR, Range)     - Bayes' Theorem & Bayesian ML               - Confidence Intervals
  - Percentiles & Outlier Clipping        - PMF, PDF, CDF Mechanics                    - Null/Alt Hypotheses & p-values
  - Covariance & Correlation              - Discrete & Continuous Dists                - Type I/II Errors & Z/t/Chi2/ANOVA
      |                                            |                                            |
      +--------------------------------------------+--------------------------------------------+
      |                                            |                                            |
[ 4. Parameter Estimation ]             [ 5. Multivariate & Linear Algebra ]         [ 6. Information Theory ]
  - Maximum Likelihood (MLE)              - Covariance Matrix (Sigma)                  - Shannon Entropy H(X)
  - Maximum A Posteriori (MAP)            - Eigenvalues & PCA Maximization             - Conditional Entropy & Mutual Info
  - Bias-Variance Tradeoff Proof          - Mahalanobis Distance & Outliers            - KL Divergence & Cross-Entropy
```

---

### ১. [অধ্যায় ০১: ডেসক্রিপটিভ স্ট্যাটিস্টিকস (Descriptive Statistics)](file:///g:/MNC/statistics/01_descriptive_statistics.md)
- **কেন্দ্রীয় প্রবণতা (Central Tendency):** Arithmetic Mean, Weighted Mean, Geometric Mean, Median, Mode (সীমাবদ্ধতা ও স্কিউড ডেটায় ব্যবহার)।
- **বিস্তার ও বিচ্যুতি (Dispersion & Spread):** Range, Interquartile Range (IQR), Sample Variance ($s^2$ with Bessel's correction $n-1$), Population Variance ($\sigma^2$), Standard Deviation।
- **পারসেন্টাইল ও আউটলায়ার ডিটেকশন:** Percentiles, Quartiles, Tukey's 1.5x IQR Rule, Z-Score Clipping (Winsorization)।
- **দ্বিমুখী সম্পর্ক:** Covariance $\text{Cov}(X,Y)$, Pearson Linear Correlation ($r$), Spearman Monotonic Rank Correlation ($\rho$), মাল্টিকোলিনিয়ারিটি চেকিং।

---

### ২. [অধ্যায় ০২: প্রবাবিলিটি থিওরি ও ডিস্ট্রিবিউশনস (Probability Theory & Distributions)](file:///g:/MNC/statistics/02_probability_theory_and_distributions.md)
- **সম্ভাব্যতার মৌলিক স্বতঃসিদ্ধ (Kolmogorov Axioms):** Non-negativity, Normalization, Additivity।
- **শর্তাধীন সম্ভাব্যতা ও বেইজের উপপাদ্য:** $P(A \mid B) = \frac{P(B \mid A)P(A)}{P(B)}$, Prior, Likelihood, Posterior, Evidence; Naive Bayes ক্লাসিফায়ার।
- **র‍্যান্ডম ভ্যারিয়েবল ও এক্সপেক্টেশন:** Discrete vs Continuous Random Variables, Expected Value $\mathbb{E}[X]$, Variance $\text{Var}(X)$, Linearity of Expectation।
- **ফাংশনাল বিশ্লেষণ:** Probability Mass Function (PMF), Probability Density Function (PDF), Cumulative Distribution Function (CDF)।
- **মেশিন লার্নিংয়ের অপরিহার্য ডিস্ট্রিবিউশন:** Bernoulli, Binomial, Normal (Gaussian), Poisson, Exponential, Continuous Uniform।

---

### ৩. [অধ্যায় ০৩: ইনফারেন্সিয়াল স্ট্যাটিস্টিকস ও হাইপোথিসিস টেস্টিং (Inferential Statistics & Hypothesis Testing)](file:///g:/MNC/statistics/03_inferential_statistics_and_hypothesis_testing.md)
- **স্যাম্পলিং ও সেন্ট্রাল লিমিট থিওরেম (CLT):** স্যাম্পল গড় স্বাভাবিক বণ্টনে উপনীত হওয়ার শর্ত এবং গাণিতিক ভিত্তি ($\bar{X} \sim \mathcal{N}(\mu, \sigma^2/n)$)।
- **কনফিডেন্স ইন্টারভাল (Confidence Intervals):** পপুলেশন প্যারামিটারের মার্জিন অব এরর এবং কনফিডেন্স লেভেল ($95\%, 99\%$) হিসাব।
- **হাইপোথিসিস টেস্টিং ফ্রেমওয়ার্ক:** Null Hypothesis ($H_0$), Alternative Hypothesis ($H_1$), Significance Level ($\alpha$), $p$-value-এর সঠিক পরিসংখ্যানগত অর্থ।
- **ভুলের শ্রেণিবিভাগ:** Type I Error ($\alpha$ - False Positive), Type II Error ($\beta$ - False Negative), Statistical Power ($1 - \beta$)।
- **প্যারামেট্রিক ও নন-প্যারামেট্রিক টেস্ট:** 
  - One-sample ও Two-sample **Z-Test** ($\sigma$ জানা থাকলে)।
  - Student's **t-Test** (One-sample, Independent two-sample, Paired t-test)।
  - **Chi-Square ($\chi^2$) Test** of Independence (ক্যাটেগোরিক্যাল ফিচারের স্বাধীনতা পরীক্ষা)।
  - **ANOVA (Analysis of Variance):** ৩ বা ততোধিক মডেল/গ্রুপের তুলনার জন্য $F$-Statistic এবং Sum of Squares ডিকম্পোজিশন।

---

### ৪. [অধ্যায় ০৪: প্যারামিটার এস্টিমেশন ও অপ্টিমাইজেশন (Parameter Estimation & Optimization)](file:///g:/MNC/statistics/04_parameter_estimation_and_optimization.md)
- **ম্যাক্সিমাম লাইকলিহুড এস্টিমেশন (MLE):** লাইকলিহুড ফাংশন $L(\theta)$, লগ-লাইকলিহুড $\ell(\theta)$, গাউসিয়ান ডিস্ট্রিবিউশনের জন্য $\hat{\mu}_{\text{MLE}}$ এবং $\hat{\sigma}^2_{\text{MLE}}$ নির্ণয়; MLE কীভাবে লজিস্টিক ও নিউরাল নেটওয়ার্কের Cross-Entropy লসে রূপ নেয়।
- **ম্যাক্সিমাম এ পোস্টেরিয়রি (MAP):** প্রায়র ডিস্ট্রিবিউশন অন্তর্ভুক্তিকরণ, গাউসিয়ান প্রায়র কীভাবে $L_2$ Regularization (Ridge) এবং ল্যাপলাস প্রায়র কীভাবে $L_1$ Regularization (Lasso)-তে রূপ নেয় তার গাণিতিক প্রমাণ।
- **বায়াস-ভ্যারিয়েন্স ট্রেডঅফ (Bias-Variance Decomposition):** Mean Squared Error (MSE)-কে $\text{Bias}^2 + \text{Variance} + \text{Irreducible Noise}$ আকারে সম্পূর্ণ গাণিতিক প্রমাণ ও আন্ডারফিটিং/ওভারফিটিং ডায়াগনস্টিকস।

---

### ৫. [অধ্যায় ০৫: মাল্টিভ্যারিয়েট স্ট্যাটিস্টিকস ও লিনিয়ার অ্যালজেব্রা ইন্টারসেকশন (Multivariate Statistics & Linear Algebra)](file:///g:/MNC/statistics/05_multivariate_statistics_and_linear_algebra.md)
- **র‍্যান্ডম ভেক্টর ও কোভ্যারিয়েন্স ম্যাট্রিক্স:** মাল্টিভ্যারিয়েট মিন ভেক্টর $\boldsymbol{\mu}$, কোভ্যারিয়েন্স ম্যাট্রিক্স $\boldsymbol{\Sigma} \in \mathbb{R}^{d \times d}$, Positive Semi-Definiteness।
- **আইগেনভ্যালু, আইগেনভেক্টর ও PCA:** ভ্যারিয়েন্স ম্যাক্সিমাইজেশন সমস্যা, ল্যাগ্রাঞ্জ মাল্টিপ্লায়ার ডেরিভেশন, স্পেকট্রাল থিওরেম এবং ডাইমেনশনালিটি রিডাকশন।
- **মাহালানোবিস দূরত্ব (Mahalanobis Distance):** ফিচারসমূহের পারস্পরিক কোরিলেশন ও ভ্যারিয়েন্স সমন্বয় করে দূরত্ব নির্ণয় ($D_M(x) = \sqrt{(x - \mu)^T \Sigma^{-1} (x - \mu)}$); মাল্টিভ্যারিয়েট আউটলায়ার ডিটেকশনে ইউক্লিডিয়ান দূরত্বের চেয়ে শ্রেষ্ঠত্ব।

---

### ৬. [অধ্যায় ০৬: ইনফরমেশন থিওরি (Information Theory)](file:///g:/MNC/statistics/06_information_theory.md)
- **শ্যানন এনট্রপি (Shannon Entropy $H(X)$):** তথ্যের অপ্রত্যাশিততা ও সারপ্রাইজাল (Self-Information), $H(X) = -\sum P(x)\log_2 P(x)$, সর্বোচ্চ এনট্রপির শর্ত।
- **জয়েন্ট ও কন্ডিশনাল এনট্রপি:** $H(X, Y)$ এবং $H(Y \mid X)$।
- **মিউচুয়াল ইনফরমেশন (Mutual Information $I(X; Y)$):** ডিসিশন ট্রির ইনফরমেশন গেইন (ID3/C4.5), ভ্যারিয়েবল ডিপেনডেন্সি।
- **কুলব্যাক-লেইবলার (KL) ডাইভারজেন্স:** $D_{KL}(P \parallel Q)$, অ্যাসিমেট্রি, নন-নেগেটিভিটি প্রমাণ।
- **ক্রস-এনট্রপি লস (Cross-Entropy Loss):** $H(P, Q) = H(P) + D_{KL}(P \parallel Q)$, সফটম্যাক্স ক্লাসিফিকেশন লস এবং অপটিমাইজেশনাল উত্তলতা (Convexity)।

---

## 📊 স্ট্যাটিস্টিক্যাল ডিসিশন ম্যাট্রিক্স (Statistical Test Selection Flowchart)

```
                            [ What is your Hypothesis Testing Goal? ]
                                                |
        +---------------------------------------+---------------------------------------+
        |                                                                               |
[ Comparing Numerical Means ]                                           [ Testing Categorical Relationships ]
        |                                                                               |
        +---> How many groups?                                            Chi-Square (chi^2) Test of Independence
        |        |
        |        +---> 1 or 2 Groups:
        |        |        |
        |        |        +---> Population Variance Known?
        |        |                 |
        |        |                 +-- Yes ---> Z-Test
        |        |                 +-- No  ---> Student's t-Test
        |        |                                |
        |        |                                +-- Independent Samples? -> Two-Sample t-Test
        |        |                                +-- Before & After?       -> Paired t-Test
        |        |
        |        +---> 3 or more Groups:
        |                 |
        |                 +---> One-Way ANOVA (F-Test) -> Post-hoc Tukey HSD
```

---

## 🛠️ কোড ও ম্যাথমেটিকাল কনভেনশন
- **প্রোগ্রামিং ল্যাঙ্গুয়েজ:** Python 3 (NumPy, SciPy, Pandas, Matplotlib/Seaborn)।
- **ভাষা শৈলী:** প্রাতিষ্ঠানিক বাংলা ভাষা, সাথে প্রথম বন্ধনীতে আন্তর্জাতিক টেকনিক্যাল পরিভাষা।
- **গাণিতিক প্রতীক:** LaTeX ফরম্যাটে নিখুঁত সমীকরণ এবং বাস্তবসম্মত সংখ্যা দিয়ে তৈরি হ্যান্ড-ক্যালকুলেশন।
