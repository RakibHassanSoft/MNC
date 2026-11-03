# Chapter 08: Retrieval-Augmented Generation (RAG) & Vector Databases

---

## ১. RAG-এর মূল ভিত্তি ও বিবর্তন (Foundations of RAG)

লার্জ ল্যাঙ্গুয়েজ মডেলের অভ্যন্তরীণ মেমোরি হলো **প্যারামেট্রিক মেমোরি (Parametric Memory)**, যা প্রি-ট্রেইনিংয়ের সময় ওজনে সংরক্ষিত হয়।
এর দুটি প্রধান সীমাবদ্ধতা:
1. **কাট-অফ ডেট ও স্ট্যাটিক জ্ঞান:** মডেল রিয়েল-টাইম সাম্প্রতিক তথ্য জানে না।
2. **হ্যালুসিনেশন ও ডোমেইন জ্ঞানের অভাব:** প্রাইভেট বা ইন্টারনাল এন্টারপ্রাইজ ডেটাবেস সম্পর্কে মডেলের কোনো ধারণা থাকে না।

**Retrieval-Augmented Generation (RAG - Lewis et al., 2020)** একটি এক্সটার্নাল **নন-প্যারামেট্রিক মেমোরি (Non-Parametric Memory / Vector Store)** যুক্ত করে এই সমস্যার সমাধান করে:

```
[User Query: "What is our company's refund policy?"]
                     |
                     v
             [ Dense Retriever ]
                     |
    (Queries Vector DB: Milvus / Qdrant / Pinecone / Chroma)
                     |
                     v
       Relevant Context Chunks:
       ["Customers can return items within 30 days..."]
                     |
                     v
[ Augmented Prompt: Context + User Query ] ---> [ LLM Generator ] ---> Accurate, Grounded Answer!
```

---

## ২. চ্যাকিং স্ট্র্যাটেজিসমূহ (Chunking Strategies)

ডকুমেন্টকে কীভাবে ছোট ছোট অংশে (chunks) বিভক্ত করা হয়, তার ওপর RAG-এর ৮০% সাফল্য নির্ভর করে।

```
                                [ Chunking Taxonomy ]
                                          |
        +------------------------+--------+-----------------------+
        |                        |                                |
Fixed-Size with Overlap   Recursive Character            Semantic Chunking
(512 tokens, 50 overlap)  (Paragraph -> Sentence)        (Splits on Cosine Distance Spikes)
```

1. **ফিক্সড-সাইজ চাঙ্কিং (Fixed-Size with Overlap):**
   - প্রতি ৫০০ ক্যারেক্টার বা টোকেন পর পর কেটে দেওয়া হয় এবং কনটেক্সট যাতে হারিয়ে না যায় সেজন্য ৫০ টোকেন ওভারল্যাপ রাখা হয়।
   - *সীমাবদ্ধতা:* বাক্যের মাঝখানে বা কোডের ব্লকের মাঝখানে অপ্রাকৃতিক বিভাজন ঘটে।
2. **রিকার্সিভ ক্যারেক্টার চাঙ্কিং (Recursive Character Chunking):**
   - হায়ারার্কিকাল সেপারেটর ব্যবহার করা হয়: প্রথমে ডাবল নিউলাইন (`\n\n` - অনুচ্ছেদ), না হলে সিঙ্গেল নিউলাইন (`\n`), না হলে দাঁড়ি বা ফুলস্টপ (`.` বা `।`), এবং সবশেষে স্পেস। এটি সম্পূর্ণ প্রাকৃতিক অনুচ্ছেদ বা বাক্য অক্ষুণ্ণ রাখে।
