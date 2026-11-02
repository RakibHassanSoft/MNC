# অধ্যায় ৮: নিউরাল নেটওয়ার্ক ও ডিপ লার্নিংয়ের ভিত্তি (Neural Networks & Deep Learning)

> *"Deep Learning is essentially representation learning — finding hierarchical layers of features automatically from raw inputs."*

ডিপ লার্নিং হলো মেশিন লার্নিংয়ের সেই সাবফিল্ড যা জটিল, বহুস্তরীয় কৃত্রিম নিউরাল নেটওয়ার্ক (Artificial Neural Networks - ANN) ব্যবহার করে মানুষের মস্তিষ্ক যেভাবে শেখে সেই নীতিকে গাণিতিক মডেলে রূপান্তর করে।

---

## ৮.১ পারসেপ্ট্রন ও এক্স-অর (XOR) সমস্যা

১৯৫৮ সালে ফ্রাঙ্ক রোজেনব্ল্যাট (Frank Rosenblatt) প্রথম একক পারসেপ্ট্রন প্রস্তাব করেন।
$$y = f(\mathbf{w}^T \mathbf{x} + b)$$

### মিনস্কি ও পাপার্টের ঐতিহাসিক সমালোচনা (১৯৬৯)
একটি একক পারসেপ্ট্রন কেবল লিনিয়ারলি সেপারেবল (Linearly Separable) সমস্যা সমাধান করতে পারে (যেমন AND, OR গেট)। কিন্তু এটি সাধারণ **XOR গেট** সমাধান করতে পারে না, কারণ XOR ফাংশনের ট্রুথ টেবিল কোনো একক সরলরেখা দিয়ে ভাগ করা সম্ভব নয়।

```
    XOR Truth Table:
    x2 ▲
     1 │  ● (1)     ■ (0)
       │
     0 │  ■ (0)     ● (1)
       └────────────────► x1
          0         1
    (একটি সরলরেখা দিয়ে ● এবং ■ আলাদা করা অসম্ভব!)
```

**সমাধান:** বহুস্তরীয় পারসেপ্ট্রন (Multilayer Perceptron - MLP)। হিডেন লেয়ার (Hidden Layer) যুক্ত করলে ইনপুট স্পেস এমনভাবে রূপান্তরিত হয় যাতে নন-লিনিয়ার বাউন্ডারিও লিনিয়ারলি সেপারেবল হয়ে যায়।

---

## ৮.২ অ্যাক্টিভেশন ফাংশনসমূহ (Activation Functions)

যদি নিউরাল নেটওয়ার্কে নন-লিনিয়ার অ্যাক্টিভেশন না থাকে, তবে ১০০টি লেয়ার থাকলেও পুরো নেটওয়ার্কটি কেবল একটি সরল লিনিয়ার রিগ্রেশন $W_{net} = W_L \dots W_1$ ছাড়া কিছুই হবে না!

### ৮.২.১ সিগময়েড ও ভ্যানিশিং গ্রেডিয়েন্ট সমস্যা (Vanishing Gradient Problem)
$$\sigma(z) = \frac{1}{1 + e^{-z}}, \quad \sigma'(z) = \sigma(z)(1 - \sigma(z))$$
- $\sigma'(z)$-এর সর্বোচ্চ মান হলো মাত্র **০.২৫** (যখন $z = 0$)।
- ব্যাকপ্রোপাগেশনে যখন অনেকগুলো লেয়ার পার হয়ে গ্রেডিয়েন্ট আসে, তখন চেইন রুলে বারবার $0.25$ দিয়ে গুণ হতে হতে গভীর লেয়ারগুলোতে গ্রেডিয়েন্ট শূন্যে ($0$) পরিণত হয়। ফলে নেটওয়ার্ক আর শেখে না।

### ৮.২.২ আধুনিক অ্যাক্টিভেশন ফাংশনসমূহ
1. **ReLU (Rectified Linear Unit):**
   $$f(z) = \max(0, z), \quad f'(z) = \begin{cases} 1, & z > 0 \\ 0, & z < 0 \end{cases}$$
   - পজিটিভ অঞ্চলে গ্রেডিয়েন্ট সর্বদা ১, ফলে ভ্যানিশিং গ্রেডিয়েন্ট হয় না।
   - **Dying ReLU:** যদি কোনো নিউরনের ইনপুট স্থায়ীভাবে ঋণাত্মক হয়ে যায়, তবে গ্রেডিয়েন্ট ০ হয়ে নিউরনটি চিরতরে "মারা" যায়।
