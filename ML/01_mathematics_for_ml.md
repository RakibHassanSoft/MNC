# অধ্যায় ১: মেশিন লার্নিংয়ের জন্য অপরিহার্য গণিত (Mathematics for Machine Learning)

> *"Mathematics is the language of machine learning. Without math, ML is just magic; with math, it is engineering and science."*

মেশিন লার্নিং কোনো কাল্পনিক জাদুকরী প্রক্রিয়া নয়; এটি পুরোপুরি ফলিত গণিত (Applied Mathematics)। এই অধ্যায়ে আমরা মেশিন লার্নিংয়ের চারটি স্তম্ভ নিয়ে বিস্তারিত আলোচনা ও গাণিতিক প্রমাণ শিখব:
1. **রৈখিক বীজগণিত (Linear Algebra)**
2. **বহুচলক ক্যালকুলাস (Multivariate Calculus)**
3. **সম্ভাবনা ও পরিসংখ্যান (Probability & Mathematical Statistics)**
4. **অপটিমাইজেশন থিওরি (Optimization Theory)**

---

## ১.১ রৈখিক বীজগণিত (Linear Algebra)

মেশিন লার্নিংয়ে ডেটা সর্বদা ভেক্টর এবং ম্যাট্রিক্স আকারে উপস্থাপিত হয়। একটি ডেটাসেটের প্রতিটি সারি (Row) হলো একটি ডেটা পয়েন্ট (ভেক্টর) এবং প্রতিটি কলাম (Column) হলো একটি বৈশিষ্ট্য বা ফিচার।

### ১.১.১ ভেক্টর এবং ভেক্টর স্পেস (Vectors & Vector Spaces)
একটি $n$-মাত্রিক বাস্তব ভেক্টর $\mathbf{x} \in \mathbb{R}^n$:
$$\mathbf{x} = \begin{bmatrix} x_1 \\ x_2 \\ \vdots \\ x_n \end{bmatrix}$$

#### ভেক্টর ডট প্রোডাক্ট (Dot Product / Inner Product)
দুটি ভেক্টর $\mathbf{u}, \mathbf{v} \in \mathbb{R}^n$-এর ডট প্রোডাক্ট:
$$\mathbf{u} \cdot \mathbf{v} = \mathbf{u}^T \mathbf{v} = \sum_{i=1}^n u_i v_i = u_1 v_1 + u_2 v_2 + \dots + u_n v_n$$

**জ্যামিতিক তাৎপর্য:**
$$\mathbf{u} \cdot \mathbf{v} = \|\mathbf{u}\| \|\mathbf{v}\| \cos(\theta)$$
যেখানে $\theta$ হলো ভেক্টর দুটির মধ্যবর্তী কোণ।
- যদি $\mathbf{u} \cdot \mathbf{v} = 0$ হয়, তবে ভেক্টর দুটি **অর্থোগোনাল (Orthogonal)** বা পরস্পর লম্ব ($\theta = 90^\circ$)।
- কোসাইন সাদৃশ্য (Cosine Similarity):
  $$\text{Cosine Similarity} = \cos(\theta) = \frac{\mathbf{u} \cdot \mathbf{v}}{\|\mathbf{u}\|_2 \|\mathbf{v}\|_2}$$
  এটি টেক্সট মাইনিং, রেকমেন্ডেশন সিস্টেম এবং এলএলএম এম্বেডিং সার্চে বহুল ব্যবহৃত হয়।

#### ভেক্টর নর্ম (Vector Norms - দূরত্বের পরিমাপ)
নর্ম হলো কোনো ভেক্টরের দৈর্ঘ্য বা আকারের পরিমাপ ($L_p$ নর্ম):
$$\|\mathbf{x}\|_p = \left( \sum_{i=1}^n |x_i|^p \right)^{1/p}$$

1. **$L_1$ নর্ম (Manhattan Norm):**
   $$\|\mathbf{x}\|_1 = \sum_{i=1}^n |x_i|$$
   - ল্যাসো রেগুলারাইজেশনে (Lasso) ব্যবহৃত হয়। এটি মডেলের অপ্রয়োজনীয় প্যারামিটারগুলোকে শূন্য (Sparse) করতে সাহায্য করে।
2. **$L_2$ নর্ম (Euclidean Norm):**
   $$\|\mathbf{x}\|_2 = \sqrt{\sum_{i=1}^n x_i^2} = \sqrt{\mathbf{x}^T \mathbf{x}}$$
   - সবচেয়ে সাধারণ দূরত্ব পরিমাপ এবং রিজ রেগুলারাইজেশনের (Ridge) ভিত্তি।
