# অধ্যায় ৩: রৈখিক মডেল ও রিগ্রেশন অ্যালগরিদম (Linear Models & Regression)

> *"All models are wrong, but some are useful." — George E. P. Box*

রিগ্রেশন অ্যানালাইসিস হলো মেশিন লার্নিংয়ের অন্যতম মূল ভিত্তি, যেখানে এক বা একাধিক স্বাধীন ইনপুট বৈশিষ্ট্যের (Independent Features) ওপর ভিত্তি করে একটি অবিচ্ছিন্ন লক্ষ্যমাত্রা (Continuous Target) অনুমান করা হয়।

---

## ৩.১ সাধারণ লিনিয়ার রিগ্রেশন (Ordinary Least Squares - OLS)

### ৩.১.১ গস-মারকভ উপপাদ্য ও লিনিয়ার রিগ্রেশনের পূর্বশর্ত (Assumptions of OLS)
গস-মারকভ উপপাদ্য (Gauss-Markov Theorem) অনুসারে, যদি নিম্নোক্ত শর্তগুলো পূরণ হয়, তবে OLS এস্টিমেটর হবে **BLUE (Best Linear Unbiased Estimator)**:

1. **প্যারামিটারে রৈখিকতা (Linearity):** টার্গেট $y$ এবং প্যারামিটার $\theta$-র মধ্যে সম্পর্ক রৈখিক হতে হবে:
   $$y = \theta_0 + \theta_1 x_1 + \dots + \theta_n x_n + \epsilon$$
2. **শূন্য শর্তাধীন গড় (Zero Conditional Mean of Errors):**
   $$\mathbb{E}[\epsilon | X] = 0$$
3. **হোমোস্কেডাস্টিসিটি (Homoscedasticity - সম-ভ্যারিয়্যান্স):** সকল ডেটা পয়েন্টের জন্য এররের ভ্যারিয়্যান্স ধ্রুবক হবে:
   $$\text{Var}(\epsilon_i | X) = \sigma^2, \quad \forall i$$
   *(যদি ভ্যারিয়্যান্স অসমান হয়, তাকে Heteroscedasticity বলে)*।
4. **কোনো স্ব-সম্পর্ক নেই (No Autocorrelation / Serial Independence):**
   $$\text{Cov}(\epsilon_i, \epsilon_j | X) = 0, \quad \forall i \neq j$$
5. **বহুরৈখিকতার অনুপস্থিতি (No Perfect Multicollinearity):** ম্যাট্রিক্স $X$-এর কলামগুলো লিনিয়ারলি ইন্ডিপেন্ডেন্ট হতে হবে ($\text{Rank}(X) = n+1$)।

---

### ৩.১.২ নরমাল সমীকরণের সম্পূর্ণ গাণিতিক প্রমাণ (Matrix Derivation of Normal Equation)

ধরি, $m$ সংখ্যক স্যাম্পল এবং $n$ সংখ্যক ফিচারবিশিষ্ট ডিজাইন ম্যাট্রিক্স $X \in \mathbb{R}^{m \times (n+1)}$ (যেখানে প্রথম কলাম বায়াসের জন্য $1$-এর ভেক্টর) এবং টার্গেট ভেক্টর $\mathbf{y} \in \mathbb{R}^m$।

মডেল প্রেডিকশন: $\hat{\mathbf{y}} = X\mathbf{\theta}$।
অবশিষ্ট বা এরর ভেক্টর: $\mathbf{e} = \mathbf{y} - X\mathbf{\theta}$।

কস্ট ফাংশন (Residual Sum of Squares - RSS):
$$J(\mathbf{\theta}) = \frac{1}{2} \mathbf{e}^T \mathbf{e} = \frac{1}{2} (\mathbf{y} - X\mathbf{\theta})^T (\mathbf{y} - X\mathbf{\theta})$$

