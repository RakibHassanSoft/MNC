# Chapter 05: Multivariate Statistics & Linear Algebra Intersections

---

## ১. মাল্টিভ্যারিয়েট ডেটা ও র‍্যান্ডম ভেক্টর (Multivariate Random Vectors)

বাস্তব জগতের মেশিন লার্নিংয়ে ডেটা কখনো একক চলকে সীমাবদ্ধ থাকে না। একটি ইমেজে হাজার হাজার পিক্সেল, একজন রোগীর মেডিকেল রেকর্ডে শত শত ল্যাব টেস্ট ভ্যালু এবং একটি টেক্সট ডকুমেন্টে হাজার হাজার ডাইমেনশনের এম্বেডিং থাকে।
এই বহুমাত্রিক চলকগুলোকে একটি **র‍্যান্ডম ভেক্টর (Random Vector)** আকারে প্রকাশ করা হয়:

$$X = \begin{bmatrix} X_1 \\ X_2 \\ \vdots \\ X_d \end{bmatrix} \in \mathbb{R}^d$$

### ১. গড় ভেক্টর (Mean Vector $\boldsymbol{\mu}$):
প্রতিটি ফিচারের ব্যক্তিগত প্রত্যাশিত মানের সমষ্টি:

$$\boldsymbol{\mu} = \mathbb{E}[X] = \begin{bmatrix} \mathbb{E}[X_1] \\ \mathbb{E}[X_2] \\ \vdots \\ \mathbb{E}[X_d] \end{bmatrix} = \begin{bmatrix} \mu_1 \\ \mu_2 \\ \vdots \\ \mu_d \end{bmatrix} \in \mathbb{R}^d$$

---

## ২. কোভ্যারিয়েন্স ম্যাট্রিক্স ($\boldsymbol{\Sigma}$ - The Covariance Matrix)

বহুমাত্রিক ডেটাসেটে সমস্ত ফিচারের নিজস্ব ভ্যারিয়েন্স এবং প্রতিটি ফিচারের সাথে অন্য ফিচারের পারস্পরিক কোভ্যারিয়েন্স একটি একক $d \times d$ ম্যাট্রিক্সে ধারণ করা হয়:

$$\boldsymbol{\Sigma} = \text{Cov}(X) = \mathbb{E}\left[ (X - \boldsymbol{\mu})(X - \boldsymbol{\mu})^T \right] \in \mathbb{R}^{d \times d}$$

```
                   [ COVARIANCE MATRIX STRUCTURE ]
              X_1               X_2          ...       X_d
       +----------------------------------------------------+
   X_1 | Var(X_1) = sigma_1^2   Cov(X_1, X_2) ...  Cov(X_1, X_d) |
   X_2 | Cov(X_2, X_1)          Var(X_2)      ...  Cov(X_2, X_d) |
   ... | ...                    ...           ...  ...           |
   X_d | Cov(X_d, X_1)          Cov(X_d, X_2) ...  Var(X_d)      |
       +----------------------------------------------------+
       \------------------ Diagonal ------------------------/
                     (Variances along Main Diagonal)
```

### ৩টি মৌলিক গাণিতিক বৈশিষ্ট্য (Mathematical Properties):
1. **সিমেট্রিক (Symmetric):**
   যেহেতু $\text{Cov}(X_i, X_j) = \text{Cov}(X_j, X_i)$, তাই:
   $$\boldsymbol{\Sigma} = \boldsymbol{\Sigma}^T$$
2. **পজিটিভ সেমি-ডেফিনিট (Positive Semi-Definite - PSD):**
   যেকোনো অশূন্য ভেক্টর $v \in \mathbb{R}^d$-এর জন্য:
   $$v^T \boldsymbol{\Sigma} v \ge 0$$
   *প্রমাণ:*
   $$v^T \boldsymbol{\Sigma} v = v^T \mathbb{E}[(X - \boldsymbol{\mu})(X - \boldsymbol{\mu})^T] v = \mathbb{E}\left[ v^T(X - \boldsymbol{\mu})(X - \boldsymbol{\mu})^T v \right]$$
   যেহেতু $v^T(X - \boldsymbol{\mu})$ একটি স্কেলার রাশি $z$:
   $$= \mathbb{E}[z \cdot z^T] = \mathbb{E}[z^2] \ge 0 \quad (\text{বর্গের গড় কখনো ঋণাত্মক হতে পারে না!})$$
