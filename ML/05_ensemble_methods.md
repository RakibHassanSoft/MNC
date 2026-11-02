# অধ্যায় ৫: এনসেম্বল লার্নিং ও অ্যাডভান্সড বুস্টিং (Ensemble Methods & Advanced Boosting)

> *"Wisdom of the Crowd: একটি একক জটিল মডেলের চেয়ে বহু সাধারণ মডেলের সম্মিলিত সিদ্ধান্ত প্রায় সবসময় বেশি নিখুঁত ও স্থিতিশীল হয়।"*

এনসেম্বল লার্নিং হলো আধুনিক মেশিন লার্নিং প্রতিযোগিতার (যেমন Kaggle) এবং ইন্ডাস্ট্রিয়াল টেবুলার ডেটার সবচেয়ে শক্তিশালী অস্ত্র।

---

## ৫.১ কেন এনসেম্বল কাজ করে? গাণিতিক প্রমাণ

### ৫.১.১ কনডরসেটের জুরি উপপাদ্য (Condorcet's Jury Theorem)
ধরি আমাদের কাছে $N$ টি স্বাধীন ক্লাসিফায়ার আছে, যাদের প্রত্যেকের সঠিক উত্তর দেওয়ার সম্ভাবনা $p > 0.5$।
সংখ্যাগরিষ্ঠ ভোটের মাধ্যমে সম্মিলিত সিদ্ধান্তের সঠিক হওয়ার সম্ভাবনা:
$$P_{majority} = \sum_{k=\lfloor N/2 \rfloor + 1}^N \binom{N}{k} p^k (1 - p)^{N - k}$$
যখন $N \to \infty$, তখন $P_{majority} \to 1$ (অর্থাৎ শতভাগ নিখুঁত সিদ্ধান্তের দিকে ধাবিত হয়!)।

### ৫.১.২ ভ্যারিয়্যান্স হ্রাসের সমীকরণ (Variance Reduction in Bagging)
ধরি $B$ সংখ্যক মডেলের প্রেডিকশন $X_1, X_2, \dots, X_B$, যাদের প্রত্যেকের ভ্যারিয়্যান্স $\sigma^2$ এবং জোড়ায় জোড়ায় পারস্পরিক কোরিলেশন $\rho$।
গড় প্রেডিকশনের ভ্যারিয়্যান্স:
$$\text{Var}\left( \frac{1}{B} \sum_{i=1}^B X_i \right) = \rho \sigma^2 + \frac{1 - \rho}{B} \sigma^2$$
- যদি মডেলগুলো সম্পূর্ণ স্বাধীন হয় ($\rho = 0$), তবে ভ্যারিয়্যান্স হয় $\frac{\sigma^2}{B}$। $B$ বৃদ্ধি পেলে ভ্যারিয়্যান্স শূন্যের কাছাকাছি চলে আসে!
- র‍্যান্ডম ফরেস্ট মূলত এই $\rho$-এর মান কমিয়ে মডেলের বৈচিত্র্য (Diversity) নিশ্চিত করে।

---

## ৫.২ ব্যাগিং ও র‍্যান্ডম ফরেস্ট (Bagging & Random Forest)

### ৫.২.১ আউট-অব-ব্যাগ এরর (Out-of-Bag - OOB Error)
বুটস্ট্র্যাপ স্যাম্পলিংয়ে মূল $N$ ডেটা থেকে রিপ্লেসমেন্টসহ $N$ টি ডেটা বাছাই করা হয়।
একটি নির্দিষ্ট ডেটা পয়েন্ট একবারও নির্বাচিত **না** হওয়ার সম্ভাবনা:
$$P(\text{Not selected}) = \left( 1 - \frac{1}{N} \right)^N$$
যখন $N \to \infty$, ক্যালকুলাসের সংজ্ঞানুসারে:
$$\lim_{N \to \infty} \left( 1 - \frac{1}{N} \right)^N = \frac{1}{e} \approx 0.368 = 36.8\%$$
অর্থাৎ প্রায় **৩৭% ডেটা** প্রতিটি ট্রির ট্রেনিংয়ে ব্যবহৃত হয় না! এই ডেটাকেই বলা হয় **OOB ডেটা**। এই ওওবি ডেটা দিয়ে মডেলের ভ্যালিডেশন এক্যুরেসি পরিমাপ করা যায়, যার ফলে পৃথক ক্রস-ভ্যালিডেশন সেট ছাড়াও নির্ভরযোগ্য মূল্যায়ন সম্ভব হয়।

---

## ৫.৩ বুস্টিং অ্যালগরিদম (Boosting Algorithms)

### ৫.৩.১ অ্যাডা বুস্ট (AdaBoost: Adaptive Boosting)
AdaBoost প্রতিটি ধাপে ভুল ক্লাসিফাইড ডেটার ওজন বাড়িয়ে দেয়।

```
Step 1: সম-ওজনযুক্ত ডেটা ──► Weak Tree 1 ──► ভুল পয়েন্টের ওজন বৃদ্ধি
Step 2: পরিবর্তিত ডেটা ──► Weak Tree 2 ──► পুনরায় ওজন সংশোধন
Final: প্রতিটি ট্রির পারফরম্যান্স ভিত্তিক ভারযুক্ত ভোট
```

