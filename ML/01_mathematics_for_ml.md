# অধ্যায় ১: মেশিন লার্নিংয়ের জন্য অপরিহার্য গণিত (Mathematics for Machine Learning Masterclass)

> *"Mathematics is not about numbers, equations, computations, or algorithms: it is about understanding." — William Paul Thurston*
>
> *"Without mathematics, machine learning is just trial-and-error magic. With mathematics, machine learning becomes a rigorous science, an engineering discipline, and a predictable technological marvel."*

এই হ্যান্ডবুকটি এমনভাবে রচিত হয়েছে যাতে একজন প্রফেশনাল মেশিন লার্নিং ইঞ্জিনিয়ারের প্রয়োজনীয় ফলিত গণিতের চারটি প্রধান স্তম্ভ—**রৈখিক বীজগণিত (Linear Algebra)**, **বহুচলক ক্যালকুলাস ও অপটিমাইজেশন (Multivariate Calculus & Optimization)**, **সম্ভাবনা ও পরিসংখ্যান (Probability & Mathematical Statistics)**, এবং **তথ্য তত্ত্ব (Information Theory)**—প্রতিটি বিষয়ের বিস্তারিত উপপাদ্যের প্রতিপাদন, জ্যামিতিক ভিজ্যুয়ালাইজেশন এবং **হাতে-কলমে সংখ্যাভিত্তিক প্রতিটি ধাপের হিসাব (Step-by-Step Numerical Calculations)** সহ নিখুঁতভাবে বোঝা যায়।

---

## 📑 পূর্ণাঙ্গ সূচিপত্র (Table of Contents)