3. **$L_\infty$ নর্ম (Max Norm):**
   $$\|\mathbf{x}\|_\infty = \max_{i} |x_i|$$

---

### ১.১.২ ম্যাট্রিক্স এবং ম্যাট্রিক্স বীজগণিত (Matrix Algebra)
একটি $m \times n$ ম্যাট্রিক্স $A \in \mathbb{R}^{m \times n}$:
$$A = \begin{bmatrix} 
a_{11} & a_{12} & \dots & a_{1n} \\ 
a_{21} & a_{22} & \dots & a_{2n} \\ 
\vdots & \vdots & \ddots & \vdots \\ 
a_{m1} & a_{m2} & \dots & a_{mn} 
\end{bmatrix}$$

- **ম্যাট্রিক্স ট্রান্সপোজ (Transpose):** সারিগুলোকে কলাম এবং কলামগুলোকে সারিতে রূপান্তর ($A_{ij}^T = A_{ji}$)।
  - বৈশিষ্ট্য: $(AB)^T = B^T A^T$
- **প্রতিসম ম্যাট্রিক্স (Symmetric Matrix):** যদি $A = A^T$ হয়। (যেমন কোভ্যারিয়্যান্স ম্যাট্রিক্স সর্বদা সিমেট্রিক)।
- **ম্যাট্রিক্স ইনভার্স (Inverse):** যদি $A \in \mathbb{R}^{n \times n}$ বর্গাকার ম্যাট্রিক্স হয় এবং $AB = BA = I$ হয়, তবে $B = A^{-1}$।
  - শর্ত: নির্ণায়ক $\det(A) \neq 0$ হতে হবে (Non-singular Matrix)।

---

### ১.১.৩ আইগেনভ্যালু এবং আইগেনভেক্টর (Eigenvalues & Eigenvectors)
রৈখিক রূপান্তরে (Linear Transformation) বেশিরভাগ ভেক্টরের দিক এবং দৈর্ঘ্য উভয়ই পরিবর্তিত হয়। কিন্তু কিছু বিশেষ ভেক্টর থাকে যাদের রূপান্তরের পরও দিক অপরিবর্তিত থাকে, কেবল দৈর্ঘ্য একটি স্কেলার মান দ্বারা প্রসারিত বা সংকুচিত হয়।

#### সংজ্ঞা:
যদি একটি বর্গাকার ম্যাট্রিক্স $A \in \mathbb{R}^{n \times n}$, একটি অশূন্য ভেক্টর $\mathbf{v} \in \mathbb{R}^n$, এবং একটি স্কেলার $\lambda \in \mathbb{R}$ এর জন্য:
$$A \mathbf{v} = \lambda \mathbf{v}$$
শর্ত পূরণ হয়, তবে $\mathbf{v}$-কে বলা হয় $A$-এর **আইগেনভেক্টর** এবং $\lambda$-কে বলা হয় সংশ্লিষ্ট **আইগেনভ্যালু**।

#### বৈশিষ্ট্য সমীকরণ (Characteristic Equation):
$$(A - \lambda I)\mathbf{v} = \mathbf{0}$$
যেহেতু $\mathbf{v} \neq \mathbf{0}$, তাই সমীকরণটির অশূন্য সমাধানের জন্য ম্যাট্রিক্সের নির্ণায়ক শূন্য হতে হবে:
$$\det(A - \lambda I) = 0$$

#### হাতে-কলমে উদাহরণ (Hand-calculated Example):
ধরি ম্যাট্রিক্স $A = \begin{bmatrix} 4 & 2 \\ 1 & 3 \end{bmatrix}$। এর আইগেনভ্যালু ও আইগেনভেক্টর নির্ণয়:
$$\det(A - \lambda I) = \det\left(\begin{bmatrix} 4-\lambda & 2 \\ 1 & 3-\lambda \end{bmatrix}\right) = (4-\lambda)(3-\lambda) - (2)(1) = 0$$
$$\implies \lambda^2 - 7\lambda + 12 - 2 = 0 \implies \lambda^2 - 7\lambda + 10 = 0$$
$$(\lambda - 5)(\lambda - 2) = 0 \implies \lambda_1 = 5, \quad \lambda_2 = 2$$

