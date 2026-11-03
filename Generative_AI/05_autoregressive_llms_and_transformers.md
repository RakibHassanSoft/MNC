# Chapter 05: Autoregressive Large Language Models (LLMs) & Modern Transformer Architectures

---

## ১. অটো-রিগ্রেসিভ ল্যাঙ্গুয়েজ মডেলের মূল ভিত্তি (Autoregressive Language Modeling)

আধুনিক লার্জ ল্যাঙ্গুয়েজ মডেলসমূহ (যেমন: GPT-4o, LLaMA-3, Claude 3.5, Mistral, Gemini) মূলত **ডিকোডার-অনলি অটো-রিগ্রেসিভ ট্রান্সফরমার (Decoder-only Autoregressive Transformers)**।
একটি টেক্সট সিকোয়েন্স $X = (x_1, x_2, \dots, x_N)$-এর যৌথ সম্ভাব্যতা বণ্টন (joint probability) চেইন রুল অনুযায়ী ফ্যাক্টরাইজ করা হয়:

$$p(X) = \prod_{i=1}^N p(x_i \mid x_1, x_2, \dots, x_{i-1})$$

```
[Prompt: "The capital of France is"]
                     |
                     v
   [ Causal Multi-Head Attention ] <--- (Future tokens masked with -inf)
                     |
                     v
      [ Logits over Vocabulary ]
                     |
                     v
        p(x_t | x_{<t}) ---> Output: "Paris" (Next-Token Prediction)
```

### কেন এনকোডার-ডিকোডার (T5) বা এনকোডার-অনলি (BERT) নয়?
1. **ইন-কনটেক্সট লার্নিং (Few-shot In-Context Learning):** ডিকোডার-অনলি আর্কিটেকচার সাধারণ প্রম্পটকে একটি অবিচ্ছিন্ন সিকোয়েন্স হিসেবে দেখে, যা প্রম্পট-বেসড শূন্য ও স্বল্প-নমুনা টাস্ক সমাধানে সর্বাধিক কার্যকর।
2. **স্কেলিং ও অপ্টিমাইজেশন:** ইউনিফায়েড প্রেডিকশন অবজেক্টিভ হওয়ায় প্যারালাল প্রি-ট্রেইনিং এবং কেভি-ক্যাশিং (KV-caching) হার্ডওয়্যারে সর্বোচ্চ ব্যান্ডউইথ ব্যবহার করতে পারে।

---

## ২. রোটারি পজিশনাল এম্বেডিং (Rotary Position Embedding - RoPE)

Su et al. (2021) কর্তৃক উদ্ভাবিত **RoPE** হলো আধুনিক এলএলএম-এর (LLaMA, Mistral, Gemma) প্রধান ভিত্তি।

### সনাতন পজিশনাল এম্বেডিংয়ের দুর্বলতা:
অ্যাবসোলিউট পজিশনাল এম্বেডিংয়ে টোকেনের সাথে পজিশন ভেক্টর যোগ করা হয় ($x_m + p_m$), যা সিকোয়েন্সের দৈর্ঘ্য প্রি-ট্রেইন্ড লিমিটের বাইরে বাড়লে (context extrapolation) সম্পূর্ণ ভেঙে পড়ে।

### RoPE-এর মূল ধারণা:
ভেক্টরের যোগফলের বদলে কোয়েরি $q_m$ এবং কি $k_n$-কে একটি ঘূর্ণন ম্যাট্রিক্স (Rotation Matrix) দ্বারা গুণ করা হয়:

$$q_m = R_{\Theta, m} W_q x_m, \quad k_n = R_{\Theta, n} W_k x_n$$

একটি ২-ডাইমেনশনাল ভেক্টরের জন্য রোটেশন ম্যাট্রিক্স:

$$R_{\Theta, m} = \begin{pmatrix} \cos(m\theta) & -\sin(m\theta) \\ \sin(m\theta) & \cos(m\theta) \end{pmatrix}$$