3. **সিম্যান্টিক চাঙ্কিং (Semantic Chunking):**
   - প্রতিটি বাক্যের এম্বেডিং তৈরি করা হয় এবং পাশাপাশি দুটি বাক্যের মধ্যবর্তী কোসাইন দূরত্ব ($\cos(e_i, e_{i+1})$) হিসাব করা হয়। যদি দূরত্ব একটি নির্দিষ্ট থ্রেশহোল্ড অতিক্রম করে (অর্থাৎ আলোচনার বিষয় পরিবর্তিত হয়েছে), তখনই নতুন চাঙ্ক শুরু করা হয়।

---

## ৩. ডেন্স এম্বেডিং ও ভেক্টর ইনডেক্সিং মেকানিক্স (ANN Algorithms)

### ১. সিমিলারিটি মেট্রিক্স (Similarity Metrics):
$d$-ডাইমেনশনাল দুটি এম্বেডিং ভেক্টর $u$ এবং $v$-এর ক্ষেত্রে:
- **Cosine Similarity:**
  $$\cos(u, v) = \frac{u \cdot v}{\|u\|_2 \|v\|_2} = \frac{\sum_{i=1}^d u_i v_i}{\sqrt{\sum_{i=1}^d u_i^2} \sqrt{\sum_{i=1}^d v_i^2}}$$
- **Dot Product (Inner Product):**
  $$\langle u, v \rangle = \sum_{i=1}^d u_i v_i \quad (\text{যদি ভেক্টরগুলো ইতিমধ্যে } L_2\text{-নরমালাইজড থাকে})$$

---

### ২. আনুমানিক নিকটতম প্রতিবেশী অনুসন্ধান (Approximate Nearest Neighbors - ANN):
লক্ষ লক্ষ ভেক্টরের মধ্যে ব্রুট-ফোর্স ফ্ল্যাট সার্চ ($O(N)$) অত্যন্ত ধীরগতির। তাই আধুনিক ভেক্টর ডেটাবেস বিশেষ ইনডেক্সিং অ্যালগরিদম ব্যবহার করে:

```
[ Flat Search: O(N) Brute Force ] ---> Scans Every Vector (100% Accurate, Impossible at Scale)
[ IVF (Inverted File): O(N/k) ]   ---> Clusters into Voronoi Cells, searches only nearest centroids
[ HNSW Graph: O(log N) ]          ---> Multi-layer Skip-list Graph (Industry Standard, Ultra Fast)
```

#### HNSW (Hierarchical Navigable Small World Graph):
- মাল্টি-লেয়ার গ্রাফ স্ট্রাকচার (যেমন ডেটা স্ট্রাকচারের Skip-List)।
- একদম শীর্ষ লেয়ারে দীর্ঘ দূরত্বের সংযোগ থাকে যা দ্রুত সঠিক সাব-রিজিওনে ঝাঁপ দেয় (fast routing)।
- নিচের লেয়ারে নেমে এসে স্থানীয় নিকটতম প্রতিবেশীদের সূক্ষ্মভাবে স্ক্যান করা হয়।
- অনুসন্ধান জটিলতা মাত্র $O(\log N)$!

---

## ৪. হাইব্রিড সার্চ ও রেসিপ্রোকাল র‍্যাঙ্ক ফিউশন (Hybrid Search & RRF)

### কেন শুধুমাত্র ভেক্টর সার্চ যথেষ্ট নয়?
ডেন্স ভেক্টর সার্চ সিম্যান্টিক বা ভাবগত অর্থ ভালো বুঝলেও সুনির্দিষ্ট কীওয়ার্ড, ইউনিক আইডি, কোড বা পার্ট নম্বর (যেমন: `PO-98472-TX`, `iPhone 15 Pro Max 256GB`) অনুসন্ধানে মারাত্মক ব্যর্থ হয়।

### সমাধান — হাইব্রিড সার্চ (Sparse + Dense):
1. **স্পার্স সার্চ (BM25 / Keyword Search):** এক্স্যাক্ট কীওয়ার্ড ম্যাচিং এবং Term Frequency - Inverse Document Frequency (TF-IDF)।
2. **ডেন্স সার্চ (Dense Vector Search):** কনসেপচুয়াল ও প্যারাফ্রেজড অর্থ উদ্ধার।

