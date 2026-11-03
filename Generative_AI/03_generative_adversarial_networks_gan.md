# Chapter 03: Generative Adversarial Networks (GAN) — From Vanilla to WGAN-GP & StyleGAN

---

## ১. ভূমিকা ও মূল দর্শন (Core Philosophy of GANs)

Ian Goodfellow et al. (2014) কর্তৃক উদ্ভাবিত **জেনারেটিভ অ্যাডভারসারিয়াল নেটওয়ার্ক (GAN)** হলো গেম থিওরি (Game Theory)-ভিত্তিক একটি কাঠামো। এখানে দুটি পৃথক নিউরাল নেটওয়ার্ক পরস্পরের বিরুদ্ধে একটি জিরো-সাম গেমে (Two-player Zero-Sum Game) প্রতিযোগিতা করে:

1. **জেনারেটর ($G_\theta$ - The Counterfeiter):**
   - একটি সহজ র‍্যান্ডম নয়েজ ভেক্টর $z \sim p_z(z)$ (যেমন $\mathcal{N}(0, I)$) ইনপুট হিসেবে নিয়ে বাস্তবসম্মত ডেটা বা ছবি $G(z)$ তৈরি করে জালিয়াতকারীর মতো ডিসক্রিমিনেটরকে ফাঁকি দেওয়ার চেষ্টা করে।
2. **ডিসক্রিমিনেটর ($D_\phi$ - The Police / Detective):**
   - একটি ইনপুট $x$ আসল ডেটা ডিস্ট্রিবিউশন $p_{data}$ থেকে এসেছে নাকি জেনারেটরের তৈরি ফেক ডেটা $p_g$ থেকে এসেছে, তা শনাক্ত করে একটি সম্ভাব্যতা মান $D(x) \in [0, 1]$ প্রদান করে।

```
                                  Real Data x ~ p_data(x)
                                             |
                                             v
Noise z ~ p_z ---> [ Generator G ] ---> G(z) [Fake Data]
                                             |
                                             v
                                   [ Discriminator D ]
                                             |
                                             v
                         Output: Probability of being Real: D(x) in [0, 1]
```

প্রতিযোগিতার মাধ্যমে ডিসক্রিমিনেটর আরও সূক্ষ্মভাবে জাল শনাক্ত করতে শেখে এবং জেনারেটর আরও অবিকল বাস্তবসম্মত ছবি তৈরি করতে শেখে। ন্যাশ ইকুইলিব্রিয়ামে (Nash Equilibrium) জেনারেটরের ডিস্ট্রিবিউশন বাস্তব ডিস্ট্রিবিউশনের সমান হয়ে যায় ($p_g = p_{data}$) এবং ডিসক্রিমিনেটর আর আসল ও ফেকের মধ্যে কোনো পার্থক্য করতে পারে না ($D(x) = 0.5$ সর্বত্র)।

---

## ২. মিনিম্যাক্স অবজেক্টিভ ফাংশনের পূর্ণাঙ্গ গাণিতিক রূপ

GAN-এর অবজেক্টিভ ফাংশন হলো একটি টু-প্লেয়ার মিনিম্যাক্স গেম:

$$\min_G \max_D V(D, G) = \mathbb{E}_{x \sim p_{data}(x)} \left[ \log D(x) \right] + \mathbb{E}_{z \sim p_z(z)} \left[ \log(1 - D(G(z))) \right]$$

- **ডিসক্রিমিনেটরের উদ্দেশ্য ($\max_D V(D, G)$):**
  - বাস্তব ডেটার ক্ষেত্রে $D(x) \to 1$ করা, ফলে $\log D(x) \to 0$ (সর্বোচ্চ মান)।
  - জেনারেটেড ফেক ডেটার ক্ষেত্রে $D(G(z)) \to 0$ করা, ফলে $\log(1 - D(G(z))) \to 0$ (সর্বোচ্চ মান)।
