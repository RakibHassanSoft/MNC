# NumPy মাস্টার নোট: মেশিন লার্নিংয়ের জন্য গাণিতিক কম্পিউটিং (NumPy Mastery)

> **"NumPy ছাড়া আধুনিক মেশিন লার্নিং বা ডিপ লার্নিংয়ের অস্তিত্ব কল্পনা করা যায় না। এটি C ভাষায় লিখিত অপটিমাইজড মেমরি বাফারের মাধ্যমে ভেক্টরাইজড ম্যাট্রিক্স গণনার সুপারপাওয়ার প্রদান করে।"**

---

## ১. কেন সাধারণ পাইথন লিস্ট নয়, NumPy Array?

সাধারণ পাইথন লিস্টে প্রতিটি উপাদান আলাদা পয়েন্টার এবং টাইপ অবজেক্ট হিসেবে মেমরিতে অসংলগ্নভাবে ছড়িয়ে থাকে (Overhead)। বিপরীতে, NumPy এর `ndarray` মেমরিতে একক অবিচ্ছিন্ন ব্লকে (Contiguous Memory Block) সমজাতীয় ডেটা (Homogeneous data) সংরক্ষণ করে এবং SIMD (Single Instruction, Multiple Data) প্রসেসর ইন্সট্রাকশন ব্যবহার করে গণনার গতি ১০০ গুণ পর্যন্ত বাড়িয়ে দেয়।

```
Python List:    [ Pointer1 ] ──► [ PyObject Int: 1 ]
                [ Pointer2 ] ──► [ PyObject Int: 2 ]
(মেমরিতে বিচ্ছিন্ন, ধীরগতির ক্যাশিং)

NumPy ndarray:  | 1 | 2 | 3 | 4 |  (একটানা মেমরি ব্লক, বিদ্যুৎগতির C-স্পিড)
```

---

## ২. অ্যারে তৈরি ও মৌলিক বৈশিষ্ট্য (Array Creation & Attributes)

```python
import numpy as np

# ১. বিভিন্ন উপায়ে অ্যারে তৈরি
a1 = np.array([1, 2, 3, 4], dtype=np.float32)       # ১-মাত্রিক ভেক্টর
a2 = np.zeros((3, 4))                                # ৩x৪ শূন্য ম্যাট্রিক্স
a3 = np.ones((2, 3), dtype=np.int32)                 # ২x৩ এক ম্যাট্রিক্স
a4 = np.eye(3)                                       # ৩x৩ আইডেন্টিটি ম্যাট্রিক্স (I)
a5 = np.arange(0, 10, 2)                             # [0, 2, 4, 6, 8]
a6 = np.linspace(0, 1, 5)                            # ০ থেকে ১ এর মধ্যে ৫টি সমান দূরত্বের বিন্দু

# গুরুত্বপূর্ণ বৈশিষ্ট্যসমূহ (Attributes)
print("Shape:", a2.shape)   # (3, 4)
print("Dimensions:", a2.ndim) # 2
print("Data Type:", a1.dtype) # float32
print("Total Elements:", a2.size) # 12
print("Item Size (bytes):", a1.itemsize) # 4 bytes
```

---

## ৩. ইনডেক্সিং, স্লাইসিং ও বুলিয়ান মাস্কিং (Indexing, Slicing & Masking)

মেশিন লার্নিংয়ে ফিচার ম্যাট্রিক্স $X$ এবং টার্গেট ভেক্টর $y$ আলাদা করতে স্লাইসিং অপরিহার্য:

```python
# ডেটাসেট ম্যাট্রিক্স: ৪টি স্যাম্পল, ৩টি ফিচার + ১টি টার্গেট
data = np.array([
    [10, 20, 30, 1],
    [40, 50, 60, 0],
    [70, 80, 90, 1],
    [15, 25, 35, 0]
])

# ফিচার X (সকল সারি, প্রথম ৩টি কলাম)
X = data[:, :-1]
# টার্গেট y (সকল সারি, শেষ কলাম)
y = data[:, -1]

print("Features X:\n", X)
print("Target y:\n", y)

# বুলিয়ান মাস্কিং (শর্তের ভিত্তিতে ফিল্টারিং)
# কেবল সেই স্যাম্পলগুলো নির্বাচন করা যাদের টার্গেট ১
positive_samples = X[y == 1]
print("Positive Class Samples:\n", positive_samples)

# নির্দিষ্ট সীমার মান ক্লিপ বা ফিল্টার করা
filtered_X = np.where(X > 40, X, 0) # ৪০ এর বড় মানগুলো রাখবে, বাকিগুলো ০ করবে
```