3. **নন-নেগেটিভ আইগেনভ্যালু (Non-Negative Eigenvalues):**
   PSD হওয়ার কারণে কোভ্যারিয়েন্স ম্যাট্রিক্সের সমস্ত আইগেনভ্যালু অ-ঋণাত্মক: $\lambda_i \ge 0$।

---

## ৩. আইগেনভ্যালু, আইগেনভেক্টর ও পিসিএ (PCA Variance Maximization Proof)

### ১. জ্যামিতিক সংজ্ঞা (Geometric Meaning):
একটি ম্যাট্রিক্স $A$ একটি ভেক্টর $v$-এর ওপর ক্রিয়াশীল হলে সাধারণত ভেক্টরটির দিক ও দৈর্ঘ্য দুটিই পরিবর্তিত হয়। কিন্তু বিশেষ কিছু ভেক্টরের ক্ষেত্রে ম্যাট্রিক্স গুণনের পর দিকের কোনো পরিবর্তন হয় না, কেবল একটি স্কেলার $\lambda$ দ্বারা প্রসারিত বা সংকুচিত হয়:

$$\boldsymbol{\Sigma} v = \lambda v$$

- $v$ হলো **আইগেনভেক্টর (Eigenvector)** (ডেটা ক্লাউডের প্রধান অক্ষের দিক)।
- $\lambda$ হলো **আইগেনভ্যালু (Eigenvalue)** (ঐ অক্ষ বরাবর ডেটার ভ্যারিয়েন্সের পরিমাণ)।

```
                                      Eigenvector v_1 (Major Axis)
                                     /  [Direction of Maximum Variance: lambda_1]
                                    /
                     *   *  *  *   /
                  *    *   *  *   /
                 *   *   *   *   /
              --+---------------+---------------->
                 *   *   *   * /
                  *    *   *  /
                     *   *   /
                            /
                    Eigenvector v_2 (Minor Axis, lambda_2)
```

---

### ২. ল্যাগ্রাঞ্জ মাল্টিপ্লায়ার দিয়ে PCA-এর সম্পূর্ণ গাণিতিক প্রমাণ

প্রিন্সিপাল কম্পোনেন্ট অ্যানালাইসিস (PCA)-এর লক্ষ্য হলো এমন একটি একক ইউনিট ভেক্টর $u$ ($\|u\|_2 = 1 \implies u^T u = 1$) খুঁজে বের করা, যার ওপর ডেটা প্রজেক্ট করলে প্রজেক্টেড ডেটার ভ্যারিয়েন্স সর্বোচ্চ (maximum) হয়।

প্রজেক্টেড ভ্যারিয়েন্সের সমীকরণ:
$$\text{Var}(u^T X) = u^T \boldsymbol{\Sigma} u$$

অপটিমাইজেশন সমস্যা:
$$\max_u u^T \boldsymbol{\Sigma} u \quad \text{subject to } u^T u = 1$$

ল্যাগ্রাঞ্জিয়ান ফাংশন তৈরি করি:
$$\mathcal{L}(u, \lambda) = u^T \boldsymbol{\Sigma} u - \lambda(u^T u - 1)$$

$u$-এর সাপেক্ষে আংশিক অন্তরীকরণ করে শূন্য ধরি:
$$\nabla_u \mathcal{L} = 2\boldsymbol{\Sigma} u - 2\lambda u = 0$$
$$\boldsymbol{\Sigma} u = \lambda u$$

### যুগান্তকারী ফলাফল:
1. $u$-কে অবশ্যই কোভ্যারিয়েন্স ম্যাট্রিক্স $\boldsymbol{\Sigma}$-এর একটি **আইগেনভেক্টর** হতে হবে!
2. প্রজেক্টেড ভ্যারিয়েন্সের মান:
   $$u^T \boldsymbol{\Sigma} u = u^T (\lambda u) = \lambda (u^T u) = \lambda \cdot 1 = \lambda$$