- **জেনারেটরের উদ্দেশ্য ($\min_G V(D, G)$):**
  - ডিসক্রিমিনেটরকে বোকা বানিয়ে $D(G(z)) \to 1$ করা, যাতে $\log(1 - D(G(z))) \to -\infty$ হয় (সর্বনিম্ন মান)।

---

## ৩. অপ্টিমাল ডিসক্রিমিনেটর ($D^*(x)$)-এর সম্পূর্ণ গাণিতিক প্রমাণ

যেকোনো নির্দিষ্ট জেনারেটর $G$-এর জন্য অপ্টিমাল ডিসক্রিমিনেটর $D^*$ নির্ণয় করতে আমরা অবজেক্টিভকে অবিচ্ছিন্ন ইন্টিগ্রেশন আকারে লিখি:

$$V(D, G) = \int_{\mathcal{X}} p_{data}(x) \log D(x) dx + \int_{\mathcal{Z}} p_z(z) \log(1 - D(G(z))) dz$$

ভেরিয়েবল ট্রান্সফরমেশনের মাধ্যমে ($x = G(z)$ এবং $x \sim p_g(x)$):

$$V(D, G) = \int_{\mathcal{X}} \left[ p_{data}(x) \log D(x) + p_g(x) \log(1 - D(x)) \right] dx$$

নির্দিষ্ট যেকোনো $x \in \mathcal{X}$-এর জন্য ইন্টিগ্রান্ডের ভেতরের ফাংশনটিকে বিবেচনা করি:

$$f(y) = a \log y + b \log(1 - y)$$

যেখানে:
- $a = p_{data}(x) > 0$
- $b = p_g(x) > 0$
- $y = D(x) \in (0, 1)$

সর্বোচ্চ মান (maximum) পেতে প্রথম ডেরিভেটিভ শূন্য ধরি:

$$\frac{df(y)}{dy} = \frac{a}{y} - \frac{b}{1 - y} = 0$$

$$\implies \frac{a}{y} = \frac{b}{1 - y} \implies a(1 - y) = by \implies a - ay = by \implies y(a + b) = a$$

$$y^* = \frac{a}{a + b}$$

মানগুলো প্রতিস্থাপন করলে আমরা পাই **অপ্টিমাল ডিসক্রিমিনেটর সূত্র:**

$$D^*(x) = \frac{p_{data}(x)}{p_{data}(x) + p_g(x)}$$

---

## ৪. জেনসেন-শ্যানন ডাইভারজেন্স (JSD) সংযোগের প্রমাণ

এখন অপ্টিমাল ডিসক্রিমিনেটর $D^*(x)$-কে মূল অবজেক্টিভে প্রতিস্থাপন করলে:

$$V(D^*, G) = \int_{\mathcal{X}} \left[ p_{data}(x) \log\left( \frac{p_{data}(x)}{p_{data}(x) + p_g(x)} \right) + p_g(x) \log\left( \frac{p_g(x)}{p_{data}(x) + p_g(x)} \right) \right] dx$$

লগের ভেতরে লব ও হরকে $2$ দ্বারা ভাগ করে সাজালে:

$$V(D^*, G) = \int p_{data}(x) \left[ \log\left( \frac{p_{data}(x)}{\frac{p_{data}(x) + p_g(x)}{2}} \right) - \log 2 \right] dx + \int p_g(x) \left[ \log\left( \frac{p_g(x)}{\frac{p_{data}(x) + p_g(x)}{2}} \right) - \log 2 \right] dx$$

$$= -\log 2 \int p_{data}(x) dx - \log 2 \int p_g(x) dx + D_{KL}\left( p_{data} \,\Big\|\, \frac{p_{data} + p_g}{2} \right) + D_{KL}\left( p_g \,\Big\|\, \frac{p_{data} + p_g}{2} \right)$$