#### গাণিতিক অ্যালগরিদম:
১. প্রতিটি নমুনার প্রাথমিক ওজন: $w_i = \frac{1}{m}, \quad i = 1, \dots, m$।
২. প্রতিটি রাউন্ড $t$-এর জন্য:
   - দুর্বল লার্নার $h_t(x)$ ট্রেন করা।
   - ভারযুক্ত এরর রেট নির্ণয়:
     $$\epsilon_t = \frac{\sum_{i: y_i \neq h_t(x_i)} w_i}{\sum_{i=1}^m w_i}$$
   - ক্লাসিফায়ারের গুরুত্ব ($\alpha_t$):
     $$\alpha_t = \frac{1}{2} \ln\left( \frac{1 - \epsilon_t}{\epsilon_t} \right)$$
     *(যদি $\epsilon_t < 0.5$ হয়, $\alpha_t > 0$; নিখুঁত হলে $\alpha_t \to \infty$)*।
   - ডেটার ওজন আপডেট:
     $$w_{i}^{(t+1)} = \frac{w_i^{(t)} \exp\left( -\alpha_t y_i h_t(x_i) \right)}{Z_t}$$
     যেখানে $Z_t$ হলো নরমালাইজেশন ফ্যাক্টর যাতে $\sum w_i = 1$ থাকে।

---

### ৫.৩.২ এক্সজি বুস্ট (XGBoost: Extreme Gradient Boosting Math)

XGBoost লস ফাংশনের **সেকেন্ড-অর্ডার টেলর সিরিজ এক্সপ্যানশন (Taylor Series Expansion)** ব্যবহার করে অপটিমাইজেশন সম্পাদন করে।

#### অবজেক্টিভ ফাংশন:
$$\mathcal{L}^{(t)} = \sum_{i=1}^n l\left(y_i, \hat{y}_i^{(t-1)} + f_t(\mathbf{x}_i)\right) + \Omega(f_t)$$
যেখানে রেগুলারাইজেশন টার্ম $\Omega(f_t) = \gamma T + \frac{1}{2} \lambda \sum_{j=1}^T w_j^2$ ($T$ হলো লিফ নোডের সংখ্যা এবং $w_j$ হলো লিফের ওজন)।

টেলর সম্প্রসারণ প্রয়োগ করে ($l(x+\Delta x) \approx l(x) + g \Delta x + \frac{1}{2} h (\Delta x)^2$):
$$\tilde{\mathcal{L}}^{(t)} \approx \sum_{i=1}^n \left[ g_i f_t(\mathbf{x}_i) + \frac{1}{2} h_i f_t^2(\mathbf{x}_i) \right] + \gamma T + \frac{1}{2} \lambda \sum_{j=1}^T w_j^2$$
যেখানে:
- প্রথম ক্রমের গ্রেডিয়েন্ট: $g_i = \partial_{\hat{y}^{(t-1)}} l(y_i, \hat{y}^{(t-1)})$
- দ্বিতীয় ক্রমের হেসিয়ান: $h_i = \partial^2_{\hat{y}^{(t-1)}} l(y_i, \hat{y}^{(t-1)})$

লিফ নোড $j$-এর অন্তর্গত নমুনা সেট $I_j$ হলে, সর্বোত্তম লিফ ওজন ($w_j^*$):
$$w_j^* = - \frac{\sum_{i \in I_j} g_i}{\sum_{i \in I_j} h_i + \lambda} = - \frac{G_j}{H_j + \lambda}$$

স্প্লিটের গুণগত মান বা **গেইন (Gain)** সমীকরণ:
$$\text{Gain} = \frac{1}{2} \left[ \frac{G_L^2}{H_L + \lambda} + \frac{G_R^2}{H_R + \lambda} - \frac{(G_L + G_R)^2}{H_L + H_R + \lambda} \right] - \gamma$$
*(যদি এই Gain ধনাত্মক না হয়, তবে স্প্লিটটি বাদ দেওয়া হয়—এটিকে বলে বিল্ট-ইন পোস্ট-প্রুনিং)*।

---

## ৫.৪ পাইথন কোড: আধুনিক এনসেম্বল মডেলস ও স্ট্যাকিং

```python
import numpy as np
from sklearn.datasets import make_classification
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier, StackingClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score

# সিন্থেটিক ক্লাসিফিকেশন ডেটা
X, y = make_classification(n_samples=2000, n_features=20, n_informative=15, random_state=42)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# ১. বেস লার্নারসমূহ
base_models = [
    ('rf', RandomForestClassifier(n_estimators=50, random_state=42)),
    ('gb', GradientBoostingClassifier(n_estimators=50, random_state=42))
]

# ২. মেটা লার্নার দিয়ে স্ট্যাকিং ক্লাসিফায়ার
stacking_clf = StackingClassifier(
    estimators=base_models,
    final_estimator=LogisticRegression(),
    cv=5 # ৫-ফোল্ড আউট-অব-ফোল্ড প্রেডিকশন (ডাটা লিকেজ রোধ করে)
)

stacking_clf.fit(X_train, y_train)
y_pred = stacking_clf.predict(X_test)
print(f"Stacking Ensemble Test Accuracy: {accuracy_score(y_test, y_pred) * 100:.2f}%")
```
