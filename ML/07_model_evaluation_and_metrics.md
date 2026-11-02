# অধ্যায় ৭: মডেল মূল্যায়ন ও অপটিমাইজেশন মেট্রিক্স (Model Evaluation & Performance Metrics)

> *"When a measure becomes a target, it ceases to be a good measure." — Goodhart's Law*

একটি মেশিন লার্নিং মডেল সফল কি না তা যাচাই করার পদ্ধতি যদি সঠিক না হয়, তবে পুরো সিস্টেমটি ব্যর্থ হতে পারে। এই অধ্যায়ে আমরা শিখব কীভাবে গাণিতিক ও পরিসংখ্যানগতভাবে মডেল মূল্যায়ন করতে হয়।

---

## ৭.১ ক্লাসিফিকেশন মেট্রিক্স (Classification Metrics)

### ৭.১.১ কনফিউশন ম্যাট্রিক্স ও ত্রুটির প্রকারভেদ

```
                     বাস্তবে পজিটিভ (Actual +)      বাস্তবে নেগেটিভ (Actual -)
প্রেডিক্ট পজিটিভ (+)   True Positive (TP)             False Positive (FP - Type I Error)
প্রেডিক্ট নেগেটিভ (-)  False Negative (FN - Type II)  True Negative (TN)
```

- **টাইপ ১ এরর (Type I Error / False Alarm):** নির্দোষ ব্যক্তিকে অপরাধী ঘোষণা করা (False Positive)।
- **টাইপ ২ এরর (Type II Error / Miss):** একজন ক্যান্সার রোগীকে সুস্থ বলে ছেড়ে দেওয়া (False Negative)।

---

### ৭.১.২ প্রধান পারফরম্যান্স পরিমাপসমূহ

1. **অ্যাকিউরেসি প্যারাডক্স (Accuracy Paradox):**
   $$\text{Accuracy} = \frac{TP + TN}{TP + TN + FP + FN}$$
   - যদি ১ লক্ষ ক্রেডিট কার্ড ট্রানজ্যাকশনের মধ্যে মাত্র ১০০টি ফ্রড (০.১%) হয়, তবে একটি ডামি মডেল যা সবসময় "স্বাভাবিক" বলবে, সেটি ৯৯.৯% এক্যুরেসি পাবে! কিন্তু মডেলটি বাস্তবে কোনো ফ্রডই ধরতে পারেনি।
2. **প্রিসিশন (Precision / Positive Predictive Value):**
   $$\text{Precision} = \frac{TP}{TP + FP}$$
   - যখন মিথ্যা সতর্কবার্তার (False Alarm) মূল্য অত্যন্ত চড়া (যেমন: ইউটিউব ভিডিও রিকমেন্ডেশন বা স্প্যাম ফিল্টার)।
3. **রিকল বা সেনসিটিভিটি (Recall / Sensitivity / True Positive Rate):**
   $$\text{Recall} = \frac{TP}{TP + FN}$$
   - যখন কোনো পজিটিভ মিস করা প্রাণঘাতী হতে পারে (যেমন: রোগ নির্ণয় বা বিমান নিরাপত্তার অ্যালার্ম)।
4. **এফ-বিটা পরিমাপ ($F_\beta$-Score):**
   $$F_\beta = (1 + \beta^2) \frac{\text{Precision} \cdot \text{Recall}}{\beta^2 \text{Precision} + \text{Recall}}$$
   - $\beta = 1$: সাধারণ $F_1$ স্কোর (প্রিসিশন ও রিকলের হারমোনিক গড়)।
   - $\beta = 2$: রিকলকে প্রিসিশনের চেয়ে দ্বিগুণ গুরুত্ব দেওয়া হয় (মেডিকেল ডায়াগনস্টিক)।
   - $\beta = 0.5$: প্রিসিশনকে রিকলের চেয়ে দ্বিগুণ গুরুত্ব দেওয়া হয়।

---