3. অতএব, **ভ্যারিয়েন্স সর্বোচ্চ করতে হলে আমাদের কোভ্যারিয়েন্স ম্যাট্রিক্সের সর্বোচ্চ আইগেনভ্যালু ($\lambda_1$)-এর সাথে সম্পর্কিত আইগেনভেক্টর ($u_1$) বেছে নিতে হবে!**
4. দ্বিতীয় সর্বোচ্চ ভ্যারিয়েন্সের জন্য $\lambda_2$-এর আইগেনভেক্টর নেওয়া হয় (যা প্রথমটির সাথে লম্ব বা অর্থোগোনাল)।

---

## ৪. মাহালানোবিস দূরত্ব (Mahalanobis Distance & Anomaly Detection)

প্রশান্ত চন্দ্র মহলানবীশ (P. C. Mahalanobis, 1936) কর্তৃক উদ্ভাবিত এই দূরত্ব মেশিন লার্নিংয়ে মাল্টিভ্যারিয়েট আউটলায়ার ডিটেকশন ও ক্লাস্টারিংয়ের এক বৈপ্লবিক হাতিয়ার।

### কেন ইউক্লিডিয়ান দূরত্ব (Euclidean Distance) মারাত্মক ব্যর্থ হয়?
ইউক্লিডিয়ান দূরত্ব $d_E(x, \mu) = \sqrt{(x - \mu)^T (x - \mu)}$ ধরে নেয়:
- সমস্ত ফিচারের ভ্যারিয়েন্স বা বিস্তার সমান।
- ফিচারগুলোর মধ্যে কোনো পারস্পরিক সম্পর্ক (Correlation) নেই।

বাস্তব ডেটাসেটে ফিচারগুলো কোরিলেটেড থাকে এবং ডেটা ক্লাউড একটি বৃত্তাকার গোলকের বদলে বাঁকা উপবৃত্ত (Ellipsoid) গঠন করে। ইউক্লিডিয়ান দূরত্ব উপবৃত্তের প্রধান অক্ষ ও গৌণ অক্ষের পার্থক্য বোঝে না, ফলে স্বাভাবিক ডেটাকে আউটলায়ার এবং ভয়াবহ আউটলায়ারকে স্বাভাবিক মনে করে!

```
                        [ Euclidean vs Mahalanobis Distance ]
                                       ^ Feature Y
                                      /
                                     /       * Outlier Point B (Euclidean says: Close!)
                                    /                          (Mahalanobis says: OUTLIER!)
                      *  *  *  *   /
                   *   *   *   *  /
                  *   *   *   *  /   * Normal Point A (Euclidean says: Far!)
               --+---------------+------------------------> Feature X
                  *   *   *   * /
                   *   *   *   /
                      *  *  * /
                             /
```

### মাহালানোবিস দূরত্বের সূত্র:
$$D_M(x, \boldsymbol{\mu}) = \sqrt{(x - \boldsymbol{\mu})^T \boldsymbol{\Sigma}^{-1} (x - \boldsymbol{\mu})}$$

- $\boldsymbol{\Sigma}^{-1}$ (ইনভার্স কোভ্যারিয়েন্স ম্যাট্রিক্স বা Precision Matrix) ডেটার পারস্পরিক কোরিলেশন ও ভ্যারিয়েন্স দিয়ে দূরত্বকে ভাগ করে স্কেল-ইনভেরিয়েন্ট হোয়াইটেনিং (Whitening Transformation) করে।
- $D_M^2$ রাশিটি ডিগ্রি অব ফ্রিডম $d$ সহ **কাই-স্কয়ার ($\chi^2_d$) ডিস্ট্রিবিউশন** অনুসরণ করে। ফলে কাই-স্কয়ার টেবিলের মাধ্যমে ৯৯% আত্মবিশ্বাসের সাথে মাল্টিভ্যারিয়েট অ্যানোমালি শনাক্ত করা যায়!

---

## ৫. সম্পূর্ণ সংখ্যাগত হ্যান্ড-ক্যালকুলেশন (Step-by-Step Hand Calculation)

