# Phase 04: কনকারেন্সি, সিঙ্ক্রোনাইজেশন প্রিমিটিভস ও ক্লাসিক্যাল সমস্যা (Concurrency & Synchronization)

---

## ⚡ ১. রেস কন্ডিশন ও ক্রিটিক্যাল সেকশন সমস্যা (Critical Section Problem)

যখন একাধিক থ্রেড বা প্রসেস একই সময়ে শেয়ার্ড মেমরি বা ভেরিয়েবল রিড ও রাইট করে এবং চূড়ান্ত ফলাফল কোন অর্ডারে থ্রেডগুলো রান হয়েছে তার উপর নির্ভর করে, তখন তাকে **রেস কন্ডিশন (Race Condition)** বলে।

```c
// Example of Race Condition: counter++ compiled to 3 assembly instructions
mov eax, [counter]   // 1. Read memory into CPU register
add eax, 1           // 2. Increment register value
mov [counter], eax   // 3. Write register back to memory
```
যদি দুটি থ্রেড প্যারালালে এই কোড রান করে, তবে কনটেক্সট সুইচের কারণে চূড়ান্ত মান $2$ হওয়ার বদলে $1$ হয়ে ডেটা করাপশন ঘটতে পারে।

### ক্রিটিক্যাল সেকশন সমাধানের ৩টি আবশ্যিক শর্ত (Mandatory Criteria):
১. **Mutual Exclusion (পারস্পরিক বর্জন):** যদি কোনো প্রসেস তার ক্রিটিক্যাল সেকশনে এক্সিকিউট করে, তবে অন্য কোনো প্রসেস সেই সময়ে ক্রিটিক্যাল সেকশনে ঢুকতে পারবে না।
২. **Progress (অগ্রগতি):** ক্রিটিক্যাল সেকশন খালি থাকলে শুধুমাত্র যারা প্রবেশ করতে ইচ্ছুক তাদের মধ্য থেকেই পরবর্তী প্রসেস নির্বাচিত হবে, এবং এই সিদ্ধান্ত অনির্দিষ্টকাল আটকে থাকতে পারবে না।
৩. **Bounded Waiting (সীমাবদ্ধ অপেক্ষা):** একটি প্রসেস ক্রিটিক্যাল সেকশনে প্রবেশের অনুরোধ জানানোর পর অন্য প্রসেসগুলোকে কতবার প্রবেশ করার অনুমতি দেওয়া হবে তার একটি নির্দিষ্ট ঊর্ধ্বসীমা থাকতে হবে (Starvation রোধের জন্য)।

---

## 📜 ২. সফটওয়্যার সমাধান: পিটারসনের অ্যালগরিদম ও মেমরি ব্যারিয়ার

