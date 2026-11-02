# 🐍 মেশিন লার্নিং পাইথন প্যাকেজ ও এক্সপ্লোরেটরি ডেটা অ্যানালাইসিস (EDA) মাস্টার গাইড

> **"ডেটা সায়েন্স এবং মেশিন লার্নিংয়ের ভিত্তিপ্রস্তর হলো সঠিক পাইথন লাইব্রেরির ওপর গভীর দখল এবং ডেটা থেকে অর্থবহ অন্তর্দৃষ্টি (Insights) বের করে আনার দক্ষতা।"**

এই ডিরেক্টরিটি বিশেষভাবে তৈরি করা হয়েছে যাতে আপনি মেশিন লার্নিংয়ের জন্য অপরিহার্য প্রতিটি পাইথন লাইব্রেরি—**NumPy**, **Pandas**, **Matplotlib**, **Seaborn**, এবং **Scikit-Learn**—পুঙ্খানুপুঙ্খভাবে হাতে-কলমে শিখতে পারেন। একই সাথে এখানে রয়েছে বাংলায় সম্পূর্ণ বাস্তবমুখী **এক্সপ্লোরেটরি ডেটা অ্যানালাইসিস (EDA) গাইডলাইন**।

---

## 📂 ফাইল ও নোটবুক সূচিপত্র (Contents & Colab Notebooks)

প্রতিটি টপিকের জন্য বিস্তারিত **রিডিং নোট (.md)** এবং সরাসরি গুগুল কোলাব (Google Colab) ও জুপিটার নোটবুকে রানযোগ্য **ইন্টারেক্টিভ নোটবুক (.ipynb)** দেওয়া হয়েছে:

| ক্রম | বিষয় ও লাইব্রেরি | কেন শিখবেন এবং এমএল-এ এর প্রয়োগ | ইন্টারেক্টিভ নোটবুক (.ipynb) | বিস্তারিত রিডিং নোট (.md) |
| :---: | :--- | :--- | :---: | :---: |
| **১** | **NumPy Mastery** | ভেক্টরাইজেশন, ব্রডকাস্টিং, ম্যাট্রিক্স অপারেশন, লিনালগ (Linear Algebra) | [👉 NumPy Notebook](file:///g:/MNC/ML/python_packages_and_eda/01_numpy_in_depth.ipynb) | [📖 NumPy Guide](file:///g:/MNC/ML/python_packages_and_eda/01_numpy_in_depth.md) |
| **২** | **Pandas Mastery** | ডেটা লোডিং, ক্লিনিং, গ্রুপিং (`groupby`), ফিল্টারিং (`loc/iloc`), মার্জিং | [👉 Pandas Notebook](file:///g:/MNC/ML/python_packages_and_eda/02_pandas_in_depth.ipynb) | [📖 Pandas Guide](file:///g:/MNC/ML/python_packages_and_eda/02_pandas_in_depth.md) |
| **৩** | **Matplotlib & Seaborn** | ডেটা ভিজ্যুয়ালাইজেশন, ডিস্ট্রিবিউশন প্লট, হিটম্যাপ, পেয়ারপ্লট, সাবপ্লট | [👉 Visualization Notebook](file:///g:/MNC/ML/python_packages_and_eda/03_matplotlib_and_seaborn_in_depth.ipynb) | [📖 Viz Guide](file:///g:/MNC/ML/python_packages_and_eda/03_matplotlib_and_seaborn_in_depth.md) |
| **৪** | **Scikit-Learn Mastery** | প্রিপ্রসেসিং পাইপলাইন, মডেল সিলেকশন, ক্রস-ভ্যালিডেশন, হাইপারপ্যারামিটার টিউনিং | [👉 Sklearn Notebook](file:///g:/MNC/ML/python_packages_and_eda/04_scikit_learn_in_depth.ipynb) | [📖 Sklearn Guide](file:///g:/MNC/ML/python_packages_and_eda/04_scikit_learn_in_depth.md) |
| **৫** | **পূর্ণাঙ্গ ডেটা অ্যানালাইসিস (EDA)** | ডেটা প্রোফাইলিং, ইউনিভ্যারিয়েট/বাইভ্যারিয়েট অ্যানালাইসিস, আউটলায়ার, VIF | [👉 Complete EDA Notebook](file:///g:/MNC/ML/python_packages_and_eda/05_complete_eda_data_analysis_guide.ipynb) | [📖 EDA Master Guide](file:///g:/MNC/ML/python_packages_and_eda/05_complete_eda_data_analysis_guide.md) |

---

## 🛠️ মেশিন লার্নিং পাইথন ইকোসিস্টেম আর্কিটেকচার

```
                          ┌─────────────────────────────────────┐
                          │     Scikit-Learn / PyTorch / TF     │ ──► Model Training & Tuning
                          └──────────────────┬──────────────────┘
                                             │
                          ┌──────────────────┴──────────────────┐
                          │          Pandas DataFrames          │ ──► Data Manipulation & Cleaning
                          └──────────────────┬──────────────────┘
                                             │
                          ┌──────────────────┴──────────────────┐
                          │            NumPy ndarrays           │ ──► Vectorized Math & Matrix Ops
                          └──────────────────┬──────────────────┘
                                             │
                     ┌───────────────────────┴───────────────────────┐
                     ▼                                               ▼
         ┌───────────────────────┐                       ┌───────────────────────┐
         │      Matplotlib       │                       │        Seaborn        │
         │  (Low-level Plotting) │                       │ (Statistical Graphics)│
         └───────────────────────┘                       └───────────────────────┘
```

---

## 🚀 গুগুল কোলাবে কীভাবে রান করবেন (How to run in Google Colab)

1. যে কোনো `.ipynb` ফাইল সরাসরি [Google Colab](https://colab.research.google.com)-এ গিয়ে `Upload Notebook` অপশনে আপলোড করুন।
2. অথবা VS Code-এ সরাসরি `.ipynb` ফাইলটি ওপেন করে Python ও Jupyter এক্সটেনশন দিয়ে রান করুন।
3. প্রতিটি কোড ব্লকে বিস্তারিত বাংলায় কমেন্ট এবং আউটপুট ব্যাখ্যা যুক্ত আছে।