1. [স্তম্ভ ১: রৈখিক বীজগণিত (Linear Algebra for ML)](#১-স্তম্ভ-১-রৈখিক-বীজগণিত)
   - ১.১ স্কেলার, ভেক্টর, ম্যাট্রিক্স ও টেনসর হায়ারার্কি
   - ১.২ ডট প্রোডাক্ট, ইনার প্রোডাক্ট ও কোসাইন সাদৃশ্য (হাতে-কলমে সংখ্যাসহ গণনা)
   - ১.৩ ভেক্টর নর্ম ($L_0, L_1, L_2, L_\infty$) ও ফ্রবেনিয়াস নর্ম (ধাপভিত্তিক উদাহরণ)
   - ১.৪ দূরত্ব মেট্রিক্স: ইউক্লিডিয়ান, ম্যানহাটন ও মাহালানোবিস দূরত্ব (বাস্তব হিসাব)
   - ১.৫ রৈখিক সমাবেশ (Linear Combination), স্প্যান, বেসিস ও ডাইমেনশন
   - ১.৬ রৈখিক স্বাধীনতা, ম্যাট্রিক্স র্যাঙ্ক ও Rank-Nullity উপপাদ্য
   - ১.৭ রৈখিক রূপান্তর (Linear Transformation), ডিটারমিন্যান্ট ও ক্ষেত্রফল বিকৃতি
   - ১.৮ অর্থোগোনালিটি ও অর্থোগোনাল প্রজেকশন: হ্যাট ম্যাট্রিক্স ($H$) সম্পূর্ণ ম্যাট্রিক্স সমাধান
   - ১.৯ আইগেনভ্যালু, আইগেনভেক্টর ও ডায়াগোনালাইজেশন (সম্পূর্ণ বীজগণিতীয় হিসাব)
   - ১.১০ সিঙ্গুলার ভ্যালু ডিকম্পোজিশন (SVD): হাতে-কলমে $U, \Sigma, V^T$ নির্ণয়
   - ১.১১ প্রিন্সিপাল কম্পোনেন্ট অ্যানালাইসিস (PCA) ল্যাগ্রাঞ্জিয়ান প্রতিপাদন
2. [স্তম্ভ ২: বহুচলক ক্যালকুলাস ও অপটিমাইজেশন (Calculus & Optimization)](#২-স্তম্ভ-২-বহুচলক-ক্যালকুলাস-ও-অপটিমাইজেশন)
   - ২.১ ফাংশন, লিমিট, কন্টিনিউটি ও ডিফারেন্সিয়েবিলিটি
   - ২.২ আংশিক অন্তরীকরণ, ডিরেকশনাল ডেরিভেটিভ ও গ্রেডিয়েন্ট ভেক্টর (সংখ্যাসহ প্রমাণ)
   - ২.৩ চেইন রুল ও জ্যাকোবিয়ান ম্যাট্রিক্স ($J \in \mathbb{R}^{m \times n}$)
   - ২.৪ হেসিয়ান ম্যাট্রিক্স ($H$), টেলর সিরিজ ও স্যাডল পয়েন্ট পরীক্ষা (হাতে-কলমে হিসাব)
   - ২.৫ কনভেক্সিটি (Convexity), জেনসেনের অসমতা ও পজিটিভ সেমি-ডেফিনিট হেসিয়ান
   - ২.৬ গ্রেডিয়েন্ট ডিসেন্ট ডাইনামিক্স: লিপসিৎস বাউন্ড ও ৩-ধাপের সংখ্যাভিত্তিক ট্রেসিং
3. [স্তম্ভ ৩: সম্ভাবনা ও গাণিতিক পরিসংখ্যান (Probability & Statistics)](#৩-স্তম্ভ-৩-সম্ভাবনা-ও-গাণিতিক-পরিসংখ্যান)
   - ৩.১ পপুলেশন বনাম স্যাম্পল ও কেন্দ্রীয় প্রবণতা (Mean, Median, Mode)
   - ৩.২ বেসেল কারেকশন ($m-1$): কেন $m$ নয়? (ক্ষুদ্র পপুলেশন দিয়ে নিখুঁত সংখ্যাভিত্তিক প্রমাণ)
   - ৩.৩ কোলমোগোরোভের স্বতঃসিদ্ধ ও বেইজের উপপাদ্য (মেডিকেল ডায়াগনস্টিক সমাধান)
   - ৩.৪ দৈব চলক ও প্রধান ডিস্ট্রিবিউশনসমূহ (Bernoulli, Binomial, Poisson, Gaussian)
   - ৩.৫ ল অফ লার্জ নাম্বার্স (LLN) ও সেন্ট্রাল লিমিট থিওরেম (CLT) গভীর ব্যাখ্যা
   - ৩.৬ হাইপোথিসিস টেস্টিং, $p$-value, Type I/II এরর ও কনফিডেন্স ইন্টারভাল
   - ৩.৭ ম্যাক্সিমাম লাইকলিহুড এস্টিমেশন (MLE): ৩টি কয়েন ফ্লিপ থেকে সম্পূর্ণ প্রতিপাদন
   - ৩.৮ ম্যাক্সিমাম এ পোস্টেরিওরি (MAP) ও রেগুলারাইজেশনের সংযোগ (Ridge/Lasso)
4. [স্তম্ভ ৪: তথ্য তত্ত্ব (Information Theory for ML)](#৪-স্তম্ভ-৪-তথ্য-তত্ত্ব)
   - ৪.১ সেলফ-ইনফরমেশন (Surprise) ও শ্যানন এন্ট্রপি (৮টি বলের সংখ্যাভিত্তিক হিসাব)
   - ৪.২ ক্রস-এন্ট্রপি (Cross-Entropy): মডেল প্রেডিকশনের ক্ষতি পরিমাপ
   - ৪.৩ কুলব্যাক-লাইবলার ডাইভারজেন্স ($D_{KL}(P || Q)$): ধাপভিত্তিক হিসাব
   - ৪.৪ ডিসিশন ট্রির ইনফরমেশন গেইন ও জিনি ইম্পিউরিটি (হাতে-কলমে স্প্লিট মান)
5. [হাতে-কলমে ভেরিফিকেশন টাস্ক ও স্ক্র্যাচ পাইথন কোড](#৫-হাতে-কলমে-ভেরিফিকেশন-টাস্ক-ও-স্ক্র্যাচ-পাইথন-কোড)
   - টাস্ক ১: পিওর নামপাই দিয়ে ভেক্টর অপারেশন ও ম্যাট্রিক্স মাল্টিপ্লিকেশন
   - টাস্ক ২: নরমাল সমীকরণ বনাম গ্রেডিয়েন্ট ডিসেন্ট লিনিয়ার রিগ্রেশন সিমুলেশন
   - টাস্ক ৩: ডিস্ট্রিবিউশন সিমুলেশন ও তাত্ত্বিক বনাম পরীক্ষামূলক পরিসংখ্যান
   - টাস্ক ৪: ক্লাসিফিকেশন ডেটাসেটের জন্য স্ক্র্যাচ এন্ট্রপি ও ইনফরমেশন গেইন গণনা
   - রোডম্যাপ পাস কন্ডিশন মূল্যায়ন ও ব্যাখ্যা

---

# ১. স্তম্ভ ১: রৈখিক বীজগণিত

মেশিন লার্নিংয়ের সমস্ত ডেটা, প্যারামিটার, ফিচার স্পেস এবং ডিপ লার্নিংয়ের নিউরনের ইন্টারঅ্যাকশন রৈখিক বীজগণিতের মাধ্যমে সংজ্ঞায়িত ও পরিচালিত হয়।

```
Scalar (0D)           Vector (1D)             Matrix (2D)                 Tensor (3D+)
  [ 42 ]                 [ x1 ]             [ a11 a12 a13 ]            ┌───────────────┐
                         [ x2 ]             [ a21 a22 a23 ]           ┌┘ a11 a12 a13 ┌┘│
                         [ x3 ]             [ a31 a32 a33 ]           │  a21 a22 a23 │ │
                                                                      │  a31 a32 a33 │ │
                                                                      └──────────────┘─┘
                                                                    (Image Batch: B x C x H x W)
```

---

### ১.১ স্কেলার, ভেক্টর, ম্যাট্রিক্স ও টেনসর হায়ারার্কি
- **স্কেলার ($s \in \mathbb{R}$):** একটি একক সংখ্যা যার কেবল মান আছে। যেমন: লার্নিং রেট $\alpha = 0.01$।
- **ভেক্টর ($\mathbf{x} \in \mathbb{R}^n$):** সংখ্যার ১-মাত্রিক বিন্যাস। একটি ডেটা পয়েন্টের সকল বৈশিষ্ট্য প্রকাশ করে:
  $$\mathbf{x} = \begin{bmatrix} x_1 & x_2 & \dots & x_n \end{bmatrix}^T$$
- **ম্যাট্রিক্স ($A \in \mathbb{R}^{m \times n}$):** সংখ্যার ২-মাত্রিক গ্রিড। $m$ সংখ্যক পর্যবেক্ষণ (সারি) এবং $n$ সংখ্যক ফিচারের (কলাম) সম্পূর্ণ ডেটাসেট।
- **টেনসর ($T \in \mathbb{R}^{d_1 \times \dots \times d_k}$):** ৩ বা ততোধিক মাত্রার বহুমাত্রিক অ্যারে। যেমন ভিডিও ডেটা: $(\text{Batch}, \text{Frames}, \text{Channels}, \text{Height}, \text{Width})$।

---

### ১.২ ডট প্রোডাক্ট, ইনার প্রোডাক্ট ও কোসাইন সাদৃশ্য

দুটি ভেক্টর $\mathbf{u}, \mathbf{v} \in \mathbb{R}^n$-এর ডট প্রোডাক্ট:
$$\mathbf{u} \cdot \mathbf{v} = \mathbf{u}^T \mathbf{v} = \sum_{i=1}^n u_i v_i$$
জ্যামিতিক রূপ:
$$\mathbf{u} \cdot \mathbf{v} = \|\mathbf{u}\|_2 \|\mathbf{v}\|_2 \cos(\theta) \implies \cos(\theta) = \frac{\mathbf{u}^T \mathbf{v}}{\|\mathbf{u}\|_2 \|\mathbf{v}\|_2}$$

#### 🔢 হাতে-কলমে উদাহরণ ও প্রতিটি ধাপের ব্যাখ্যা (Step-by-Step Calculation):
ধরি দুটি শব্দ এম্বেডিং ভেক্টর:
$\mathbf{u} = \begin{bmatrix} 3 \\ 4 \end{bmatrix}$ (যেমন: "Machine") এবং $\mathbf{v} = \begin{bmatrix} 4 \\ 3 \end{bmatrix}$ (যেমন: "Learning")

- **ধাপ ১: ডট প্রোডাক্ট গণনা:**
  $$\mathbf{u} \cdot \mathbf{v} = (3 \times 4) + (4 \times 3) = 12 + 12 = 24$$
- **ধাপ ২: প্রতিটি ভেক্টরের $L_2$ দৈর্ঘ্য (Norm) নির্ণয়:**
  $$\|\mathbf{u}\|_2 = \sqrt{3^2 + 4^2} = \sqrt{9 + 16} = \sqrt{25} = 5$$
  $$\|\mathbf{v}\|_2 = \sqrt{4^2 + 3^2} = \sqrt{16 + 9} = \sqrt{25} = 5$$
- **ধাপ ৩: কোসাইন সাদৃশ্য ও মধ্যবর্তী কোণ নির্ধারণ:**
  $$\cos(\theta) = \frac{24}{5 \times 5} = \frac{24}{25} = 0.96$$
  $$\theta = \arccos(0.96) \approx 16.26^\circ$$
- **ফলাফল ব্যাখ্যা:** কোসাইন সাদৃশ্য ১-এর অত্যন্ত কাছাকাছি ($0.96$) এবং কোণ মাত্র $16.26^\circ$, যা প্রমাণ করে ভেক্টর দুটি শব্দার্থগতভাবে অত্যন্ত সাদৃশ্যপূর্ণ!

---

### ১.৩ ভেক্টর নর্ম ($L_0, L_1, L_2, L_\infty$) ও ফ্রবেনিয়াস নর্ম

ভেক্টরের দৈর্ঘ্য বা আকারের গাণিতিক পরিমাপ ($L_p$ নর্ম):
$$\|\mathbf{x}\|_p = \left( \sum_{i=1}^n |x_i|^p \right)^{1/p}$$

```
          L1 Norm (Manhattan)                  L2 Norm (Euclidean)
               ^ x2                                 ^ x2
               |                                    |
              /\                                 .-----.
             /  \                              .'       '.
         ---|----+---|---> x1              ---|----+---|---> x1
             \  /                              '.       .'
              \/                                 '-----'
      (Corners hit axes: Sparsity)              (Smooth circle: Shrinkage)
```

#### 🔢 হাতে-কলমে সংখ্যাসহ উদাহরণ:
ধরি একটি মডেলের ওয়েট ভেক্টর: $\mathbf{w} = \begin{bmatrix} 3 & -4 & 0 & 12 \end{bmatrix}^T$
1. **$L_0$ নর্ম (অশূন্য উপাদানের সংখ্যা):**
   $$\|\mathbf{w}\|_0 = \mathbb{I}(3 \neq 0) + \mathbb{I}(-4 \neq 0) + \mathbb{I}(0 \neq 0) + \mathbb{I}(12 \neq 0) = 1 + 1 + 0 + 1 = 3$$
2. **$L_1$ নর্ম (Manhattan Norm - ল্যাসো পেনাল্টি):**
   $$\|\mathbf{w}\|_1 = |3| + |-4| + |0| + |12| = 3 + 4 + 0 + 12 = 19$$
3. **$L_2$ নর্ম (Euclidean Norm - রিজ পেনাল্টি):**
   $$\|\mathbf{w}\|_2 = \sqrt{3^2 + (-4)^2 + 0^2 + 12^2} = \sqrt{9 + 16 + 0 + 144} = \sqrt{169} = 13$$
4. **$L_\infty$ নর্ম (Max Norm):**
   $$\|\mathbf{w}\|_\infty = \max(|3|, |-4|, |0|, |12|) = 12$$
5. **ম্যাট্রিক্স ফ্রবেনিয়াস নর্ম (Frobenius Norm):**
   ধরি ম্যাট্রিক্স $A = \begin{bmatrix} 1 & 2 \\ 3 & 4 \end{bmatrix}$
   $$\|A\|_F = \sqrt{1^2 + 2^2 + 3^2 + 4^2} = \sqrt{1 + 4 + 9 + 16} = \sqrt{30} \approx 5.477$$

---

### ১.৪ দূরত্ব মেট্রিক্স: ইউক্লিডিয়ান, ম্যানহাটন ও মাহালানোবিস দূরত্ব

1. **Euclidean Distance:** $d_E(\mathbf{x}, \mathbf{y}) = \sqrt{\sum (x_i - y_i)^2}$
2. **Manhattan Distance:** $d_M(\mathbf{x}, \mathbf{y}) = \sum |x_i - y_i|$
3. **মাহালানোবিস দূরত্ব (Mahalanobis Distance):**
   $$D_{\text{Maha}}(\mathbf{x}, \mathbf{y}) = \sqrt{(\mathbf{x} - \mathbf{y})^T \Sigma^{-1} (\mathbf{x} - \mathbf{y})}$$

#### 🔢 কেন মাহালানোবিস দূরত্ব সাধারণ দূরত্বের চেয়ে শ্রেষ্ঠ? (হাতে-কলমে কেস)
ধরি ডেটা কোভ্যারিয়্যান্স ম্যাট্রিক্স $\Sigma = \begin{bmatrix} 4 & 0 \\ 0 & 1 \end{bmatrix}$ (অর্থাৎ $x_1$ ফিচারের ভ্যারিয়্যান্স ৪, কিন্তু $x_2$ ফিচারের ভ্যারিয়্যান্স মাত্র ১)।
অরিজিন $\mathbf{0} = [0, 0]^T$ থেকে দুটি ভিন্ন পয়েন্টের দূরত্ব তুলনা করি:
- পয়েন্ট $A = [2, 0]^T$
- পয়েন্ট $B = [0, 2]^T$

**ইউক্লিডিয়ান দূরত্ব গণনা:**
$$d_E(\mathbf{0}, A) = \sqrt{(2-0)^2 + 0^2} = 2$$
$$d_E(\mathbf{0}, B) = \sqrt{0^2 + (2-0)^2} = 2$$
*(ইউক্লিডিয়ান দূরত্ব বলে দুটি বিন্দুর দূরত্ব হুবহু সমান!)*

**মাহালানোবিস দূরত্ব গণনা:**
$\Sigma^{-1} = \begin{bmatrix} 1/4 & 0 \\ 0 & 1 \end{bmatrix}$
- বিন্দু $A$-এর দূরত্ব:
  $$D_M(\mathbf{0}, A) = \sqrt{\begin{bmatrix} 2 & 0 \end{bmatrix} \begin{bmatrix} 0.25 & 0 \\ 0 & 1 \end{bmatrix} \begin{bmatrix} 2 \\ 0 \end{bmatrix}} = \sqrt{\begin{bmatrix} 0.5 & 0 \end{bmatrix} \begin{bmatrix} 2 \\ 0 \end{bmatrix}} = \sqrt{1.0} = 1.0$$
- বিন্দু $B$-এর দূরত্ব:
  $$D_M(\mathbf{0}, B) = \sqrt{\begin{bmatrix} 0 & 2 \end{bmatrix} \begin{bmatrix} 0.25 & 0 \\ 0 & 1 \end{bmatrix} \begin{bmatrix} 0 \\ 2 \end{bmatrix}} = \sqrt{\begin{bmatrix} 0 & 2 \end{bmatrix} \begin{bmatrix} 0 \\ 2 \end{bmatrix}} = \sqrt{4.0} = 2.0$$
- **ব্যাখ্যা:** $x_1$ অক্ষে ভ্যারিয়্যান্স বেশি থাকায় সেখানে ২ একক যাওয়া কোনো অস্বাভাবিক ঘটনা নয় ($D_M = 1.0$); কিন্তু $x_2$ অক্ষে ভ্যারিয়্যান্স কম থাকায় সেখানে ২ একক যাওয়া চরম অস্বাভাবিক আউটলায়ার ($D_M = 2.0$)! মাহালানোবিস দূরত্ব ডেটার এই সত্যকে নিখুঁতভাবে প্রকাশ করে।

---

### ১.৫ রৈখিক সমাবেশ, স্প্যান, বেসিস ও ডাইমেনশন

- **রৈখিক সমাবেশ:** $\mathbf{y} = c_1 \mathbf{v}_1 + c_2 \mathbf{v}_2 + \dots + c_k \mathbf{v}_k$
- **বেসিস (Basis):** একটি সাবস্পেসের এমন ক্ষুদ্রতম ভেক্টর সেট যা পরস্পর স্বাধীন এবং পুরো সাবস্পেস স্প্যান করতে পারে।
- **ডাইমেনশন:** বেসিসে থাকা ভেক্টরের মোট সংখ্যা।

---

### ১.৬ রৈখিক স্বাধীনতা, ম্যাট্রিক্স র্যাঙ্ক ও Rank-Nullity উপপাদ্য

ভেক্টর সেট রৈখিকভাবে স্বাধীন হবে যদি $c_1 \mathbf{v}_1 + \dots + c_k \mathbf{v}_k = \mathbf{0} \implies c_1 = \dots = c_k = 0$ হয়।

#### 🔢 হাতে-কলমে রৈখিক স্বাধীনতা ও র্যাঙ্ক পরীক্ষা:
ধরি তিনটি ভেক্টর:
$\mathbf{v}_1 = \begin{bmatrix} 1 \\ 0 \\ 1 \end{bmatrix}, \quad \mathbf{v}_2 = \begin{bmatrix} 1 \\ 1 \\ 0 \end{bmatrix}, \quad \mathbf{v}_3 = \begin{bmatrix} 2 \\ 1 \\ 1 \end{bmatrix}$
লক্ষ্য করুন: $\mathbf{v}_3 = \mathbf{v}_1 + \mathbf{v}_2$!
ম্যাট্রিক্স আকারে লিখে ডিটারমিন্যান্ট বের করি:
$$A = \begin{bmatrix} 1 & 1 & 2 \\ 0 & 1 & 1 \\ 1 & 0 & 1 \end{bmatrix}$$
$$\det(A) = 1(1 - 0) - 1(0 - 1) + 2(0 - 1) = 1(1) - 1(-1) + 2(-1) = 1 + 1 - 2 = 0$$
- যেহেতু নির্ণায়ক शून्य ($\det=0$), তাই ভেক্টরগুলো পরস্পর **রৈখিকভাবে নির্ভরশীল (Linearly Dependent)**।
- ম্যাট্রিক্সটির র্যাঙ্ক $\text{Rank}(A) = 2$ (৩ নয়)।
- **Rank-Nullity উপপাদ্য:** $\text{Rank}(A) + \text{Nullity}(A) = 3 \implies 2 + 1 = 3$। অর্থাৎ এর নাল-স্পেসের ডাইমেনশন ১!

---

### ১.৭ রৈখিক রূপান্তর, ডিটারমিন্যান্ট ও ক্ষেত্রফল বিকৃতি

একটি ২x২ ম্যাট্রিক্স $A = \begin{bmatrix} a & b \\ c & d \end{bmatrix}$二维 কার্তেসীয় তলের বেসিস ভেক্টর $\mathbf{i} = [1, 0]^T$ এবং $\mathbf{j} = [0, 1]^T$-কে নতুন অবস্থানে রূপান্তর করে:
$$A \begin{bmatrix} 1 \\ 0 \end{bmatrix} = \begin{bmatrix} a \\ c \end{bmatrix}, \quad A \begin{bmatrix} 0 \\ 1 \end{bmatrix} = \begin{bmatrix} b \\ d \end{bmatrix}$$

#### 🔢 ক্ষেত্রফল বিকৃতি হাতে-কলমে গণনা:
ধরি রূপান্তর ম্যাট্রিক্স $A = \begin{bmatrix} 3 & 1 \\ 0 & 2 \end{bmatrix}$
- ডিটারমিন্যান্ট গণনা:
  $$\det(A) = (3 \times 2) - (1 \times 0) = 6 - 0 = 6$$
- **জ্যামিতিক তাৎপর্য:** মূল স্পেসে ১ একক ক্ষেত্রফলবিশিষ্ট একটি একক বর্গক্ষেত্র (Unit Square: $[0,1] \times [0,1]$) রূপান্তরের পর একটি সামান্তরিকে পরিণত হয় যার নতুন ক্ষেত্রফল হবে ঠিক **৬ বর্গ একক**!

---

### ১.৮ অর্থোগোনালিটি ও অর্থোগোনাল প্রজেকশন: হ্যাট ম্যাট্রিক্স ($H$) সম্পূর্ণ সমাধান

```
               y
               ^
               | \
               |   \  e = y - y_hat (Orthogonal error vector)
               |     \
               +───────> y_hat = X * theta = H * y
               Subspace Col(X)
```

#### 🔢 হাতে-কলমে হ্যাট ম্যাট্রিক্স ($H$) নির্ণয় ও প্রেডিকশন যাচাই:
ধরি ২টি মাত্র পর্যবেক্ষণ এবং ১টি ফিচারবিশিষ্ট ডিজাইন ম্যাট্রিক্স:
$X = \begin{bmatrix} 1 & 1 \\ 1 & 2 \end{bmatrix}$ (প্রথম কলাম বায়াস), এবং টার্গেট $\mathbf{y} = \begin{bmatrix} 2 \\ 3 \end{bmatrix}$

- **ধাপ ১: $X^T X$ গণনা:**
  $$X^T X = \begin{bmatrix} 1 & 1 \\ 1 & 2 \end{bmatrix} \begin{bmatrix} 1 & 1 \\ 1 & 2 \end{bmatrix} = \begin{bmatrix} 1+1 & 1+2 \\ 1+2 & 1+4 \end{bmatrix} = \begin{bmatrix} 2 & 3 \\ 3 & 5 \end{bmatrix}$$
- **ধাপ ২: $(X^T X)^{-1}$ ইনভার্স নির্ণয়:**
  $$\det(X^T X) = (2)(5) - (3)(3) = 10 - 9 = 1$$
  $$(X^T X)^{-1} = \frac{1}{1} \begin{bmatrix} 5 & -3 \\ -3 & 2 \end{bmatrix} = \begin{bmatrix} 5 & -3 \\ -3 & 2 \end{bmatrix}$$
- **ধাপ ৩: হ্যাট ম্যাট্রিক্স $H = X(X^T X)^{-1} X^T$ গণনা:**
  $$X (X^T X)^{-1} = \begin{bmatrix} 1 & 1 \\ 1 & 2 \end{bmatrix} \begin{bmatrix} 5 & -3 \\ -3 & 2 \end{bmatrix} = \begin{bmatrix} 5-3 & -3+2 \\ 5-6 & -3+4 \end{bmatrix} = \begin{bmatrix} 2 & -1 \\ -1 & 1 \end{bmatrix}$$
  $$H = \begin{bmatrix} 2 & -1 \\ -1 & 1 \end{bmatrix} \begin{bmatrix} 1 & 1 \\ 1 & 2 \end{bmatrix} = \begin{bmatrix} 2-1 & 2-2 \\ -1+1 & -1+2 \end{bmatrix} = \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix} = I$$
- **ধাপ ৪: প্রজেকশন যাচাই:**
  $$\hat{\mathbf{y}} = H \mathbf{y} = \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix} \begin{bmatrix} 2 \\ 3 \end{bmatrix} = \begin{bmatrix} 2 \\ 3 \end{bmatrix}$$
  *(যেহেতু ডেটা পয়েন্টের সংখ্যা ও প্যারামিটার সংখ্যা সমান ছিল, মডেল নিখুঁত ১০০% ফিট হয়েছে এবং এরর ভেক্টর $\mathbf{e} = \mathbf{y} - \hat{\mathbf{y}} = \mathbf{0}$!)*

---

### ১.৯ আইগেনভ্যালু, আইগেনভেক্টর ও ডায়াগোনালাইজেশন

$$A \mathbf{v} = \lambda \mathbf{v} \iff \det(A - \lambda I) = 0$$

#### 🔢 সম্পূর্ণ বীজগণিতীয় সমাধান:
ধরি ম্যাট্রিক্স $A = \begin{bmatrix} 4 & 2 \\ 1 & 3 \end{bmatrix}$
- **ধাপ ১: ক্যারেক্টারিস্টিক পলিনোমিয়াল:**
  $$\det\left(\begin{bmatrix} 4-\lambda & 2 \\ 1 & 3-\lambda \end{bmatrix}\right) = (4-\lambda)(3-\lambda) - (2)(1) = 0$$
  $$\lambda^2 - 7\lambda + 12 - 2 = \lambda^2 - 7\lambda + 10 = 0$$
  $$(\lambda - 5)(\lambda - 2) = 0 \implies \lambda_1 = 5, \quad \lambda_2 = 2$$
- **ধাপ ২: প্রথম আইগেনভ্যালু $\lambda_1 = 5$-এর জন্য আইগেনভেক্টর নির্ণয়:**
  $$(A - 5I)\mathbf{v}_1 = \mathbf{0} \implies \begin{bmatrix} 4-5 & 2 \\ 1 & 3-5 \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix} = \begin{bmatrix} -1 & 2 \\ 1 & -2 \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$$
  $$-x + 2y = 0 \implies x = 2y$$
  ধরি $y = 1 \implies \mathbf{v}_1 = \begin{bmatrix} 2 \\ 1 \end{bmatrix}$।
  ইউনিট নরমালাইজড আইগেনভেক্টর: $\hat{\mathbf{v}}_1 = \frac{1}{\sqrt{2^2 + 1^2}} \begin{bmatrix} 2 \\ 1 \end{bmatrix} = \begin{bmatrix} 2/\sqrt{5} \\ 1/\sqrt{5} \end{bmatrix}$।
- **ধাপ ৩: দ্বিতীয় আইগেনভ্যালু $\lambda_2 = 2$-এর জন্য আইগেনভেক্টর নির্ণয়:**
  $$(A - 2I)\mathbf{v}_2 = \mathbf{0} \implies \begin{bmatrix} 4-2 & 2 \\ 1 & 3-2 \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix} = \begin{bmatrix} 2 & 2 \\ 1 & 1 \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$$
  $$x + y = 0 \implies x = -y$$
  ধরি $y = 1 \implies \mathbf{v}_2 = \begin{bmatrix} -1 \\ 1 \end{bmatrix}$।
  ইউনিট আইগেনভেক্টর: $\hat{\mathbf{v}}_2 = \begin{bmatrix} -1/\sqrt{2} \\ 1/\sqrt{2} \end{bmatrix}$।
- **যাচাই:**
  $$A \mathbf{v}_1 = \begin{bmatrix} 4 & 2 \\ 1 & 3 \end{bmatrix} \begin{bmatrix} 2 \\ 1 \end{bmatrix} = \begin{bmatrix} 8+2 \\ 2+3 \end{bmatrix} = \begin{bmatrix} 10 \\ 5 \end{bmatrix} = 5 \begin{bmatrix} 2 \\ 1 \end{bmatrix} = \lambda_1 \mathbf{v}_1 \quad \text{Q.E.D. (প্রমাণিত!)}$$

---

### ১.১০ সিঙ্গুলার ভ্যালু ডিকম্পোজিশন (SVD): হাতে-কলমে $U, \Sigma, V^T$ নির্ণয়

ধরি ম্যাট্রিক্স $A = \begin{bmatrix} 3 & 0 \\ 4 & 5 \end{bmatrix}$
SVD রূপ: $A = U \Sigma V^T$

- **ধাপ ১: $A^T A$ গণনা করা ($V$ এবং $\Sigma$-এর জন্য):**
  $$A^T A = \begin{bmatrix} 3 & 4 \\ 0 & 5 \end{bmatrix} \begin{bmatrix} 3 & 0 \\ 4 & 5 \end{bmatrix} = \begin{bmatrix} 9+16 & 0+20 \\ 20 & 0+25 \end{bmatrix} = \begin{bmatrix} 25 & 20 \\ 20 & 25 \end{bmatrix}$$
- **ধাপ ২: $A^T A$-এর আইগেনভ্যালু নির্ণয়:**
  $$\det\left(\begin{bmatrix} 25-\lambda & 20 \\ 20 & 25-\lambda \end{bmatrix}\right) = (25-\lambda)^2 - 400 = 0$$
  $$(25-\lambda)^2 = 400 \implies 25 - \lambda = \pm 20$$
  $$\lambda_1 = 45, \quad \lambda_2 = 5$$
- **ধাপ ৩: সিঙ্গুলার ভ্যালুজ ($\Sigma$) গণনা:**
  $$\sigma_1 = \sqrt{\lambda_1} = \sqrt{45} = 3\sqrt{5} \approx 6.708$$
  $$\sigma_2 = \sqrt{\lambda_2} = \sqrt{5} \approx 2.236$$
  $$\Sigma = \begin{bmatrix} 3\sqrt{5} & 0 \\ 0 & \sqrt{5} \end{bmatrix}$$
- **ধাপ ৪: $V$ ম্যাট্রিক্সের আইগেনভেক্টর:**
  $\lambda_1 = 45$-এর জন্য: $(25-45)x + 20y = 0 \implies -20x + 20y = 0 \implies x = y$
  $$\mathbf{v}_1 = \frac{1}{\sqrt{2}} \begin{bmatrix} 1 \\ 1 \end{bmatrix}, \quad \mathbf{v}_2 = \frac{1}{\sqrt{2}} \begin{bmatrix} -1 \\ 1 \end{bmatrix}$$
- **ধাপ ৫: $U$ ম্যাট্রিক্সের কলাম নির্ণয় ($\mathbf{u}_i = \frac{1}{\sigma_i} A \mathbf{v}_i$):**
  $$\mathbf{u}_1 = \frac{1}{3\sqrt{5}} \begin{bmatrix} 3 & 0 \\ 4 & 5 \end{bmatrix} \begin{bmatrix} 1/\sqrt{2} \\ 1/\sqrt{2} \end{bmatrix} = \frac{1}{3\sqrt{10}} \begin{bmatrix} 3 \\ 9 \end{bmatrix} = \begin{bmatrix} 1/\sqrt{10} \\ 3/\sqrt{10} \end{bmatrix}$$
  $$\mathbf{u}_2 = \frac{1}{\sqrt{5}} \begin{bmatrix} 3 & 0 \\ 4 & 5 \end{bmatrix} \begin{bmatrix} -1/\sqrt{2} \\ 1/\sqrt{2} \end{bmatrix} = \frac{1}{\sqrt{10}} \begin{bmatrix} -3 \\ 1 \end{bmatrix} = \begin{bmatrix} -3/\sqrt{10} \\ 1/\sqrt{10} \end{bmatrix}$$
- এভাবেই সম্পূর্ণ বিশ্লেষণী উপায়ে $U, \Sigma, V^T$ অর্জিত হয়!

---

# ২. স্তম্ভ ২: বহুচলক ক্যালকুলাস ও অপটিমাইজেশন

---

### ২.১ ডেরিভেটিভ, আংশিক অন্তরীকরণ ও ডিরেকশনাল ডেরিভেটিভ

#### 🔢 হাতে-কলমে উদাহরণ:
ধরি বহুচলক কস্ট ফাংশন: $f(x, y) = x^2 + 3xy + 2y^2$
আমরা বিন্দু $P(1, 2)$-তে এর পরিবর্তন বিশ্লেষণ করব।

- **ধাপ ১: আংশিক ডেরিভেটিভসমূহ:**
  $$\frac{\partial f}{\partial x} = 2x + 3y, \quad \frac{\partial f}{\partial y} = 3x + 4y$$
- **ধাপ ২: বিন্দু $P(1, 2)$-তে গ্রেডিয়েন্ট ভেক্টর ($\nabla f$):**
  $$\nabla f(1, 2) = \begin{bmatrix} 2(1) + 3(2) \\ 3(1) + 4(2) \end{bmatrix} = \begin{bmatrix} 2 + 6 \\ 3 + 8 \end{bmatrix} = \begin{bmatrix} 8 \\ 11 \end{bmatrix}$$
- **ধাপ ৩: ডিরেকশনাল ডেরিভেটিভ নির্ণয়:**
  ধরি আমরা দিক ভেক্টর $\mathbf{u} = \begin{bmatrix} 1 \\ 1 \end{bmatrix}$ বরাবর পরিবর্তনের হার জানতে চাই।
  ইউনিট দিক ভেক্টর: $\hat{\mathbf{u}} = \frac{1}{\sqrt{1^2 + 1^2}} \begin{bmatrix} 1 \\ 1 \end{bmatrix} = \begin{bmatrix} 1/\sqrt{2} \\ 1/\sqrt{2} \end{bmatrix}$
  $$D_{\hat{\mathbf{u}}} f(1, 2) = \nabla f \cdot \hat{\mathbf{u}} = (8 \times \frac{1}{\sqrt{2}}) + (11 \times \frac{1}{\sqrt{2}}) = \frac{19}{\sqrt{2}} \approx 13.435$$
  *(অর্থাৎ এই দিক বরাবর ১ ইউনিট এগোলে ফাংশনের মান ১৩.৪৩৫ বৃদ্ধি পাবে!)*

---

### ২.২ হেসিয়ান ম্যাট্রিক্স, টেলর সিরিজ ও স্যাডল পয়েন্ট পরীক্ষা

#### 🔢 হাতে-কলমে হেসিয়ান ও কনভেক্সিটি টেস্ট:
পূর্বের ফাংশনটি বিবেচনা করি: $f(x, y) = x^2 + 3xy + 2y^2$
- দ্বিতীয় ক্রমের আংশিক অন্তরকসমূহ:
  $$\frac{\partial^2 f}{\partial x^2} = \frac{\partial}{\partial x}(2x + 3y) = 2$$
  $$\frac{\partial^2 f}{\partial y^2} = \frac{\partial}{\partial y}(3x + 4y) = 4$$
  $$\frac{\partial^2 f}{\partial x \partial y} = \frac{\partial}{\partial x}(3x + 4y) = 3$$
- **হেসিয়ান ম্যাট্রিক্স:**
  $$H = \begin{bmatrix} 2 & 3 \\ 3 & 4 \end{bmatrix}$$
- **কনভেক্সিটি যাচাই (নির্ণায়ক ও আইগেনভ্যালু পরীক্ষা):**
  $$\det(H) = (2 \times 4) - (3 \times 3) = 8 - 9 = -1 < 0$$
- **সিদ্ধান্ত:** যেহেতু $\det(H) < 0$, হেসিয়ান ম্যাট্রিক্সের একটি আইগেনভ্যালু ধনাত্মক এবং অপরটি ঋণাত্মক (Indefinite Matrix)। সুতরাং ফাংশনটি কনভেক্স নয় এবং এর ক্রিটিক্যাল পয়েন্ট একটি **স্যাডল পয়েন্ট (Saddle Point)**!

---

### ২.৩ গ্রেডিয়েন্ট ডিসেন্ট ডাইনামিক্স: ৩-ধাপের সংখ্যাভিত্তিক ট্রেসিং

ধরি অপটিমাইজেশন সমস্যা: $f(w) = w^2$ (গ্লোবাল মিনিমাম $w^* = 0$)।
ধরি প্রারম্ভিক ওয়েট $w^{(0)} = 5.0$, লার্নিং রেট $\alpha = 0.1$।
ডেরিভেটিভ: $\nabla f(w) = 2w$।

| ইটারেশন ($t$) | বর্তমান ওয়েট ($w^{(t)}$) | লস ($f(w) = w^2$) | গ্রেডিয়েন্ট ($\nabla f = 2w$) | স্টেপ সাইজ ($\alpha \nabla f$) | নতুন ওয়েট ($w^{(t+1)} = w - \alpha \nabla f$) |
| :---: | :---: | :---: | :---: | :---: | :---: |
| **০** | **৫.০** | ২৫.০০ | $2 \times 5.0 = 10.0$ | $0.1 \times 10.0 = 1.0$ | $5.0 - 1.0 = 4.0$ |
| **১** | **৪.০** | ১৬.০০ | $2 \times 4.0 = 8.0$ | $0.1 \times 8.0 = 0.8$ | $4.0 - 0.8 = 3.2$ |
| **২** | **৩.২** | ১০.২৪ | $2 \times 3.2 = 6.4$ | $0.1 \times 6.4 = 0.64$ | $3.2 - 0.64 = 2.56$ |
| **৩** | **২.৫৬** | ৬.৫৫ | $2 \times 2.56 = 5.12$ | $0.1 \times 5.12 = 0.512$ | $2.56 - 0.512 = 2.048$ |

- **পর্যবেক্ষণ:** প্রতিটি ধাপে লস ২৫ $\to$ ১৬ $\to$ ১০.২৪ $\to$ ৬.৫৫ এ দ্রুত হ্রাস পাচ্ছে। ওয়েট $w \to 0$-এর দিকে কনভার্জ করছে।

---

# ৩. স্তম্ভ ৩: সম্ভাবনা ও গাণিতিক পরিসংখ্যান

---

### ৩.১ বেসেল কারেকশন ($m-1$): ক্ষুদ্র পপুলেশন দিয়ে নিখুঁত সংখ্যাভিত্তিক প্রমাণ

ধরি একটি অতি ক্ষুদ্র পপুলেশন মাত্র ৩টি সংখ্যা নিয়ে গঠিত: $\{2, 4, 6\}$ (পপুলেশন সাইজ $N = 3$)।

- **আসল পপুলেশন গড় ($\mu$):**
  $$\mu = \frac{2 + 4 + 6}{3} = \frac{12}{3} = 4$$
- **আসল পপুলেশন ভ্যারিয়্যান্স ($\sigma^2$):**
  $$\sigma^2 = \frac{(2-4)^2 + (4-4)^2 + (6-4)^2}{3} = \frac{(-2)^2 + 0^2 + 2^2}{3} = \frac{4 + 0 + 4}{3} = \frac{8}{3} \approx 2.667$$

এখন এই পপুলেশন থেকে প্রতিস্থাপনসহ (with replacement) $m = 2$ সাইজের সকল সম্ভাব্য স্যাম্পল তৈরি করি। মোট $3^2 = 9$ টি স্যাম্পল তৈরি সম্ভব:

| স্যাম্পল | উপাদান | স্যাম্পল গড় ($\bar{x}$) | $m=2$ দিয়ে ভাগ ($S_n^2$) | $m-1=1$ দিয়ে ভাগ ($S_{n-1}^2$) |
| :---: | :---: | :---: | :---: | :---: |
| ১ | $(2, 2)$ | ২.০ | $\frac{(2-2)^2 + (2-2)^2}{2} = 0$ | $\frac{0}{1} = 0$ |
| ২ | $(2, 4)$ | ৩.০ | $\frac{(2-3)^2 + (4-3)^2}{2} = 1$ | $\frac{1+1}{1} = 2$ |
| ৩ | $(2, 6)$ | ৪.০ | $\frac{(2-4)^2 + (6-4)^2}{2} = 4$ | $\frac{4+4}{1} = 8$ |
| ৪ | $(4, 2)$ | ৩.০ | $\frac{(4-3)^2 + (2-3)^2}{2} = 1$ | $\frac{1+1}{1} = 2$ |
| ৫ | $(4, 4)$ | ৪.০ | $0$ | $0$ |
| ৬ | $(4, 6)$ | ৫.০ | $\frac{(4-5)^2 + (6-5)^2}{2} = 1$ | $\frac{1+1}{1} = 2$ |
| ৭ | $(6, 2)$ | ৪.০ | $\frac{(6-4)^2 + (2-4)^2}{2} = 4$ | $\frac{4+4}{1} = 8$ |
| ৮ | $(6, 4)$ | ৫.০ | $\frac{(6-5)^2 + (4-5)^2}{2} = 1$ | $\frac{1+1}{1} = 2$ |
| ৯ | $(6, 6)$ | ৬.০ | $0$ | $0$ |

#### ফলাফল তুলনা:
1. **$m$ দিয়ে ভাগ করা ভ্যারিয়্যান্সের প্রত্যাশিত গড়:**
   $$\mathbb{E}[S_n^2] = \frac{0 + 1 + 4 + 1 + 0 + 1 + 4 + 1 + 0}{9} = \frac{12}{9} \approx 1.333$$
   *(ভয়াবহ আন্ডার-এস্টিমেশন! আসল ভ্যারিয়্যান্স ছিল $2.667$, কিন্তু $m$ দিয়ে ভাগ করায় এসেছে মাত্র $1.333$—অর্ধেক মান গায়েব হয়ে গেছে!)*
2. **$m-1$ দিয়ে ভাগ করা ভ্যারিয়্যান্সের প্রত্যাশিত গড় (Bessel's Correction):**
   $$\mathbb{E}[S_{n-1}^2] = \frac{0 + 2 + 8 + 2 + 0 + 2 + 8 + 2 + 0}{9} = \frac{24}{9} = \frac{8}{3} \approx 2.667$$
   *(নিখুঁত শতভাগ মিল! $\mathbb{E}[S^2] = \sigma^2$। এটিই হলো বেসেল কারেকশনে $m-1$ ব্যবহারের অলঙ্ঘনীয় গাণিতিক প্রমাণ!)*

---

### ৩.২ ম্যাক্সিমাম লাইকলিহুড এস্টিমেশন (MLE): ৩টি কয়েন ফ্লিপ থেকে সম্পূর্ণ প্রতিপাদন

ধরি একটি বায়াসড কয়েন ৩ বার টস করা হলো এবং ফলাফল আসলো: **Head, Head, Tail** (বা $1, 1, 0$)।
ধরি হেড ওঠার অজানা সম্ভাব্যতা $p$ ($0 \le p \le 1$)।

- **ধাপ ১: লাইকলিহুড ফাংশন গঠন:**
  $$L(p) = P(H) \times P(H) \times P(T) = p \times p \times (1 - p) = p^2 (1 - p) = p^2 - p^3$$
- **ধাপ ২: লগ-লাইকলিহুড রূপান্তর:**
  $$\ell(p) = \ln L(p) = \ln(p^2) + \ln(1 - p) = 2 \ln(p) + \ln(1 - p)$$
- **ধাপ ৩: ডেরিভেটিভ শূন্য ধরে সমাধান:**
  $$\frac{d\ell}{dp} = \frac{2}{p} - \frac{1}{1 - p} = 0$$
  $$\frac{2}{p} = \frac{1}{1 - p} \implies 2(1 - p) = p \implies 2 - 2p = p \implies 3p = 2 \implies p_{\text{MLE}} = \frac{2}{3} \approx 0.667$$
- **ব্যাখ্যা:** ৩টি ট্রায়ালে ২টি হেড আসায় কমনসেন্স বলে হেড ওঠার সম্ভাবনা ২/৩ হওয়া উচিত। ক্যালকুলাসের MLE কঠোরভাবে প্রমাণ করে যে ২/৩-ই হলো সেই প্যারামিটার যা এই ডেটা পাওয়ার সম্ভাবনাকে সর্বোচ্চ করে!

---

# ৪. স্তম্ভ ৪: তথ্য তত্ত্ব (Information Theory)

---

### ৪.১ সেলফ-ইনফরমেশন ও শ্যানন এন্ট্রপি: ৮টি বলের সংখ্যাভিত্তিক হিসাব

ধরি একটি পাত্রে মোট ৮টি বল আছে: **৫টি লাল (Red)** এবং **৩টি নীল (Blue)**।
- লাল বল ওঠার সম্ভাবনা: $p_R = \frac{5}{8} = 0.625$
- নীল বল ওঠার সম্ভাবনা: $p_B = \frac{3}{8} = 0.375$

```
Container of 8 Balls: [ R, R, R, R, R, B, B, B ]
  p(Red)  = 5/8 = 0.625  --> Self-Information I(R) = -log2(0.625) = 0.678 bits
  p(Blue) = 3/8 = 0.375  --> Self-Information I(B) = -log2(0.375) = 1.415 bits (More surprise!)
```

#### ধাপভিত্তিক শ্যানন এন্ট্রপি গণনা:
$$H(S) = - \left[ p_R \log_2(p_R) + p_B \log_2(p_B) \right]$$
1. $\log_2(0.625) = \frac{\ln(0.625)}{\ln(2)} = \frac{-0.4700}{0.6931} \approx -0.6781$
2. $\log_2(0.375) = \frac{\ln(0.375)}{\ln(2)} = \frac{-0.9808}{0.6931} \approx -1.4150$
3. গুণফলসমূহ:
   $$0.625 \times (-0.6781) \approx -0.4238$$
   $$0.375 \times (-1.4150) \approx -0.5306$$
4. চূড়ান্ত এন্ট্রপি:
   $$H(S) = - [-0.4238 - 0.5306] = - [-0.9544] = \mathbf{0.9544 \text{ bits}}$$
- **তাৎপর্য:** পাত্রে যেহেতু দুটি ভিন্ন রঙের বল আছে, এতে অনিশ্চয়তা রয়েছে। যদি সব বল লাল হতো ($p_R = 1$), তবে এন্ট্রপি হতো $0$ bit। যদি ৪টি লাল ও ৪টি নীল হতো ($p=0.5$), তবে এন্ট্রপি সর্বোচ্চ $1.0$ bit হতো।

---

### ৪.২ ক্রস-এন্ট্রপি ও মডেল তুলনা: সংখ্যাভিত্তিক ক্ষতি বিশ্লেষণ

ধরি একটি ছবি রয়েছে যা নিশ্চিতভাবে **বিড়াল (Cat)** ($y = [1, 0, 0]$ for Cat, Dog, Bird)।
দুটি ভিন্ন মডেল এই ছবির জন্য প্রেডিকশন দিয়েছে:
- **Model 1:** $Q_1 = [0.70, 0.20, 0.10]$ (৭০% নিশ্চিত বিড়াল)
- **Model 2:** $Q_2 = [0.20, 0.50, 0.30]$ (ভুলবশত কুকুর মনে করছে)

#### ক্রস-এন্ট্রপি লস গণনা:
$$\mathcal{L}_{CE} = - \sum_{k=1}^3 P(k) \ln(Q(k)) = - [1 \cdot \ln(Q_{\text{Cat}}) + 0 + 0] = - \ln(Q_{\text{Cat}})$$
- **Model 1 Loss:**
  $$\mathcal{L}_1 = - \ln(0.70) \approx -(-0.3567) = \mathbf{0.3567}$$
- **Model 2 Loss:**
  $$\mathcal{L}_2 = - \ln(0.20) \approx -(-1.6094) = \mathbf{1.6094}$$
- **ফলাফল:** Model 2 ভুল প্রেডিকশন দেওয়ায় তার ক্রস-এন্ট্রপি লস সাড়ে চার গুণ বেশি এসেছে ($1.6094 > 0.3567$)। এভাবেই ক্রস-এন্ট্রপি লস ভুল মডেলকে কঠোর পেনাল্টি দেয়!

---

### ৪.৩ ইনফরমেশন গেইন (Information Gain) সংখ্যাভিত্তিক সমাধান

পূর্বের ৮টি বলের পাত্রটি ধরি একটি ফিচার (যেমন: বলের ওজন > ৫০ গ্রাম?) দিয়ে দুটি পাত্রে বিভক্ত হলো:
- **লেফট চাইল্ড ($S_L$):** ৪টি বল (৪টিই লাল, ০টি নীল) $\implies$ সম্পূর্ণ বিশুদ্ধ!
  $$H(S_L) = - [1 \log_2(1) + 0] = \mathbf{0.0 \text{ bits}}$$
- **রাইট চাইল্ড ($S_R$):** ৪টি বল (১টি লাল, ৩টি নীল)
  $$p_R = 1/4 = 0.25, \quad p_B = 3/4 = 0.75$$
  $$H(S_R) = - [0.25 \log_2(0.25) + 0.75 \log_2(0.75)] = - [0.25(-2) + 0.75(-0.415)] = - [-0.5 - 0.311] = \mathbf{0.811 \text{ bits}}$$

#### ওয়েটেড চাইল্ড এন্ট্রপি ও ইনফরমেশন গেইন:
- চাইল্ডদের গড় এন্ট্রপি:
  $$H(S, A) = \frac{4}{8} H(S_L) + \frac{4}{8} H(S_R) = 0.5(0.0) + 0.5(0.811) = \mathbf{0.4055 \text{ bits}}$$
- **ইনফরমেশন গেইন (Information Gain):**
  $$\text{IG} = H(S) - H(S, A) = 0.9544 - 0.4055 = \mathbf{0.5489 \text{ bits}}$$
- **সিদ্ধান্ত:** এই স্প্লিটের মাধ্যমে ডেটাসেটের বিশৃঙ্খলা বা অনিশ্চয়তা $0.5489$ বিট কমে গেছে। ডিসিশন ট্রি সর্বোচ্চ ইনফরমেশন গেইনযুক্ত স্প্লিটটি নির্বাচন করবে!

---

# 💻 ৫. হাতে-কলমে ভেরিফিকেশন টাস্ক ও স্ক্র্যাচ পাইথন কোড

```python
"""
mathematics_for_ml_verification.py
Phase 2 Verification Checklist:
1. Implement vector operations and matrix multiplication with NumPy.
2. Implement linear regression using closed-form and gradient descent.
3. Simulate distributions and compare observed statistics with theoretical values.
4. Calculate entropy and information gain for a small classification dataset.
"""

import numpy as np

# =====================================================================
# টাস্ক ১: ভেক্টর অপারেশন ও ম্যাট্রিক্স মাল্টিপ্লিকেশন স্ক্র্যাচ থেকে
# =====================================================================
def vector_dot_product(u: np.ndarray, v: np.ndarray) -> float:
    assert u.shape == v.shape, "ভেক্টরের আকার অবশ্যই অভিন্ন হতে হবে"
    dot = 0.0
    for i in range(len(u)):
        dot += u[i] * v[i]
    return float(dot)

def matrix_multiplication(A: np.ndarray, B: np.ndarray) -> np.ndarray:
    m, k1 = A.shape
    k2, n = B.shape
    assert k1 == k2, f"ইনভ্যালিড মাত্রা: A ({A.shape}) এবং B ({B.shape})"
    C = np.zeros((m, n))
    for i in range(m):
        for j in range(n):
            for k in range(k1):
                C[i, j] += A[i, k] * B[k, j]
    return C

# =====================================================================
# টাস্ক ২: লিনিয়ার রিগ্রেশন: নরমাল সমীকরণ বনাম গ্রেডিয়েন্ট ডিসেন্ট
# =====================================================================
class ScratchLinearRegression:
    def __init__(self):
        self.w_closed_form = None
        self.w_gd = None
        self.cost_history = []

    def fit_closed_form(self, X: np.ndarray, y: np.ndarray):
        """w = (X^T X)^(-1) X^T y"""
        m = X.shape[0]
        X_b = np.c_[np.ones((m, 1)), X]
        self.w_closed_form = np.linalg.inv(X_b.T @ X_b) @ (X_b.T @ y)
        return self.w_closed_form

    def fit_gradient_descent(self, X: np.ndarray, y: np.ndarray, lr: float = 0.05, epochs: int = 500):
        m, n = X.shape
        X_b = np.c_[np.ones((m, 1)), X]
        self.w_gd = np.zeros(n + 1)
        self.cost_history = []

        for _ in range(epochs):
            predictions = X_b @ self.w_gd
            errors = predictions - y
            gradient = (1 / m) * (X_b.T @ errors)
            self.w_gd -= lr * gradient
            cost = (1 / (2 * m)) * np.sum(errors ** 2)
            self.cost_history.append(cost)
        return self.w_gd

# =====================================================================
# টাস্ক ৩: ডিস্ট্রিবিউশন সিমুলেশন ও পরিসংখ্যানিক যাচাই
# =====================================================================
def verify_distribution_simulation(n_samples: int = 100_000):
    np.random.seed(42)
    # গাউসিয়ান সিমুলেশন: তাত্ত্বিক গড় = 10.0, ভ্যারিয়্যান্স = 4.0 (মানক বিচ্যুতি = 2.0)
    mu_true = 10.0
    sigma_true = 2.0
    samples = np.random.normal(loc=mu_true, scale=sigma_true, size=n_samples)

    mu_observed = np.mean(samples)
    sigma_observed = np.std(samples, ddof=1) # বেসেল কারেকশন সহ

    print(f"📊 [গাউসিয়ান সিমুলেশন] তাত্ত্বিক গড়: {mu_true}, নমুনা গড়: {mu_observed:.4f}")
    print(f"📊 [গাউসিয়ান সিমুলেশন] তাত্ত্বিক বিচ্যুতি: {sigma_true}, নমুনা বিচ্যুতি: {sigma_observed:.4f}")

    # সেন্ট্রাল লিমিট থিওরেম (CLT) সিমুলেশন
    sample_means = [np.mean(np.random.uniform(0, 1, size=50)) for _ in range(10_000)]
    clt_mean = np.mean(sample_means)
    print(f"🎯 [CLT সিমুলেশন] তাত্ত্বিক গড়: 0.5000, ৫০-সাইজ স্যাম্পল গড়ের গড়: {clt_mean:.4f}")

# =====================================================================
# টাস্ক ৪: ক্লাসিফিকেশনের জন্য এন্ট্রপি ও ইনফরমেশন গেইন গণনা
# =====================================================================
def compute_shannon_entropy(labels: np.ndarray) -> float:
    if len(labels) == 0:
        return 0.0
    _, counts = np.unique(labels, return_counts=True)
    probabilities = counts / len(labels)
    entropy = -np.sum(probabilities * np.log2(probabilities + 1e-12))
    return float(entropy)

def compute_information_gain(parent_labels: np.ndarray, left_labels: np.ndarray, right_labels: np.ndarray) -> float:
    total_n = len(parent_labels)
    if total_n == 0:
        return 0.0
    h_parent = compute_shannon_entropy(parent_labels)
    w_left = len(left_labels) / total_n
    w_right = len(right_labels) / total_n
    h_children = w_left * compute_shannon_entropy(left_labels) + w_right * compute_shannon_entropy(right_labels)
    return float(h_parent - h_children)

# =====================================================================
# এক্সিকিউশন ও ভ্যালিডেশন
# =====================================================================
if __name__ == "__main__":
    print("=" * 60)
    print("🚀 ফেজ ২ ম্যাথমেটিক্স ফর মেশিন লার্নিং ভেরিফিকেশন রান")
    print("=" * 60)

    # ১. ভেক্টর ও ম্যাট্রিক্স টেস্ট
    v1 = np.array([1.0, 2.0, 3.0])
    v2 = np.array([4.0, 5.0, 6.0])
    print(f"Pure Dot Product: {vector_dot_product(v1, v2)} (NumPy Check: {np.dot(v1, v2)})")

    A = np.array([[1, 2], [3, 4]])
    B = np.array([[5, 6], [7, 8]])
    print("Pure Matrix Multiplication:\n", matrix_multiplication(A, B))

    # ২. রিগ্রেশন মডেল টেস্ট
    X_synthetic = 2 * np.random.rand(200, 1)
    y_synthetic = 4 + 3 * X_synthetic.flatten() + np.random.randn(200) * 0.2

    model = ScratchLinearRegression()
    w_cf = model.fit_closed_form(X_synthetic, y_synthetic)
    w_gd = model.fit_gradient_descent(X_synthetic, y_synthetic, lr=0.1, epochs=300)

    print(f"\n📈 [রিগ্রেশন ওএলএস সমাধান] বায়াস: {w_cf[0]:.4f}, স্লোপ: {w_cf[1]:.4f}")
    print(f"📉 [গ্রেডিয়েন্ট ডিসেন্ট সমাধান] বায়াস: {w_gd[0]:.4f}, স্লোপ: {w_gd[1]:.4f}")

    # ৩. সিমুলেশন অডিট
    print("\n" + "-" * 50)
    verify_distribution_simulation()

    # ৪. এন্ট্রপি ও ইনফরমেশন গেইন টেস্ট
    parent = np.array([1, 1, 1, 1, 1, 0, 0, 0])        # ৫টি ১, ৩টি ০ (৮টি বলের উদাহরণ)
    left = np.array([1, 1, 1, 1])                       # ৪টি ১
    right = np.array([1, 0, 0, 0])                      # ১টি ১, ৩টি ০

    h_p = compute_shannon_entropy(parent)
    ig = compute_information_gain(parent, left, right)
    print("\n" + "-" * 50)
    print(f"প্যারেন্ট নোড এন্ট্রপি: {h_p:.4f} bits (হাতে-কলমে হিসাব ছিল: 0.9544)")
    print(f"ফিচার স্প্লিটের ইনফরমেশন গেইন: {ig:.4f} bits (হাতে-কলমে হিসাব ছিল: 0.5489)")
    print("=" * 60)
    print("✅ সকল ম্যাথমেটিক্যাল ভেরিফিকেশন সফলভাবে সম্পন্ন!")
```

---

## 🎯 রোডম্যাপ পাস কন্ডিশন মূল্যায়ন (Pass Condition Mastery)

### ১. গ্রেডিয়েন্ট ডিসেন্ট কীভাবে প্যারামিটার আপডেট করে?
গ্রেডিয়েন্ট ডিসেন্ট কস্ট ফাংশনের টেলর সিরিজ সম্প্রসারণের প্রথম ক্রমের ভিত্তিতে পরিচালিত হয়। গ্রেডিয়েন্ট ভেক্টর $\nabla_{\mathbf{w}} J(\mathbf{w})$ নির্দেশ করে কস্ট ফাংশন বৃদ্ধির সর্বোচ্চ দিক। সুতরাং, কস্ট দ্রুততম কমাতে প্যারামিটারগুলোকে ঠিক তার বিপরীত দিকে (Negative Gradient) সরাতে হয়:
$$\mathbf{w}^{(t+1)} = \mathbf{w}^{(t)} - \alpha \nabla_{\mathbf{w}} J(\mathbf{w}^{(t)})$$

### ২. লার্নিং রেট ($\alpha$) কীভাবে ট্রেনিংকে প্রভাবিত করে?
- **যদি $\alpha$ খুব ছোট হয়:** মডেল কচ্ছপের গতিতে অগ্রসর হয়; কনভার্জ করতে লাখ লাখ পদক্ষেপ লাগে এবং স্যাডল পয়েন্ট বা অগভীর লোকাল মিনিমাতে আটকে যায়।
- **যদি $\alpha$ খুব বড় হয়:** কস্ট সারফেসের সর্বনিম্ন উপত্যকাকে ওভারশুট (Overshoot) করে কস্ট প্রতি ধাপে বৃদ্ধি পায় এবং মডেল অসীমে বিস্ফোরিত (Diverge) হয়।
- **অপটিমাল শর্ত:** লিপসিৎস কনস্ট্যান্ট $L$-এর জন্য $\alpha \approx \frac{1}{L}$ বজায় রাখা অথবা অ্যাডাপ্টিভ লার্নিং রেট (যেমন Adam/RMSprop) ব্যবহার করা।

### ৩. সম্ভাবনা এবং এন্ট্রপি কীভাবে ক্লাসিফিকেশনের ভিত্তি রচনা করে?
ক্লাসিফিকেশন মডেল মূলত সরাসরি লেবেল প্রেডিক্ট করে না; এটি ইনপুট ফিচারের ভিত্তিতে শর্তাধীন সম্ভাবনা ডিস্ট্রিবিউশন $P(y=k|\mathbf{x})$ অনুমান করে। তথ্য তত্ত্বে **শ্যানন এন্ট্রপি** হলো এই সম্ভাবনার অনিশ্চয়তার পরিমাপ। যখন আমরা মডেলের প্রেডিক্টেড সম্ভাবনা $Q$ এবং বাস্তব লেবেলের সম্ভাবনা $P$-এর মধ্যে সাদৃশ্য অর্জন করতে চাই, তখন আমরা তাদের **ক্রস-এন্ট্রপি লস** মিনিমাইজ করি, যা মূলত তাদের মধ্যকার **KL-ডাইভারজেন্সকে শূন্যে নামিয়ে আনার** সমান। এভাবেই সম্ভাবনা এবং তথ্য তত্ত্বের এন্ট্রপি যৌথভাবে সকল সুপারভাইজড ক্লাসিফিকেশন লস ফাংশনের গাণিতিক ভিত গড়ে তোলে।

---
*রোডম্যাপ নেভিগেশন:* [👉 মূল মাস্টার সূচিপত্রে ফিরে যান](file:///g:/MNC/ML/README.md)