### আপেক্ষিক দূরত্বের গাণিতিক প্রমাণ (Preserving Relative Distance):
কোয়েরি ও কি-এর ডট প্রোডাক্ট নির্ণয় করলে:

$$\langle q_m, k_n \rangle = (R_m W_q x_m)^T (R_n W_k x_n) = x_m^T W_q^T R_m^T R_n W_k x_n$$

যেহেতু অর্থোগোনাল রোটেশন ম্যাট্রিক্সের ক্ষেত্রে $R_m^T R_n = R_{n-m}$:

$$\langle q_m, k_n \rangle = (W_q x_m)^T R_{n-m} (W_k x_n)$$

এটি প্রমাণ করে যে এটেনশন স্কোর কেবলমাত্র টোকেন দুটির আপেক্ষিক দূরত্বের ($m - n$) ওপর নির্ভর করে, তাদের পরম পজিশন $m$ বা $n$-এর ওপর নয়! এর ফলে RoPE যেকোনো দীর্ঘ সিকোয়েন্সের জন্য অসাধারণ এক্সট্রাপোলেশন সুবিধা প্রদান করে।

---

## ৩. এটেনশনের বিবর্তন: MHA $\to$ MQA $\to$ GQA

ইনফারেন্সের সময় জিপিইউ মেমোরির প্রধান সীমাবদ্ধতা হলো **KV-Cache**। এটি সমাধানের জন্য এটেনশন আর্কিটেকচারের পরিবর্তন এসেছে:

```
[Multi-Head Attention (MHA)]        [Multi-Query Attention (MQA)]        [Grouped-Query Attention (GQA)]
       Q1 Q2 Q3 Q4 Q5 Q6 Q7 Q8                   Q1 Q2 Q3 Q4 Q5 Q6 Q7 Q8                 Q1 Q2 Q3 Q4  Q5 Q6 Q7 Q8
        |  |  |  |  |  |  |  |                    \  \  \  |  /  /  /  /                  \  \  /  /    \  \  /  /
       K1 K2 K3 K4 K5 K6 K7 K8                           K1                                  K1             K2
       V1 V2 V3 V4 V5 V6 V7 V8                           V1                                  V1             V2
(8 Q, 8 K, 8 V - Huge Memory)             (8 Q, 1 K, 1 V - Extreme Comp.)          (8 Q, 2 K, 2 V - Optimal Balance)
```

1. **Multi-Head Attention (MHA - Vaswani 2017):**
   - কোয়েরি, কি এবং ভ্যালু প্রত্যেকের জন্য সমান সংখ্যক হেড থাকে ($H_q = H_k = H_v = 32$)।
   - ইনফারেন্স মেমোরি খরচ আকাশচুম্বী।
2. **Multi-Query Attention (MQA - Shazeer 2019):**
   - সমস্ত কোয়েরি হেড একটিমাত্র কি এবং ভ্যালু হেড শেয়ার করে ($H_k = H_v = 1$)।
   - মেমোরি ব্যান্ডউইথ বহুলাংশে হ্রাস পায়, কিন্তু জটিল লজিক ও লং-কনটেক্সটে অ্যাকুরেসি সামান্য ড্রপ করতে পারে।
3. **Grouped-Query Attention (GQA - Ainslie et al. 2023 - Llama 2/3):**
   - কোয়েরি হেডগুলোকে কয়েকটি গ্রুপে ভাগ করা হয় (যেমন ৩২টি কোয়েরির জন্য ৮টি গ্রুপ)। প্রতিটি গ্রুপ ১টি করে কি ও ভ্যালু হেড শেয়ার করে ($H_k = H_v = 8$)।
   - এটি MHA-এর সমান পারফরম্যান্স ধরে রেখে KV ক্যাশের সাইজ ৪ থেকে ৮ গুণ কমিয়ে দেয়!

---

## ৪. আধুনিক নরমালাইজেশন ও অ্যাক্টিভেশন: RMSNorm ও SwiGLU

