# অধ্যায় ৫: এনসেম্বল লার্নিং ও অ্যাডভান্সড বুস্টিং (Ensemble Learning & Boosting Complete Masterclass)

> *"Wisdom of the Crowd: একটি একক জটিল মডেলের চেয়ে বহু বৈচিত্র্যময় সাধারণ মডেলের সমন্বিত সিদ্ধান্ত প্রায় সবসময় বেশি নিখুঁত, নির্ভরযোগ্য ও স্থিতিশীল হয়।"*
> 
> এনসেম্বল লার্নিং হলো আধুনিক মেশিন লার্নিং প্রতিযোগিতা (যেমন Kaggle) এবং ইন্ডাস্ট্রিয়াল ট্যাবুলার ডেটার সবচেয়ে শক্তিশালী মডেলিং কৌশল। এই অধ্যায়ে ব্যাগিং, বুস্টিং, স্ট্যাকিং, ভোটিং, গ্রেডিয়েন্ট বুস্টিংয়ের অভ্যন্তরীণ অ্যালগরিদম, এবং আধুনিক XGBoost, LightGBM ও CatBoost-এর গাণিতিক ভিত্তি ও **হাতে-কলমে সংখ্যাভিত্তিক প্রতিটি ধাপের গণনা (Step-by-Step Numerical Calculations)** সন্নিবেশিত হয়েছে।

---

