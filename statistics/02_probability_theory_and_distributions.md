# Chapter 02: Probability Theory & Distributions in Machine Learning

---

## ১. সম্ভাব্যতা তত্ত্বের মূল ভিত্তি (Axioms of Probability)

মেশিন লার্নিংয়ে অনিশ্চয়তা (Uncertainty) মডেলিং, গোলমালপূর্ণ ডেটা থেকে প্যাটার্ন আবিষ্কার এবং ঝুঁকির মুখে সিদ্ধান্ত গ্রহণের ভিত্তি হলো **সম্ভাব্যতা তত্ত্ব (Probability Theory)**।
আন্দ্রে কলমোগোরোভ (Andrey Kolmogorov, 1933) আধুনিক সম্ভাব্যতা তত্ত্বের ৩টি মৌলিক স্বতঃসিদ্ধ (Axioms) প্রতিষ্ঠা করেন:

ধরা যাক $\Omega$ হলো একটি স্যাম্পল স্পেস (Sample Space) এবং $E$ হলো যেকোনো ঘটনা (Event):

1. **অ-ঋণাত্মকতার স্বতঃসিদ্ধ (Non-Negativity):**
   যেকোনো ঘটনা $E$-এর সম্ভাব্যতা কখনো ঋণাত্মক হতে পারে না:
   $$P(E) \ge 0$$
2. **একক পরিমাপের স্বতঃসিদ্ধ (Unit Measure):**
   সমগ্র স্যাম্পল স্পেসের নিশ্চিত ঘটনার সম্ভাব্যতা সর্বদা $1$:
   $$P(\Omega) = 1$$
3. **যোগের স্বতঃসিদ্ধ (Countable Additivity):**
   পরস্পর বিচ্ছিন্ন বা নিশ্ছিদ্র (mutually exclusive / disjoint) ঘটনাগুলোর ক্ষেত্রে ($E_i \cap E_j = \emptyset$ যখন $i \ne j$):
   $$P\left( \bigcup_{i=1}^\infty E_i \right) = \sum_{i=1}^\infty P(E_i)$$

---

## ২. শর্তাধীন সম্ভাব্যতা ও বেইজের উপপাদ্য (Bayes' Theorem & Bayesian ML)

### ১. শর্তাধীন সম্ভাব্যতা (Conditional Probability):
একটি ঘটনা $B$ ইতিমধ্যে ঘটে গেছে জেনে অন্য একটি ঘটনা $A$ ঘটার সম্ভাব্যতা:

$$P(A \mid B) = \frac{P(A \cap B)}{P(B)}, \quad \text{where } P(B) > 0$$

### ২. সামগ্রিক সম্ভাব্যতা সূত্র (Law of Total Probability):
যদি $\{A_1, A_2, \dots, A_k\}$ স্যাম্পল স্পেসের একটি সম্পূর্ণ পার্টিশন (Partition) হয়:

$$P(B) = \sum_{i=1}^k P(B \mid A_i) P(A_i)$$

### ৩. বেইজের উপপাদ্য (Bayes' Theorem):
শর্তাধীন সম্ভাব্যতা এবং সামগ্রিক সম্ভাব্যতা সমন্বয় করে আমরা পাই মেশিন লার্নিংয়ের অন্যতম ভিত্তি:

$$P(A \mid B) = \frac{P(B \mid A) P(A)}{P(B)} = \frac{P(B \mid A) P(A)}{\sum_i P(B \mid A_i) P(A_i)}$$

```
                                [ বেইজের উপপাদ্যের ৪টি স্তম্ভ ]
                                              |
                   +--------------------------+--------------------------+
                   |                                                     |
            P(A | B) [ Posterior ]                                 P(A) [ Prior ]
     প্রমাণ পাওয়ার পর পরিবর্তিত বিশ্বাস                         ঘটনা পর্যবেক্ষণের পূর্বের বিশ্বাস
                   |                                                     |
                   +--------------------------+--------------------------+
                   |                                                     |
            P(B | A) [ Likelihood ]                                P(B) [ Marginal Evidence ]
   A সত্য হলে প্রমাণ B পাওয়ার সম্ভাব্যতা                     প্রমাণ B ঘটার সামগ্রিক সম্ভাব্যতা
```