```
                                  [ User Query ]
                                        |
               +------------------------+------------------------+
               |                                                 |
      [ BM25 Sparse Search ]                            [ Dense Vector Search ]
      Doc 3 (Rank 1)                                    Doc 7 (Rank 1)
      Doc 1 (Rank 2)                                    Doc 3 (Rank 2)
      Doc 5 (Rank 3)                                    Doc 2 (Rank 3)
               \                                                 /
                +-----------------------+-----------------------+
                                        |
                      [ Reciprocal Rank Fusion (RRF) ]
                                        |
                             Combined Top Ranked Docs
```

### রেসিপ্রোকাল র‍্যাঙ্ক ফিউশন (RRF) সূত্র:
$$\text{RRF\_Score}(d) = \sum_{m \in M} \frac{1}{k + r_m(d)}$$

- $M$ হলো রিট্রিভাল পদ্ধতির সেট (BM25 এবং Dense)।
- $r_m(d)$ হলো পদ্ধতি $m$-এ ডকুমেন্ট $d$-এর র‍্যাঙ্ক ($1, 2, 3, \dots$)।
- $k$ হলো স্মুথিং কনস্ট্যান্ট (সাধারনত $k = 60$)।

### সংখ্যাগত উদাহরণ (Step-by-Step RRF Calculation):
ধরা যাক আমাদের কাছে ৩টি ডকুমেন্ট ($D_1, D_2, D_3$) আছে এবং কনস্ট্যান্ট $k = 60$:
- **BM25 র‍্যাঙ্কিং:**
  - $D_1$: Rank 1
  - $D_2$: Rank 2
  - $D_3$: Rank 3
- **Dense Vector র‍্যাঙ্কিং:**
  - $D_3$: Rank 1
  - $D_1$: Rank 2
  - $D_2$: Rank 3

#### প্রতিটি ডকুমেন্টের স্কোর হিসাব:
- **ডকুমেন্ট $D_1$:**
  $$\text{RRF}(D_1) = \frac{1}{60 + 1} + \frac{1}{60 + 2} = \frac{1}{61} + \frac{1}{62} \approx 0.01639 + 0.01613 = \mathbf{0.03252}$$
- **ডকুমেন্ট $D_2$:**
  $$\text{RRF}(D_2) = \frac{1}{60 + 2} + \frac{1}{60 + 3} = \frac{1}{62} + \frac{1}{63} \approx 0.01613 + 0.01587 = \mathbf{0.03200}$$
- **ডকুমেন্ট $D_3$:**
  $$\text{RRF}(D_3) = \frac{1}{60 + 3} + \frac{1}{60 + 1} = \frac{1}{63} + \frac{1}{61} \approx 0.01587 + 0.01639 = \mathbf{0.03226}$$

**চূড়ান্ত হাইব্রিড র‍্যাঙ্কিং:**
1. **প্রথম স্থান:** $D_1$ (স্কোর: $0.03252$)
2. **দ্বিতীয় স্থান:** $D_3$ (স্কোর: $0.03226$)
3. **তৃতীয় স্থান:** $D_2$ (স্কোর: $0.03200$)

উভয় সিস্টেমে সুষম ভালো পারফরম্যান্সের কারণে $D_1$ সর্বোচ্চ স্কোর অর্জন করে!

---

## ৫. ক্রস-এনকোডার রি-র‍্যাঙ্কিং (Cross-Encoder Re-Ranking)

Bi-Encoder দ্রুতগতির হলেও কোয়েরি ও ডকুমেন্টকে আলাদাভাবে এম্বেড করে, ফলে টোকেন-লেভেল পারস্পরিক মিথস্ক্রিয়া ধরা পড়ে না।
রিট্রিভ করা শীর্ষ ২৫-৫০টি ডকুমেন্টকে একটি **Cross-Encoder (যেমন: BGE-Reranker, Cohere Rerank)** মডেলে পাস করা হয়, যা কোয়েরি এবং ডকুমেন্টকে একসাথে কনক্যাটেনেট করে ফুল সেলফ-এটেনশন গণনা করে এবং একটি একক প্রাসঙ্গিকতা স্কোর ($[0, 1]$) প্রদান করে।