### ৭.১.৩ ম্যাথিউস কোরিলেশন কোফিশিয়েন্ট (MCC - Matthews Correlation Coefficient)
ইমব্যালান্সড ডেটায় $F_1$ স্কোরের চেয়েও নির্ভরযোগ্য একক মেট্রিক হলো MCC:
$$\text{MCC} = \frac{TP \times TN - FP \times FN}{\sqrt{(TP + FP)(TP + FN)(TN + FP)(TN + FN)}} \in [-1, +1]$$
- $+1$: নিখুঁত প্রেডিকশন
- $0$: সম্পূর্ণ দৈবচয়ন বা র‍্যান্ডম গেস
- $-1$: পুরোপুরি ভুল প্রেডিকশন

---

## ৭.২ রিগ্রেশন মেট্রিক্স (Regression Metrics)

1. **গড় পরম ত্রুটি (MAE):**
   $$\text{MAE} = \frac{1}{m} \sum_{i=1}^m |y_i - \hat{y}_i|$$
   - আউটলায়ারের প্রতি অত্যন্ত রোবাস্ট।
2. **রুট মিন স্কয়ার্ড এরর (RMSE):**
   $$\text{RMSE} = \sqrt{\frac{1}{m} \sum_{i=1}^m (y_i - \hat{y}_i)^2}$$
   - বড় ভুলের ওপর অতিরিক্ত পেনাল্টি আরোপ করে।
3. **এডজাস্টেড $R^2$ (Adjusted $R^2$):**
   সাধারণ $R^2$ মডেলে যে কোনো ফালতু ফিচার যোগ করলেও কখনোই কমে না (হয় বাড়ে নয় স্থির থাকে)। তাই ফিচার সংখ্যার পেনাল্টিসহ এডজাস্টেড $R^2$ ব্যবহৃত হয়:
   $$R^2_{adj} = 1 - \left[ \frac{(1 - R^2)(m - 1)}{m - p - 1} \right]$$
   যেখানে $m$ হলো স্যাম্পল সংখ্যা এবং $p$ হলো ফিচার সংখ্যা। নতুন ফিচার মডেল উন্নত না করলে $R^2_{adj}$ হ্রাস পায়।

---

## ৭.৩ বায়াস-ভ্যারিয়্যান্স ডিকম্পোজিশনের গাণিতিক প্রমাণ (Bias-Variance Proof)

ধরি ট্রু ডেটা জেনারেটিং প্রসেস: $y = f(\mathbf{x}) + \epsilon$, যেখানে নয়েজের গড় $\mathbb{E}[\epsilon] = 0$ এবং ভ্যারিয়্যান্স $\text{Var}(\epsilon) = \sigma^2$।
একটি প্রশিক্ষিত মডেল $\hat{f}(\mathbf{x})$-এর প্রত্যাশিত বর্গের এরর (Expected Mean Squared Error):
$$\mathbb{E}\left[ (y - \hat{f}(\mathbf{x}))^2 \right]$$

$y$-এর মান বসিয়ে পাই:
$$\mathbb{E}\left[ (f(\mathbf{x}) + \epsilon - \hat{f}(\mathbf{x}))^2 \right] = \mathbb{E}\left[ ((f(\mathbf{x}) - \hat{f}(\mathbf{x})) + \epsilon)^2 \right]$$
$$= \mathbb{E}\left[ (f(\mathbf{x}) - \hat{f}(\mathbf{x}))^2 \right] + 2 \mathbb{E}[(f(\mathbf{x}) - \hat{f}(\mathbf{x})) \epsilon] + \mathbb{E}[\epsilon^2]$$

যেহেতু নয়েজ $\epsilon$ মডেল ও ডেটা থেকে সম্পূর্ণ স্বাধীন এবং $\mathbb{E}[\epsilon]=0$:
$$2 \mathbb{E}[(f(\mathbf{x}) - \hat{f}(\mathbf{x})) \epsilon] = 0 \quad \text{এবং} \quad \mathbb{E}[\epsilon^2] = \sigma^2$$

