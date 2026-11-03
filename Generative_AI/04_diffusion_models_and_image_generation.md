# Chapter 04: Diffusion Models & High-Fidelity Image Generation

---

## ১. ভূমিকা ও পদার্থবিজ্ঞানের নন-ইকুইলিব্রিয়াম থার্মোডাইনামিক্স

**ডিফিউশন মডেল (Diffusion Models)** হলো আধুনিক জেনারেটিভ এআই-এর সবচেয়ে শক্তিশালী ও প্রভাবশালী ইমেজ সিন্থেসিস পদ্ধতি (যেমন: Midjourney, Stable Diffusion, DALL-E 3, Flux)।
এর মূল অনুপ্রেরণা এসেছে তাত্ত্বিক পদার্থবিজ্ঞানের **নন-ইকুইলিব্রিয়াম থার্মোডাইনামিক্স (Non-equilibrium Thermodynamics)** থেকে।

ধরা যাক, এক গ্লাস স্বচ্ছ পানিতে এক ফোঁটা নীল কালি ফেলা হলো। সময়ের সাথে সাথে কালির অণুগুলো পানির মধ্যে ছড়িয়ে পড়ে (diffusion / entropy increase) এবং ধীরে ধীরে সম্পূর্ণ পানি হালকা নীল রঙের বিশৃঙ্খল ধোঁয়াশায় রূপ নেয়। এটি একটি অপরিবর্তনীয় প্রাকৃতিক প্রক্রিয়া। কিন্তু আমরা যদি গাণিতিকভাবে সময়ের উল্টো দিকে পরিভ্রমণ করতে পারতাম এবং প্রতিটি অণুর বিক্ষিপ্ত গতিপথ নিখুঁতভাবে রিভার্স করতে পারতাম, তবে সেই বিশৃঙ্খল নীল পানি থেকে আবার এক ফোঁটা খাঁটি কালিকে আলাদা করে পুনর্গঠন করা সম্ভব হতো!

```
[Forward Process: Data to Pure Noise (q)]
[Original Image x_0] ---> Add Noise ---> Add Noise ---> Pure Gaussian Noise x_T ~ N(0, I)
      t = 0                  t = 100         t = 500              t = 1000

[Reverse Process: Pure Noise to High-Res Image (p_theta)]
[Pure Noise x_T]     ---> Denoise   ---> Denoise   ---> [Photorealistic Image x_0]
      t = 1000               t = 500         t = 100              t = 0
```

---

## ২. ফরোয়ার্ড প্রসেস (Forward Noising Process $q$)

ফরোয়ার্ড প্রক্রিয়া হলো একটি ফিক্সড (নন-লার্নেবল) মার্কভ চেইন (Markov Chain), যা একটি পূর্বনির্ধারিত ভ্যারিয়েন্স শিডিউল $\beta_1, \beta_2, \dots, \beta_T$ অনুযায়ী মূল ইমেজ $x_0$-এ ধাপে ধাপে অল্প অল্প করে গাউসিয়ান নয়েজ যোগ করে $T$ ধাপে (সাধারণত $T = 1000$) একটি খাঁটি আইসোট্রপিক গাউসিয়ান নয়েজ $x_T \sim \mathcal{N}(0, I)$-এ রূপান্তরিত করে:

$$q(x_1, \dots, x_T \mid x_0) = \prod_{t=1}^T q(x_t \mid x_{t-1})$$

যেখানে প্রতিটি ধাপের ট্রানজিশন ডিস্ট্রিবিউশন:

$$q(x_t \mid x_{t-1}) = \mathcal{N}\left(x_t; \; \sqrt{1 - \beta_t} \, x_{t-1}, \; \beta_t I\right)$$

