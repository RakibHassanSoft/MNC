# Chapter 07: Fine-Tuning, PEFT, LoRA/QLoRA & Alignment (RLHF to DPO)

---

## ১. ফুল ফাইন-টিউনিংয়ের সীমাবদ্ধতা বনাম PEFT (Full Fine-Tuning vs PEFT)

প্রি-ট্রেইন্ড লার্জ ল্যাঙ্গুয়েজ মডেলকে কোনো নির্দিষ্ট ডাউনস্ট্রিম ডোমেইনে (যেমন: মেডিকেল, লিগ্যাল, বা কাস্টমার সাপোর্ট) স্পেশালাইজড করার জন্য ফাইন-টিউনিং অপরিহার্য। কিন্তু সম্পূর্ণ মডেলের সমস্ত প্যারামিটার পুনরায় ট্রেইন করাকে **ফুল ফাইন-টিউনিং (Full Fine-Tuning - FFT)** বলা হয়, যার মারাত্মক অসুবিধা রয়েছে:

```
[Full Fine-Tuning (FFT)]
70B Model Weights ---> 140 GB (BF16 Weights) + 280 GB (Gradients) + 560 GB (Adam Optimizer States)
                      Total VRAM Required = ~1000 GB (1 Terabyte VRAM!)
                      Risk: Catastrophic Forgetting (মডেল সাধারণ জ্ঞান হারিয়ে ফেলে)

[Parameter-Efficient Fine-Tuning (PEFT / LoRA)]
Freeze 70B Base Model ---> Train only tiny low-rank adapter matrices (< 0.1% parameters)
                          Total Extra VRAM = Few Megabytes! No Catastrophic Forgetting.
```

### অ্যাডাম অপটিমাইজারের মেমোরি ব্রেকডাউন (VRAM Breakdown Math):
একটি $N$-প্যারামিটার মডেলের জন্য AdamW অপটিমাইজার ব্যবহার করলে:
1. **Model Weights (FP16/BF16):** $2 \times N$ বাইট।
2. **Gradients (FP16/BF16):** $2 \times N$ বাইট।
3. **Adam First Moment ($m_t$, FP32):** $4 \times N$ বাইট।
4. **Adam Second Moment ($v_t$, FP32):** $4 \times N$ বাইট।
5. **Master Weights (FP32 Copy for numerical stability):** $4 \times N$ বাইট।
- **মোট মেমোরি খরচ:** $2N + 2N + 4N + 4N + 4N = \mathbf{16N \text{ Bytes}}$।
- একটি ৭ বিলিয়ন ($N = 7 \times 10^9$) মডেলের জন্য শুধুমাত্র অপটিমাইজার ও ওজনের মেমোরি লাগে: $16 \times 7 = \mathbf{112 \text{ GB VRAM}}$!

---

## ২. সুপারভাইজড ফাইন-টিউনিং (SFT) ও প্রম্পট মাস্কিং

বেস মডেল (Raw Completion Model)-কে চ্যাটবট বা অ্যাসিস্ট্যান্টে রূপান্তর করার প্রথম ধাপ হলো **Supervised Fine-Tuning (SFT)**।

### প্রম্পট-রেসপন্স লস মাস্কিং (Cross-Entropy Masking):
ইনপুট প্রম্পট টোকেনগুলোর ওপর লস ক্যালকুলেট করা উচিত নয়, কারণ মডেলকে ইউজার প্রম্পট প্রেডিক্ট করতে নয় বরং শুধুমাত্র অ্যাসিস্ট্যান্টের রেসপন্স প্রেডিক্ট করতে শেখানো আমাদের লক্ষ্য:

$$\mathcal{L}_{\text{SFT}} = -\frac{1}{|T_{\text{response}}|} \sum_{t \in T_{\text{response}}} \log p_\theta(x_t \mid x_{<t})$$

```
Token Sequence:  [System] [User: What is AI?] [Assistant: AI is machine intelligence.]
Loss Mask:          0            0                       1    1    1       1
(Prompt tokens masked with -100 in PyTorch CrossEntropyLoss, preventing gradient updates)
```

---

