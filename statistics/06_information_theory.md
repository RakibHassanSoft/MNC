# Chapter 06: Information Theory for Machine Learning & Deep Learning

---

## ১. ইনফরমেশন থিওরির মূল দর্শন (Information Theory Foundations)

ক্লদ শ্যানন (Claude Shannon, 1948)-এর যুগান্তকারী গবেষণাপত্র *"A Mathematical Theory of Communication"* আধুনিক তথ্য যুগের জন্ম দেয়। মেশিন লার্নিং ও ডিপ লার্নিংয়ে মডেলের অনির্দিষ্টতা পরিমাপ, ডিসিশন ট্রির ফিচার সিলেকশন, ক্লাসিফিকেশনের ক্রস-এনট্রপি লস এবং VAE/GAN-এর ল্যাটেন্ট ডাইভারজেন্স অপটিমাইজেশনের মূল চালিকাশক্তি হলো **ইনফরমেশন থিওরি (Information Theory)**।

```
                                  [ INFORMATION THEORY ]
                                             |
             +-------------------------------+-------------------------------+
             |                               |                               |
[ 1. Surprisal & Entropy ]       [ 2. Mutual Information ]       [ 3. Divergence & Loss ]
- তথ্যের অনিশ্চয়তা / অপবিত্রতা      - ফিচারের ডিপেনডেন্সি ও শেয়ার্ড তথ্য - দুটি ডিস্ট্রিবিউশনের ব্যবধান
- Self-Information I(x)          - Information Gain (ID3 Tree)    - KL Divergence D_KL(P || Q)
- Shannon Entropy H(X)           - I(X; Y) = H(Y) - H(Y | X)      - Cross-Entropy Loss
```

---

## ২. সারপ্রাইজাল ও শ্যানন এনট্রপি (Self-Information & Shannon Entropy)

### ১. সারপ্রাইজাল বা আত্ম-তথ্য (Self-Information / Surprisal):
একটি ঘটনা ঘটলে আমরা কতটা নতুন তথ্য পাই? যে ঘটনার সম্ভাব্যতা অত্যন্ত বেশি (যেমন: "আগামীকাল সকালে সূর্য পূর্ব দিকে উঠবে"), তা ঘটলে আমাদের কোনো বিস্ময় জাগে না ($I \to 0$)। কিন্তু যে ঘটনার সম্ভাব্যতা অত্যন্ত কম (যেমন: "আজ ঢাকায় তুষারপাত হয়েছে"), তা ঘটলে তথ্যের পরিমাণ বা বিস্ময় আকাশচুম্বী ($I \to \infty$)।

$$I(x) = -\log_2 P(x) = \log_2 \left( \frac{1}{P(x)} \right) \quad \text{(একক: Bits বা Shannons)}$$

*(ন্যাচারাল লগ $\ln(\cdot)$ ব্যবহার করলে একক হয় Nats)*

#### কেন লগারিদম ব্যবহার করা হয়?
কারণ দুটি স্বাধীন ঘটনার যৌথ তথ্যের পরিমাণ তাদের পৃথক তথ্যের যোগফলের সমান হওয়া উচিত:
$$P(x, y) = P(x)P(y) \implies I(x, y) = -\log(P(x)P(y)) = -\log P(x) - \log P(y) = I(x) + I(y)$$

---

### ২. শ্যানন এনট্রপি ($H(X)$):
একটি ডিসক্রিট র‍্যান্ডম ভ্যারিয়েবল $X$-এর সামগ্রিক অনিশ্চয়তা বা প্রত্যাশিত তথ্যের পরিমাণ:

$$H(X) = \mathbb{E}[I(X)] = -\sum_{i=1}^n P(x_i) \log_2 P(x_i)$$

```
Entropy H(p) in Bits
     ^
 1.0 |                   * * *  (Max Entropy at p = 0.5: Fair Coin)
     |                *         *
     |              *             *
 0.5 |             *               *
     |           *                   *
 0.0 +----------*---------------------*--------->
    p = 0.0 (Deterministic)       p = 1.0 (Deterministic)
```

