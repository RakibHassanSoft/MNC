# অধ্যায় ৯: প্রোডাকশন এমএলঅপ্স ও বাস্তব কেস স্টাডিজ (MLOps & Production Engineering)

> *"In theory, there is no difference between theory and practice. In practice, there is." — Benjamin Brewster*

গবেষণাগার বা জুপিটার নোটবুকের মডেল আর রিয়েল-টাইম প্রোডাকশন সিস্টেমের মধ্যে পার্থক্য বিশাল। গুগল-এর বিখ্যাত গবেষণাপত্র অনুসারে, একটি সফল প্রোডাকশন সিস্টেমে আসল মেশিন লার্নিং কোড থাকে মাত্র ৫% থেকে ১০%; বাকি ৯০% অংশই হলো ডেটা পাইপলাইন, কনফিগারেশন, সার্ভিং এবং মনিটরিং অবকাঠামো।

---

## ৯.১ ডেটা লিকেজ (Data Leakage: The Silent Killer)

ডেটা লিকেজ হলো এমন একটি মারাত্মক ভুল যেখানে ট্রেনিংয়ের সময় এমন তথ্য মডেলের ভেতর ঢুকে পড়ে যা বাস্তবে প্রেডিকশনের সময় পাওয়ার কথা নয়। এর ফলে অফলাইন টেস্টে মডেল ৯৯% এক্যুরেসি দেয়, কিন্তু প্রোডাকশনে পুরোপুরি ব্যর্থ হয়।

### ৯.১.১ প্রধান প্রকারভেদ:
1. **টার্গেট লিকেজ (Target Leakage):**
   - উদাহরণ: নিউমোনিয়া প্রেডিক্ট করার মডেলে "অ্যান্টিবায়োটিক ইনজেকশন দেওয়া হয়েছে কি না" ফিচার রাখা। অ্যান্টিবায়োটিক দেওয়া হয় রোগ ধরা পড়ার পর; আগে নয়!
2. **ট্রেন-টেস্ট কন্টামিনেশন (Train-Test Contamination):**
   - ভুল পদ্ধতি: পুরো ডেটাসেটের ওপর `StandardScaler().fit_transform(X)` বা `SimpleImputer()` চালানো এবং তারপর `train_test_split()` করা।
   - সঠিক পদ্ধতি: সর্বদা প্রথমে স্প্লিট করতে হবে! শুধু `X_train`-এর ওপর `fit()` এবং সেই অনুমিত মান দিয়ে `X_test`-কে `transform()` করতে হবে:
     ```python
     # সঠিক নিয়ম
     scaler.fit(X_train)
     X_train_scaled = scaler.transform(X_train)
     X_test_scaled = scaler.transform(X_test)
     ```

---

## ৯.২ ডেটা ড্রিফট ও কনসেপ্ট ড্রিফট (Data Drift vs Concept Drift)

মডেল প্রোডাকশনে ডিপ্লয় করার পর সময়ের সাথে সাথে তার কার্যক্ষমতা হ্রাস পায় যাকে **মডেল ডিকে (Model Decay)** বলে।

```
1. Covariate Shift (Data Drift):
   P(X) পরিবর্তিত হয়, কিন্তু P(y|X) অপরিবর্তিত থাকে।
   (যেমন: নতুন বয়সের কাস্টমার প্ল্যাটফর্মে যুক্ত হওয়া)।

2. Concept Drift:
   P(y|X) পরিবর্তিত হয়।
   (যেমন: কোভিড-১৯ মহামারীর কারণে কেনাকাটার আচরণগত পরিবর্তন; ফ্রড প্রতারকদের নতুন কৌশল)।
```

### সনাক্তকরণ পদ্ধতি:
- **পপুলেশন স্ট্যাবিলিটি ইনডেক্স (PSI):**
  $$\text{PSI} = \sum \left( \text{Actual}_i - \text{Expected}_i \right) \times \ln\left( \frac{\text{Actual}_i}{\text{Expected}_i} \right)$$
  - $\text{PSI} < 0.1$: কোনো পরিবর্তন নেই।
  - $0.1 \le \text{PSI} \le 0.2$: মৃদু পরিবর্তন (সতর্কবার্তা)।
  - $\text{PSI} > 0.2$: মারাত্মক ড্রিফট; মডেল অবিলম্বে রি-ট্রেনিং আবশ্যক!

