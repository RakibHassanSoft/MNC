# অধ্যায় ৪: সুপারভাইজড ক্লাসিফিকেশন অ্যালগরিদম (Supervised Classification)

> *"Classification is the art of drawing optimal boundaries in high-dimensional spaces to separate truth from falsehood."*

ক্লাসিফিকেশন হলো এমন এক ধরনের সুপারভাইজড লার্নিং যেখানে মডেল অতীত ডেটা থেকে শিখে নতুন ইনপুটের জন্য ডিসক্রিট ক্লাস লেবেল (Discrete Class Label) নির্ধারণ করে।

---

## ৪.১ লজিস্টিক রিগ্রেশন (Logistic Regression & Softmax)

### ৪.১.১ অডস থেকে সিগময়েড সমীকরণ প্রতিপাদন (Derivation from Odds to Sigmoid)
ধরি কোনো ঘটনা ঘটার সম্ভাবনা $p$ ($0 \le p \le 1$)।
- **অডস রেশিও (Odds Ratio):** ঘটনাটি ঘটার সম্ভাবনা এবং না ঘটার সম্ভাবনার অনুপাত:
  $$\text{Odds} = \frac{p}{1 - p} \in [0, \infty)$$
- **লগ-অডস বা লজিট ফাংশন (Log-Odds / Logit):**
  অডসকে লগারিদম নিলে এর পরিসীমা সম্পূর্ণ বাস্তব সংখ্যা রেখায় ($(-\infty, \infty)$) রূপান্তরিত হয়:
  $$\text{logit}(p) = \ln\left( \frac{p}{1 - p} \right) \in (-\infty, \infty)$$

লজিস্টিক রিগ্রেশনে আমরা ধরে নিই যে এই লগ-অডস হলো ইনপুট ফিচারগুলোর একটি সরলরৈখিক সমাবেশ:
$$\ln\left( \frac{p}{1 - p} \right) = \mathbf{w}^T \mathbf{x} + b$$

উভয়পাশে এক্সপোনেনশিয়াল নিলে:
$$\frac{p}{1 - p} = e^{\mathbf{w}^T \mathbf{x} + b}$$
$$p = (1 - p) e^{\mathbf{w}^T \mathbf{x} + b} = e^{\mathbf{w}^T \mathbf{x} + b} - p e^{\mathbf{w}^T \mathbf{x} + b}$$
$$p \left( 1 + e^{\mathbf{w}^T \mathbf{x} + b} \right) = e^{\mathbf{w}^T \mathbf{x} + b}$$
$$p = \frac{e^{\mathbf{w}^T \mathbf{x} + b}}{1 + e^{\mathbf{w}^T \mathbf{x} + b}} = \frac{1}{1 + e^{-(\mathbf{w}^T \mathbf{x} + b)}} = \sigma(\mathbf{w}^T \mathbf{x} + b)$$

*(এভাবেই সিগময়েড ফাংশনের উৎপত্তি ঘটে!)*

---

### ৪.১.২ ম্যাক্সিমাম লাইকলিহুড এস্টিমেশন থেকে ক্রস-এনট্রপি লস প্রতিপাদন
ধরি প্রতিটি স্যাম্পল $y^{(i)} \in \{0, 1\}$ একটি বার্নৌলি ট্রায়াল মেনে চলে। স্যাম্পল $i$-এর জন্য সম্ভাবনা:
$$P(y^{(i)} | \mathbf{x}^{(i)}) = (\hat{y}^{(i)})^{y^{(i)}} (1 - \hat{y}^{(i)})^{1 - y^{(i)}}$$

$m$ সংখ্যক স্বাধীন নমুনার মোট লাইকলিহুড:
$$L(\mathbf{w}, b) = \prod_{i=1}^m (\hat{y}^{(i)})^{y^{(i)}} (1 - \hat{y}^{(i)})^{1 - y^{(i)}}$$

লগ-লাইকলিহুড:
$$\ln L(\mathbf{w}, b) = \sum_{i=1}^m \left[ y^{(i)} \ln(\hat{y}^{(i)}) + (1 - y^{(i)}) \ln(1 - \hat{y}^{(i)}) \right]$$

যেহেতু আমরা অপটিমাইজেশনে লস ন্যূনতম করতে চাই, তাই লগ-লাইকলিহুডের ঋণাত্মক গড় নিলে পাওয়া যায় **বাইনারি ক্রস-এনট্রপি (BCE) কস্ট ফাংশন**:
$$J(\mathbf{w}, b) = -\frac{1}{m} \sum_{i=1}^m \left[ y^{(i)} \ln(\hat{y}^{(i)}) + (1 - y^{(i)}) \ln(1 - \hat{y}^{(i)}) \right]$$

---

