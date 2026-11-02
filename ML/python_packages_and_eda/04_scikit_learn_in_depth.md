# Scikit-Learn মাস্টার নোট: প্রোডাকশন পাইপলাইন ও মডেলিং (Scikit-Learn Mastery)

> **"Scikit-Learn-এর ডিজাইন আর্কিটেকচার বিশ্বের অন্যতম সেরা সফটওয়্যার ইঞ্জিনিয়ারিং প্যাটার্ন। এর ইউনিফাইড এপিআই (fit, transform, predict) পুরো মেশিন লার্নিং ওয়ার্কফ্লোকে সুশৃঙ্খল ও শক্তিশালী করে তোলে।"**

---

## ১. Scikit-Learn এর মূল আর্কিটেকচার ও এপিআই ডিজাইন

Scikit-Learn মূলত তিনটি প্রধান অবজেক্ট টাইপের ওপর প্রতিষ্ঠিত:

1. **এস্টিমেটর (Estimator):** যেকোনো অবজেক্ট যা ডেটা থেকে কিছু শেখে (`.fit(X, y)`)। যেমন: `LinearRegression`, `StandardScaler`।
2. **ট্রান্সফরমার (Transformer):** যা ডেটার গঠন বা মান রূপান্তর করে (`.transform(X)` অথবা `.fit_transform(X)`)। যেমন: `StandardScaler`, `OneHotEncoder`।
3. **প্রেডিক্টর (Predictor):** যা নতুন ডেটায় ফলাফল অনুমান করতে পারে (`.predict(X)`, `.predict_proba(X)`)।

```
Data (X, y) ──► Estimator.fit() ──► মডেল প্যারামিটার শেখে (যেমন w, b)
Data (X)    ──► Transformer.transform() ──► নতুন স্কেলড/এনকোডেড ডেটা (X_new)
Data (X)    ──► Predictor.predict() ──► পূর্বাভাস বা প্রেডিকশন (y_pred)
```

---

## ২. কলাম ট্রান্সফরমার ও পাইপলাইন (`ColumnTransformer` & `Pipeline`)

ডেটা লিকেজ রোধ করার এবং প্রোডাকশন-রেডি ক্লিন কোড লেখার একমাত্র ইন্ডাস্ট্রি স্ট্যান্ডার্ড উপায় হলো **পাইপলাইন**।

```python
import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.impute import SimpleImputer
from sklearn.ensemble import RandomForestClassifier

# ডেমো ডেটাসেট
df = pd.DataFrame({
    'age': [25, 32, np.nan, 45, 50, 23],
    'fare': [50.5, 100.2, 75.0, 250.0, 120.0, np.nan],
    'class_type': ['Economy', 'Business', 'Economy', 'First', 'Business', 'Economy'],
    'survived': [1, 0, 1, 0, 1, 0]
})

X = df.drop('survived', axis=1)
y = df['survived']

num_features = ['age', 'fare']
cat_features = ['class_type']

# ১. নিউমেরিক্যাল পাইপলাইন (ইম্পিউটেশন + স্কেলিং)
num_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())
])

# ২. ক্যাটাগরিকাল পাইপলাইন (ইম্পিউটেশন + ওয়ান-হট এনকোডিং)
cat_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy='most_frequent')),
    ('encoder', OneHotEncoder(drop='first', sparse_output=False, handle_unknown='ignore'))
])

# ৩. কলাম ট্রান্সফরমার দিয়ে দুটোকে একত্রিত করা
preprocessor = ColumnTransformer([
    ('num', num_pipeline, num_features),
    ('cat', cat_pipeline, cat_features)
])

# ৪. সম্পূর্ণ এন্ড-টু-এন্ড মডেল পাইপলাইন
full_model_pipeline = Pipeline([
    ('preprocessor', preprocessor),
    ('classifier', RandomForestClassifier(n_estimators=50, random_state=42))
])

# ট্রেনিং ও টেস্ট স্প্লিট
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.33, random_state=42)

# এক ক্লিকে ফিট! (কোনো ডাটা লিকেজ হওয়ার সুযোগ নেই)
full_model_pipeline.fit(X_train, y_train)

# এক ক্লিকে প্রেডিকশন
predictions = full_model_pipeline.predict(X_test)
print("Predictions:", predictions)
```

---

## ৩. কাস্টম ট্রান্সফরমার তৈরি (Custom Transformers)

বাস্তব প্রোজেক্টে আপনাকে প্রায়ই নিজস্ব বিজনেস লজিক দিয়ে ফিচার রূপান্তর করতে হয়। `BaseEstimator` এবং `TransformerMixin` ইনহেরিট করে তৈরি করা কাস্টম ক্লাস সরাসরি Scikit-Learn পাইপলাইনে যুক্ত করা যায়:

```python
from sklearn.base import BaseEstimator, TransformerMixin

class AgeIncomeRatioTransformer(BaseEstimator, TransformerMixin):
    def __init__(self, add_log=True):
        self.add_log = add_log

    def fit(self, X, y=None):
        return self # ফিট করার কিছু নেই, কেবল ট্রান্সফর্ম

    def transform(self, X):
        X_copy = X.copy()
        # বয়স ও আয়ের অনুপাত
        ratio = X_copy[:, 1] / (X_copy[:, 0] + 1e-5)
        if self.add_log:
            ratio = np.log1p(np.maximum(0, ratio))
        return np.c_[X_copy, ratio]
```

---

## ৪. হাইপারপ্যারামিটার টিউনিং: `GridSearchCV` বনাম `RandomizedSearchCV`

- **GridSearchCV:** সংজ্ঞায়িত সকল প্যারামিটারের প্রতিটি কম্বিনেশন পরীক্ষা করে। নিশ্চিত সেরা রেজাল্ট দেয় কিন্তু অনেক বেশি সময় নেয় ($O(\prod |params|)$)।
- **RandomizedSearchCV:** নির্দিষ্ট সংখ্যক ইটারেশনে দৈবচয়নভাবে প্যারামিটার বাছাই করে। বড় সার্চ স্পেসে ১০ গুণ কম সময়ে প্রায় একই মানের সেরা রেজাল্ট খুঁজে পায়।

```python
from sklearn.model_selection import RandomizedSearchCV

param_distributions = {
    'classifier__n_estimators': [50, 100, 200],
    'classifier__max_depth': [3, 5, 10, None],
    'classifier__min_samples_split': [2, 5, 10]
}

search = RandomizedSearchCV(
    estimator=full_model_pipeline,
    param_distributions=param_distributions,
    n_iter=5,
    cv=3,
    scoring='accuracy',
    random_state=42,
    n_jobs=-1
)
search.fit(X_train, y_train)
print("Best Parameters:", search.best_params_)
print(f"Best CV Score: {search.best_score_:.4f}")
```