১. যখন $\lambda_1 = 5$:
$$(A - 5I)\mathbf{v} = \begin{bmatrix} 4-5 & 2 \\ 1 & 3-5 \end{bmatrix}\begin{bmatrix} v_1 \\ v_2 \end{bmatrix} = \begin{bmatrix} -1 & 2 \\ 1 & -2 \end{bmatrix}\begin{bmatrix} v_1 \\ v_2 \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$$
$$\implies -v_1 + 2v_2 = 0 \implies v_1 = 2v_2$$
যদি $v_2 = 1$ ধরি, তবে প্রথম আইগেনভেক্টর $\mathbf{v}_1 = \begin{bmatrix} 2 \\ 1 \end{bmatrix}$।

---

## ১.২ বহুচলক ক্যালকুলাস ও গ্রেডিয়েন্ট (Multivariate Calculus)

মেশিন লার্নিং মডেলে অপটিমাইজেশন করার মূল চাবিকাঠি হলো ডেরিভেটিভ।

### ১.২.১ আংশিক অবকলন ও গ্রেডিয়েন্ট (Partial Derivatives & Gradient)
যদি $f(\mathbf{x}): \mathbb{R}^n \rightarrow \mathbb{R}$ একটি বহুচলক স্কেলার ফাংশন হয়, যেখানে $\mathbf{x} = [x_1, x_2, \dots, x_n]^T$, তবে তার গ্রেডিয়েন্ট $\nabla f(\mathbf{x})$:
$$\nabla f(\mathbf{x}) = \begin{bmatrix} \frac{\partial f}{\partial x_1} \\ \frac{\partial f}{\partial x_2} \\ \vdots \\ \frac{\partial f}{\partial x_n} \end{bmatrix}$$

### ১.২.২ চেইন রুল (Chain Rule)
যদি $y = f(u)$ এবং $u = g(x)$ হয়, তবে:
$$\frac{dy}{dx} = \frac{dy}{du} \cdot \frac{du}{dx}$$
ভেক্টর আকারে (নিউরাল নেটওয়ার্ক ব্যাকপ্রোপাগেশনে ব্যবহৃত):
$$\frac{\partial z}{\partial \mathbf{x}} = \left( \frac{\partial \mathbf{u}}{\partial \mathbf{x}} \right)^T \frac{\partial z}{\partial \mathbf{u}}$$
যেখানে $\frac{\partial \mathbf{u}}{\partial \mathbf{x}}$ হলো **জ্যাকোবিয়ান ম্যাট্রিক্স (Jacobian Matrix)**।

### ১.২.৩ হেসিয়ান ম্যাট্রিক্স (Hessian Matrix - দ্বিতীয় মাত্রার অবকলন)
একটি ফাংশনের বক্রতা (Curvature) বোঝার জন্য দ্বিতীয় মাত্রার আংশিক অবকলনের সিমেট্রিক ম্যাট্রিক্স:
$$H_{ij} = \frac{\partial^2 f}{\partial x_i \partial x_j}$$
$$H = \begin{bmatrix} 
\frac{\partial^2 f}{\partial x_1^2} & \frac{\partial^2 f}{\partial x_1 \partial x_2} & \dots & \frac{\partial^2 f}{\partial x_1 \partial x_n} \\
\frac{\partial^2 f}{\partial x_2 \partial x_1} & \frac{\partial^2 f}{\partial x_2^2} & \dots & \frac{\partial^2 f}{\partial x_2 \partial x_n} \\
\vdots & \vdots & \ddots & \vdots \\
\frac{\partial^2 f}{\partial x_n \partial x_1} & \frac{\partial^2 f}{\partial x_n \partial x_2} & \dots & \frac{\partial^2 f}{\partial x_n^2}
\end{bmatrix}$$
- যদি $H$ **পজিটিভ ডেফিনিট (Positive Definite)** হয় ($\mathbf{z}^T H \mathbf{z} > 0, \forall \mathbf{z} \neq \mathbf{0}$), তবে বিন্দুটি একটি **লোকাল মিনিমাম (Local Minimum)**।
- সেকেন্ড-অর্ডার অপটিমাইজার যেমন **Newton-Raphson** এবং **XGBoost**-এ হেসিয়ান ম্যাট্রিক্স ব্যবহৃত হয়।

---

## ১.৩ সম্ভাবনা ও পরিসংখ্যান (Probability & Statistics)