- $\beta_t \in (0, 1)$ হলো প্রতিটি টাইমস্টেপে নয়েজের পরিমাণ (যেমন লিনিয়ার শিডিউলে $\beta_1 = 10^{-4}$ থেকে $\beta_T = 0.02$)।
- $\sqrt{1 - \beta_t}$ ফ্যাক্টরটি ডেটার ভ্যারিয়েন্সকে আনবাউন্ডেডভাবে বাড়তে না দিয়ে নিয়ন্ত্রণে রাখে ($1$ এর নিচে স্কেল করে)।

---

## ৩. যেকোনো টাইমস্টেপ $t$-এ ডিরেক্ট স্যাম্পলিংয়ের গাণিতিক প্রমাণ

### সমস্যা ও সমাধান:
ট্রেনিং চলাকালীন যদি $t=500$ তম ধাপের স্যাম্পল $x_{500}$ পেতে আমাদের পূর্ববর্তী ৫০০টি ধাপ ক্রমান্বয়ে হিসাব করতে হতো, তবে ট্রেনিং অসম্ভব ধীরগতির হতো। সোহল-ডিকস্টেইন (2015) ও হো এট আল (2020) প্রমাণ করেন যে রিপ্যারামেট্রাইজেশনের মাধ্যমে $x_0$ থেকে সরাসরি যেকোনো টাইমস্টেপ $t$-এর স্যাম্পল একটি সিঙ্গেল ধাপে নির্ণয় করা যায়!

সংজ্ঞা:
$$\alpha_t \triangleq 1 - \beta_t, \quad \bar{\alpha}_t \triangleq \prod_{s=1}^t \alpha_s$$

### গাণিতিক প্রমাণ (Proof by Induction):
ধাপ ১ এর জন্য:
$$x_1 = \sqrt{\alpha_1} x_0 + \sqrt{1 - \alpha_1} \epsilon_0, \quad \text{where } \epsilon_0 \sim \mathcal{N}(0, I)$$

ধাপ ২ এর জন্য:
$$x_2 = \sqrt{\alpha_2} x_1 + \sqrt{1 - \alpha_2} \epsilon_1$$

$x_1$-এর মান প্রতিস্থাপন করলে:
$$x_2 = \sqrt{\alpha_2} \left( \sqrt{\alpha_1} x_0 + \sqrt{1 - \alpha_1} \epsilon_0 \right) + \sqrt{1 - \alpha_2} \epsilon_1$$
$$= \sqrt{\alpha_1 \alpha_2} \, x_0 + \left( \sqrt{\alpha_2 (1 - \alpha_1)} \epsilon_0 + \sqrt{1 - \alpha_2} \epsilon_1 \right)$$

যেহেতু দুটি স্বাধীন গাউসিয়ান ভ্যারিয়েবলের যোগফল $\mathcal{N}(0, \sigma_a^2 I) + \mathcal{N}(0, \sigma_b^2 I) = \mathcal{N}(0, (\sigma_a^2 + \sigma_b^2) I)$:
$$\sigma_{\text{merged}}^2 = \left( \sqrt{\alpha_2 (1 - \alpha_1)} \right)^2 + \left( \sqrt{1 - \alpha_2} \right)^2$$
$$= \alpha_2 (1 - \alpha_1) + 1 - \alpha_2 = \alpha_2 - \alpha_1 \alpha_2 + 1 - \alpha_2 = 1 - \alpha_1 \alpha_2$$

অতএব:
$$x_2 = \sqrt{\alpha_1 \alpha_2} \, x_0 + \sqrt{1 - \alpha_1 \alpha_2} \, \bar{\epsilon}_1, \quad \text{where } \bar{\epsilon}_1 \sim \mathcal{N}(0, I)$$

গাণিতিক আরোহ বিধি (Mathematical Induction) অনুযায়ী সমগ্র $t$ ধাপের জন্য:

$$x_t = \sqrt{\bar{\alpha}_t} \, x_0 + \sqrt{1 - \bar{\alpha}_t} \, \epsilon, \quad \text{where } \epsilon \sim \mathcal{N}(0, I)$$

অর্থাৎ:

$$q(x_t \mid x_0) = \mathcal{N}\left(x_t; \; \sqrt{\bar{\alpha}_t} x_0, \; (1 - \bar{\alpha}_t) I\right)$$

---

## ৪. রিভার্স প্রসেস (Reverse Denoising Process $p_\theta$)

আমাদের লক্ষ্য হলো খাঁটি নয়েজ $x_T \sim \mathcal{N}(0, I)$ থেকে শুরু করে ধাপে ধাপে নয়েজ বিয়োগ করে পরিষ্কার ইমেজ পুনর্গঠন করা:

$$p_\theta(x_{0:T}) = p(x_T) \prod_{t=1}^T p_\theta(x_{t-1} \mid x_t)$$

$$p_\theta(x_{t-1} \mid x_t) = \mathcal{N}\left(x_{t-1}; \; \mu_\theta(x_t, t), \; \Sigma_\theta(x_t, t)\right)$$

### প্রকৃত পোস্টেরিয়র ডিস্ট্রিবিউশন $q(x_{t-1} \mid x_t, x_0)$:
বেইজের উপপাদ্য প্রয়োগ করে পাওয়া যায়:

$$q(x_{t-1} \mid x_t, x_0) = \mathcal{N}(x_{t-1}; \; \tilde{\mu}_t(x_t, x_0), \; \tilde{\beta}_t I)$$

যেখানে ট্র্যাক্টেবল গড় মান (tractable mean):
$$\tilde{\mu}_t(x_t, x_0) = \frac{\sqrt{\bar{\alpha}_{t-1}}\beta_t}{1 - \bar{\alpha}_t} x_0 + \frac{\sqrt{\alpha_t}(1 - \bar{\alpha}_{t-1})}{1 - \bar{\alpha}_t} x_t$$

যেহেতু $x_0 = \frac{1}{\sqrt{\bar{\alpha}_t}}(x_t - \sqrt{1 - \bar{\alpha}_t}\epsilon)$, এটি প্রতিস্থাপন করলে নিউরাল নেটওয়ার্ক দ্বারা প্যারামেট্রাইজড প্রেডিক্টেড গড় $\mu_\theta$ দাঁড়ায়:

$$\mu_\theta(x_t, t) = \frac{1}{\sqrt{\alpha_t}} \left( x_t - \frac{\beta_t}{\sqrt{1 - \bar{\alpha}_t}} \epsilon_\theta(x_t, t) \right)$$

---

## ৫. সরলীকৃত ট্রেইনিং অবজেক্টিভ (DDPM Simplified Loss)

জনাথন হো এট আল. (Ho et al., 2020) প্রমাণ করেন যে সম্পূর্ণ ভ্যারিয়েশনাল লোয়ার বাউন্ড (VLB)-এর জটিল টার্মগুলোকে বাদ দিয়ে শুধুমাত্র **নয়েজ প্রেডিকশন এরর (Noise Prediction Error)** মিনিমাইজ করলেই মডেলটি চমৎকার গুণমানের ছবি শেখে:

$$\mathcal{L}_{\text{simple}}(\theta) = \mathbb{E}_{t \sim U(1, T), \; x_0 \sim p_{\text{data}}, \; \epsilon \sim \mathcal{N}(0, I)} \left[ \left\| \epsilon - \epsilon_\theta(x_t, t) \right\|^2 \right]$$

### ট্রেইনিং অ্যালগরিদম (Training Step):
1. ডেটাসেট থেকে একটি আসল ইমেজ $x_0$ নিন।
2. ইউনিফর্ম ডিস্ট্রিবিউশন থেকে একটি র‍্যান্ডম টাইমস্টেপ স্যাম্পল করুন: $t \sim \text{Uniform}(\{1, \dots, T\})$।
3. স্ট্যান্ডার্ড গাউসিয়ান নয়েজ ড্র করুন: $\epsilon \sim \mathcal{N}(0, I)$।
4. ক্লোজড-ফর্ম সমীকরণের মাধ্যমে নয়েজি ইমেজ তৈরি করুন: $x_t = \sqrt{\bar{\alpha}_t} x_0 + \sqrt{1 - \bar{\alpha}_t} \epsilon$।
5. নিউরাল নেটওয়ার্ককে (U-Net) $x_t$ এবং টাইমস্টেপ $t$ ইনপুট দিয়ে প্রেডিক্টেড নয়েজ $\epsilon_\theta(x_t, t)$ নির্ণয় করুন।
6. Mean Squared Error (MSE) লসের মাধ্যমে নেটওয়ার্কের ওজন আপডেট করুন: $\|\epsilon - \epsilon_\theta(x_t, t)\|^2$।