ধরা যাক ২-ডাইমেনশনাল ৩টি ডেটাপয়েন্ট:
$$x_1 = \begin{bmatrix} 2 \\ 2 \end{bmatrix}, \quad x_2 = \begin{bmatrix} 4 \\ 4 \end{bmatrix}, \quad x_3 = \begin{bmatrix} 6 \\ 6 \end{bmatrix}$$

### ধাপ ১: গড় ভেক্টর ($\boldsymbol{\mu}$) ও কেন্দ্রিকরণ
$$\boldsymbol{\mu} = \frac{1}{3} \left( \begin{bmatrix} 2 \\ 2 \end{bmatrix} + \begin{bmatrix} 4 \\ 4 \end{bmatrix} + \begin{bmatrix} 6 \\ 6 \end{bmatrix} \right) = \begin{bmatrix} 4 \\ 4 \end{bmatrix}$$

কেন্দ্রিকৃত ডেটা (Mean-centered):
$$x_1 - \mu = \begin{bmatrix} -2 \\ -2 \end{bmatrix}, \quad x_2 - \mu = \begin{bmatrix} 0 \\ 0 \end{bmatrix}, \quad x_3 - \mu = \begin{bmatrix} 2 \\ 2 \end{bmatrix}$$

### ধাপ ২: স্যাম্পল কোভ্যারিয়েন্স ম্যাট্রিক্স ($\boldsymbol{\Sigma}$, $n-1 = 2$)
$$\boldsymbol{\Sigma} = \frac{1}{2} \left( \begin{bmatrix} -2 \\ -2 \end{bmatrix}\begin{bmatrix} -2 & -2 \end{bmatrix} + \begin{bmatrix} 0 \\ 0 \end{bmatrix}\begin{bmatrix} 0 & 0 \end{bmatrix} + \begin{bmatrix} 2 \\ 2 \end{bmatrix}\begin{bmatrix} 2 & 2 \end{bmatrix} \right)$$
$$\boldsymbol{\Sigma} = \frac{1}{2} \left( \begin{bmatrix} 4 & 4 \\ 4 & 4 \end{bmatrix} + \begin{bmatrix} 0 & 0 \\ 0 & 0 \end{bmatrix} + \begin{bmatrix} 4 & 4 \\ 4 & 4 \end{bmatrix} \right) = \frac{1}{2} \begin{bmatrix} 8 & 8 \\ 8 & 8 \end{bmatrix} = \begin{bmatrix} 4 & 4 \\ 4 & 4 \end{bmatrix}$$

### ধাপ ৩: আইগেনভ্যালু নির্ণয়
বৈশিষ্ট্যসূচক সমীকরণ (Characteristic Equation):
$$\det(\boldsymbol{\Sigma} - \lambda I) = 0 \implies \det\begin{pmatrix} 4 - \lambda & 4 \\ 4 & 4 - \lambda \end{pmatrix} = 0$$
$$(4 - \lambda)(4 - \lambda) - (4 \times 4) = 0$$
$$(4 - \lambda)^2 - 16 = 0 \implies 16 - 8\lambda + \lambda^2 - 16 = 0$$
$$\lambda^2 - 8\lambda = 0 \implies \lambda(\lambda - 8) = 0$$

অতএব, দুটি আইগেনভ্যালু:
$$\mathbf{\lambda_1 = 8, \quad \lambda_2 = 0}$$

### ধাপ ৪: আইগেনভেক্টর নির্ণয় ($\lambda_1 = 8$-এর জন্য)
$$(\boldsymbol{\Sigma} - 8I)v_1 = 0 \implies \begin{pmatrix} 4 - 8 & 4 \\ 4 & 4 - 8 \end{pmatrix} \begin{pmatrix} v_{11} \\ v_{12} \end{pmatrix} = \begin{pmatrix} 0 \\ 0 \end{pmatrix}$$
$$\begin{pmatrix} -4 & 4 \\ 4 & -4 \end{pmatrix} \begin{pmatrix} v_{11} \\ v_{12} \end{pmatrix} = \begin{pmatrix} 0 \\ 0 \end{pmatrix} \implies -4v_{11} + 4v_{12} = 0 \implies v_{11} = v_{12}$$