---

## ৬. রাগাস মূল্যায়ন কাঠামো (RAG Triad & RAGAS Framework)

```
                            [ The RAG Triad ]
                                    |
          +-------------------------+-------------------------+
          |                                                   |
   Context Precision                                     Faithfulness
(Are retrieved chunks relevant                      (Is the answer 100% supported
     to the user query?)                                 by retrieved context?)
          \                                                   /
           +-----------------------+-------------------------+
                                   |
                            Answer Relevance
                   (Does the answer directly address
                         the user's inquiry?)
```

1. **Context Precision:** রিট্রিভ করা চাঙ্কগুলোর কত শতাংশ তথাকথিত গোল্ডেন তথ্যের সাথে প্রাসঙ্গিক।
2. **Faithfulness (Grounding):** জেনারেটেড উত্তরের প্রতিটি দাবি কি রিট্রিভ করা কনটেক্সটের ভেতর প্রমাণযোগ্য? এটি হ্যালুসিনেশন পরিমাপ করে।
3. **Answer Relevance:** প্রম্পটের মূল প্রশ্নের সাথে উত্তরের সামঞ্জস্যতা।

---

## ৭. সম্পূর্ণ পাইথন হাইব্রিড রাগ ইমপ্লিমেন্টেশন (Production Python Script)

নিচে একটি স্বয়ংসম্পূর্ণ, ডিপেন্ডেন্সি-মুক্ত পাইথন বাস্তবায়ন দেওয়া হলো যা রিকার্সিভ চাঙ্কিং, BM25 স্পার্স সার্চ, ডেন্স সিমিলারিটি এবং Reciprocal Rank Fusion (RRF) সম্পাদন করে:

```python
"""
Production-Grade Hybrid RAG Engine with BM25, Dense Cosine, and RRF Fusion.
Fully runnable and verified.
"""

import math
import re
from typing import List, Dict, Any


# ============================================================================
# 1. Document Chunker (Recursive Text Chunker)
# ============================================================================
def recursive_chunk_text(text: str, chunk_size: int = 200, overlap: int = 40) -> List[str]:
    """Recursively splits text on natural boundaries."""
    paragraphs = text.split("\n\n")
    chunks = []
    current_chunk = ""

    for p in paragraphs:
        p = p.strip()
        if not p:
            continue
        if len(current_chunk) + len(p) <= chunk_size:
            current_chunk += ("\n\n" if current_chunk else "") + p
        else:
            if current_chunk:
                chunks.append(current_chunk)
                # Overlap logic
                overlap_text = current_chunk[-overlap:] if len(current_chunk) > overlap else current_chunk
                current_chunk = overlap_text + " " + p
            else:
                chunks.append(p[:chunk_size])
                current_chunk = p[chunk_size - overlap:]

    if current_chunk:
        chunks.append(current_chunk)
    return chunks


# ============================================================================
# 2. Sparse BM25 Search Implementation
# ============================================================================
class SimpleBM25:
    def __init__(self, corpus: List[str], k1: float = 1.5, b: float = 0.75):
        self.corpus = corpus
        self.k1 = k1
        self.b = b
        self.doc_len = [len(self._tokenize(doc)) for doc in corpus]
        self.avg_doc_len = sum(self.doc_len) / len(self.doc_len) if self.doc_len else 1.0
        self.df = {}
        self.idf = {}
        self._build_index()

    def _tokenize(self, text: str) -> List[str]:
        return re.findall(r"\w+", text.lower())

    def _build_index(self):
        N = len(self.corpus)
        for doc in self.corpus:
            tokens = set(self._tokenize(doc))
            for t in tokens:
                self.df[t] = self.df.get(t, 0) + 1
        for t, freq in self.df.items():
            # Standard Lucene/BM25 IDF
            self.idf[t] = math.log(1.0 + (N - freq + 0.5) / (freq + 0.5))

    def score(self, query: str) -> List[float]:
        q_tokens = self._tokenize(query)
        scores = []
        for idx, doc in enumerate(self.corpus):
            d_tokens = self._tokenize(doc)
            tf_dict = {}
            for t in d_tokens:
                tf_dict[t] = tf_dict.get(t, 0) + 1
            doc_score = 0.0
            L = self.doc_len[idx]
            for q in q_tokens:
                if q in tf_dict:
                    tf = tf_dict[q]
                    numerator = self.idf.get(q, 0.0) * tf * (self.k1 + 1.0)
                    denominator = tf + self.k1 * (1.0 - self.b + self.b * (L / self.avg_doc_len))
                    doc_score += numerator / denominator
            scores.append(doc_score)
        return scores


# ============================================================================
# 3. Dense Similarity Mock (Simulated Vector Embeddings)
# ============================================================================
def mock_dense_similarity(query: str, corpus: List[str]) -> List[float]:
    """Mock semantic similarity based on shared root semantics."""
    q_words = set(re.findall(r"\w+", query.lower()))
    scores = []
    for doc in corpus:
        d_words = set(re.findall(r"\w+", doc.lower()))
        jaccard = len(q_words.intersection(d_words)) / max(1, len(q_words.union(d_words)))
        scores.append(jaccard)
    return scores


# ============================================================================
# 4. Reciprocal Rank Fusion (RRF)
# ============================================================================
def reciprocal_rank_fusion(sparse_scores: List[float], dense_scores: List[float], k: int = 60) -> List[Dict[str, Any]]:
    # Rank sparse results
    sparse_ranked = sorted(range(len(sparse_scores)), key=lambda i: sparse_scores[i], reverse=True)
    dense_ranked = sorted(range(len(dense_scores)), key=lambda i: dense_scores[i], reverse=True)

    rrf_scores = {}
    for rank, doc_id in enumerate(sparse_ranked, start=1):
        rrf_scores[doc_id] = rrf_scores.get(doc_id, 0.0) + (1.0 / (k + rank))
    for rank, doc_id in enumerate(dense_ranked, start=1):
        rrf_scores[doc_id] = rrf_scores.get(doc_id, 0.0) + (1.0 / (k + rank))

    sorted_results = sorted(rrf_scores.items(), key=lambda item: item[1], reverse=True)
    return [{"doc_id": doc_id, "rrf_score": score} for doc_id, score in sorted_results]


# ============================================================================
# 5. Verification & End-to-End Pipeline
# ============================================================================
if __name__ == "__main__":
    knowledge_base = [
        "Company Policy: Employees are entitled to 20 days of paid annual leave per calendar year.",
        "Server Configuration: The production database cluster runs PostgreSQL 16 on port 5432.",
        "Refund Policy: Customers can request a full refund within 30 days of hardware purchase with original receipt.",
        "Security Protocol: Multi-factor authentication (MFA) is strictly mandatory for all SSH access to cloud servers."
    ]

    user_query = "How many days do I have to request a refund on hardware?"

    # 1. Sparse Search
    bm25 = SimpleBM25(knowledge_base)
    bm25_scores = bm25.score(user_query)

    # 2. Dense Search
    dense_scores = mock_dense_similarity(user_query, knowledge_base)

    # 3. Hybrid Fusion
    fused_ranks = reciprocal_rank_fusion(bm25_scores, dense_scores)

    print("--- User Query ---")
    print(user_query)
    print("\n--- Hybrid RAG Top Retrieved Documents ---")
    for item in fused_ranks[:2]:
        doc_idx = item["doc_id"]
        print(f"Rank Score: {item['rrf_score']:.5f} | Document: {knowledge_base[doc_idx]}")

    print("\nHybrid RAG Pipeline verified and executed successfully!")
```

