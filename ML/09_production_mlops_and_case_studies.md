# অধ্যায় ৯: প্রোডাকশন এমএল ইঞ্জিনিয়ারিং ও এমএলঅপ্স (Production ML Engineering & MLOps Masterclass)

> *"In data science, a model in a notebook is an insight. In ML engineering, only a model deployed reliably behind a secure, monitored API creating measurable business value is considered done."*
> 
> গুগল-এর ঐতিহাসিক গবেষণাপত্র (*"Hidden Technical Debt in Machine Learning Systems"*) অনুসারে, একটি সফল প্রোডাকশন সিস্টেমে কোর মেশিন লার্নিং কোড থাকে মাত্র **৫% থেকে ১০%**; বাকি **৯০% থেকে ৯৫%** অংশই হলো ডেটা পাইপলাইন, কনফিগারেশন, সার্ভিং অবকাঠামো, এমএলঅপ্স (MLOps), কন্টেইনারাইজেশন এবং ড্রিফট মনিটরিং। এই অধ্যায়ে মডেল প্যাকেজিং থেকে শুরু করে FastAPI সার্ভিং, Pydantic ডেটা ভ্যালিডেশন, রিয়েল-টাইম বনাম ব্যাচ ইনফারেন্স, MLflow মডেল রেজিস্ট্রি, ডকার কন্টেইনারাইজেশন, GitHub Actions CI/CD, পপুলেশন স্ট্যাবিলিটি ইনডেক্স (PSI) ড্রিফট হিসাব, এবং খারাপ মডেল রিলিজ থেকে দ্রুত রিকভারি করার পূর্ণাঙ্গ প্রোডাকশন গাইড সংকলিত হয়েছে।

---