### মেশিন লার্নিংয়ে বেইজ: নাইভ বেইজ ক্লাসিফায়ার (Naive Bayes):
ইনপুট ফিচার $X = (x_1, x_2, \dots, x_d)$ থেকে ক্লাস লেবেল $y$ প্রেডিক্ট করার ক্ষেত্রে:

$$P(y \mid x_1, \dots, x_d) \propto P(y) P(x_1, \dots, x_d \mid y)$$

ফিচারগুলো পরস্পরের সাথে শর্তহীনভাবে স্বাধীন—এই সরলীকৃত অনুমানের (Naive Conditional Independence Assumption) কারণে:

$$P(y \mid X) \propto P(y) \prod_{j=1}^d P(x_j \mid y)$$

$$\hat{y} = \arg\max_{y} \left[ \log P(y) + \sum_{j=1}^d \log P(x_j \mid y) \right]$$

---

## ৩. র‍্যান্ডম ভ্যারিয়েবল, এক্সপেক্টেশন ও ভ্যারিয়েন্স (Expectations & Variance)

### ১. এক্সপেক্টেশন বা প্রত্যাশিত মান ($\mathbb{E}[X]$):
কোনো র‍্যান্ডম ভ্যারিয়েবলের সম্ভাব্য সমস্ত মানের সম্ভাব্যতা-ভিত্তিক ভারযুক্ত গড় (Probability-weighted average):
- **ডিসক্রিট ভ্যারিয়েবল:**
  $$\mathbb{E}[X] = \sum_x x \cdot P(X = x)$$
- **কন্টিনিউয়াস ভ্যারিয়েবল:**
  $$\mathbb{E}[X] = \int_{-\infty}^\infty x \cdot f(x) dx$$

### এক্সপেক্টেশনের রৈখিকতা (Linearity of Expectation):
যেকোনো দুটি ভ্যারিয়েবল $X$ এবং $Y$ স্বাধীন হোক বা না হোক (always holds!):

$$\mathbb{E}[aX + bY + c] = a\mathbb{E}[X] + b\mathbb{E}[Y] + c$$

### ২. ভ্যারিয়েন্স ও শর্টকাট সূত্র:
$$\text{Var}(X) = \mathbb{E}\left[ (X - \mathbb{E}[X])^2 \right] = \mathbb{E}[X^2] - (\mathbb{E}[X])^2$$

- **লিনিয়ার স্কেলিং প্রোপার্টি:**
  $$\text{Var}(aX + b) = a^2 \text{Var}(X)$$

---

## ৪. PMF বনাম PDF বনাম CDF (Functional Distinctions)

```
                            [ ডিস্ট্রিবিউশন ফাংশনের রূপভেদ ]
                                          |
        +---------------------------------+---------------------------------+
        |                                                                   |
[ Discrete Random Variable ]                                     [ Continuous Random Variable ]
        |                                                                   |
Probability Mass Function (PMF)                                   Probability Density Function (PDF)
- P(X = x) সরাসরি পয়েন্ট প্রবাবিলিটি                           - f(x) কোনো প্রবাবিলিটি নয়! এটি ঘনমাত্রা।
- 0 <= p(x) <= 1                                                  - f(x) >= 0 (f(x) ১-এর চেয়ে বড় হতে পারে!)
- sum p(x) = 1                                                    - নির্দিষ্ট বিন্দুতে প্রবাবিলিটি P(X = c) = 0!
        \                                                                   /
         +--------------------------------+--------------------------------+
                                          |
                         Cumulative Distribution Function (CDF)
                         - F(x) = P(X <= x)
                         - F(-inf) = 0, F(inf) = 1, সর্বদা নন-ডিক্রিজিং
                         - Continuous এর ক্ষেত্রে: f(x) = d/dx F(x)
```

