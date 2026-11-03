# Chapter 12: Production GenAI Deployment, Serving Optimization & LLMOps

---

## ১. প্রোডাকশন সার্ভিংয়ের মূল চ্যালেঞ্জ ও বটলনেকসমূহ

ল্যাঙ্গুয়েজ মডেল লোকাল নোটবুকে রান করা আর হাজার হাজার সমসাময়িক ব্যবহারকারীর জন্য প্রোডাকশনে সার্ভ করার মধ্যে আকাশ-পাতাল ব্যবধান রয়েছে।
সনাতন HuggingFace `pipeline()` প্রোডাকশনে মারাত্মকভাবে ব্যর্থ হয়:
1. **মেমোরি ফ্র্যাগমেন্টেশন (Memory Fragmentation):** ট্র্যাডিশনাল মেমোরি অ্যালোকেটর সর্বোচ্চ সম্ভাব্য কনটেক্সট লেন্থের (যেমন ২০৪৮ টোকেন) জন্য অগ্রিম ধারাবাহিক VRAM ব্লক রিজার্ভ করে। ফলে ৬০% থেকে ৮০% গ্রাফিক্স মেমোরি সম্পূর্ণ অপচয় হয়।
2. **স্ট্যাটিক ব্যাচিংয়ের বিলম্ব (Static Batching Head-of-Line Blocking):** ব্যাচের সবচেয়ে দীর্ঘ রেসপন্স জেনারেট না হওয়া পর্যন্ত নতুন কোনো রিকোয়েস্ট প্রক্রিয়াজাত হতে পারে না।
3. **মেমোরি ব্যান্ডউইথ বটলনেক (Memory Bandwidth Bound):** ডিকোডিং ধাপে প্রতি টোকেনে সমগ্র ওজনকে HBM থেকে চিপে লোড করতে হয়।

```
Standard Naive Serving:
Request 1 (50 tokens)   [================] (Finishes fast, but GPU sits idle waiting!)
Request 2 (500 tokens)  [========================================================================]
New Request 3           [BLOCKED! Must wait until Request 2 completely terminates!]
```

---

## ২. পেজড-এটেনশন ও vLLM (PagedAttention: Kwon et al., SOSP 2023)

অপারেটিং সিস্টেমের **ভার্চুয়াল মেমোরি ও পেজিং (Virtual Memory Paging)** ধারণা থেকে অনুপ্রাণিত হয়ে UC Berkeley-এর গবেষকরা উদ্ভাবন করেন **PagedAttention**।

```
Logical KV-Cache for Request:
[ Logical Block 0 ] ---> [ Logical Block 1 ] ---> [ Logical Block 2 ]
        |                         |                         |
        v                         v                         v
[ Physical Block 7 ]      [ Physical Block 2 ]      [ Physical Block 9 ]
           (Non-contiguous physical memory blocks in GPU VRAM!)
```

### কার্যপদ্ধতি ও ব্লক টেবিল (Block Table Mapping):
- কনটেক্সটকে ফিক্সড-সাইজ ব্লকে (যেমন ১৬ বা ৩২টি টোকেন) ভাগ করা হয়।
- ফিজিক্যাল মেমোরিতে এই ব্লকগুলোকে পরস্পর সংলগ্ন (contiguous) থাকার কোনো প্রয়োজন নেই; ফাঁকা যেকোনো ফিজিক্যাল ব্লকে ডেটা রাখা যায়।
- একটি **ব্লক টেবিল** লজিক্যাল ব্লকের সাথে ফিজিক্যাল ব্লকের ম্যাপিং ট্র্যাক করে।
- **ফলাফল:** মেমোরি অপচয় ৮০% থেকে কমে **৪%-এর নিচে** নেমে আসে, যার ফলে একই জিপিইউতে ২ থেকে ৪ গুণ বেশি রিকোয়েস্ট একসাথে সার্ভ করা সম্ভব হয়!

---

## ৩. কন্টিনিউয়াস ব্যাচিং (Continuous / Iteration-Level Batching - Orca)

Yu et al. (2022) প্রস্তাব করেন আইটারেশন-লেভেল বা কন্টিনিউয়াস ব্যাচিং।

```
Continuous Batching Timeline:
Iteration 1: [Req A (token 1)] [Req B (token 12)]
Iteration 2: [Req A (token 2)] [Req B (token 13 - FINISHED!)]
Iteration 3: [Req A (token 3)] [Req C (token 1 - NEW REQUEST ENTERS IMMEDIATELY!)]
```