## 📑 সম্পূর্ণ বিষয়সূচি (Table of Contents)
1. [ল্যাবরেটরি বনাম প্রোডাকশন এমএল: লুকায়িত টেকনিক্যাল ঋণ](#১-ল্যাবরেটরি-বনাম-প্রোডাকশন-এমএল)
2. [মডেল সিরিয়ালাইজেশন ও প্যাকেজিং (Pickle vs ONNX vs Safetensors)](#২-মডেল-সিরিয়ালাইজেশন-ও-প্যাকেজিং)
3. [মডেল সার্ভিং ও ইনফারেন্স ব্যাকএন্ড (FastAPI ও Pydantic v2 আর্কিটেকচার)](#৩-মডেল-সার্ভিং-ও-ইনফারেন্স-ব্যাকএন্ড)
   - ৩.১ Pydantic v2 স্কিমা এনফোর্সমেন্ট ও ইনপুট স্যানিটাইজেশন
   - ৩.২ প্রেডিকশন, ব্যাচ এবং হেলথ-চেক এন্ডপয়েন্ট ডিজাইন
   - ৩.৩ স্ট্রাকচার্ড JSON লগিং ও রিকোয়েস্ট কোরিলেশন আইডি
4. [ইনফারেন্স কৌশল ও ট্রেড-অফ (Batch vs Real-Time vs Streaming)](#৪-ইনফারেন্স-কৌশল-ও-ট্রেড-অফ)
   - ৪.১ লেটেন্সি (P50, P95, P99) বনাম থ্রুপুট (QPS)
   - ৪.২ ডাইনামিক রিকোয়েস্ট ব্যাচিং (Dynamic Request Batching)
   - ৪.৩ মাল্টি-টিয়ার ক্যাশিং কৌশল (Redis Feature & Response Cache)
5. [ডেটা ও মডেল পাইপলাইন: ট্রেনিং-সার্ভিং প্যারিটি](#৫-ডেটা-ও-মডেল-পাইপলাইন)
   - ৫.১ ট্রেনিং-সার্ভিং স্কিউ (Training-Serving Skew) নির্মূল
   - ৫.২ ফিচার স্টোর আর্কিটেকচার (Feast: Offline বনাম Online Store)
   - ৫.৩ ডেটা ভার্সনিং (DVC - Data Version Control) ও পাইপলাইন অর্কেস্ট্রেশন (Airflow)
6. [এমএলঅপ্স টুলচেইন ও মডেল লাইফসাইকেল (MLflow Registry)](#৬-এমএলঅপ্স-টুলচেইন-ও-মডেল-লাইফসাইকেল)
   - ৬.১ MLflow Tracking, Signatures ও মডেল স্টেজ প্রমোশন
7. [কন্টেইনারাইজেশন ও CI/CD অটোমেশন (Docker & GitHub Actions)](#৭-কন্টেইনারাইজেশন-ও-cicd-অটোমেশন)
   - ৭.১ অপটিমাইজড মাল্টি-স্টেজ ডকারফাইল ও নন-রুট নিরাপত্তা
   - ৭.২ প্রোডাকশন GitHub Actions CI/CD ওয়ার্কফ্লো
8. [প্রোডাকশন মনিটরিং ও ড্রিফট গণিত (Data Drift, Concept Drift ও PSI)](#৮-প্রোডাকশন-মনিটরিং-ও-ড্রিফট-গণিত)
   - ৮.১ ড্রিফটের প্রকারভেদ: Covariate Shift, Concept Drift ও Prior Shift
   - ৮.২ পপুলেশন স্ট্যাবিলিটি ইনডেক্স (PSI) হাতে-কলমে বাস্তব সংখ্যাভিত্তিক হিসাব
   - ৮.৩ ডিলেড গ্রাউন্ড ট্রুথ (Delayed Ground Truth) ও প্রক্সি মেট্রিক্স মনিটরিং
9. [রিলিজ স্ট্র্যাটেজি, অ্যালার্টিং ও অটোমেটিক রোলব্যাক](#৯-রিলিজ-স্ট্র্যাটেজি-অ্যালার্টিং-ও-অটোমেটিক-রোলব্যাক)
   - ৯.১ Canary Deployment, Blue-Green ও Shadow Launch
   - ৯.২ সার্কিট ব্রেকার ও ফলব্যাক মেকানিজম
10. [রোডম্যাপ ক্যাপস্টোন টাস্ক ও পাস কন্ডিশন অডিট](#১০-রোডম্যাপ-ক্যাপস্টোন-টাস্ক-ও-পাস-কন্ডিশন-অডিট)
11. [প্রোডাকশন-গ্রেড পাইথন ইমপ্লিমেন্টেশন কোড](#১১-প্রোডাকশন-গ্রেড-পাইথন-ইমপ্লিমেন্টেশন-কোড)

---

# ১. ল্যাবরেটরি বনাম প্রোডাকশন এমএল

জুপাইটার নোটবুকে একটি মডেল ট্রেন করে ৯৫% এক্যুরেসি পাওয়া একটি ডেটা সায়েন্স প্রজেক্টের মাত্র সূচনা। বাস্তব এন্টারপ্রাইজ সিস্টেমে মডেলটি হাজার হাজার ব্যবহারকারীর ট্রাফিকের মুখে পড়ে, যেখানে ডেটা ক্রমাগত পরিবর্তিত হয়, নেটওয়ার্ক স্লো হতে পারে এবং ইনপুটে ক্ষতিকর পেলোড আসতে পারে।

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│                           Machine Learning Code (5-10%)                          │
│                                  [ Model.fit() ]                                 │
├──────────────────────────────────────────────────────────────────────────────────┤
│                       Production Infrastructure (90-95%)                         │
│  ┌───────────────────────┐  ┌───────────────────────┐  ┌──────────────────────┐  │
│  │ Data Ingestion & ETL  │  │ Feature Store (Feast) │  │ Schema Validation    │  │
│  └───────────────────────┘  └───────────────────────┘  └──────────────────────┘  │
│  ┌───────────────────────┐  ┌───────────────────────┐  ┌──────────────────────┐  │
│  │ MLflow Registry       │  │ Docker CI/CD Pipeline │  │ FastAPI Serving (K8s)│  │
│  └───────────────────────┘  └───────────────────────┘  └──────────────────────┘  │
│  ┌───────────────────────┐  ┌───────────────────────┐  ┌──────────────────────┐  │
│  │ Drift Monitor (PSI)   │  │ Prometheus / Grafana  │  │ Auto-Rollback Engine │  │
│  └───────────────────────┘  └───────────────────────┘  └──────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────────────┘
```

---

# ২. মডেল সিরিয়ালাইজেশন ও প্যাকেজিং

ট্রেন করা মডেলকে ডিস্কে সেভ এবং মেমরিতে রিলোড করার পদ্ধতি মডেলের ইনফারেন্স গতি ও নিরাপত্তার ওপর বিশাল প্রভাব ফেলে:

| ফরম্যাট | মূল বৈশিষ্ট্য | সুবিধা | মারাত্মক ঝুঁকি / সীমাবদ্ধতা |
| :--- | :--- | :--- | :--- |
| **Pickle / Joblib** | পাইথনের অবজেক্ট সিরিয়ালাইজেশন | স্কিট-লার্ন ও সাধারণ পাইথনে ব্যবহার সহজ | **মারাত্মক নিরাপত্তা ঝুঁকি!** `__reduce__` মেথড দিয়ে হ্যাকার আরবিট্রারি শেল কোড এক্সিকিউট করতে পারে। আনট্রাস্টেড সোর্স থেকে লোড করা নিষিদ্ধ। |
| **ONNX Runtime** | ক্রস-প্ল্যাটফর্ম ওপেন স্ট্যান্ডার্ড | C++, Java, Python-এ ২-৫ গুণ দ্রুত ইনফারেন্স, গ্রাফ অপটিমাইজেশন | কাস্টম পাইথন লজিক সরাসরি কনভার্ট করা কিছুটা জটিল |
| **TorchScript** | PyTorch C++ ইন্টারমিডিয়েট রিপ্রেজেন্টেশন | পাইথন ইন্টারপ্রেটার ও GIL ছাড়াই C++ সার্ভারে লো-লেটেন্সি এক্সিকিউশন | ডাইনামিক পাইথন ফিচার সীমিত |
| **Safetensors** | HuggingFace-এর আধুনিক টেনসর ফরম্যাট | জিরো-কপি মেমরি ম্যাপিং (mmap), সম্পূর্ণ নিরাপদ (কোনো কোড রান করে না) | মূলত ডিপ লার্নিং টেনসর ও ওয়েটের জন্য প্রযোজ্য |

---

# ৩. মডেল সার্ভিং ও ইনফারেন্স ব্যাকএন্ড (FastAPI Architecture)

আধুনিক এন্টারপ্রাইজ এমএল সার্ভিংয়ে **FastAPI** ডিফ্যাক্টো স্ট্যান্ডার্ড। এটি অ্যাসিঙ্ক্রোনাস (ASGI), অত্যন্ত দ্রুত এবং Pydantic দিয়ে স্বয়ংক্রিয়ভাবে ইনপুট স্কিমা ভ্যালিডেশন ও OpenAPI ডকুমেন্টেশন তৈরি করে।

```
Client Request (JSON)
       │
       ▼
[ FastAPI Middleware ] ──► Inject Correlation ID / Request ID, Latency Timer
       │
       ▼
[ Pydantic v2 Validator ] ──► Type Checking, Range Validation (e.g., Age 18-100, Amount > 0)
       │ (HTTP 422 if invalid)
       ▼
[ Prediction Pipeline ]   ──► Preprocessing Transformer ──► Model Inference (ONNX / Joblib)
       │
       ▼
[ Structured JSON Log ]  ──► Emit Log: {request_id, latency_ms, prediction, input_summary}
       │
       ▼
[ Response JSON ]        ──► Return HTTP 200 with Prediction & Model Metadata
```

---

### ৩.১ Pydantic v2 স্কিমা এনফোর্সমেন্ট ও ফিল্ড ভ্যালিডেশন
মডেল যেন কখনো অবৈধ ডেটা (`amount = -500` বা `age = 999`) পেয়ে ক্র্যাশ না করে, সেজন্য Pydantic Field লেভেলে কড়া বাউন্ডারি নির্ধারণ করা হয়:
```python
class TransactionPayload(BaseModel):
    transaction_id: str = Field(..., description="UUID of transaction")
    amount: float = Field(..., gt=0.0, le=1_000_000.0, description="Amount must be positive")
    user_age: int = Field(..., ge=18, le=120, description="Cardholder age")
    foreign_tx: int = Field(..., ge=0, le=1, description="Binary flag: 1 if international")
```

---

### ৩.২ স্ট্রাকচার্ড JSON লগিং ও রিকোয়েস্ট কোরিলেশন আইডি
প্রোডাকশনে আনস্ট্রাকচার্ড প্লেইন টেক্সট প্রিন্ট করা সম্পূর্ণ নিষিদ্ধ। ক্লাউড সেন্ট্রালাইজড লগিং (Datadog, AWS CloudWatch, ELK Stack) এর জন্য প্রতিটি রিকোয়েস্টে একটি অনন্য `request_id` (UUID4) যুক্ত করে JSON ফরম্যাটে লগ করতে হয়:
```json
{
  "timestamp": "2026-11-03T11:30:00Z",
  "level": "INFO",
  "request_id": "a9f8b2c4-1234-4567-89ab-cdef01234567",
  "endpoint": "/v1/predict",
  "model_version": "fraud_xgb_v2.1",
  "input_features": {"amount": 250.0, "user_age": 34, "foreign_tx": 1},
  "prediction": 0.884,
  "decision": "DECLINE",
  "latency_ms": 4.12
}
```

---

# ৪. ইনফারেন্স কৌশল ও ট্রেড-অফ (Batch vs Real-Time vs Streaming)

```
┌──────────────────────┬──────────────────────────────┬──────────────────────────────┐
│ বৈশিষ্ট্য             │ Real-Time (Online API)       │ Batch Scoring                │
├──────────────────────┼──────────────────────────────┼──────────────────────────────┤
│ **লেটেন্সি SLA**     │ ১০ ms থেকে ১০০ ms            │ মিনিট, ঘণ্টা বা দিন          │
│ **ইনফ্রাস্ট্রাকচার** │ Kubernetes Pods, FastAPI     │ Airflow, Spark, BigQuery SQL │
│ **থ্রুপুট**          │ শত থেকে হাজার QPS            │ কোটি কোটি রেকর্ড একত্রে      │
│ **ক্যাশিং**          │ Redis ইন-মেমরি ক্যাশ         │ ডেটাবেস প্রি-কম্পিউটেড ভ্যালু│
│ **ব্যবহারের ক্ষেত্র**│ ফ্রড ডিটেকশন, সার্চ র‍্যাঙ্কিং│ মাসিক ক্রেডিট স্কোর, রিকমেন্ডার│
└──────────────────────┴──────────────────────────────┴──────────────────────────────┘
```

---

### ৪.১ লেটেন্সি পারসেন্টাইল (P50, P95, P99)
গড় লেটেন্সি (Average Latency) দিয়ে প্রোডাকশন পারফরম্যান্স মাপা একটি মারাত্মক বিভ্রান্তি! যদি ১০০ জন ইউজারের মধ্যে ৯৯ জন ১০ ms পায় কিন্তু ১ জন ১০,০০০ ms (১০ সেকেন্ড) লোডিং দেখে, তবে গড় লেটেন্সি দেখাবে চমৎকার অথচ ১% ব্যবহারকারী সিস্টেমে ক্র্যাশ ফেস করছে।
- **P95 Latency:** ৯৫% রিকোয়েস্ট যত মিলি-সেকেন্ডের মধ্যে রেসপন্স পায়।
- **P99 Latency (The Gold Standard):** ৯৯% রিকোয়েস্টের সর্বোচ্চ সময়সীমা। এন্টারপ্রাইজ SLA সর্বদা P99-এর ভিত্তিতে নির্ধারিত হয় (যেমন: $P99 < 50\text{ ms}$)।

---

### ৪.২ ডাইনামিক রিকোয়েস্ট ব্যাচিং (Dynamic Request Batching)
জিপিইউতে একক রিকোয়েস্ট এক্সিকিউট করলে প্যারালাল কম্পিউটেশনের অপচয় হয়। ট্রাইটন (Triton) বা কাস্টম কিউ ব্যবহার করে একটি ক্ষুদ্র উইন্ডোর (যেমন ২ থেকে ৫ ms) মধ্যে জমা হওয়া একাধিক ইনকামিং রিকোয়েস্টকে একটি একক ব্যাচ $B$ বানিয়ে জিপিইউতে পাঠানো হয়। এতে থ্রুপুট ৫ গুণ বৃদ্ধি পায় অথচ লেটেন্সি SLA ক্ষুণ্ণ হয় না।

---

# ৫. ডেটা ও মডেল পাইপলাইন: ট্রেনিং-সার্ভিং প্যারিটি

### ৫.১ ট্রেনিং-সার্ভিং স্কিউ (Training-Serving Skew) নির্মূল
ট্রেনিং-সার্ভিং স্কিউ হলো সেই মারাত্মক বিপর্যয় যখন মডেল ট্রেইনিংয়ের ডেটা প্রিപ്രসেসিং লজিক (অফলাইনে Pandas/SQL দিয়ে তৈরি) এবং প্রোডাকশন ইনফারেন্সের লজিক (API-তে রিয়েল-টাইমে তৈরি) সামান্য ভিন্ন হয়।
- *বাস্তব উদাহরণ:* অফলাইন ট্রেইনিংয়ে মিসিং ভ্যালু পূরণ করা হয়েছে ট্রেইনিং সেটের গড় ($Mean = 35.4$) দিয়ে। কিন্তু এপিআই সার্ভারে ডেভেলপার কোড লিখেছে: `if not val: val = 0`। এই সামান্য পার্থক্যের কারণেই মডেল প্রোডাকশনে ধ্বংসাত্মক প্রেডিকশন দেবে!
- **প্রতিরোধ:** সমগ্র প্রিপ্রসেসিং এবং মডেল প্রেডিকশনকে একটি একক Scikit-learn `Pipeline` বা `ColumnTransformer` অবজেক্ট হিসেবে সেভ ও লোড করা, অথবা **Feature Store** ব্যবহার করা।

---

### ৫.২ ফিচার স্টোর আর্কিটেকচার (Feast)
একটি সেন্ট্রালাইজড ফিচার স্টোর নিশ্চিত করে যে ট্রেইনিং এবং সার্ভিং উভয়েই হুবহু একই ফিচারের সংজ্ঞা ব্যবহার করছে:
1. **Offline Store (Snowflake / BigQuery):** পয়েন্ট-ইন-টাইম কারেক্টনেস বজায় রেখে ঐতিহাসিক ফিচারের ওপর মডেল ট্রেনিং ডেটাসেট তৈরি করে (কোনো ডেটা লিকেজ ছাড়া)।
2. **Online Store (Redis / DynamoDB):** সর্বশেষ ফিচারের মান লো-লেটেন্সিতে ($< 5\text{ ms}$) কি-ভ্যালু স্টোরে জমা রাখে যাতে API তাৎক্ষণিকভাবে ইউজার হিস্ট্রি রিট্রিভ করতে পারে।

---

# ৬. এমএলঅপ্স টুলচেইন ও মডেল লাইফসাইকেল (MLflow)

```
[ Research & Experiments ] ──► MLflow Tracking (Params, Metrics, Artifacts)
                                      │
                                      ▼
[ Evaluation & Approval ]  ──► MLflow Model Registry (Signature, Input/Output Schema)
                                      │
                                      ▼
[ Staging & Canary Test ]  ──► Transition: Staging ──► Integration Tests Pass
                                      │
                                      ▼
[ Production Release ]     ──► Transition: Production ──► Auto-Deploy to K8s Cluster
```

- **মডেল সিগনেচার (Model Signature):** মডেল কী শেইপ এবং টাইপের ইনপুট আশা করে এবং কী আউটপুট দেয়, তা MLflow সিগনেচারের মাধ্যমে কোড হিসেবে সংরক্ষিত থাকে (`infer_signature(X_train, y_pred)`)। ফলে স্কিমা পরিবর্তন হলে রেজিস্ট্রিতেই ত্রুটি ধরা পড়ে।

---

# ৭. কন্টেইনারাইজেশন ও CI/CD অটোমেশন (Docker & GitHub Actions)

### ৭.১ প্রোডাকশন ডকারফাইল অপটিমাইজেশন নীতি:
1. **মাল্টি-স্টেজ বিল্ড (Multi-Stage Build):** প্রথম স্টেজে বিল্ড টুলস (gcc, g++) ব্যবহার করে হুইল কম্পাইল করা হয় এবং ফাইনাল স্টেজে শুধু কম্পাইল্ড বাইনারি রাখা হয়, ফলে ইমেজের আকার ১ জিবি থেকে মাত্র ১৫০ এমবিতে নেমে আসে।
2. **নন-রুট সিকিউরিটি (Non-Root User):** কন্টেইনার কখনো `root` ইউজার হিসেবে চালানো উচিত নয়। কন্টেইনার কম্প্রোমাইজ হলে হ্যাকার যেন হোস্ট মেশিনের অ্যাক্সেস না পায়, সেজন্য ডেডিকেটেড `appuser` তৈরি করতে হয়।
3. **লেয়ার ক্যাশিং (Layer Caching):** সোর্স কোড পরিবর্তনের আগে `COPY requirements.txt` এবং `pip install` করতে হয়, যাতে প্রতিবার কোড পরিবর্তনে অপ্রয়োজনীয় প্যাকেজ ডাউনলোড না হয়।

---

### ৭.২ প্রোডাকশন GitHub Actions CI/CD ওয়ার্কফ্লো:
```yaml
name: Production ML CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test-and-validate:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Set up Python 3.11
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install Dependencies
        run: |
          pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest flake8

      - name: Code Quality & Linting
        run: |
          flake8 app.py --max-line-length=120

      - name: Run Unit & Integration Tests
        run: |
          pytest tests/ -v --cov=app

      - name: Build & Push Docker Image
        if: github.ref == 'refs/heads/main'
        run: |
          docker build -t ghcr.io/myorg/fraud-service:latest .
          # docker push ghcr.io/myorg/fraud-service:latest
```

---

# ৮. প্রোডাকশন মনিটরিং ও ড্রিফট গণিত (Data Drift, Concept Drift ও PSI)

```
1. Data Drift (Covariate Shift):   P(X) পরিবর্তিত হয়, কিন্তু P(y|X) অপরিবর্তিত থাকে।
2. Concept Drift:                  P(y|X) পরিবর্তিত হয় (যেমন: মহামারী বা মূল্যস্ফীতির কারণে ক্রেতার আচরণ বদল)।
3. Prior Probability Shift:        P(y) পরিবর্তিত হয় (যেমন: ফ্রডের সামগ্রিক হার হঠাৎ বেড়ে যাওয়া)।
```

---

### ৮.১ পপুলেশন স্ট্যাবিলিটি ইনডেক্স (Population Stability Index - PSI)
প্রোডাকশনের ইনকামিং ফিচার ডিস্ট্রিবিউশন ($Actual$) ট্রেইনিং বা বেসলাইন ডিস্ট্রিবিউশন ($Expected$) থেকে কতটা সরে গেছে তা পরিমাপের গাণিতিক স্ট্যান্ডার্ড হলো **PSI**:

$$\text{PSI} = \sum_{b=1}^B \left( \text{Actual}_b - \text{Expected}_b \right) \times \ln\left( \frac{\text{Actual}_b}{\text{Expected}_b} \right)$$
যেখানে $B$ হলো বাকেটের সংখ্যা (সাধারণত ১০টি ডিসাইল বা কোয়ান্টাইল বাকেট)।

#### 🔢 হাতে-কলমে বাস্তব সংখ্যাভিত্তিক PSI হিসাব:
ধরি ট্রানজ্যাকশন অ্যামাউন্ট ফিচারের জন্য ৪টি বাকেটে ট্রেইনিং বনাম প্রোডাকশন ডেটার অনুপাত নিম্নরূপ:

| বাকেট | রেঞ্জ | Expected (ট্রেনিং অনুপাত) | Actual (প্রোডাকশন অনুপাত) | $(Actual - Expected)$ | $\ln(Actual / Expected)$ | সাব-টোটাল PSI |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **১** | $\$0 - \$50$ | $0.40$ ($40\%$) | $0.20$ ($20\%$) | $-0.20$ | $\ln(0.20/0.40) = -0.693$ | $(-0.20) \times (-0.693) = \mathbf{0.1386}$ |
| **২** | $\$50 - \$100$ | $0.30$ ($30\%$) | $0.25$ ($25\%$) | $-0.05$ | $\ln(0.25/0.30) = -0.182$ | $(-0.05) \times (-0.182) = \mathbf{0.0091}$ |
| **৩** | $\$100 - \$200$| $0.20$ ($20\%$) | $0.25$ ($25\%$) | $+0.05$ | $\ln(0.25/0.20) = +0.223$ | $(+0.05) \times (+0.223) = \mathbf{0.0112}$ |
| **৪** | $\$200+$ | $0.10$ ($10\%$) | $0.30$ ($30\%$) | $+0.20$ | $\ln(0.30/0.10) = +1.099$ | $(+0.20) \times (+1.099) = \mathbf{0.2198}$ |

**মোট PSI যোগফল:**
$$\text{Total PSI} = 0.1386 + 0.0091 + 0.0112 + 0.2197 = \mathbf{0.3786} \quad (\approx \mathbf{0.379})$$

#### 📊 PSI সিদ্ধান্তের নীতি (Standard Industry Interpretation):
- $\text{PSI} < 0.10$: কোনো পরিবর্তন নেই; মডেল সম্পূর্ণ স্থিতিশীল।
- $0.10 \le \text{PSI} \le 0.25$: মৃদু ড্রিফট; অ্যালার্ম জারি ও নিবিড় পর্যবেক্ষণ প্রয়োজন।
- $\text{PSI} > 0.25$: **মারাত্মক ড্রিফট (Severe Drift)!**
- *অডিট সিদ্ধান্ত:* যেহেতু আমাদের হিসাবকৃত $\text{PSI} = \mathbf{0.379} > 0.25$, তাই ইনকামিং গ্রাহকদের ব্যয়ের প্যাটার্নে বৈপ্লবিক পরিবর্তন এসেছে। এই মডেল প্রোডাকশনে রাখা অনিরাপদ; তাৎক্ষণিকভাবে স্বয়ংক্রিয় রি-ট্রেইনিং ট্রিগার করতে হবে!

---

### ৮.২ ডিলেড গ্রাউন্ড ট্রুথ (Delayed Ground Truth) সমাধান
ক্রেডিট কার্ড ফ্রড বা ঋণ খেলাপি মডেলে প্রকৃত সত্য ($y$) জানতে ৩০ থেকে ৯০ দিন পর্যন্ত সময় লাগে (চার্জব্যাক প্রসেস)। 
- গ্রাউন্ড ট্রুথ আসার অপেক্ষায় বসে থাকলে কোম্পানি ধ্বংস হয়ে যাবে।
- **সমাধান:** প্রতিদিন ইনপুট ডেটার **PSI**, আউটপুট প্রেডিকশন প্রোবাবিলিটির ডিস্ট্রিবিউশন, এবং মিসিং ভ্যালু রেট ট্র্যাক করা। যদি আউটপুট ডিস্ট্রিবিউশন স্বাভাবিক ৫% ফ্রড থেকে হঠাৎ ২৫%-এ লাফ দেয়, তবে সত্য লেবেল ছাড়াই বোঝা যায় যে সিস্টেমে কোনো বিপর্যয় ঘটেছে।

---

# ৯. রিলিজ স্ট্র্যাটেজি, অ্যালার্টিং ও অটোমেটিক রোলব্যাক

```
                    ┌────────────────────────────┐
                    │  API Gateway / Router      │
                    └─────────────┬──────────────┘
                                  │
                 ┌────────────────┴────────────────┐
          95% Traffic                       5% Traffic (Canary)
                 ▼                                 ▼
    ┌─────────────────────────┐       ┌─────────────────────────┐
    │ Stable Model (v1.0.0)   │       │ Candidate Model (v2.0.0)│
    │ (Monitored baseline)    │       │ (Under strict audit)    │
    └─────────────────────────┘       └────────────┬────────────┘
                                                   │
                           If Error Rate > 1% or P99 > 100ms
                                                   │
                                                   ▼
                                      [ INSTANT AUTO-ROLLBACK ]
                                     (Drop Canary Pods in < 30s)
```

1. **Canary Deployment:** নতুন মডেল রিলিজ হলে সরাসরি ১০০% ট্রাফিকে না দিয়ে মাত্র ৫% লাইভ ট্রাফিকে পাঠানো হয়। পরবর্তী ১ ঘণ্টা যদি এরর রেট $<0.1\%$ এবং লেটেন্সি স্থিতিশীল থাকে, তবে ট্রাফিক ধাপে ধাপে ১০% $\to$ ৫০% $\to$ ১০০% করা হয়।
2. **Shadow Deployment (Dark Launch):** নতুন মডেল প্রোডাকশনে চলে কিন্তু ব্যবহারকারীকে কোনো উত্তর ফেরত দেয় না। কেবল ইনপুট কপি করে মডেলের লেটেন্সি ও প্রেডিকশন লগ পরীক্ষা করা হয়।
3. **সার্কিট ব্রেকার ও ফলব্যাক (Circuit Breaker):** যদি মডেল এপিআই সম্পূর্ণ ক্র্যাশ করে বা টাইমআউট দেয়, তবে অ্যাপ্লিকেশন ডাউন না হয়ে স্বয়ংক্রিয়ভাবে একটি কনজারভেটিভ রুল-বেসড হিউরিস্টিক বা ক্যাশড ডিফল্ট প্রেডিকশন রিটার্ন করে।

---

# ১০. রোডম্যাপ ক্যাপস্টোন টাস্ক ও পাস কন্ডিশন অডিট

> 🎯 **Roadmap Pass Condition (Page 7):**
> *"Explain how you would detect and recover from a bad model release."*

```
========================================================================================
            প্রোডাকশন ব্যাড রিলিজ অডিট: ঘটনা বিশ্লেষণ ও রিকভারি প্রোটোকল
========================================================================================
ঘটনা: ফ্রড ডিটেকশন মডেল v2.1.0 রিলিজের ৫ মিনিটের মাথায় কাস্টমারদের বৈধ কার্ড ব্লক হওয়া শুরু হলো।
শনাক্তকরণ: Prometheus P99 অ্যালার্ট এবং পেমেন্ট গেটওয়ে এরর রেট স্পাইক।
========================================================================================
```

### 🎓 পাস কন্ডিশন পূর্ণাঙ্গ ডিফেন্স ও রিকভারি ব্লুপ্রিন্ট:

#### ধাপ ১: তাৎক্ষণিক শনাক্তকরণ (Real-Time Detection within 2 Minutes):
1. **ইনফ্রাস্ট্রাকচার মেট্রিক্স:** HTTP 500 এরর রেট ১% এর বেশি হওয়া অথবা P99 ইনফারেন্স লেটেন্সি নির্ধারিত SLA ($50\text{ ms}$) ছাড়িয়ে যাওয়া।
2. **মডেল স্ট্যাটিস্টিক্যাল মেট্রিক্স:** প্রতি মিনিটে পজিটিভ প্রেডিকশন রেট (Fraud Flag Rate) সাধারণ বেসলাইন ২.১% থেকে অস্বাভাবিকভাবে ১৮.৫%-এ লাফ দেওয়া।
3. **সিস্টেম অ্যালার্ট:** PagerDuty এবং স্ল্যাক `#ml-incidents` চ্যানেলে হাই-প্রায়োরিটি অ্যালার্ম ট্রিগার।

#### ধাপ ২: স্বয়ংক্রিয় প্রশমন ও রোলব্যাক (Instant Mitigation in < 60 Seconds):
1. **অটো-রোলব্যাক ট্রিগার:** কুবারনেটিস বা ক্লাউড লোড ব্যালান্সারে প্রি-কনফিগার করা হেলথ রুল অনুসারে ক্যানারি পডগুলো তাত্ক্ষণিকভাবে টার্মিনেট করা এবং ১০০% ট্রাফিক পূর্ববর্তী স্থিতিশীল মডেল `v2.0.0`-এ রি-রুট করা।
2. **সার্কিট ব্রেকার সক্রিয়করণ:** যদি রোলব্যাকে বিলম্ব হয়, তবে API গেটওয়ে তাৎক্ষণিকভাবে ফলব্যাক রুল-ইঞ্জিন চালু করবে যাতে কোনো বৈধ ট্রানজ্যাকশন ব্লক না হয়।

#### ধাপ ৩: রুট কজ অ্যানালাইসিস ও পোস্ট-মর্টেম (RCA & Blameless Post-Mortem):
1. **লগ অডিট:** রিকোয়েস্ট কোরিলেশন আইডি দিয়ে ব্যর্থ রিকোয়েস্টগুলোর ইনপুট পেলোড বিশ্লেষণ।
2. **ফলাফল:** দেখা গেল আপস্ট্রিম মোবাইল অ্যাপের নতুন আপডেটে `user_age` ফিল্ডে `null` পাঠানো হচ্ছিল, যা নতুন মডেলের প্রিপ্রসেসিং পাইপলাইনে `NaN` তৈরি করে প্রেডিকশন প্রোবাবিলিটি বিকৃত করেছে।
3. **স্থায়ী সমাধান:** Pydantic স্কিমাতে `null` হ্যান্ডলিং জোরদার করা, প্রিপ্রসেসিং পাইপলাইনে ইম্পিউটার টেস্ট কভারেজ বাড়ানো এবং CI/CD গেটে ইন্টিগ্রেশন রিগ্রেশন টেস্ট যুক্ত করা।

---

# ১১. প্রোডাকশন-গ্রেড পাইথন ইমপ্লিমেন্টেশন কোড

### ১১.১ সম্পূর্ণ প্রোডাকশন FastAPI সার্ভিস (`app.py`)

```python
"""
production_service.py
Enterprise FastAPI Serving Backend with Pydantic v2, Health Probes & Metrics
"""
from fastapi import FastAPI, HTTPException, Request, status
from fastapi.responses import JSONResponse
from pydantic import BaseModel, Field, field_validator
import time
import uuid
import logging
import json
import numpy as np

# ১. স্ট্রাকচার্ড JSON লগিং কনফিগারেশন
class JsonFormatter(logging.Formatter):
    def format(self, record):
        log_obj = {
            "timestamp": self.formatTime(record, self.datefmt),
            "level": record.levelname,
            "message": record.getMessage(),
            "logger": record.name
        }
        if hasattr(record, "extra_info"):
            log_obj.update(record.extra_info)
        return json.dumps(log_obj)

logger = logging.getLogger("ml_service")
handler = logging.StreamHandler()
handler.setFormatter(JsonFormatter())
logger.addHandler(handler)
logger.setLevel(logging.INFO)

# ২. FastAPI অ্যাপ ইনিশিয়ালাইজেশন
app = FastAPI(
    title="Production Fraud Classification Service",
    description="Enterprise-grade inference API with schema validation & health checks",
    version="2.0.0"
)

# ৩. Pydantic v2 ইনপুট ও আউটপুট স্কিমা
class TransactionInput(BaseModel):
    transaction_id: str = Field(..., description="Unique transaction UUID")
    amount: float = Field(..., gt=0.0, le=100000.0, description="Amount in USD (0 to 100k)")
    user_age: int = Field(..., ge=18, le=120, description="Cardholder age (18 to 120)")
    foreign_tx: int = Field(..., ge=0, le=1, description="Binary flag for international tx")
    tx_hour: int = Field(..., ge=0, le=23, description="Hour of the day (0 to 23)")

    @field_validator("transaction_id")
    @classmethod
    def validate_id_not_empty(cls, v: str) -> str:
        if not v.strip():
            raise ValueError("transaction_id cannot be blank")
        return v

class BatchTransactionInput(BaseModel):
    transactions: list[TransactionInput] = Field(..., max_length=100)

class PredictionResult(BaseModel):
    transaction_id: str
    fraud_probability: float
    is_fraud: bool
    decision: str
    model_version: str

# ৪. সিমুলেটেড মডেল ইঞ্জিন (প্রোডাকশনে joblib বা ONNX runtime লোড হবে)
class ProductionFraudModel:
    def __init__(self, version: str = "v2.0.0"):
        self.version = version
        self.threshold = 0.15 # বিজনেস কস্ট-অপটিমাইজড থ্রেশহোল্ড
        self.is_ready = True

    def predict(self, features: np.ndarray) -> np.ndarray:
        # মক প্রেডিকশন লজিক (বাস্তবে self.model.predict_proba)
        # amount বেশি ও foreign হলে ফ্রড রিস্ক বেশি
        scores = []
        for row in features:
            amt, age, foreign, hr = row
            base_risk = 0.05
            if foreign == 1:
                base_risk += 0.20
            if amt > 1000.0:
                base_risk += 0.40
            scores.append(min(0.99, base_risk))
        return np.array(scores)

MODEL_INSTANCE = ProductionFraudModel()

# ৫. মিডলওয়্যার: কোরিলেশন আইডি ও লেটেন্সি ট্র্যাকিং
@app.middleware("http")
async def add_correlation_and_timing(request: Request, call_next):
    correlation_id = request.headers.get("X-Correlation-ID", str(uuid.uuid4()))
    request.state.correlation_id = correlation_id
    start_time = time.perf_counter()
    
    response = await call_next(request)
    
    duration_ms = (time.perf_counter() - start_time) * 1000.0
    response.headers["X-Correlation-ID"] = correlation_id
    response.headers["X-Response-Time-Ms"] = f"{duration_ms:.2f}"
    
    logger.info(
        f"Handled {request.method} {request.url.path} in {duration_ms:.2f}ms",
        extra={"extra_info": {
            "correlation_id": correlation_id,
            "status_code": response.status_code,
            "duration_ms": round(duration_ms, 2)
        }}
    )
    return response

# ৬. এন্ডপয়েন্টস
@app.post("/v1/predict", response_model=PredictionResult)
def predict_single(payload: TransactionInput, request: Request):
    feat = np.array([[payload.amount, payload.user_age, payload.foreign_tx, payload.tx_hour]])
    prob = float(MODEL_INSTANCE.predict(feat)[0])
    is_fraud = prob >= MODEL_INSTANCE.threshold

    return PredictionResult(
        transaction_id=payload.transaction_id,
        fraud_probability=round(prob, 4),
        is_fraud=is_fraud,
        decision="BLOCK" if is_fraud else "ALLOW",
        model_version=MODEL_INSTANCE.version
    )

@app.post("/v1/batch_predict", response_model=list[PredictionResult])
def predict_batch(payload: BatchTransactionInput):
    txs = payload.transactions
    features = np.array([[t.amount, t.user_age, t.foreign_tx, t.tx_hour] for t in txs])
    probs = MODEL_INSTANCE.predict(features)

    results = []
    for t, prob in zip(txs, probs):
        is_fraud = prob >= MODEL_INSTANCE.threshold
        results.append(PredictionResult(
            transaction_id=t.transaction_id,
            fraud_probability=round(float(prob), 4),
            is_fraud=is_fraud,
            decision="BLOCK" if is_fraud else "ALLOW",
            model_version=MODEL_INSTANCE.version
        ))
    return results

@app.get("/health/live", status_code=status.HTTP_200_OK)
def liveness():
    """কুবারনেটিস পড জীবিত আছে কিনা যাচাই করে"""
    return {"status": "alive"}

@app.get("/health/ready", status_code=status.HTTP_200_OK)
def readiness():
    """মডেল মেমরিতে লোড হয়ে ট্রাফিকের জন্য প্রস্তুত কিনা যাচাই করে"""
    if not MODEL_INSTANCE.is_ready:
        raise HTTPException(status_code=503, detail="Model is not ready to serve traffic")
    return {"status": "ready", "model_version": MODEL_INSTANCE.version}
```

---

### ১১.২ ইন্টিগ্রেশন ও ইউনিট টেস্ট স্যুট (`test_service.py`)

```python
"""
test_service.py
Pytest integration tests verifying schema validation and predictions
"""
from fastapi.testclient import TestClient
from app import app

client = TestClient(app)

def test_liveness_probe():
    response = client.get("/health/live")
    assert response.status_code == 200
    assert response.json() == {"status": "alive"}

def test_readiness_probe():
    response = client.get("/health/ready")
    assert response.status_code == 200
    assert response.json()["status"] == "ready"

def test_valid_prediction():
    payload = {
        "transaction_id": "tx_98765",
        "amount": 1500.0,
        "user_age": 42,
        "foreign_tx": 1,
        "tx_hour": 14
    }
    response = client.post("/v1/predict", json=payload)
    assert response.status_code == 200
    data = response.json()
    assert data["transaction_id"] == "tx_98765"
    assert data["is_fraud"] is True
    assert data["decision"] == "BLOCK"
    assert "X-Correlation-ID" in response.headers

def test_invalid_input_validation():
    # নেগেটিভ অ্যামাউন্ট দিলে HTTP 422 Unprocessable Entity আসবে
    invalid_payload = {
        "transaction_id": "tx_fail",
        "amount": -50.0,
        "user_age": 15, # ১৮ বছরের কম
        "foreign_tx": 1,
        "tx_hour": 25 # ২৪ এর বেশি
    }
    response = client.post("/v1/predict", json=invalid_payload)
    assert response.status_code == 422
```

---

### ১১.৩ প্রোডাকশন মাল্টি-স্টেজ ডকারফাইল (`Dockerfile`)

```dockerfile
# ==========================================
# Stage 1: Build stage (Dependencies)
# ==========================================
FROM python:3.11-slim as builder

WORKDIR /build

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

# ==========================================
# Stage 2: Runtime stage (Minimal & Secure)
# ==========================================
FROM python:3.11-slim as runner

WORKDIR /app

# বিল্ডার স্টেজ থেকে কম্পাইল করা পাইথন প্যাকেজ কপি
COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH

# অ্যাপ্লিকেশন কোড কপি
COPY app.py .

# নিরাপত্তা: নন-রুট ইউজার তৈরি
RUN useradd -m -u 10001 mluser && chown -R mluser /app
USER mluser

# নেটওয়ার্ক ও রানটাইম কনফিগারেশন
EXPOSE 8000
HEALTHCHECK --interval=30s --timeout=5s --start-period=5s --retries=3 \
  CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health/ready')"

CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4", "--log-level", "warning"]
```

---

## 🎯 সংক্ষেপ ও সারসংক্ষেপ
- **সার্ভিং ব্যাকএন্ড:** FastAPI ও Pydantic v2 ইনপুট স্কিমা কড়াভাবে নিয়ন্ত্রণ করে এবং রিকোয়েস্ট কোরিলেশন আইডি দিয়ে প্রতি মিলি-সেকেন্ড ট্র্যাক করে।
- **ট্রেড-অফ:** গড় লেটেন্সি নয়, সর্বদা **P99 লেটেন্সি** এবং ডাইনামিক ব্যাচিং দিয়ে স্কেলিং নিয়ন্ত্রণ করতে হয়।
- **ট্রেনিং-সার্ভিং প্যারিটি:** স্কিউ প্রতিরোধে Scikit-learn Pipeline ও সেন্ট্রালাইজড ফিচার স্টোর (Feast) অপরিহার্য।
- **ড্রিফট মনিটরিং:** ইনপুট ডিস্ট্রিবিউশন শিফট পরিমাপে **PSI** এবং মডেল ড্রিফটে স্বয়ংক্রিয় রি-ট্রেইনিং ট্রিগার করা হয়।
- **পাস কন্ডিশন রিকভারি:** ক্যানারি ডিপ্লয়মেন্ট এবং অটোমেটেড রোলব্যাক মেকানিজম ব্যবহার করে ত্রুটিপূর্ণ মডেল ৬০ সেকেন্ডের মধ্যে ফিরিয়ে আনা সম্ভব।

---
*পরবর্তী অধ্যায়:* [👉 অধ্যায় ১৩: ক্লাউড, স্কেলিং ও এমএল সিস্টেম ডিজাইন (Phase 11)](file:///g:/MNC/ML/13_cloud_scale_and_ml_system_design.md) | [📚 মাস্টার হ্যান্ডবুক সূচিপত্র](file:///g:/MNC/ML/README.md)