> **[!IMPORTANT] ক্রিটিক্যাল কনসেপ্ট:**
> কন্টিনিউয়াস ডিস্ট্রিবিউশনের ক্ষেত্রে একটি একক নির্দিষ্ট বিন্দুতে প্রবাবিলিটি সর্বদা শূন্য ($P(X = 3.50000\dots) = 0$), কারণ অবিচ্ছিন্ন স্পেসে বিন্দুর সংখ্যা অসীম। তাই কন্টিনিউয়াস ভ্যারিয়েবলে সর্বদা একটি ব্যবধান বা ইন্টারভালের প্রবাবিলিটি হিসাব করা হয়:
> $$P(a \le X \le b) = \int_a^b f(x) dx$$

---

## ৫. মেশিন লার্নিংয়ের ৬টি অপরিহার্য ডিস্ট্রিবিউশন

### ১. বের্নোলি ডিস্ট্রিবিউশন (Bernoulli Distribution):
একটি একক ট্রায়াল যার ফলাফল কেবল দুটি: ১ (সাফল্য) অথবা ০ (ব্যর্থতা)।
- **PMF:** $P(X = k) = p^k (1 - p)^{1 - k}, \quad k \in \{0, 1\}$
- **গড়:** $\mathbb{E}[X] = p$
- **ভ্যারিয়েন্স:** $\text{Var}(X) = p(1 - p)$
- **এমএল প্রয়োগ:** Logistic Regression-এর একক নমুনার প্রেডিকশন এবং বাইনারি ক্রস-এনট্রপি।

---

### ২. দ্বিপদী ডিস্ট্রিবিউশন (Binomial Distribution):
$p$ সফলতার সম্ভাবনাবিশিষ্ট $n$ সংখ্যক স্বাধীন বের্নোলি ট্রায়ালে মোট সফলতার সংখ্যা $k$।
- **PMF:** $P(X = k) = \binom{n}{k} p^k (1 - p)^{n - k}$
- **গড়:** $\mathbb{E}[X] = np$
- **ভ্যারিয়েন্স:** $\text{Var}(X) = np(1 - p)$
- **এমএল প্রয়োগ:** A/B টেস্টিংয়ে ক্লিক থ্রু রেট (CTR), $n$ টি টেস্ট স্যাম্পলে সঠিক প্রেডিকশনের সংখ্যা।

---

### ৩. নরমাল বা গাউসিয়ান ডিস্ট্রিবিউশন (Normal / Gaussian Distribution):
প্রকৃতির সর্বাধিক প্রচলিত ও গুরুত্বপূর্ণ বেল-শেপড ডিস্ট্রিবিউশন।
- **PDF:**
  $$f(x) = \frac{1}{\sigma \sqrt{2\pi}} \exp\left( -\frac{(x - \mu)^2}{2\sigma^2} \right)$$
- **গড়:** $\mu$, **ভ্যারিয়েন্স:** $\sigma^2$
- **অভিজ্ঞতালব্ধ নিয়ম (Empirical 68-95-99.7% Rule):**
  - $\mu \pm 1\sigma$ সীমার মধ্যে **৬৮.২৭%** ডেটা থাকে।
  - $\mu \pm 2\sigma$ সীমার মধ্যে **৯৫.৪৫%** ডেটা থাকে।
  - $\mu \pm 3\sigma$ সীমার মধ্যে **৯৯.৭৩%** ডেটা থাকে।
- **এমএল প্রয়োগ:** লিনিয়ার রিগ্রেশনের গাউসিয়ান এরর এজাম্পশন, গাউসিয়ান নাইভ বেইজ, VAE ল্যাটেন্ট স্পেস প্রায়র ($\mathcal{N}(0, I)$), ওয়েট ইনিশিয়ালাইজেশন।

---