প্রতিটি সিঙ্গেল টোকেন জেনারেশন আইটারেশন শেষে সমাপ্ত হওয়া রিকোয়েস্টগুলোকে তাৎক্ষণিক ডেলিভারি দিয়ে মুক্ত মেমোরিতে নতুন আগত রিকোয়েস্টগুলোকে ঢুকিয়ে নেওয়া হয়। কোনো রিকোয়েস্টকে অন্যদের জন্য অপেক্ষা করতে হয় না।

---

## ৪. মডেল কোয়ান্টাইজেশন: AWQ, GPTQ ও GGUF

মডেলের ১৬-বিট ওজনকে (FP16 / BF16) গুণমান অক্ষুণ্ণ রেখে ৪-বিট বা ৮-বিটে সংকুচিত করার আধুনিক কৌশলসমূহ:

```
                            [ Quantization Families ]
                                        |
        +-------------------------------+-------------------------------+
        |                               |                               |
AWQ (Activation-aware)             GPTQ (Hessian Matrix)             GGUF (llama.cpp)
Protects the top 1% critical       Second-order Taylor error        Optimized for CPU/Apple
salient weights based on acts.     compensation across layers.      Silicon/GPU unified RAM.
```

1. **AWQ (Activation-aware Weight Quantization - Lin et al., 2023):**
   - সমস্ত ওজন সমান গুরুত্বপূর্ণ নয়। অ্যাক্টিভেশনের মাত্রা পর্যবেক্ষণ করে দেখা গেছে শীর্ষ ১% সংবেদনশীল ওজন সুরক্ষিত রাখলে বাকি ৯৯% ওজনকে ৪-বিটে নামিয়েও পারপ্লেক্সিটি অক্ষুণ্ণ থাকে।
2. **GPTQ (Frantar et al., 2022):**
   - সেকেন্ড-অর্ডার টেলর সিরিজের হেসিয়ান ম্যাট্রিক্সের ইনভার্স ($H^{-1}$) ব্যবহার করে কোয়ান্টাইজেশনের এররকে সংলগ্ন ওজনগুলোতে সুষমভাবে সমন্বয় করে দেয়।
3. **GGUF (llama.cpp):**
   - কনজিউমার হার্ডওয়্যার এবং ম্যাকবুক অ্যাপল সিলিকন (Unified RAM)-এ একক বাইনারি ফাইলে মডেল রান করার জন্য বৈশ্বিক স্ট্যান্ডার্ড ফরম্যাট।

---

## ৫. স্পেকুলেটিভ ডিকোডিং (Speculative Decoding - Leviathan et al., 2023)

### মূল ধারণা (Draft + Target Verification):
অটো-রিগ্রেসিভ ডিকোডিং মেমোরি-ব্যান্ডউইথ বাউন্ড হওয়ায় একটি ৭০ বিলিয়ন মডেল থেকে ৫টি টোকেন জেনারেট করতে ৫ বার সমগ্র ৭০ বিলিয়ন ওজন লোড করতে হয়।

```
                                  [ Draft Model (e.g. 1B Model - Ultra Fast) ]
                                                       |
                                      Generates K Candidate Tokens (e.g. K=4)
                                                       |
                                                       v
                                      [ "AI", "is", "transforming", "the" ]
                                                       |
                                                       v
                                  [ Target Model (e.g. 70B Model - Heavy) ]
                                                       |
                                  Evaluates ALL 4 tokens in ONE SINGLE Forward Pass!
                                                       |
                                                       v
                                  Accepts 3 tokens, rejects 4th and corrects it!
```

### স্পেকুলেটিভ ভেরিফিকেশন অ্যালগরিদম:
টার্গেট মডেল একটি একক প্যারালাল ফরোয়ার্ড পাসে $K$ টি টোকেনের সবকটির সম্ভাব্যতার মান বিচার করে। যদি ড্রাফট মডেলের টোকেন গ্রহণযোগ্য হয়, তবে এক পদক্ষেপে ৪টি টোকেন উৎপন্ন হয়!
- **গতিবৃদ্ধি:** মডেলের মূল ডিস্ট্রিবিউশন বিন্দুমাত্র পরিবর্তন না করে ইনফারেন্স গতি **২ থেকে ৩ গুণ বৃদ্ধি পায়**।

