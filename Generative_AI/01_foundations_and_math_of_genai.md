# অধ্যায় ১: জেনারেটিভ এআই-এর গাণিতিক ও তাত্ত্বিক ভিত্তি (Mathematical Foundations of Generative AI)

> *"To truly understand generative artificial intelligence, one must look past the buzzwords and understand the fundamental problem it solves: how to approximate an unimaginably complex, high-dimensional probability distribution $P_{\text{data}}(X)$ and draw new samples from it that are indistinguishable from reality."*
> 
> মেশিন লার্নিংয়ের সাধারণ মডেলগুলো যেখানে কেবল বিদ্যমান ডেটার প্যাটার্ন দেখে লেবেল প্রেডিক্ট করে, সেখানে জেনারেটিভ মডেল সম্পূর্ণ নতুন তথ্য তৈরি করে। এই অধ্যায়ে ডিসক্রিমিনেটিভ বনাম জেনারেটিভ মডেলিংয়ের পার্থক্য, জয়েন্ট ডিস্ট্রিবিউশন, ইনফরমেশন থিওরি (শ্যানন এন্ট্রপি, ক্রস-এন্ট্রপি), কুলব্যাক-লেইব্লার (KL) ও জেনসেন-শ্যানন (JS) ডাইভারজেন্সের গাণিতিক প্রমাণ, মোড-কভারিং বনাম মোড-সিকিং আচরণ, এবং ভ্যারিয়েশনাল ইনফারেন্সের ভিত্তিপ্রস্তর **Evidence Lower Bound (ELBO)**-এর সম্পূর্ণ ডেরিভেশন বিশদভাবে সংকলিত হয়েছে।

---