2. **Leaky ReLU:**
   $$f(z) = \max(\alpha z, z) \quad (\alpha \approx 0.01)$$
   - ঋণাত্মক মানগুলোতেও মৃদু গ্রেডিয়েন্ট বজায় রেখে নিউরনকে জীবিত রাখে।
3. **GELU (Gaussian Error Linear Unit):**
   আধুনিক ট্রান্সফরমার (BERT, GPT, LLaMA)-এ ব্যবহৃত হয়:
   $$\text{GELU}(z) = z \cdot \Phi(z) = z \cdot P(X \le z), \quad X \sim \mathcal{N}(0, 1)$$

---

## ৮.৩ ব্যাকপ্রোপাগেশনের পূর্ণাঙ্গ ম্যাট্রিক্স ক্যালকুলাস প্রতিপাদন (Matrix Backpropagation Derivation)

ধরি একটি $L$-লেয়ার নিউরাল নেটওয়ার্ক। যেকোনো লেয়ার $l$-এর জন্য:
- লিনিয়ার কম্বিনেশন: $Z^{[l]} = W^{[l]} A^{[l-1]} + \mathbf{b}^{[l]}$
- অ্যাক্টিভেশন: $A^{[l]} = g^{[l]}(Z^{[l]})$
- ইনপুট লেয়ার: $A^{[0]} = X$

ধরি আউটপুট লেয়ারে লস হলো $\mathcal{L}$। চেইন রুল প্রয়োগ করে ব্যাকওয়ার্ড পাস:

১. আউটপুট লেয়ারের এরর ($dZ^{[L]}$):
$$dZ^{[L]} = \frac{\partial \mathcal{L}}{\partial Z^{[L]}} = \frac{\partial \mathcal{L}}{\partial A^{[L]}} \odot g'^{[L]}(Z^{[L]})$$
*(লজিস্টিক রিগ্রেশন ও সফটম্যাক্সের ক্ষেত্রে এটি সরাসরি $A^{[L]} - Y$ হয়)*।

২. ওয়েটের গ্রেডিয়েন্ট ($dW^{[l]}$):
$$dW^{[l]} = \frac{\partial \mathcal{L}}{\partial W^{[l]}} = \frac{1}{m} dZ^{[l]} (A^{[l-1]})^T$$

৩. বায়াসের গ্রেডিয়েন্ট ($d\mathbf{b}^{[l]}$):
$$d\mathbf{b}^{[l]} = \frac{1}{m} \sum_{\text{columns}} dZ^{[l]}$$

৪. পূর্ববর্তী হিডেন লেয়ারের গ্রেডিয়েন্ট ($dZ^{[l-1]}$):
$$dA^{[l-1]} = (W^{[l]})^T dZ^{[l]}$$
$$dZ^{[l-1]} = dA^{[l-1]} \odot g'^{[l-1]}(Z^{[l-1]})$$

*(এই চারটি সমীকরণই হলো ডিপ লার্নিংয়ের সম্পূর্ণ ব্যাকপ্রোপাগেশন ইঞ্জিনের ভিত্তি!)*

---

## ৮.৪ আধুনিক অপটিমাইজার: অ্যাডাম (Adam Optimizer)

Adam হলো **Momentum** (প্রথম মোমেন্ট - গতির জড়তা) এবং **RMSProp** (দ্বিতীয় মোমেন্ট - লার্নিং রেট এডাপ্টেশন)-এর সংমিশ্রণ।

প্রতিটি স্টেপ $t$-এ গ্রেডিয়েন্ট $g_t = \nabla_\theta \mathcal{L}(\theta_t)$:
1. প্রথম মোমেন্ট (মুভিং এভারেজ অব গ্রেডিয়েন্ট):
   $$m_t = \beta_1 m_{t-1} + (1 - \beta_1) g_t$$
2. দ্বিতীয় মোমেন্ট (মুভিং এভারেজ অব গ্রেডিয়েন্ট স্কয়ার):
   $$v_t = \beta_2 v_{t-1} + (1 - \beta_2) g_t^2$$