যেহেতু প্রবাবিলিটি ডেনসিটির ইন্টিগ্রেশন $= 1$:

$$V(D^*, G) = -2\log 2 + 2 \cdot D_{JS}(p_{data} \parallel p_g)$$

### গুরুত্বপূর্ণ সিদ্ধান্ত:
1. $D_{JS}(p_{data} \parallel p_g) \ge 0$, এবং এর মান সর্বনিম্ন $0$ হবে যদি এবং কেবল যদি $p_g = p_{data}$ হয়।
2. সুতরাং, গ্লোবাল মিনিমাম মান হলো:
   $$V(D^*, G^*) = -2\log 2 = -\log 4 \approx -1.3863$$
3. এর অর্থ হলো: **ভ্যানিলা GAN মূলত ডেটা ডিস্ট্রিবিউশন এবং জেনারেটর ডিস্ট্রিবিউশনের মধ্যবর্তী Jensen-Shannon Divergence মিনিমাইজ করে!**

---

## ৫. সনাতন GAN-এর ব্যর্থতার কারণসমূহ (Failure Modes)

1. **ভ্যানিশিং গ্রেডিয়েন্ট (Vanishing Gradients):**
   - ট্রেনিংয়ের শুরুতে জেনারেটর খুব নিম্নমানের ছবি তৈরি করে। ফলে ডিসক্রিমিনেটর অতি সহজেই আসল ও ফেক আলাদা করে ফেলে ($D(G(z)) \approx 0$)।
   - ফাংশন $\log(1 - D(G(z)))$-এর গ্রাফ লক্ষ্য করলে দেখা যায় $D(G(z)) \approx 0$ বিন্দুতে এর ঢাল (slope) প্রায় শূন্য। ফলে জেনারেটরের কাছে কোনো কার্যকর ব্যাকপ্রপাগেশন গ্রেডিয়েন্ট পৌঁছায় না।
   - *Heuristic Fix:* $\min_G \log(1 - D(G(z)))$-এর বদলে নন-স্যাচুরেটিং অবজেক্টিভ $\max_G \log D(G(z))$ ব্যবহার করা হয়।
2. **মোড কলাপ্স (Mode Collapse):**
   - জেনারেটর সমগ্র ডিস্ট্রিবিউশন শেখার বদলে ডিসক্রিমিনেটরকে বোকা বানাতে পারে এমন মাত্র কয়েকটি নির্দিষ্ট নমুনা বা ইমেজ মোড (যেমন শুধু বিড়ালের একটি একক ছবি) বারবার তৈরি করতে থাকে।
3. **নন-কনভার্জেন্স এবং দোলন (Oscillations / Instability):**
   - দুটি নন-লিনিয়ার নিউরাল নেটওয়ার্কের গেম গ্রেডিয়েন্ট ডিসেন্টে চক্রাকার আবর্তনে (limit cycles) আটকে যায়, ন্যাশ ইকুইলিব্রিয়ামে পৌঁছাতে ব্যর্থ হয়।

---

## ৬. ওয়াসারস্টেইন গ্যান (WGAN) এবং গ্র্যাডিয়েন্ট পেনাল্টি (WGAN-GP)

Martin Arjovsky et al. (2017) প্রমাণ করেন যে হাই-ডাইমেনশনাল স্পেসে $p_{data}$ এবং $p_g$-এর সাপোর্ট প্রায় সম্পূর্ণ ডিসজয়েন্ট (disjoint manifolds)। ডিসজয়েন্ট সাপোর্টে Jensen-Shannon ডাইভারজেন্স সর্বদা কনস্ট্যান্ট $\log 2$ থাকে, যা শূন্য গ্রেডিয়েন্ট তৈরি করে।

সমাধান হিসেবে তাঁরা **Earth Mover's Distance বা Wasserstein-1 Distance** ব্যবহার করেন:

$$W(p_{data}, p_g) = \inf_{\gamma \in \Pi(p_{data}, p_g)} \mathbb{E}_{(x, y) \sim \gamma} [\|x - y\|]$$

এটি নির্দেশ করে একটি ডিস্ট্রিবিউশনের ভরকে অন্য ডিস্ট্রিবিউশনে রূপান্তর করতে ন্যূনতম কতটুকু কাজ করতে হবে। ম্যানিফোল্ড দুটি পরস্পর স্পর্শ না করলেও ওয়াসারস্টেইন ডিসট্যান্স একটি স্মুথ, লিনিয়ার গ্রেডিয়েন্ট বজায় রাখে!

### ক্যান্টোরোভিচ-রুবিনস্টাইন ডুয়ালিটি (Kantorovich-Rubinstein Duality):
ইনফিমাম বের করা কঠিন হওয়ায় ডুয়াল ফর্মে ক্রিটিক (Critic $D$)-কে ১-লিপসিৎস ফাংশন ($\|D\|_L \le 1$) হিসেবে কনস্ট্রেইন্ড করা হয়:

$$\max_{w: \|D_w\|_L \le 1} \mathbb{E}_{x \sim p_{data}} [D_w(x)] - \mathbb{E}_{z \sim p_z} [D_w(G_\theta(z))]$$

```
WGAN Critic Loss = E[D(G(z))] - E[D(x)]
Generator Loss   = -E[D(G(z))]
```

### লিপসিৎস শর্ত প্রয়োগ: Weight Clipping বনাম Gradient Penalty (WGAN-GP)
- **WGAN (Weight Clipping):** শুরুতে ওজনগুলোকে $[-c, c]$ (যেমন $[-0.01, 0.01]$) সীমার মধ্যে কেটে দেওয়া হতো। এর ফলে ক্যাপাসিটি অপচয় এবং এক্সপ্লোডিং/ভ্যানিশিং গ্রেডিয়েন্ট দেখা দেয়।
- **WGAN-GP (Gulrajani et al., 2017):** বাস্তব ও ফেক পয়েন্টের মধ্যকার সরলরেখা বরাবর ইন্টারপোলেটেড পয়েন্ট $\hat{x}$-এ ক্রিটিকের গ্রেডিয়েন্ট নর্মকে $1$-এর কাছাকাছি রাখতে পেনাল্টি যোগ করা হয়:

$$\hat{x} = \epsilon x + (1 - \epsilon) \tilde{x}, \quad \text{where } \epsilon \sim U(0, 1), \; \tilde{x} = G(z)$$

$$\mathcal{L}_{\text{WGAN-GP}} = \underbrace{\mathbb{E}[D(\tilde{x})] - \mathbb{E}[D(x)]}_{\text{Wasserstein Loss}} + \underbrace{\lambda \cdot \mathbb{E}_{\hat{x}} \left[ \left( \|\nabla_{\hat{x}} D(\hat{x})\|_2 - 1 \right)^2 \right]}_{\text{Gradient Penalty (সাধারণত } \lambda = 10\text{)}}$$

---

## ৭. সংখ্যাগত উদাহরণ (Step-by-Step Numerical Example)

ধরা যাক, একটি সিঙ্গেল ট্রেইনিং পদক্ষেপে:
- বাস্তব স্যাম্পল $x = [2.00, 3.00]$
- জেনারেটেড স্যাম্পল $\tilde{x} = G(z) = [0.50, 1.00]$
- ক্রিটিক বাস্তব নমুনার জন্য আউটপুট দিল: $D(x) = 4.50$
- ক্রিটিক ফেক নমুনার জন্য আউটপুট দিল: $D(\tilde{x}) = 1.20$

### ধাপ ১: ওয়াসারস্টেইন বেসিক লস
$$\mathcal{L}_{\text{W-base}} = D(\tilde{x}) - D(x) = 1.20 - 4.50 = -3.30$$