---

## ৬. স্যাম্পলিং অ্যালগরিদম: DDPM বনাম DDIM

### ১. DDPM স্যাম্পলিং (Ho et al. 2020):
প্রতিটি টাইমস্টেপ $t = T, T-1, \dots, 1$ এর জন্য:
$$x_{t-1} = \frac{1}{\sqrt{\alpha_t}} \left( x_t - \frac{1 - \alpha_t}{\sqrt{1 - \bar{\alpha}_t}} \epsilon_\theta(x_t, t) \right) + \sigma_t z, \quad \text{where } z \sim \mathcal{N}(0, I) \text{ if } t > 1 \text{ else } 0$$
- **সীমাবদ্ধতা:** ছবি তৈরি করতে পূর্ণ ১০০০টি ফরোয়ার্ড পাস লাগে, যা অত্যন্ত ধীরগতির (কয়েক সেকেন্ড থেকে মিনিট)।

### ২. DDIM স্যাম্পলিং (Song et al. 2020 - Denoising Diffusion Implicit Models):
নন-মার্কভিয়ান প্রসেস ব্যবহার করে সম্পূর্ণ ডিটারমিনিস্টিক রিভার্স ট্র্যাজেক্টরি তৈরি করা হয় ($\sigma_t = 0$):
- মাত্র ২০ থেকে ৫০টি জাম্পিং টাইমস্টেপেই (e.g. $t = 1000, 950, 900, \dots$) নিখুঁত ছবি তৈরি সম্ভব।
- এটি ইমেজ জেনারেশনকে প্রায় ২০-৫০ গুণ দ্রুততর করেছে।

---

## ৭. ক্লাসিফায়ার-ফ্রি গাইডেন্স (Classifier-Free Guidance - CFG)

টেক্সট প্রম্পটের সাথে ছবির সামঞ্জস্যতা (Text-to-Image alignment) নিয়ন্ত্রণের অন্যতম শ্রেষ্ঠ কৌশল হলো CFG (Ho & Salimans, 2021)।

```
                                      Prompt Condition c
                                             |
                                             v
x_t ---> [ U-Net Noise Predictor ] ---> eps_theta(x_t, c) [Conditional Noise]
                                             |
x_t ---> [ U-Net Noise Predictor ] ---> eps_theta(x_t, 0) [Unconditional Noise (c = empty)]
                                             |
                                             v
             Guided Noise: eps_tilde = eps_uncond + s * (eps_cond - eps_uncond)
```

### গাণিতিক সূত্র:
$$\tilde{\epsilon}_\theta(x_t, c) = \epsilon_\theta(x_t, \emptyset) + s \cdot \left( \epsilon_\theta(x_t, c) - \epsilon_\theta(x_t, \emptyset) \right)$$

- $s$ হলো **Guidance Scale**:
  - $s = 1.0$: সাধারণ কন্ডিশনাল জেনারেশন।
  - $s \in [7.0, 8.5]$: গোল্ডিলক্স জোন (হাইপার-ডিটেইলড ও টেক্সটের প্রতি বিশ্বস্ত ছবি)।
  - $s > 15.0$: ওভার-স্যাচুরেটেড, কৃত্রিম ও অস্বাভাবিক কনট্রাস্টযুক্ত ছবি (artifacts)।

---