### ৪.১.৩ ক্রস-এনট্রপি লসের গ্রেডিয়েন্ট প্রমাণ (Step-by-Step Gradient Derivation)
সিগময়েড ফাংশন $\sigma(z) = \frac{1}{1 + e^{-z}}$-এর ডেরিভেটিভ:
$$\frac{d\sigma}{dz} = \frac{e^{-z}}{(1+e^{-z})^2} = \frac{1}{1+e^{-z}} \cdot \frac{e^{-z}}{1+e^{-z}} = \sigma(z)(1 - \sigma(z))$$

এখন, একটি একক স্যাম্পলের লস $L = - [y \ln \hat{y} + (1-y)\ln(1-\hat{y})]$ এর জন্য চেইন রুল প্রয়োগ করি:
$$\frac{\partial L}{\partial w_j} = \frac{\partial L}{\partial \hat{y}} \cdot \frac{\partial \hat{y}}{\partial z} \cdot \frac{\partial z}{\partial w_j}$$
1. $\frac{\partial L}{\partial \hat{y}} = -\frac{y}{\hat{y}} + \frac{1-y}{1-\hat{y}} = \frac{\hat{y} - y}{\hat{y}(1-\hat{y})}$
2. $\frac{\partial \hat{y}}{\partial z} = \hat{y}(1 - \hat{y})$
3. $\frac{\partial z}{\partial w_j} = x_j$

সবগুলো একসাথে গুণ করলে:
$$\frac{\partial L}{\partial w_j} = \left[ \frac{\hat{y} - y}{\hat{y}(1-\hat{y})} \right] \cdot \left[ \hat{y}(1 - \hat{y}) \right] \cdot x_j = (\hat{y} - y) x_j$$

ম্যাট্রিক্স ফর্মে পুরো ডেটাসেটের জন্য গ্রেডিয়েন্ট:
$$\nabla_{\mathbf{w}} J = \frac{1}{m} X^T (\mathbf{\hat{y}} - \mathbf{y})$$

---

## ৪.২ সাপোর্ট ভেক্টর মেশিন (Support Vector Machine - SVM)

### ৪.২.১ মার্জিনের জ্যামিতিক প্রমাণ
একটি ডিসিশন হাইপারপ্লেন: $\mathbf{w}^T \mathbf{x} + b = 0$।
যে কোনো বিন্দু $\mathbf{x}$ থেকে এই হাইপারপ্লেনের লম্ব দূরত্ব:
$$\text{Distance} = \frac{|\mathbf{w}^T \mathbf{x} + b|}{\|\mathbf{w}\|}$$

সাপোর্ট ভেক্টরগুলোর জন্য আমরা শর্ত নির্ধারণ করি: $|\mathbf{w}^T \mathbf{x} + b| = 1$।
অতএব, পজিটিভ এবং নেগেটিভ সাপোর্ট ভেক্টরের মধ্যকার মোট মার্জিনের প্রস্থ:
$$\text{Margin} = \frac{1}{\|\mathbf{w}\|} + \frac{1}{\|\mathbf{w}\|} = \frac{2}{\|\mathbf{w}\|}$$

মার্জিন সর্বোচ্চ (Maximize) করা মানে হলো $\|\mathbf{w}\|$ বা $\frac{1}{2}\|\mathbf{w}\|^2$ ন্যূনতম (Minimize) করা।

```
         Class +1 (+1 Boundary: w^T x + b = +1)
            /
           /  ▲
          /   │ Margin = 2 / ||w||
         /    ▼
        /  (-1 Boundary: w^T x + b = -1)
       /
      Class -1
```

### ৪.২.২ ডুয়াল ফর্মুলেশন ও KKT শর্তাবলী (Lagrangian Dual & KKT)
ল্যাগ্রাঞ্জ মাল্টিপ্লায়ার $\alpha_i \ge 0$ ব্যবহার করে প্রাইমাল সমস্যাকে ডুয়ালে রূপান্তর করা হয়:
$$L(\mathbf{w}, b, \boldsymbol{\alpha}) = \frac{1}{2}\|\mathbf{w}\|^2 - \sum_{i=1}^m \alpha_i \left[ y^{(i)}(\mathbf{w}^T \mathbf{x}^{(i)} + b) - 1 \right]$$

$\mathbf{w}$ এবং $b$-এর সাপেক্ষে ডেরিভেটিভ শূন্য বসালে পাওয়া যায়:
$$\mathbf{w} = \sum_{i=1}^m \alpha_i y^{(i)} \mathbf{x}^{(i)}, \quad \sum_{i=1}^m \alpha_i y^{(i)} = 0$$

এটি প্রতিস্থাপন করলে চূড়ান্ত **ওল্ফ ডুয়াল (Wolfe Dual Problem)** পাওয়া যায়:
$$\max_{\boldsymbol{\alpha}} \sum_{i=1}^m \alpha_i - \frac{1}{2} \sum_{i=1}^m \sum_{j=1}^m \alpha_i \alpha_j y^{(i)} y^{(j)} (\mathbf{x}^{(i)} \cdot \mathbf{x}^{(j)})$$
$$\text{subject to } 0 \le \alpha_i \le C \quad \text{and} \quad \sum_{i=1}^m \alpha_i y^{(i)} = 0$$

