# Chapter 02: Variational Autoencoders (VAE) & Discrete Latents (VQ-VAE)

---

## ১. ভূমিকা ও সনাতন অটো-এনকোডারের সীমাবদ্ধতা (Traditional Autoencoder Limitations)

একটি স্ট্যান্ডার্ড বা সনাতন অটো-এনকোডার (Deterministic Autoencoder) ইনপুট ডেটা $x \in \mathbb{R}^D$-কে একটি লো-ডাইমেনশনাল ভেক্টর $z \in \mathbb{R}^d$ ($d \ll D$) তে সংকুচিত (compress) করে এবং পরবর্তীতে ডিকোডার নেটওয়ার্কের মাধ্যমে পুনরায় $x$ কে পুনর্গঠন (reconstruct) করে:

$$z = f_\phi(x), \quad \hat{x} = g_\theta(z)$$

```
[Standard Deterministic Autoencoder]
x (Input) ---> [Encoder f_phi] ---> z (Single Point in Latent Space) ---> [Decoder g_theta] ---> x_hat
                                    (No smooth distribution, isolated clusters with holes!)
```

### সমস্যা (Why Autoencoders Fail at Generation):
1. **বিচ্ছিন্ন ল্যাটেন্ট স্পেস (Discontinuous / Fragmented Latent Space):**
   - স্ট্যান্ডার্ড অটো-এনকোডার ল্যাটেন্ট স্পেসের প্রতিটি পয়েন্টকে একটি বিচ্ছিন্ন বিন্দুরূপে (point mass) শেখে। এর ফলে স্পেসের মাঝে বিশাল "ফাঁকা গহ্বর" (voids / holes) তৈরি হয়।
2. **নতুন নমুনা জেনারেশনে অক্ষমতা (Cannot Sample Novel Data):**
   - আপনি যদি ল্যাটেন্ট স্পেস থেকে একটি র‍্যান্ডম ভেক্টর $z \sim \mathcal{N}(0, I)$ স্যাম্পল করেন, তবে সেই পয়েন্টটি সম্ভবত প্রশিক্ষণ ডেটার কোনো ক্লাস্টারের বাইরে একটি ফাঁকা অঞ্চলে পড়বে। ফলে ডিকোডার অবাস্তব, অর্থহীন (garbage/blurry) আউটপুট তৈরি করবে।
3. **সমাধান — ভ্যারিয়েশনাল অটো-এনকোডার (VAE):**
   - VAE ল্যাটেন্ট স্পেসকে বিচ্ছিন্ন বিন্দুর বদলে একটি **ধারাবাহিক সম্ভাব্যতা বিন্যাস (continuous probability distribution)** হিসেবে শেখে। প্রতিটি ইনপুট $x$-এর জন্য এনকোডার একটি একক পয়েন্ট না দিয়ে একটি গড় ($\mu$) এবং ভ্যারিয়েন্স ($\sigma^2$) আউটপুট করে।

---

## ২. VAE-এর সম্ভাব্যতা কাঠামো (Probabilistic Formulation)

ধরা যাক, আমাদের পর্যবেক্ষণকৃত ডেটা $x$ একটি সুপ্ত বা লুকানো ভ্যারিয়েবল (latent variable) $z$ থেকে উৎপন্ন হয়েছে:
1. $z \sim p(z) = \mathcal{N}(0, I)$ (Prior Distribution)
2. $x \sim p_\theta(x|z)$ (Likelihood / Generative Process parameterized by Decoder $\theta$)

আমাদের লক্ষ্য হলো ডেটার সত্য প্রবাবিলিটি $p(x)$ ম্যাক্সিমাইজ করা (Marginal Likelihood):

$$p(x) = \int p_\theta(x|z) p(z) dz$$

```
                         p(z) ~ N(0, I) [Prior]
                               |
                               v
                       [Decoder p_theta(x|z)]
                               |
                               v
                           x [Generated Data]
```

