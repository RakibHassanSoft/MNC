# Matplotlib ও Seaborn মাস্টার নোট: মেশিন লার্নিং ভিজ্যুয়ালাইজেশন (Data Visualization Mastery)

> **"A picture is worth a thousand numbers. একটি চমৎকার গ্রাফ জটিল গাণিতিক প্যাটার্নকে নিমিষেই মানুষের চোখে দৃশ্যমান করে তোলে।"**

মেশিন লার্নিংয়ে ভিজ্যুয়ালাইজেশনের তিনটি মূল উদ্দেশ্য থাকে:
1. **এক্সপ্লোরেটরি অ্যানালাইসিস (EDA):** ডেটার বিন্যাস, স্কিউনেস ও আউটলায়ার শনাক্ত করা।
2. **মডেল ট্রেনিং ডায়াগনস্টিকস:** লার্নিং কার্ভ ও কনভার্জেন্স পর্যবেক্ষণ করা।
3. **ফলাফল মূল্যায়ন:** কনফিউশন ম্যাট্রিক্স, আরওসি (ROC) কার্ভ ও ফিচার গুরুত্ব তুলে ধরা।

---

## ১. Matplotlib-এর অবজেক্ট ওরিয়েন্টেড (OO) ইন্টারফেস

অনেকে ভুল করে `plt.plot()` ব্যবহার করে কোড জগাখিচুড়ি করে ফেলেন। প্রোফেশনাল ডেটা সায়েন্টিস্টরা সর্বদা **Object-Oriented (OO) API** ব্যবহার করেন:

```python
import matplotlib.pyplot as plt
import numpy as np

# ক্যানভাস (Figure) এবং অক্ষ (Axes) আলাদা করা
fig, ax = plt.subplots(figsize=(8, 5), dpi=100)

x = np.linspace(0, 10, 100)
y1 = np.sin(x)
y2 = np.cos(x)

# প্লটিং
ax.plot(x, y1, label='Sin(x)', color='#2b5c8f', linewidth=2, linestyle='-')
ax.plot(x, y2, label='Cos(x)', color='#e05d44', linewidth=2, linestyle='--')

# কাস্টমাইজেশন
ax.set_title("Trigonometric Functions in ML", fontsize=14, fontweight='bold', pad=10)
ax.set_xlabel("Angle (Radians)", fontsize=11)
ax.set_ylabel("Amplitude", fontsize=11)
ax.legend(loc='upper right', frameon=True)
ax.grid(True, linestyle=':', alpha=0.6)

# এক্সিস স্পাইন সুন্দর করা
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

plt.show()
```

---

## ২. Seaborn: পরিসংখ্যানিক ডেটা ভিজ্যুয়ালাইজেশন

Seaborn মূলত Pandas ডেটাফ্রেমের ওপর অত্যন্ত মসৃণভাবে পরিসংখ্যানিক গ্রাফ তৈরি করে।

```python
import seaborn as sns
import pandas as pd

# স্টাইল সেট করা
sns.set_theme(style="whitegrid", palette="muted")

# ডেমো ডেটাসেট
df = sns.load_dataset("penguins")

# ১. ডিস্ট্রিবিউশন প্লট (KDE সহ হিস্টোগ্রাম)
fig, ax = plt.subplots(figsize=(7, 4))
sns.histplot(data=df, x="flipper_length_mm", hue="species", kde=True, ax=ax)
ax.set_title("Flipper Length Distribution by Species")
plt.show()

# ২. বক্সপ্লট (আউটলায়ার ও কোয়ার্টাইল দেখতে)
fig, ax = plt.subplots(figsize=(7, 4))
sns.boxplot(data=df, x="species", y="body_mass_g", ax=ax, palette="Set2")
ax.set_title("Body Mass Distribution (Detecting Outliers)")
plt.show()

# ৩. কোরিলেশন হিটম্যাপ (মাল্টিকোলিনিয়ারিটি চেক)
fig, ax = plt.subplots(figsize=(6, 5))
numeric_df = df.select_dtypes(include=[np.number])
corr_matrix = numeric_df.corr()
sns.heatmap(corr_matrix, annot=True, cmap="coolwarm", fmt=".2f", linewidths=0.5, ax=ax)
ax.set_title("Feature Correlation Heatmap")
plt.show()
```

---

## ৩. মেশিন লার্নিং স্পেসিফিক প্লটস (ML Diagnostic Visualizations)

### ৩.১ লার্নিং ও লস কার্ভ (Loss Curve)
মডেল ওভারফিটিং হচ্ছে নাকি আন্ডারফিটিং হচ্ছে তা বোঝার সেরা উপায়:

```python
def plot_learning_curves(train_losses, val_losses):
    epochs = range(1, len(train_losses) + 1)
    fig, ax = plt.subplots(figsize=(8, 4))
    ax.plot(epochs, train_losses, 'b-o', label='Training Loss', markersize=4)
    ax.plot(epochs, val_losses, 'r-s', label='Validation Loss', markersize=4)
    ax.set_title("Training vs Validation Loss (Overfitting Diagnostic)")
    ax.set_xlabel("Epochs")
    ax.set_ylabel("Loss")
    ax.legend()
    ax.grid(True, alpha=0.3)
    plt.show()
```

### ৩.২ কনফিউশন ম্যাট্রিক্স হিটম্যাপ (Confusion Matrix)
```python
from sklearn.metrics import confusion_matrix

def plot_confusion_matrix_custom(y_true, y_pred, class_names):
    cm = confusion_matrix(y_true, y_pred)
    fig, ax = plt.subplots(figsize=(5, 4))
    sns.heatmap(cm, annot=True, fmt='d', cmap='Blues',
                xticklabels=class_names, yticklabels=class_names, ax=ax)
    ax.set_ylabel('Actual Label')
    ax.set_xlabel('Predicted Label')
    ax.set_title('Confusion Matrix')
    plt.show()
```