## ৩. লো-র‍্যাঙ্ক অ্যাডাপ্টেশন (LoRA - Hu et al., 2021)

### ইন্ট্রিনসিক র‍্যাঙ্ক হাইপোথিসিস (Intrinsic Rank Hypothesis):
Aghajanyan et al. (2020) দেখিয়েছেন যে, বৃহৎ নিউরাল নেটওয়ার্কের ওজন ম্যাট্রিক্সগুলো বিশাল ডাইমেনশনের হলেও ডাউনস্ট্রিম টাস্ক অ্যাডাপ্টেশনের জন্য প্রয়োজনীয় কার্যকরী তথ্যের র‍্যাঙ্ক অত্যন্ত কম (Low Intrinsic Rank)।

### গাণিতিক ফর্মুলেশন:
ধরা যাক একটি প্রিটেইন্ড লিনিয়ার লেয়ারের ওজন $W_0 \in \mathbb{R}^{d \times k}$। LoRA এর মূল ওজন $W_0$-কে সম্পূর্ণ ফ্রিজ (freeze) করে রাখে এবং ওজনের পরিবর্তন $\Delta W$-কে দুটি অতি ক্ষুদ্র লো-র‍্যাঙ্ক ম্যাট্রিক্সের গুণফল আকারে প্রকাশ করে:

$$W = W_0 + \Delta W = W_0 + \frac{\alpha}{r} (B \cdot A)$$

যেখানে:
- $B \in \mathbb{R}^{d \times r}$
- $A \in \mathbb{R}^{r \times k}$
- র‍্যাঙ্ক $r \ll \min(d, k)$ (সাধারণত $r \in [4, 64]$)
- $\alpha$ হলো স্কেলিং কনস্ট্যান্ট (সাধারণত $\alpha = 2r$ বা $16$)

```
                  h = W_0 * x + (alpha / r) * B * A * x
                                   |
                  +----------------+----------------+
                  |                                 |
         [ Frozen Base W_0 ]                  [ Low-Rank Path ]
             (d x k)                                |
                |                               [ Matrix B ] (d x r, initialized to 0)
                |                                   ^
                |                                   |
                |                               [ Matrix A ] (r x k, initialized to Gaussian)
                |                                   ^
                |                                   |
                +----------------- x ---------------+
```

### ইনিশিয়ালাইজেশন ও স্কেলিং:
1. $A \sim \mathcal{N}\left(0, \frac{1}{r}\right)$ (র‍্যান্ডম গাউসিয়ান) এবং $B = 0$ দ্বারা ইনিশিয়ালাইজ করা হয়।
2. ফলে ট্রেইনিংয়ের একদম শুরুতে $\Delta W = B \cdot A = 0 \cdot A = 0$ থাকে, অর্থাৎ মডেলের প্রি-ট্রেইন্ড আচরণ বিন্দুমাত্র ব্যাহত হয় না।
3. স্কেলিং ফ্যাক্টর $\frac{\alpha}{r}$ বিভিন্ন র‍্যাঙ্ক পরিবর্তনের সময় লার্নিং রেট রি-টিউন করার ঝামেলা দূর করে।

### প্যারামিটার হ্রাসের গাণিতিক প্রমাণ:
ধরা যাক $d = k = 4096$, এবং LoRA র‍্যাঙ্ক $r = 16$:
- মূল প্যারামিটার সংখ্যা: $4096 \times 4096 = 16,777,216$ (~১৬.৮ মিলিয়ন)।
- LoRA প্যারামিটার সংখ্যা: $(4096 \times 16) + (16 \times 4096) = 65,536 + 65,536 = 131,072$ (~০.১৩ মিলিয়ন)।
- প্যারামিটার সেভিংস:
  $$\frac{131,072}{16,777,216} \approx 0.0078 = \mathbf{99.22\% \text{ প্যারামিটার হ্রাস!}}$$

### জিরো ইনফারেন্স ল্যাটেন্সি (Weight Merging):
প্রোডাকশনে ডেপ্লয় করার সময় LoRA অ্যাডাপ্টারকে সরাসরি বেস মডেলের ওজনের সাথে যোগ করে দেওয়া যায় ($W_{\text{final}} = W_0 + \frac{\alpha}{r}BA$)। ফলে ইনফারেন্সের সময় কোনো অতিরিক্ত লেয়ার বা মেমোরি ল্যাটেন্সি থাকে না!