## 📑 সম্পূর্ণ বিষয়সূচি (Table of Contents)
1. [ডিসক্রিমিনেটিভ বনাম জেনারেটিভ মডেলিং: মৌলিক প্যারাডাইম শিফট](#১-ডিসক্রিমিনেটিভ-বনাম-জেনারেটিভ-মডেলিং)
   - ১.১ $P(Y \mid X)$ বনাম $P(X)$ এবং $P(X, Y)$
   - ১.২ বেইস রুল সংযোগ (Bayes' Rule Connection)
   - ১.৩ হাই-ডাইমেনশনাল স্পেস ও ইনট্র্যাক্টেবল মার্জিনাল লাইকলিহুড ($P(X) = \int P(X, z) dz$)
2. [ইনফরমেশন থিওরি ও প্রোবাবিলিটি মেট্রিক্স](#২-ইনফরমেশন-থিওরি-ও-প্রোবাবিলিটি-মেট্রিক্স)
   - ২.১ শ্যানন এন্ট্রপি ও ক্রস-এন্ট্রপির গাণিতিক অর্থ
   - ২.২ কুলব্যাক-লেইব্লার (KL) ডাইভারজেন্স ও জেনসেনের অসমতা (Jensen's Inequality) প্রমাণ
   - ২.৩ ফরোয়ার্ড KL (Mode Covering) বনাম রিভার্স KL (Mode Seeking)
   - ২.৪ জেনসেন-শ্যানন (JS) ডাইভারজেন্স ও প্রতিসাম্যতা প্রমাণ
3. [ভ্যারিয়েশনাল ইনফারেন্স ও ELBO-এর সম্পূর্ণ গাণিতিক ডেরিভেশন](#৩-ভ্যারিয়েশনাল-ইনফারেন্স-ও-elbo)
   - ৩.১ ট্রু পোস্টেরিয়রের অননুমেয়তা (Intractable Posterior $P(z \mid x)$)
   - ৩.২ ভ্যারিয়েশনাল ডিস্ট্রিবিউশন $Q_\phi(z \mid x)$ প্রবর্তন
   - ৩.৩ চেইন রুল ও জেনসেনের অসমতা দিয়ে ELBO প্রমাণ (Step-by-Step Derivation)
   - ৩.৪ রিকনস্ট্রাকশন টার্ম বনাম রেগুলারাইজেশন টার্মের স্বজ্ঞাত অর্থ
4. [বাস্তব সংখ্যাভিত্তিক হাতে-কলমে হিসাব (Step-by-Step Calculations)](#৪-বাস্তব-সংখ্যাভিত্তিক-হাতে-কলমে-হিসাব)
   - ৪.১ দুটি ডিস্ট্রিবিউশনের KL ডাইভারজেন্স ক্যালকুলেশন
   - ৪.২ গাউসিয়ান ডিস্ট্রিবিউশনের মধ্যকার KL ক্লোজড-ফর্ম হিসাব
5. [পাইথন স্ক্র্যাচ ইমপ্লিমেন্টেশন ও ভেরিফিকেশন](#৫-পাইথন-স্ক্র্যাচ-ইমপ্লিমেন্টেশন-ও-ভেরিফিকেশন)

---

# ১. ডিসক্রিমিনেটিভ বনাম জেনারেটিভ মডেলিং

মেশিন লার্নিং জগতে মডেলিং মূলত দুটি মৌলিক ধারায় বিভক্ত:

```
    Discriminative Modeling (ক্লাসিফায়ার):
    Input Data X (ইমেজ) ────────► [ Decision Boundary ] ────────► Output Label Y (Cat or Dog)
    মডেল শেখে: P(Y | X)   ── "এই পিক্সেলগুলো দেখলে এটি কি বিড়াল নাকি কুকুর?"
    
    Generative Modeling (জেনারেটর):
    Prior Noise z ~ P(z) ───────► [ Generator Network ] ───────► Synthetic Data X_new (বাস্তবধর্মী বিড়াল)
    মডেল শেখে: P(X) বা P(X | Y) ── "একটি বাস্তবসম্মত বিড়ালের পিক্সেল বিন্যাস কেমন হতে পারে?"
```

---

### ১.১ গাণিতিক তুলনা ও সম্ভাবনা তত্ত্ব:
1. **ডিসক্রিমিনেটিভ মডেল (Discriminative):**
   সরাসরি কন্ডিশনাল প্রোবাবিলিটি $P(Y \mid X)$ অনুমান করে। মডেলের ইনপুট ডেটা $X$-এর অভ্যন্তরীণ ডিস্ট্রিবিউশন শেখার কোনো প্রয়োজন পড়ে না; সে শুধু দুটি বা ততোধিক ক্লাসের মাঝে সবচেয়ে দক্ষ ডিসিশন বাউন্ডারি (Hyperplane) টানে।
   - *উদাহরণ:* Logistic Regression, SVM, Random Forest Classifier, ResNet।
2. **জেনারেটিভ মডেল (Generative):**
   ইনপুট ডেটার আনকন্ডিশনাল ডিস্ট্রিবিউশন $P(X)$ অথবা জয়েন্ট প্রোবাবিলিটি ডিস্ট্রিবিউশন $P(X, Y) = P(X \mid Y)P(Y)$ শেখে। মডেল ইনপুট ডেটার সমস্ত বৈশিষ্ট্য, গঠন ও পারস্পরিক নির্ভরতার একটি পূর্ণাঙ্গ সম্ভাবনা মানচিত্র তৈরি করে।
   - *বেইস রুলের মাধ্যমে ক্লাসিফিকেশন:*
     $$P(Y \mid X) = \frac{P(X \mid Y) P(Y)}{P(X)} = \frac{P(X \mid Y) P(Y)}{\sum_{y'} P(X \mid y') P(y')}$$
   - *জেনারেটিভ সামর্থ্য:* একবার $P(X)$ শিখে ফেললে, সেই ডিস্ট্রিবিউশন থেকে নতুন স্যাম্পল $\mathbf{x}_{\text{new}} \sim P(X)$ ড্র করা যায়, যা পূর্বে কখনো অস্তিত্বে ছিল না!

---

### ১.২ হাই-ডাইমেনশনাল স্পেস ও ইনট্র্যাক্টেবল মার্জিনাল লাইকলিহুড
ধরি একটি সাধারণ $256 \times 256$ রেজোলিউশনের রঙিন ছবি। 
- মোট মাত্রার সংখ্যা (Dimensions): $D = 256 \times 256 \times 3 = \mathbf{196,608}$ টি পিক্সেল!
- প্রতিটি পিক্সেলের মান $0$ থেকে $255$ হতে পারে ($256$ টি স্তর)।
- মোট সম্ভাব্য ছবির বিন্যাস সংখ্যা: $256^{196,608} \approx 10^{473,000}$! (মহাবিশ্বের মোট পরমাণু সংখ্যার চেয়েও কোটি কোটি গুণ বড়!)
- এই বিপুল স্পেসের ৯৯.৯৯৯৯% ছবিই কেবল অর্থহীন নয়েজ (টিভি স্ক্রিনের সাদা-কালো ঝিরঝির)। বাস্তবসম্মত ছবিগুলো (মানুষ, বিড়াল, গাড়ি) এই বিশাল স্পেসের একটি অতি-ক্ষুদ্র নিম্ন-মাত্রিক সাবস্পেস বা **ম্যানিফোল্ডে (Low-dimensional Manifold)** অবস্থান করে।

জেনারেটিভ মডেলের কাজ হলো একটি নিম্ন-মাত্রিক ল্যাটেন্ট স্পেস $\mathbf{z} \in \mathbb{R}^d$ ($d \approx 128 - 1024$) থেকে হাই-ডাইমেনশনাল স্পেস $X$-এ ম্যাপিং শেখা:
$$P(X) = \int P(X \mid \mathbf{z}) P(\mathbf{z}) \, d\mathbf{z}$$
- **The Curse of Intractability:** এই ইন্টিগ্রালটি ক্লোজড-ফর্মে সমাধান করা গাণিতিকভাবে অসম্ভব (Intractable), কারণ সম্ভাব্য সকল $\mathbf{z}$-এর ওপর ইন্টিগ্রেশন করতে অসীম কম্পিউটেশন লাগে। এই সমস্যার সমাধান করতেই VAE, GAN এবং Diffusion মডেলের জন্ম!

---

# ২. ইনফরমেশন থিওরি ও প্রোবাবিলিটি মেট্রিক্স

জেনারেটিভ মডেল প্রশিক্ষণ দেওয়ার মূল ভিত্তি হলো দুটি সম্ভাবনা ডিস্ট্রিবিউশনের মধ্যে ব্যবধান পরিমাপ করা:
- প্রকৃত ডেটা ডিস্ট্রিবিউশন: $P_{\text{data}}(x)$
- মডেলের জেনারেট করা ডিস্ট্রিবিউশন: $P_\theta(x)$

```
P_data(x) [বাস্তব ডেটা]  ◄────── Distance Metric (KL / JS / Wasserstein) ──────►  P_theta(x) [মডেল প্রেডিকশন]
```

---

### ২.১ শ্যানন এন্ট্রপি (Shannon Entropy) ও ক্রস-এন্ট্রপি
1. **সারপ্রাইজাল (Surprise):** কোনো ঘটনা ঘটার সম্ভাবনা $p(x)$ হলে তার তথ্যের পরিমাণ বা বিস্ময়: $I(x) = -\log p(x)$। বিরল ঘটনার বিস্ময় সবচেয়ে বেশি।
2. **শ্যানন এন্ট্রপি ($H(P)$):** একটি ডিস্ট্রিবিউশনের সামগ্রিক অনিশ্চয়তা বা গড় তথ্য:
   $$H(P) = \mathbb{E}_{x \sim P}[-\log p(x)] = - \sum_{x} p(x) \log p(x)$$
3. **ক্রস-এন্ট্রপি ($H(P, Q)$):** প্রকৃত ডিস্ট্রিবিউশন $P$ কে ভুল ডিস্ট্রিবিউশন $Q$ দিয়ে এনকোড করতে প্রয়োজনীয় গড় বিট সংখ্যা:
   $$H(P, Q) = \mathbb{E}_{x \sim P}[-\log q(x)] = - \sum_{x} p(x) \log q(x)$$

---

### ২.২ কুলব্যাক-লেইব্লার (KL) ডাইভারজেন্স ও জেনসেনের অসমতা প্রমাণ

#### গাণিতিক সংজ্ঞা:
প্রকৃত ডিস্ট্রিবিউশন $P$ এবং অনুমিত ডিস্ট্রিবিউশন $Q$-এর মধ্যকার তথ্যের অপচয় বা অদক্ষতা হলো KL ডাইভারজেন্স:
$$D_{\text{KL}}(P \parallel Q) = H(P, Q) - H(P) = \sum_{x} p(x) \log \left( \frac{p(x)}{q(x)} \right)$$

#### 🔬 জেনসেনের অসমতা দিয়ে প্রমাণ যে $D_{\text{KL}}(P \parallel Q) \ge 0$:
**Jensen's Inequality:** যদি $f(t)$ একটি অবতল (Concave) ফাংশন হয় (যেমন $f(t) = \ln(t)$), তবে:
$$\mathbb{E}[f(t)] \le f(\mathbb{E}[t])$$

প্রমাণ:
$$- D_{\text{KL}}(P \parallel Q) = - \sum_{x} p(x) \ln\left( \frac{p(x)}{q(x)} \right) = \sum_{x} p(x) \ln\left( \frac{q(x)}{p(x)} \right)$$
এটি হলো $\frac{q(x)}{p(x)}$ এর ওপর লগারিদমের প্রত্যাশিত মান ($\mathbb{E}_{P}$)। জেনসেনের অসমতা প্রয়োগ করে:
$$\sum_{x} p(x) \ln\left( \frac{q(x)}{p(x)} \right) \le \ln\left( \sum_{x} p(x) \frac{q(x)}{p(x)} \right) = \ln\left( \sum_{x} q(x) \right)$$
যেহেতু $Q$ একটি বৈধ সম্ভাবনা ডিস্ট্রিবিউশন, তাই $\sum_x q(x) = 1$।
$$- D_{\text{KL}}(P \parallel Q) \le \ln(1) = 0$$
উভয় পক্ষে $-1$ দিয়ে গুণ করলে অসমতার দিক পরিবর্তিত হয়:
$$D_{\text{KL}}(P \parallel Q) \ge \mathbf{0} \quad \text{(Q.E.D.)}$$
- $D_{\text{KL}}(P \parallel Q) = 0$ হবে যদি এবং কেবল যদি সকল $x$-এর জন্য $p(x) = q(x)$ হয়।

---

### ২.৩ ফরোয়ার্ড KL বনাম রিভার্স KL: জেনারেটিভ মডেলের আচরণ
KL ডাইভারজেন্স প্রতিসম (Symmetric) নয়: $D_{\text{KL}}(P \parallel Q) \neq D_{\text{KL}}(Q \parallel P)$। এই অপ্রতিসাম্য জেনারেটিভ মডেলের জন্য বৈপ্লবিক ভূমিকা রাখে:

```
         Forward KL: D_KL(P || Q)                          Reverse KL: D_KL(Q || P)
      Zero-Avoiding / Mode Covering                     Zero-Forcing / Mode Seeking
      
            P(x) (Two Modes)                                  P(x) (Two Modes)
          ▲       ▲                                         ▲       ▲
         / \     / \                                       / \     / \
        /   \   /   \                                     /   \   /   \
       /     \ /     \                                   /     \ /     \
      ┌───────────────┐                                 ┌───────┐
      │ Q(x) covers   │                                 │ Q(x)  │ (Drops one mode completely,
      │ ALL modes     │                                 │ fits  │  focuses on one single mode)
      └───────────────┘                                 └───────┘
```

1. **Forward KL: $D_{\text{KL}}(P \parallel Q) = \int P(x) \log \frac{P(x)}{Q(x)} \, dx$ (Zero-Avoiding / Mode Covering):**
   - যেখানে $P(x) > 0$, সেখানে যদি $Q(x) \to 0$ হয়, তবে অনুপাত $\frac{P(x)}{Q(x)} \to \infty$ হয়ে পেনাল্টি অসীম হয়ে যায়।
   - ফলে $Q(x)$ বাধ্য হয় $P(x)$-এর প্রতিটি মোড কভার করতে। এর ফলে মডেল ব্লারি (ঝাপসা) কিন্তু সেফ ডেটা তৈরি করে (যেমন: VAE-এর আচরণ)।
2. **Reverse KL: $D_{\text{KL}}(Q \parallel P) = \int Q(x) \log \frac{Q(x)}{P(x)} \, dx$ (Zero-Forcing / Mode Seeking):**
   - যেখানে $P(x) = 0$, সেখানে $Q(x)$ কে অবশ্যই শূন্য হতে হবে, অন্যথায় পেনাল্টি অসীম হবে।
   - ফলে $Q(x)$ একাধিক মোডের মধ্যে কেবল একটি সেরা মোড বেছে নিয়ে তাতে ফোকাস করে এবং বাকি মোড ড্রপ করে দেয়। এর ফলে শার্প কিন্তু কম বৈচিত্র্যময় ছবি তৈরি হয় (যেমন: GAN-এর মোড কলাপ্স ঝুঁকি)।

---

### ২.৪ জেনসেন-শ্যানন (JS) ডাইভারজেন্স
যেহেতু KL ডাইভারজেন্স প্রতিসম নয় এবং অসীম ($\infty$) হতে পারে, তাই Goodfellow et al. (GAN-এর প্রতিষ্ঠাতা) **Jensen-Shannon Divergence** ব্যবহার করেন:
$$M = \frac{1}{2}(P + Q)$$
$$\text{JSD}(P \parallel Q) = \frac{1}{2} D_{\text{KL}}(P \parallel M) + \frac{1}{2} D_{\text{KL}}(Q \parallel M)$$
- **বৈশিষ্ট্যসমূহ:**
  1. এটি সম্পূর্ণ প্রতিসম: $\text{JSD}(P \parallel Q) = \text{JSD}(Q \parallel P)$।
  2. এর মান সর্বদা সীমাবদ্ধ (Bounded): $0 \le \text{JSD}(P \parallel Q) \le \ln(2) \approx \mathbf{0.693}$ (ন্যাচারাল লগে) অথবা $1.0$ (বেস-২ লগে)।
  3. $\sqrt{\text{JSD}}$ একটি সত্যিকারের গাণিতিক দূরত্ব মেট্রিক (Triangle Inequality মেনে চলে)।

---

# ৩. ভ্যারিয়েশনাল ইনফারেন্স ও ELBO-এর সম্পূর্ণ গাণিতিক ডেরিভেশন

জেনারেটিভ মডেলের চূড়ান্ত লক্ষ্য হলো ডেটার সত্য লাইকলিহুড $\log P_\theta(x)$ সর্বোচ্চ করা।
কিন্তু ল্যাটেন্ট স্পেস $z$-এর কারণে:
$$P_\theta(x) = \int P_\theta(x, z) \, dz = \int P_\theta(x \mid z) P(z) \, dz$$
এবং ট্রু পোস্টেরিয়র:
$$P_\theta(z \mid x) = \frac{P_\theta(x \mid z) P(z)}{P_\theta(x)}$$
যেহেতু হর $P_\theta(x)$ গণনা করা অসম্ভব, তাই পোস্টেরিয়র $P_\theta(z \mid x)$ বের করাও অসম্ভব!

---

### ৩.১ ভ্যারিয়েশনাল ইনফারেন্স সমাধান
আমরা সত্য কিন্তু অজানা পোস্টেরিয়র $P_\theta(z \mid x)$-কে একটি সহজ, ট্র্যাক্টেবল ডিস্ট্রিবিউশন $Q_\phi(z \mid x)$ (যেমন একটি নিউরাল নেটওয়ার্ক দ্বারা প্রেডিক্ট করা গাউসিয়ান $\mathcal{N}(\boldsymbol{\mu}, \boldsymbol{\sigma}^2)$) দিয়ে অনুমান করব।

আমাদের লক্ষ্য $Q_\phi(z \mid x)$ এবং $P_\theta(z \mid x)$-এর মধ্যকার KL ডাইভারজেন্স শূন্য করা:
$$D_{\text{KL}}\left( Q_\phi(z \mid x) \;\middle\|\; P_\theta(z \mid x) \right) = \mathbb{E}_{z \sim Q}\left[ \log \frac{Q_\phi(z \mid x)}{P_\theta(z \mid x)} \right]$$

---

### ৩.২ Evidence Lower Bound (ELBO) সম্পূর্ণ প্রমাণ (Step-by-Step)

বেইস রুল অনুসারে $P_\theta(z \mid x) = \frac{P_\theta(x, z)}{P_\theta(x)}$ বসিয়ে পাই:
$$D_{\text{KL}}\left( Q_\phi(z \mid x) \;\middle\|\; P_\theta(z \mid x) \right) = \mathbb{E}_{z \sim Q}\left[ \log \left( \frac{Q_\phi(z \mid x) P_\theta(x)}{P_\theta(x, z)} \right) \right]$$

লগের গুণ ও ভাগের নিয়ম অনুযায়ী ভেঙে পাই:
$$D_{\text{KL}}\left( Q_\phi(z \mid x) \;\middle\|\; P_\theta(z \mid x) \right) = \mathbb{E}_{z \sim Q}\left[ \log P_\theta(x) + \log \left( \frac{Q_\phi(z \mid x)}{P_\theta(x, z)} \right) \right]$$

যেহেতু $\log P_\theta(x)$ পদে $z$ ভ্যারিয়েবলটি নেই, তাই একে প্রত্যাশিত মানের ($\mathbb{E}_{z \sim Q}$) বাইরে আনা যায় ($\mathbb{E}_{z \sim Q}[\log P_\theta(x)] = \log P_\theta(x)$):
$$D_{\text{KL}}\left( Q_\phi(z \mid x) \;\middle\|\; P_\theta(z \mid x) \right) = \log P_\theta(x) + \mathbb{E}_{z \sim Q}\left[ \log \left( \frac{Q_\phi(z \mid x)}{P_\theta(x, z)} \right) \right]$$

পক্ষান্তর করে মূল ডেটা লাইকলিহুড $\log P_\theta(x)$-কে বামে রাখলে:
$$\log P_\theta(x) = \mathbb{E}_{z \sim Q}\left[ \log \left( \frac{P_\theta(x, z)}{Q_\phi(z \mid x)} \right) \right] + D_{\text{KL}}\left( Q_\phi(z \mid x) \;\middle\|\; P_\theta(z \mid x) \right)$$

যেহেতু আমরা জানি KL ডাইভারজেন্স সর্বদা অঋণাত্মক ($D_{\text{KL}} \ge 0$), তাই ডানদিকের প্রথম অংশটি অবশ্যই $\log P_\theta(x)$-এর একটি নিম্নসীমা (Lower Bound):
$$\log P_\theta(x) \ge \mathbb{E}_{z \sim Q}\left[ \log \left( \frac{P_\theta(x, z)}{Q_\phi(z \mid x)} \right) \right] \equiv \mathbf{ELBO}(\theta, \phi; x)$$

---

### ৩.৩ ELBO-এর চূড়ান্ত রূপ ও দুটি পদের অর্থ:
জয়েন্ট প্রোবাবিলিটি $P_\theta(x, z) = P_\theta(x \mid z) P(z)$ বসালে:
$$\text{ELBO} = \mathbb{E}_{z \sim Q}\left[ \log \frac{P_\theta(x \mid z) P(z)}{Q_\phi(z \mid x)} \right]$$
$$\text{ELBO} = \mathbb{E}_{z \sim Q}[\log P_\theta(x \mid z)] + \mathbb{E}_{z \sim Q}\left[ \log \frac{P(z)}{Q_\phi(z \mid x)} \right]$$
দ্বিতীয় পদের লগের ভেতর উল্টে দিলে ঋণাত্মক চিহ্ন চলে আসে, যা সরাসরি KL ডাইভারজেন্স:
$$\mathbf{ELBO}(\theta, \phi; x) = \underbrace{\mathbb{E}_{z \sim Q_\phi(z \mid x)}[\log P_\theta(x \mid z)]}_{\text{Term 1: Reconstruction Likelihood}} - \underbrace{D_{\text{KL}}\left( Q_\phi(z \mid x) \;\middle\|\; P(z) \right)}_{\text{Term 2: Latent Regularization}}$$

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│                             THE ELBO DECOMPOSITION                               │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 1. Reconstruction Term: E_{Q}[log P(x|z)]                                        │
│    👉 ডিকোডার ল্যাটেন্ট কোড z থেকে মূল ডেটা x কে কতটা নিখুঁতভাবে রিকনস্ট্রাক্ট  │
│       করতে পারে (MSE বা Binary Cross-Entropy লস)।                                 │
│                                                                                  │
│ 2. Regularization Term: D_KL(Q(z|x) || P(z))                                     │
│    👉 এনকোডারের তৈরি ল্যাটেন্ট ডিস্ট্রিবিউশন Q(z|x) যেন আদর্শ প্রায়র P(z)        │
│       (স্ট্যান্ডার্ড নরমাল গাউসিয়ান N(0, I)) থেকে বেশি দূরে সরে না যায়।           │
│       এটি ল্যাটেন্ট স্পেসকে মসৃণ ও কন্টিনিউয়াস রাখে।                            │
└──────────────────────────────────────────────────────────────────────────────────┘
```

---

# ৪. বাস্তব সংখ্যাভিত্তিক হাতে-কলমে হিসাব

### ৪.১ দুটি ডিসক্রিট ডিস্ট্রিবিউশনের KL ডাইভারজেন্স গণনা:
ধরি একটি কয়েন টসের ৩টি আউটকাম ($A, B, C$):
- প্রকৃত ডিস্ট্রিবিউশন $P = [0.50, 0.25, 0.25]$
- অনুমিত মডেল ডিস্ট্রিবিউশন $Q = [0.333, 0.333, 0.333]$ (ইউনিফর্ম)

$$D_{\text{KL}}(P \parallel Q) = \sum_{i=1}^3 P(i) \log_2\left( \frac{P(i)}{Q(i)} \right)$$
1. আউটকাম $A$: $0.50 \times \log_2(0.50 / 0.333) = 0.50 \times \log_2(1.50) = 0.50 \times 0.585 = \mathbf{0.2925}$
2. আউটকাম $B$: $0.25 \times \log_2(0.25 / 0.333) = 0.25 \times \log_2(0.75) = 0.25 \times (-0.415) = \mathbf{-0.1038}$
3. আউটকাম $C$: $0.25 \times \log_2(0.25 / 0.333) = 0.25 \times (-0.415) = \mathbf{-0.1038}$

**মোট KL ডাইভারজেন্স:**
$$D_{\text{KL}}(P \parallel Q) = 0.2925 - 0.1038 - 0.1038 = \mathbf{0.0849\text{ bits}}$$
- ফলাফল ধনাত্মক ($>0$), যা তথ্যের অদক্ষতা প্রকাশ করে।

---

### ৪.২ গাউসিয়ান ডিস্ট্রিবিউশনের ক্লোজড-ফর্ম KL ডাইভারজেন্স হিসাব
ভ্যারিয়েশনাল অটোএনকোডারে $Q(z \mid x) = \mathcal{N}(\mu, \sigma^2)$ এবং প্রায়র $P(z) = \mathcal{N}(0, 1)$ হলে তাদের KL ডাইভারজেন্সের ক্লোজড-ফর্ম সূত্র:
$$D_{\text{KL}}(\mathcal{N}(\mu, \sigma^2) \parallel \mathcal{N}(0, 1)) = - \frac{1}{2} \sum_{j=1}^J \left( 1 + \ln(\sigma_j^2) - \mu_j^2 - \sigma_j^2 \right)$$

#### 🔢 ১-মাত্রিক ল্যাটেন্ট স্পেসে বাস্তব সংখ্যা হিসাব:
ধরি এনকোডার প্রেডিক্ট করেছে: গড় $\mu = 1.5$, এবং ভ্যারিয়্যান্স $\sigma^2 = 0.25$ ($\implies \ln(\sigma^2) = \ln(0.25) \approx -1.3863$)।

সূত্রে মান বসিয়ে পাই:
$$D_{\text{KL}} = - \frac{1}{2} \left[ 1 + (-1.3863) - (1.5)^2 - 0.25 \right]$$
$$D_{\text{KL}} = - \frac{1}{2} \left[ 1 - 1.3863 - 2.25 - 0.25 \right]$$
$$D_{\text{KL}} = - \frac{1}{2} \left[ 1 - 3.8863 \right] = - \frac{1}{2} [-2.8863] = \mathbf{1.44315}$$
- *অন্তর্দৃষ্টি:* এনকোডার গাউসিয়ানটি স্ট্যান্ডার্ড $\mathcal{N}(0, 1)$ থেকে যত দূরে সরে যাবে বা যত সংকুচিত হবে, এই পেনাল্টি তত বাড়বে এবং এনকোডারকে পুনরায় সেন্টারে টেনে আনবে।

---

# ৫. পাইথন স্ক্র্যাচ ইমপ্লিমেন্টেশন ও ভেরিফিকেশন

```python
"""
math_foundations_genai.py
Pure Python & NumPy verification of KL Divergence, JS Divergence & Gaussian ELBO Penalty
"""
import numpy as np

def kl_divergence(p: np.ndarray, q: np.ndarray, eps: float = 1e-12) -> float:
    """ডিসক্রিট ডিস্ট্রিবিউশনের KL ডাইভারজেন্স গণনা D_KL(P || Q)"""
    p = np.clip(p, eps, 1.0)
    q = np.clip(q, eps, 1.0)
    return float(np.sum(p * np.log(p / q)))

def js_divergence(p: np.ndarray, q: np.ndarray) -> float:
    """প্রতিসম ও সীমাবদ্ধ Jensen-Shannon Divergence"""
    m = 0.5 * (p + q)
    return 0.5 * kl_divergence(p, m) + 0.5 * kl_divergence(q, m)

def gaussian_kl_loss(mu: np.ndarray, log_var: np.ndarray) -> float:
    """VAE গাউসিয়ান KL ডাইভারজেন্স পেনাল্টি: D_KL(N(mu, sigma^2) || N(0, I))"""
    # log_var = ln(sigma^2)
    return float(-0.5 * np.sum(1 + log_var - np.square(mu) - np.exp(log_var)))

if __name__ == "__main__":
    # ১. ডিসক্রিট KL ও JS পরীক্ষা
    p_dist = np.array([0.50, 0.25, 0.25])
    q_dist = np.array([0.333333, 0.333333, 0.333334])

    kl_val = kl_divergence(p_dist, q_dist)
    js_val = js_divergence(p_dist, q_dist)

    print("📊 প্রোবাবিলিটি ডাইভারজেন্স অডিট:")
    print(f"  • Forward KL Divergence D_KL(P || Q): {kl_val:.5f} nats ({kl_val / np.log(2):.4f} bits)")
    print(f"  • Jensen-Shannon Divergence JSD(P || Q): {js_val:.5f} (সর্বদা <= ln(2) = 0.693)")

    # ২. গাউসিয়ান KL পেনাল্টি পরীক্ষা (mu = 1.5, sigma^2 = 0.25)
    mu_test = np.array([1.5])
    log_var_test = np.array([np.log(0.25)])
    kl_gaussian = gaussian_kl_loss(mu_test, log_var_test)

    print("\n🔬 VAE গাউসিয়ান রেগুলারাইজেশন অডিট:")
    print(f"  • Gaussian KL Penalty: {kl_gaussian:.5f}")
    assert np.isclose(kl_gaussian, 1.44315, atol=1e-4), "গণনায় ত্রুটি!"
    print("  ✅ গাউসিয়ান ডেরিভেশন সফলভাবে প্রমাণিত ও ভেরিফাইড!")
```

---

## 🎯 সংক্ষেপ ও সারসংক্ষেপ
- **জেনারেটিভ লক্ষ্য:** $P(Y|X)$ প্রেডিক্ট করা নয়; ডেটার হাই-ডাইমেনশনাল ম্যানিফোল্ড $P(X)$ শিখে সেখান থেকে নতুন স্যাম্পল তৈরি করা।
- **ইনট্র্যাক্টেবিলিটি:** মার্জিনাল লাইকলিহুড $P(X) = \int P(X|z)P(z)dz$ সরাসরি সমাধান করা অসম্ভব বলেই আধুনিক আর্কিটেকচারগুলোর প্রয়োজন।
- **KL ডাইভারজেন্স:** জেনসেনের অসমতা দিয়ে প্রমাণিত যে $D_{\text{KL}} \ge 0$। ফরোয়ার্ড KL মোড কভার করে ব্লারি ছবি দেয়, রিভার্স KL মোড সিক করে শার্প ছবি তৈরি করে।
- **ELBO:** ডেটার সত্য লাইকলিহুডের নিম্নসীমা, যা রিকনস্ট্রাকশন এরর এবং ল্যাটেন্ট গাউসিয়ান রেগুলারাইজেশনের যোগফল।

---
*পরবর্তী অধ্যায়:* [👉 অধ্যায় ২: ভ্যারিয়েশনাল অটোএনকোডার (VAE) ও ল্যাটেন্ট স্পেস](file:///g:/MNC/Generative_AI/02_variational_autoencoders_vae.md) | [📚 মাস্টার কারিকুলাম সূচিপত্র](file:///g:/MNC/Generative_AI/README.md)