## 📑 সম্পূর্ণ বিষয়সূচি (Table of Contents)
1. [এনসেম্বল লার্নিংয়ের দর্শন ও গাণিতিক ভিত্তি (Condorcet's Theorem & Variance Reduction)](#১-এনসেম্বল-লার্নিংয়ের-দর্শন-ও-গাণিতিক-ভিত্তি)
2. [ব্যাগিং ও র‍্যান্ডম ফরেস্ট (Bagging & Random Forest Architecture)](#২-ব্যাগিং-ও-র‍্যান্ডম-ফরেস্ট)
   - ২.১ বুটস্ট্র্যাপ স্যাম্পলিং ও র‍্যান্ডম সাবস্পেস ($m = \sqrt{p}$)
   - ২.২ আউট-অব-ব্যাগ (OOB) এরর উপপাদ্যের গাণিতিক প্রমাণ ($\approx 36.8\%$)
3. [ভোটিং ক্লাসিফায়ার (Hard Voting বনাম Soft Voting)](#৩-ভোটিং-ক্লাসিফায়ার)
4. [স্ট্যাকিং ও মেটা-লার্নিং (Stacking & Out-of-Fold Leakage Prevention)](#৪-স্ট্যাকিং-ও-মেটা-লার্নিং)
5. [অ্যাডা বুস্ট (AdaBoost: হাতে-কলমে স্যাম্পল ও লার্নার ওয়েট আপডেট গণনা)](#৫-অ্যাডা-বুস্ট-adaboost)
6. [গ্রেডিয়েন্ট বুস্টিং মেশিন (GBM Internals: সিউডো-রেসিডুয়াল প্রতিপাদন)](#৬-গ্রেডিয়েন্ট-বুস্টিং-মেশিন-gbm-internals)
7. [আধুনিক বুস্টিং ট্রায়ো: XGBoost, LightGBM ও CatBoost](#৭-আধুনিক-বুস্টিং-ট্রায়ো)
   - ৭.১ XGBoost: ২য় অর্ডারের টেইলর সিরিজ, লিফ ওয়েট ও স্প্লিট গেইন (হাতে-কলমে হিসাব)
   - ৭.২ LightGBM: হিস্টোগ্রাম বিনিং, GOSS ও EFB মেকানিজম
   - ৭.৩ CatBoost: অর্ডার্ড বুস্টিং ও সিমেট্রিক অবলিভিয়াস ট্রি
8. [ফিচার ইম্পর্ট্যান্সের সীমাবদ্ধতা (MDI বায়াস বনাম Permutation Importance বনাম SHAP)](#৮-ফিচার-ইম্পর্ট্যান্সের-সীমাবদ্ধতা)
9. [স্ক্র্যাচ পাইথন ও Scikit-Learn প্রোডাকশন কোড](#৯-স্ক্র্যাচ-পাইথন-ও-scikit-learn-প্রোডাকশন-কোড)

---

## ১. এনসেম্বল লার্নিংয়ের দর্শন ও গাণিতিক ভিত্তি

### ১.১ কনডরসেটের জুরি উপপাদ্য (Condorcet's Jury Theorem)
যদি $B$ টি স্বাধীন মডেলের প্রত্যেকের সঠিক সিদ্ধান্তের সম্ভাবনা $p > 0.5$ হয়, তবে সংখ্যাগরিষ্ঠের ভোটের ভিত্তিতে নেওয়া যৌথ সিদ্ধান্তের সঠিক হওয়ার সম্ভাবনা:
$$P(\text{Majority Correct}) = \sum_{k=\lfloor B/2 \rfloor + 1}^B \binom{B}{k} p^k (1 - p)^{B - k} \xrightarrow{B \to \infty} 1.0$$
- *বাস্তব দৃষ্টান্ত:* যদি একটি ক্লাসিফায়ারের নির্ভুলতা $p = 0.60$ হয় এবং এমন ২৫টি স্বাধীন ক্লাসিফায়ারের ভোট নেওয়া হয়, তবে যৌথ সিদ্ধান্তের নির্ভুলতা বৃদ্ধি পেয়ে **৭৮.৭%** এ উন্নীত হয়!

### ১.২ ভ্যারিয়্যান্স হ্রাসের সমীকরণ
$B$ সংখ্যক মডেলের গড় প্রেডিকশনের ভ্যারিয়্যান্স:
$$\text{Var}\left( \frac{1}{B} \sum_{i=1}^B X_i \right) = \rho \sigma^2 + \frac{1 - \rho}{B} \sigma^2$$
- যেখানে $\rho$ হলো মডেলগুলোর মধ্যবর্তী কোরিলেশন।
- যদি মডেলগুলোকে বৈচিত্র্যময় করে $\rho \to 0$ করা যায়, তবে ভ্যারিয়্যান্স সরাসরি $\frac{\sigma^2}{B}$-এ নেমে আসে (ওভারফিটিং পুরোপুরি নির্মূল হয়)।

---

## ২. ব্যাগিং ও র‍্যান্ডম ফরেস্ট

```
                       Original Training Set (N samples, p features)
                       /               |               \
             Bootstrap 1 (N)      Bootstrap 2 (N)     Bootstrap B (N)
                  |                    |                    |
             Tree 1 (sqrt(p))     Tree 2 (sqrt(p))     Tree B (sqrt(p))
             (Deep, Unpruned)     (Deep, Unpruned)     (Deep, Unpruned)
                  \                    |                    /
                   \                   |                   /
                    ───► Aggregate: Vote (Class) / Mean (Reg) ◄───
```

### ২.১ আউট-অব-ব্যাগ (OOB) ডেটার উপপাদ্য
মূল ডেটাসেটের আকার $N$ হলে, প্রতিস্থাপন সহ বুটস্ট্র্যাপ স্যাম্পলিংয়ে কোনো একটি নির্দিষ্ট ডেটা পয়েন্ট একবারও না আসার সম্ভাবনা:
$$P(\text{Not Selected in 1 draw}) = 1 - \frac{1}{N}$$
$$P(\text{Not Selected in } N \text{ draws}) = \left( 1 - \frac{1}{N} \right)^N$$
ক্যালকুলাসের স্ট্যান্ডার্ড লিমিট অনুযায়ী:
$$\lim_{N \to \infty} \left( 1 - \frac{1}{N} \right)^N = e^{-1} = \frac{1}{e} \approx 0.367879 \approx \mathbf{36.8\%}$$
- **অর্থ:** কোনো ক্রস-ভ্যালিডেশন ছাড়াই প্রতিটি ট্রির ট্রেনিংয়ে অব্যবহৃত থাকা এই ৩৭% ডেটা (OOB স্যাম্পল) দিয়ে মডেলের টেস্ট এরর আনবায়াসডভাবে নির্ণয় করা সম্ভব!

---

## ৩. ভোটিং ক্লাসিফায়ার (Hard Voting বনাম Soft Voting)

```
Model 1 (SVM):      P(Class 1) = 0.49  --> Hard Pred: 0
Model 2 (RF):       P(Class 1) = 0.48  --> Hard Pred: 0
Model 3 (XGBoost):  P(Class 1) = 0.99  --> Hard Pred: 1

Hard Voting: Class 0 wins (2 votes vs 1 vote)  <-- ভুল সিদ্ধান্ত!
Soft Voting: Avg Prob = (0.49 + 0.48 + 0.99) / 3 = 0.6533 --> Class 1 wins! <-- সঠিক সিদ্ধান্ত!
```
- **পেশাদার সিদ্ধান্ত:** সর্বদা **Soft Voting** ব্যবহার করুন, কারণ এটি মডেলের কনফিডেন্সকে গুরুত্ব দেয়।

---

## ৪. স্ট্যাকিং ও মেটা-লার্নিং

স্ট্যাকিংয়ে একাধিক বেস মডেলের প্রেডিকশনকে ইনপুট ফিচার হিসেবে নিয়ে একটি মেটা-লার্নার (যেমন: Logistic Regression বা Ridge) ট্রেন করা হয়।

### Out-of-Fold (OOF) প্রেডিকশন কেন বাধ্যতামূলক?
যদি বেইজ মডেলগুলোকে পুরো ট্রেনিং সেটে ট্রেন করে তাদের প্রেডিকশন দিয়ে মেটা-মডেল তৈরি করা হয়, তবে মেটা-মডেল মারাত্মক **ওভারফিট (Data Leakage)** করবে।
- **সমাধান:** K-Fold CV করে প্রতি ফোল্ডের আনসিন ভ্যালিডেশন প্রেডিকশন জোড়া লাগিয়ে OOF ম্যাট্রিক্স তৈরি করতে হয়।

---

## ৫. অ্যাডা বুস্ট (AdaBoost: Adaptive Boosting)

অ্যাডা বুস্টে প্রতিটি রাউন্ডে ভুল ক্লাসিফাইড ডেটার ওজন বাড়ানো হয় যাতে পরবর্তী উইক লার্নার (Decision Stump) সেই কঠিন স্যাম্পলগুলোর ওপর বেশি মনোযোগ দেয়।

### গাণিতিক সূত্রাবলী:
1. উইক লার্নারের ওজন: $\alpha_t = \frac{1}{2} \ln\left( \frac{1 - \epsilon_t}{\epsilon_t} \right)$
2. স্যাম্পল ওজন আপডেট: $w_i^{(t+1)} = \frac{w_i^{(t)} \exp\left( -\alpha_t y_i h_t(\mathbf{x}_i) \right)}{Z_t}$
3. চূড়ান্ত এনসেম্বল প্রেডিকশন: $H(\mathbf{x}) = \text{sign}\left( \sum_{t=1}^T \alpha_t h_t(\mathbf{x}) \right)$

#### 🔢 হাতে-কলমে অ্যাডা বুস্ট ওয়েট আপডেট গণনা:
ধরি ৪টি স্যাম্পল আছে: $y = [+1, +1, -1, -1]$।
প্রাথমিক সমান ওজন: $w^{(0)} = [0.25, 0.25, 0.25, 0.25]$।
একটি ডিসিশন স্টাম্প প্রথম স্যাম্পলটিকে ভুল করেছে ($h(x_1) = -1$) এবং বাকি ৩টিকে সঠিকভাবে ক্লাসিফাই করেছে।

1. **ভুলের হার ($\epsilon_t$):**
   $$\epsilon_t = \sum_{\text{errors}} w_i = w_1 = \mathbf{0.25}$$
2. **মডেলের গুরুত্ব ($\alpha_t$):**
   $$\alpha_t = \frac{1}{2} \ln\left( \frac{1 - 0.25}{0.25} \right) = \frac{1}{2} \ln(3.0) = \frac{1}{2}(1.0986) = \mathbf{0.5493}$$
3. **ওজন আপডেট:**
   - ভুল স্যাম্পল ১: $y_1 h(x_1) = -1 \implies w_1' = 0.25 \times e^{+0.5493} = 0.25 \times 1.732 = \mathbf{0.4330}$
   - সঠিক স্যাম্পল ২, ৩, ৪: $y_i h(x_i) = +1 \implies w_i' = 0.25 \times e^{-0.5493} = 0.25 \times 0.5774 = \mathbf{0.1443}$
4. **নরমালাইজেশন ফ্যাক্টর ($Z_t$):**
   $$Z_t = 0.4330 + 0.1443 + 0.1443 + 0.1443 = 0.4330 + 0.4330 = \mathbf{0.8660}$$
5. **নতুন নরমালাইজড ওজন ($w^{(1)}$):**
   - $w_1 = \frac{0.4330}{0.8660} = \mathbf{0.500}$ (ভুল হওয়ায় ওজন দ্বিগুণ হয়ে ২৫% থেকে ৫০% হয়েছে!)
   - $w_2 = w_3 = w_4 = \frac{0.1443}{0.8660} = \mathbf{0.1667}$ (সঠিক হওয়ায় ওজন কমেছে!)

---

## ৬. গ্রেডিয়েন্ট বুস্টিং মেশিন (GBM Internals)

অ্যাডা বুস্ট কেবল স্যাম্পলের ওজন বদলায়, কিন্তু গ্রেডিয়েন্ট বুস্টিং সরাসরি পূর্ববর্তী এনসেম্বলের **সিউডো-রেসিডুয়াল (Pseudo-residuals / লস ফাংশনের নেগেটিভ গ্রেডিয়েন্ট)** ফিট করে:
$$r_{im} = - \left[ \frac{\partial L(y_i, F(\mathbf{x}_i))}{\partial F(\mathbf{x}_i)} \right]_{F(\mathbf{x}) = F_{m-1}(\mathbf{x})}$$
- **MSE লসে:** $L = \frac{1}{2}(y - F)^2 \implies -\frac{\partial L}{\partial F} = y_i - F_{m-1}(\mathbf{x}_i)$ (সাধারণ অবশিষ্ট ত্রুটি)।
- **শ্রিনকেজ / লার্নিং রেট ($\nu$):** ওভারফিটিং রুখতে প্রতি ট্রির অবদান সংকুচিত করা হয়:
  $$F_m(\mathbf{x}) = F_{m-1}(\mathbf{x}) + \nu \cdot h_m(\mathbf{x})$$

---

## ৭. আধুনিক বুস্টিং ট্রায়ো: XGBoost, LightGBM ও CatBoost

```
┌───────────────┬────────────────────────────┬─────────────────────────────┬───────────────────────────┐
│ বৈশিষ্ট্য      │ XGBoost                    │ LightGBM                    │ CatBoost                  │
├───────────────┼────────────────────────────┼─────────────────────────────┼───────────────────────────┤
│ ট্রি গ্রোথ    │ Level-wise (সুষম গভীরতা)    │ Leaf-wise (Best-first)      │ Symmetric Oblivious Trees │
│ স্প্লিট মেথড   │ Exact Greedy / Quantile    │ Histogram-based (256 Bins)  │ Histogram-based           │
│ বিশেষ শক্তি   │ ২য় অর্ডারের টেইলর অপ্টিমাইজেশন│ GOSS ও EFB (বিশাল ডেটায় দ্রুত)| ক্যাটাগরিকাল ও লিকেজ-ফ্রি  │
│ স্পিড         │ দ্রুত                      │ অত্যন্ত দ্রুত (Ultra Fast)  │ দ্রুত ও সেরা আউট-অব-বক্স  │
└───────────────┴────────────────────────────┴─────────────────────────────┴───────────────────────────┘
```

### ৭.১ XGBoost: ২য় অর্ডারের টেইলর সিরিজ ও স্প্লিট গেইন

লস ফাংশনের ২য় অর্ডারের টেইলর প্রসারণ:
$$\mathcal{L}^{(t)} \approx \sum_{i=1}^m \left[ g_i f_t(\mathbf{x}_i) + \frac{1}{2} h_i f_t^2(\mathbf{x}_i) \right] + \gamma T + \frac{1}{2}\lambda \sum_{j=1}^T w_j^2$$
যেখানে $g_i = \partial_{\hat{y}} L$ (গ্রেডিয়েন্ট) এবং $h_i = \partial_{\hat{y}}^2 L$ (হেসিয়ান)।

#### অপটিমাল লিফ ওয়েট ($w_j^*$) এবং স্প্লিট গেইন:
$$w_j^* = - \frac{\sum_{i \in I_j} g_i}{\sum_{i \in I_j} h_i + \lambda} = - \frac{G_j}{H_j + \lambda}$$
$$\text{Gain} = \frac{1}{2} \left[ \frac{G_L^2}{H_L + \lambda} + \frac{G_R^2}{H_R + \lambda} - \frac{(G_L + G_R)^2}{H_L + H_R + \lambda} \right] - \gamma$$

#### 🔢 হাতে-কলমে XGBoost স্প্লিট গেইন হিসাব:
ধরি রেগুলারাইজেশন প্যারামিটার $\lambda = 1.0$ এবং স্প্লিট পেনাল্টি $\gamma = 0.50$।
একটি নোডে মোট গ্রেডিয়েন্ট সমষ্টি $G = -4.0$, হেসিয়ান সমষ্টি $H = 8.0$।
নোডটিকে স্প্লিট করার পর:
- বাম চাইল্ড (Left): $G_L = -3.0, H_L = 4.0$
- ডান চাইল্ড (Right): $G_R = -1.0, H_R = 4.0$

1. **স্প্লিটের আগে পেরেন্ট স্কোর:**
   $$\text{Score}_{\text{Parent}} = \frac{G^2}{H + \lambda} = \frac{(-4.0)^2}{8.0 + 1.0} = \frac{16}{9} \approx 1.7778$$
2. **বাম ও ডান চাইল্ডের স্কোর:**
   $$\text{Score}_L = \frac{G_L^2}{H_L + \lambda} = \frac{(-3.0)^2}{4.0 + 1.0} = \frac{9}{5} = 1.8000$$
   $$\text{Score}_R = \frac{G_R^2}{H_R + \lambda} = \frac{(-1.0)^2}{4.0 + 1.0} = \frac{1}{5} = 0.2000$$
3. **স্প্লিট গেইন গণনা:**
   $$\text{Gain} = \frac{1}{2} \left[ \text{Score}_L + \text{Score}_R - \text{Score}_{\text{Parent}} \right] - \gamma$$
   $$\text{Gain} = \frac{1}{2} [1.8000 + 0.2000 - 1.7778] - 0.50 = \frac{1}{2}[0.2222] - 0.50 = 0.1111 - 0.50 = \mathbf{-0.3889}$$
- **ডিসিশন:** যেহেতু $\text{Gain} < 0$, তাই এই স্প্লিটটি বাতিল হবে (Pruned)! অতিরিক্ত জটিলতা বাড়ানোর চেয়ে নোডটি অবিভাজিত রাখা লাভজনক।

---

## ৮. ফিচার ইম্পর্ট্যান্সের সীমাবদ্ধতা

1. **MDI (Mean Decrease in Impurity / Gini Importance):**
   - *ত্রুটি:* যে ফিচারে কার্ডিনালিটি বেশি (যেমন ইউনিক আইডি বা প্রচুর ক্যাটাগরি) অথবা কন্টিনিউয়াস নয়েজ, ট্রি তাকে বেশি স্প্লিট করে কৃত্রিমভাবে শীর্ষ গুরুত্বপূর্ণ হিসেবে দেখায়।
2. **Permutation Feature Importance (Best Practice):**
   - ভ্যালিডেশন সেটের একটি নির্দিষ্ট ফিচারের মানগুলো র‍্যান্ডম শাফলিং করে দেখা হয় মেট্রিক কতটা ড্রপ করে। এটি সম্পূর্ণ আনবায়াসড।
3. **SHAP (SHapley Additive exPlanations):**
   - গেম থিওরির ওপর ভিত্তি করে প্রতিটি ফিচারের যোগাত্মক অবদান ($\phi_i$) নির্ণয় করে।

---

## ৯. স্ক্র্যাচ পাইথন ও Scikit-Learn প্রোডাকশন কোড

```python
"""
production_ensemble_pipeline.py
Scratch AdaBoost & Modern Boosting Comparison
"""
import numpy as np

class ScratchAdaBoostClassifier:
    def __init__(self, n_estimators: int = 10):
        self.n_estimators = n_estimators
        self.alphas = []
        self.stumps = []

    def fit(self, X: np.ndarray, y: np.ndarray):
        m, n = X.shape
        w = np.full(m, 1.0 / m)

        for _ in range(self.n_estimators):
            # সর্বোত্তম ১-লেভেল স্টাম্প খোঁজা (টয় ডেমো)
            best_stump = {'feature': 0, 'threshold': np.median(X[:, 0]), 'polarity': 1}
            preds = np.where(X[:, best_stump['feature']] >= best_stump['threshold'], 1, -1)
            
            # ভুলের হার গণনা
            misclassified = (preds != y)
            error = np.dot(w, misclassified) + 1e-10
            
            # লার্নার ওয়েট
            alpha = 0.5 * np.log((1.0 - error) / error)
            
            # স্যাম্পল ওয়েট আপডেট
            w *= np.exp(-alpha * y * preds)
            w /= np.sum(w)  # নরমালাইজ

            self.alphas.append(alpha)
            self.stumps.append(best_stump)
        return self

    def predict(self, X: np.ndarray) -> np.ndarray:
        stump_preds = [
            alpha * np.where(X[:, s['feature']] >= s['threshold'], 1, -1)
            for alpha, s in zip(self.alphas, self.stumps)
        ]
        return np.sign(np.sum(stump_preds, axis=0))

if __name__ == "__main__":
    X = np.array([[1.0], [2.0], [3.0], [4.0]])
    y = np.array([-1, -1, 1, 1])
    adaboost = ScratchAdaBoostClassifier(n_estimators=3).fit(X, y)
    print("AdaBoost Predictions:", adaboost.predict(X))
```