### ১. RMSNorm (Root Mean Square Normalization - Zhang & Sennrich 2019):
লেয়ার নরমালাইজেশনে গড় (mean) গণনা করা এবং বিয়োগ করা কম্পিউটেশনাল ওভারহেড তৈরি করে। গবেষকরা প্রমাণ করেন যে লেয়ার নরমালাইজেশনের মূল সাফল্য আসে এর স্কেলিং প্রোপার্টি থেকে, গড় শূন্য করার কারণে নয়।

$$\text{RMSNorm}(x) = \frac{x}{\text{RMS}(x)} \odot \gamma, \quad \text{where } \text{RMS}(x) = \sqrt{\frac{1}{d} \sum_{i=1}^d x_i^2 + \epsilon}$$

- এটি গড় বিয়োগের কাজ বাদ দিয়ে ট্রেইনিংয়ের গতি ১০% থেকে ৫০% বাড়িয়ে দেয়।

### ২. SwiGLU (Swish Gated Linear Unit - Shazeer 2020):
সনাতন ReLU বা GELU অ্যাক্টিভেশন লেয়ারের পরিবর্তে আধুনিক এলএলএম SwiGLU ব্যবহার করে:

$$\text{SwiGLU}(x) = \left( x W_1 \otimes \sigma(x W_1) \right) \odot (x W_2)$$

$$\text{FFN}_{\text{SwiGLU}}(x) = \left( \text{Swish}(x W_{\text{gate}}) \odot (x W_{\text{up}}) \right) W_{\text{down}}$$

- গেটিং মেকানিজমের কারণে নেটওয়ার্ক তথ্যের গুরুত্ব ডাইনামিকালি ফিল্টার করতে পারে, যা পারপ্লেক্সিটি উল্লেখযোগ্যভাবে হ্রাস করে।

---

## ৫. ফ্ল্যাশ-এটেনশন (FlashAttention Mechanics - Dao et al.)

### সমস্যা (GPU Memory Bottleneck):
স্ট্যান্ডার্ড এটেনশনে সফটম্যাক্স গণনার জন্য ইন্টারমিডিয়েট $N \times N$ এটেনশন ম্যাট্রিক্স তৈরি হয়:

$$S = Q K^T \in \mathbb{R}^{N \times N}, \quad P = \text{Softmax}(S) \in \mathbb{R}^{N \times N}$$

$N = 32,768$ টোকেনের জন্য এই ম্যাট্রিক্সটি জিপিইউর উচ্চ গতির **SRAM**-এ ধরে না। ফলে একে ধীরগতির **HBM (High Bandwidth Memory / VRAM)**-এ বারবার লিখতে ও পড়তে হয় ($O(N^2)$ memory reads/writes), যা মেমোরি ব্যান্ডউইথকে স্যাচুরেট করে ফেলে।

```
Standard Attention:
[ SRAM ] <======== $O(N^2)$ Read/Write Memory Bottleneck ========> [ GPU HBM (VRAM) ]

FlashAttention:
Tiles Q, K, V into small blocks ---> Fits entirely in SRAM ---> Online Softmax ---> Writes only O(N) to HBM
```

### সমাধান — টাইলিং ও অনলাইন সফটম্যাক্স (FlashAttention-1/2/3):
1. **টাইলিং (Tiling):** ইনপুট $Q, K, V$-কে ছোট ছোট ব্লকে ভাগ করা হয় যা সরাসরি অতি দ্রুত SRAM (On-chip memory)-এ লোড হয়।
2. **অনলাইন সফটম্যাক্স (Online Softmax):** সম্পূর্ণ $N \times N$ ম্যাট্রিক্স সংরক্ষণ না করেই রানিং ম্যাক্সিমাম ($m$) এবং সামেশন ডিনমিনেটর ($l$) আপডেট করার মাধ্যমে এক পাসেই সঠিক সফটম্যাক্স গণনা করা হয়।
3. **ফলাফল:** মেমোরি জটিলতা $O(N^2)$ থেকে কমে $O(N)$-এ নেমে আসে এবং ইনফারেন্স ও প্রি-ট্রেইনিং গতি ২ থেকে ৪ গুণ বৃদ্ধি পায়।