3. বায়াস কারেকশন (শুরুর দিকের শূন্য পক্ষপাত দূরীকরণ):
   $$\hat{m}_t = \frac{m_t}{1 - \beta_1^t}, \quad \hat{v}_t = \frac{v_t}{1 - \beta_2^t}$$
4. প্যারামিটার আপডেট:
   $$\theta_{t+1} = \theta_t - \frac{\alpha}{\sqrt{\hat{v}_t} + \epsilon} \hat{m}_t$$
*(ডিফল্ট হাইপারপ্যারামিটার: $\beta_1 = 0.9, \beta_2 = 0.999, \epsilon = 10^{-8}, \alpha = 0.001$)*।

---

## ৮.৫ স্ক্র্যাচ কোড: সম্পূর্ণ ভেক্টরাইজড ২-লেয়ার নিউরাল নেটওয়ার্ক (Pure NumPy)

```python
import numpy as np

class TwoLayerNeuralNetworkScratch:
    def __init__(self, n_x, n_h, n_y, learning_rate=0.1):
        # n_x: ইনপুট ফিচার, n_h: হিডেন নিউরন, n_y: আউটপুট সাইজ
        np.random.seed(42)
        self.lr = learning_rate
        # He / Xavier ইনিশিয়ালাইজেশন
        self.W1 = np.random.randn(n_h, n_x) * np.sqrt(2.0 / n_x)
        self.b1 = np.zeros((n_h, 1))
        self.W2 = np.random.randn(n_y, n_h) * np.sqrt(1.0 / n_h)
        self.b2 = np.zeros((n_y, 1))

    def _relu(self, Z):
        return np.maximum(0, Z)

    def _relu_derivative(self, Z):
        return (Z > 0).astype(float)

    def _sigmoid(self, Z):
        return 1.0 / (1.0 + np.exp(-np.clip(Z, -250, 250)))

    def forward(self, X):
        # Forward pass
        self.Z1 = np.dot(self.W1, X) + self.b1
        self.A1 = self._relu(self.Z1)
        self.Z2 = np.dot(self.W2, self.A1) + self.b2
        self.A2 = self._sigmoid(self.Z2)
        return self.A2

    def backward(self, X, Y):
        m = X.shape[1]
        # আউটপুট লেয়ারের এরর (Cross-Entropy with Sigmoid)
        dZ2 = self.A2 - Y
        dW2 = (1.0 / m) * np.dot(dZ2, self.A1.T)
        db2 = (1.0 / m) * np.sum(dZ2, axis=1, keepdims=True)

        # হিডেন লেয়ারের এরর
        dA1 = np.dot(self.W2.T, dZ2)
        dZ1 = dA1 * self._relu_derivative(self.Z1)
        dW1 = (1.0 / m) * np.dot(dZ1, X.T)
        db1 = (1.0 / m) * np.sum(dZ1, axis=1, keepdims=True)

        # প্যারামিটার আপডেট
        self.W1 -= self.lr * dW1
        self.b1 -= self.lr * db1
        self.W2 -= self.lr * dW2
        self.b2 -= self.lr * db2

    def train(self, X, Y, epochs=1000):
        for epoch in range(epochs):
            A2 = self.forward(X)
            cost = -np.mean(Y * np.log(A2 + 1e-8) + (1 - Y) * np.log(1 - A2 + 1e-8))
            self.backward(X, Y)
            if epoch % 200 == 0:
                print(f"Epoch {epoch}: Binary Cross-Entropy Cost = {cost:.4f}")

# টেস্ট ড্রাইভ (XOR সমস্যা সমাধান)
if __name__ == "__main__":
    # 4টি ইনপুট স্যাম্পল (ফিচার=2, স্যাম্পল=4)
    X = np.array([[0, 0, 1, 1],
                  [0, 1, 0, 1]])
    Y = np.array([[0, 1, 1, 0]]) # XOR লেবেল

    nn = TwoLayerNeuralNetworkScratch(n_x=2, n_h=4, n_y=1, learning_rate=0.5)
    nn.train(X, Y, epochs=1001)

    predictions = (nn.forward(X) > 0.5).astype(int)
    print("XOR Ground Truth:", Y)
    print("XOR Predictions :", predictions) # নিখুঁত [0, 1, 1, 0]
```