### ৪. পয়সোঁ ডিস্ট্রিবিউশন (Poisson Distribution):
একটি নির্দিষ্ট সময় বা স্থানের ব্যবধানে কোনো স্বাধীন ঘটনা ঘটার সংখ্যা (Count Data)।
- **PMF:**
  $$P(X = k) = \frac{\lambda^k e^{-\lambda}}{k!}, \quad k \in \{0, 1, 2, \dots\}$$
- **গড়:** $\mathbb{E}[X] = \lambda$
- **ভ্যারিয়েন্স:** $\text{Var}(X) = \lambda$ *(গড় ও ভ্যারিয়েন্স সমান!)*
- **এমএল প্রয়োগ:** সার্ভারে প্রতি সেকেন্ডে রিকোয়েস্ট সংখ্যা, ওয়েবসাইটের ভিজিটর ট্রাফিক প্রেডিকশন, কল সেন্টারের লোড প্রেডিকশন।

---

### ৫. এক্সপোনেনশিয়াল ডিস্ট্রিবিউশন (Exponential Distribution):
একটি পয়সোঁ প্রসেসে একটি ঘটনা ঘটার পর পরবর্তী ঘটনাটি ঘটার মধ্যবর্তী অপেক্ষমাণ সময় (Waiting time between events)।
- **PDF:**
  $$f(x) = \lambda e^{-\lambda x}, \quad x \ge 0$$
- **CDF:** $F(x) = 1 - e^{-\lambda x}$
- **গড়:** $\mathbb{E}[X] = \frac{1}{\lambda}$, **ভ্যারিয়েন্স:** $\text{Var}(X) = \frac{1}{\lambda^2}$
- **মেমোরিলেস প্রোপার্টি (Memoryless Property):** $P(X > s + t \mid X > s) = P(X > t)$
- **এমএল প্রয়োগ:** সার্ভার ফেইলিউর মডেলিং, কাস্টমার ড্রপ-আউট টাইম, সারভাইভাল অ্যানালিসিস।

---

### ৬. কন্টিনিউয়াস ইউনিফর্ম ডিস্ট্রিবিউশন (Continuous Uniform Distribution):
একটি ব্যবধান $[a, b]$-এর প্রতিটি বিন্দুর সম্ভাব্যতা সমান।
- **PDF:**
  $$f(x) = \frac{1}{b - a}, \quad a \le x \le b$$
- **গড়:** $\frac{a + b}{2}$, **ভ্যারিয়েন্স:** $\frac{(b - a)^2}{12}$
- **এমএল প্রয়োগ:** র্যান্ডম ড্রপআউট মাস্কিং, এক্সপ্লোরেশন স্ট্র্যাটেজি, ওজন ইনিশিয়ালাইজেশন (Xavier/He uniform)।

---

## ৬. সংখ্যাগত উদাহরণ (Step-by-Step Numerical Examples)

### উদাহরণ ১: মেডিকেল টেস্টে বেইজের উপপাদ্য প্রয়োগ
একটি বিরল রোগের পরিস্থিতি বিবেচনা করি:
- জনসংখ্যায় রোগের প্রাদুর্ভাব (Prior): $P(\text{Disease}) = 0.01$ (১%) $\implies P(\text{Healthy}) = 0.99$ (৯৯%)
- টেস্টের সংবেদনশীলতা (Sensitivity / True Positive): $P(+ \mid \text{Disease}) = 0.95$ (৯৫%)
- টেস্টের মিথ্যা পজিটিভ হার (False Positive Rate): $P(+ \mid \text{Healthy}) = 0.05$ (৫%)

একজন ব্যক্তির টেস্ট রিপোর্ট **পজিটিভ (+)** এসেছে। তার সত্যিই রোগটি থাকার প্রকৃত সম্ভাবনা (Posterior $P(\text{Disease} \mid +)$) কত?

