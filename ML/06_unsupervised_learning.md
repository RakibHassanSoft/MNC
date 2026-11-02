# অধ্যায় ৬: আনসুপারভাইজড লার্নিং ও ডাইমেনশনালিটি রিডাকশন (Unsupervised Learning & Dimensionality Reduction)

> *"Unsupervised learning is the dark matter of artificial intelligence; most of human learning happens without explicit supervision."*

আনসুপারভাইজড লার্নিংয়ে কোনো গ্রাউন্ড ট্রুথ লেবেল ($y$) থাকে না। ইনপুট স্পেসের নিজস্ব গাঠনিক বিন্যাস, ক্লাস্টার বা সুপ্ত বৈশিষ্ট্য (Latent Representations) খুঁজে বের করাই এর কাজ।

---

## ৬.১ কে-মিন্স ক্লাস্টারিং (K-Means Clustering)

### ৬.১.১ অবজেক্টিভ ফাংশন ও কনভার্জেন্সের গাণিতিক প্রমাণ
কে-মিন্স ক্লাস্টারিংয়ের লক্ষ্য হলো ক্লাস্টারের অভ্যন্তরীণ দূরত্বের বর্গের সমষ্টি (Within-Cluster Sum of Squares - WCSS বা Inertia) ন্যূনতম করা:
$$J = \sum_{k=1}^K \sum_{i \in C_k} \|\mathbf{x}^{(i)} - \boldsymbol{\mu}_k\|^2$$