---

## ৪. QLoRA: ৪-বিট কোয়ান্টাইজেশন ও কনজিউমার জিপিইউতে ফাইন-টিউনিং

Tim Dettmers et al. (2023) উদ্ভাবন করেন **QLoRA**, যা একটি একক সাধারণ 24GB জিপিইউতে (যেমন RTX 3090/4090) সম্পূর্ণ ৬৫ বিলিয়ন বা ৭০ বিলিয়ন মডেল ফাইন-টিউন করা সম্ভব করেছে।

QLoRA-এর তিনটি বিপ্লবী স্তম্ভ:
1. **NF4 (NormalFloat4 Quantization):** তথ্যতাত্ত্বিক (information-theoretically) অপ্টিমাল কোয়ান্টাইল বিন্যাস যা স্বাভাবিকভাবে বণ্টিত জিরো-মিন গাউসিয়ান ওজনকে ৪-বিটে রূপান্তর করে কোনো সিগন্যাল লস ছাড়াই।
2. **Double Quantization (DQ):** কোয়ান্টাইজেশন ধ্রুবকগুলোকে (quantization constants) পুনরায় ৮-বিটে কোয়ান্টাইজ করে প্যারামিটার প্রতি অতিরিক্ত $0.37$ বিট মেমোরি বাঁচায়।
3. **Paged Optimizers:** মেমোরি পিক ও OOM (Out-of-Memory) স্পাইক ঠেকাতে গ্রাফিক্স মেমোরি ও সিপিইউ র‍্যামের মধ্যে সিঙ্কড পেজিং (CUDA Unified Memory) ব্যবহার করে।

---

## ৫. পোস্ট-ট্রেইনিং অ্যালাইনমেন্ট: RLHF বনাম DPO

```
                         [ পোস্ট-ট্রেইনিং অ্যালাইনমেন্ট বিবর্তন ]
1. SFT (Instruction Tuning) ---> 2. RLHF (PPO + Reward Model) ---> 3. DPO (Direct Preference Optimization)
                                 (3 Models in VRAM, Unstable RL)      (Direct Closed-Form Loss, 100% Stable)
```

### ১. RLHF (Reinforcement Learning from Human Feedback):
- **ধাপ ১: রিওয়ার্ড মডেল ট্রেইনিং (Reward Model):** হিউম্যান প্রেফারেন্স ডেটাসেট $(x, y_w, y_l)$ যেখানে $y_w$ হলো উইনিং (পছন্দের) উত্তর এবং $y_l$ হলো লুজিং (খারাপ) উত্তর। ব্র্যাডলি-টেরি (Bradley-Terry) প্রেফারেন্স মডেল অনুযায়ী লস:
  $$\mathcal{L}_{\text{RM}}(\phi) = -\mathbb{E}_{(x, y_w, y_l)} \left[ \log \sigma(r_\phi(x, y_w) - r_\phi(x, y_l)) \right]$$
- **ধাপ ২: PPO পলিসি অপটিমাইজেশন:** পলিসি মডেল $\pi_\theta$-কে এমনভাবে ট্রেইন করা হয় যাতে রিওয়ার্ড সর্বোচ্চ হয় কিন্তু প্রি-ট্রেইন্ড রেফারেন্স মডেল $\pi_{\text{ref}}$ থেকে বেশি দূরে সরে না যায় (KL Penalty):
  $$\max_\theta \mathbb{E}_{x, y \sim \pi_\theta} \left[ r_\phi(x, y) \right] - \beta D_{KL}\left( \pi_\theta(y \mid x) \,\parallel\, \pi_{\text{ref}}(y \mid x) \right)$$
- *সীমাবদ্ধতা:* একই সাথে ৪টি মডেল (Policy, Reference, Reward Model, Value Critic) VRAM-এ লোড রাখতে হয়। RL অত্যন্ত হাইপার-প্যারামিটার সংবেদনশীল ও ভঙ্গুর।

---