পিটারসনের অ্যালগরিদম (Peterson's Algorithm) দুটি প্রসেসের ($P_0, P_1$) জন্য একটি ক্লাসিক্যাল সফটওয়্যার সমাধান:

```c
// Shared variables
bool flag[2] = {false, false}; // flag[i] = true means Pi wants to enter
int turn;                      // Whose turn it is to enter

// Process P_i (where j is the other process: j = 1 - i)
void enter_critical_section(int i, int j) {
    flag[i] = true;            // I want to enter
    turn = j;                  // Generously give turn to the other process
    
    // Busy wait as long as the other wants to enter and it's their turn
    while (flag[j] && turn == j) {
        // Spin / Do nothing
    }
    
    // --- CRITICAL SECTION ---
    
    flag[i] = false;           // Exit section: I no longer need it
}
```

### ক. পিটারসনের অ্যালগরিদমের গাণিতিক সত্যতা প্রমাণ (Formal Proof)
- **Mutual Exclusion Proof:**
  ধরা যাক $P_0$ এবং $P_1$ উভয়েই একই সাথে ক্রিটিক্যাল সেকশনে ঢুকেছে।
  $P_0$ ক্রিটিক্যাল সেকশনে ঢুকতে পেরেছে মানে: `flag[1] == false` অথবা `turn == 0`।
  $P_1$ ক্রিটিক্যাল সেকশনে ঢুকতে পেরেছে মানে: `flag[0] == false` অথবা `turn == 1`।
  কিন্তু উভয়ে প্রবেশ করতে চাইলে `flag[0] == true` এবং `flag[1] == true` হতে হবে।
  ভেরিয়েবল `turn` একটি সিঙ্গেল মেমরি লোকেশন, যার মান একই সাথে $0$ এবং $1$ হওয়া অসম্ভব! সুতরাং উভয়ে একসাথে ঢুকতে পারে না। Mutual Exclusion প্রমাণিত।

### খ. আধুনিক মাল্টি-কোর সিপিইউতে পিটারসনের অ্যালগরিদম কেন ফেইল করে?
- **Out-of-Order Execution & Store Buffers:** আধুনিক প্রসেসর (যেমন Intel x86 বা ARM) পারফরম্যান্স বৃদ্ধির জন্য ইনস্ট্রাকশন রি-অর্ডার করে। কম্পাইলার এবং সিপিইউ দেখতে পায় যে `flag[i] = true` এবং `turn = j` দুটি স্বাধীন ভেরিয়েবল, তাই তারা মেমরি রাইট অপারেশন উল্টে দিতে পারে!
- **সমাধান - Memory Barrier:**
  আধুনিক মাল্টি-কোর হার্ডওয়্যারে সঠিক সিঙ্ক্রোনাইজেশনের জন্য হার্ডওয়্যার মেমরি ফেন্স (`mfence` বা `std::atomic_thread_fence`) ব্যবহার করা আবশ্যক:
  ```c
  flag[i].store(true, std::memory_order_release);
  turn.store(j, std::memory_order_seq_cst);
  ```

---

## 🔒 ৩. হার্ডওয়্যার অ্যাটমিক প্রিমিটিভস (Hardware Atomic Primitives)

আধুনিক ওএস কার্নেল এবং সিঙ্ক্রোনাইজেশন লাইব্রেরি হার্ডওয়্যার-লেভেল অ্যাটমিক ইন্সট্রাকশনের উপর প্রতিষ্ঠিত:

### ক. Compare-And-Swap (CAS)
x86-64-এ এটি `CMPXCHG` ইন্সট্রাকশন দ্বারা সম্পন্ন হয়:
```c
bool CAS(int *addr, int expected, int new_val) {
    // Atomically executed by hardware bus lock / cache coherency
    if (*addr == expected) {
        *addr = new_val;
        return true;
    }
    return false;
}
```
এটি লক-ফ্রি (Lock-free) ডেটা স্ট্রাকচার তৈরির মূল চালিকাশক্তি।

---

## 🚦 ৪. স্পিনলক বনাম মিউটেক্স বনাম সেমাফোর

```
+------------------------------------------------------------------------------------+
|                      SYNCHRONIZATION PRIMITIVES MATRIX                             |
+-------------------+-----------------------------+----------------------------------+
| বৈশিষ্ট্য           | স্পিনলক (Spinlock)           | মিউটেক্স (Mutex / Futex)          |
+-------------------+-----------------------------+----------------------------------+
| লক না পেলে আচরণ   | Busy Waiting (CPU লুপে ঘোরে)| প্রসেসকে Sleep/Block করে দেয়     |
| সিপিইউ কনজিউম     | ১০০% কোর ইউটিলাইজেশন করে     | শূন্য (প্রসেস স্লিপিং স্টেটে থাকে)|
| কনটেক্সট সুইচিং   | জিরো ওভারহেড                 | থ্রেড স্লিপ ও ওয়েকআপে ওভারহেড হয় |
| ব্যবহারের ক্ষেত্র| ক্রিটিক্যাল সেকশন অতি ক্ষুদ্র | ক্রিটিক্যাল সেকশন দীর্ঘস্থায়ী   |
| কার্নেল রুল       | Interrupt Handler-এ বাধ্যতামূলক| Interrupt Handler-এ স্লিপ নিষিদ্ধ|
+-------------------+-----------------------------+----------------------------------+
```

### সেমাফোর (Dijkstra's Semaphore)
সেমাফোর হলো একটি ইন্টিজার ভ্যারিয়েবল $S$ যা দুটি অ্যাটমিক অপারেশন দ্বারা নিয়ন্ত্রিত হয়:
- **`wait(S)` বা `P(S)` (Proberen - to test):**
  ```c
  void wait(Semaphore S) {
      S.value--;
      if (S.value < 0) {
          // Add this process to S.queue
          block();
      }
  }
  ```
- **`signal(S)` বা `V(S)` (Verhogen - to increment):**
  ```c
  void signal(Semaphore S) {
      S.value++;
      if (S.value <= 0) {
          // Remove a process P from S.queue
          wakeup(P);
      }
  }
  ```

---

## 🚀 ৫. লিনাক্স ফাস্ট ইউজারস্পেস মিউটেক্স (`futex`)

ঐতিহ্যগতভাবে প্রতিটি লক একোয়ার করতে সিস্টেম কল চালালে প্রতিবার কার্নেল মোড সুইচিংয়ের ল্যাটেন্সি হতো। লিনাক্স কার্নেল **`futex` (Fast Userspace Mutex)** প্রবর্তন করে যা আধুনিক `pthread_mutex` এর ভিত্তি:

```
[ THREAD TRIES TO LOCK MUTEX ]
               |
               v
  Atomic CAS in User Space (Zero Syscall Overhead)
               |
      +--------+--------+
      |                 |
(Success: Uncontended)  (Failed: Lock is held by another thread)
      |                 |
      v                 v
[ Acquired Lock ]     Syscall: futex(addr, FUTEX_WAIT, val, NULL)
 (10-15 nanoseconds)    |
                        v
                      Kernel puts thread to sleep on Wait Queue
                        |
                        v
                      [ Thread Awakened when Lock is released ]
```
- **Uncontended Case:** ৯৫% ক্ষেত্রে কোনো থ্রেড কনটেনশন থাকে না। ইউজার স্পেসের একটি অ্যাটমিক CAS ইন্সট্রাকশন দিয়েই লক পাওয়া যায়। সময় লাগে মাত্র **~১০ ন্যানোসেকেন্ড**!
- **Contended Case:** কেবল যখন লক ইতিমধ্যেই অন্য কেউ ধরে রেখেছে, তখনই `sys_futex()` সিস্টেম কল ডেকে থ্রেডটিকে কার্নেলের স্লিপ কিউতে ঘুম পাড়ানো হয়।

---

## 🧩 ৬. ক্লাসিক্যাল কনকারেন্সি সমস্যা ও সমাধান (Production-Grade C Code)

### ক. বাউন্ডেড বাফার (Producer-Consumer Problem)
প্রডিউসার ডেটা তৈরি করে বাফারে রাখে এবং কনজিউমার বাফার থেকে ডেটা প্রসেস করে।

```c
#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>
#include <unistd.h>

#define BUFFER_SIZE 5

int buffer[BUFFER_SIZE];
int in = 0, out = 0;

sem_t mutex; // Controls mutual exclusion to buffer
sem_t empty; // Counts empty buffer slots (Initially BUFFER_SIZE)
sem_t full;  // Counts filled buffer slots (Initially 0)

void* producer(void* arg) {
    int item = 1;
    while (1) {
        sem_wait(&empty); // Decrement empty slots (blocks if buffer is full)
        sem_wait(&mutex); // Enter critical section

        buffer[in] = item;
        printf("[Producer] Produced item %d at index %d\n", item, in);
        in = (in + 1) % BUFFER_SIZE;
        item++;

        sem_post(&mutex); // Exit critical section
        sem_post(&full);  // Increment filled slots (wakes up consumer)
        sleep(1);
    }
}

void* consumer(void* arg) {
    while (1) {
        sem_wait(&full);  // Decrement filled slots (blocks if buffer is empty)
        sem_wait(&mutex); // Enter critical section

        int item = buffer[out];
        printf("[Consumer] Consumed item %d from index %d\n", item, out);
        out = (out + 1) % BUFFER_SIZE;

        sem_post(&mutex); // Exit critical section
        sem_post(&empty); // Increment empty slots (wakes up producer)
        sleep(2);
    }
}
```

---

### খ. রিডার্স-রাইটার্স সমস্যা (Readers-Writers Problem - Starvation-Free Fair RWLock)
যদি রিডারদের অগ্রাধিকার দেওয়া হয়, তবে ক্রমাগত নতুন রিডার আসতে থাকলে রাইটাররা চিরতরে না খেয়ে থাকে (Writer Starvation)। নিচে একটি **Starvation-Free Fair Lock** আর্কিটেকচার দেওয়া হলো যেখানে একটি অতিরিক্ত কিউ সেমাফোর (`queue_turn`) ব্যবহার করে সবার ন্যায্যতা নিশ্চিত করা হয়:

```c
#include <pthread.h>
#include <semaphore.h>

sem_t resource_lock; // Protects data resource
sem_t read_count_lock; // Protects read_count variable
sem_t queue_turn;    // Enforces FIFO ordering to avoid starvation
int read_count = 0;

void reader(void) {
    sem_wait(&queue_turn);       // Wait in fair queue
    sem_wait(&read_count_lock);
    read_count++;
    if (read_count == 1) {
        sem_wait(&resource_lock); // First reader locks resource for writers
    }
    sem_post(&queue_turn);       // Let next waiter in queue enter
    sem_post(&read_count_lock);

    // --- READING DATA ---

    sem_wait(&read_count_lock);
    read_count--;
    if (read_count == 0) {
        sem_post(&resource_lock); // Last reader releases resource for writers
    }
    sem_post(&read_count_lock);
}

void writer(void) {
    sem_wait(&queue_turn);       // Wait in fair queue
    sem_wait(&resource_lock);    // Acquire exclusive access to resource
    sem_post(&queue_turn);       // Let next waiter proceed

    // --- WRITING DATA ---

    sem_post(&resource_lock);    // Release exclusive access
}
```

---

### গ. ডাইনিং ফিলোসফার্স সমস্যা (Dining Philosophers Problem)
৫ জন দার্শনিক গোল টেবিলে বসে আছেন। টেবিলের মাঝে ৫টি চপস্টিক আছে। প্রত্যেকে চিন্তা করেন এবং ক্ষুধা লাগলে বাম ও ডান পাশের দুটি চপস্টিক নিয়ে খাবার খান।
- **ডেডলকের কারণ:** সবাই একই সাথে ক্ষুধার্ত হয়ে যদি একযোগে বাম পাশের চপস্টিক তুলে নেয়, তবে ডান পাশের চপস্টিক কেউ খালি পাবে না এবং বৃত্তাকার অপেক্ষা (Circular Wait) তৈরি হয়ে ডেডলক হবে!
- **রিসোর্স হায়ারার্কি সমাধান (Dijkstra's Resource Ordering):**
  সবাই চপস্টিকগুলো ছোট নম্বর অনুযায়ী তুলবে:
  $$\text{Chopstick } i \text{ has order } i \quad (0 \le i \le 4)$$
  দার্শনিক $0, 1, 2, 3$ প্রথমে বামেরটি ($i$) ও পরে ডানেরটি ($i+1$) তুলবে। কিন্তু শেষ দার্শনিক ($P_4$) ডানেরটি ($0$) আগে তুলবে এবং বামেরটি ($4$) পরে তুলবে!
  এর ফলে বৃত্তাকার অপেক্ষার শর্ত ভেঙে যায় এবং গাণিতিকভাবে ডেডলক অসম্ভব হয়ে পড়ে।

---

## 🎯 ৭. শীর্ষ এমএনসি ইন্টারভিউ প্রশ্ন ও সমাধান (MNC Deep Dives)

### Q1: লিনাক্স কার্নেলে Interrupt Handler (Top Half)-এ মিউটেক্স (Mutex) লক ব্যবহার করা নিষিদ্ধ কেন?
**উত্তর:**
- ইন্টারাপ্ট হ্যান্ডলার কোনো প্রসেস বা থ্রেড কনটেক্সটে রান করে না; এটি একটি স্পেশাল **Interrupt Context**-এ চলে।
- মিউটেক্স যদি লকড থাকে, তবে থ্রেড স্লিপে যায় এবং কার্নেল শিডিউলার অন্য থ্রেডকে রান করায়। কিন্তু ইন্টারাপ্ট কনটেক্সটের কোনো নিজস্ব `task_struct` বা প্রসেস কন্ট্রোল ব্লক থাকে না যাকে স্লিপ কিউতে রাখা সম্ভব!
- ইন্টারাপ্ট হ্যান্ডলার স্লিপে যাওয়ার চেষ্টা করলে সিস্টেম তাৎক্ষণিকভাবে **Kernel Panic: scheduling while atomic** দিয়ে ক্র্যাশ করবে।
- তাই ইন্টারাপ্ট হ্যান্ডলারে কেবল **Spinlock (`spin_lock_irqsave`)** ব্যবহার বৈধ।

### Q2: কন্ডিশন ভেরিয়েবলে `pthread_cond_wait()` কেন সর্বদা `while` লুপের মধ্যে কল করতে হয়, `if` ব্লকে নয়?
**উত্তর:**
দুটি কারণে `while` লুপ বাধ্যতামূলক:
১. **Spurious Wakeup (ভুয়া জাগরণ):** অপারেটিং সিস্টেম ও মাল্টি-কোর হার্ডওয়্যারে কোনো সিগন্যাল ছাড়াই মাঝে মাঝে থ্রেড স্লিপ থেকে জেগে উঠতে পারে।
২. **Mesa-Style Semantics:** যখন কোনো থ্রেড `pthread_cond_signal()` চালায়, তখন অপেক্ষমান থ্রেডটি জেগে ওঠে ঠিকই, কিন্তু লক পুনরায় পাওয়ার আগেই অন্য একটি থ্রেড ঢুকে শর্তটি মিথ্যা করে দিতে পারে।
তাই জেগে ওঠার পর পুনরায় কন্ডিশনটি সত্য কি না তা যাচাই করার জন্য `while (condition == false)` আবশ্যক।