#### ধাপ ১: পজিটিভ টেস্ট হওয়ার সামগ্রিক সম্ভাবনা ($P(+)$ - Evidence)
$$P(+) = P(+ \mid \text{Disease}) P(\text{Disease}) + P(+ \mid \text{Healthy}) P(\text{Healthy})$$
$$P(+) = (0.95 \times 0.01) + (0.05 \times 0.99)$$
$$P(+) = 0.0095 + 0.0495 = \mathbf{0.0590} \; (5.9\%)$$

#### ধাপ ২: বেইজের উপপাদ্য প্রয়োগ করে পোস্টেরিয়র নির্ণয়
$$P(\text{Disease} \mid +) = \frac{P(+ \mid \text{Disease}) P(\text{Disease})}{P(+)} = \frac{0.0095}{0.0590} \approx \mathbf{0.1610} \; (\mathbf{16.1\%})$$

**চমকপ্রদ অন্তর্দৃষ্টি:** টেস্টটি ৯৫% নিখুঁত হওয়া সত্ত্বেও রিপোর্ট পজিটিভ আসার পর রোগীর রোগ থাকার সম্ভাবনা মাত্র ১৬.১%! এর কারণ হলো রোগের প্রায়র প্রবাবিলিটি অত্যন্ত কম (১%) হওয়ায় ৯৯% সুস্থ মানুষের ৫% মিথ্যা পজিটিভের সংখ্যা আসল রোগীর চেয়ে বহুগুণ বেশি। এই কারণেই বেইজিয়ান রিজনিং এমএল চিকিৎসায় অপরিহার্য।

---

### উদাহরণ ২: পয়সোঁ ডিস্ট্রিবিউশনে সার্ভার ওভারলোড হিসাব
একটি এআই মডেলের API এন্ডপয়েন্টে গড়ে প্রতি সেকেন্ডে ৩টি রিকোয়েস্ট আসে ($\lambda = 3$)। আগামী ১ সেকেন্ডে ঠিক ৫টি রিকোয়েস্ট আসার সম্ভাবনা কত?

$$P(X = 5) = \frac{3^5 e^{-3}}{5!} = \frac{243 \times e^{-3}}{120}$$

যেহেতু $e^{-3} \approx 0.049787$:
$$P(X = 5) = \frac{243 \times 0.049787}{120} = \frac{12.0982}{120} \approx \mathbf{0.1008} \; (\mathbf{10.08\%})$$

---

## ৭. সম্পূর্ণ পাইথন বাস্তবায়ন (Pure Python & SciPy Simulations)

নিচে একটি স্বয়ংসম্পূর্ণ, ডিপেন্ডেন্সি-মুক্ত পাইথন কোড দেওয়া হলো যা বেইজিয়ান পোস্টেরিয়র গণনা এবং বিভিন্ন ডিস্ট্রিবিউশনের ম্যাথমেটিকাল ক্যালকুলেশন নিখুঁতভাবে সম্পাদন করে:

```python
"""
Probability Theory & Key ML Distributions Implementation.
Pure Python and standard math library.
"""

import math
from typing import Dict, Any


# ============================================================================
# 1. Bayes' Theorem Calculator
# ============================================================================
class BayesianInference:
    @staticmethod
    def compute_posterior(prior: float, sensitivity: float, false_positive_rate: float) -> Dict[str, float]:
        """
        P(A|B) = P(B|A)P(A) / [P(B|A)P(A) + P(B|~A)P(~A)]
        """
        prior_not_a = 1.0 - prior
        marginal_evidence = (sensitivity * prior) + (false_positive_rate * prior_not_a)
        posterior = (sensitivity * prior) / marginal_evidence
        return {
            "Prior": prior,
            "Marginal_Evidence": marginal_evidence,
            "Posterior": posterior
        }


# ============================================================================
# 2. Probability Distributions Library
# ============================================================================
class ProbabilityDistributions:
    @staticmethod
    def bernoulli_pmf(k: int, p: float) -> float:
        assert k in (0, 1), "k must be 0 or 1 for Bernoulli"
        return p if k == 1 else (1.0 - p)

    @staticmethod
    def binomial_pmf(k: int, n: int, p: float) -> float:
        comb = math.comb(n, k)
        return comb * (p ** k) * ((1.0 - p) ** (n - k))

    @staticmethod
    def normal_pdf(x: float, mu: float = 0.0, sigma: float = 1.0) -> float:
        coeff = 1.0 / (sigma * math.sqrt(2.0 * math.pi))
        exponent = -0.5 * ((x - mu) / sigma) ** 2
        return coeff * math.exp(exponent)

    @staticmethod
    def normal_cdf_approx(x: float, mu: float = 0.0, sigma: float = 1.0) -> float:
        """Approximates standard normal CDF using error function math.erf."""
        z = (x - mu) / sigma
        return 0.5 * (1.0 + math.erf(z / math.sqrt(2.0)))

    @staticmethod
    def poisson_pmf(k: int, lmbda: float) -> float:
        assert k >= 0, "k must be non-negative integer"
        return ((lmbda ** k) * math.exp(-lmbda)) / math.factorial(k)

    @staticmethod
    def exponential_pdf(x: float, lmbda: float) -> float:
        if x < 0:
            return 0.0
        return lmbda * math.exp(-lmbda * x)


# ============================================================================
# 3. Verification & Demo Execution
# ============================================================================
if __name__ == "__main__":
    print("--- 1. Testing Bayesian Medical Diagnosis ---")
    bayes_res = BayesianInference.compute_posterior(prior=0.01, sensitivity=0.95, false_positive_rate=0.05)
    for k, v in bayes_res.items():
        print(f"  {k}: {v:.4f}")

    print("\n--- 2. Testing Key Probability Distributions ---")
    # Binomial: 10 coin tosses, prob of 5 heads with fair coin
    b_prob = ProbabilityDistributions.binomial_pmf(k=5, n=10, p=0.5)
    print(f"Binomial PMF (k=5, n=10, p=0.5):    {b_prob:.4f}")

    # Normal PDF at peak (x=0) and CDF within 1 std dev
    norm_peak = ProbabilityDistributions.normal_pdf(x=0.0, mu=0.0, sigma=1.0)
    norm_1sigma = ProbabilityDistributions.normal_cdf_approx(1.0) - ProbabilityDistributions.normal_cdf_approx(-1.0)
    print(f"Standard Normal Peak PDF at x=0:     {norm_peak:.4f}")
    print(f"Normal Probability in [-1, +1] std: {norm_1sigma:.4f} (Matches 68.27% Rule!)")

    # Poisson: Lambda=3, P(X=5)
    poiss = ProbabilityDistributions.poisson_pmf(k=5, lmbda=3.0)
    print(f"Poisson PMF (k=5, lambda=3.0):       {poiss:.4f}")

    # Exponential PDF at x=2, lambda=0.5
    exp_val = ProbabilityDistributions.exponential_pdf(x=2.0, lmbda=0.5)
    print(f"Exponential PDF (x=2.0, lambda=0.5): {exp_val:.4f}")
    print("Probability calculations verified successfully!")
```

---

## ৮. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স