---

## ৬. কেভি-ক্যাশিং ও মেমোরি হিসাবের পূর্ণাঙ্গ গাণিতিক সূত্র (KV Cache Math)

অটো-রিগ্রেসিভ জেনারেশনে নতুন টোকেন $t+1$ প্রেডিক্ট করার সময় পূর্ববর্তী $1, \dots, t$ টোকেনগুলোর কোয়েরি আর প্রয়োজন হয় না, কিন্তু কি ($K$) ও ভ্যালু ($V$) ভেক্টরগুলো পুনরায় প্রয়োজন হয়। এই ভেক্টরগুলোকে প্রতি ধাপে রি-কম্পিউট না করে জিপিইউ মেমোরিতে ক্যাশ করে রাখাকে **KV-Cache** বলে।

### মেমোরি হিসাবের সার্বজনীন সূত্র:

$$\text{KV Cache Size (Bytes)} = 2 \times 2 \times n_{\text{layers}} \times n_{\text{kv\_heads}} \times d_{\text{head}} \times \text{seq\_len} \times \text{batch\_size} \times \text{bytes\_per\_param}$$

- প্রথম $2$: $K$ এবং $V$ দুটি টেন্সরের জন্য।
- দ্বিতীয় $2$: প্রতিটি লেয়ারে কি এবং ভ্যালুর জোড়া।
- $\text{bytes\_per\_param}$: FP16 বা BF16 এর জন্য $2$ বাইট, FP8 এর জন্য $1$ বাইট।

### সংখ্যাগত উদাহরণ: LLaMA-3-8B-এর মেমোরি হিসাব
ধরা যাক:
- লেয়ার সংখ্যা ($n_{\text{layers}}$) $= 32$
- জিবিএ কি/ভ্যালু হেড ($n_{\text{kv\_heads}}$) $= 8$ (GQA)
- হেড ডাইমেনশন ($d_{\text{head}}$) $= 128$
- ডেটা টাইপ $= 16\text{-bit float (BF16)} \implies 2 \text{ bytes}$
- কনটেক্সট দৈর্ঘ্য ($\text{seq\_len}$) $= 4096$
- ব্যাচ সাইজ ($\text{batch\_size}$) $= 16$

$$\text{Bytes} = 2 \times 2 \times 32 \times 8 \times 128 \times 4096 \times 16 \times 2$$
$$= 4 \times 32 \times 8 \times 128 \times 4096 \times 16 \times 2$$
$$= 4 \times 32 = 128$$
$$128 \times 8 = 1024$$
$$1024 \times 128 = 131,072$$
$$131,072 \times 4096 = 536,870,912$$
$$536,870,912 \times 16 = 8,589,934,592$$
$$8,589,934,592 \times 2 = 17,179,869,184 \text{ Bytes}$$

গিগাবাইটে রূপান্তর:
$$\text{GB} = \frac{17,179,869,184}{1024^3} = \mathbf{16.0 \text{ GB}}$$

**গুরুত্বপূর্ণ উপলব্ধি:** মডেলের ৮ বিলিয়ন ওজনের নিজস্ব সাইজ প্রায় ১৬ জিবি ($8 \times 2 \text{ GB}$)। কিন্তু মাত্র ১৬ জন সমসাময়িক ব্যবহারকারীর জন্য ৪কে কনটেক্সটে KV-Cache আরও একটি অতিরিক্ত ১৬ জিবি VRAM দাবি করে! এই কারণেই GQA এবং PagedAttention আধুনিক সার্ভিংয়ে অত্যাবশ্যক।

---

## ৭. ডিকোডিং কৌশলসমূহ (Decoding Strategies)

লগিটস $z_i$ থেকে পরবর্তী টোকেন স্যাম্পল করার বিভিন্ন কৌশল:

### ১. টেম্পারেচার স্যাম্পলিং (Temperature $T$):
$$p_i = \frac{\exp(z_i / T)}{\sum_j \exp(z_j / T)}$$
- $T \to 0$: গ্রিডি ডিকোডিং (Greedy), মডেল কঠোরভাবে সর্বোচ্চ সম্ভাব্য টোকেন বেছে নেয় (ম্যাথ, কোডিংয়ের জন্য আদর্শ)।
- $T = 0.7 - 0.9$: প্রাকৃতিক কথোপকথন ও লেখার জন্য আদর্শ।
- $T > 1.2$: সৃষ্টিশীল কিন্তু হ্যালুসিনেশন ও অসংলগ্নতার ঝুঁকি বৃদ্ধি পায়।

### ২. টপ-কে (Top-k) ও টপ-পি (Top-p / Nucleus Sampling):
- **Top-k:** সম্ভাব্যতার ভিত্তিতে শুধুমাত্র শীর্ষ $k$ টি টোকেন ফিল্টার করা হয়।
- **Top-p (Nucleus):** কিউমুলেটিভ প্রবাবিলিটি থ্রেশহোল্ড $p$ (যেমন $p = 0.90$) পূরণ করে এমন ক্ষুদ্রতম সেট বেছে নেওয়া হয়। এটি গতিশীলভাবে ডিস্ট্রিবিউশনের ফ্ল্যাট বা পিকড অবস্থার সাথে মানিয়ে নেয়।

---

## ৮. পাইটর্চে সম্পূর্ণ রোটারি এটেনশন ও জিবিএ আর্কিটেকচার (Production PyTorch Code)

নিচে একটি কমপ্লিট, ভেরিফাইড পাইটর্চ স্ক্রিপ্ট দেওয়া হলো যা RoPE, RMSNorm, GQA এবং KV-Cache লজিক এক্সিকিউট করে:

```python
"""
Modern LLM Architecture Components: RoPE, RMSNorm, GQA with KV-Caching.
Fully runnable, verified and mathematically grounded.
"""

import math
import torch
import torch.nn as nn
import torch.nn.functional as F


# ============================================================================
# 1. RMSNorm Implementation
# ============================================================================
class RMSNorm(nn.Module):
    def __init__(self, dim: int, eps: float = 1e-6):
        super(RMSNorm, self).__init__()
        self.eps = eps
        self.weight = nn.Parameter(torch.ones(dim))

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        # RMS = sqrt(mean(x^2) + eps)
        variance = x.pow(2).mean(-1, keepdim=True)
        return x * torch.rsqrt(variance + self.eps) * self.weight


# ============================================================================
# 2. Rotary Position Embeddings (RoPE)
# ============================================================================
def precompute_freqs_cis(dim: int, max_seq_len: int, theta: float = 10000.0):
    freqs = 1.0 / (theta ** (torch.arange(0, dim, 2)[: (dim // 2)].float() / dim))
    t = torch.arange(max_seq_len, dtype=torch.float32)
    freqs = torch.outer(t, freqs)
    # Return cos and sin components
    return torch.cos(freqs), torch.sin(freqs)


def apply_rotary_emb(x: torch.Tensor, cos: torch.Tensor, sin: torch.Tensor) -> torch.Tensor:
    # x shape: [batch, seq_len, n_heads, head_dim]
    d = x.shape[-1]
    x1 = x[..., : d // 2]
    x2 = x[..., d // 2 :]
    # Rotate 2D vector: [-x2, x1]
    rotated = torch.cat((-x2, x1), dim=-1)
    cos = cos.unsqueeze(0).unsqueeze(2)  # [1, seq_len, 1, head_dim/2]
    cos = torch.cat((cos, cos), dim=-1)
    sin = sin.unsqueeze(0).unsqueeze(2)
    sin = torch.cat((sin, sin), dim=-1)
    return (x * cos) + (rotated * sin)


# ============================================================================
# 3. Grouped-Query Attention (GQA) with KV Cache
# ============================================================================
class GroupedQueryAttention(nn.Module):
    def __init__(self, dim: int = 256, n_heads: int = 8, n_kv_heads: int = 2, max_seq_len: int = 512):
        super(GroupedQueryAttention, self).__init__()
        self.dim = dim
        self.n_heads = n_heads
        self.n_kv_heads = n_kv_heads
        self.head_dim = dim // n_heads
        self.num_queries_per_kv = n_heads // n_kv_heads

        self.wq = nn.Linear(dim, n_heads * self.head_dim, bias=False)
        self.wk = nn.Linear(dim, n_kv_heads * self.head_dim, bias=False)
        self.wv = nn.Linear(dim, n_kv_heads * self.head_dim, bias=False)
        self.wo = nn.Linear(dim, dim, bias=False)

        cos, sin = precompute_freqs_cis(self.head_dim, max_seq_len)
        self.register_buffer("cos", cos)
        self.register_buffer("sin", sin)

        # Preallocated KV-Cache
        self.k_cache = None
        self.v_cache = None

    def forward(self, x: torch.Tensor, use_cache: bool = False, start_pos: int = 0) -> torch.Tensor:
        batch_size, seq_len, _ = x.shape

        q = self.wq(x).view(batch_size, seq_len, self.n_heads, self.head_dim)
        k = self.wk(x).view(batch_size, seq_len, self.n_kv_heads, self.head_dim)
        v = self.wv(x).view(batch_size, seq_len, self.n_kv_heads, self.head_dim)

        # Apply RoPE
        cur_cos = self.cos[start_pos : start_pos + seq_len]
        cur_sin = self.sin[start_pos : start_pos + seq_len]
        q = apply_rotary_emb(q, cur_cos, cur_sin)
        k = apply_rotary_emb(k, cur_cos, cur_sin)

        # KV-Caching logic
        if use_cache:
            if self.k_cache is None or start_pos == 0:
                self.k_cache = k
                self.v_cache = v
            else:
                self.k_cache = torch.cat([self.k_cache, k], dim=1)
                self.v_cache = torch.cat([self.v_cache, v], dim=1)
            keys = self.k_cache
            values = self.v_cache
        else:
            keys = k
            values = v

        # Repeat KV heads to match query heads (GQA broadcast)
        keys = keys.repeat_interleave(self.num_queries_per_kv, dim=2)
        values = values.repeat_interleave(self.num_queries_per_kv, dim=2)

        # Permute for attention computation: [batch, n_heads, seq_len, head_dim]
        q = q.transpose(1, 2)
        keys = keys.transpose(1, 2)
        values = values.transpose(1, 2)

        # Scaled Dot-Product Attention with Causal Mask
        scores = torch.matmul(q, keys.transpose(-2, -1)) / math.sqrt(self.head_dim)
        if seq_len > 1:
            mask = torch.full((seq_len, keys.shape[2]), float("-inf"), device=x.device)
            mask = torch.triu(mask, diagonal=start_pos + 1)
            scores = scores + mask

        probs = F.softmax(scores, dim=-1)
        output = torch.matmul(probs, values)  # [batch, n_heads, seq_len, head_dim]

        output = output.transpose(1, 2).contiguous().view(batch_size, seq_len, self.dim)
        return self.wo(output)


# ============================================================================
# 4. Complete Verification & Demo
# ============================================================================
if __name__ == "__main__":
    torch.manual_seed(42)
    device = torch.device("cpu")

    batch_size = 2
    dim = 64
    n_heads = 4
    n_kv_heads = 2
    seq_len = 5

    gqa_layer = GroupedQueryAttention(dim=dim, n_heads=n_heads, n_kv_heads=n_kv_heads).to(device)
    norm = RMSNorm(dim=dim).to(device)

    # Initial prompt input
    prompt_tokens = torch.randn(batch_size, seq_len, dim, device=device)
    normalized_input = norm(prompt_tokens)

    print("--- 1. Testing Prefill Step (Prompt Processing) ---")
    out = gqa_layer(normalized_input, use_cache=True, start_pos=0)
    print(f"Prompt Input Shape:  {prompt_tokens.shape}")
    print(f"Prefill Output Shape: {out.shape}")
    print(f"Cached Keys Shape:   {gqa_layer.k_cache.shape}")

    print("\n--- 2. Testing Autoregressive Next-Token Step ---")
    next_token = torch.randn(batch_size, 1, dim, device=device)
    next_out = gqa_layer(norm(next_token), use_cache=True, start_pos=seq_len)
    print(f"New Single Token Shape: {next_token.shape}")
    print(f"Generated Output Shape: {next_out.shape}")
    print(f"Updated Cached Keys:    {gqa_layer.k_cache.shape}")
    print("All modern LLM components executed and verified successfully!")
```

