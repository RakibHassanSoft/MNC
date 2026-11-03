# মাস্টার গাইড: মেশিন লার্নিংয়ের জন্য ডেটা ভিজ্যুয়ালাইজেশন (Matplotlib & Seaborn Mastery)

> **"A picture is worth a thousand numbers. একটি চমৎকার গ্রাফ ডেটার জটিল মাল্টিডাইমেনশনাল প্যাটার্নকে মানুষের মস্তিষ্কে সরাসরি প্রতিফলিত করে।"**

---

## 📑 সম্পূর্ণ বিষয়সূচি (Table of Contents)
1. [Matplotlib-এর অবজেক্ট-ওরিয়েন্টেড (OO) আর্কিটেকচার বনাম স্টেটফুল Pyplot](#১-matplotlib-এর-অবজেক্ট-ওরিয়েন্টেড-oo-আর্কিটেকচার)
2. [Anatomy of a Figure: ক্যানভাস, অ্যাক্সেস, স্পাইনস ও লেআউট ম্যানেজমেন্ট](#২-anatomy-of-a-figure)
3. [মাল্টিপল সাবপ্লটস ও ডুয়াল এক্সিস (Subplots & Twinx Architecture)](#৩-মাল্টিপল-সাবপ্লটস-ও-ডুয়াল-এক্সিস)
4. [পরিসংখ্যানিক ভিজ্যুয়ালাইজেশনের সম্পূর্ণ গাইড (Seaborn Statistical Plots)](#৪-পরিসংখ্যানিক-ভিজ্যুয়ালাইজেশনের-সম্পূর্ণ-গাইড)
   - ৪.১ হিস্টোগ্রাম ও ECDF প্লট (কেন ECDF হিস্টোগ্রামের চেয়ে সেরা?)
   - ৪.২ বক্সপ্লট বনাম ভায়োলিন প্লট (Boxplot vs Violinplot)
   - ৪.৩ রিগ্রেশন ও জয়েন্টপ্লট (`regplot`, `jointplot`)
   - ৪.৪ কোরিলেশন হিটম্যাপ ও ট্রায়াঙ্গুলার মাস্কিং (Correlation Heatmap with Upper Mask)
5. [ভিজ্যুয়াল পারসেপশন প্রিন্সিপলস ও কালার প্যালেট নির্বাচন (Data-Ink Ratio & Color Theory)](#৫-ভিজ্যুয়াল-পারসেপশন-প্রিন্সিপলস-ও-কালার-প্যালেট-নির্বাচন)
6. [পাবলিকেশন-কোয়ালিটি ভেক্টর ফিগার এক্সপোর্ট (High-res 300 DPI SVG/PDF)](#৬-পাবলিকেশন-কোয়ালিটি-ভেক্টর-ফিগার-এক্সপোর্ট)

---

## ১. Matplotlib-এর অবজেক্ট-ওরিয়েন্টেড (OO) আর্কিটেকচার

মেশিন লার্নিং কোডে অনেক সময় `plt.plot()` লিখে গ্লোবাল স্টেট নষ্ট করা হয়। পেশাদার কোডে সর্বদা **Object-Oriented (OO) API** ব্যবহার করা উচিত:

```
            ┌──────────────────────────────────────────────┐
            │                  Figure (Canvas)             │
            │  ┌────────────────────┐ ┌──────────────────┐ │
            │  │      Axes 1        │ │      Axes 2      │ │
            │  │  (plot, title, x/y)│ │  (plot, title)   │ │
            │  └────────────────────┘ └──────────────────┘ │
            └──────────────────────────────────────────────┘
```

```python
import matplotlib.pyplot as plt
import numpy as np

# ১. Figure ও Axes আলাদা করা
fig, ax = plt.subplots(figsize=(8, 4.5), dpi=100)

x = np.linspace(-3, 3, 200)
y = 1 / (1 + np.exp(-x)) # সিগময়েড ফাংশন

# ২. Axes মেথডের মাধ্যমে প্লট
ax.plot(x, y, label=r'$\sigma(x) = \frac{1}{1 + e^{-x}}$', color='#1f77b4', lw=2.5)
ax.axhline(0.5, color='red', linestyle='--', alpha=0.7, label='Decision Boundary (0.5)')
ax.axvline(0, color='gray', linestyle=':', alpha=0.5)

# ৩. কাস্টমাইজেশন
ax.set_title("Logistic Sigmoid Activation Function", fontsize=13, fontweight='bold', pad=12)
ax.set_xlabel("Input Logit (z)", fontsize=11)
ax.set_ylabel("Probability P(Y=1)", fontsize=11)
ax.legend(frameon=True, facecolor='white', framealpha=0.9)
ax.grid(True, linestyle='--', alpha=0.4)

# স্পাইনস সুন্দর করা (টপ ও রাইট বর্ডার রিমুভ)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

plt.show()
```

---

## ২. Anatomy of a Figure

একটি Matplotlib চিত্রের প্রতিটি অংশের সুনির্দিষ্ট পরিচয়:
- **`Figure`:** পুরো চিত্র বা ক্যানভাস যেখানে সব প্লট আঁকা হয়।
- **`Axes`:** ভেতরের বাস্তব প্লট যেখানে ডেটা, টিকস, টাইটেল ও লেবেল আঁকা হয় (একটি ফিগারে একাধিক Axes থাকতে পারে)।
- **`Spines`:** প্লটের চারপাশের চারটি বর্ডার রেখা (top, bottom, left, right)।
- **`Ticks & TickLabels`:** অক্ষ বরাবর সংখ্যা এবং দাগ।

---

## ৩. মাল্টিপল সাবপ্লটস ও ডুয়াল এক্সিস (Twinx)

ট্রেইনিং লস ও অ্যাকুরেসি একই প্লটে দুটি ভিন্ন স্কেলে দেখানোর জন্য `twinx()` অপরিহার্য:

```python
epochs = np.arange(1, 21)
train_loss = 1.0 / np.sqrt(epochs) + np.random.normal(0, 0.02, 20)
accuracy = 0.5 + 0.45 * (1 - np.exp(-epochs / 5))

fig, ax1 = plt.subplots(figsize=(9, 4.5))

# অক্ষ ১: লস (বাম পাশে)
color = '#d62728'
ax1.set_xlabel('Epochs', fontsize=11)
ax1.set_ylabel('Training Loss', color=color, fontsize=11)
line1 = ax1.plot(epochs, train_loss, color=color, marker='o', label='Cross-Entropy Loss')
ax1.tick_params(axis='y', labelcolor=color)

# অক্ষ ২: অ্যাকুরেসি (ডান পাশে - Twinx)
ax2 = ax1.twinx()
color = '#2ca02c'
ax2.set_ylabel('Validation Accuracy', color=color, fontsize=11)
line2 = ax2.plot(epochs, accuracy, color=color, marker='s', linestyle='--', label='Accuracy')
ax2.tick_params(axis='y', labelcolor=color)

# উভয় লেজেন্ডকে একত্রিত করা
lines = line1 + line2
labels = [l.get_label() for l in lines]
ax1.legend(lines, labels, loc='center right')
ax1.set_title("Training Loss vs Accuracy Dual-Axis Tracking", fontsize=13, fontweight='bold')
plt.show()
```

---

## ৪. পরিসংখ্যানিক ভিজ্যুয়ালাইজেশনের সম্পূর্ণ গাইড (Seaborn)

### ৪.১ হিস্টোগ্রাম বনাম ECDF প্লট
হিস্টোগ্রামের বিন সাইজ বদলালে ডিস্ট্রিবিউশন সম্পূর্ণ ভিন্ন দেখাতে পারে (Binning Bias)। এর সেরা বিকল্প হলো **Empirical Cumulative Distribution Function (ECDF)**:

```python
import seaborn as sns

df = sns.load_dataset("tips")

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

# ১. হিস্টোগ্রাম + KDE
sns.histplot(data=df, x="total_bill", kde=True, ax=axes[0], color='#4c72b0')
axes[0].set_title("Histogram with KDE (Affected by Bin Width)")

# ২. ECDF প্লট (কোনো বিন বায়াস নেই!)
sns.ecdfplot(data=df, x="total_bill", ax=axes[1], color='#55a868', lw=2)
axes[1].axhline(0.8, color='red', linestyle='--', alpha=0.7)
axes[1].set_title("Empirical CDF (80% of bills are <= $28)")
plt.tight_layout()
plt.show()
```

---

### ৪.২ বক্সপ্লট বনাম ভায়োলিন প্লট (Boxplot vs Violinplot)
বক্সপ্লট কেবল ৫-নম্বর সামারি দেখায় কিন্তু বাইমডাল ডেটা ধরতে পারে না। ভায়োলিন প্লট কার্নেল ডেনসিটি যুক্ত করে ভেতরের পুরো গঠন ফুটিয়ে তোলে:

```python
fig, axes = plt.subplots(1, 2, figsize=(12, 4.5))

# বক্সপ্লট
sns.boxplot(data=df, x="day", y="total_bill", palette="Set2", ax=axes[0])
axes[0].set_title("Boxplot (Outliers & Quartiles)")

# ভায়োলিন প্লট
sns.violinplot(data=df, x="day", y="total_bill", palette="Set2", inner="quartile", ax=axes[1])
axes[1].set_title("Violinplot (Full Density Shape)")
plt.tight_layout()
plt.show()
```

---

### ৪.৩ কোরিলেশন হিটম্যাপ ও ট্রায়াঙ্গুলার মাস্কিং (Upper Triangle Mask)
একটি কোরিলেশন ম্যাট্রিক্সের উপরের এবং নিচের অংশ হুবহু এক। অপ্রয়োজনীয় ডুপ্লিকেট তথ্য দূর করতে আপার ট্রায়াঙ্গল মাস্ক করা হয়:

```python
numeric_df = df.select_dtypes(include=[np.number])
corr = numeric_df.corr()

# আপার ট্রায়াঙ্গল মাস্ক তৈরি
mask = np.triu(np.ones_like(corr, dtype=bool))

fig, ax = plt.subplots(figsize=(6, 5))
sns.heatmap(
    corr, 
    mask=mask, 
    annot=True, 
    fmt=".2f", 
    cmap="coolwarm", 
    vmin=-1, vmax=1, 
    linewidths=1, 
    cbar_kws={"shrink": 0.8},
    ax=ax
)
ax.set_title("Lower-Triangular Correlation Matrix", fontsize=12, fontweight='bold')
plt.show()
```

---

## ৫. ভিজ্যুয়াল পারসেপশন প্রিন্সিপলস ও কালার প্যালেট নির্বাচন

### এডওয়ার্ড টাফটির ডেটা-ইঙ্ক অনুপাত (Data-Ink Ratio):
$$\text{Data-Ink Ratio} = \frac{\text{ডেটা প্রদর্শনে ব্যবহৃত ইঙ্ক (Ink)}}{\text{মোট ব্যবহৃত গ্রাফিক ইঙ্ক}}$$
- অপ্রয়োজনীয় ব্যাকগ্রাউন্ড রঙ, থ্রি-ডি (3D) শ্যাডো, ঘন গ্রিডলাইন বাদ দিয়ে ডেটা-ইঙ্ক সর্বোচ্চ করুন।

### তিনটি কালার প্যালেট নীতি:
1. **Sequential (ক্রমিক):** একমুখী স্কেল (০ থেকে ১০০), যেমন: `Viridis`, `Blues`।
2. **Diverging (দ্বিমুখী):** মাঝখানে নিরপেক্ষ শূন্য, দুই পাশে দুটি চরম দিক (যেমন কোরিলেশন $-1$ থেকে $+1$), যেমন: `coolwarm`, `RdBu`।
3. **Qualitative (গুণবাচক):** কোনো ক্রম নেই এমন ক্যাটাগরি (যেমন পুরুষ/মহিলা, দেশ), যেমন: `Set2`, `tab10`।

---

## ৬. পাবলিকেশন-কোয়ালিটি ভেক্টর ফিগার এক্সপোর্ট

গবেষণাপত্র বা প্রেজেন্টেশনের জন্য ব্লারি পিএনজি না করে উচ্চ রেজোলিউশনের ভেক্টর ফরম্যাটে সেভ করুন:

```python
fig.savefig(
    "final_model_evaluation.pdf",  # অথবা .svg / .png
    dpi=300,                       # প্রিন্ট কোয়ালিটি রেজোলিউশন
    bbox_inches='tight',           # বাড়তি চারপাশের সাদা মার্জিন ছাঁটাই
    transparent=False
)
```