### ১.৩.১ দৈব চলক ও ডিস্ট্রিবিউশন (Random Variables & Distributions)
- **গাউসিয়ান / নরমাল ডিস্ট্রিবিউশন (Normal Distribution):**
  $$p(x; \mu, \sigma^2) = \frac{1}{\sqrt{2\pi\sigma^2}} \exp\left( -\frac{(x - \mu)^2}{2\sigma^2} \right)$$
  - $\mu$ হলো প্রত্যাশিত মান বা গড় (Mean): $\mathbb{E}[X] = \mu$
  - $\sigma^2$ হলো ভ্যারিয়্যান্স (Variance): $\text{Var}(X) = \mathbb{E}[(X - \mu)^2] = \sigma^2$
- **বহুমাত্রিক গাউসিয়ান (Multivariate Normal Distribution):**
  $$p(\mathbf{x}; \boldsymbol{\mu}, \Sigma) = \frac{1}{(2\pi)^{n/2} |\Sigma|^{1/2}} \exp\left( -\frac{1}{2} (\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu}) \right)$$
  যেখানে $\Sigma \in \mathbb{R}^{n \times n}$ হলো কোভ্যারিয়্যান্স ম্যাট্রিক্স।

### ১.৩.২ কোভ্যারিয়্যান্স ও কোরিলেশন (Covariance & Correlation)
দুটি চলক $X$ এবং $Y$-এর পারস্পরিক সম্পর্কের পরিমাপ:
$$\text{Cov}(X, Y) = \mathbb{E}[(X - \mathbb{E}[X])(Y - \mathbb{E}[Y])]$$
পিয়ারসন কোরিলেশন কোফিশিয়েন্ট (Pearson Correlation Coefficient):
$$\rho_{X, Y} = \frac{\text{Cov}(X, Y)}{\sigma_X \sigma_Y} \in [-1, 1]$$

### ১.৩.৩ ম্যাক্সিমাম লাইকলিহুড এস্টিমেশন (MLE - Maximum Likelihood Estimation)
মেশিন লার্নিং মডেলের অপটিমাইজেশনের মূল ভিত্তি হলো MLE।
ধরি আমাদের কাছে স্বাধীন ও সমবন্টিত (i.i.d.) ডেটা $D = \{x^{(1)}, x^{(2)}, \dots, x^{(m)}\}$ রয়েছে, যা একটি অজানা প্যারামিটার $\theta$-র ডিস্ট্রিবিউশন থেকে এসেছে।

লাইকলিহুড ফাংশন:
$$L(\theta) = P(D|\theta) = \prod_{i=1}^m P(x^{(i)}|\theta)$$
গুনফল যোগফলে রূপান্তর করতে লগ-লাইকলিহুড নেওয়া হয়:
$$\ell(\theta) = \ln L(\theta) = \sum_{i=1}^m \ln P(x^{(i)}|\theta)$$
সেরা প্যারামিটার $\theta_{\text{MLE}}$ হবে:
$$\theta_{\text{MLE}} = \arg\max_\theta \ell(\theta) = \arg\min_\theta [-\ell(\theta)]$$
*(এখান থেকেই নেগেটিভ লগ লাইকলিহুড বা লস ফাংশনের ধারণা তৈরি হয়েছে!)*

---

## ১.৪ স্ক্র্যাচ কোড: গাণিতিক লাইব্রেরি (Pure NumPy)

```python
import numpy as np

# ১. কোসাইন সাদৃশ্য ফাংশন
def cosine_similarity(u, v):
    dot_prod = np.dot(u, v)
    norm_u = np.linalg.norm(u)
    norm_v = np.linalg.norm(v)
    if norm_u == 0 or norm_v == 0:
        return 0.0
    return dot_prod / (norm_u * norm_v)

# ২. কোভ্যারিয়্যান্স ম্যাট্রিক্স গণনা স্ক্র্যাচ থেকে
def compute_covariance_matrix(X):
    # X এর আকার: (m_samples, n_features)
    m = X.shape[0]
    mean = np.mean(X, axis=0)
    X_centered = X - mean
    cov_matrix = (1 / (m - 1)) * np.dot(X_centered.T, X_centered)
    return cov_matrix

# ৩. টেস্ট অপারেশন
if __name__ == "__main__":
    v1 = np.array([1.0, 2.0, 3.0])
    v2 = np.array([2.0, 4.0, 6.0])
    print(f"Cosine Similarity (Parallel Vectors): {cosine_similarity(v1, v2):.4f}") # 1.0000

    data = np.array([[2.5, 2.4],
                     [0.5, 0.7],
                     [2.2, 2.9],
                     [1.9, 2.2],
                     [3.1, 3.0]])
    print("Covariance Matrix:\n", compute_covariance_matrix(data))
```