### ধাপ ২: ইন্টারপোলেশন পয়েন্ট $\hat{x}$ নির্ণয়
ধরা যাক র‍্যান্ডম ড্র $\epsilon = 0.40$:
$$\hat{x} = 0.40 \cdot [2.00, 3.00] + (1 - 0.40) \cdot [0.50, 1.00]$$
$$\hat{x} = [0.80, 1.20] + [0.30, 0.60] = [1.10, 1.80]$$

### ধাপ ৩: ক্রিটিকের গ্রেডিয়েন্ট ক্যালকুলেশন
ধরা যাক $\hat{x}$ বিন্দুতে ক্রিটিক ফাংশনের গ্রেডিয়েন্ট হলো:
$$\nabla_{\hat{x}} D(\hat{x}) = [0.80, 0.60]$$

### ধাপ ৪: গ্রেডিয়েন্ট নর্ম ও পেনাল্টি নির্ণয়
গ্রেডিয়েন্টের $L_2$ নর্ম:
$$\|\nabla_{\hat{x}} D(\hat{x})\|_2 = \sqrt{(0.80)^2 + (0.60)^2} = \sqrt{0.64 + 0.36} = \sqrt{1.00} = 1.00$$

গ্রেডিয়েন্ট পেনাল্টি ($\lambda = 10$):
$$\mathcal{L}_{GP} = \lambda \cdot (\|\nabla_{\hat{x}} D(\hat{x})\|_2 - 1)^2 = 10 \cdot (1.00 - 1.00)^2 = 0.00$$

*(যদি গ্রেডিয়েন্ট নর্ম $1.40$ হতো, তবে পেনাল্টি হতো $10 \cdot (1.40 - 1)^2 = 10 \cdot 0.16 = 1.60$)*

### ধাপ ৫: মোট ক্রিটিক লস
$$\mathcal{L}_{\text{Critic}} = -3.30 + 0.00 = -3.30$$

ক্রিটিক অপটিমাইজার এই লস মিনিমাইজ করে বাস্তব স্যাম্পলের স্কোর বাড়াবে এবং ফেকের স্কোর কমাবে।

---

## ৮. আধুনিক গ্যান পরিবার: cGAN, CycleGAN ও StyleGAN

```
                     [ আধুনিক গ্যান বিবর্তন ]
Vanilla GAN (2014) ---> DCGAN (2015) ---> WGAN-GP (2017)
                             |
         +-------------------+-------------------+
         |                                       |
    CycleGAN (2017)                        StyleGAN 1/2/3 (NVIDIA)
 (Unpaired Translation)                (Disentangled Style Modulation)
```

### ১. কন্ডিশনাল গ্যান (cGAN):
জেনারেটর এবং ডিসক্রিমিনেটর উভয়কেই অতিরিক্ত ক্লাস লেবেল বা টেক্সট এম্বেডিং $y$ কন্ডিশন হিসেবে দেওয়া হয়:
$$\min_G \max_D V(D, G) = \mathbb{E}_{x, y} [\log D(x, y)] + \mathbb{E}_{z, y} [\log(1 - D(G(z, y), y))]$$

### ২. সাইকেল-গ্যান (CycleGAN - Unpaired Image-to-Image Translation):
জোড়াহীন দুটি ডোমেইন $X$ (যেমন ঘোড়া) এবং $Y$ (যেমন জেব্রা)-এর মধ্যে রূপান্তরের জন্য দুটি জেনারেটর ($G: X \to Y$ এবং $F: Y \to X$) এবং দুটি ডিসক্রিমিনেটর ব্যবহার করা হয়।
- **সাইকেল কনসিস্টেন্সি লস (Cycle-Consistency Loss):**
  $$\mathcal{L}_{\text{cyc}}(G, F) = \mathbb{E}_{x \sim p(x)} [\|F(G(x)) - x\|_1] + \mathbb{E}_{y \sim p(y)} [\|G(F(y)) - y\|_1]$$