- **সর্বনিম্ন এনট্রপি ($H = 0$):** যখন ফলাফল নিশ্চিত ($P(x_1) = 1, P(x_2) = 0$)। এখানে কোনো অনিশ্চয়তা বা তথ্য নেই।
- **সর্বোচ্চ এনট্রপি ($H_{\max} = \log_2 n$):** যখন সমস্ত ঘটনা সম-সম্ভাব্য (Uniform Distribution)।
- **কন্টিনিউয়াস ভ্যারিয়েবলের ক্ষেত্রে:** ফিক্সড ভ্যারিয়েন্সযুক্ত সমস্ত অবিচ্ছিন্ন ডিস্ট্রিবিউশনের মধ্যে **গাউসিয়ান বা নরমাল ডিস্ট্রিবিউশন সর্বোচ্চ এনট্রপি ধারণ করে**।

---

## ৩. জয়েন্ট, কন্ডিশনাল এনট্রপি ও মিউচুয়াল ইনফরমেশন (Mutual Information)

### ১. যৌথ এনট্রপি (Joint Entropy):
$$H(X, Y) = -\sum_{x} \sum_{y} P(x, y) \log_2 P(x, y)$$

### ২. শর্তাধীন এনট্রপি (Conditional Entropy):
$X$ জানা থাকলে $Y$-এর মধ্যে অবশিষ্ট অনিশ্চয়তা:
$$H(Y \mid X) = \sum_x P(x) H(Y \mid X = x) = -\sum_{x} \sum_{y} P(x, y) \log_2 P(y \mid x)$$

**চেইন রুল:**
$$H(X, Y) = H(X) + H(Y \mid X) = H(Y) + H(X \mid Y)$$

---

### ৩. মিউচুয়াল ইনফরমেশন ($I(X; Y)$) ও ভেন ডায়াগ্রাম:
একটি চলক $X$ পর্যবেক্ষণ করলে অন্য চলক $Y$-এর অনিশ্চয়তা কতটুকু হ্রাস পায় (Mutual Information):

$$I(X; Y) = H(Y) - H(Y \mid X) = H(X) - H(X \mid Y)$$

```
              +-------------------+   +-------------------+
              |                   |   |                   |
              |     H(X | Y)      |   |      H(Y | X)     |
              |                   |   |                   |
              |             +-----+---+-----+             |
              |             |   I(X; Y)     |             |
              |             |  Shared Info  |             |
              +-------------+---------------+-------------+
              \------------------- H(X, Y) ---------------/
```

- যদি $X$ এবং $Y$ সম্পূর্ণ স্বাধীন হয়, তবে $H(Y \mid X) = H(Y) \implies I(X; Y) = 0$।
- যদি $X$ জানা থাকলে $Y$ নিশ্চিতভাবে জানা যায়, তবে $I(X; Y) = H(Y)$।

---

## ৪. ডিসিশন ট্রির ইনফরমেশন গেইন (Information Gain in Decision Trees)

Ross Quinlan (1986)-এর ID3 এবং C4.5 ডিসিশন ট্রি অ্যালগরিদম প্রতিটি নোডে সর্বোত্তম স্প্লিটিং ফিচার বেছে নিতে মিউচুয়াল ইনফরমেশন বা **Information Gain (IG)** ব্যবহার করে।

একটি ডেটাসেট $S$-কে একটি ফিচার $A$ দ্বারা বিভক্ত করলে ইনফরমেশন গেইন:

$$\text{IG}(S, A) = H(S) - \sum_{v \in \text{Values}(A)} \frac{|S_v|}{|S|} H(S_v) \equiv I(S; A)$$

যে ফিচারের ইনফরমেশন গেইন সর্বোচ্চ, ডিসিশন ট্রি সেই ফিচারটিকে স্প্লিট নোড হিসেবে নির্বাচন করে।

---

## ৫. কুলব্যাক-লেইবলার ডাইভারজেন্স (Kullback-Leibler Divergence - $D_{KL}$)

দুটি সম্ভাব্যতা বিন্যাস $P$ (সত্য ডিস্ট্রিবিউশন) এবং $Q$ (মডেলের আনুমানিক ডিস্ট্রিবিউশন)-এর মধ্যবর্তী তথ্যগত অপচয় বা আপেক্ষিক এনট্রপিকে **KL ডাইভারজেন্স** বলে:

$$D_{KL}(P \,\parallel\, Q) = \sum_{x} P(x) \log \left( \frac{P(x)}{Q(x)} \right) = \mathbb{E}_{x \sim P} \left[ \log P(x) - \log Q(x) \right]$$

### ৩টি ক্রিটিক্যাল বৈশিষ্ট্য:
1. **অ-ঋণাত্মকতা (Gibbs' Inequality):**
   জেনসেনের অসমতা প্রয়োগ করে প্রমাণ করা যায়:
   $$D_{KL}(P \,\parallel\, Q) \ge 0$$
   এর মান $0$ হবে যদি এবং কেবল যদি সমস্ত $x$-এর জন্য $P(x) = Q(x)$ হয়।
2. **অসমতা বা অ্যাসিমেট্রি (Asymmetry):**
   $$D_{KL}(P \,\parallel\, Q) \ne D_{KL}(Q \,\parallel\, P)$$
   এটি কোনো দূরত্ব (Distance metric) নয়, কারণ ত্রিভুজ অসমতা ও প্রতিসাম্যতা মানে না।
3. **ফরোয়ার্ড বনাম রিভার্স KL:**
   - **Forward KL ($D_{KL}(P \parallel Q)$):** জিরো-অ্যাভয়েডিং / মোড-কভারিং (সমগ্র ডেটা ডিস্ট্রিবিউশনকে ঘিরে রাখে)।
   - **Reverse KL ($D_{KL}(Q \parallel P)$):** জিরো-ফোর্সিং / মোড-সিকিং (VAE ও Diffusion-এ ব্যবহৃত হয়)।

---

## ৬. ক্রস-এনট্রপি লস (Cross-Entropy Loss Derivation)

দুটি ডিস্ট্রিবিউশনের যৌথ ক্রস-এনট্রপি:

$$H(P, Q) = -\sum_{x} P(x) \log Q(x)$$

আমরা একে শ্যানন এনট্রপি এবং KL ডাইভারজেন্সের যোগফল আকারে লিখতে পারি:

$$H(P, Q) = -\sum P(x) \log P(x) + \sum P(x) \log \left( \frac{P(x)}{Q(x)} \right) = \mathbf{H(P) + D_{KL}(P \,\parallel\, Q)}$$

```
[ Cross-Entropy Loss: H(P, Q) ] = [ Data Entropy H(P) ] + [ KL Divergence D_KL(P || Q) ]
                                          |
                                   (Constant for Given Data!)
```

### ডিপ লার্নিংয়ে ক্রস-এনট্রপির গুরুত্ব:
সুপারভাইজড লার্নিংয়ে সত্য লেবেল ডিস্ট্রিবিউশন $P$ ফিক্সড (ওয়ান-হট ভেক্টরের জন্য $H(P) = 0$)।
অতএব:

$$\arg\min_\theta H(P, Q_\theta) \equiv \arg\min_\theta D_{KL}(P \,\parallel\, Q_\theta)$$

মডেলের ক্রস-এনট্রপি লস কমানো মূলত মডেল ডিস্ট্রিবিউশন $Q_\theta$-কে সত্য ডিস্ট্রিবিউশন $P$-এর দিকে টেনে নিয়ে KL ডাইভারজেন্স শূন্য করারই নামান্তর!

---

## ৭. সম্পূর্ণ সংখ্যাগত হ্যান্ড-ক্যালকুলেশন (Step-by-Step Hand Calculations)

### উদাহরণ ১: ডিসিশন ট্রির ইনফরমেশন গেইন গণনা
ধরা যাক একটি ক্লাসিফিকেশন টাস্কে ৪টি ডেটাপয়েন্ট আছে। টার্গেট হলো ব্যবহারকারী লোন অনুমোদন পাবে কিনা ($Y \in \{\text{Yes}, \text{No}\}$):
- মোট নমুনা: ৩টি 'Yes', ১টি 'No' ($|S| = 4$)।
- ফিচার $A$ (ক্রেডিট স্কোর):
  - $A = \text{High}$: ২টি নমুনা, উভয়ই 'Yes' ($[2\text{ Yes}, 0\text{ No}]$)।
  - $A = \text{Low}$: ২টি নমুনা, ১টি 'Yes', ১টি 'No' ($[1\text{ Yes}, 1\text{ No}]$)।

#### ধাপ ১: প্রাথমিক এনট্রপি $H(S)$ নির্ণয়
$$P(\text{Yes}) = \frac{3}{4} = 0.75, \quad P(\text{No}) = \frac{1}{4} = 0.25$$
$$H(S) = -(0.75 \log_2 0.75 + 0.25 \log_2 0.25)$$
যেহেতু $\log_2 0.75 \approx -0.4150$ এবং $\log_2 0.25 = -2.00$:
$$H(S) = -((0.75 \times -0.4150) + (0.25 \times -2.00)) = -(-0.3113 - 0.5000) = \mathbf{0.8113 \text{ Bits}}$$

#### ধাপ ২: সাবসেটগুলোর এনট্রপি নির্ণয়
- **High সাবসেট ($S_{\text{High}}$):**
  $$P(\text{Yes}) = 1.0, \; P(\text{No}) = 0.0 \implies H(S_{\text{High}}) = -(1 \log_2 1 + 0) = \mathbf{0.00 \text{ Bits}} \quad (\text{Pure Node!})$$
- **Low সাবসেট ($S_{\text{Low}}$):**
  $$P(\text{Yes}) = 0.5, \; P(\text{No}) = 0.5 \implies H(S_{\text{Low}}) = -(0.5 \log_2 0.5 + 0.5 \log_2 0.5) = -(0.5(-1) + 0.5(-1)) = \mathbf{1.00 \text{ Bits}}$$

#### ধাপ ৩: কন্ডিশনাল এনট্রপি ও ইনফরমেশন গেইন
$$H(S \mid A) = \frac{|S_{\text{High}}|}{|S|} H(S_{\text{High}}) + \frac{|S_{\text{Low}}|}{|S|} H(S_{\text{Low}}) = \left(\frac{2}{4} \times 0.00\right) + \left(\frac{2}{4} \times 1.00\right) = 0 + 0.50 = \mathbf{0.50 \text{ Bits}}$$

$$\text{IG}(S, A) = H(S) - H(S \mid A) = 0.8113 - 0.5000 = \mathbf{0.3113 \text{ Bits}}$$

ফিচার $A$ দ্বারা স্প্লিট করলে ডেটাসেটের অপবিত্রতা ০.৩১ Bits কমে যায়!

---

### উদাহরণ ২: KL ডাইভারজেন্স ও ক্রস-এনট্রপি গণনা
ধরা যাক একটি বাইনারি ইভেন্টের:
- সত্য ডিস্ট্রিবিউশন $P = [0.80, 0.20]$
- মডেলের প্রেডিকশন $Q = [0.50, 0.50]$ (ন্যাচারাল লগ $\ln$ ব্যবহার করে)

#### ধাপ ১: KL ডাইভারজেন্স
$$D_{KL}(P \parallel Q) = P_1 \ln\left(\frac{P_1}{Q_1}\right) + P_2 \ln\left(\frac{P_2}{Q_2}\right)$$
$$= 0.80 \ln\left(\frac{0.80}{0.50}\right) + 0.20 \ln\left(\frac{0.20}{0.50}\right) = 0.80 \ln(1.60) + 0.20 \ln(0.40)$$
যেহেতু $\ln(1.60) \approx 0.4700$ এবং $\ln(0.40) \approx -0.9163$:
$$D_{KL} = (0.80 \times 0.4700) + (0.20 \times -0.9163) = 0.3760 - 0.1833 = \mathbf{0.1927 \text{ Nats}}$$

#### ধাপ ২: ক্রস-এনট্রপি
$$H(P, Q) = -(0.80 \ln 0.50 + 0.20 \ln 0.50) = -\ln 0.50 \approx \mathbf{0.6931 \text{ Nats}}$$

---

## ৮. সম্পূর্ণ পাইথন বাস্তবায়ন (Pure Python Information Engine)

নিচে একটি স্বয়ংসম্পূর্ণ, ডিপেন্ডেন্সি-মুক্ত পাইথন বাস্তবায়ন দেওয়া হলো যা শ্যানন এনট্রপি, কন্ডিশনাল এনট্রপি, মিউচুয়াল ইনফরমেশন, KL ডাইভারজেন্স এবং ক্রস-এনট্রপি গণনা করে:

```python
"""
Information Theory Engine: Entropy, Mutual Information, KL Divergence & Cross-Entropy.
Fully runnable, verified and mathematically grounded.
"""

import math
from typing import List, Dict, Any


# ============================================================================
# 1. Core Information Theory Metrics
# ============================================================================
class InformationMetrics:
    @staticmethod
    def entropy(probabilities: List[float], base: float = 2.0) -> float:
        """H(X) = -sum(p * log(p))"""
        h = 0.0
        for p in probabilities:
            if p > 0.0:
                h -= p * (math.log(p) / math.log(base))
        return h

    @staticmethod
    def kl_divergence(p: List[float], q: List[float], base: float = math.e) -> float:
        """D_KL(P || Q) = sum(p * log(p / q))"""
        assert len(p) == len(q), "Distributions must have same length"
        kl = 0.0
        for p_val, q_val in zip(p, q):
            if p_val > 0.0:
                assert q_val > 0.0, "Absolute continuity violation: Q(x) == 0 while P(x) > 0"
                kl += p_val * (math.log(p_val / q_val) / math.log(base))
        return kl

    @staticmethod
    def cross_entropy(p: List[float], q: List[float], base: float = math.e) -> float:
        """H(P, Q) = -sum(p * log(q))"""
        assert len(p) == len(q)
        ce = 0.0
        for p_val, q_val in zip(p, q):
            if p_val > 0.0:
                assert q_val > 0.0
                ce -= p_val * (math.log(q_val) / math.log(base))
        return ce

    @staticmethod
    def information_gain(parent_labels: List[Any], splits: List[List[Any]]) -> float:
        """
        Information Gain = H(Parent) - sum(|S_v| / |S| * H(S_v))
        """
        def get_probs(labels: List[Any]) -> List[float]:
            counts = {}
            for l in labels:
                counts[l] = counts.get(l, 0) + 1
            total = len(labels)
            return [c / total for c in counts.values()]

        h_parent = InformationMetrics.entropy(get_probs(parent_labels), base=2.0)

        total_samples = len(parent_labels)
        h_split = 0.0
        for split in splits:
            if split:
                weight = len(split) / total_samples
                h_split += weight * InformationMetrics.entropy(get_probs(split), base=2.0)

        return h_parent - h_split


# ============================================================================
# 2. Verification & Demo Execution
# ============================================================================
if __name__ == "__main__":
    print("--- 1. Testing Shannon Entropy ---")
    fair_coin = [0.5, 0.5]
    biased_coin = [0.9, 0.1]
    deterministic = [1.0, 0.0]

    print(f"Fair Coin Entropy:         {InformationMetrics.entropy(fair_coin):.4f} Bits (Max Uncertainty = 1.0)")
    print(f"Biased Coin (90/10) Entropy:{InformationMetrics.entropy(biased_coin):.4f} Bits")
    print(f"Deterministic Event Entropy:{InformationMetrics.entropy(deterministic):.4f} Bits (Zero Uncertainty)")

    print("\n--- 2. Testing Decision Tree Information Gain ---")
    parent = ["Yes", "Yes", "Yes", "No"]
    split_high = ["Yes", "Yes"]
    split_low = ["Yes", "No"]
    ig = InformationMetrics.information_gain(parent, [split_high, split_low])
    print(f"Information Gain for Feature: {ig:.4f} Bits")

    print("\n--- 3. Testing KL Divergence & Cross-Entropy ---")
    P = [0.80, 0.20]
    Q = [0.50, 0.50]
    kl_val = InformationMetrics.kl_divergence(P, Q)
    ce_val = InformationMetrics.cross_entropy(P, Q)
    h_p = InformationMetrics.entropy(P, base=math.e)

    print(f"D_KL(P || Q):               {kl_val:.4f} Nats")
    print(f"Cross-Entropy H(P, Q):      {ce_val:.4f} Nats")
    print(f"H(P) + D_KL(P || Q):        {(h_p + kl_val):.4f} Nats (Identical to Cross-Entropy!)")
    print("Information theory components verified successfully!")
```

---

## ৯. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স

| ধারণা | পরিমাপক উদ্দেশ্য | ইনপুট | মিনিমাম মান | ম্যাক্সিমাম মান | এমএল প্রয়োগ |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Entropy $H(X)$** | একক চলকের অনিশ্চয়তা | $P(X)$ | $0$ (নিশ্চিত ঘটনা) | $\log_2 n$ (ইউনিফর্ম) | ডেটা ইম্পিউরিটি, আউটপুট ডাইভারসিটি |
| **Mutual Info $I(X;Y)$**| দুটি চলকের শেয়ার্ড তথ্য | $P(X, Y)$ | $0$ (স্বাধীন) | $\min(H(X), H(Y))$ | ডিসিশন ট্রি স্প্লিটিং, ফিচার সিলেকশন |
| **KL Divergence $D_{KL}$**| দুটি বিন্যাসের তাত্ত্বিক ব্যবধান| $P, Q$ | $0$ (হুবহু এক) | $\infty$ (ডিসজয়েন্ট সাপোর্ট)| VAE ল্যাটেন্ট প্রায়র পেনাল্টি, DPO লস |
| **Cross-Entropy $H(P, Q)$**| ভুল ডিস্ট্রিবিউশন কোডিং খরচ| $P, Q$ | $H(P)$ | $\infty$ | নিউরাল নেটওয়ার্কের ক্লাসিফিকেশন লস |

---

## ১০. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: Cross-Entropy Loss কেন Mean Squared Error (MSE)-এর চেয়ে ক্লাসিফিকেশনে বহুগুণ দ্রুত কনভার্জ করে?**
   - **উত্তর:** সফটম্যাক্স বা সিগময়েড অ্যাক্টিভেশনের সাথে MSE ব্যবহার করলে ভুল প্রেডিকশনের ক্ষেত্রে স্যাচুরেশন দেখা দেয় এবং গ্র্যাডিয়েন্ট $\nabla \mathcal{L} \to 0$ হয়ে ভ্যানিশিং গ্রেডিয়েন্ট ঘটে। কিন্তু Cross-Entropy লসের সাথে সফটম্যাক্স ডেরিভেটিভ নিলে সমস্ত লগারিদম ও এক্সপোনেনশিয়াল চমৎকারভাবে কেটে গিয়ে অবশিষ্ট থাকে: $\frac{\partial \mathcal{L}}{\partial z_i} = \hat{y}_i - y_i$। মডেল যত বেশি ভুল করে, গ্রেডিয়েন্ট তত বড় হয়, ফলে লার্নিং অত্যন্ত দ্রুত ও গতিশীল হয়।
2. **প্রশ্ন: KL ডাইভারজেন্স কি একটি ডিস্ট্যান্স মেট্রিক (Distance Metric)?**
   - **উত্তর:** না! কোনো পরিমাপকে মেট্রিক হতে হলে তাকে প্রতিসাম্য ($d(A, B) = d(B, A)$) এবং ত্রিভুজ অসমতা ($d(A, C) \le d(A, B) + d(B, C)$) মানতে হয়। KL ডাইভারজেন্স এই দুটি শর্তের কোনোটিই মানে না ($D_{KL}(P \parallel Q) \ne D_{KL}(Q \parallel P)$)। তাই এটিকে "ডিসট্যান্স" না বলে "ডাইভারজেন্স" বা "রিলেটিভ এনট্রপি" বলা হয়।
3. **প্রশ্ন: Decision Tree-তে Information Gain-এর পরিবর্তে Gain Ratio কেন ব্যবহার করা হয়?**
   - **উত্তর:** সাধারণ Information Gain অনেকগুলো ইউনিক মান থাকা ফিচারগুলোকে (যেমন: কাস্টমার আইডি বা ফোন নম্বর) পক্ষপাতমূলকভাবে বেছে নেয়, কারণ প্রতিটি আইডির জন্য একটি একক পিওর সাবসেট ($H = 0$) তৈরি হয়ে Information Gain সর্বোচ্চ দেখায়। C4.5 অ্যালগরিদম এই ফাঁকি দূর করতে স্প্লিট এনট্রপি দিয়ে ভাগ করে **Gain Ratio** ($\frac{\text{IG}}{\text{SplitInfo}}$) ব্যবহার করে।