| ডিস্ট্রিবিউশন | ধরন | সাপোর্ট (Domain) | প্রধান প্যারামিটার | সাধারণ ব্যবহারের ক্ষেত্র |
| :--- | :--- | :--- | :--- | :--- |
| **Bernoulli** | ডিসক্রিট | $\{0, 1\}$ | $p$ (সফলতার সম্ভাবনা) | লজিস্টিক রিগ্রেশনের একক ইভেন্ট প্রেডিকশন |
| **Binomial** | ডিসক্রিট | $\{0, 1, \dots, n\}$ | $n$ (ট্রায়াল), $p$ | ব্যাচে মডেলের সঠিক প্রেডিকশনের সংখ্যা |
| **Poisson** | ডিসক্রিট | $\{0, 1, 2, \dots, \infty\}$| $\lambda$ (ঘটার গড় হার) | ওয়েব ট্রাফিক, সার্ভারে রিকোয়েস্টের সংখ্যা |
| **Normal** | কন্টিনিউয়াস | $(-\infty, \infty)$ | $\mu$ (গড়), $\sigma$ (স্ট্যান্ডার্ড ডেভিয়েশন)| রিগ্রেশন রেসিজুয়াল, VAE ল্যাটেন্ট, ন্যাচারাল ডেটা |
| **Exponential**| কন্টিনিউয়াস | $[0, \infty)$ | $\lambda$ (রেট) | দুটি সার্ভার ব্যর্থতার মধ্যবর্তী সময়, কাস্টমার চার্ন |
| **Uniform** | কন্টিনিউয়াস | $[a, b]$ | $a$ (সর্বনিম্ন), $b$ (সর্বোচ্চ) | র্যান্ডম এক্সপ্লোরেশন, ওয়েট ইনিশিয়ালাইজেশন |

---

## ৯. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: Probability Density Function (PDF)-এর মান কি কখনো ১-এর চেয়ে বড় হতে পারে?**
   - **উত্তর:** হ্যাঁ! PDF হলো সম্ভাব্যতার ঘনত্ব (Density), সরাসরি সম্ভাব্যতা নয়। উদাহরণস্বরূপ, একটি ইউনিফর্ম ডিস্ট্রিবিউশন $U(0, 0.1)$-এর ক্ষেত্রে ব্যবধানের দৈর্ঘ্য $0.1$। অতএব, $f(x) = \frac{1}{0.1 - 0} = 10$ সর্বত্র। এখানে ডেনসিটি ১০, যা ১-এর চেয়ে অনেক বড়। শুধুমাত্র এর ইন্টিগ্রেশন বা ক্ষেত্রফল সর্বদা $1$ হতে হবে।
2. **প্রশ্ন: নাইভ বেইজ ক্লাসিফায়ারকে কেন "নাইভ" (Naive) বলা হয়?**
   - **উত্তর:** কারণ এটি ধরে নেয় যে কোনো ক্লাসের সাপেক্ষে ইনপুট ফিচারগুলো পরস্পরের থেকে সম্পূর্ণ স্বাধীন ($P(x_1, x_2 \mid y) = P(x_1 \mid y) P(x_2 \mid y)$)। বাস্তব জগতে ফিচারগুলোর মধ্যে প্রায়শই গভীর পারস্পরিক সম্পর্ক থাকে (যেমন টেক্সট ক্লাসিফিকেশনে "Hong" এবং "Kong" শব্দ দুটি একে অপরের ওপর নির্ভরশীল)। তবে এই অবাস্তব অনুমান সত্ত্বেও নাইভ বেইজ টেক্সট ক্লাসিফিকেশনে দুর্দান্ত পারফর্ম করে।
3. **প্রশ্ন: লজিস্টিক রিগ্রেশনে ক্রস-এনট্রপি লস কীভাবে বের্নোলি ডিস্ট্রিবিউশন থেকে আসে?**
   - **উত্তর:** লজিস্টিক রিগ্রেশন ধরে নেয় টার্গেট $y \in \{0, 1\}$ একটি বের্নোলি ডিস্ট্রিবিউশন $P(y \mid x) = \hat{y}^y (1 - \hat{y})^{1-y}$ অনুসরণ করে। সমস্ত স্বাধীন নমুনার যৌথ লাইকলিহুড হলো $\prod \hat{y}_i^{y_i} (1 - \hat{y}_i)^{1-y_i}$। এই লাইকলিহুড ম্যাক্সিমাইজ করার জন্য ঋণাত্মক লগ নিলে পাওয়া যায়: $-\sum [y_i \log \hat{y}_i + (1 - y_i) \log(1 - \hat{y}_i)]$, যা হুবহু বাইনারি ক্রস-এনট্রপি (Binary Cross-Entropy Loss)।
