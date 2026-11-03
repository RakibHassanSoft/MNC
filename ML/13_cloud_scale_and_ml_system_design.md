# অধ্যায় ১৩: ক্লাউড, স্কেলিং ও এমএল সিস্টেম ডিজাইন (Cloud, Scale & ML System Design Masterclass)

> *"In an interview or in enterprise production, knowing how to train a model is assumed. Knowing how to architect a fault-tolerant system that processes 50,000 QPS, trains over terabytes of streaming data across distributed GPU clusters, and meets strict 25ms P99 latency SLAs within cloud budget constraints is what defines a Staff/Principal Machine Learning Engineer."*
> 
> মেশিন লার্নিং সিস্টেম ডিজাইন হলো সফ্টওয়্যার আর্কিটেকচার, ডেটা ইঞ্জিনিয়ারিং এবং ডিপ লার্নিংয়ের সমন্বিত রূপ। এই অধ্যায়ে ক্লাউড অবকাঠামো (AWS/GCP), ডেটা ইঞ্জিনিয়ারিং (Kafka, Spark, Feature Stores), ডিস্ট্রিবিউটেড ট্রেনিং মেকানিক্স (DDP, FSDP/ZeRO, Megatron-LM), ইনফারেন্স অপটিমাইজেশন (Triton Dynamic Batching, Knowledge Distillation), ব্যাক-অব-দ্য-এনভেলপ গণিত এবং একটি পূর্ণাঙ্গ লার্জ-স্কেল রেকমেন্ডেশন সিস্টেম আর্কিটেকচার ডিজাইন সংকলিত হয়েছে।

---

