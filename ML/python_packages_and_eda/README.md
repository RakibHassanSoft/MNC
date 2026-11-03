# 🐍 মেশিন লার্নিং পাইথন প্যাকেজ, SQL ও এক্সপ্লোরেটরি ডেটা অ্যানালাইসিস (EDA) মাস্টার গাইড

> **"ডেটা সায়েন্স এবং মেশিন লার্নিংয়ের ভিত্তিপ্রস্তর হলো সঠিক পাইথন সফটওয়্যার ইঞ্জিনিয়ারিং, ডেটাবেজ লেভেলে SQL ফিচার পাইপলাইনিং, লাইব্রেরির ওপর গভীর দখল এবং ডেটা থেকে অর্থবহ অন্তর্দৃষ্টি (Insights) বের করে আনার দক্ষতা।"**

এই ডিরেক্টরিটি বিশেষভাবে তৈরি করা হয়েছে যাতে আপনি প্রফেশনাল মেশিন লার্নিং ইঞ্জিনিয়ার রোডম্যাপের **ফেইজ ১ (Python & Software Engineering Foundations)** এবং **ফেইজ ৩ (Data Analysis, Visualization, and SQL)** পুঙ্খানুপুঙ্খভাবে হাতে-কলমে ও গাণিতিক প্রমাণের সাথে শিখতে পারেন।

---

## 📂 ফাইল ও রোডম্যাপ সূচিপত্র (Roadmap Phase Mapping & Guides)

| ক্রম | বিষয় ও লাইব্রেরি | রোডম্যাপ ফেইজ | কেন শিখবেন এবং এমএল-এ এর প্রয়োগ | ইন্টারেক্টিভ নোটবুক | বিস্তারিত রিডিং নোট (.md) |
| :---: | :--- | :---: | :--- | :---: | :---: |
| **০** | **Python & SE Foundations** | **Phase 1** | মেমরি মডেল, OOP, টাইপিং, জেনারেটর, pytest, লগিং, প্রোডাকশন CLI টুল | — | [📖 Core Python & SE Guide](file:///g:/MNC/ML/python_packages_and_eda/00_python_software_engineering_foundations.md) |
| **১** | **NumPy Mastery** | **Phase 3** | মেমরি স্ট্রাইডস, ভেক্টরাইজেশন, ব্রডকাস্টিং, LogSumExp ট্রিক, OLS নরমাল সমীকরণ | [👉 NumPy Notebook](file:///g:/MNC/ML/python_packages_and_eda/01_numpy_in_depth.ipynb) | [📖 NumPy Guide](file:///g:/MNC/ML/python_packages_and_eda/01_numpy_in_depth.md) |
| **২** | **Pandas Mastery** | **Phase 3** | মেমরি ডাউনকাস্টিং (৮০% মেমরি সেভ), Parquet প্রেডিকেট পুশডাউন, GroupBy `transform` | [👉 Pandas Notebook](file:///g:/MNC/ML/python_packages_and_eda/02_pandas_in_depth.ipynb) | [📖 Pandas Guide](file:///g:/MNC/ML/python_packages_and_eda/02_pandas_in_depth.md) |
| **৩** | **Matplotlib & Seaborn** | **Phase 3** | অবজেক্ট-ওরিয়েন্টেড (OO) API, ডুয়াল এক্সিস `twinx`, ECDF প্লট, কোরিলেশন মাস্কিং | [👉 Visualization Notebook](file:///g:/MNC/ML/python_packages_and_eda/03_matplotlib_and_seaborn_in_depth.ipynb) | [📖 Viz Guide](file:///g:/MNC/ML/python_packages_and_eda/03_matplotlib_and_seaborn_in_depth.md) |
| **৪** | **Scikit-Learn Mastery** | **Phase 4** | প্রিপ্রসেসিং পাইপলাইন, ColumnTransformer, CV স্ট্র্যাটেজি, হাইপারপ্যারামিটার টিউনিং | [👉 Sklearn Notebook](file:///g:/MNC/ML/python_packages_and_eda/04_scikit_learn_in_depth.ipynb) | [📖 Sklearn Guide](file:///g:/MNC/ML/python_packages_and_eda/04_scikit_learn_in_depth.md) |
| **৫** | **কমপ্লিট ডেটা অ্যানালাইসিস (EDA)** | **Phase 3** | স্কিউনেস, কার্টোসিস, পিয়ারসন $r$, স্পিয়ারম্যান $\rho$, কাই-স্কয়ার $\chi^2$, VIF, ১০-ফাইন্ডিং রিপোর্ট | [👉 Complete EDA Notebook](file:///g:/MNC/ML/python_packages_and_eda/05_complete_eda_data_analysis_guide.ipynb) | [📖 EDA Master Guide](file:///g:/MNC/ML/python_packages_and_eda/05_complete_eda_data_analysis_guide.md) |
| **৬** | **SQL for Data Analysis & ML** | **Phase 3** | উইন্ডো ফাংশন (`ROW_NUMBER`, `RANK`, `DENSE_RANK`), CTEs, রোলিং উইন্ডো, RFM স্কোরিং | — | [📖 SQL for ML Guide](file:///g:/MNC/ML/python_packages_and_eda/06_sql_for_data_analysis_and_ml.md) |

---

## 🛠️ মেশিন লার্নিং ডেটা অ্যানালাইসিস ও ফিচার পাইপলাইন আর্কিটেকচার

```
                          ┌─────────────────────────────────────┐
                          │     Transactional SQL DB / Lake     │ ──► Raw Events / Logs
                          └──────────────────┬──────────────────┘
                                             │
                                             ▼ Analytical SQL (CTEs, Windowing, Aggs)
                          ┌─────────────────────────────────────┐
                          │      Engineered Feature Store       │ ──► Parquet Columnar Storage
                          └──────────────────┬──────────────────┘
                                             │
                                             ▼ Pandas Downcasting & NumPy Vectorization
                          ┌─────────────────────────────────────┐
                          │   Exploratory Data Analysis (EDA)   │ ──► Skewness, VIF, Chi-Square,
                          │         & Visual Diagnostics        │     Leakage Audit, Sanity
                          └──────────────────┬──────────────────┘
                                             │ Clean, Validated Features (X, y)
                                             ▼
                          ┌─────────────────────────────────────┐
                          │   Scikit-Learn Pipelines & Models   │ ──► Training, Tuning & Deploy
                          └─────────────────────────────────────┘
```

---

## 🚀 গুগুল কোলাবে কীভাবে রান করবেন (How to run in Google Colab)

1. যে কোনো `.ipynb` ফাইল সরাসরি [Google Colab](https://colab.research.google.com)-এ গিয়ে `Upload Notebook` অপশনে আপলোড করুন।
2. কোড সেলগুলো ওপর থেকে নিচে ক্রমান্বয়ে শিফট + এন্টার (`Shift + Enter`) চেপে এক্সিকিউট করুন।