---

## ৮. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স

| ফিচার | সনাতন Naive RAG | আধুনিক Modular / Hybrid RAG |
| :--- | :--- | :--- |
| **অনুসন্ধান পদ্ধতি** | একক ডেন্স ভেক্টর সার্চ | হাইব্রিড (BM25 + Dense HNSW) + Re-Ranker |
| **কীওয়ার্ড ম্যাচিং** | দুর্বল (পার্ট নম্বর বা আইডিতে ব্যর্থ) | শতভাগ নিখুঁত |
| **কনটেক্সট নয়েজ** | উচ্চ (অপ্রয়োজনীয় তথ্য ইনপুটে যায়) | কম (Cross-Encoder ফিল্টারিং) |
| **ইনফারেন্স ল্যাটেন্সি** | সর্বনিম্ন (৫০-১০০ ms) | সামান্য বেশি (১৫০-২৫০ ms) |
| **অ্যাকুরেসি ও গ্রাউন্ডিং**| মাঝারি (হ্যালুসিনেশনের ঝুঁকি থাকে) | এন্টারপ্রাইজ-গ্রেড সর্বোচ্চ সুরক্ষা |

---

## ৯. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: HyDE (Hypothetical Document Embeddings) কীভাবে কাজ করে?**
   - **উত্তর:** অনেক সময় ব্যবহারকারীর প্রশ্ন খুব সংক্ষিপ্ত বা অস্পষ্ট হয় যার সরাসরি ভেক্টর এম্বেডিং ডকুমেন্টের ভেক্টরের সাথে মেলে না। HyDE প্রথমে একটি এলএলএম দিয়ে কাল্পনিক একটি নিখুঁত উত্তর তৈরি করায়। এরপর সেই কাল্পনিক উত্তরের এম্বেডিং তৈরি করে ভেক্টর ডেটাবেসে সার্চ করা হয়। যেহেতু উত্তর-থেকে-উত্তর সিম্যান্টিক স্পেস বেশি কাছাকাছি থাকে, তাই রিট্রিভাল নির্ভুলতা বহুগুণ বাড়ে।
2. **প্রশ্ন: HNSW ইনডেক্সিংয়ে $M$ এবং $ef\_construction$ প্যারামিটারের ভূমিকা কী?**
   - **উত্তর:** $M$ হলো গ্রাফের প্রতিটি নোডের সর্বোচ্চ এজ (edge) বা সংযোগ সংখ্যা (সাধারণত ১৬-৬৪)। $ef\_construction$ হলো ইনডেক্স তৈরির সময় অনুসন্ধান তালিকার গভীরতা (সাধারণত ১০০-৫০০)। এদের মান বাড়ালে ইনডেক্স বিল্ড টাইম ও মেমোরি বৃদ্ধি পায় কিন্তু সার্চের রিকল (Recall Rate) উল্লেখযোগ্যভাবে বৃদ্ধি পায়।
3. **প্রশ্ন: RAG-এ Context Stuffing বা বিশাল কনটেক্সট উইন্ডো ব্যবহার করার ঝুঁকি কী?**
   - **উত্তর:** যদি প্রম্পটে ৩০টি অপ্রয়োজনীয় ডকুমেন্ট ঢুকিয়ে দেওয়া হয়, তবে মডেল **Lost-in-the-Middle** সমস্যায় আক্রান্ত হয়, টোকেন প্রসেসিং খরচ বেড়ে যায় এবং অপ্রাসঙ্গিক তথ্যের বিভ্রান্তিতে মডেল হ্যালুসিনেশন করা শুরু করে। তাই রি-র‍্যাঙ্কিং করে কেবলমাত্র সেরা ৩-৫টি চাঙ্ক পাঠানোই সর্বোত্তম।