**লয়েডের অ্যালগরিদম (Lloyd's Algorithm) দুটি ধাপে কাজ করে:**
1. **অ্যাসাইনমেন্ট ধাপ (Assignment Step):**
   প্রতিটি পয়েন্টকে এমন ক্লাস্টারে নেওয়া হয় যা $J$-কে ন্যূনতম করে:
   $$C_k = \left\{ \mathbf{x}^{(i)} : \|\mathbf{x}^{(i)} - \boldsymbol{\mu}_k\|^2 \le \|\mathbf{x}^{(i)} - \boldsymbol{\mu}_j\|^2, \quad \forall j \right\}$$
2. **আপডেট ধাপ (Update Step):**
   নতুন সেন্ট্রয়েড নির্ধারণ করতে $J$-এর ডেরিভেটিভ $\boldsymbol{\mu}_k$-এর সাপেক্ষে শূন্য ধরা হয়:
   $$\frac{\partial J}{\partial \boldsymbol{\mu}_k} = -2 \sum_{i \in C_k} (\mathbf{x}^{(i)} - \boldsymbol{\mu}_k) = \mathbf{0}$$
   $$\sum_{i \in C_k} \mathbf{x}^{(i)} = |C_k| \boldsymbol{\mu}_k \implies \boldsymbol{\mu}_k = \frac{1}{|C_k|} \sum_{i \in C_k} \mathbf{x}^{(i)}$$

**কনভার্জেন্সের নিশ্চয়তা:** প্রতিটি পদক্ষেপে কস্ট ফাংশন $J$ হয় হ্রাস পায় অথবা অপরিবর্তিত থাকে। যেহেতু $m$ ডেটা পয়েন্টকে $K$ ক্লাস্টারে বিন্যস্ত করার উপায় সংখ্যা সসীম ($K^m$), তাই অ্যালগরিদমটি অবশ্যই একটি লোকাল মিনিমাতে কনভার্জ করবে।

---

### ৬.১.২ কে-মিন্স++ ইনিশিয়ালাইজেশন (K-Means++ Initialization)
দৈবচয়নভাবে সেন্ট্রয়েড বাছলে তা সাব-অপটিমাল লোকাল মিনিমাতে আটকে যায়। K-Means++ সেন্ট্রয়েডগুলোকে পরস্পরের থেকে দূরে দূরে ছড়িয়ে দেয়:
১. প্রথম সেন্ট্রয়েড $\mu_1$ ডেটাসেট থেকে সম্পূর্ণ দৈবচয়নভাবে নেওয়া হয়।
২. পরবর্তী সেন্ট্রয়েড নির্বাচনের জন্য প্রতিটি পয়েন্টের নিকটতম বিদ্যমান সেন্ট্রয়েডের দূরত্ব $D(\mathbf{x})$ বের করা হয়।
৩. পয়েন্ট $\mathbf{x}$-কে পরবর্তী সেন্ট্রয়েড হিসেবে বাছাই করার সম্ভাবনা নির্ধারণ করা হয়:
   $$P(\mathbf{x}) = \frac{D(\mathbf{x})^2}{\sum_{\mathbf{x}'} D(\mathbf{x}')^2}$$
৪. $K$ সংখ্যক সেন্ট্রয়েড না পাওয়া পর্যন্ত পুনরাবৃত্তি করা হয়।

---

## ৬.২ প্রিন্সিপাল কম্পোনেন্ট অ্যানালাইসিস (PCA)

PCA হলো ভ্যারিয়্যান্স বা তথ্যের বিস্তার সর্বাধিক রাখার মাধ্যমে উচ্চমাত্রিক ডেটাকে নিম্নমাত্রায় রূপান্তর করার একটি রৈখিক কৌশল।

### ৬.২.১ ল্যাগ্রাঞ্জ মাল্টিপ্লায়ার দিয়ে PCA-এর সম্পূর্ণ গাণিতিক প্রমাণ
ধরি ডেটা শূন্য-গড় বিশিষ্ট (Zero-centered): $\sum \mathbf{x}_i = \mathbf{0}$।
কোভ্যারিয়্যান্স ম্যাট্রিক্স:
$$\Sigma = \frac{1}{m} X^T X$$

আমরা একটি একক অক্ষ ভেক্টর $\mathbf{u}_1$ ($\|\mathbf{u}_1\|_2 = 1 \implies \mathbf{u}_1^T \mathbf{u}_1 = 1$) খুঁজতে চাই যার ওপর ডেটা প্রোজেকশন করলে ভ্যারিয়্যান্স সর্বাধিক হয়।
প্রোজেক্টেড ডেটার ভ্যারিয়্যান্স:
$$\text{Var}(\mathbf{u}_1) = \frac{1}{m} \sum_{i=1}^m (\mathbf{x}_i^T \mathbf{u}_1)^2 = \frac{1}{m} \mathbf{u}_1^T X^T X \mathbf{u}_1 = \mathbf{u}_1^T \Sigma \mathbf{u}_1$$

শর্তাধীন অপটিমাইজেশন সমস্যা:
$$\max_{\mathbf{u}_1} \mathbf{u}_1^T \Sigma \mathbf{u}_1 \quad \text{subject to } \mathbf{u}_1^T \mathbf{u}_1 = 1$$

ল্যাগ্রাঞ্জিয়ান ফাংশন:
$$\mathcal{L}(\mathbf{u}_1, \lambda) = \mathbf{u}_1^T \Sigma \mathbf{u}_1 - \lambda (\mathbf{u}_1^T \mathbf{u}_1 - 1)$$

$\mathbf{u}_1$-এর সাপেক্ষে ডেরিভেটিভ শূন্য ধরি:
$$\nabla_{\mathbf{u}_1} \mathcal{L} = 2 \Sigma \mathbf{u}_1 - 2 \lambda \mathbf{u}_1 = \mathbf{0}$$
$$\Sigma \mathbf{u}_1 = \lambda \mathbf{u}_1$$

**চরম পর্যবেক্ষণ:**
এটি সরাসরি একটি **আইগেনভ্যালু সমস্যা (Eigenvalue Problem)**!
উভয়পাশে বাম দিক থেকে $\mathbf{u}_1^T$ দ্বারা গুণ করলে:
$$\mathbf{u}_1^T \Sigma \mathbf{u}_1 = \lambda \mathbf{u}_1^T \mathbf{u}_1 = \lambda$$
অর্থাৎ প্রক্ষেপিত ভ্যারিয়্যান্স হলো স্বয়ং আইগেনভ্যালু $\lambda$। ভ্যারিয়্যান্স সর্বোচ্চ করতে হলে আমাদের **সর্বোচ্চ আইগেনভ্যালু ($\lambda_{max}$)** এবং তার সংশ্লিষ্ট **আইগেনভেক্টর ($\mathbf{u}_{max}$)** বেছে নিতে হবে!

*(প্রমাণিত)*

---

## ৬.৩ স্ক্র্যাচ কোড: সম্পূর্ণ K-Means ক্লাস্টারিং (Pure NumPy)

```python
import numpy as np

class KMeansScratch:
    def __init__(self, k=3, max_iters=100, tol=1e-4):
        self.k = k
        self.max_iters = max_iters
        self.tol = tol
        self.centroids = None

    def fit(self, X):
        m, n = X.shape
        # K-Means++ স্টাইল অথবা র‍্যান্ডম ইনিশিয়ালাইজেশন
        random_indices = np.random.choice(m, self.k, replace=False)
        self.centroids = X[random_indices]

        for iter_num in range(self.max_iters):
            # ১. অ্যাসাইনমেন্ট ধাপ: দূরত্ব নির্ণয়
            distances = np.linalg.norm(X[:, np.newaxis] - self.centroids, axis=2)
            labels = np.argmin(distances, axis=1)

            # ২. আপডেট ধাপ: নতুন সেন্ট্রয়েড নির্ণয়
            new_centroids = np.array([
                X[labels == k].mean(axis=0) if np.sum(labels == k) > 0 else self.centroids[k]
                for k in range(self.k)
            ])

            # ৩. কনভার্জেন্স পরীক্ষা
            shift = np.linalg.norm(new_centroids - self.centroids)
            self.centroids = new_centroids
            if shift < self.tol:
                break

    def predict(self, X):
        distances = np.linalg.norm(X[:, np.newaxis] - self.centroids, axis=2)
        return np.argmin(distances, axis=1)

# ভেরিফিকেশন
if __name__ == "__main__":
    np.random.seed(42)
    # ৩টি ক্লাস্টার তৈরি
    cluster1 = np.random.randn(50, 2) + np.array([0, 5])
    cluster2 = np.random.randn(50, 2) + np.array([5, 0])
    cluster3 = np.random.randn(50, 2) + np.array([-5, -5])
    X = np.vstack([cluster1, cluster2, cluster3])

    kmeans = KMeansScratch(k=3)
    kmeans.fit(X)
    print("Learned Cluster Centroids:\n", kmeans.centroids)
```