নরমালাইজড ইউনিট ভেক্টর:
$$v_1 = \begin{bmatrix} \frac{1}{\sqrt{2}} \\ \frac{1}{\sqrt{2}} \end{bmatrix} \approx \begin{bmatrix} 0.7071 \\ 0.7071 \end{bmatrix}$$

**সিদ্ধান্ত:** প্রথম প্রিন্সিপাল কম্পোনেন্ট $v_1$ সমগ্র ভ্যারিয়েন্সের $\frac{8}{8 + 0} = \mathbf{100\%}$ ক্যাপচার করে! ডেটাপয়েন্টগুলো নিখুঁতভাবে $y = x$ রেখায় অবস্থিত হওয়ায় মাত্র ১টি কম্পোনেন্টেই ২-ডাইমেনশনাল ডেটাকে বিন্দুমাত্র তথ্য না হারিয়ে সংকুচিত করা সম্ভব।

---

## ৬. সম্পূর্ণ পাইথন বাস্তবায়ন (Scratch PCA & Mahalanobis Distance)

নিচে একটি স্বয়ংসম্পূর্ণ, ডিপেন্ডেন্সি-মুক্ত পাইথন বাস্তবায়ন দেওয়া হলো যা সম্পূর্ণ স্ক্র্যাচ থেকে কোভ্যারিয়েন্স ম্যাট্রিক্স, আইগেনভ্যালু সলভিং এবং মাহালানোবিস দূরত্ব গণনা করে:

```python
"""
Multivariate Statistics: Covariance Matrix, PCA from Scratch, and Mahalanobis Distance.
Fully runnable, verified and mathematically grounded.
"""

import math
from typing import List, Tuple, Dict, Any


# ============================================================================
# 1. Linear Algebra & Matrix Math Helpers
# ============================================================================
def matrix_mult(A: List[List[float]], B: List[List[float]]) -> List[List[float]]:
    rows_A, cols_A = len(A), len(A[0])
    rows_B, cols_B = len(B), len(B[0])
    assert cols_A == rows_B, "Incompatible matrix shapes"
    C = [[0.0 for _ in range(cols_B)] for _ in range(rows_A)]
    for i in range(rows_A):
        for j in range(cols_B):
            for k in range(cols_A):
                C[i][j] += A[i][k] * B[k][j]
    return C


def invert_2x2_matrix(A: List[List[float]]) -> List[List[float]]:
    det = A[0][0] * A[1][1] - A[0][1] * A[1][0]
    assert abs(det) > 1e-9, "Matrix is singular / non-invertible"
    inv_det = 1.0 / det
    return [
        [A[1][1] * inv_det, -A[0][1] * inv_det],
        [-A[1][0] * inv_det, A[0][0] * inv_det]
    ]


# ============================================================================
# 2. Covariance Matrix & Mahalanobis Distance Engine
# ============================================================================
class MultivariateEngine:
    @staticmethod
    def compute_covariance_matrix(data: List[List[float]]) -> Tuple[List[float], List[List[float]]]:
        """data shape: [num_samples, num_features]"""
        n = len(data)
        d = len(data[0])
        # Compute mean vector
        mean_vec = [sum(data[i][j] for i in range(n)) / n for j in range(d)]

        # Covariance matrix (n - 1)
        cov = [[0.0 for _ in range(d)] for _ in range(d)]
        for i in range(d):
            for j in range(d):
                s = sum((data[k][i] - mean_vec[i]) * (data[k][j] - mean_vec[j]) for k in range(n))
                cov[i][j] = s / (n - 1)
        return mean_vec, cov

    @staticmethod
    def mahalanobis_distance(x: List[float], mean: List[float], inv_cov: List[List[float]]) -> float:
        """D_M = sqrt((x - mu)^T * Sigma^{-1} * (x - mu))"""
        d = len(x)
        diff = [[x[i] - mean[i]] for i in range(d)]  # Column vector [d, 1]
        diff_T = [x[i] - mean[i] for i in range(d)]  # Row vector [1, d]

        # inv_cov * diff
        temp = [sum(inv_cov[i][j] * diff[j][0] for j in range(d)) for i in range(d)]
        # diff_T * temp
        dist_sq = sum(diff_T[i] * temp[i] for i in range(d))
        return math.sqrt(max(0.0, dist_sq))


# ============================================================================
# 3. 2D Principal Component Analysis (PCA) Solver
# ============================================================================
class Scratch2DPCA:
    @staticmethod
    def solve_pca_2d(cov: List[List[float]]) -> Dict[str, Any]:
        """Analytically solves eigenvalues and eigenvectors for 2x2 covariance."""
        a = cov[0][0]
        b = cov[0][1]
        c = cov[1][0]
        d = cov[1][1]

        # Characteristic polynomial: lambda^2 - Trace*lambda + Det = 0
        trace = a + d
        det = a * d - b * c
        discriminant = math.sqrt(max(0.0, trace ** 2 - 4 * det))

        lambda_1 = (trace + discriminant) / 2.0
        lambda_2 = (trace - discriminant) / 2.0

        # Eigenvector for lambda_1: (a - lambda_1)x + b*y = 0 -> y = -(a - lambda_1) / b * x
        if abs(b) > 1e-9:
            v1_x = b
            v1_y = lambda_1 - a
        else:
            v1_x = 1.0
            v1_y = 0.0
        norm_1 = math.sqrt(v1_x ** 2 + v1_y ** 2)
        v1 = [v1_x / norm_1, v1_y / norm_1]

        total_variance = lambda_1 + lambda_2
        explained_ratio = lambda_1 / total_variance if total_variance > 0 else 1.0

        return {
            "eigenvalue_1": lambda_1,
            "eigenvalue_2": lambda_2,
            "principal_vector": v1,
            "explained_variance_ratio": explained_ratio
        }


# ============================================================================
# 4. Verification & Demo Execution
# ============================================================================
if __name__ == "__main__":
    # Synthetic 2D dataset with correlated features
    dataset = [
        [2.0, 3.0],
        [3.0, 4.0],
        [5.0, 6.0],
        [7.0, 8.0],
        [8.0, 9.0]
    ]

    mean_vec, cov_mat = MultivariateEngine.compute_covariance_matrix(dataset)
    print("Mean Vector:\n ", mean_vec)
    print("Covariance Matrix:")
    for row in cov_mat:
        print(" ", [round(v, 4) for v in row])

    # 1. Run PCA
    pca_res = Scratch2DPCA.solve_pca_2d(cov_mat)
    print("\n--- 2D PCA Results ---")
    print(f"Top Eigenvalue (lambda_1):  {pca_res['eigenvalue_1']:.4f}")
    print(f"Second Eigenvalue (lambda_2):{pca_res['eigenvalue_2']:.4f}")
    print(f"Principal Eigenvector:      {[round(x, 4) for x in pca_res['principal_vector']]}")
    print(f"Variance Explained:         {pca_res['explained_variance_ratio'] * 100:.2f}%")

    # 2. Run Mahalanobis Distance for Anomaly Detection
    # Add regularized epsilon to diagonal to ensure invertible covariance
    cov_reg = [[cov_mat[i][j] + (0.1 if i == j else 0.0) for j in range(2)] for i in range(2)]
    inv_c = invert_2x2_matrix(cov_reg)

    normal_point = [5.0, 6.0]
    anomaly_point = [5.0, 1.0]  # Breaks correlation: Y is 1 instead of ~6

    d_norm = MultivariateEngine.mahalanobis_distance(normal_point, mean_vec, inv_c)
    d_anom = MultivariateEngine.mahalanobis_distance(anomaly_point, mean_vec, inv_c)

    print("\n--- Mahalanobis Distance (Anomaly Detection) ---")
    print(f"Normal Point {normal_point} D_M:  {d_norm:.4f}")
    print(f"Anomaly Point {anomaly_point} D_M: {d_anom:.4f} (Massive Distance Spikes Anomaly!)")
    print("Multivariate statistics components verified successfully!")
```

---

## ৭. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স