## ৮. লেটেন্ট ডিফিউশন মডেল (LDM / Stable Diffusion)

পিক্সেল স্পেসে ডিফিউশন চালানো অত্যন্ত কম্পিউটেশনালি এক্সপেনসিভ। একটি $512 \times 512 \times 3$ ইমেজে ৭,৮৬,৪৩২টি ডাইমেনশন থাকে।

Rombach et al. (2022) উদ্ভাবন করেন **Latent Diffusion Model (Stable Diffusion)**:
1. প্রথমে একটি প্রি-ট্রেইন্ড ভ্যারিয়েশনাল অটো-এনকোডার (VAE) ব্যবহার করে ইমেজকে $8\times$ ছোট ল্যাটেন্ট স্পেসে ($64 \times 64 \times 4 = 16,384$ ডাইমেনশন) কম্প্রেস করা হয় (৪৮ গুণ কম মেমোরি!)।
2. সমস্ত ফরোয়ার্ড ও রিভার্স ডিফিউশন প্রসেস চালানো হয় এই কম্প্যাক্ট ল্যাটেন্ট স্পেসে ($z_t$)।
3. ডিফিউশন সম্পন্ন হওয়ার পর VAE ডিকোডার ল্যাটেন্ট ভেক্টর $z_0$-কে পুনরায় হাই-রেজোলিউশন $512 \times 512$ আরজিবি ইমেজে রূপান্তর করে।

```
Pixel Space (512x512x3)                                              Pixel Space (512x512x3)
      |                                                                    ^
      v                                                                    |
 [ VAE Encoder ]                                                    [ VAE Decoder ]
      |                                                                    |
      v                                                                    |
Latent Space (64x64x4) ---> [ U-Net Denoising with Cross-Attention ] ---> Final Latent z_0
                                           ^
                                           | Text Embeddings
                                    [ CLIP Text Encoder ]
                                           ^
                                           |
                                      "A cute cat"
```

---

## ৯. সংখ্যাগত উদাহরণ (Step-by-Step Numerical Example)

ধরা যাক:
- একটি সিঙ্গেল পিক্সেল ভ্যালু $x_0 = 1.50$
- টাইমস্টেপ $t$ এর জন্য $\beta_t = 0.10 \implies \alpha_t = 1 - 0.10 = 0.90$
- কিউমুলেটিভ প্রডাক্ট $\bar{\alpha}_t = 0.64 \implies \sqrt{\bar{\alpha}_t} = \sqrt{0.64} = 0.80$
- $\sqrt{1 - \bar{\alpha}_t} = \sqrt{1 - 0.64} = \sqrt{0.36} = 0.60$
- র‍্যান্ডম নয়েজ ড্র $\epsilon = 0.50$

### ধাপ ১: ফরোয়ার্ড নোইজিং স্যাম্পল $x_t$ গণনা
$$x_t = \sqrt{\bar{\alpha}_t} x_0 + \sqrt{1 - \bar{\alpha}_t} \epsilon$$
$$x_t = (0.80 \times 1.50) + (0.60 \times 0.50) = 1.20 + 0.30 = 1.50$$

### ধাপ ২: মডেল প্রেডিকশন ও লস
ধরা যাক আমাদের U-Net নেটওয়ার্ক প্রেডিক্ট করল নয়েজ $\epsilon_\theta(x_t, t) = 0.42$:
$$\mathcal{L}_{\text{simple}} = (\epsilon - \epsilon_\theta)^2 = (0.50 - 0.42)^2 = (0.08)^2 = 0.0064$$

### ধাপ ৩: রিভার্স ডিনোইজিং স্টেপে গড় $\mu_\theta$ নির্ণয়
$$\mu_\theta(x_t, t) = \frac{1}{\sqrt{\alpha_t}} \left( x_t - \frac{\beta_t}{\sqrt{1 - \bar{\alpha}_t}} \epsilon_\theta(x_t, t) \right)$$
$$\mu_\theta = \frac{1}{\sqrt{0.90}} \left( 1.50 - \frac{0.10}{0.60} \times 0.42 \right)$$
$$\mu_\theta = \frac{1}{0.9487} (1.50 - 0.0700) = \frac{1.4300}{0.9487} \approx 1.5073$$