## 📑 সম্পূর্ণ বিষয়সূচি (Table of Contents)
1. [ক্লাউড ইনফ্রাস্ট্রাকচার ও সিকিউরিটি আর্কিটেকচার](#১-ক্লাউড-ইনফ্রাস্ট্রাকচার-ও-সিকিউরিটি-আর্কিটেকচার)
   - ১.১ কম্পিউট ও জিপিইউ হার্ডওয়্যার: T4, A100, H100 ও Multi-Instance GPU (MIG)
   - ১.২ নেটওয়ার্কিং ও আইডেন্টিটি: VPC, প্রাইভেট সাবনেট, KMS এনক্রিপশন ও IAM Least-Privilege
   - ১.৩ Infrastructure-as-Code (Terraform ডিক্লেয়ারেটিভ ক্লাউড ক্লাস্টার)
2. [এমএলের জন্য ডেটা ইঞ্জিনিয়ারিং (Data Engineering for ML)](#২-এমএলের-জন্য-ডেটা-ইঞ্জিনিয়ারিং)
   - ২.১ Data Lakehouse আর্কিটেকচার (Delta Lake ও Apache Iceberg)
   - ২.২ ব্যাচ বনাম স্ট্রিমিং: Apache Kafka ও Apache Spark/Flink ডিস্ট্রিবিউটেড প্রসেসিং
   - ২.৩ এন্টারপ্রাইজ ফিচার স্টোর (Feast: Point-in-Time Correctness ও Skew নির্মূল)
   - ২.৪ ডেটা কন্ট্রাক্ট ও স্কিমা ইভোলিউশন (Protobuf/Avro)
3. [স্কেলেবল লো-লেটেন্সি ইনফারেন্স আর্কিটেকচার](#৩-স্কেলেবল-লো-লেটেন্সি-ইনফারেন্স-আর্কিটেকচার)
   - ৩.১ ডাইনামিক রিকোয়েস্ট ব্যাচিং মেকানিক্স (Triton Architecture)
   - ৩.২ হরিজন্টাল পড অটোস্কেলিং (HPA) ও কাস্টম জিপিইউ মেট্রিক্স
   - ৩.৩ মাল্টি-টিয়ার ক্যাশিং কৌশল (L1 Local Memory + L2 Redis Cluster)
4. [মডেল কম্প্রেশন ও নলেজ ডিস্টিলেশন (Model Compression)](#৪-মডেল-কম্প্রেশন-ও-নলেজ-ডিস্টিলেশন)
   - ৪.১ প্রুনিং (Structured vs Unstructured Pruning)
   - ৪.২ কোয়ান্টাইজেশন (INT8 Post-Training vs QAT)
   - ৪.৩ নলেজ ডিস্টিলেশন (Hinton Loss ডেরিভেশন ও হাতে-কলমে KL-Divergence হিসাব)
5. [ডিস্ট্রিবিউটেড ট্রেনিং সিস্টেমস (Distributed Training Systems)](#৫-ডিস্ট্রিবিউটেড-ট্রেনিং-সিস্টেমস)
   - ৫.১ Distributed Data Parallel (DDP) ও Ring All-Reduce অ্যালগরিদম
   - ৫.২ টেনসর ও পাইপলাইন প্যারালালেলিজম (Megatron-LM ও GPipe)
   - ৫.৩ Fully Sharded Data Parallel (FSDP / DeepSpeed ZeRO-1, 2, 3)
   - ৫.৪ স্পট ইনস্ট্যান্স প্রি-এম্পশন ও ফল্ট-টলারেন্ট চেকপয়েন্টিং
6. [এমএল সিস্টেম ডিজাইন ফ্রেমওয়ার্ক: ব্যাক-অব-দ্য-এনভেলপ গণিত](#৬-এমএল-সিস্টেম-ডিজাইন-ফ্রেমওয়ার্ক)
   - ৬.১ সিস্টেম রিকোয়ারমেন্টস ও কনস্ট্রেইন্টস (SLA, QPS, Storage)
   - ৬.২ হাতে-কলমে ব্যাক-অব-দ্য-এনভেলপ হিসাব (৫০M ইউজারের QPS, স্টোরেজ ও ব্যান্ডউইথ)
7. [রোডম্যাপ ক্যাপস্টোন প্রজেক্ট: লার্জ-স্কেল রেকমেন্ডেশন সিস্টেম আর্কিটেকচার](#৭-রোডম্যাপ-ক্যাপস্টোন-প্রজেক্ট)
   - ৭.১ থ্রি-স্টেজ রিকমেন্ডার আর্কিটেকচার (Retrieval $\to$ Heavy Ranking $\to$ Re-ranking)
   - ৭.২ এন্ড-টু-এন্ড ডেটা ফ্লো ও আর্কিটেকচার ডায়াগ্রাম
   - ৭.৩ গ্রেসফুল ডিগ্রেডেশন ও ফলব্যাক মেকানিজম
8. [রোডম্যাপ পাস কন্ডিশন ও আর্কিটেকচারাল ট্রেড-অফ ডিফেন্স](#৮-রোডম্যাপ-পাস-কন্ডিশন-ও-আর্কিটেকচারাল-ট্রেড-অফ-ডিফেন্স)
9. [প্রোডাকশন-গ্রেড ডিস্ট্রিবিউটেড পাইথন কোড](#৯-প্রোডাকশন-গ্রেড-ডিস্ট্রিবিউটেড-পাইথন-কোড)

---

# ১. ক্লাউড ইনফ্রাস্ট্রাকচার ও সিকিউরিটি আর্কিটেকচার

একটি নির্ভরযোগ্য এমএল প্ল্যাটফর্মের ভিত্তি হলো ক্লাউড ইনফ্রাস্ট্রাকচার (AWS, Google Cloud, Azure)। 

```
                                  VPC (Virtual Private Cloud - 10.0.0.0/16)
  ┌─────────────────────────────────────────────────────────────────────────────────────────────┐
  │ Public Subnet (Internet-Facing)                                                             │
  │  ┌────────────────────────┐         ┌──────────────────────────────────────────────────┐    │
  │  │ AWS ALB / API Gateway  │ ──────► │ Cloudflare WAF & DDoS Protection                 │    │
  │  └───────────┬────────────┘         └──────────────────────────────────────────────────┘    │
  ├──────────────┼──────────────────────────────────────────────────────────────────────────────┤
  │ Private Subnet (No Direct Public Internet Access)                                           │
  │              ▼                                                                              │
  │  ┌────────────────────────┐         ┌────────────────────────┐   ┌───────────────────────┐  │
  │  │ Kubernetes (EKS / GKE) │ <─────> │ Feature Store (Redis)  │   │ Object Storage (S3)   │  │
  │  │ FastAPI Inference Pods │         │ Sub-5ms Feature Cache  │   │ Models & Parquet Logs │  │
  │  └───────────┬────────────┘         └────────────────────────┘   └───────────┬───────────┘  │
  │              │                                                               │              │
  │              ▼                                                               ▼              │
  │  ┌────────────────────────┐                                      ┌───────────────────────┐  │
  │  │ GPU Nodegroup (A100)   │ <──────────────────────────────────> │ IAM Least-Privilege   │  │
  │  │ Distributed Training   │   KMS Customer Managed Keys (CMK)    │ IRSA Role-based Auth  │  │
  │  └────────────────────────┘                                      └───────────────────────┘  │
  └─────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

### ১.১ কম্পিউট ও জিপিইউ হার্ডওয়্যার নির্বাচন
1. **NVIDIA T4 (16 GB):** সাশ্রয়ী, হালকা ডিপ লার্নিং ও ক্লাসিক্যাল এমএল ইনফারেন্সের জন্য আদর্শ (FP16/INT8 সাপোর্ট)।
2. **NVIDIA A100 (40 GB / 80 GB SXM4):** লার্জ ল্যাঙ্গুয়েজ মডেল এবং ভারী কনভোল্যুশনাল নেটওয়ার্কের জন্য ইন্ডাস্ট্রিয়াল ওয়ার্কহর্স (Tensor Cores ও NVLink 600 GB/s)।
3. **NVIDIA H100 (80 GB SXM5):** ট্রান্সফরমার ইঞ্জিন ও FP8 সাপোর্ট সহ বর্তমান বিশ্বের দ্রুততম জিপিইউ (A100-এর চেয়ে ৩ গুণ দ্রুত)।
4. **Multi-Instance GPU (MIG):** একটি A100-কে ৭টি স্বাধীন ভার্চুয়াল জিপিইউতে বিভক্ত করা যায়। ইনফারেন্সে যখন পুরো ৮০ জিবি প্রয়োজন হয় না, তখন MIG ব্যবহার করে ক্লাউড বিল **৭০% পর্যন্ত কমানো যায়**।

---

### ১.২ নেটওয়ার্কিং ও সিকিউরিটি নীতি
- **VPC ও প্রাইভেট সাবনেট:** মডেল সার্ভার এবং ডেটাবেস কখনোই পাবলিক ইন্টারনেটে উন্মুক্ত থাকবে না; শুধুমাত্র API Gateway বা Load Balancer পাবলিক সাবনেটে থাকবে।
- **IAM (Principle of Least Privilege):** কোনো সার্ভিসে হার্ডকোডেড API Key ব্যবহার করা যাবে না। কুবারনেটিসে **IRSA (IAM Roles for Service Accounts)** ব্যবহার করে পডকে সুনির্দিষ্ট S3 বালতি পড়ার সীমিত অনুমতি দিতে হবে।
- **ডেটা এনক্রিপশন:** বিশ্রামে থাকা সমস্ত ডেটা ক্লাউড **KMS (Key Management Service)** এবং ট্রানজিটে থাকা সমস্ত ট্রাফিক **TLS 1.3** দ্বারা এনক্রিপ্ট থাকবে।

---

# ২. এমএলের জন্য ডেটা ইঞ্জিনিয়ারিং (Data Engineering for ML)

মেশিন লার্নিং মডেলের গুণগত মান নির্ভর করে ইনপুট পাইপলাইনের নির্ভরযোগ্যতার ওপর।

```
User Clickstream Events ──► Apache Kafka (Streaming Buffer)
                                    │
           ┌────────────────────────┴────────────────────────┐
           ▼                                                 ▼
[ Real-time Stream: Flink ]                       [ Batch ETL: Apache Spark ]
Last 5-min Click Count / Velocity                 Daily Historical Aggregations
           │                                                 │
           ▼                                                 ▼
[ Online Store: Redis ]                           [ Offline Store: Delta Lake / S3 ]
(Latency: < 5ms for Inference)                    (Point-in-Time Correct Training)
```

---

### ২.১ Data Lakehouse আর্কিটেকচার (Delta Lake ও Apache Iceberg)
ঐতিহ্যগত ডেটা লেকে ট্রানজ্যাকশন সাপোর্ট ও রিট্রাই সুবিধা ছিল না। **Lakehouse (Apache Iceberg / Delta Lake)** অবজেক্ট স্টোরেজের (S3/GCS) ওপর **ACID ট্রানজ্যাকশন**, স্কিমা এনফোর্সমেন্ট এবং **Time-Travel (টাইম ট্রাভেল কুয়েরি)** সুবিধা দেয়। মডেল ট্রেনিংয়ের সময় ২ মাস আগের হুবহু স্ন্যাপশট কুয়েরি করে রিপ্রোডিউসিবিলিটি নিশ্চিত করা যায়।

---

### ২.২ ব্যাচ বনাম স্ট্রিমিং: Spark ও Kafka
1. **Apache Kafka:** ডিস্ট্রিবিউটেড পাব-সাব মেসেজ ব্রোকার যা প্রতি সেকেন্ডে লক্ষ লক্ষ ইভেন্টকে পার্টিশন ও কনজিউমার গ্রুপের মাধ্যমে বাফার করে রাখে।
2. **Apache Spark:** টেরাবাইট সাইজের ডেটাসেটকে প্যারালাল পার্টিশনে ভাগ করে ইন-মেমরি ডিস্ট্রিবিউটেড কম্পিউটেশন চালায়।
   - *অপটিমাইজেশন:* অপ্রয়োজনীয় ডাটা সাফলিং (Shuffle) এড়াতে ব্রডকাস্ট জয়েন (`broadcast join`) এবং উপযুক্ত পার্টিশনিং কি ব্যবহার করা।

---

### ২.৩ এন্টারপ্রাইজ ফিচার স্টোর (Feast)
ফিচার স্টোর হলো ডেটা ইঞ্জিনিয়ারিং ও মডেল সার্ভিংয়ের সংযোগকারী সেতু:
1. **Point-in-Time Correctness:** অফলাইনে মডেল ট্রেনিংয়ের সময় ডেটা লিকেজ এড়াতে নির্দিষ্ট ইভেন্ট টাইমস্ট্যাম্পের পূর্বের ডেটা যুক্ত করা।
2. **Training-Serving Consistency:** অফলাইন ডেটা সোর্স (Parquet) এবং অনলাইন রিয়েল-টাইম ক্যাশ (Redis) একই পাইথনিক ফিচার ডেফিনিশন শেয়ার করে, ফলে ট্রেনিং-সার্ভিং স্কিউ শূন্যে নেমে আসে।

---

# ৩. স্কেলেবল লো-লেটেন্সি ইনফারেন্স আর্কিটেকচার

### ৩.১ ডাইনামিক রিকোয়েস্ট ব্যাচিং (Dynamic Request Batching)
একটি জিপিইউতে প্রতি রিকোয়েস্টে সিঙ্গেল ইনফারেন্স চালালে জিপিইউ ব্যবহার মাত্র ৫% থাকে। **Triton Inference Server** বা **TorchServe** ইনকামিং রিকোয়েস্টগুলোকে একটি ক্ষুদ্র সময় উইন্ডোতে (যেমন: ৩ ms) বাফার করে একটি বড় টেনসর ব্যাচে রূপান্তর করে:

```
Request 1 (t = 0.0ms) ──┐
Request 2 (t = 1.2ms) ──┼──► [ Dynamic Batcher Queue ] ──► Merged Batch [4, D] ──► GPU Tensor Core
Request 3 (t = 2.1ms) ──┤    (Max Delay: 3ms, Max Size: 8)                           (Single Pass)
Request 4 (t = 2.8ms) ──┘
```
- **ফলাফল:** একক ইনফারেন্সের সমান সময়ে ৪টি রিকোয়েস্ট প্রসেস হয়ে যায়, ফলে সার্ভারের থ্রুপুট **৪ থেকে ৮ গুণ বৃদ্ধি পায়**।

---

### ৩.২ হরিজন্টাল পড অটোস্কেলিং (HPA)
কুবারনেটিসের ডিফল্ট CPU-বেসড স্কেলিং এমএলের জন্য অপর্যাপ্ত। Prometheus Adapter ব্যবহার করে **কাস্টম মেট্রিক্স** দ্বারা স্কেল করতে হয়:
- ইনকামিং রিকোয়েস্ট রেট ($QPS > 1,000$ পার পড)।
- ইনফারেন্স কিউ লেন্থ ($\text{Queue Length} > 20$)।
- জিপিইউ ডিউটি সাইকেল ($\text{GPU Duty Cycle} > 80\%$)।

---

# ৪. মডেল কম্প্রেশন ও নলেজ ডিস্টিলেশন

বিশাল ডিপ লার্নিং মডেলকে মোবাইল অ্যাপ বা লো-লেটেন্সি সার্ভারে চালাতে কম্প্রেশন অপরিহার্য:

### ৪.১ নলেজ ডিস্টিলেশন (Knowledge Distillation - Hinton et al., 2015)
একটি বিশাল শিক্ষক মডেলের (Teacher) প্রবাবিলিটি ডিস্ট্রিবিউশন (Soft Targets) দেখে একটি ক্ষুদ্র ছাত্র মডেলকে (Student) ট্রেইন করা হয়:

$$\mathcal{L}_{\text{KD}} = (1 - \alpha) \mathcal{L}_{\text{CE}}(y, \sigma(\mathbf{z}_s)) + \alpha T^2 D_{\text{KL}}\left( \sigma\left(\frac{\mathbf{z}_s}{T}\right) \;\middle\|\; \sigma\left(\frac{\mathbf{z}_t}{T}\right) \right)$$
- $T$: টেম্পারেচার (উচ্চ টেম্পারেচারে সফটম্যাক্স ডিস্ট্রিবিউশন মসৃণ হয়ে ভুল ক্লাসের মধ্যকার গোপন সম্পর্ক বা **Dark Knowledge** প্রকাশ করে)।
- $\alpha$: লস ব্যালান্সিং প্যারামিটার (সাধারণত $0.5 - 0.7$)।

#### 🔢 হাতে-কলমে টেম্পারেচার স্কেলিং ও সফট টার্গেটের ডার্ক নলেজ হিসাব:
ধরি একটি ৩-ক্লাসের ইমেজ ক্লাসিফিকেশন টাস্ক (Cat, Dog, Car):
- শিক্ষক মডেলের র লজিট: $\mathbf{z}_t = [8.0, 4.0, 0.0]$

**১. সাধারণ সফটম্যাক্স ($T = 1.0$):**
- $e^8 = 2980.96, \quad e^4 = 54.60, \quad e^0 = 1.0 \implies \text{Sum} = 3036.56$
- $p = [0.9817, 0.0179, 0.0003] \implies$ কুকুর আর গাড়ির মধ্যকার পার্থক্য সম্পূর্ণ হারিয়ে যায়!

**২. টেম্পারেচার স্কেলিং ($T = 4.0$):**
- স্কেলড লজিট: $[8/4, 4/4, 0/4] = [2.0, 1.0, 0.0]$
- $e^2 = 7.389, \quad e^1 = 2.718, \quad e^0 = 1.0 \implies \text{Sum} = 11.107$
- $p_{\text{soft}} = \left[\frac{7.389}{11.107}, \frac{2.718}{11.107}, \frac{1.0}{11.107}\right] = \mathbf{[0.665, 0.245, 0.090]}$
- *ডার্ক নলেজের প্রমাণ:* এখন স্পষ্ট বোঝা যাচ্ছে যে বিড়ালের ছবিটি গাড়ির চেয়ে কুকুরের সাথে **২.৭ গুণ বেশি সাদৃশ্যপূর্ণ** ($0.245 / 0.090$)! ছাত্র মডেল এই লুকায়িত জ্ঞান অনুকরণ করে আকারে ৫ গুণ ছোট হয়েও শিক্ষকের ৯৮% নির্ভুলতা অর্জন করে।

---

# ৫. ডিস্ট্রিবিউটেড ট্রেনিং সিস্টেমস (Distributed Training)

```
┌─────────────────────────────────┬─────────────────────────────────┬─────────────────────────────────┐
│ কৌশল                            │ মেকানিজম                        │ যোগাযোগের ধরন                   │
├─────────────────────────────────┼─────────────────────────────────┼─────────────────────────────────┤
│ **Data Parallel (DDP)**         │ প্রতিটি জিপিইউতে মডেল থাকে, ডেটা│ Ring All-Reduce গ্রেডিয়েন্ট সিঙ্ক│
│                                 │ ব্যাচ ভাগ হয়                    │                                 │
│ **Tensor Parallel (Megatron)**  │ ম্যাট্রিক্স গুণন জিপিইউতে রো ও   │ হাই-স্পিড NVLink ইন্ট্রা-নোড    │
│                                 │ কলাম বরাবর ভাগ হয়               │ অল-রিডিউস                       │
│ **FSDP / DeepSpeed ZeRO-3**     │ মডেল প্যারামিটার, গ্রেডিয়েন্ট ও │ অন-ডিমান্ড প্যারামিটার গ্যাদার ও│
│                                 │ অপটিমাইজার স্টেট সব ভাগ (Shard) │ রিলিজ                           │
└─────────────────────────────────┴─────────────────────────────────┴─────────────────────────────────┘
```

---

### ৫.১ Distributed Data Parallel (DDP) ও Ring All-Reduce
মাস্টার নোডের সেন্ট্রাল সার্ভার বটleneck দূর করতে আধুনিক DDP **Ring All-Reduce** ব্যবহার করে:
1. $N$ টি জিপিইউকে একটি ভার্চুয়াল রিং বা বলয়ে সাজানো হয়।
2. গ্রেডিয়েন্ট বাফারকে $N$ টি চাঙ্কে ভাগ করে প্রতিটি জিপিইউ কেবল তার ডানদিকের প্রতিবেশীকে ডেটা পাঠায় এবং বামদিকের থেকে ডেটা নেয়।
3. **যোগাযোগের পরিমাণ (Communication Volume):**
   $$\text{Total Transferred} = 2 \times \frac{N - 1}{N} \times \text{Model Size}$$
   জিপিইউ সংখ্যা $N$ যত বাড়ে, কমিউনিকেশন খরচ প্রায় কনস্ট্যান্ট ($2 \times \text{Model Size}$) থাকে, যা রৈখিক স্কেলিং নিশ্চিত করে।

---

### ৫.২ FSDP / DeepSpeed ZeRO স্টেজসমূহের মেমরি সাশ্রয়
- **ZeRO-1:** শুধু অ্যাডাম অপটিমাইজার স্টেট ($16 \times \Phi$ বাইট) ভাগ করে $\implies$ **৪ গুণ মেমরি সাশ্রয়**।
- **ZeRO-2:** অপটিমাইজার স্টেট + গ্রেডিয়েন্ট ($4 \times \Phi$) ভাগ করে $\implies$ **৮ গুণ মেমরি সাশ্রয়**।
- **ZeRO-3 (FSDP):** অপটিমাইজার + গ্রেডিয়েন্ট + মডেল প্যারামিটার ($4 \times \Phi$) সব ভাগ করে ফেলে। ফরওয়ার্ড পাসে শুধু প্রয়োজনীয় লেয়ারটি গ্যাদার করা হয় এবং গণনা শেষেই মেমরি খালি করে দেওয়া হয়। ফলে শত শত বিলিয়ন প্যারামিটারের মডেল কনজিউমার জিপিইউতেও ট্রেন করা যায়!

---

# ৬. এমএল সিস্টেম ডিজাইন ফ্রেমওয়ার্ক: ব্যাক-অব-দ্য-এনভেলপ গণিত

একটি এন্টারপ্রাইজ এমএল সিস্টেম ডিজাইন শুরু করার আগে অবশ্যই রিসোর্স ও স্কেলিংয়ের বাস্তবসম্মত হিসাব করতে হবে।

### 🔢 হাতে-কলমে ব্যাক-অব-দ্য-এনভেলপ হিসাব (৫০M DAU রিকমেন্ডার সিস্টেম):
- দৈনিক সক্রিয় ব্যবহারকারী (DAU) = **৫০,০০০,০০০ ($50\text{M}$)**
- প্রতিটি ইউজার প্রতিদিন গড়ে **১০ বার** অ্যাপে স্ক্রল বা পেজ রিফ্রেশ করে।

#### ১. ট্রাফিক ও QPS হিসাব:
- দৈনিক মোট রিকোয়েস্ট সংখ্যা:
  $$\text{Total Requests/Day} = 50,000,000 \times 10 = \mathbf{500,000,000} \quad (\text{৫০০ মিলিয়ন})$$
- গড় QPS (Queries Per Second):
  $$\text{Average QPS} = \frac{500,000,000}{86,400\text{ seconds}} \approx \mathbf{5,787\text{ QPS}}$$
- পিক ট্রাফিক (Peak QPS - ৩ গুণ গুণনীয়ক):
  $$\text{Peak QPS} = 5,787 \times 3 \approx \mathbf{17,361\text{ QPS}}$$

#### ২. ইনফারেন্স কম্পিউট ও সার্ভার নোড সাইজিং:
- আমাদের টার্গেট লেটেন্সি: **$P99 < 30\text{ ms}$**।
- একটি ৮-কোর CPU সার্ভার ডাইনামিক ব্যাচিং সহ প্রতি সেকেন্ডে $200\text{ QPS}$ হ্যান্ডেল করতে পারে।
- প্রয়োজনীয় পিক ইনস্ট্যান্স সংখ্যা:
  $$\text{Required Pods} = \frac{17,361}{200} \approx \mathbf{87\text{ টি পড}}$$
  (অটোস্কেলিংয়ে পিক আওয়ারে ৯০টি পড এবং অফ-পিকে ৩০টি পড সক্রিয় থাকবে)।

#### ৩. স্টোরেজ রিকোয়ারমেন্টস (Logs & Interactions):
- প্রতি ইন্টারঅ্যাকশন লগের আকার $\approx 1\text{ KB}$ (User ID, Item ID, Timestamp, Context Features)।
- দৈনিক নতুন ডেটা:
  $$\text{Daily Storage} = 500,000,000 \times 1\text{ KB} = 500,000,000\text{ KB} \approx \mathbf{500\text{ GB / Day}}$$
- বার্ষিক স্টোরেজ (S3 Parquet Compressed $\times 0.25$):
  $$\text{Annual Storage} = 500\text{ GB} \times 365 \times 0.25 \approx \mathbf{45.6\text{ TB / Year}}$$

---

# ৭. রোডম্যাপ ক্যাপস্টোন প্রজেক্ট: লার্জ-স্কেল রেকমেন্ডেশন সিস্টেম

```
                                  End-to-End System Architecture (50,000 QPS)
                                  
  Client Device (iOS / Android / Web)
          │
          ▼  [ HTTPS Request: User ID, Location, Context ]
  ┌────────────────────────────────────────────────────────────────────────┐
  │ API Gateway & Edge Proxy (Cloudflare + AWS ALB)                       │
  │ • Authentication, Rate Limiting, Latency Tracking Middleware           │
  └───────────────────────────────────┬────────────────────────────────────┘
                                      │
                                      ▼
  ┌────────────────────────────────────────────────────────────────────────┐
  │ Online Serving Service (FastAPI Kubernetes Pods)                       │
  │                                                                        │
  │  1. Feature Fetching (< 3ms):                                          │
  │     Query Redis Cluster ──► Fetch User History & Context Embeddings    │
  │                                                                        │
  │  2. Stage 1: Retrieval / Candidate Generation (< 6ms):                 │
  │     • Two-Tower Deep Neural Network                                    │
  │     • Query Milvus / ScaNN Vector Database (HNSW Index)                │
  │     • 10,000,000 Items ──► Filter down to 1,000 Candidates             │
  │                                                                        │
  │  3. Stage 2: Heavy Scoring & Ranking (< 12ms):                         │
  │     • Deep & Cross Network (DCN-v2) / Ranker Model                     │
  │     • Joint scoring on 1,000 items with 200 real-time features         │
  │     • 1,000 Candidates ──► Score & Sort down to Top 50 Items           │
  │                                                                        │
  │  4. Stage 3: Re-ranking, Business Logic & Diversity (< 4ms):           │
  │     • Maximal Marginal Relevance (MMR) for Diversity                   │
  │     • Deduplication (remove seen items in last 24h)                    │
  │     • Inject sponsored items (ad revenue compliance)                   │
  │     • Top 50 Items ──► Final Top 10 Personalized Recommendations       │
  └───────────────────────────────────┬────────────────────────────────────┘
                                      │
                                      ▼
  ┌────────────────────────────────────────────────────────────────────────┐
  │ Response JSON: [Item_1, Item_2, ..., Item_10]                          │
  │ Total P99 End-to-End Latency: 25ms!                                    │
  └────────────────────────────────────────────────────────────────────────┘
```

---

### ৭.১ ফলব্যাক মেকানিজম ও গ্রেসফুল ডিগ্রেডেশন (Graceful Degradation):
প্রোডাকশনে সিস্টেম ক্র্যাশ করা গ্রহণযোগ্য নয়। প্রতিটি পর্যায়ে কঠোর ফলব্যাক ডিফাইন করা হয়েছে:

```
[ Primary: Deep Ranker Model (DCN-v2) ] ──(Timeout > 15ms)──► [ Fallback 1: Fast LightGBM Model ]
                                                                             │
                                                                       (Fails / OOM)
                                                                             ▼
                                                               [ Fallback 2: Category Popularity Baseline ]
                                                               (Instant in-memory Redis Cache, Latency: 1ms)
```
- যদি ডীপ লার্নিং র‍্যাঙ্কার কোনো কারণে ১৫ ms-এর মধ্যে উত্তর না দেয়, তবে সার্কিট ব্রেকার তাৎক্ষণিকভাবে মেমরিতে ক্যাশ থাকা **"ক্যাটাগরির শীর্ষ জনপ্রিয় আইটেম (Popularity Baseline)"** পরিবেশন করবে। ব্যবহারকারী কখনোই ফাঁকা স্ক্রিন বা এরর পেজ দেখবে না!

---

# ৮. রোডম্যাপ পাস কন্ডিশন ও আর্কিটেকচারাল ট্রেড-অফ ডিফেন্স

> 🎯 **Roadmap Pass Condition (Page 8):**
> *"Draw the architecture and justify its main choices and trade-offs."*

### 🎓 পাস কন্ডিশন ডিফেন্স ও আর্কিটেকচারাল ট্রেড-অফ জাস্টিফিকেশন:

#### ১. টু-স্টেজ আর্কিটেকচার বনাম একক মোনোলিথিক র‍্যাঙ্কার:
- *বিকল্প:* ১ কোটি আইটেমের ওপর সরাসরি জটিল নিউরাল নেটওয়ার্ক (DCN-v2) চালিয়ে সেরা ১০টি বের করা।
- *ট্রেড-অফ যুক্তি:* ১ কোটি আইটেম প্রতি রিকোয়েস্টে স্কোর করতে কয়েক সেকেন্ড সময় এবং লক্ষ লক্ষ ডলারের জিপিইউ ইনফ্রাস্ট্রাকচার লাগবে। তাই আমরা **Two-Stage** আর্কিটেকচার বেছে নিয়েছি। স্টেজ ১ (Two-Tower) অতি-দ্রুত ভেক্টর স্পেসে কোটি আইটেম থেকে ১০০০টি ক্যান্ডিডেট ছাঁটাই করে (৫ ms); এরপর স্টেজ ২ (ভারী মডেল) কেবল ওই ১০০০টির ওপর গভীর ফিচার ক্রস বিশ্লেষণ করে (১২ ms)। এতে **৯৯.৯% কম্পিউটেশনাল খরচ সাশ্রয় হয়** অথচ নির্ভুলতায় কোনো ছাড় দিতে হয় না।

#### ২. রিয়েল-টাইম অনলাইন স্কোরিং বনাম প্রি-কম্পিউটেড ব্যাচ স্কোরিং:
- *বিকল্প:* রাতে ব্যাচ জবে প্রতিটি ইউজারের জন্য টপ ১০ আইটেম বের করে ডাটাবেসে সেভ রাখা।
- *ট্রেড-অফ যুক্তি:* ব্যাচ স্কোরিং সস্তা হলেও ইউজারের বর্তমান সেশনের আচরণ (যেমন: গত ২ মিনিটে সে কী সার্চ করেছে বা ক্লিক করেছে) ধরতে পারে না। ইকমার্স ও ভিডিও প্ল্যাটফর্মে সেশন ড্রিফট অত্যন্ত দ্রুত ঘটে। তাই আমরা হাইব্রিড পদ্ধতি বেছে নিয়েছি: ক্যান্ডিডেট এম্বেডিং অফলাইনে তৈরি হলেও চূড়ান্ত র‍্যাঙ্কিং রিয়েল-টাইমে Redis থেকে ফিচার নিয়ে ডায়নামিকালি তৈরি হয়।

#### ৩. Redis ফিচার ক্যাশ বনাম লাইভ SQL কোয়েরি:
- *ট্রেড-অফ যুক্তি:* প্রতি সেকেন্ডে ১৭,০০০ পিক রিকোয়েস্টে SQL ডাটাবেসে জয়েন কোয়েরি চালালে ডাটাবেস ক্র্যাশ করবে। Redis ক্লাস্টার মেমরিতে কি-ভ্যালু স্টোর হিসেবে সাব-৫ মিলিসেকেন্ডে ফিচার সার্ভ করে, যা P99 লেটেন্সি ৩০ ms এর নিচে রাখার একমাত্র বাস্তব উপায়।

---

# ৯. প্রোডাকশন-গ্রেড ডিস্ট্রিবিউটেড পাইথন কোড

### ৯.১ PyTorch Distributed Data Parallel (DDP) মাল্টি-জিপিইউ ট্রেনিং স্যুট

```python
"""
distributed_ddp_training.py
Production-grade PyTorch Distributed Data Parallel (DDP) Multi-GPU Training Script
Run via: torchrun --nproc_per_node=4 distributed_ddp_training.py
"""
import os
import torch
import torch.nn as nn
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP
from torch.utils.data import DataLoader, Dataset
from torch.utils.data.distributed import DistributedSampler

class SyntheticRecommenderDataset(Dataset):
    def __init__(self, size=10000, features=64):
        self.X = torch.randn(size, features)
        self.y = torch.randint(0, 2, (size,)).float()

    def __len__(self):
        return len(self.X)

    def __getitem__(self, idx):
        return self.X[idx], self.y[idx]

class FastRanker(nn.Module):
    def __init__(self, in_features=64):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(in_features, 128),
            nn.BatchNorm1d(128),
            nn.ReLU(),
            nn.Linear(128, 1)
        )

    def forward(self, x):
        return self.net(x).squeeze(-1)

def setup_distributed():
    # PyTorch distributed পরিবেশ ইনিশিয়ালাইজেশন
    dist.init_process_group(backend="nccl" if torch.cuda.is_available() else "gloo")
    local_rank = int(os.environ.get("LOCAL_RANK", 0))
    if torch.cuda.is_available():
        torch.cuda.set_device(local_rank)
    return local_rank

def cleanup_distributed():
    dist.destroy_process_group()

def train_distributed_epoch():
    local_rank = setup_distributed()
    device = torch.device(f"cuda:{local_rank}" if torch.cuda.is_available() else "cpu")

    dataset = SyntheticRecommenderDataset()
    # DistributedSampler নিশ্চিত করে যে প্রতিটি GPU ডেটার ভিন্ন ভিন্ন অংশ পাবে
    sampler = DistributedSampler(dataset, shuffle=True)
    dataloader = DataLoader(dataset, batch_size=64, sampler=sampler)

    model = FastRanker().to(device)
    # DDP দিয়ে মডেল র‍্যাপ করা (All-Reduce গ্রেডিয়েন্ট সিঙ্ক)
    ddp_model = DDP(model, device_ids=[local_rank] if torch.cuda.is_available() else None)

    criterion = nn.BCEWithLogitsLoss()
    optimizer = torch.optim.AdamW(ddp_model.parameters(), lr=1e-3)

    ddp_model.train()
    sampler.set_epoch(1) # প্রতি এপোকে সিড পরিবর্তন
    total_loss = 0.0

    for X_b, y_b in dataloader:
        X_b, y_b = X_b.to(device), y_b.to(device)
        optimizer.zero_grad()
        logits = ddp_model(X_b)
        loss = criterion(logits, y_b)
        loss.backward() # স্বয়ংক্রিয় Ring All-Reduce এক্সিকিউশন
        optimizer.step()
        total_loss += loss.item()

    if local_rank == 0:
        print(f"✅ [Rank 0 Master] Epoch সম্পন্ন! গড় ক্ষতি: {total_loss / len(dataloader):.4f}")
        # মাস্টার নোড চেকপয়েন্ট সংরক্ষণ করবে
        torch.save(ddp_model.module.state_dict(), "ddp_checkpoint.pt")

    cleanup_distributed()

if __name__ == "__main__":
    if "RANK" in os.environ:
        train_distributed_epoch()
    else:
        print("💡 এই স্ক্রিপ্টটি torchrun দিয়ে চালাতে হয়:\n   torchrun --nproc_per_node=2 distributed_ddp_training.py")
```

---

### ৯.২ নলেজ ডিস্টিলেশন (Knowledge Distillation) ট্রেইনিং স্যুট

```python
"""
knowledge_distillation.py
Hinton-style Knowledge Distillation with Temperature-Scaled KL Divergence
"""
import torch
import torch.nn as nn
import torch.nn.functional as F

class TeacherModel(nn.Module):
    def __init__(self):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(32, 256),
            nn.ReLU(),
            nn.Linear(256, 128),
            nn.ReLU(),
            nn.Linear(128, 5) # ৫টি ক্লাস
        )
    def forward(self, x):
        return self.net(x)

class StudentModel(nn.Module):
    def __init__(self):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(32, 32), # ৫ গুণ ছোট
            nn.ReLU(),
            nn.Linear(32, 5)
        )
    def forward(self, x):
        return self.net(x)

def distillation_loss(student_logits, teacher_logits, labels, T=4.0, alpha=0.7):
    """
    হিন্টন ডিস্টিলেশন লস: সফট টার্গেট KL ডাইভারজেন্স + হার্ড টার্গেট ক্রস-এন্ট্রপি
    """
    # ১. সফট টার্গেট লস (Teacher-Student KL Divergence)
    p_teacher = F.softmax(teacher_logits / T, dim=-1)
    log_p_student = F.log_softmax(student_logits / T, dim=-1)
    kl_loss = F.kl_div(log_p_student, p_teacher, reduction='batchmean') * (T * T)

    # ২. হার্ড টার্গেট লস (Ground Truth Cross Entropy)
    ce_loss = F.cross_entropy(student_logits, labels)

    # ৩. কম্বাইন্ড উত্তল লস
    return alpha * kl_loss + (1.0 - alpha) * ce_loss

if __name__ == "__main__":
    torch.manual_seed(42)
    teacher = TeacherModel()
    student = StudentModel()

    dummy_input = torch.randn(16, 32)
    dummy_labels = torch.randint(0, 5, (16,))

    # টিচার ইনফারেন্স (ফ্রিজড)
    with torch.no_grad():
        t_logits = teacher(dummy_input)

    # স্টুডেন্ট ফরওয়ার্ড
    s_logits = student(dummy_input)
    loss = distillation_loss(s_logits, t_logits, dummy_labels, T=4.0, alpha=0.7)
    
    print("✅ নলেজ ডিস্টিলেশন লস সফলভাবে হিসাব করা হয়েছে:", round(float(loss.item()), 4))
```

---

## 🎯 সংক্ষেপ ও সারসংক্ষেপ
- **ক্লাউড ইনফ্রাস্ট্রাকচার:** প্রাইভেট সাবনেট ও IRSA Least-Privilege দিয়ে ক্লাউড সুরক্ষিত করা হয়; MIG দিয়ে জিপিইউ ইউটিলাইজেশন অপটিমাইজ করা হয়।
- **ডেটা ইঞ্জিনিয়ারিং:** Lakehouse (Delta Lake) ট্রানজ্যাকশন ও টাইম-ট্রাভেল নিশ্চিত করে; Feast ফিচার স্টোর ট্রেনিং-সার্ভিং স্কিউ দূর করে।
- **স্কেলিং ও ইনফারেন্স:** Triton ডাইনামিক ব্যাচিং দিয়ে থ্রুপুট ৮ গুণ বৃদ্ধি পায়; HPA কাস্টম QPS মেট্রিক্সে পড স্কেল করে।
- **ডিস্ট্রিবিউটেড ট্রেনিং:** DDP Ring All-Reduce কমিউনিকেশন স্থির রাখে; FSDP/ZeRO-3 মেমরিকে শার্ডিং করে বড় মডেল ট্রেনিং সম্ভব করে।
- **এমএল সিস্টেম ডিজাইন:** ব্যাক-অব-দ্য-এনভেলপ গণিত দিয়ে ট্রাফিক ও সার্ভার নোড সাইজ নির্ধারণ করতে হয়; থ্রি-স্টেজ আর্কিটেকচার (Retrieval $\to$ Ranking $\to$ Diversity) কোটি কোটি আইটেমকে ২০ ms-এ পরিবেশন করে।

---
*পরবর্তী অধ্যায়:* [👉 অধ্যায় ১৪: পোর্টফোলিও প্রজেক্ট ও ইন্টারভিউ প্রস্তুতি (Phase 12)](file:///g:/MNC/ML/14_portfolio_projects_and_interview_mastery.md) | [📚 মাস্টার হ্যান্ডবুক সূচিপত্র](file:///g:/MNC/ML/README.md)