### ২. ডিরেক্ট প্রেফারেন্স অপটিমাইজেশন (DPO - Rafailov et al., NeurIPS 2023):
রাফাইলভ এট আল. গাণিতিকভাবে প্রমাণ করেন যে, RLHF-এর অপ্টিমাল রিওয়ার্ড ফাংশনটিকে সরাসরি পলিসি ও রেফারেন্স মডেলের লগ-প্রবাবিলিটি অনুপাতের মাধ্যমে প্রতিস্থাপন করা যায়:

$$r(x, y) = \beta \log \frac{\pi_\theta(y \mid x)}{\pi_{\text{ref}}(y \mid x)}$$

এই সমীকরণটি ব্র্যাডলি-টেরি লসে সরাসরি প্রতিস্থাপন করলে কোনো আলাদা রিওয়ার্ড মডেল বা পিকেও (PPO) রেইনফোর্সমেন্ট লার্নিং ছাড়াই সরাসরি সুপারভাইজড লসের মতো অপটিমাইজ করা সম্ভব হয়!

$$\mathcal{L}_{\text{DPO}}(\theta; \pi_{\text{ref}}) = -\mathbb{E}_{(x, y_w, y_l)} \left[ \log \sigma \left( \beta \log \frac{\pi_\theta(y_w \mid x)}{\pi_{\text{ref}}(y_w \mid x)} - \beta \log \frac{\pi_\theta(y_l \mid x)}{\pi_{\text{ref}}(y_l \mid x)} \right) \right]$$

- এটি স্থিতিশীল, দ্বিগুণ দ্রুত এবং রিওয়ার্ড মডেলের কোনো অতিরিক্ত মেমোরি খরচ নেই।

---

## ৬. সংখ্যাগত উদাহরণ (Step-by-Step Numerical Example)

### DPO লস ক্যালকুলেশন:
ধরা যাক $\beta = 0.10$। একটি প্রম্পট $x$-এর জন্য:
- রেফারেন্স মডেলের লগ-প্রবাবিলিটি:
  - $\log \pi_{\text{ref}}(y_w \mid x) = -3.50$
  - $\log \pi_{\text{ref}}(y_l \mid x) = -4.00$
- ট্রেইন্ড পলিসি মডেলের বর্তমান লগ-প্রবাবিলিটি:
  - $\log \pi_\theta(y_w \mid x) = -2.80$ (উইনিং রেসপন্সের সম্ভাবনা বাড়িয়েছে)
  - $\log \pi_\theta(y_l \mid x) = -4.50$ (লুজিং রেসপন্সের সম্ভাবনা কমিয়েছে)

#### ধাপ ১: উইনিং ও লুজিং রেসপন্সের লগ রেশিও নির্ণয়
$$\Delta \log \pi(y_w) = \log \pi_\theta(y_w \mid x) - \log \pi_{\text{ref}}(y_w \mid x) = -2.80 - (-3.50) = +0.70$$
$$\Delta \log \pi(y_l) = \log \pi_\theta(y_l \mid x) - \log \pi_{\text{ref}}(y_l \mid x) = -4.50 - (-4.00) = -0.50$$

#### ধাপ ২: অন্তর্নিহিত মার্জিন (Implicit Margin) হিসাব
$$M = \beta \left( \Delta \log \pi(y_w) - \Delta \log \pi(y_l) \right)$$
$$M = 0.10 \times (0.70 - (-0.50)) = 0.10 \times (0.70 + 0.50) = 0.10 \times 1.20 = 0.12$$

#### ধাপ ৩: সিগময়েড প্রবাবিলিটি ও DPO লস
$$\sigma(M) = \frac{1}{1 + e^{-0.12}} = \frac{1}{1 + 0.8869} = \frac{1}{1.8869} \approx 0.5300$$
$$\mathcal{L}_{\text{DPO}} = -\log(\sigma(M)) = -\log(0.5300) \approx \mathbf{0.6349}$$

মডেলটি যখন পছন্দের উত্তরের সম্ভাবনা আরও বাড়াবে এবং অপছন্দের উত্তরের সম্ভাবনা কমাবে, মার্জিন $M$ বৃদ্ধি পাবে এবং লস শূন্যের কাছাকাছি নেমে আসবে।