সুতরাং:
$$\mathbb{E}\left[ (y - \hat{f}(\mathbf{x}))^2 \right] = \mathbb{E}\left[ (f(\mathbf{x}) - \hat{f}(\mathbf{x}))^2 \right] + \sigma^2$$

এখন প্রথম পদে মডেলের প্রত্যাশিত মান $\mathbb{E}[\hat{f}]$ যোগ ও বিয়োগ করি:
$$f(\mathbf{x}) - \hat{f}(\mathbf{x}) = (f(\mathbf{x}) - \mathbb{E}[\hat{f}(\mathbf{x})]) + (\mathbb{E}[\hat{f}(\mathbf{x})] - \hat{f}(\mathbf{x}))$$

বর্গ করে প্রত্যাশা নিলে ক্রস-টার্ম শূন্য হয়ে যায়:
$$\mathbb{E}\left[ (f(\mathbf{x}) - \hat{f}(\mathbf{x}))^2 \right] = \underbrace{(f(\mathbf{x}) - \mathbb{E}[\hat{f}(\mathbf{x})])^2}_{\text{Bias}^2} + \underbrace{\mathbb{E}\left[ (\hat{f}(\mathbf{x}) - \mathbb{E}[\hat{f}(\mathbf{x})])^2 \right]}_{\text{Variance}}$$

অতএব চূড়ান্ত রূপ:
$$\mathbb{E}\left[ (y - \hat{f}(\mathbf{x}))^2 \right] = \text{Bias}^2 + \text{Variance} + \sigma^2$$

- **Bias:** মডেলের অনুমানের গড় এবং আসল সত্যের মধ্যবর্তী পার্থক্য (আন্ডারফিটিং নির্দেশ করে)।
- **Variance:** ভিন্ন ভিন্ন ট্রেনিং সেটের কারণে প্রেডিকশনের তারতম্য (ওভারফিটিং নির্দেশ করে)।
- $\sigma^2$: অপ্রতিরোধ্য নয়েজ (Irreducible Error - কোনো মডেলই এটি কমাতে পারে না)।

*(প্রমাণিত)*

---

## ৭.৪ পাইথন কোড: সম্পূর্ণ পারফরম্যান্স ইভ্যালুয়েশন ড্যাশবোর্ড

```python
import numpy as np
from sklearn.metrics import confusion_matrix, classification_report, roc_auc_score, matthews_corrcoef

def evaluate_classifier_performance(y_true, y_pred, y_prob):
    cm = confusion_matrix(y_true, y_pred)
    tn, fp, fn, tp = cm.ravel()
    
    accuracy = (tp + tn) / (tp + tn + fp + fn)
    precision = tp / (tp + fp) if (tp + fp) > 0 else 0
    recall = tp / (tp + fn) if (tp + fn) > 0 else 0
    f1 = 2 * (precision * recall) / (precision + recall) if (precision + recall) > 0 else 0
    mcc = matthews_corrcoef(y_true, y_pred)
    auc = roc_auc_score(y_true, y_prob)

    print("===== মডেল মূল্যায়ন রিপোর্ট =====")
    print(f"Confusion Matrix:\n{cm}")
    print(f"Accuracy:  {accuracy * 100:.2f}%")
    print(f"Precision: {precision:.4f}")
    print(f"Recall:    {recall:.4f}")
    print(f"F1-Score:  {f1:.4f}")
    print(f"MCC Score: {mcc:.4f}")
    print(f"ROC-AUC:   {auc:.4f}")

# টেস্ট
if __name__ == "__main__":
    y_true = np.array([0, 0, 0, 0, 1, 1, 1, 1])
    y_pred = np.array([0, 0, 1, 0, 1, 1, 0, 1])
    y_prob = np.array([0.1, 0.2, 0.6, 0.3, 0.8, 0.9, 0.4, 0.85])
    evaluate_classifier_performance(y_true, y_pred, y_prob)
```
