# ফেইজ ১: পাইথন ও সফটওয়্যার ইঞ্জিনিয়ারিং ভিত্তি (Python & Software Engineering Foundations for ML)

> *"Writing machine learning code in an isolated Jupyter notebook is only prototyping. Engineering production-ready, testable, maintainable, modular, and reproducible ML software requires solid software engineering foundations."*

এই হ্যান্ডবুকটি একজন পেশাদার মেশিন লার্নিং ইঞ্জিনিয়ারের জন্য প্রয়োজনীয় পাইথন প্রোগ্রামিং ও আধুনিক সফটওয়্যার ইঞ্জিনিয়ারিংয়ের গভীরতম ভিত্তিপ্রস্তর। এতে পাইথনের ইন্টারনাল মেমরি মডেল, অ্যাডভান্সড ফাংশনাল ও অবজেক্ট-ওরিয়েন্টেড ডিজাইন (OOP), মডার্ন টাইপ হিন্টস, টেস্টিং (`pytest`), স্ট্রাকচার্ড লগিং, লিনাক্স টার্মিনাল, গিট ওয়ার্কফ্লো এবং একটি সম্পূর্ণ প্যাকেজড সিএলআই প্রজেক্ট বিস্তারিতভাবে আলোচনা করা হয়েছে।

---