**KKT কমপ্লিমেন্টারি স্ল্যাকনেস:**
$$\alpha_i \left[ y^{(i)}(\mathbf{w}^T \mathbf{x}^{(i)} + b) - 1 + \xi_i \right] = 0$$
- যে ডেটা পয়েন্টগুলো মার্জিনের বাইরে সঠিকভাবে ক্লাসিফাইড, তাদের জন্য $\alpha_i = 0$।
- কেবল যে পয়েন্টগুলো মার্জিনের ওপর বা ভেতরে থাকে তাদের জন্য $\alpha_i > 0$। এরাই হলো **সাপোর্ট ভেক্টর (Support Vectors)**! বাকি সব পয়েন্ট ফেলে দিলেও মডেলের কোনো পরিবর্তন হয় না!

---

## ৪.৩ ডিসিশন ট্রি ও স্প্লিটিং মানদণ্ড (Decision Trees: Step-by-Step Numerical Example)

ধরি আমাদের একটি ছোট ডেটাসেট আছে যাতে ৪টি নমুনা রয়েছে:

| নমুনা | পড়াশোনার ঘণ্টা ($X$) | পাস করেছে কি না ($y$) |
| :---: | :---: | :---: |
| ১ | ১ ঘণ্টা | No (0) |
| ২ | ২ ঘণ্টা | No (0) |
| ৩ | ৪ ঘণ্টা | Yes (1) |
| ৪ | ৫ ঘণ্টা | Yes (1) |

### স্টেপ ১: মূল নোডের জিনি ইম্পিউরিটি (Parent Gini Impurity)
মোট স্যাম্পল ৪টি, যার মধ্যে ২টিতে No এবং ২টিতে Yes।
$$p_0 = \frac{2}{4} = 0.5, \quad p_1 = \frac{2}{4} = 0.5$$
$$\text{Gini}_{parent} = 1 - (p_0^2 + p_1^2) = 1 - (0.5^2 + 0.5^2) = 1 - 0.50 = 0.50$$

### স্টেপ ২: স্প্লিট পরীক্ষা ($X \le 3$ শর্তে)
- **বাম চাইল্ড ($X \le 3$):** নমুনা ১ ও ২ (উভয়ই No)।
  - $p_0 = 1.0, p_1 = 0$
  - $\text{Gini}_{left} = 1 - (1.0^2 + 0^2) = 0.0$ (নিখুঁত বিশুদ্ধ নোড!)
- **ডান চাইল্ড ($X > 3$):** নমুনা ৩ ও ৪ (উভয়ই Yes)।
  - $p_0 = 0, p_1 = 1.0$
  - $\text{Gini}_{right} = 1 - (0^2 + 1.0^2) = 0.0$ (নিখুঁত বিশুদ্ধ নোড!)

### স্টেপ ৩: ভারযুক্ত জিনি ইম্পিউরিটি (Weighted Gini Impurity)
$$\text{Gini}_{split} = \frac{2}{4} \times 0.0 + \frac{2}{4} \times 0.0 = 0.0$$
জিনি গেইন: $\Delta \text{Gini} = 0.50 - 0.0 = 0.50$ (সর্বোচ্চ অর্জন, তাই এই স্প্লিটটি নির্বাচিত হবে)।

---

## ৪.৪ স্ক্র্যাচ কোড: সম্পূর্ণ লজিস্টিক রিগ্রেশন ও কে-এন-এন (Pure NumPy)

```python
import numpy as np

class KNNClassifierScratch:
    def __init__(self, k=3):
        self.k = k
        self.X_train = None
        self.y_train = None

    def fit(self, X, y):
        self.X_train = np.array(X)
        self.y_train = np.array(y)

    def predict(self, X):
        predictions = [self._predict_single(x) for x in np.array(X)]
        return np.array(predictions)

    def _predict_single(self, x):
        # ১. ইউক্লিডিয়ান দূরত্ব নির্ণয়
        distances = np.linalg.norm(self.X_train - x, axis=1)
        # ২. নিকটতম k টি বিন্দুর সূচক বের করা
        k_indices = np.argsort(distances)[:self.k]
        # ৩. সংখ্যাগরিষ্ঠ ভোটের ভিত্তিতে লেবেল প্রদান
        k_nearest_labels = self.y_train[k_indices]
        unique_labels, counts = np.unique(k_nearest_labels, return_counts=True)
        return unique_labels[np.argmax(counts)]

# টেস্ট ড্রাইভ
if __name__ == "__main__":
    X_train = np.array([[1, 2], [2, 3], [3, 1], [6, 5], [7, 7], [8, 6]])
    y_train = np.array([0, 0, 0, 1, 1, 1])

    knn = KNNClassifierScratch(k=3)
    knn.fit(X_train, y_train)

    test_points = np.array([[1.5, 2.2], [7.5, 6.5]])
    print("KNN Predictions for test points:", knn.predict(test_points)) # [0, 1]
```