বন্ধনী সম্প্রসারণ করি:
$$J(\mathbf{\theta}) = \frac{1}{2} \left( \mathbf{y}^T - \mathbf{\theta}^T X^T \right) (\mathbf{y} - X\mathbf{\theta})$$
$$J(\mathbf{\theta}) = \frac{1}{2} \left[ \mathbf{y}^T \mathbf{y} - \mathbf{y}^T X \mathbf{\theta} - \mathbf{\theta}^T X^T \mathbf{y} + \mathbf{\theta}^T X^T X \mathbf{\theta} \right]$$

যেহেতু $\mathbf{y}^T X \mathbf{\theta}$ একটি স্কেলার রাশি, এর ট্রান্সপোজ অপরিবর্তিত থাকে: $(\mathbf{y}^T X \mathbf{\theta})^T = \mathbf{\theta}^T X^T \mathbf{y}$।
সুতরাং:
$$J(\mathbf{\theta}) = \frac{1}{2} \left[ \mathbf{y}^T \mathbf{y} - 2 \mathbf{\theta}^T X^T \mathbf{y} + \mathbf{\theta}^T X^T X \mathbf{\theta} \right]$$

কস্ট ফাংশন ন্যূনতম করতে $\mathbf{\theta}$-র সাপেক্ষে ম্যাট্রিক্স ডেরিভেটিভ শূন্য ধরি:
$$\nabla_{\mathbf{\theta}} J(\mathbf{\theta}) = \frac{1}{2} \left[ 0 - 2 X^T \mathbf{y} + 2 X^T X \mathbf{\theta} \right] = \mathbf{0}$$
$$-X^T \mathbf{y} + X^T X \mathbf{\theta} = \mathbf{0}$$
$$X^T X \mathbf{\theta} = X^T \mathbf{y}$$

উভয়পাশে $(X^T X)^{-1}$ দ্বারা গুণ করে আমরা পাই চূড়ান্ত **নরমাল সমীকরণ (Normal Equation)**:
$$\mathbf{\theta}^* = (X^T X)^{-1} X^T \mathbf{y}$$

*(প্রমাণিত)*

---

### ৩.১.৩ হাতে-কলমে গাণিতিক সমাধান (Hand-calculated Example)
ধরি মাত্র ২টি ডেটা পয়েন্ট আছে:
- পয়েন্ট ১: $x = 1, y = 2$
- পয়েন্ট ২: $x = 2, y = 3$

ডিজাইন ম্যাট্রিক্স $X$ এবং টার্গেট $\mathbf{y}$:
$$X = \begin{bmatrix} 1 & 1 \\ 1 & 2 \end{bmatrix}, \quad \mathbf{y} = \begin{bmatrix} 2 \\ 3 \end{bmatrix}$$

১. $X^T X$ গণনা:
$$X^T X = \begin{bmatrix} 1 & 1 \\ 1 & 2 \end{bmatrix} \begin{bmatrix} 1 & 1 \\ 1 & 2 \end{bmatrix} = \begin{bmatrix} 1+1 & 1+2 \\ 1+2 & 1+4 \end{bmatrix} = \begin{bmatrix} 2 & 3 \\ 3 & 5 \end{bmatrix}$$

২. $(X^T X)^{-1}$ নির্ণয়:
$$\det(X^T X) = (2)(5) - (3)(3) = 10 - 9 = 1$$
$$(X^T X)^{-1} = \frac{1}{1} \begin{bmatrix} 5 & -3 \\ -3 & 2 \end{bmatrix} = \begin{bmatrix} 5 & -3 \\ -3 & 2 \end{bmatrix}$$

৩. $X^T \mathbf{y}$ গণনা:
$$X^T \mathbf{y} = \begin{bmatrix} 1 & 1 \\ 1 & 2 \end{bmatrix} \begin{bmatrix} 2 \\ 3 \end{bmatrix} = \begin{bmatrix} 2+3 \\ 2+6 \end{bmatrix} = \begin{bmatrix} 5 \\ 8 \end{bmatrix}$$