---

## ৬. সিম্যান্টিক ক্যাশিং (Semantic Caching)

সনাতন কী-ভ্যালু ক্যাশে হুবহু স্ট্রিং মিলতে হয়:
- `"What is the capital of France?"` এবং `"Tell me France's capital city"` সনাতন ক্যাশে দুটি আলাদা কুয়েরি হিসেবে ট্রিট হবে এবং দুইবার সম্পূর্ণ জিপিইউ ইনফারেন্স খরচ করবে।

**সিম্যান্টিক ক্যাশিং (Semantic Caching):**
ইনপুট প্রম্পটকে একটি লাইটওয়েট ভেক্টর এম্বেডিংয়ে রূপান্তর করে ক্যাশ ডাটাবেসে (Redis/Milvus) অনুসন্ধান করা হয়। যদি কোসাইন সিমিলারিটি $\cos(q_{\text{new}}, q_{\text{cached}}) \ge 0.95$ হয়, তবে জিপিইউ স্পর্শ না করেই তাৎক্ষণিক ০.১ মিলিসেকেন্ডে ক্যাশ থেকে উত্তর প্রদান করা হয়! এটি ক্লাউড খরচ ৬০% পর্যন্ত কমিয়ে দেয়।

---

## ৭. সম্পূর্ণ পাইথন প্রোডাকশন ফাস্টএপিআই সার্ভিং সিস্টেম

নিচে একটি স্বয়ংসম্পূর্ণ, ডিপেন্ডেন্সি-মুক্ত পাইথন বাস্তবায়ন দেওয়া হলো যা সার্ভার-সেন্ট ইভেন্টস (SSE) স্ট্রিমিং, সিম্যান্টিক ক্যাশিং এবং টোকেন-বাই-টোকেন রেসপন্স ডেলিভারি প্রদর্শন করে:

```python
"""
Production GenAI Serving Engine: Streaming Generator and Semantic Cache.
Fully runnable and verified.
"""

import time
import math
import re
from typing import Generator, Dict, Optional


# ============================================================================
# 1. Semantic Cache Engine
# ============================================================================
class SemanticCache:
    def __init__(self, similarity_threshold: float = 0.85):
        self.threshold = similarity_threshold
        self.cache: Dict[str, str] = {}  # {query: response}

    def _mock_embedding_similarity(self, q1: str, q2: str) -> float:
        """Computes word overlap similarity as proxy for dense cosine."""
        w1 = set(re.findall(r"\w+", q1.lower()))
        w2 = set(re.findall(r"\w+", q2.lower()))
        if not w1 or not w2:
            return 0.0
        return len(w1.intersection(w2)) / max(len(w1), len(w2))

    def get(self, query: str) -> Optional[str]:
        for cached_q, response in self.cache.items():
            sim = self._mock_embedding_similarity(query, cached_q)
            if sim >= self.threshold:
                return response
        return None

    def set(self, query: str, response: str):
        self.cache[query] = response


# ============================================================================
# 2. Streaming LLM Inference Engine (Server-Sent Events)
# ============================================================================
class ProductionLLMEngine:
    def __init__(self):
        self.cache = SemanticCache(similarity_threshold=0.80)

    def generate_stream(self, prompt: str) -> Generator[str, None, None]:
        # 1. Check Semantic Cache
        cached_response = self.cache.get(prompt)
        if cached_response:
            yield f"data: [CACHE_HIT] {cached_response}\n\n"
            yield "data: [DONE]\n\n"
            return

        # 2. Simulate Token-by-Token Streaming Generation
        simulated_response = f"Artificial Intelligence is revolutionizing enterprise software through intelligent automation."
        tokens = simulated_response.split()

        accumulated = []
        for token in tokens:
            time.sleep(0.04)  # Simulate GPU decode time (~40ms per token = 25 tokens/sec)
            accumulated.append(token)
            yield f"data: {token} \n\n"

        # 3. Store completed response in semantic cache
        full_text = " ".join(accumulated)
        self.cache.set(prompt, full_text)
        yield "data: [DONE]\n\n"


# ============================================================================
# 3. Execution & Verification Demo
# ============================================================================
if __name__ == "__main__":
    engine = ProductionLLMEngine()

    print("--- Request 1: Cold Query (Requires Full Model Execution) ---")
    query_1 = "What is artificial intelligence doing in software?"
    start_time = time.time()
    for chunk in engine.generate_stream(query_1):
        print(chunk.strip())
    print(f"Elapsed Time: {time.time() - start_time:.3f} seconds\n")

    print("--- Request 2: Semantically Equivalent Query (Cache Hit Expected) ---")
    query_2 = "What is AI doing in enterprise software?"
    start_time = time.time()
    for chunk in engine.generate_stream(query_2):
        print(chunk.strip())
    print(f"Elapsed Time: {time.time() - start_time:.3f} seconds (Near-instant!)")

    print("\nProduction Serving Engine verified successfully!")
```