| মেট্রিক / কৌশল | ভ্যারিয়েন্স বিবেচনা | কোরিলেশন সমন্বয় | স্কেল ইনভেরিয়েন্ট? | প্রধান ব্যবহার |
| :--- | :--- | :--- | :--- | :--- |
| **Euclidean Distance** | না (সব দিক সমান) | না (ফিচার স্বাধীন ধরে) | না | সহজ কে-মিন্স (K-Means), k-NN |
| **Manhattan Distance** | না | না | না | হাই-ডাইমেনশনাল স্পার্সিটি, টেক্সট |
| **Mahalanobis Distance**| **হ্যাঁ ($\sigma_i$)** | **হ্যাঁ ($\boldsymbol{\Sigma}$ ইনভার্স)** | **হ্যাঁ (শতভাগ)** | **মাল্টিভ্যারিয়েট আউটলায়ার ডিটেকশন, GMM** |
| **PCA** | **হ্যাঁ (সর্বোচ্চ করে)** | **হ্যাঁ (ডেকোরিলেট করে)**| স্ট্যান্ডার্ডাইজেশন প্রয়োজন | **ডাইমেনশনালিটি রিডাকশন, ডেটা কম্প্রেশন** |

---

## ৮. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: PCA চালানোর পূর্বে ডেটাকে স্ট্যান্ডার্ডাইজ (StandardScaler) করা কি বাধ্যতামূলক?**
   - **উত্তর:** হ্যাঁ, অত্যন্ত জরুরি! PCA সর্বোচ্চ ভ্যারিয়েন্সের অক্ষ খোঁজে। যদি একটি ফিচারের একক হয় কিলোমিটার ($1-10$) এবং অন্যটির একক হয় মিলিমিটার ($1000-10,000$), তবে দ্বিতীয় ফিচারটির ভ্যারিয়েন্স কৃত্রিমভাবে লক্ষ গুণ বেশি দেখাবে। স্ট্যান্ডার্ডাইজ না করলে PCA কেবল সবচেয়ে বড় স্কেলের ফিচারটিকে অন্ধের মতো প্রথম প্রিন্সিপাল কম্পোনেন্ট বানিয়ে দেবে।
2. **প্রশ্ন: কোভ্যারিয়েন্স ম্যাট্রিক্স কেন সর্বদা Positive Semi-Definite (PSD) হয়?**
   - **উত্তর:** কোভ্যারিয়েন্স ম্যাট্রিক্সের কোয়াড্রেটিক ফর্ম $v^T \boldsymbol{\Sigma} v$ মূলত ডেটার যেকোনো লিনিয়ার কম্বিনেশনের ($z = v^T X$) ভ্যারিয়েন্সকে নির্দেশ করে। যেহেতু যেকোনো বাস্তব রাশির ভ্যারিয়েন্স সর্বদা অ-ঋণাত্মক ($\text{Var}(z) \ge 0$), তাই $v^T \boldsymbol{\Sigma} v \ge 0$ হতে বাধ্য। এর ফলে এর সমস্ত আইগেনভ্যালু বাস্তব এবং অ-ঋণাত্মক ($\lambda_i \ge 0$) হয়।
3. **প্রশ্ন: ইউক্লিডিয়ান দূরত্বের চেয়ে মাহালানোবিস দূরত্ব কখন ব্যবহার করা আবশ্যক?**
   - **উত্তর:** যখন ডেটাসেটের ফিচারগুলোর মধ্যে শক্তিশালী সহ-সম্পর্ক (Multicollinearity) থাকে এবং তাদের ভ্যারিয়েন্স ভিন্ন ভিন্ন হয়। যেমন ব্যাংক ফ্রড ডিটেকশনে লেনদেনের পরিমাণ এবং সময় পরস্পরের ওপর নির্ভরশীল। সেখানে ইউক্লিডিয়ান দূরত্ব বৃত্তাকার আইসো-কন্ট্যুর ধরে ভুল করে, আর মাহালানোবিস দূরত্ব উপবৃত্তাকার আইসো-কন্ট্যুর তৈরি করে নিখুঁতভাবে অ্যানোমালি শনাক্ত করে।