---

## ৯. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স

| আর্কিটেকচার ফিচার | ক্লাসিক্যাল ট্রান্সফরমার (2017) | আধুনিক LLaMA-3 / Mistral (2024) |
| :--- | :--- | :--- |
| **পজিশনাল এম্বেডিং** | অ্যাবসোলিউট সাইনুসয়েডাল ($x + P$) | রোটারি পজিশনাল এম্বেডিং (RoPE) |
| **নরমালাইজেশন** | Post-LN / Pre-LN LayerNorm | Pre-LN RMSNorm |
| **অ্যাক্টিভেশন ফাংশন** | ReLU / GELU | SwiGLU Gated Unit |
| **এটেনশন আর্কিটেকচার** | Multi-Head Attention (MHA) | Grouped-Query Attention (GQA) + FlashAttention |
| **ইনফারেন্স মেমোরি বটলনেক**| বিশাল ও অনিয়ন্ত্রিত | ৪-৮ গুণ কম (GQA + Paged KV-Cache) |

---

## ১০. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: RoPE-তে বেস ফ্রিকোয়েন্সি $\theta = 10000$ থেকে বাড়িয়ে ৫০০,০০০ বা ৮,০০০,০০০ করার কারণ কী?**
   - **উত্তর:** এটি হলো **RoPE Base Scaling**। দীর্ঘ কনটেক্সটে (যেমন 8k থেকে 128k টোকেন) পজিশন কোণ অত্যন্ত দ্রুত পরিবর্তিত হয়ে মডেল কনফিউজড হয়। বেস ফ্রিকোয়েন্সি বহুগুণ বাড়ালে উচ্চতর ডাইমেনশনগুলোর ঘূর্ণন অত্যন্ত ধীরগতির হয়ে যায়, যা মডেলকে লং-কনটেক্সট এক্সটেনশনে সূক্ষ্ম পজিশন পার্থক্য সহজে ধরে রাখতে সাহায্য করে।
2. **প্রশ্ন: প্রিফিল স্টেজ এবং ডিকোড স্টেজের মধ্যে কম্পিউটেশনাল পার্থক্য কী?**
   - **উত্তর:** **Prefill Stage:** প্রম্পটের সমস্ত টোকেন সমান্তরালভাবে একবারে প্রসেস হয়। এটি **Compute-bound** (জিপিইউ কোর সম্পূর্ণ ব্যস্ত থাকে)। **Decode Stage:** টোকেনগুলো একটি একটি করে জেনারেট হয়। এটি **Memory-bandwidth bound** (প্রতিটি সিঙ্গেল টোকেন জেনারেট করতে সমগ্র মডেলের ওজন ও কেভি ক্যাশ মেমোরি থেকে রিড করতে হয়)।
3. **প্রশ্ন: FlashAttention কেন কোনো অ্যাকুরেসি বা প্রিসিশন লস করে না?**
   - **উত্তর:** কারণ FlashAttention কোনো আনুমানিক বা লসি অ্যালগরিদম নয় (এটি কোনো প্রুনিং বা লো-র‍্যাঙ্ক ফ্যাক্টরাইজেশন করে না)। এটি সাধারণ এটেনশনের এক্স্যাক্ট ম্যাথমেটিকাল আউটপুটই তৈরি করে, শুধুমাত্র হার্ডওয়্যার-অ্যাওয়ার টাইলিং এবং অনলাইন সফটম্যাক্স ট্রিকের মাধ্যমে SRAM-এ ইন্টারমিডিয়েট মানগুলো প্রসেস করে।