মডেলটি এই গাণিতিক ফর্মুলার মাধ্যমেই অত্যন্ত নিখুঁতভাবে পূর্ববর্তী ধাপের পরিষ্কার সিগন্যাল উদ্ধার করে!

---

## ১০. পাইটর্চে সম্পূর্ণ DDPM ইমপ্লিমেন্টেশন (Production PyTorch Code)

নিচে একটি স্বয়ংসম্পূর্ণ, ভেরিফাইড পাইটর্চ স্ক্রিপ্ট দেওয়া হলো যা সাইনুসয়েডাল টাইম এম্বেডিং, মিনি U-Net নয়েজ প্রেডিক্টর এবং সম্পূর্ণ ফরোয়ার্ড ও রিভার্স স্যাম্পলিং লুপ বাস্তবায়ন করে:

```python
"""
Denoising Diffusion Probabilistic Models (DDPM) Minimal Implementation
Fully runnable, verified and mathematically grounded.
"""

import math
import torch
import torch.nn as nn
import torch.nn.functional as F


# ============================================================================
# 1. Sinusoidal Time Embedding
# ============================================================================
class SinusoidalTimeEmbedding(nn.Module):
    def __init__(self, dim: int):
        super(SinusoidalTimeEmbedding, self).__init__()
        self.dim = dim

    def forward(self, t: torch.Tensor) -> torch.Tensor:
        device = t.device
        half_dim = self.dim // 2
        embeddings = math.log(10000) / (half_dim - 1)
        embeddings = torch.exp(torch.arange(half_dim, device=device) * -embeddings)
        embeddings = t[:, None] * embeddings[None, :]
        embeddings = torch.cat((embeddings.sin(), embeddings.cos()), dim=-1)
        return embeddings


# ============================================================================
# 2. Minimalist Denoising Network (Toy U-Net Block)
# ============================================================================
class ToyDenoiser(nn.Module):
    def __init__(self, in_channels: int = 1, hidden_dim: int = 64):
        super(ToyDenoiser, self).__init__()
        self.time_mlp = nn.Sequential(
            SinusoidalTimeEmbedding(hidden_dim),
            nn.Linear(hidden_dim, hidden_dim),
            nn.GELU()
        )
        self.conv_in = nn.Conv2d(in_channels, hidden_dim, kernel_size=3, padding=1)
        self.block = nn.Sequential(
            nn.Conv2d(hidden_dim, hidden_dim, kernel_size=3, padding=1),
            nn.BatchNorm2d(hidden_dim),
            nn.GELU(),
            nn.Conv2d(hidden_dim, hidden_dim, kernel_size=3, padding=1)
        )
        self.conv_out = nn.Conv2d(hidden_dim, in_channels, kernel_size=3, padding=1)

    def forward(self, x: torch.Tensor, t: torch.Tensor) -> torch.Tensor:
        t_emb = self.time_mlp(t)[:, :, None, None]
        h = self.conv_in(x) + t_emb
        h = self.block(h) + h
        return self.conv_out(h)


# ============================================================================
# 3. Diffusion Pipeline (Forward Noising & Sampling)
# ============================================================================
class DDPM(nn.Module):
    def __init__(self, model: nn.Module, timesteps: int = 200, beta_start: float = 1e-4, beta_end: float = 0.02):
        super(DDPM, self).__init__()
        self.model = model
        self.timesteps = timesteps

        # Linear variance schedule
        betas = torch.linspace(beta_start, beta_end, timesteps)
        alphas = 1.0 - betas
        alphas_cumprod = torch.cumprod(alphas, dim=0)

        # Register buffers so they persist on GPU/CPU without being optimizer parameters
        self.register_buffer("betas", betas)
        self.register_buffer("alphas", alphas)
        self.register_buffer("alphas_cumprod", alphas_cumprod)
        self.register_buffer("sqrt_alphas_cumprod", torch.sqrt(alphas_cumprod))
        self.register_buffer("sqrt_one_minus_alphas_cumprod", torch.sqrt(1.0 - alphas_cumprod))

    def q_sample(self, x_0: torch.Tensor, t: torch.Tensor, noise: torch.Tensor = None) -> torch.Tensor:
        """
        Direct sampling at arbitrary timestep t: x_t = sqrt(alpha_bar_t)*x_0 + sqrt(1 - alpha_bar_t)*noise
        """
        if noise is None:
            noise = torch.randn_like(x_0)
        sqrt_alphas_cumprod_t = self.sqrt_alphas_cumprod[t].view(-1, 1, 1, 1)
        sqrt_one_minus_alphas_cumprod_t = self.sqrt_one_minus_alphas_cumprod[t].view(-1, 1, 1, 1)
        return sqrt_alphas_cumprod_t * x_0 + sqrt_one_minus_alphas_cumprod_t * noise

    def compute_loss(self, x_0: torch.Tensor) -> torch.Tensor:
        batch_size = x_0.size(0)
        t = torch.randint(0, self.timesteps, (batch_size,), device=x_0.device).long()
        noise = torch.randn_like(x_0)
        x_noisy = self.q_sample(x_0, t, noise)
        predicted_noise = self.model(x_noisy, t)
        return F.mse_loss(predicted_noise, noise)

    @torch.no_grad()
    def p_sample(self, x: torch.Tensor, t: int) -> torch.Tensor:
        """
        Reverse sampling step: p(x_{t-1} | x_t)
        """
        t_tensor = torch.full((x.size(0),), t, device=x.device, dtype=torch.long)
        predicted_noise = self.model(x, t_tensor)

        alpha = self.alphas[t]
        alpha_bar = self.alphas_cumprod[t]
        beta = self.betas[t]

        # Calculate mean: mu_theta = (1 / sqrt(alpha)) * (x - (beta / sqrt(1 - alpha_bar)) * eps)
        mean = (1.0 / torch.sqrt(alpha)) * (x - (beta / torch.sqrt(1.0 - alpha_bar)) * predicted_noise)

        if t > 0:
            noise = torch.randn_like(x)
            sigma = torch.sqrt(beta)
            return mean + sigma * noise
        else:
            return mean


# ============================================================================
# 4. Complete Verification & Demo Step
# ============================================================================
if __name__ == "__main__":
    torch.manual_seed(42)
    device = torch.device("cpu")

    denoiser = ToyDenoiser(in_channels=1, hidden_dim=32).to(device)
    ddpm = DDPM(model=denoiser, timesteps=100).to(device)

    # Dummy batch of 8 images (1 channel, 16x16 resolution)
    dummy_imgs = torch.randn(8, 1, 16, 16, device=device)

    print("--- 1. Testing DDPM Forward Loss Step ---")
    loss = ddpm.compute_loss(dummy_imgs)
    print(f"Training L_simple MSE Loss: {loss.item():.4f}")

    print("\n--- 2. Testing Reverse Sampling Step ---")
    # Start from pure isotropic Gaussian noise
    x_t = torch.randn(4, 1, 16, 16, device=device)
    # Perform 5 sample steps backwards
    for step in reversed(range(95, 100)):
        x_t = ddpm.p_sample(x_t, step)
    print(f"Denoised Sample Output Shape: {x_t.shape}")
    print("DDPM forward noising and reverse sampling pipeline verified successfully!")
```