৪. $\mathbf{\theta}$ নির্ণয়:
$$\mathbf{\theta} = \begin{bmatrix} 5 & -3 \\ -3 & 2 \end{bmatrix} \begin{bmatrix} 5 \\ 8 \end{bmatrix} = \begin{bmatrix} 25 - 24 \\ -15 + 16 \end{bmatrix} = \begin{bmatrix} 1 \\ 1 \end{bmatrix}$$
অর্থাৎ বায়াস $\theta_0 = 1$ এবং স্লোপ $\theta_1 = 1$।
মডেলের সমীকরণ: $\hat{y} = 1 + 1 \cdot x$।
যাচাই: $x=1 \implies \hat{y} = 2$; $x=2 \implies \hat{y} = 3$ (নিখুঁত ফলাফল!)।

---

## ৩.২ অপটিমাইজেশন: গ্রেডিয়েন্ট ডিসেন্ট ভ্যারিয়্যান্টস

বড় ডেটাসেটে $(X^T X)^{-1}$ ম্যাট্রিক্স ইনভার্স হিসাবের জটিলতা $O(n^3)$। তাই লক্ষাধিক বৈশিষ্ট্যের জন্য গ্রেডিয়েন্ট ডিসেন্ট অপরিহার্য।

```
1. Batch GD:           সকল m ডেটার গড় গ্রেডিয়েন্ট নেয় (ধীরগতি, মসৃণ কনভার্জেন্স)
2. Stochastic GD (SGD): প্রতি ধাপে ১টি ডেটা নিয়ে আপডেট (দ্রুত, নয়েজি কনভার্জেন্স)
3. Mini-Batch GD:      ছোট ব্যাচ (৩২, ৬৪, ১২৮) নিয়ে আপডেট (সর্বোত্তম ব্যালেন্স)
```

আপডেট সমীকরণ:
$$\mathbf{\theta}_{t+1} = \mathbf{\theta}_t - \alpha \nabla J(\mathbf{\theta}_t)$$
যেখানে $\alpha$ হলো লার্নিং রেট।

---

## ৩.৩ রেগুলারাইজড লিনিয়ার মডেলস (Ridge, Lasso & ElasticNet)

যখন ডেটায় বহুরৈখিকতা (Multicollinearity) থাকে বা ফিচার সংখ্যা ডেটার চেয়ে বেশি হয় ($n > m$), তখন $X^T X$ ম্যাট্রিক্স সিঙ্গুলার বা প্রায়-সিঙ্গুলার হয়। এর ফলে প্যারামিটারগুলোর ভ্যারিয়্যান্স বিশাল হয়ে ওভারফিটিং ঘটে।

### ৩.৩.১ রিজ রিগ্রেশন (Ridge / $L_2$ Regularization)
কস্ট ফাংশন:
$$J_{Ridge}(\mathbf{\theta}) = \frac{1}{2m} \|X\mathbf{\theta} - \mathbf{y}\|_2^2 + \frac{\lambda}{2m} \|\mathbf{\theta}\|_2^2$$
নরমাল সমীকরণ:
$$\mathbf{\theta}_{Ridge} = (X^T X + \lambda I)^{-1} X^T \mathbf{y}$$

**কেন রিজ রিগ্রেশন ম্যাট্রিক্স ইনভার্টিবিলিটি নিশ্চিত করে?**
ম্যাট্রিক্স $X^T X$ হলো পজিটিভ সেমি-ডেফিনিট (আইগেনভ্যালু $\ge 0$)। এর সাথে $\lambda I$ যোগ করলে নতুন ম্যাট্রিক্সের প্রতিটি আইগেনভ্যালু $\lambda$ পরিমাণ বেড়ে যায়:
$$\lambda_i(X^T X + \lambda I) = \lambda_i(X^T X) + \lambda > 0 \quad (\text{if } \lambda > 0)$$
ফলে ম্যাট্রিক্সটি সর্বদা স্ট্রিক্টলি পজিটিভ ডেফিনিট এবং নিশ্চিতভাবে ইনভার্টিবল হয়!

### ৩.৩.২ ল্যাসো রিগ্রেশন (Lasso / $L_1$ Regularization)
কস্ট ফাংশন:
$$J_{Lasso}(\mathbf{\theta}) = \frac{1}{2m} \|X\mathbf{\theta} - \mathbf{y}\|_2^2 + \frac{\lambda}{m} \sum_{j=1}^n |\theta_j|$$