### ইন্ট্র্যাক্টেবল পোস্টেরিয়র (The Intractability of $p(z|x)$):
বেইজের উপপাদ্য (Bayes' Theorem) অনুযায়ী:

$$p_\theta(z|x) = \frac{p_\theta(x|z) p(z)}{p(x)} = \frac{p_\theta(x|z) p(z)}{\int p_\theta(x|z) p(z) dz}$$

এখানে হর (denominator) $\int p_\theta(x|z) p(z) dz$-এর ইন্টিগ্রেশন সমগ্র ল্যাটেন্ট ডাইমেনশনের ওপর বিস্তৃত। যদি $z$ এর ডাইমেনশন $d = 32$ বা $128$ হয়, তবে হাইপার-ডাইমেনশনাল স্পেসে ইন্টিগ্রেশন গণনা করা গাণিতিকভাবে অসম্ভব (intractable)।

অতএব, আমরা একটি ট্র্যাকেবল প্যারামেট্রিক ডিস্ট্রিবিউশন $q_\phi(z|x)$ (যা এনকোডার নিউরাল নেটওয়ার্ক দ্বারা প্যারামেট্রাইজড) দিয়ে প্রকৃত পোস্টেরিয়র $p_\theta(z|x)$-কে আনুমানিক পরিমাপ (variational approximation) করি।

---

## ৩. এভিডেন্স লোয়ার বাউন্ড (ELBO)-এর সম্পূর্ণ গাণিতিক প্রমাণ

আমরা $\log p(x)$-কে নিম্নরূপ বিশ্লেষণ করি:

$$\log p(x) = \log \int p_\theta(x, z) dz = \log \int q_\phi(z|x) \frac{p_\theta(x, z)}{q_\phi(z|x)} dz$$

এক্সপেক্টেশন আকারে প্রকাশ করলে:

$$\log p(x) = \log \mathbb{E}_{q_\phi(z|x)} \left[ \frac{p_\theta(x, z)}{q_\phi(z|x)} \right]$$

যেহেতু লগারিদম একটি কনকেভ ফাংশন ($\log(\cdot)$ is concave), জেনসেনের অসমতা (Jensen's Inequality, $\log \mathbb{E}[Y] \ge \mathbb{E}[\log Y]$) প্রয়োগ করে:

$$\log p(x) \ge \mathbb{E}_{q_\phi(z|x)} \left[ \log \frac{p_\theta(x, z)}{q_\phi(z|x)} \right] \triangleq \text{ELBO}(\phi, \theta)$$

চলুন এক্সপেক্টেশনের ভেতরের অংশটি ভেঙে দেখি:

$$\text{ELBO} = \mathbb{E}_{q_\phi(z|x)} \left[ \log \frac{p_\theta(x|z) p(z)}{q_\phi(z|x)} \right]$$

$$= \mathbb{E}_{q_\phi(z|x)} [\log p_\theta(x|z)] + \mathbb{E}_{q_\phi(z|x)} \left[ \log \frac{p(z)}{q_\phi(z|x)} \right]$$

$$= \mathbb{E}_{q_\phi(z|x)} [\log p_\theta(x|z)] - \mathbb{E}_{q_\phi(z|x)} \left[ \log \frac{q_\phi(z|x)}{p(z)} \right]$$

$$= \underbrace{\mathbb{E}_{q_\phi(z|x)} [\log p_\theta(x|z)]}_{\text{Reconstruction Term (ডিকোডারের নির্ভুলতা)}} - \underbrace{D_{KL}\left( q_\phi(z|x) \,\parallel\, p(z) \right)}_{\text{Regularization Term (প্রায়রের কাছাকাছি রাখার পেনাল্টি)}}$$

### সত্য মার্জিনাল ও ELBO-এর ব্যবধান:
$$\log p(x) = \text{ELBO}(\phi, \theta) + D_{KL}\left( q_\phi(z|x) \,\parallel\, p_\theta(z|x) \right)$$

যেহেতু $D_{KL} \ge 0$, তাই $\text{ELBO} \le \log p(x)$। যখন আমরা ELBO বাড়াই, তখন স্বয়ংক্রিয়ভাবে $\log p(x)$ বৃদ্ধি পায় এবং variational approximation $q_\phi(z|x)$ প্রকৃত $p_\theta(z|x)$-এর খুব নিকটবর্তী হয়।

---

## ৪. গাউসিয়ান KL ডাইভারজেন্সের ক্লোজড-ফর্ম সমাধান (Analytical Gaussian KL Derivation)

ধরা যাক:
- প্রায়র: $p(z) = \mathcal{N}(0, I)$
- ভ্যারিয়েশনাল পোস্টেরিয়র: $q_\phi(z|x) = \mathcal{N}(\mu, \text{diag}(\sigma^2))$

দুটি ডায়াগোনাল মাল্টিভেরিয়েট গাউসিয়ান ডিস্ট্রিবিউশনের ক্ষেত্রে KL ডাইভারজেন্সের মান:

$$D_{KL}(q_\phi(z|x) \parallel p(z)) = \int q(z) \log \frac{q(z)}{p(z)} dz = \mathbb{E}_q [\log q(z)] - \mathbb{E}_q [\log p(z)]$$

$d$-ডাইমেনশনাল ভেক্টরের জন্য:
$$\log q(z) = -\frac{d}{2} \log(2\pi) - \frac{1}{2} \sum_{j=1}^d \log \sigma_j^2 - \frac{1}{2} \sum_{j=1}^d \frac{(z_j - \mu_j)^2}{\sigma_j^2}$$

$$\mathbb{E}_q [\log q(z)] = -\frac{d}{2} \log(2\pi) - \frac{1}{2} \sum_{j=1}^d \log \sigma_j^2 - \frac{1}{2} \sum_{j=1}^d 1$$

অনুরূপভাবে $p(z) = \mathcal{N}(0, I)$-এর জন্য:
$$\log p(z) = -\frac{d}{2} \log(2\pi) - \frac{1}{2} \sum_{j=1}^d z_j^2$$

যেহেতু $\mathbb{E}_q [z_j^2] = \mu_j^2 + \sigma_j^2$:
$$\mathbb{E}_q [\log p(z)] = -\frac{d}{2} \log(2\pi) - \frac{1}{2} \sum_{j=1}^d (\mu_j^2 + \sigma_j^2)$$

এখন উভয় অংশ বিয়োগ করলে:
$$D_{KL}(q_\phi(z|x) \parallel p(z)) = -\frac{1}{2} \sum_{j=1}^d \left( 1 + \log(\sigma_j^2) - \mu_j^2 - \sigma_j^2 \right)$$

নিউমেরিক্যাল স্ট্যাবিলিটির জন্য নিউরাল নেটওয়ার্ক সরাসরি $\sigma^2$ আউটপুট না করে $\log(\sigma^2)$ (log-variance) আউটপুট করে:

$$\mathcal{L}_{KL} = -\frac{1}{2} \sum_{j=1}^d \left( 1 + \log \sigma_j^2 - \mu_j^2 - \exp(\log \sigma_j^2) \right)$$

---

## ৫. দ্য রিপ্যারামেট্রাইজেশন ট্রিক (The Reparameterization Trick)

### সমস্যা (Backpropagation Bottleneck):
যদি আমরা সরাসরি $z \sim q_\phi(z|x) = \mathcal{N}(\mu_\phi(x), \sigma_\phi^2(x))$ থেকে স্যাম্পল করি:

```
[Input x] ---> [Encoder phi] ---> [ mu, sigma^2 ]
                                         |
                                         v
                                  [ Sample z ~ N(mu, sigma^2) ]  <--- STOCHASTIC NODE!
                                         |                             Gradient cannot flow!
                                         v
                                  [ Decoder theta ] ---> x_hat
```

স্টোকাস্টিক স্যাম্পলিং নোডের কারণে ব্যাকপ্রপাগেশন করার সময় $\nabla_\phi \mathbb{E}_{q_\phi}[f(z)]$ ক্যালকুলেট করা যায় না, কারণ র‍্যান্ডম ড্র কোনো ডিফারেনশিয়েবল ফাংশন নয়।

### কৌশল ও সমাধান (The Trick):
র‍্যান্ডমনেসকে নেটওয়ার্কের অভ্যন্তরীণ গ্রাফ থেকে সম্পূর্ণ আলাদা করে একটি স্বাধীন অগজিলিয়ারি ভ্যারিয়েবল হিসেবে ইনপুট দেওয়া হয়:

$$\epsilon \sim \mathcal{N}(0, I)$$
$$z = g_\phi(x, \epsilon) = \mu_\phi(x) + \sigma_\phi(x) \odot \epsilon$$

এখানে $\odot$ হলো এলিমেন্ট-ওয়াইজ গুণফল (Hadamard product)।

```
                                  eps ~ N(0, I) [Independent Noise]
                                         |
[Input x] ---> [Encoder phi] ---> [ mu, sigma ]
                                    \   /
                                     \ /
                                [ z = mu + sigma * eps ]  <--- Fully Differentiable Node!
                                         |
                                         v
                                  [ Decoder theta ] ---> x_hat
```

### গ্রেডিয়েন্ট ডেরিভেশন (Mathematical Proof):
$$\nabla_\phi \mathbb{E}_{z \sim q_\phi(z|x)} [f(z)] = \nabla_\phi \mathbb{E}_{\epsilon \sim \mathcal{N}(0, I)} [f(\mu_\phi(x) + \sigma_\phi(x) \odot \epsilon)]$$

চেইন রুল অনুযায়ী:
$$= \mathbb{E}_{\epsilon \sim \mathcal{N}(0, I)} \left[ \nabla_z f(z) \cdot \nabla_\phi (\mu_\phi(x) + \sigma_\phi(x) \odot \epsilon) \right]$$

যেহেতু এক্সপেক্টেশনের ভেতরে কোনো $\phi$-ডিপেনডেন্ট ডিস্ট্রিবিউশন নেই, তাই সাধারণ মন্টে-কার্লো স্যাম্পলিংয়ের মাধ্যমে একটি সিঙ্গেল $\epsilon$ ড্র করেই ব্যাকপ্রপাগেশন নিখুঁতভাবে চালানো সম্ভব!

---

## ৬. সংখ্যাগত উদাহরণ (Step-by-Step Numerical Example)

ধরা যাক, আমাদের ল্যাটেন্ট ডাইমেনশন $d = 2$। একটি ইনপুট ইমেজের জন্য এনকোডার নিচের মানগুলো আউটপুট করল:
- $\mu = [0.60, -0.40]$
- $\log \sigma^2 = [-0.2231, 0.3365]$

### ধাপ ১: $\sigma^2$ এবং $\sigma$ বের করা
$$\sigma_1^2 = \exp(-0.2231) \approx 0.80, \quad \sigma_1 = \sqrt{0.80} \approx 0.8944$$
$$\sigma_2^2 = \exp(0.3365) \approx 1.40, \quad \sigma_2 = \sqrt{1.40} \approx 1.1832$$

### ধাপ ২: স্ট্যান্ডার্ড নয়েজ স্যাম্পল করা
র‍্যান্ডম ড্র থেকে প্রাপ্ত:
$$\epsilon = [0.50, -0.80]$$

### ধাপ ৩: রিপ্যারামেট্রাইজেশনের মাধ্যমে $z$ নির্ণয়
$$z_1 = \mu_1 + \sigma_1 \cdot \epsilon_1 = 0.60 + (0.8944 \times 0.50) = 0.60 + 0.4472 = 1.0472$$
$$z_2 = \mu_2 + \sigma_2 \cdot \epsilon_2 = -0.40 + (1.1832 \times -0.80) = -0.40 - 0.9466 = -1.3466$$
$$z = [1.0472, -1.3466]$$

### ধাপ ৪: KL ডাইভারজেন্স পেনাল্টি গণনা
প্রতিটি ডাইমেনশনের জন্য মান:
- ডাইমেনশন ১:
  $$1 + \log \sigma_1^2 - \mu_1^2 - \sigma_1^2 = 1 + (-0.2231) - (0.60)^2 - 0.80$$
  $$= 1 - 0.2231 - 0.36 - 0.80 = -0.3831$$
- ডাইমেনশন ২:
  $$1 + \log \sigma_2^2 - \mu_2^2 - \sigma_2^2 = 1 + 0.3365 - (-0.40)^2 - 1.40$$
  $$= 1 + 0.3365 - 0.16 - 1.40 = -0.2235$$

মোট KL লস:
$$D_{KL} = -\frac{1}{2} \left[ (-0.3831) + (-0.2235) \right] = -\frac{1}{2} [-0.6066] = 0.3033$$

### ধাপ ৫: রিকনস্ট্রাকশন লস ও টোটাল লস
ধরা যাক ডিকোডার আউটপুটের সাথে আসল ইমেজের Mean Squared Error (MSE):
$$\mathcal{L}_{\text{recon}} = 1.2500$$

মোট VAE লস:
$$\mathcal{L}_{\text{total}} = \mathcal{L}_{\text{recon}} + \mathcal{L}_{KL} = 1.2500 + 0.3033 = 1.5533$$

---

## ৭. অ্যাডভান্সড ভ্যারিয়েন্টসমূহ: $\beta$-VAE ও VQ-VAE

### ১. $\beta$-VAE (Disentanglement Learning):
স্ট্যান্ডার্ড VAE-তে রিকনস্ট্রাকশন এবং KL পেনাল্টির মধ্যে ট্রেইড-অফ নিয়ন্ত্রণের জন্য একটি হাইপারপ্যারামিটার $\beta$ যুক্ত করা হয়:

$$\mathcal{L}_{\beta\text{-VAE}} = \mathcal{L}_{\text{recon}} + \beta \cdot D_{KL}(q_\phi(z|x) \parallel p(z))$$

- **$\beta > 1$:** ল্যাটেন্ট ভ্যারিয়েবলগুলোর পারস্পরিক নির্ভরতা শূন্যের কোঠায় চলে আসে। প্রতিটি ল্যাটেন্ট ডাইমেনশন মানুষের বোধগম্য একক অর্থপূর্ণ বৈশিষ্ট্য (যথা: মানুষের মুখের হাসির কোণ, চশমা থাকা বা না থাকা, রোটেশন কোণ) আলাদাভাবে প্রকাশ করে। একে **Disentangled Representation** বলা হয়।

---

### ২. VQ-VAE (Vector Quantized VAE):
কন্টিনিউয়াস ল্যাটেন্ট ভ্যারিয়েবলের অন্যতম প্রধান দুর্বলতা হলো **"Posterior Collapse"** (যেখানে ডিকোডার ল্যাটেন্ট কোড $z$-কে উপেক্ষা করে শক্তিশালী অটো-রিগ্রেসিভ প্রায়র শেখে) এবং ব্লারি ছবি তৈরি হওয়া।
DeepMind-এর van den Oord et al. (2017) প্রস্তাব করেন **VQ-VAE**, যা ল্যাটেন্ট স্পেসকে বিচ্ছিন্ন (discrete) ভেক্টরের একটি কোডবুকে কোয়ান্টাইজ করে।

```
[Input x] ---> [Encoder] ---> z_e(x) (Continuous Latent Representation)
                                |
                                v (Nearest Neighbor Lookup)
                      [ Codebook e_1, e_2, ..., e_K ]
                                |
                                v
                             z_q(x) = e_k  (Discrete Quantized Vector)
                                |
[Reconstructed x_hat] <--- [Decoder] <--- (Straight-Through Estimator passes gradient)
```

#### কোয়ান্টাইজেশন প্রক্রিয়া (Vector Quantization):
কোডবুকে $K$ সংখ্যক $D$-ডাইমেনশনাল ভেক্টর থাকে: $E = \{e_1, e_2, \dots, e_K\}$।
এনকোডার থেকে প্রাপ্ত কন্টিনিউয়াস ভেক্টর $z_e(x)$-এর সবচেয়ে নিকটবর্তী কোডবুক ভেক্টরটি বেছে নেওয়া হয়:

$$z_q(x) = e_k \quad \text{where} \quad k = \arg\min_j \|z_e(x) - e_j\|_2$$

#### স্ট্রেইট-থ্রু এস্টিমেটর (Straight-Through Estimator - STE):
$\arg\min$ অপারেশনটি নন-ডিফারেনশিয়েবল ($\frac{\partial z_q}{\partial z_e} = 0$ সর্বত্র)। তাই ব্যাকপ্রপাগেশনের সময় আমরা ফরোয়ার্ড পাসে $z_q$ ব্যবহার করি, কিন্তু ব্যাকওয়ার্ড পাসে গ্রেডিয়েন্ট সরাসরি $z_e$-তে কপি করে পাস করি:

$$z_q(x) = z_e(x) + \text{sg}[z_q(x) - z_e(x)]$$

এখানে $\text{sg}[\cdot]$ হলো স্টপ-গ্রেডিয়েন্ট (Stop Gradient) অপারেটর।

#### VQ-VAE লস ফাংশন:
$$\mathcal{L}_{\text{VQ}} = \underbrace{\mathcal{L}_{\text{recon}}(x, \hat{x})}_{\text{Reconstruction Loss}} + \underbrace{\|\text{sg}[z_e(x)] - z_q(x)\|_2^2}_{\text{Codebook / Vector Quantization Loss}} + \underbrace{\beta \|z_e(x) - \text{sg}[z_q(x)]\|_2^2}_{\text{Commitment Loss}}$$

1. **Reconstruction Loss:** ডিকোডার ও এনকোডারকে ইমেজ নিখুঁত করতে বাধ্য করে।
2. **Codebook Loss:** নির্বাচিত কোডবুক ভেক্টরগুলোকে এনকোডার আউটপুটের দিকে টেনে আনে ($L_2$ error)।
3. **Commitment Loss:** এনকোডারকে নির্দেশ দেয় যাতে তার আউটপুট $z_e(x)$ কোডবুক এম্বেডিং থেকে অতিরিক্ত দূরে সরে না যায় (স্থায়িত্ব বজায় রাখে, সাধারণত $\beta = 0.25$)।

---

## ৮. পাইটর্চে সম্পূর্ণ VAE ও VQ-VAE ইমপ্লিমেন্টেশন (Production PyTorch Code)

নিচে একটি স্বয়ংসম্পূর্ণ, ভেরিফাইড পাইটর্চ স্ক্রিপ্ট দেওয়া হলো যা VAE (রিপ্যারামেট্রাইজেশন ও গাউসিয়ান KL সহ) এবং VQ-VAE (কোডবুক ও স্ট্রেইট-থ্রু এস্টিমেটর সহ) বাস্তবায়ন করে:

```python
"""
Variational Autoencoder (VAE) & Vector Quantized VAE (VQ-VAE) Implementation
Fully runnable, verified and mathematically grounded.
"""

import torch
import torch.nn as nn
import torch.nn.functional as F


# ============================================================================
# 1. Standard Variational Autoencoder (VAE)
# ============================================================================
class VariationalAutoencoder(nn.Module):
    def __init__(self, input_dim: int = 784, hidden_dim: int = 256, latent_dim: int = 32):
        super(VariationalAutoencoder, self).__init__()
        self.latent_dim = latent_dim

        # Encoder: x -> hidden -> (mu, log_var)
        self.encoder = nn.Sequential(
            nn.Linear(input_dim, hidden_dim),
            nn.LeakyReLU(0.2),
            nn.Linear(hidden_dim, hidden_dim // 2),
            nn.LeakyReLU(0.2)
        )
        self.fc_mu = nn.Linear(hidden_dim // 2, latent_dim)
        self.fc_logvar = nn.Linear(hidden_dim // 2, latent_dim)

        # Decoder: z -> hidden -> x_reconstructed
        self.decoder = nn.Sequential(
            nn.Linear(latent_dim, hidden_dim // 2),
            nn.LeakyReLU(0.2),
            nn.Linear(hidden_dim // 2, hidden_dim),
            nn.LeakyReLU(0.2),
            nn.Linear(hidden_dim, input_dim),
            nn.Sigmoid()  # Pixel intensities in [0, 1]
        )

    def encode(self, x: torch.Tensor):
        h = self.encoder(x)
        mu = self.fc_mu(h)
        logvar = self.fc_logvar(h)
        return mu, logvar

    def reparameterize(self, mu: torch.Tensor, logvar: torch.Tensor) -> torch.Tensor:
        """
        Reparameterization Trick: z = mu + sigma * epsilon
        where epsilon ~ N(0, I)
        """
        if self.training:
            std = torch.exp(0.5 * logvar)
            eps = torch.randn_like(std)
            return mu + eps * std
        else:
            return mu

    def decode(self, z: torch.Tensor) -> torch.Tensor:
        return self.decoder(z)

    def forward(self, x: torch.Tensor):
        mu, logvar = self.encode(x)
        z = self.reparameterize(mu, logvar)
        x_recon = self.decode(z)
        return x_recon, mu, logvar


def vae_loss_function(recon_x: torch.Tensor, x: torch.Tensor, mu: torch.Tensor, logvar: torch.Tensor, beta: float = 1.0):
    """
    ELBO = E[log p(x|z)] - beta * D_KL(q(z|x) || p(z))
    Loss = Recon_Loss + beta * KL_Divergence
    """
    # Reconstruction loss (Binary Cross Entropy for pixel intensities)
    recon_loss = F.binary_cross_entropy(recon_x, x, reduction='sum')

    # Closed-form Gaussian KL Divergence: -0.5 * sum(1 + log(sigma^2) - mu^2 - sigma^2)
    kl_div = -0.5 * torch.sum(1 + logvar - mu.pow(2) - logvar.exp())

    total_loss = recon_loss + beta * kl_div
    return total_loss, recon_loss, kl_div


# ============================================================================
# 2. Vector Quantized Layer (VQ-VAE Core)
# ============================================================================
class VectorQuantizer(nn.Module):
    def __init__(self, num_embeddings: int, embedding_dim: int, commitment_cost: float = 0.25):
        super(VectorQuantizer, self).__init__()
        self.num_embeddings = num_embeddings
        self.embedding_dim = embedding_dim
        self.commitment_cost = commitment_cost

        # Codebook embeddings
        self.embedding = nn.Embedding(num_embeddings, embedding_dim)
        self.embedding.weight.data.uniform_(-1.0 / num_embeddings, 1.0 / num_embeddings)

    def forward(self, inputs: torch.Tensor):
        # inputs shape: [Batch, Channel, Height, Width] -> permute to [B, H, W, C]
        inputs = inputs.permute(0, 2, 3, 1).contiguous()
        input_shape = inputs.shape
        flat_input = inputs.view(-1, self.embedding_dim)

        # Compute L2 distances: ||z_e - e_j||^2 = z_e^2 + e_j^2 - 2 * z_e * e_j
        distances = (
            torch.sum(flat_input ** 2, dim=1, keepdim=True)
            + torch.sum(self.embedding.weight ** 2, dim=1)
            - 2 * torch.matmul(flat_input, self.embedding.weight.t())
        )

        # Find nearest codebook vector indices
        encoding_indices = torch.argmin(distances, dim=1).unsqueeze(1)
        encodings = torch.zeros(encoding_indices.shape[0], self.num_embeddings, device=inputs.device)
        encodings.scatter_(1, encoding_indices, 1)

        # Quantize latent representations
        quantized = torch.matmul(encodings, self.embedding.weight).view(input_shape)

        # VQ Loss formulation
        # 1. Codebook loss: ||sg[z_e] - e||^2
        loss_codebook = F.mse_loss(quantized, inputs.detach())
        # 2. Commitment loss: beta * ||z_e - sg[e]||^2
        loss_commitment = self.commitment_cost * F.mse_loss(quantized.detach(), inputs)
        vq_loss = loss_codebook + loss_commitment

        # Straight-Through Estimator: pass gradients directly to encoder
        quantized = inputs + (quantized - inputs).detach()

        # Permute back to [Batch, Channel, Height, Width]
        quantized = quantized.permute(0, 3, 1, 2).contiguous()
        return quantized, vq_loss, encoding_indices


# ============================================================================
# 3. Complete Verification & Demo
# ============================================================================
if __name__ == "__main__":
    torch.manual_seed(42)

    print("--- 1. Testing Standard VAE ---")
    batch_size = 16
    vae = VariationalAutoencoder(input_dim=784, hidden_dim=256, latent_dim=16)
    dummy_img = torch.rand(batch_size, 784)

    recon, mu, logvar = vae(dummy_img)
    total_loss, recon_l, kl_l = vae_loss_function(recon, dummy_img, mu, logvar)

    print(f"Input Shape:       {dummy_img.shape}")
    print(f"Reconstructed:     {recon.shape}")
    print(f"Latent Mean (mu):  {mu.shape}, LogVar: {logvar.shape}")
    print(f"Total ELBO Loss:   {total_loss.item():.4f} (Recon: {recon_l.item():.4f}, KL: {kl_l.item():.4f})")

    # Generate novel samples from prior N(0, I)
    vae.eval()
    with torch.no_grad():
        random_z = torch.randn(4, 16)
        generated_samples = vae.decode(random_z)
        print(f"Generated Samples from Prior N(0, I): {generated_samples.shape}")

    print("\n--- 2. Testing VQ-VAE Vector Quantizer ---")
    vq = VectorQuantizer(num_embeddings=128, embedding_dim=64, commitment_cost=0.25)
    dummy_feature_map = torch.randn(4, 64, 8, 8)  # [Batch, Channels, H, W]
    quantized_latents, vq_loss, indices = vq(dummy_feature_map)

    print(f"Continuous Latent Shape: {dummy_feature_map.shape}")
    print(f"Quantized Latent Shape:  {quantized_latents.shape}")
    print(f"VQ-Loss (Codebook+Commit): {vq_loss.item():.4f}")
    print(f"Unique Codebook Tokens used: {len(torch.unique(indices))}/128")
    print("All VAE and VQ-VAE components executed successfully!")
```

---

## ৯. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স (Architecture Comparison & When to Use)

| ক্রাইটেরিয়া (Feature) | ভ্যানিলা অটো-এনকোডার (AE) | ভ্যারিয়েশনাল অটো-এনকোডার (VAE) | ভেক্টর কোয়ান্টাইজড VAE (VQ-VAE) |
| :--- | :--- | :--- | :--- |
| **ল্যাটেন্ট স্পেসের ধরন** | বিচ্ছিন্ন (Discrete points / Arbitrary) | ধারাবাহিক স্বাভাবিক বিন্যাস ($\mathcal{N}(\mu, \sigma^2)$) | বিচ্ছিন্ন কোডবুক ইনডেক্স ($e_k \in \mathbb{R}^D$) |
| **জেনারেটিভ সামর্থ্য** | অত্যন্ত দুর্বল (Fails on random noise) | ভালো, তবে ছবি কিছুটা ব্লারি (Blurry images) | অত্যন্ত উচ্চমানের ও শার্প (Sharp details) |
| **গাণিতিক ভিত্তি** | রিকনস্ট্রাকশন অপটিমাইজেশন | এলবো (ELBO) ও ভ্যারিয়েশনাল ইনফারেন্স | ভেক্টর কোয়ান্টাইজেশন ও স্ট্রেইট-থ্রু এস্টিমেটর |
| **Posterior Collapse ঝুঁকি** | প্রযোজ্য নয় | উচ্চ (যদি ডিকোডারের ক্ষমতা অতিরিক্ত হয়) | শূন্য (কোডবুকের কারণে কোল্যাপ্স হয় না) |
| **কোথায় ব্যবহার করবেন** | ডাইমেনশনালিটি রিডাকশন, অ্যানোমালি ডিটেকশন | ডেটা ইন্টারপোলেশন, ড্রাগ ডিসকভারি, স্মুথ ল্যাটেন্ট ওয়াক | আধুনিক ডিফিউশন মডেলের লেটেন্ট স্টেজ (Stable Diffusion VAE) |
| **কোথায় ব্যবহার করবেন না** | হাই-রেজোলিউশন ফটো-রিয়ালিস্টিক সিন্থেসিস | ক্রিস্প টেক্সট বা তীক্ষ্ণ এজ জেনারেশন | অত্যন্ত সিম্পল বা লো-রিসোর্স এমবেডেড সিস্টেম |

---

## ১০. হ্যান্ডস-অন প্র্যাকটিস ও ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: VAE-তে MSE এবং BCE লস কখন ব্যবহার করা উচিত?**
   - **উত্তর:** ইনপুট ডেটা যদি স্বাভাবিকীকৃত পিক্সেল ইনটেনসিটি হয় ($x \in [0, 1]$), তবে আউটপুট লেয়ারে Sigmoid ব্যবহার করে Binary Cross Entropy (BCE) লস ব্যবহার করা হয় (বার্নোলি ডিস্ট্রিবিউশন এজাম্পশন)। আর ডেটা যদি আনবাউন্ডেড রিয়েল ভ্যালু হয় ($x \in \mathbb{R}$), তখন গাউসিয়ান লাইকলিহুড এজাম্পশনে Mean Squared Error (MSE) লস প্রযোজ্য।
2. **প্রশ্ন: রিপ্যারামেট্রাইজেশন ট্রিক ছাড়া কি VAE ট্রেইন করা সম্ভব?**
   - **উত্তর:** হ্যাঁ, REINFORCE অ্যালগরিদম (Score Function Estimator) ব্যবহার করে করা সম্ভব: $\nabla_\phi \mathbb{E}[f(z)] = \mathbb{E}[f(z) \nabla_\phi \log q_\phi(z|x)]$। কিন্তু এর ভ্যারিয়েন্স (variance) অত্যন্ত উচ্চ হওয়ায় কনভার্জেন্স অসম্ভব ধীরগতির ও অস্থির হয়ে পড়ে। রিপ্যারামেট্রাইজেশন ট্রিক অত্যন্ত কম ভ্যারিয়েন্সযুক্ত গ্র্যাডিয়েন্ট প্রদান করে।
3. **প্রশ্ন: VQ-VAE-তে Straight-Through Estimator কেন প্রয়োজন?**
   - **উত্তর:** নিকটবর্তী কোডবুক ভেক্টর নির্বাচনের কাজ ($\arg\min$) একটি স্টেপ-ফাংশন, যার ডেরিভেটিভ সর্বত্র শূন্য। STE ফরোয়ার্ড পাসে কোয়ান্টাইজড ভেক্টর পাঠায় কিন্তু ব্যাকওয়ার্ড পাসে এনকোডার আউটপুটে আনচেঞ্জড গ্রেডিয়েন্ট কপি করে পাস করে, ফলে এন্ড-টু-এন্ড ব্যাকপ্রপাগেশন সম্ভব হয়।