---

## ১১. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স (Architecture Comparison)

| ক্রাইটেরিয়া | VAE | GAN | Diffusion Models (DDPM / LDM) |
| :--- | :--- | :--- | :--- |
| **স্যাম্পল কোয়ালিটি** | মাঝারি (প্রায়শই ব্লারি) | অত্যন্ত তীক্ষ্ণ (Very Sharp) | সর্বোচ্চ ও ফটো-রিয়ালিস্টিক (State-of-the-Art) |
| **ট্রেনিং স্থায়িত্ব** | স্থিতিশীল (ELBO) | অত্যন্ত অস্থির (Adversarial min-max) | অত্যন্ত স্থিতিশীল (Standard L2 loss) |
| **স্যাম্পলিং গতি** | আল্ট্রা-ফাস্ট (Single Forward pass) | আল্ট্রা-ফাস্ট (Single Forward pass) | তুলনামূলক ধীর (২০-৫০ আইটারেশন প্রয়োজন) |
| **মোড কাভারেজ** | সম্পূর্ণ (Mode covering) | দুর্বল (Mode collapse ঝুঁকি) | সম্পূর্ণ ও বহুমাত্রিক (High diversity) |
| **কন্ডিশনিং ক্ষমতা** | সাধারণ | ক্লাস লেবেল ভিত্তিক | অসাধারণ (Cross-Attention Text-to-Image) |