**কেন ল্যাসো ফিচার সিলেক্ট করে (ওয়েট শূন্য বানায়)?**
- $L_2$ পেনাল্টির কন্ট্যুর একটি বৃত্ত বা হাইপার-স্ফেয়ার ($\theta_1^2 + \theta_2^2 \le t$)। OLS উপবৃত্তাকার লস কনট্যুর যখন এই বৃত্তকে স্পর্শ করে, স্পর্শবিন্দু সাধারণত অক্ষের বাইরে কোনো স্থানে ঘটে।
- কিন্তু $L_1$ পেনাল্টির কন্ট্যুর হলো একটি ডায়মন্ড বা পলিটোপ ($|\theta_1| + |\theta_2| \le t$), যার কোণগুলো সরাসরি অক্ষের ওপর অবস্থিত ($\theta_1=0$ বা $\theta_2=0$)। OLS উপবৃত্তের এই ধারালো কোণে স্পর্শ করার সম্ভাব্যতা অত্যন্ত বেশি, ফলে অপ্রয়োজনীয় প্যারামিটার পুরোপুরি শূন্যে নেমে আসে!

---

## ৩.৪ স্ক্র্যাচ থেকে পাইথন ইমপ্লিমেন্টেশন (Linear, Ridge & Lasso)

```python
import numpy as np

class AdvancedLinearRegression:
    def __init__(self, method="normal", lr=0.01, epochs=1000, alpha=0.1, l1_ratio=0.0):
        self.method = method      # 'normal', 'gd', 'ridge'
        self.lr = lr
        self.epochs = epochs
        self.alpha = alpha        # রেগুলারাইজেশন স্ট্রেংথ λ
        self.l1_ratio = l1_ratio  # 0 = Ridge, 1 = Lasso
        self.theta = None

    def fit(self, X, y):
        m, n = X.shape
        # বায়াস টার্ম যোগ করা (X0 = 1)
        X_b = np.c_[np.ones((m, 1)), X]
        
        if self.method == "normal":
            # নরমাল সমীকরণ: θ = (X^T X)^-1 X^T y
            self.theta = np.linalg.pinv(X_b.T.dot(X_b)).dot(X_b.T).dot(y)
            
        elif self.method == "ridge":
            # রিজ সমীকরণ: θ = (X^T X + λI)^-1 X^T y (বায়াসে পেনাল্টি নেই)
            I = np.eye(n + 1)
            I[0, 0] = 0.0 # বায়াস টার্মে পেনাল্টি বাদ দেওয়া হলো
            self.theta = np.linalg.inv(X_b.T.dot(X_b) + self.alpha * I).dot(X_b.T).dot(y)
            
        elif self.method == "gd":
            # গ্রেডিয়েন্ট ডিসেন্ট
            self.theta = np.zeros(n + 1)
            for _ in range(self.epochs):
                gradients = (1 / m) * X_b.T.dot(X_b.dot(self.theta) - y)
                if self.alpha > 0: # L2 পেনাল্টি যোগ
                    reg_term = self.alpha * self.theta
                    reg_term[0] = 0.0
                    gradients += (1 / m) * reg_term
                self.theta -= self.lr * gradients

    def predict(self, X):
        m = X.shape[0]
        X_b = np.c_[np.ones((m, 1)), X]
        return X_b.dot(self.theta)

# ভেরিফিকেশন
if __name__ == "__main__":
    np.random.seed(42)
    X = 2 * np.random.rand(100, 1)
    y = 4 + 3 * X.squeeze() + np.random.randn(100) * 0.2

    # ১. নরমাল সমীকরণ
    model_normal = AdvancedLinearRegression(method="normal")
    model_normal.fit(X, y)
    print("Normal Eqn Theta [Bias, Slope]:", model_normal.theta)

    # ২. রিজ রিগ্রেশন
    model_ridge = AdvancedLinearRegression(method="ridge", alpha=1.0)
    model_ridge.fit(X, y)
    print("Ridge Theta [Bias, Slope]:", model_ridge.theta)
```