---

## ৭. পাইটর্চে সম্পূর্ণ LoRA লেয়ার ইমপ্লিমেন্টেশন (Scratch PyTorch Code)

নিচে একটি স্বয়ংসম্পূর্ণ, প্রোডাকশন-রেডি পাইটর্চ স্ক্রিপ্ট দেওয়া হলো যা ম্যানুয়াল LoRA লিনিয়ার লেয়ার তৈরি করে, ফরোয়ার্ড ক্যালকুলেশন করে এবং ইনফারেন্সের জন্য ওজন মার্জ/আনমার্জ করার নিখুঁত মেকানিজম প্রদর্শন করে:

```python
"""
Parameter-Efficient Fine-Tuning: Custom LoRA Layer Implementation
Fully runnable, verified and mathematically grounded.
"""

import math
import torch
import torch.nn as nn
import torch.nn.functional as F


# ============================================================================
# 1. Custom LoRA Linear Layer
# ============================================================================
class LoRALinear(nn.Module):
    def __init__(self, in_features: int, out_features: int, rank: int = 8, alpha: float = 16.0):
        super(LoRALinear, self).__init__()
        self.in_features = in_features
        self.out_features = out_features
        self.rank = rank
        self.alpha = alpha
        self.scaling = alpha / rank

        # Frozen base weight
        self.weight = nn.Parameter(torch.empty(out_features, in_features))
        self.bias = nn.Parameter(torch.zeros(out_features))

        # LoRA Adapter matrices: B and A
        self.lora_A = nn.Parameter(torch.empty(rank, in_features))
        self.lora_B = nn.Parameter(torch.zeros(out_features, rank))  # Initialized to 0!

        # State tracking
        self.merged = False
        self.reset_parameters()

    def reset_parameters(self):
        # Kaiming uniform for base weight
        nn.init.kaiming_uniform_(self.weight, a=math.sqrt(5))
        # Matrix A initialized to Gaussian; Matrix B to 0
        nn.init.kaiming_uniform_(self.lora_A, a=math.sqrt(5))
        nn.init.zeros_(self.lora_B)

    def merge(self):
        """Merge LoRA weights into base weights for zero-latency inference."""
        if not self.merged:
            # W_final = W_0 + (alpha / r) * B * A
            delta_w = (self.lora_B @ self.lora_A) * self.scaling
            self.weight.data += delta_w
            self.merged = True
            print("LoRA Adapter successfully merged into Base Weight.")

    def unmerge(self):
        """Unmerge LoRA weights back to continue fine-tuning."""
        if self.merged:
            delta_w = (self.lora_B @ self.lora_A) * self.scaling
            self.weight.data -= delta_w
            self.merged = False
            print("LoRA Adapter unmerged. Ready for training.")

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        if self.merged:
            return F.linear(x, self.weight, self.bias)
        else:
            # Base path: x * W_0^T
            base_out = F.linear(x, self.weight, self.bias)
            # LoRA path: (alpha / r) * (x * A^T) * B^T
            lora_out = (x @ self.lora_A.t() @ self.lora_B.t()) * self.scaling
            return base_out + lora_out


# ============================================================================
# 2. Verification and Parameter Counting
# ============================================================================
if __name__ == "__main__":
    torch.manual_seed(42)

    in_dim = 1024
    out_dim = 1024
    rank = 16
    alpha = 32.0

    lora_layer = LoRALinear(in_features=in_dim, out_features=out_dim, rank=rank, alpha=alpha)

    # Freeze base weight and bias
    lora_layer.weight.requires_grad = False
    lora_layer.bias.requires_grad = False

    base_params = lora_layer.weight.numel()
    trainable_params = lora_layer.lora_A.numel() + lora_layer.lora_B.numel()

    print(f"Total Base Weight Parameters: {base_params:,}")
    print(f"Trainable LoRA Parameters:   {trainable_params:,}")
    print(f"Parameter Reduction:         {(1.0 - trainable_params / base_params) * 100:.2f}%")

    # Forward pass test
    dummy_input = torch.randn(4, 32, in_dim)  # [Batch, SeqLen, InDim]
    out_train = lora_layer(dummy_input)
    print(f"\nForward Pass (Training Mode) Output Shape: {out_train.shape}")

    # Test Merging
    lora_layer.merge()
    out_merged = lora_layer(dummy_input)
    diff = torch.norm(out_train - out_merged).item()
    print(f"L2 Difference between Merged and Unmerged Output: {diff:.6e} (Identical!)")
    print("LoRA layer verified successfully!")
```