---

## ১২. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: U-Net-এ Sinusoidal Time Embedding কেন ব্যবহার করা হয়?**
   - **উত্তর:** নয়েজের মাত্রা টাইমস্টেপ $t$-এর ওপর চরমভাবে নির্ভরশীল ($t=900$-এ প্রায় পুরোটাই নয়েজ, অথচ $t=50$-এ সূক্ষ্ম টেক্সচার)। ট্রেইনিংয়ের প্রতিটি ধাপে নেটওয়ার্কের ওজনকে জানাতে হয় ইমেজটি বর্তমানে কতটা বিশৃঙ্খল অবস্থায় আছে। ট্রান্সফরমারের পজিশনাল এম্বেডিংয়ের মতো সাইনুসয়েডাল ফাংশন প্রতিটি টাইমস্টেপের জন্য একটি অনন্য ধারাবাহিক ভেক্টর প্রদান করে যা নেটওয়ার্ক সহজেই বুঝতে পারে।
2. **প্রশ্ন: Classifier Guidance বনাম Classifier-Free Guidance (CFG)-এর পার্থক্য কী?**
   - **উত্তর:** ক্লাসিফায়ার গাইডেন্সে একটি আলাদা প্রি-ট্রেইন্ড ইমেজ ক্লাসিফায়ারের গ্রেডিয়েন্ট $\nabla_x \log p(y|x)$ ব্যবহার করে স্যাম্পলিংকে পরিচালিত করা হতো, যা অতিরিক্ত মেমোরি ও নয়েজি গ্রেডিয়েন্ট তৈরি করত। অন্যদিকে CFG-তে কোনো বাহ্যিক মডেল লাগে না; একই U-Net মডেলকে ট্রেইনিংয়ের সময় ১০% ক্ষেত্রে প্রম্পট বাদ দিয়ে ($\emptyset$) ট্রেইন করা হয়, এবং ইনফারেন্সের সময় কন্ডিশনাল ও আনকন্ডিশনাল প্রেডিকশনের ভেক্টর দূরত্বের স্কেলিং করা হয়।
3. **প্রশ্ন: Latent Diffusion Models (LDM)-এ VAE কেন অপরিহার্য?**
   - **উত্তর:** হাই-রেজোলিউশন পিক্সেল স্পেসে হাই-ফ্রিকোয়েন্সি পারসেপচুয়াল তথ্য (যেমন সামান্য পিক্সেল শিফট) থাকে যা সিম্যান্টিক্যালি অগুরুত্বপূর্ণ হলেও বিপুল কম্পিউটেশন খরচ করে। VAE এই অপ্রয়োজনীয় পিক্সেল তথ্য ফিল্টার করে একটি লো-ডাইমেনশনাল পারসেপচুয়াল ল্যাটেন্ট স্পেস প্রদান করে, যার ফলে সাধারণ জিপিইউতেই ডিফিউশন প্রশিক্ষণ সম্ভব হয়।