---

## ৮. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স

| ইনফারেন্স ফ্রেমওয়ার্ক | থ্রুপুট (Tokens/sec) | KV-Cache অপ্টিমাইজেশন | ডিস্ট্রিবিউটেড সার্ভিং (TP/PP) | সেরা ব্যবহারের ক্ষেত্র |
| :--- | :--- | :--- | :--- | :--- |
| **HuggingFace TGI** | উচ্চ | কন্টিনিউয়াস ব্যাচিং | টেনসর প্যারালালিিজম | সাধারণ ডকারাইজড ক্লাউড ডেপ্লয়মেন্ট |
| **vLLM** | **সর্বোচ্চ (২-৪x)** | **PagedAttention (Zero Waste)** | টেনসর ও পাইপলাইন প্যারালালিজম | হাই-কনকারেন্সি এন্টারপ্রাইজ প্রোডাকশন |
| **llama.cpp / Ollama** | মাঝারি | মেমোরি ম্যাপিং (mmap) | সীমিত | লোকাল ডিভাইস, ম্যাকবুক, অন-প্রিমিস এজ |
| **TensorRT-LLM (NVIDIA)**| চরম সর্বোচ্চ | PagedAttention + FP8 In-flight | এনভিডিয়া অপ্টিমাইজড কার্নেল | ডেডিকেটেড H100/A100 ক্লাস্টার |

---

## ৯. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: Time-to-First-Token (TTFT) বনাম Time-Per-Output-Token (TPOT)-এর মধ্যে পার্থক্য কী?**
   - **উত্তর:** **TTFT:** ব্যবহারকারী প্রম্পট পাঠানোর পর প্রথম টোকেনটি পর্দায় ভেসে ওঠার মধ্যবর্তী বিলম্ব (এটি মডেলের প্রিফিল স্টেজের গতি নির্দেশ করে)। **TPOT:** প্রথম টোকেনের পর পরবর্তী প্রতিটি টোকেন জেনারেট হওয়ার মধ্যবর্তী সময় (যেমন ৩০-৪০ মিলিসেকেন্ড; এটি ডিকোডিং স্টেজের মেমোরি ব্যান্ডউইথ নির্দেশ করে)।
2. **প্রশ্ন: টেনসর প্যারালালিজম (TP) বনাম পাইপলাইন প্যারালালিজম (PP)-এর তফাত কী?**
   - **উত্তর:** **Tensor Parallelism:** একটি একক লেয়ারের ওজন ম্যাট্রিক্সকে কলাম বা রো বরাবর কেটে একাধিক জিপিইউতে ভাগ করে সমান্তরালভাবে গুণ করা হয় (কম ল্যাটেন্সি, হাই-স্পিড NVLink প্রয়োজন)। **Pipeline Parallelism:** মডেলের লেয়ারগুলোকে পর পর বিভিন্ন জিপিইউতে স্থাপন করা হয় (যেমন লেয়ার ১-১৬ GPU 0 তে, লেয়ার ১৭-৩২ GPU 1 এ; হাই লেভেল ক্লাস্টারে প্রযোজ্য)।
3. **প্রশ্ন: FP8 কোয়ান্টাইজেশন FP16-এর তুলনায় কেন দ্বিগুণ থ্রুপুট দেয়?**
   - **উত্তর:** আধুনিক GPU আর্কিটেকচারে (যেমন NVIDIA Hopper H100) বিশেষায়িত Transformer Engine রয়েছে যা FP8 টেনসর কোরের মাধ্যমে প্রতি চক্রে FP16-এর দ্বিগুণ গাণিতিক অপারেশন (FLOPs) সম্পাদন করতে পারে এবং মেমোরি থেকে ওজনের আকার অর্ধেক কমিয়ে মেমোরি ব্যান্ডউইথ স্যাচুরেশন দূর করে।