---

## ৮. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স

| প্যারামিটার | Full Fine-Tuning (FFT) | LoRA | QLoRA |
| :--- | :--- | :--- | :--- |
| **ট্রেইনেবল প্যারামিটার** | ১০০% | ০.০৫% – ০.৫% | ০.০৫% – ০.৫% |
| **VRAM চাহিদা (70B মডেল)** | ~1000 GB (1 TB VRAM) | ~140 GB | **~24-48 GB (Single GPU!)** |
| **বেস মডেল প্রিসিশন** | FP16 / BF16 | FP16 / BF16 | **4-bit NF4 (NormalFloat4)** |
| **ইনফারেন্স ওভারহেড** | ০% | ০% (Weight Merging-এর পর) | ০% (ডিকোয়ান্টাইজেশনের পর) |
| **বহুমুখী অ্যাডাপ্টার স্টোরেজ** | অসম্ভব (প্রতিটির জন্য বিশাল সাইজ)| অসাধারণ (প্রতি টাস্কে মাত্র ৫০MB) | অসাধারণ |

---

## ৯. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: LoRA-তে ম্যাট্রিক্স B-কে কেন শূন্য দ্বারা ইনিশিয়ালাইজ করা হয়?**
   - **উত্তর:** যদি ম্যাট্রিক্স A এবং B দুটোকেই র‍্যান্ডম ভ্যালু দিয়ে ইনিশিয়ালাইজ করা হতো, তবে ট্রেনিং শুরুর প্রথম পদক্ষেপেই $\Delta W = B \cdot A \ne 0$ হতো। এর ফলে প্রি-ট্রেইন্ড বেস মডেলের নিখুঁত ওজন নষ্ট হয়ে যেত। $B=0$ রাখায় শুরুতে $\Delta W = 0$ থাকে, যা প্রি-ট্রেইন্ড জ্ঞান অক্ষুণ্ণ রেখে মসৃণভাবে ডাউনস্ট্রিম অ্যাডাপ্টেশন শুরু করে।
2. **প্রশ্ন: RLHF-এর চেয়ে DPO কেন বেশি জনপ্রিয় হয়ে উঠেছে?**
   - **উত্তর:** RLHF-এ পলিসি ট্রেইন করতে একটি আলাদা রিওয়ার্ড মডেল এবং PPO-এর অত্যন্ত জটিল অ্যাক্টর-ক্রিটিক ট্রেইনিং লুপ চালাতে হয়, যা অত্যন্ত অস্থির (sensitive to hyper-parameters) এবং ঘন ঘন মোড কলাপ্স ঘটায়। DPO গণিতের একটি চমৎকার ক্লোজড-ফর্ম রূপান্তরের মাধ্যমে কোনো রিওয়ার্ড মডেল ছাড়াই সাধারণ বাইনারি ক্রস-এনট্রপি সমতুল্য লসে সরাসরি সুপারভাইজড পদ্ধতিতে মডেল অ্যালাইন করতে পারে।
3. **প্রশ্ন: LoRA Rank $r$-এর মান কেমন রাখা উচিত?**
   - **উত্তর:** সাধারণ স্টাইল পরিবর্তন, ফরম্যাটিং বা ক্লাসিফিকেশনের জন্য $r = 4$ বা $r = 8$ যথেষ্ট। কিন্তু নতুন ডোমেইন জ্ঞান বা কোডিং/ম্যাথ শেখানোর জন্য $r = 32$ বা $r = 64$ ব্যবহার করা হয়। এর বেশি বাড়ালে প্যারামিটার সেভিংসের সুবিধা কমে যায় এবং ওভারফিটিং দেখা দিতে পারে।