---

## ৯.৩ প্রোডাকশন এন্ড-টু-এন্ড প্রজেক্ট: ব্যালেন্সড ক্লাসিফিকেশন পাইপলাইন

```python
import numpy as np
import pandas as pd
import joblib
from sklearn.datasets import make_classification
from sklearn.model_selection import train_test_split, StratifiedKFold, RandomizedSearchCV
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler, RobustScaler
from sklearn.impute import SimpleImputer
from sklearn.ensemble import HistGradientBoostingClassifier
from sklearn.metrics import classification_report, roc_auc_score

# ১. বাস্তবধর্মী ভারসাম্যহীন (Imbalanced) ডেটাসেট তৈরি (১০,০০০ স্যাম্পল, ফ্রড মাত্র ৫%)
X_raw, y_raw = make_classification(
    n_samples=10000, n_features=10, n_informative=8,
    weights=[0.95, 0.05], random_state=42
)
df = pd.DataFrame(X_raw, columns=[f'feature_{i}' for i in range(10)])
df['target'] = y_raw

# কিছু মিসিং ভ্যালু ইনজেক্ট করা
df.iloc[np.random.choice(10000, 200), 0] = np.nan

# ২. ফিচার ও টার্গেট বিভক্তিকরণ
X = df.drop('target', axis=1)
y = df['target']

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

# ৩. রোবাস্ট পাইপলাইন তৈরি
num_cols = X.columns.tolist()

preprocessor = ColumnTransformer(
    transformers=[
        ('num', Pipeline([
            ('imputer', SimpleImputer(strategy='median')),
            ('scaler', RobustScaler())
        ]), num_cols)
    ]
)

# ক্লাস ওয়েটিং দিয়ে ভারসাম্যহীনতা সামলানো
model = HistGradientBoostingClassifier(
    class_weight='balanced',
    random_state=42
)

full_pipeline = Pipeline([
    ('preprocessor', preprocessor),
    ('classifier', model)
])

# ৪. হাইপারপ্যারামিটার টিউনিং (RandomizedSearchCV)
param_distributions = {
    'classifier__max_iter': [100, 200],
    'classifier__learning_rate': [0.01, 0.05, 0.1],
    'classifier__max_leaf_nodes': [15, 31, 63],
    'classifier__min_samples_leaf': [10, 20, 50]
}

cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)
random_search = RandomizedSearchCV(
    full_pipeline, param_distributions, n_iter=8,
    scoring='roc_auc', cv=cv, random_state=42, n_jobs=-1
)

print("মডেল ট্রেনিং ও টিউনিং চলছে...")
random_search.fit(X_train, y_train)

# ৫. টেস্ট সেটে চূড়ান্ত মূল্যায়ন
best_pipeline = random_search.best_estimator_
y_pred = best_pipeline.predict(X_test)
y_prob = best_pipeline.predict_proba(X_test)[:, 1]

print("\n===== টেস্ট ফলাফল =====")
print(classification_report(y_test, y_pred, target_names=['স্বাভাবিক', 'ফ্রড']))
print(f"Test ROC-AUC: {roc_auc_score(y_test, y_prob):.4f}")

# ৬. মডেল সিরিয়ালাইজেশন (Deployment-Ready Artifact)
model_path = "fraud_detection_production_model.joblib"
joblib.dump(best_pipeline, model_path)
print(f"\nমডেল সফলভাবে সেভ করা হয়েছে: {model_path}")

# ৭. প্রোডাকশন ইনফারেন্স টেস্ট
loaded_model = joblib.load(model_path)
sample_unseen_data = X_test.iloc[:2]
sample_pred = loaded_model.predict(sample_unseen_data)
sample_prob = loaded_model.predict_proba(sample_unseen_data)[:, 1]
print("অনসিন ডেটায় তাৎক্ষণিক প্রেডিকশন:", sample_pred)
print("ফ্রড হওয়ার সম্ভাবনা (%):", np.round(sample_prob * 100, 2))
```