## 📑 সূচিপত্র
1. [পাইথন মেমরি মডেল ও কোর ডেটা স্ট্রাকচার](#১-পাইথন-মেমরি-মডেল-ও-কোর-ডেটা-স্ট্রাকচার)
   - ১.১ অবজেক্ট রেফারেন্স, আইডেন্টিটি (`id()`), Mutability ও Immutability
   - ১.২ Shallow Copy বনাম Deep Copy এবং এর গোপন বাগ
   - ১.৩ গার্বেজ কালেকশন: রেফারেন্স কাউন্টিং ও সাইক্লিক জিসি
   - ১.৪ লিস্ট, টিউপল, সেট ও ডিকশনারির ইন্টারনাল আর্কিটেকচার ও বিগ-ও ($O$)
   - ১.৫ কমপ্রিহেনশন ও জেনারেটর এক্সপ্রেশন মেমরি তুলনা
2. [অ্যাডভান্সড ফাংশনাল পাইথন](#২-অ্যাডভান্সড-ফাংশনাল-পাইথন)
   - ২.১ ফার্স্ট-ক্লাস সিটিজেন ও লেক্সিক্যাল ক্লোজার (Closures)
   - ২.২ ভ্যারিয়েবল স্কোপ ও LEGB রুল (`global` ও `nonlocal`)
   - ২.৩ আর্গুমেন্ট আনপ্যাকিং (`*args`, `**kwargs`, Keyword-Only)
   - ২.৪ ইটারেটর ও ইটারেবল প্রোটোকল (`__iter__`, `__next__`)
   - ২.৫ জেনারেটর ও মেমরি-সাশ্রয়ী স্ট্রিমিং (`yield`, `yield from`)
   - ২.৬ প্যারামিটারাইজড ডেকোরেটর ও মেটাডেটা প্রিজার্ভেশন (`functools.wraps`)
   - ২.৭ কনটেক্সট ম্যানেজার ও রিসোর্স লাইফসাইকেল (`__enter__`, `__exit__`)
3. [অবজেক্ট-ওরিয়েন্টেড প্রোগ্রামিং (OOP) ও টাইপ সিস্টেম](#৩-অবজেক্ট-ওরিয়েন্টেড-প্রোগ্রামিং-oop-ও-টাইপ-সিস্টেম)
   - ৩.১ ক্লাস ও ইনস্ট্যান্স মেকানিক্স, ডান্ডার / ম্যাজিক মেথডস
   - ৩.২ ইনহেরিটেন্স, MRO (C3 Linearization) ও `super()`
   - ৩.৩ অ্যাবস্ট্রাক্ট বেইজ ক্লাস (`abc.ABC`) ও Scikit-Learn স্টাইল ট্রান্সফরমার ডিজাইন
   - ৩.৪ এনক্যাপসুলেশন, প্রাইভেট অ্যাট্রিবিউট ও `@property`
   - ৩.৫ ডেটাক্লাস (`@dataclass`) বনাম Pydantic কনফিগারেশন
   - ৩.৬ মডার্ন টাইপ হিন্টস (`typing`, `Protocol`, `TypeVar`) ও `mypy` স্ট্যাটিক অ্যানালাইসিস
4. [প্রফেশনাল সফটওয়্যার ইঞ্জিনিয়ারিং প্র্যাকটিস](#৪-প্রফেশনাল-সフトওয়্যার-ইঞ্জিনিয়ারিং-প্র্যাকটিস)
   - ৪.১ প্রোডাকশন লগিং বনাম `print()`: হ্যান্ডলার, ফরম্যাটার ও স্ট্রাকচার্ড JSON লগ
   - ৪.২ এক্সেপশন হায়ারার্কি ও রেসিলিয়েন্ট এরর হ্যান্ডলিং (`raise ... from e`)
   - ৪.৩ ডিবাগিং মেকানিক্স: `pdb`, `breakpoint()` ও কল-স্ট্যাক ট্রেসিং
   - ৪.৪ কোড কোয়ালিটি ও স্ট্যান্ডার্ড: PEP 8, Linters (`ruff`, `black`), প্রি-কমিট হুকস
   - ৪.৫ ইউনিট টেস্টিং ও কোড কভারেজ (`pytest`, Fixtures, Mocking, Float Testing)
   - ৪.৬ পারফরম্যান্স প্রোফাইলিং: `cProfile`, মেমরি প্রফাইলিং ও অ্যালগরিদমিক অপটিমাইজেশন
5. [ডেভেলপার টুলস, টার্মিনাল ও পরিবেশ ব্যবস্থাপনা](#৫-ডেভেলপার-টুলস-টার্মিনাল-ও-পরিবেশ-ব্যবস্থাপনা)
   - ৫.১ লিনাক্স টার্মিনাল ও শেল মাস্টারি (`grep`, `awk`, `sed`, `find`, `xargs`, `top`)
   - ৫.২ জিপিইউ মনিটরিং (`nvidia-smi`) ও রিসোর্স ট্র্যাকিং
   - ৫.৩ গিট ও গিটহাব প্রফেশনাল ওয়ার্কফ্লো (অ্যাটোমিক কমিট, পিআর, Git LFS)
   - ৫.৪ ভার্চুয়াল এনভায়রনমেন্ট ও মডার্ন প্যাকেজ ম্যানেজার (`venv`, `poetry`, `uv`)
   - ৫.৫ সিক্রেটস ও এনভায়রনমেন্ট ভ্যারিয়েবলস (The 12-Factor App)
   - ৫.৬ নেটওয়ার্কিং ও HTTP/REST বেসিকস (GET, POST, Status Codes, JSON Contracts)
   - ৫.৭ সিড লক ও রিপ্রোডিউসিবল পাইপলাইন
6. [হাতে-কলমে ভেরিফিকেশন প্রজেক্ট: প্রোডাকশন CLI CSV ক্লিনার প্যাকেজ](#৬-হাতে-কলমে-ভেরিফিকেশন-প্রজেক্ট)
   - ৬.১ আর্কিটেকচার ও প্যাকেজ কাঠামো
   - ৬.২ সম্পূর্ণ সোর্স কোড (`pyproject.toml`, Core Module, CLI Handler)
   - ৬.৩ `pytest` সুইট ও টেস্ট ভ্যালিডেশন
   - ৬.৪ ইনস্টলেশন ও ব্যবহার নির্দেশিকা

---

# ১. পাইথন মেমরি মডেল ও কোর ডেটা স্ট্রাকচার

পাইথনে সবকিছুই একটি অবজেক্ট (Everything is an object)। CPython-এ প্রতিটি ভ্যারিয়েবল আসলে একটি মান নয়; এটি হিপ মেমরিতে থাকা একটি অবজেক্টের **পয়েন্টার বা রেফারেন্স**।

```
Python Reference Model:
   Variable Name (Stack)             Heap Memory (PyObject)
   ┌─────────┐                     ┌───────────────────────────────┐
   │    a    │ ──────────────────> │ ob_refcnt = 2                 │
   └─────────┘                     │ ob_type   = &PyLong_Type      │
   ┌─────────┐                     │ ob_ival   = 42 (Value)        │
   │    b    │ ──────────────────> └───────────────────────────────┘
   └─────────┘
   (Both 'a' and 'b' reference the EXACT same memory address!)
```

### ১.১ অবজেক্ট রেফারেন্স, আইডেন্টিটি (`id()`), Mutability ও Immutability
- **অপরিবর্তনশীল (Immutable):** `int`, `float`, `str`, `tuple`, `frozenset`। মান পরিবর্তন করতে গেলে পাইথন মেমরিতে সম্পূর্ণ নতুন অবজেক্ট তৈরি করে।
- **পরিবর্তনশীল (Mutable):** `list`, `dict`, `set`। ইন-প্লেস মান পরিবর্তন করা যায়, মেমরি অ্যাড্রেস অপরিবর্তিত থাকে।
- **`is` বনাম `==`:**
  - `==` অপারেটর চেক করে দুটি অবজেক্টের ভেতরের মান সমান কি না (`__eq__`)।
  - `is` অপারেটর চেক করে দুটি ভ্যারিয়েবল মেমরির হুবহু একই ঠিকানাকে নির্দেশ করছে কি না (`id(a) == id(b)`)।

```python
x = [1, 2, 3]
y = [1, 2, 3]
print(x == y)  # True (মান সমান)
print(x is y)  # False (মেমরিতে দুটি ভিন্ন লিস্ট অবজেক্ট)

# ইন্টিজার ক্যাশিং (Small Integer Caching: -5 থেকে 256)
a = 100
b = 100
print(a is b)  # True (CPython পারফরম্যান্সের স্বার্থে এগুলোকে প্রি-অ্যালোকেট করে রাখে)
```

---

### ১.২ Shallow Copy বনাম Deep Copy এবং এর গোপন বাগ
এমএল পাইপলাইনে ডেটা ট্রান্সফর্মেশনের সময় শ্যালো কপির কারণে মারাত্মক বাগ তৈরি হয়:

```python
import copy

original_dataset = [
    {"user_id": 1, "scores": [80, 90]},
    {"user_id": 2, "scores": [70, 85]}
]

# ১. সাধারণ অ্যাসাইনমেন্ট (শুধুমাত্র রেফারেন্স কপি)
ref_copy = original_dataset

# ২. শ্যালো কপি (বাহিরের লিস্ট কপি হয়, ভেতরের ডিকশনারি রেফারেন্স রয়ে যায়)
shallow = copy.copy(original_dataset)

# ৩. ডিপ কপি (ভেতরের সকল নেস্টেড অবজেক্ট মেমরিতে রিকার্সিভলি নতুন তৈরি হয়)
deep = copy.deepcopy(original_dataset)

# পরিবর্তন পরীক্ষা
shallow[0]["scores"].append(100)

print(original_dataset[0]["scores"]) # [80, 90, 100] -> শ্যালো কপির কারণে মূল ডেটা নষ্ট হয়ে গেছে!
print(deep[0]["scores"])             # [80, 90] -> ডিপ কপি সম্পূর্ণ সুরক্ষিত!
```

---

### ১.৩ গার্বেজ কালেকশন: রেফারেন্স কাউন্টিং ও সাইক্লিক জিসি
CPython দুটি কৌশলে অব্যবহৃত মেমরি মুক্ত করে:
1. **রেফারেন্স কাউন্টিং (Reference Counting):** প্রতিটি অবজেক্টের `ob_refcnt` ফিল্ড ট্র্যাক করে কয়টি ভ্যারিয়েবল তাকে পয়েন্ট করছে। রেফারেন্স কাউন্ট $0$ হলেই তৎক্ষণাৎ মেমরি রিলিজ হয়।
2. **সাইক্লিক গার্বেজ কালেক্টর (`gc` মডিউল):** দুটি অবজেক্ট যদি পরস্পরকে রেফার করে ($A \to B$ এবং $B \to A$), তবে তাদের বাইরে কোনো রেফারেন্স না থাকলেও রেফারেন্স কাউন্ট কখনো শূন্য হয় না। এই সাইকেল শনাক্ত করে মেমরি ফাঁকা করতে ব্যাকগ্রাউন্ডে ৩টি জেনারেশনাল সাইক্লিক কালেক্টর রান করে।

```python
import sys
import gc

a = [1, 2, 3]
print(sys.getrefcount(a) - 1)  # 1 (আসল রেফারেন্স)

b = a
print(sys.getrefcount(a) - 1)  # 2

del b
print(sys.getrefcount(a) - 1)  # 1
```

---

### ১.৪ লিস্ট, টিউপল, সেট ও ডিকশনারির ইন্টারনাল আর্কিটেকচার ও বিগ-ও ($O$)

| ডেটা স্ট্রাকচার | ইন্টারনাল আর্কিটেকচার | Access | Search | Append | Insert / Delete |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **`list`** | ডাইনামিক অ্যারে (Contiguous Pointers) | $O(1)$ | $O(n)$ | $O(1)$ amortized | $O(n)$ (Shifting লাগে) |
| **`tuple`** | ফিক্সড সাইজ অ্যারে (Immutable) | $O(1)$ | $O(n)$ | N/A | N/A |
| **`set`** | হ্যাশ টেবিল (Open Addressing) | N/A | $O(1)$ avg | $O(1)$ avg | $O(1)$ avg |
| **`dict`** | কমপ্যাক্ট হ্যাশ টেবিল (Indices + Entries) | $O(1)$ avg | $O(1)$ avg | $O(1)$ avg | $O(1)$ avg |

#### পাইথন লিস্টের ওভার-অ্যালোকেশন মেকানিক্স:
লিস্টে বারবার `append()` করলে যেন প্রতিবারে নতুন মেমরি কপি করতে না হয়, তাই পাইথন একটি বিশেষ সূত্রে বাড়তি মেমরি আগে থেকেই বরাদ্দ করে:
$$\text{allocated} \approx \text{newsize} + (\text{newsize} \gg 3) + (\text{newsize} < 9 \ ? \ 3 : 6)$$
গ্রোথ প্যাটার্ন: $0, 4, 8, 16, 25, 35, 46, \dots$

#### পাইথন ৩.৬+ কমপ্যাক্ট ডিকশনারি আর্কিটেকচার:
পূর্বে ডিকশনারি মেমরি অপচয় করত। আধুনিক পাইথনে এটি দুটি অ্যারেতে বিভক্ত:
```
Keys: Hash Table (Sparse Array of Indices)
indices = [None, 0, None, 1, None]

Entries: Dense Array (Stores Insertion Order!)
entries = [
    [-8529183, "name", "Antigravity"],
    [ 7201928, "role", "AI Assistant" ]
]
(এর ফলে মেমরি খরচ ২৫-৩০% হ্রাস পেয়েছে এবং ডিকশনারির অর্ডার সংরক্ষিত থাকে!)
```

---

### ১.৫ কমপ্রিহেনশন ও জেনারেটর এক্সপ্রেশন মেমরি তুলনা

```python
import sys

# লিস্ট কমপ্রিহেনশন: সাথে সাথে পুরো মেমরি অ্যালোকেট করে
large_list = [x ** 2 for x in range(1_000_000)]
print(f"লিস্ট মেমরি সাইজ: {sys.getsizeof(large_list) / (1024 * 1024):.2f} MB") # ~8.0 MB

# জেনারেটর এক্সপ্রেশন: অন-ডিমান্ড lazy evaluation
large_gen = (x ** 2 for x in range(1_000_000))
print(f"জেনারেটর মেমরি সাইজ: {sys.getsizeof(large_gen)} Bytes") # মাত্র 104 বা 200 Bytes!
```

---

# ২. অ্যাডভান্সড ফাংশনাল পাইথন

### ২.১ ফার্স্ট-ক্লাস সিটিজেন ও লেক্সিক্যাল ক্লোজার (Closures)
পাইথনে ফাংশন হলো অবজেক্ট। ফাংশনকে আর্গুমেন্ট হিসেবে পাস করা যায়, অন্য ফাংশন থেকে রিটার্ন করা যায় এবং ডেটা স্ট্রাকচারে রাখা যায়।

```python
def make_exponential_loss(power: float):
    """Outer Function: প্যারামিটার এনক্লোজ করে একটি ক্লোজার রিটার্ন করে"""
    def loss_function(y_true: float, y_pred: float) -> float:
        # 'power' ভ্যারিয়েবলটি ক্লোজারের এনক্লোজিং স্কোপ থেকে আবদ্ধ থাকে
        return abs(y_true - y_pred) ** power
    return loss_function

quadratic_loss = make_exponential_loss(power=2.0)
cubic_loss = make_exponential_loss(power=3.0)

print(quadratic_loss(y_true=10, y_pred=7)) # (3)^2 = 9.0
print(cubic_loss(y_true=10, y_pred=7))     # (3)^3 = 27.0
```

---

### ২.২ ভ্যারিয়েবল স্কোপ ও LEGB রুল
পাইথন ভ্যারিয়েবল খোঁজার জন্য চারটি ক্রমিক স্তরে অনুসন্ধান করে:
1. **L (Local):** ফাংশনের ভেতরে ডিফাইন করা।
2. **E (Enclosing):** বাইরের নেস্টেড ফাংশনে ডিফাইন করা।
3. **G (Global):** মডিউলের রুট লেভেলে ডিফাইন করা।
4. **B (Built-in):** পাইথনের বিল্ট-ইন নেমস্পেস (`len`, `range`, `ValueError`)।

- **`global`:** ফাংশনের ভেতর থেকে গ্লোবাল ভ্যারিয়েবল পরিবর্তন করতে।
- **`nonlocal`:** ক্লোজারের ভেতর থেকে এনক্লোজিং ফাংশনের ভ্যারিয়েবল মডিফাই করতে।

---

### ২.৩ আর্গুমেন্ট আনপ্যাকিং (`*args`, `**kwargs`, Keyword-Only)

```python
def configure_model(model_name: str, *layers: int, learning_rate: float = 0.001, **hyperparams):
    """
    model_name: পজিশনাল
    *layers: অনির্দিষ্ট সংখ্যক পজিশনাল আর্গুমেন্ট (Tuple)
    learning_rate: কি-ওয়ার্ড ডিফল্ট
    **hyperparams: অনির্দিষ্ট সংখ্যক কি-ওয়ার্ড আর্গুমেন্ট (Dict)
    """
    print(f"Model: {model_name}, Layers: {layers}, LR: {learning_rate}, Extra: {hyperparams}")

configure_model("MLP", 64, 32, 16, learning_rate=0.01, dropout=0.2, activation="relu")
```

---

### ২.৪ ইটারেটর ও ইটারেবল প্রোটোকল

```python
class BatchIterator:
    """স্ক্র্যাচ থেকে কাস্টম ML মিনি-ব্যাচ ইটারেটর প্রোটোকল"""
    def __init__(self, data, batch_size: int = 32):
        self.data = data
        self.batch_size = batch_size
        self.index = 0

    def __iter__(self):
        """ইটারেবল হতে হলে অবশ্যই একটি ইটারেটর রিটার্ন করতে হবে"""
        self.index = 0
        return self

    def __next__(self):
        """পরবর্তী ব্যাচ রিটার্ন করবে, শেষ হলে StopIteration রেজ করবে"""
        if self.index >= len(self.data):
            raise StopIteration
        batch = self.data[self.index : self.index + self.batch_size]
        self.index += self.batch_size
        return batch

# ব্যবহার
loader = BatchIterator(list(range(100)), batch_size=40)
for batch in loader:
    print(batch) # [0..39], [40..79], [80..99]
```

---

### ২.৫ জেনারেটর ও মেমরি-সাশ্রয়ী স্ট্রিমিং (`yield`, `yield from`)

```python
def stream_csv_chunks(filepath: str, chunk_size: int = 1000):
    """মেমরি ক্র্যাশ ছাড়া ১০ গিগাবাইটের ফাইল পড়ার জেনারেটর"""
    chunk = []
    with open(filepath, "r", encoding="utf-8") as f:
        header = f.readline().strip().split(",")
        for line in f:
            chunk.append(line.strip().split(","))
            if len(chunk) == chunk_size:
                yield header, chunk
                chunk = []
        if chunk:
            yield header, chunk
```

---

### ২.৬ প্যারামিটারাইজড ডেকোরেটর ও মেটাডেটা প্রিজার্ভেশন

```python
import functools
import time
import logging

def retry_on_exception(max_retries: int = 3, delay: float = 1.0):
    """প্যারামিটারাইজড রিট্রাই ডেকোরেটর (API বা ডাটাবেজ কানেকশনের জন্য)"""
    def decorator(func):
        @functools.wraps(func) # মূল ফাংশনের __name__ ও __doc__ অক্ষুণ্ণ রাখে
        def wrapper(*args, **kwargs):
            retries = 0
            while retries < max_retries:
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    retries += 1
                    logging.warning(f"[{func.__name__}] ব্যর্থ হয়েছে (চেষ্টা {retries}/{max_retries}): {e}")
                    time.sleep(delay)
            raise RuntimeError(f"[{func.__name__}] {max_retries} বার চেষ্টার পরেও চূড়ান্তভাবে ব্যর্থ!")
        return wrapper
    return decorator

@retry_on_exception(max_retries=3, delay=0.5)
def fetch_training_data_from_remote_api():
    # ডামি অস্থির রিমোট ফেচ
    raise ConnectionResetError("Remote server closed connection")
```

---

### ২.৭ কনটেক্সট ম্যানেজার ও রিসোর্স লাইফসাইকেল

```python
import time
from contextlib import contextmanager

@contextmanager
def execution_timer(task_name: str):
    """রিসোর্স বা টাইমার ট্র্যাকিং কনটেক্সট ম্যানেজার"""
    start_time = time.perf_counter()
    print(f"🚀 [{task_name}] শুরু হয়েছে...")
    try:
        yield
    finally:
        elapsed = time.perf_counter() - start_time
        print(f"⏱️ [{task_name}] সম্পন্ন হয়েছে: {elapsed:.4f} সেকেন্ড")

with execution_timer("Model Feature Extraction"):
    time.sleep(0.5) # ভারী গণনা
```

---

# ৩. অবজেক্ট-ওরিয়েন্টেড প্রোগ্রামিং (OOP) ও টাইপ সিস্টেম

### ৩.১ ডান্ডার / ম্যাজিক মেথডস ও Callable অবজেক্ট
PyTorch-এর `nn.Module`-এর মতো একটি অবজেক্টকে ফাংশনের মতো কল করতে `__call__` মেথড ব্যবহৃত হয়:

```python
class FeatureStandardizer:
    def __init__(self, mean: float, std: float):
        self.mean = mean
        self.std = std

    def __call__(self, x: float) -> float:
        """অবজেক্টকে সরাসরি ফাংশন হিসেবে এক্সিকিউট করতে দেয়: obj(x)"""
        return (x - self.mean) / (self.std + 1e-8)

    def __repr__(self) -> str:
        return f"FeatureStandardizer(mean={self.mean:.2f}, std={self.std:.2f})"

scaler = FeatureStandardizer(mean=50.0, std=10.0)
print(scaler(70.0))  # 2.0 (Callable magic!)
print(repr(scaler))  # FeatureStandardizer(mean=50.00, std=10.00)
```

---

### ৩.২ অ্যাবস্ট্রাক্ট বেইজ ক্লাস ও Scikit-Learn ট্রান্সফরমার আর্কিটেকচার

```python
from abc import ABC, abstractmethod
import numpy as np

class BaseMLTransformer(ABC):
    """অ্যাবস্ট্রাক্ট বেইজ ক্লাস: সব চাইল্ড ক্লাসে fit ও transform থাকা বাধ্যতামূলক করে"""
    
    @abstractmethod
    def fit(self, X: np.ndarray, y: np.ndarray = None) -> "BaseMLTransformer":
        """প্যারামিটার শেখে"""
        pass

    @abstractmethod
    def transform(self, X: np.ndarray) -> np.ndarray:
        """ডেটা ট্রান্সফর্ম করে"""
        pass

    def fit_transform(self, X: np.ndarray, y: np.ndarray = None) -> np.ndarray:
        return self.fit(X, y).transform(X)

class RobustMedianImputer(BaseMLTransformer):
    def __init__(self):
        self.medians_ = None

    def fit(self, X: np.ndarray, y: np.ndarray = None) -> "RobustMedianImputer":
        self.medians_ = np.nanmedian(X, axis=0)
        return self

    def transform(self, X: np.ndarray) -> np.ndarray:
        if self.medians_ is None:
            raise RuntimeError("আগে fit() চালাতে হবে!")
        X_out = X.copy()
        for col_idx in range(X_out.shape[1]):
            nan_mask = np.isnan(X_out[:, col_idx])
            X_out[nan_mask, col_idx] = self.medians_[col_idx]
        return X_out
```

---

### ৩.৩ ডেটাক্লাস (`@dataclass`) বনাম Pydantic
মডেল কনফিগারেশন ম্যানেজমেন্টে ডেটাক্লাস বয়লারপ্লেট কোড (যেমন `__init__`, `__repr__`, `__eq__`) স্বয়ংক্রিয়ভাবে তৈরি করে:

```python
from dataclasses import dataclass, field
from typing import List

@dataclass(frozen=True) # Immutable কনফিগারেশন
class ModelTrainingConfig:
    model_name: str
    learning_rate: float = 0.001
    batch_size: int = 64
    epochs: int = 100
    features: List[str] = field(default_factory=lambda: ["age", "income", "credit_score"])

config = ModelTrainingConfig(model_name="ResNet50")
print(config)
# config.learning_rate = 0.01  # FrozenInstanceError রেজ করবে! (কনফিগ দুর্ঘটনাবশত পরিবর্তন রোধ)
```

---

### ৩.৪ মডার্ন টাইপ হিন্টস ও `mypy` স্ট্যাটিক অ্যানালাইসিস

```python
from typing import Union, Optional, Callable, Dict, Any, List, Protocol

# টাইপ অ্যালিয়াস
Vector = List[float]
Matrix = List[Vector]

class PredictorProtocol(Protocol):
    """ডাকাতিপিংয়ের বদলে ইন্টারফেস প্রোটোকল ভ্যালিডেশন"""
    def predict(self, X: Matrix) -> Vector: ...

def evaluate_pipeline(
    model: PredictorProtocol,
    test_data: Matrix,
    metric_fn: Callable[[Vector, Vector], float],
    weights: Optional[Vector] = None
) -> float:
    predictions = model.predict(test_data)
    # mypy রান করলে টাইপ অসঙ্গতি তাৎক্ষণিক ধরা পড়ে
    return metric_fn([1.0, 0.0], predictions)
```

---

# ৪. প্রফেশনাল সফটওয়্যার ইঞ্জিনিয়ারিং প্র্যাকটিস

### ৪.১ প্রোডাকশন লগিং বনাম `print()`
প্রোডাকশন সিস্টেমে কনসোল `print()` ব্যবহার করা বড় অপরাধ। প্রোডাকশনে ফাইল রোটেটিং ও স্ট্রাকচার্ড JSON লগিং ব্যবহার করতে হয়:

```python
import logging
from logging.handlers import RotatingFileHandler
import json

class JSONFormatter(logging.Formatter):
    """ELK / CloudWatch-এর উপযোগী স্ট্রাকচার্ড JSON লগ ফরম্যাটার"""
    def format(self, record):
        log_obj = {
            "timestamp": self.formatTime(record, self.datefmt),
            "level": record.levelname,
            "module": record.module,
            "message": record.getMessage()
        }
        if record.exc_info:
            log_obj["exception"] = self.formatException(record.exc_info)
        return json.dumps(log_obj, ensure_ascii=False)

def setup_production_logger(name: str) -> logging.Logger:
    logger = logging.getLogger(name)
    logger.setLevel(logging.INFO)
    
    if not logger.handlers:
        # ১০ মেগাবাইট হলে নতুন ফাইল তৈরি করবে, সর্বোচ্চ ৫টি ফাইল রাখবে
        handler = RotatingFileHandler("pipeline.log", maxBytes=10*1024*1024, backupCount=5, encoding="utf-8")
        handler.setFormatter(JSONFormatter())
        logger.addHandler(handler)
        
        # কনসোল হ্যান্ডলার
        stream_handler = logging.StreamHandler()
        stream_handler.setFormatter(logging.Formatter("%(asctime)s [%(levelname)s]: %(message)s"))
        logger.addHandler(stream_handler)
    return logger

logger = setup_production_logger("MLPipeline")
logger.info("ফিচার নিষ্কাশন সফলভাবে সম্পন্ন হয়েছে।")
```

---

### ৪.২ এক্সেপশন হায়ারার্কি ও রেসিলিয়েন্ট এরর হ্যান্ডলিং

```python
class MLEngineeringException(Exception):
    """বেস কাস্টম এক্সেপশন"""
    pass

class DataSchemaValidationError(MLEngineeringException):
    """ইনপুট ডেটা স্কিমা অমিল সংক্রান্ত ত্রুটি"""
    pass

class ModelNotFittedError(MLEngineeringException):
    """আন-ফিটেড মডেল দিয়ে প্রেডিক্ট করার চেষ্টা"""
    pass

def safe_inference(model, input_matrix):
    try:
        return model.predict(input_matrix)
    except AttributeError as e:
        # Exception Chaining: আসল এরর ট্র্যাকিং বজায় রেখে অর্থবহ ডোমেন এক্সেপশন রেইজ
        raise ModelNotFittedError("মডেলটি পূর্বে ট্রেনিং করা হয়নি!") from e
```

---

### ৪.৩ ইউনিট টেস্টিং ও ফ্লোটিং পয়েন্ট ভ্যালিডেশন (`pytest`)

```python
# test_preprocessing.py
import pytest
import numpy as np

def test_imputer_handles_nans_correctly():
    imputer = RobustMedianImputer()
    X = np.array([
        [1.0, 10.0],
        [np.nan, 20.0],
        [3.0, np.nan]
    ])
    X_clean = imputer.fit_transform(X)
    
    # ন্যান চলে গেছে কি না নিশ্চিত করা
    assert not np.isnan(X_clean).any()
    # ফ্লোটিং পয়েন্ট সমতা যাচাই (np.allclose)
    np.testing.assert_allclose(X_clean[1, 0], 2.0, atol=1e-5)
    np.testing.assert_allclose(X_clean[2, 1], 15.0, atol=1e-5)

@pytest.mark.parametrize("invalid_input", [
    np.array([]),
    np.zeros((0, 5))
])
def test_imputer_raises_on_empty_data(invalid_input):
    imputer = RobustMedianImputer()
    with pytest.raises(Exception):
        imputer.fit(invalid_input)
```

---

# ৫. ডেভেলপার টুলস, টার্মিনাল ও পরিবেশ ব্যবস্থাপনা

### ৫.১ লিনাক্স টার্মিনাল ও ডেটা প্রসেসিং ব্যাশ কমান্ডস
একজন এমএল ইঞ্জিনিয়ারের সার্ভারে পাইথন ছাড়াই দ্রুত গিগাবাইট সাইজের ডেটা অডিট করতে টার্মিনাল কমান্ড জানা আবশ্যক:

```bash
# ১. বিশাল CSV ফাইলের প্রথম ১০টি লাইন ও হেডার দেখা
head -n 10 transactions.csv

# ২. মোট কতটি সারি আছে দ্রুত গণনা (WC = Word Count)
wc -l transactions.csv

# ৩. কলাম অনুযায়ী ইউনিক ক্যাটাগরি ফ্রিকোয়েন্সি বের করা (৩য় কলামের জন্য)
cut -d ',' -f 3 transactions.csv | sort | uniq -c | sort -nr | head -n 10

# ৪. কোনো শব্দ বা এরর লগ ফিল্টার করা
grep -E "ERROR|CRITICAL" pipeline.log | tail -n 20

# ৫. জিপিইউ ব্যবহার ও মেমরি পর্যবেক্ষণ
watch -n 1 nvidia-smi
```

---

### ৫.২ গিট ও গিটহাব প্রফেশনাল ওয়ার্কফ্লো
- **অ্যাটোমিক কমিট (Atomic Commits):** প্রতি কমিটে কেবল একটি নির্দিষ্ট কাজ (Feature বা Fix) অন্তর্ভুক্ত করা।
- **`.gitignore` ফর এমএল (ML Best Practice):** কখনোই বড় ডেটাসেট (`.csv`, `.parquet`) বা মডেল ওয়েটস (`.pt`, `.pkl`) সরাসরি গিট-এ পুশ করবেন না; এগুলো `DVC` বা ক্লাউড স্টোরেজে থাকবে।

```gitignore
# .gitignore
.venv/
__pycache__/
*.py[cod]
.pytest_cache/
.coverage
data/raw/
data/processed/
models/*.pkl
models/*.pt
.env
```

---

### ৫.৩ ভার্চুয়াল এনভায়রনমেন্ট ও মডার্ন প্যাকেজ ম্যানেজার
1. **`venv` (Standard Library):**
   ```bash
   python -m venv .venv
   source .venv/bin/activate       # Linux / Mac
   .venv\Scripts\activate          # Windows PowerShell
   ```
2. **`uv` (Next-Gen Ultra Fast):**
   রাস্ট-এ লেখা `uv` সাধারণ `pip`-এর চেয়ে ১০ থেকে ১০০ গুণ দ্রুত প্যাকেজ ইনস্টল ও রেজোলিউশন করে:
   ```bash
   uv pip install torch scikit-learn pandas
   ```

---

# 🛠️ ৬. হাতে-কলমে ভেরিফিকেশন প্রজেক্ট: প্রোডাকশন CLI CSV ক্লিনার প্যাকেজ

রোডম্যাপের ফেইজ ১-এর ভেরিফিকেশন শর্ত: *"Build a CSV-cleaning command-line application. Turn it into a small Python package; add unit tests and configuration via environment variables."*

নিচে এই রিকোয়ারমেন্টের শতভাগ অনুসরণ করে তৈরি একটি সম্পূর্ণ প্যাকেজের আর্কিটেকচার ও প্রোডাকশন কোড দেওয়া হলো:

```
csv_cleaner_pkg/
├── pyproject.toml              # Modern PEP 621 packaging metadata
├── README.md                   # Installation & usage documentation
├── .env.example                # Environment variable blueprint
├── src/
│   └── csv_cleaner/
│       ├── __init__.py         # Package exports
│       ├── core.py             # Pure cleaning and transformation logic
│       ├── cli.py              # Command-line interface with argparse
│       └── exceptions.py       # Custom exception hierarchies
└── tests/
    ├── __init__.py
    └── test_cleaner.py         # Pytest test suite with edge cases
```

---

### ৬.১ `pyproject.toml`
```toml
[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"

[project]
name = "csv-cleaner-cli"
version = "0.1.0"
description = "Professional production-grade CSV data cleaning tool for ML pipelines"
readme = "README.md"
requires-python = ">=3.9"
dependencies = [
    "pandas>=2.0.0",
    "numpy>=1.24.0",
    "python-dotenv>=1.0.0"
]

[project.scripts]
csv-clean = "csv_cleaner.cli:main"

[tool.pytest.ini_options]
testpaths = ["tests"]
```

---

### ৬.২ `src/csv_cleaner/exceptions.py`
```python
class CSVCleanerException(Exception):
    """Base exception for the CSV Cleaner application"""
    pass

class EmptyDataError(CSVCleanerException):
    """Raised when the input CSV has zero records"""
    pass

class InvalidStrategyError(CSVCleanerException):
    """Raised when an invalid imputation strategy is provided"""
    pass
```

---

### ৬.৩ `src/csv_cleaner/core.py`
```python
import logging
import os
import pandas as pd
import numpy as np
from typing import Tuple, Optional
from csv_cleaner.exceptions import EmptyDataError, InvalidStrategyError

logger = logging.getLogger("CSVCleaner.Core")

class DataFrameCleaner:
    """Core business logic for inspecting and sanitizing tabular datasets"""
    VALID_STRATEGIES = ("mean", "median", "mode", "drop")

    def __init__(self, strategy: str = "median", drop_na_threshold: float = 0.5):
        if strategy not in self.VALID_STRATEGIES:
            raise InvalidStrategyError(f"অকার্যকর স্ট্র্যাটেজি '{strategy}'. বৈধ অপশন: {self.VALID_STRATEGIES}")
        self.strategy = strategy
        self.drop_na_threshold = drop_na_threshold

    def clean(self, df: pd.DataFrame) -> Tuple[pd.DataFrame, dict]:
        """
        Executes cleaning pipeline:
        1. Duplicate removal
        2. High-null column dropping
        3. Numerical & Categorical imputation
        """
        if df.empty:
            raise EmptyDataError("ইনপুট ডেটাফ্রেমে কোনো রেকর্ড পাওয়া যায়নি!")

        stats = {
            "initial_rows": len(df),
            "initial_cols": len(df.columns),
            "duplicates_removed": 0,
            "columns_dropped": [],
            "imputed_columns": []
        }

        # ১. ডুপ্লিকেট সারি অপসারণ
        cleaned_df = df.drop_duplicates()
        stats["duplicates_removed"] = stats["initial_rows"] - len(cleaned_df)
        logger.info(f"ডুপ্লিকেট সরানো হয়েছে: {stats['duplicates_removed']} টি")

        # ২. অতিমাত্রায় ন্যান থাকা কলাম ড্রপ
        threshold_count = int((1.0 - self.drop_na_threshold) * len(cleaned_df))
        cols_before = set(cleaned_df.columns)
        cleaned_df = cleaned_df.dropna(axis=1, thresh=threshold_count)
        stats["columns_dropped"] = list(cols_before - set(cleaned_df.columns))
        if stats["columns_dropped"]:
            logger.warning(f"অতিরিক্ত মিসিং ডেটার কারণে কলাম ড্রপ হয়েছে: {stats['columns_dropped']}")

        # ৩. মিসিং ডেটা ইম্পিউটেশন
        for col in cleaned_df.columns:
            if cleaned_df[col].isnull().sum() > 0:
                stats["imputed_columns"].append(col)
                if pd.api.types.is_numeric_dtype(cleaned_df[col]):
                    if self.strategy == "mean":
                        fill_val = cleaned_df[col].mean()
                    elif self.strategy == "median":
                        fill_val = cleaned_df[col].median()
                    elif self.strategy == "drop":
                        cleaned_df = cleaned_df.dropna(subset=[col])
                        continue
                    cleaned_df[col] = cleaned_df[col].fillna(fill_val)
                    logger.info(f"নিউমেরিক্যাল কলাম '{col}' ফিল করা হয়েছে {self.strategy} ({fill_val:.2f}) দিয়ে।")
                else:
                    # ক্যাটেগরিক্যাল কলামে প্রচুরক (Mode) ইম্পিউটেশন
                    mode_val = cleaned_df[col].mode()
                    fill_val = mode_val[0] if not mode_val.empty else "Unknown"
                    cleaned_df[col] = cleaned_df[col].fillna(fill_val)
                    logger.info(f"ক্যাটেগরিক্যাল কলাম '{col}' ফিল করা হয়েছে Mode ('{fill_val}') দিয়ে।")

        stats["final_rows"] = len(cleaned_df)
        stats["final_cols"] = len(cleaned_df.columns)
        return cleaned_df, stats
```

---

### ৬.৪ `src/csv_cleaner/cli.py`
```python
import argparse
import os
import sys
import logging
import pandas as pd
from dotenv import load_dotenv
from csv_cleaner.core import DataFrameCleaner
from csv_cleaner.exceptions import CSVCleanerException

# এনভায়রনমেন্ট ভ্যারিয়েবল লোড করা (.env থেকে)
load_dotenv()

def setup_cli_logging(level_name: str):
    logging.basicConfig(
        level=getattr(logging, level_name.upper(), logging.INFO),
        format="%(asctime)s [%(levelname)s] [%(name)s]: %(message)s"
    )

def main():
    # এনভায়রনমেন্ট ভ্যারিয়েবল থেকে ডিফল্ট কনফিগ আনা (12-Factor Design)
    env_strategy = os.getenv("CSV_CLEANER_DEFAULT_STRATEGY", "median")
    env_log_level = os.getenv("CSV_CLEANER_LOG_LEVEL", "INFO")
    env_threshold = float(os.getenv("CSV_CLEANER_DROP_THRESHOLD", "0.5"))

    parser = argparse.ArgumentParser(
        description="🚀 Professional CSV Data Cleaning CLI Tool for Production ML Pipelines",
        formatter_class=argparse.ArgumentDefaultsHelpFormatter
    )
    parser.add_argument("-i", "--input", required=True, type=str, help="Input CSV file path")
    parser.add_argument("-o", "--output", required=True, type=str, help="Output cleaned CSV file path")
    parser.add_argument("-s", "--strategy", type=str, default=env_strategy, choices=["mean", "median", "drop"],
                        help="Imputation strategy for numerical missing values")
    parser.add_argument("-t", "--threshold", type=float, default=env_threshold,
                        help="Missing ratio threshold (0.0 to 1.0) above which columns are dropped")
    parser.add_argument("--log-level", type=str, default=env_log_level, choices=["DEBUG", "INFO", "WARNING", "ERROR"],
                        help="Application logging verbosity")

    args = parser.parse_args()
    setup_cli_logging(args.log_level)
    logger = logging.getLogger("CSVCleaner.CLI")

    if not os.path.exists(args.input):
        logger.error(f"❌ ইনপুট ফাইল পাওয়া যায়নি: {args.input}")
        sys.exit(1)

    try:
        logger.info(f"ফাইল পড়া শুরু হচ্ছে: {args.input}")
        raw_df = pd.read_csv(args.input)
        
        cleaner = DataFrameCleaner(strategy=args.strategy, drop_na_threshold=args.threshold)
        cleaned_df, stats = cleaner.clean(raw_df)

        # আউটপুট ডিরেক্টরি নিশ্চিত করা
        output_dir = os.path.dirname(args.output)
        if output_dir:
            os.makedirs(output_dir, exist_ok=True)

        cleaned_df.to_csv(args.output, index=False)
        logger.info(f"✅ ক্লিন ডেটা সফলভাবে সেভ হয়েছে: {args.output}")
        logger.info(f"📊 সামারি: {stats['initial_rows']} -> {stats['final_rows']} সারি | ডুপ্লিকেট বাদ: {stats['duplicates_removed']}")

    except CSVCleanerException as e:
        logger.error(f"⚠️ প্রসেসিং এরর: {e}")
        sys.exit(2)
    except Exception as e:
        logger.critical(f"💥 অপ্রত্যাশিত সিস্টেম ক্র্যাশ: {e}", exc_info=True)
        sys.exit(3)

if __name__ == "__main__":
    main()
```

---

### ৬.৫ `tests/test_cleaner.py`
```python
import pytest
import pandas as pd
import numpy as np
from csv_cleaner.core import DataFrameCleaner
from csv_cleaner.exceptions import EmptyDataError, InvalidStrategyError

@pytest.fixture
def sample_dirty_dataframe():
    """টেস্টিংয়ের জন্য ডার্টি ডেটাফ্রেমে ফিক্সচার"""
    return pd.DataFrame({
        "id": [1, 2, 2, 4, 5],                 # ডুপ্লিকেট আছে (id 2)
        "income": [50000, 60000, 60000, np.nan, 80000], # মিসিং আছে
        "all_null": [np.nan, np.nan, np.nan, np.nan, np.nan], # ১০০% মিসিং কলাম
        "city": ["Dhaka", "Sylhet", "Sylhet", np.nan, "Chittagong"]
    })

def test_duplicate_removal(sample_dirty_dataframe):
    cleaner = DataFrameCleaner(strategy="median", drop_na_threshold=0.5)
    cleaned, stats = cleaner.clean(sample_dirty_dataframe)
    
    assert stats["duplicates_removed"] == 1
    assert len(cleaned) == 4

def test_high_null_column_drop(sample_dirty_dataframe):
    cleaner = DataFrameCleaner(strategy="median", drop_na_threshold=0.5)
    cleaned, stats = cleaner.clean(sample_dirty_dataframe)
    
    assert "all_null" in stats["columns_dropped"]
    assert "all_null" not in cleaned.columns

def test_imputation_median(sample_dirty_dataframe):
    cleaner = DataFrameCleaner(strategy="median", drop_na_threshold=0.5)
    cleaned, stats = cleaner.clean(sample_dirty_dataframe)
    
    # 50000, 60000, 80000-এর মিডিয়ান হলো 60000
    assert cleaned["income"].isnull().sum() == 0
    assert (cleaned["income"] == 60000).sum() == 2 # আসল ১টি + ইম্পিউটেড ১টি

def test_empty_dataframe_raises_exception():
    cleaner = DataFrameCleaner()
    with pytest.raises(EmptyDataError):
        cleaner.clean(pd.DataFrame())

def test_invalid_strategy_raises_exception():
    with pytest.raises(InvalidStrategyError):
        DataFrameCleaner(strategy="quantum_magic")
```

---

### ৬.৬ ইনস্টলেশন ও ব্যবহার নির্দেশিকা

```bash
# ১. রিপোজিটরি ক্লোন করে ফোল্ডারে প্রবেশ
cd csv_cleaner_pkg

# ২. এডিটেবল মোডে প্যাকেজ ইনস্টলেশন
pip install -e .

# ৩. ইউনিট টেস্ট সুইট রান করা
pytest -v

# ৪. সিএলআই কমান্ড চালানো
csv-clean --input raw_data.csv --output clean_data.csv --strategy median --threshold 0.6
```

---
*রোডম্যাপ নেভিগেশন:* [👉 মূল মাস্টার সূচিপত্রে ফিরে যান](file:///g:/MNC/ML/README.md)