---

## ৪. ব্রডকাস্টিং নীতি (Broadcasting Rules in ML)

ব্রডকাস্টিং হলো মেমরি কপি না করেই ভিন্ন আকারের দুটি অ্যারের মধ্যে গাণিতিক অপারেশন চালানোর নিয়ম।
**ব্রডকাস্টিং নিয়ম:**
ডান দিক থেকে বাঁ দিকে মাত্রাগুলো মেলানো হয়। দুটি মাত্রা সামঞ্জস্যপূর্ণ হবে যদি:
১. মাত্রা দুটি হুবহু সমান হয়, অথবা
২. যেকোনো একটি মাত্রা **১** হয়।

```python
# মেশিন লার্নিং প্রয়োগ: ডেটা মিন-সেন্টারিং (Zero-centering)
X = np.array([
    [10.0, 200.0],
    [20.0, 400.0],
    [30.0, 600.0]
]) # Shape: (3, 2)

mean = np.mean(X, axis=0) # Shape: (2,) -> ফিচার অনুযায়ী গড় [20.0, 400.0]
# ব্রডকাস্টিং: (3, 2) থেকে (2,) বিয়োগ স্বয়ংক্রিয়ভাবে প্রতিটি সারির জন্য ঘটবে!
X_centered = X - mean
print("Zero-centered Data:\n", X_centered)
```

---

## ৫. রৈখিক বীজগণিত ও ম্যাট্রিক্স অপারেশন (`np.linalg`)

```python
A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])

# ১. ম্যাট্রিক্স গুণন (Dot Product / Matrix Multiplication)
# ভুল: A * B হলো এলিমেন্ট-ওয়াইজ গুণ!
C = np.dot(A, B)     # অথবা A @ B (আধুনিক পাইথন সিনট্যাক্স)
print("Matrix Product A @ B:\n", C)

# ২. ম্যাট্রিক্স ইনভার্স ও ডিটারমিন্যান্ট
inv_A = np.linalg.inv(A)
det_A = np.linalg.det(A)
print("Inverse of A:\n", inv_A)
print("Determinant of A:", det_A)

# ৩. নরমাল সমীকরণ সমাধান (OLS Linear Regression from scratch)
# y = X * theta => theta = (X^T X)^-1 X^T y
X = np.array([[1, 1], [1, 2], [1, 3]]) # বায়াস কলামসহ
y = np.array([2, 4, 5])
theta = np.linalg.inv(X.T @ X) @ X.T @ y
print("Calculated Parameters Theta [Bias, Weight]:", theta)

# ৪. আইগেনভ্যালু ও আইগেনভেক্টর (PCA এর ভিত্তি)
eigenvalues, eigenvectors = np.linalg.eig(A @ A.T)
print("Eigenvalues:", eigenvalues)
print("Eigenvectors:\n", eigenvectors)
```

---

## ⚡ গতি পরীক্ষা: পাইথন লুপ বনাম ভেক্টরাইজড NumPy

```python
import time

size = 10_000_000
list_a = list(range(size))
list_b = list(range(size))
arr_a = np.arange(size)
arr_b = np.arange(size)

# পাইথন লুপ
start = time.time()
list_result = [a + b for a, b in zip(list_a, list_b)]
loop_time = time.time() - start

# NumPy ভেক্টরাইজেশন
start = time.time()
arr_result = arr_a + arr_b
numpy_time = time.time() - start

print(f"Python Loop Time : {loop_time:.4f} seconds")
print(f"NumPy Vectorized : {numpy_time:.4f} seconds")
print(f"NumPy হলো {loop_time / numpy_time:.1f} গুণ দ্রুততর! 🚀")
```