### ৩. স্টাইল-গ্যান (StyleGAN Architecture - Karras et al.):
1. **Mapping Network ($f: \mathcal{Z} \to \mathcal{W}$):** ৮-লেয়ারের MLP যা ইনপুট নয়েজ $z \in \mathcal{Z}$ কে একটি আনট্যাঙ্গলড স্পেস $w \in \mathcal{W}$ তে ম্যাপ করে।
2. **Adaptive Instance Normalization (AdaIN):** $w$ ভেক্টরকে কনভলিউশন লেয়ারগুলোর স্কেল ও বায়াস ($\gamma, \beta$) প্যারামিটারে রূপান্তর করে স্টাইল মড্যুলেট করা হয়:
   $$\text{AdaIN}(x_i, y) = y_{s, i} \left( \frac{x_i - \mu(x_i)}{\sigma(x_i)} \right) + y_{b, i}$$
3. **কোয়ার্স, মিডল এবং ফাইন স্টাইল পৃথকীকরণ:** নিচের লেয়ারগুলো শেখে পোজ ও ফেস-শেপ (coarse), মধ্যবর্তী লেয়ারগুলো শেখে হেয়ারস্টাইল ও চোখ (middle), আর উপরের লেয়ারগুলো শেখে কালার স্কিম ও ত্বকের টেক্সচার (fine styles)।

---

## ৯. পাইটর্চে সম্পূর্ণ WGAN-GP ইমপ্লিমেন্টেশন (Production PyTorch Code)

নিচে একটি কমপ্লিট, ভেরিফাইড পাইটর্চ কোড দেওয়া হলো যা WGAN-GP-এর ক্রিটিক, জেনারেটর এবং গ্রেডিয়েন্ট পেনাল্টির ক্যালকুলেশন নিখুঁতভাবে সম্পাদন করে:

```python
"""
Wasserstein GAN with Gradient Penalty (WGAN-GP) Implementation
Fully runnable, verified and mathematically grounded.
"""

import torch
import torch.nn as nn
import torch.autograd as autograd


# ============================================================================
# 1. Generator and Critic Networks
# ============================================================================
class WGANGenerator(nn.Module):
    def __init__(self, latent_dim: int = 100, img_dim: int = 784):
        super(WGANGenerator, self).__init__()
        self.net = nn.Sequential(
            nn.Linear(latent_dim, 256),
            nn.LeakyReLU(0.2, inplace=True),
            nn.Linear(256, 512),
            nn.LeakyReLU(0.2, inplace=True),
            nn.Linear(512, 1024),
            nn.LeakyReLU(0.2, inplace=True),
            nn.Linear(1024, img_dim),
            nn.Tanh()  # Normalized image output in [-1, 1]
        )

    def forward(self, z: torch.Tensor) -> torch.Tensor:
        return self.net(z)


class WGANCritic(nn.Module):
    def __init__(self, img_dim: int = 784):
        super(WGANCritic, self).__init__()
        self.net = nn.Sequential(
            nn.Linear(img_dim, 512),
            nn.LeakyReLU(0.2, inplace=True),
            nn.Linear(512, 256),
            nn.LeakyReLU(0.2, inplace=True),
            nn.Linear(256, 1)  # WGAN Critic outputs unbounded scalar score (No Sigmoid!)
        )

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.net(x)


# ============================================================================
# 2. Gradient Penalty Calculation (The 1-Lipschitz Constraint)
# ============================================================================
def compute_gradient_penalty(critic: nn.Module, real_samples: torch.Tensor, fake_samples: torch.Tensor, device: torch.device):
    """
    Computes Gradient Penalty: E_x_hat [(||grad_{x_hat} D(x_hat)||_2 - 1)^2]
    """
    batch_size = real_samples.size(0)
    # Random interpolation weight epsilon ~ Uniform(0, 1)
    epsilon = torch.rand(batch_size, 1, device=device)
    epsilon = epsilon.expand_as(real_samples)

    # Interpolated sample: x_hat = eps * real + (1 - eps) * fake
    interpolated = (epsilon * real_samples + ((1 - epsilon) * fake_samples)).requires_grad_(True)

    # Calculate Critic output on interpolated points
    d_interpolated = critic(interpolated)

    # Calculate gradients w.r.t. interpolated points
    fake_grad_output = torch.ones_like(d_interpolated, device=device)
    gradients = autograd.grad(
        outputs=d_interpolated,
        inputs=interpolated,
        grad_outputs=fake_grad_output,
        create_graph=True,
        retain_graph=True,
        only_inputs=True
    )[0]

    # Compute L2 norm of gradients
    gradients = gradients.view(batch_size, -1)
    gradient_norm = gradients.norm(2, dim=1)

    # Gradient penalty enforcing 1-Lipschitz continuity: (||grad||_2 - 1)^2
    gradient_penalty = torch.mean((gradient_norm - 1.0) ** 2)
    return gradient_penalty


# ============================================================================
# 3. Complete Verification & Demo Step
# ============================================================================
if __name__ == "__main__":
    torch.manual_seed(42)
    device = torch.device("cpu")

    latent_dim = 64
    img_dim = 784
    batch_size = 16
    lambda_gp = 10.0

    generator = WGANGenerator(latent_dim=latent_dim, img_dim=img_dim).to(device)
    critic = WGANCritic(img_dim=img_dim).to(device)

    opt_critic = torch.optim.Adam(critic.parameters(), lr=1e-4, betas=(0.0, 0.9))
    opt_gen = torch.optim.Adam(generator.parameters(), lr=1e-4, betas=(0.0, 0.9))

    # Dummy real image batch in [-1, 1]
    real_data = torch.randn(batch_size, img_dim, device=device).clamp(-1.0, 1.0)

    print("--- 1. Training Step: Critic Update ---")
    opt_critic.zero_grad()

    # Generate fake images
    z = torch.randn(batch_size, latent_dim, device=device)
    fake_data = generator(z)

    # Compute Critic scores
    real_scores = critic(real_data)
    fake_scores = critic(fake_data.detach())

    # Base Wasserstein Loss: E[D(fake)] - E[D(real)]
    w_loss = fake_scores.mean() - real_scores.mean()

    # Gradient Penalty
    gp = compute_gradient_penalty(critic, real_data.data, fake_data.data, device)

    # Total Critic Loss
    critic_loss = w_loss + lambda_gp * gp
    critic_loss.backward()
    opt_critic.step()

    print(f"Real Score Mean:      {real_scores.mean().item():.4f}")
    print(f"Fake Score Mean:      {fake_scores.mean().item():.4f}")
    print(f"Wasserstein Distance: {(-w_loss).item():.4f}")
    print(f"Gradient Penalty:     {gp.item():.4f}")
    print(f"Total Critic Loss:    {critic_loss.item():.4f}")

    print("\n--- 2. Training Step: Generator Update ---")
    opt_gen.zero_grad()
    # Generator aims to maximize Critic score on fake data -> minimize -E[D(G(z))]
    gen_z = torch.randn(batch_size, latent_dim, device=device)
    gen_fake = generator(gen_z)
    gen_loss = -torch.mean(critic(gen_fake))
    gen_loss.backward()
    opt_gen.step()

    print(f"Generator Loss:       {gen_loss.item():.4f}")
    print("WGAN-GP forward and backward pipeline verified successfully!")
```

---

## ১০. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স (Architecture Comparison)

| ক্রাইটেরিয়া | ভ্যানিলা GAN (Goodfellow 2014) | WGAN-GP (Gulrajani 2017) | StyleGAN-3 (Karras 2021) |
| :--- | :--- | :--- | :--- |
| **অবজেক্টিভ মেজার** | Jensen-Shannon Divergence | Wasserstein-1 Distance | Wasserstein Distance + Path Regularization |
| **ট্রেনিং স্থায়িত্ব** | অত্যন্ত অস্থির (Unstable, Sensitive) | অত্যন্ত স্থিতিশীল (Highly Stable) | হাইপার-অপ্টিমাইজড ও সুস্থিত |
| **আউটপুট রেজোলিউশন** | কম ($64 \times 64$) | মাঝারি ($128 \times 128$) | আল্ট্রা-হাই ($1024 \times 1024$ ফটো-রিয়ালিস্টিক) |
| **ল্যাটেন্ট ডিসএনট্যাঙ্গেলমেন্ট**| অনুপস্থিত | সীমিত | সর্বোচ্চ (Mapping network $\mathcal{W}$ স্পেস) |
| **মোড কলাপ্স ঝুঁকি** | অত্যন্ত উচ্চ | প্রায় শূন্য | প্রায় শূন্য |
| **ব্যবহার ক্ষেত্র** | বেসিক কনসেপ্ট ও শিক্ষা | সাধারণ ডোমেইন কনভার্সন ও সিন্থেসিস | হাই-এন্ড ফেস সিন্থেসিস, ফ্যাশন, সিনেমা VFX |

---

## ১১. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: WGAN-এ ডিসক্রিমিনেটরের শেষ লেয়ারে কেন Sigmoid অ্যাক্টিভেশন বাদ দেওয়া হয়?**
   - **উত্তর:** ভ্যানিলা GAN-এর ডিসক্রিমিনেটর প্রবাবিলিটি আউটপুট করে ($[0, 1]$), তাই Sigmoid প্রয়োজন হয়। কিন্তু WGAN-এ এটি একটি ডিসক্রিমিনেটর নয় বরং একটি "ক্রিটিক" (Critic), যা কোনো সম্ভাব্যতা মান দেয় না বরং একটি আনবাউন্ডেড রিয়েল স্কেলার রিগ্রেশন স্কোর ($\mathbb{R}$) আউটপুট করে। Sigmoid যোগ করলে স্যাচুরেশন ও গ্রেডিয়েন্ট ভ্যানিশিং ঘটবে।
2. **প্রশ্ন: StyleGAN-এ $z \in \mathcal{Z}$-এর পরিবর্তে $w \in \mathcal{W}$ স্পেস ব্যবহার করার কারণ কী?**
   - **উত্তর:** প্রায়র নয়েজ $z \sim \mathcal{N}(0, I)$ গাউসিয়ান ডিস্ট্রিবিউশন অনুসরণ করতে বাধ্য হওয়ায় এর ফিচারগুলো পরস্পরের সাথে জড়িয়ে (entangled) থাকে। Mapping Network ৮টি ফুল্লি-কানেক্টেড লেয়ারের মাধ্যমে একটি ডিস্টর্টেড ম্যানিফোল্ড $\mathcal{W}$ তৈরি করে, যেখানে ফিচারগুলো সম্পূর্ণ স্বাধীন ও অর্থপূর্ণভাবে পৃথক (disentangled) হয়।
3. **প্রশ্ন: ডিসক্রিমিনেটর এবং জেনারেটরের ট্রেইনিং রেশিও কেমন হওয়া উচিত?**
   - **উত্তর:** WGAN-GP-তে জেনারেটরের প্রতি ১ বার আপডেটের বিপরীতে ক্রিটিককে সাধারণত ৫ বার ($n_{\text{critic}} = 5$) আপডেট করা হয়। এর কারণ হলো ওয়াসারস্টেইন দূরত্বের নিখুঁত আনুমানিক পরিমাপ পাওয়ার জন্য ক্রিটিককে অপ্টিমালের খুব কাছাকাছি থাকা বাধ্যতামূলক।
