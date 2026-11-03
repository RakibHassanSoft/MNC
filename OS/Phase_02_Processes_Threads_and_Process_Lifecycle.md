# Phase 02: প্রসেস, থ্রেড, লাইফসাইকেল ও ইন্টার-প্রসেস কমিউনিকেশন (Processes, Threads & IPC)

---

## 🧠 ১. প্রসেস অ্যাবস্ট্রাকশন ও ভার্চুয়াল অ্যাড্রেস স্পেস (Virtual Address Space Layout)

একটি প্রসেস হলো এক্সিকিউশনরত একটি প্রোগ্রাম (Program in Execution)। এটি শুধুমাত্র কোড নয়, বরং এর সাথে মেমরি স্পেস, রেজিস্টার ভ্যালু, ফাইল ডিসক্রিপ্টর এবং কার্নেল স্টেট যুক্ত থাকে।

আধুনিক লিনাক্স x86-64 সিস্টেমে প্রতিটি ইউজার প্রসেসের জন্য একটি স্বতন্ত্র **৪৮-বিট ক্যানোনিকাল ভার্চুয়াল অ্যাড্রেস স্পেস (128 Terabyte User Space + 128 Terabyte Kernel Space)** বরাদ্দ থাকে:

```
0xFFFFFFFFFFFFFFFF +---------------------------------------------------+
                   |         Kernel Space (128 TB)                     |
                   |  - Process-specific kernel stack (8KB - 16KB)     |
                   |  - Physical memory mapping, Kernel code, Drivers  |
0xFFFF800000000000 +---------------------------------------------------+
                   |          Non-Canonical Address Hole (16 EB)       |
                   |   (Unmapped, triggers #GP if accessed)            |
0x00007FFFFFFFFFFF +---------------------------------------------------+
                   |  Stack (grows downward vvv)                       |
                   |  - Function call frames, local variables, %rbp    |
                   +---------------------------------------------------+
                   |                 |                                 |
                   |                 v                                 |
                   |                                                   |
                   |                 ^                                 |
                   |                 |                                 |
                   +---------------------------------------------------+
                   |  Memory Mapping Segment (mmap, shared libs, .so)  |
                   +---------------------------------------------------+
                   |                 ^                                 |
                   |                 |                                 |
                   |  Heap (grows upward ^^^ via brk / sbrk)           |
                   |  - Dynamic memory (malloc, calloc, new)           |
                   +---------------------------------------------------+
                   |  BSS Segment (.bss) - Uninitialized Global/Static |
                   |  - Zero-initialized on demand by kernel           |
                   +---------------------------------------------------+
                   |  Data Segment (.data) - Initialized Global/Static |
                   +---------------------------------------------------+
                   |  Text Segment (.text) - Executable Machine Code   |
                   |  - Read-Only, Shared across multiple instances    |
0x0000000000400000 +---------------------------------------------------+
                   |  Reserved (Trap null-pointer dereference)         |
0x0000000000000000 +---------------------------------------------------+
```

### মেমরি সেগমেন্টসমূহের নিখুঁত কারিগরি ভূমিকা:
১. **Reserved Area (Zero Page):** `0x0` থেকে শুরু করে প্রথম পেজগুলো আনম্যাপড থাকে। ফলে কোডে যদি কেউ `NULL` পয়েন্টার ডিরিফারেন্স (`*ptr = 5` যেখানে `ptr == NULL`) করে, সাথে সাথে সিপিইউ পেজ ফল্ট ট্র্যাপ তৈরি করে এবং কার্নেল প্রসেসটিকে `SIGSEGV` (Segmentation Fault) দিয়ে কিল করে।
২. **Text Segment:** কম্পাইল্ড বাইনারি কোড ধারণ করে। এটি **Read-Only** এবং **Executable** (`R-X`)।同一个 বাইনারি (যেমন `/bin/bash` বা `python3`) ১০টি প্রসেস চালালে ফিজিক্যাল মেমরিতে টেক্সট সেগমেন্ট মাত্র একবারই লোড হয় এবং সবার মধ্যে শেয়ার্ড থাকে।
৩. **Data Segment (.data):** ইনিশিয়ালাইজড গ্লোবাল ও স্ট্যাটিক ভেরিয়েবল (যেমন: `int count = 100;`)। এটি **Read-Write** (`RW-`)।
৪. **BSS Segment (.bss - Block Started by Symbol):** যেসব গ্লোবাল ভেরিয়েবলের কোনো ইনিশিয়াল মান দেওয়া হয়নি (যেমন: `static char buffer[4096];`)। ডিস্কে বাইনারির সাইজ বাঁচাতে বাইনারি ফাইলে কোনো জিরো স্টোর করা হয় না; কার্নেল এক্সিকিউশনের সময় এই সেগমেন্টকে জিরো দিয়ে পেজ ম্যাপ করে দেয়।
৫. **Heap:** রানটাইমে ডাইনামিকালি মেমরি বরাদ্দের জন্য ব্যবহৃত হয় (`malloc()`, `new`)। এটি নিচ থেকে উপরের দিকে বাড়ে (`brk()` পয়েন্টার বৃদ্ধি পায়)।
৬. **Memory Mapping Segment:** ডায়নামিক লাইব্রেরি (`libc.so`), শেয়ার্ড মেমরি এবং বড় সাইজের `malloc` অ্যালোকেশন (`mmap` এর মাধ্যমে) এখানে স্থান পায়।
৭. **Stack:** ফাংশন কল ফ্রেম, রিটার্ন অ্যাড্রেস, এবং লোকাল ভেরিয়েবল ধারণ করে। এটি উপর থেকে নিচের দিকে বাড়ে (`%rsp` কমে)।

---

## 🗃️ ২. প্রসেস কন্ট্রোল ব্লক ও লিনাক্স `task_struct`

লিনাক্স কার্নেলে প্রতিটি প্রসেস ও থ্রেড একটি বিশদ ডেটা স্ট্রাকচার দ্বারা প্রকাশিত হয় যার নাম `struct task_struct` (যা `<linux/sched.h>`-এ সংজ্ঞায়িত):

```c
struct task_struct {
    volatile long state;               // -1 unrunnable, 0 runnable, >0 stopped
    void *stack;                       // Points to process kernel stack
    pid_t pid;                         // Process ID (unique per thread/LWP)
    pid_t tgid;                        // Thread Group ID (Process ID seen by user)
    
    struct task_struct *parent;        // Parent process pointer
    struct list_head children;         // List of child processes
    
    struct mm_struct *mm;              // Virtual Memory Descriptor (Page tables, segments)
    struct files_struct *files;        // Open File Descriptors Table (fd table)
    struct fs_struct *fs;              // Current working directory & root path
    struct signal_struct *signal;      // Signal handlers & pending signals
    
    struct sched_entity se;            // CFS Scheduling entity (vruntime, weight)
    int prio, static_prio, normal_prio;// Priorities (-20 to 19)
    cpumask_t cpus_allowed;            // CPU Affinity mask
};
```

> **MNC Interview Distinction: `pid` বনাম `tgid`:**
> ইউজার স্পেসে আমরা যাকে প্রসেস আইডি (PID) বলি (যেমন `getpid()`), লিনাক্স কার্নেলের ভেতরে সেটি আসলে **`tgid` (Thread Group ID)**!
> একটি মাল্টি-থ্রেডেড প্রসেসের প্রতিটি থ্রেডের নিজস্ব স্বতন্ত্র কার্নেল `pid` (Light-Weight Process ID) থাকে, কিন্তু সবার `tgid` থাকে একই (প্রধান প্রসেসের `pid`)।

### প্রসেসের স্টেট ট্রানজিশন ডায়াগ্রাম (Process State Lifecycle)

```
                       [ New ]
                          |
                          | (Admitted by fork/clone)
                          v
            +-------->[ Ready ]<----------+
            |             |               |
            |             | (Scheduler    | (I/O Completed /
 (Preemption/             |  Dispatch)    |  Event Arrived)
  Time Slice Expired)     v               |
            +--------- [ Running ]        |
                          |               |
                          | (Wait for I/O)|
                          v               |
                     [ Waiting ]----------+
               (TASK_INTERRUPTIBLE /
                TASK_UNINTERRUPTIBLE)
                          |
                          | (Process Exits)
                          v
                     [ Zombie ] (EXIT_ZOMBIE)
                          |
                          | (Parent calls waitpid())
                          v
                    [ Terminated ]
```

- **`TASK_RUNNING`:** সিপিইউতে চলছে অথবা রান-কিউতে শিডিউলারের জন্য অপেক্ষা করছে।
- **`TASK_INTERRUPTIBLE` (S state):** কোনো রিসোর্স বা সিগন্যালের জন্য ওয়েট করছে। সিগন্যাল (যেমন `SIGINT`) এলে সাথে সাথে জেগে ওঠে।
- **`TASK_UNINTERRUPTIBLE` (D state):** সাধারণত ডিস্ক আই/ও বা হার্ডওয়্যার অপারেশনের জন্য ওয়েট করছে। **কোনো সিগন্যাল (`kill -9` সহ) এটিকে ইন্টারাপ্ট করতে পারে না!** যদি হার্ডওয়্যার রেসপন্স না করে, প্রসেসটি চিরতরে D স্টেটে আটকে থাকে।
- **`EXIT_ZOMBIE` (Z state):** প্রসেস এক্সিট করেছে, মেমরি ও ফাইল রিলিজ হয়েছে, কিন্তু প্রসেসের এক্সিট স্ট্যাটাস ধারণকারী `task_struct` এখনো কার্নেলে আছে কারণ প্যারেন্ট এখনো `wait()` কল করেনি।

---

## ⚡ ৩. প্রসেস লাইফসাইকেল ও কপি-অন-রাইট (Copy-on-Write - COW)

লিনাক্সে নতুন প্রসেস তৈরি এবং প্রোগ্রাম লোড করার ক্লাসিক্যাল ট্রিলজি হলো: `fork()`, `execve()`, এবং `waitpid()`।

### ক. Copy-on-Write (COW) আর্কিটেকচার
ঐতিহাসিকভাবে `fork()` কল করলে প্যারেন্টের সম্পূর্ণ ফিজিক্যাল মেমরি চাইল্ডের জন্য ডুপ্লিকেট করা হতো, যা মেগা-বাইট/গিগাবাইট ডেটার জন্য বিশাল অপচয় ছিল। আধুনিক লিনাক্স **Copy-on-Write (COW)** ব্যবহার করে:

```
BEFORE WRITE:
[ Parent Process PTE ] ----\
                            +---> [ Shared Physical Frame (Read-Only) ]
[ Child Process PTE  ] ----/      (Reference Count = 2)

AFTER CHILD EXECUTES: *ptr = 42 (Write Attempt):
1. Hardware MMU raises Page Fault (#PF) because PTE is marked Read-Only.
2. Kernel page fault handler detects COW flag.
3. Allocates New Physical Frame, copies 4KB data from original frame.
4. Updates Child PTE to point to New Frame and marks it Read-Write.
5. Decrements Reference Count on original frame; Parent PTE remains Read-Only.

[ Parent Process PTE ] ---------> [ Original Physical Frame ] (Ref Count = 1)
[ Child Process PTE  ] ---------> [ NEW Physical Frame (Read-Write) ]
```

### খ. `fork()`, `execve()`, এবং `waitpid()` এর প্রফেশনাল C কোড
নিচের কোডটি দেখায় কীভাবে একটি প্যারেন্ট প্রসেস চাইল্ড তৈরি করে, চাইল্ড একটি নতুন প্রোগ্রাম এক্সিকিউট করে এবং প্যারেন্ট চাইল্ডের এক্সিট স্ট্যাটাস সংগ্রহ করে জম্বি প্রসেস প্রতিরোধ করে:

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

int main(void) {
    pid_t pid = fork();

    if (pid < 0) {
        perror("fork failed");
        exit(EXIT_FAILURE);
    } else if (pid == 0) {
        // --- CHILD PROCESS EXECUTION ---
        printf("[Child] PID: %d, Parent PID: %d\n", getpid(), getppid());
        
        char *args[] = {"/bin/ls", "-la", NULL};
        char *env[] = {NULL};
        
        // execve replaces current process image with /bin/ls
        if (execve("/bin/ls", args, env) == -1) {
            perror("execve failed");
            exit(EXIT_FAILURE);
        }
    } else {
        // --- PARENT PROCESS EXECUTION ---
        printf("[Parent] Created child with PID: %d. Waiting for it...\n", pid);
        
        int status;
        pid_t terminated_child = waitpid(pid, &status, 0); // Blocking wait
        
        if (WIFEXITED(status)) {
            printf("[Parent] Child %d terminated normally with exit code: %d\n",
                   terminated_child, WEXITSTATUS(status));
        } else if (WIFSIGNALED(status)) {
            printf("[Parent] Child %d was killed by signal: %d\n",
                   terminated_child, WTERMSIG(status));
        }
    }
    return EXIT_SUCCESS;
}
```

---

## 🧵 ৪. থ্রেড আর্কিটেকচার ও থ্রেডিং মডেলস (Threads vs Processes)

থ্রেড হলো সিপিইউ এক্সিকিউশনের ক্ষুদ্রতম ইউনিট (Light-Weight Process)।

```
+-------------------------------------------------------------------------+
|                         SINGLE PROCESS                                  |
|  +-------------------------------------------------------------------+  |
|  | SHARED RESOURCES:                                                 |  |
|  | - Virtual Address Space (Code, Data, Heap)                        |  |
|  | - Open File Descriptors (Sockets, Files)                           |  |
|  | - Signal Handlers, Current Working Directory                      |  |
|  +-------------------------------------------------------------------+  |
|                                                                         |
|  +-------------------+  +-------------------+  +---------------------+  |
|  | THREAD 1          |  | THREAD 2          |  | THREAD 3            |  |
|  | - Thread ID (TID) |  | - Thread ID (TID) |  | - Thread ID (TID)   |  |
|  | - Registers/PC/SP |  | - Registers/PC/SP |  | - Registers/PC/SP   |  |
|  | - Private Stack   |  | - Private Stack   |  | - Private Stack     |  |
|  | - Thread-Local Sto|  | - Thread-Local Sto|  | - Thread-Local Sto  |  |
|  +-------------------+  +-------------------+  +---------------------+  |
+-------------------------------------------------------------------------+
```

### থ্রেডিং মডেলসমূহের তুলনামূলক পর্যালোচনা:

```
  Many-to-One (User Threads)     One-to-One (Linux NPTL)     Many-to-Many (Go Runtime)
     [U] [U] [U]                    [U]   [U]   [U]              [U] [U] [U] [U]
       \  |  /                       |     |     |                 \ /   \ /
         [K]                        [K]   [K]   [K]                [K]   [K]
 (Blocked on any Syscall)   (Full Core Parallelism)       (M Goroutines on N OS Threads)
```

১. **Many-to-One (User-Level Threads - ULT):**
   - থ্রেড ম্যানেজমেন্ট ইউজার স্পেস লাইব্রেরিতে হয়। কার্নেল জানেই না যে একাধিক থ্রেড আছে।
   - *সুবিধা:* কনটেক্সট সুইচ অতি দ্রুত (কোনো কার্নেল ট্র্যাপ নেই)।
   - *অসুবিধা:* একটি থ্রেড যদি কোনো ব্লকিং সিস্টেম কল (যেমন `read()`) করে, তবে কার্নেল পুরো প্রসেসকে ব্লক করে দেয়! মাল্টি-কোর সিপিইউর সুবিধা পাওয়া যায় না।
২. **One-to-One (Kernel-Level Threads - KLT):**
   - লিনাক্সের **NPTL (Native POSIX Thread Library)** এই মডেল ব্যবহার করে। প্রতিটি ইউজার থ্রেডের জন্য কার্নেলে একটি করে `task_struct` (LWP) থাকে যা `clone(CLONE_VM | CLONE_FS | CLONE_FILES | CLONE_SIGHAND, ...)` দিয়ে তৈরি হয়।
   - *সুবিধা:* সম্পূর্ণ মাল্টি-কোর হার্ডওয়্যার প্যারালালে এক্সিকিউট হতে পারে। একটি থ্রেড ব্লক হলেও অন্যগুলো সচল থাকে।
৩. **Many-to-Many (Hybrid / M:N - Go Goroutines):**
   - $M$ সংখ্যক ইউজার থ্রেডকে (যেমন Golang Goroutines) $N$ সংখ্যক কার্নেল থ্রেডের উপর রানটাইম শিডিউলার দ্বারা মাল্টিপ্লেক্স করা হয়।

---

## 🔄 ৫. কনটেক্সট সুইচিংয়ের অভ্যন্তরীণ ফিজিক্স (Context Switching Mechanics)

কনটেক্সট সুইচ হলো সিপিইউর কন্ট্রোল একটি প্রসেস/থ্রেড থেকে অন্যটিতে স্থানান্তর করার মেকানিজম।

### ক. প্রসেস সুইচ বনাম থ্রেড সুইচের ল্যাটেন্সি পার্থক্য
- **Thread Context Switch:**
  - সেইভ ও রিস্টোর করতে হয়: General Purpose Registers (`RAX`, `RBX`, `RCX`, ইত্যাদি), Program Counter (`RIP`), Stack Pointer (`RSP`), এবং Floating Point / SIMD Registers।
  - **মেমরি স্পেস একই থাকে!** তাই পেজ টেবিল বেস রেজিস্টার (`CR3`) পরিবর্তনের কোনো প্রয়োজন নেই।
  - **টিএলবি (TLB) ইনভ্যালিডেট হয় না!** ল্যাটেন্সি সাধারণত **~০.৫ থেকে ১ মাইক্রোসেকেন্ড**।
- **Process Context Switch:**
  - থ্রেডের সমস্ত স্টেট সেইভ ছাড়াও **`CR3` রেজিস্টার পরিবর্তন** করতে হয়।
  - ফলস্বরূপ, Translation Lookaside Buffer (TLB) সম্পূর্ণ বা আংশিক ইনভ্যালিডেট হয়ে যায় (যদি না PCID এনাবল থাকে)।
  - পরবর্তী প্রসেস যখন রান শুরু করে, তখন সব মেমরি অ্যাক্সেসে **TLB Miss** এবং **L1/L2/L3 CPU Cache Miss** হয় (Cache Pollution)।
  - ল্যাটেন্সি **~২ থেকে ৫ মাইক্রোসেকেন্ড**, তবে ক্যাশ কোল্ড-স্টার্টের কারণে কার্যকর পারফরম্যান্স পেনাল্টি আরও অনেক বেশি হয়।

---

## 📡 ৬. ইন্টার-প্রসেস কমিউনিকেশন (IPC) ট্যাক্সোনমি ও তুলনা

যেহেতু প্রতিটি প্রসেসের ভার্চুয়াল অ্যাড্রেস স্পেস সম্পূর্ণ আইসোলেটেড, তাই তাদের মধ্যে যোগাযোগের জন্য বিশেষ আইপিসি মেকানিজম প্রয়োজন:

```
+------------------------------------------------------------------------------------+
|                               IPC MECHANISMS                                       |
+----------------------+--------------------+----------------------------------------+
| Mechanism            | Throughput / Speed | Typical Use Case                       |
+----------------------+--------------------+----------------------------------------+
| Shared Memory (mmap) | FASTEST (Zero-Copy)| High-frequency trading, DB buffers     |
| Anonymous Pipe       | High (Buffer-bound)| Shell pipelines (ls | grep)            |
| Named Pipe (FIFO)    | High               | Unrelated local processes              |
| Unix Domain Sockets  | Medium-High        | Docker daemon communication, NGINX-uWSGI|
| Message Queues       | Medium             | Asynchronous task dispatching          |
| Signals              | Minimal (Async Noti| Process termination, Timer ticks       |
+----------------------+--------------------+----------------------------------------+
```

### ক. পাইপ (Anonymous Pipe) আর্কিটেকচার
পাইপ হলো কার্নেল বাফার দ্বারা পরিচালিত একটি হাফ-ডুপ্লেক্স (Uni-directional) বাইট স্ট্রিম।
- `pipe(int pipefd[2])` কল করলে দুটি ফাইল ডিসক্রিপ্টর তৈরি হয়:
  - `pipefd[0]` -> Read End
  - `pipefd[1]` -> Write End
- কার্নেল একটি সার্কুলার বাফার (ডিফল্ট সাইজ 64KB) ম্যানেজ করে। বাফার পূর্ণ থাকলে রাইটার প্রসেস ব্লক হয়, আর বাফার খালি থাকলে রিডার প্রসেস ব্লক হয়।

### খ. ইউনিক্স ডোমেন সকেটস (Unix Domain Sockets - UDS) বনাম TCP/IP Loopback
লোকাল মেশিনে দুটি প্রসেসের যোগাযোগের জন্য `127.0.0.1` লোকাল লুপব্যাকের চেয়ে **Unix Domain Socket (`AF_UNIX`)** প্রায় দ্বিগুণ দ্রুত:
- TCP/IP লুপব্যাকে প্যাকেট রাউটিং, চেকসাম গণনা, হ্যান্ডশেক এবং নেটওয়ার্ক স্ট্যাক ওভারহেড থাকে।
- UDS সরাসরি কার্নেল মেমরিতে প্রসেসদের মধ্যে বাফার কপি করে, কোনো প্যাকেটাইজেশন বা চেকসাম প্রয়োজন হয় না।

---

## 🎯 ৭. শীর্ষ এমএনসি ইন্টারভিউ প্রশ্ন ও সমাধান (MNC Deep Dives)

### Q1: জম্বি (Zombie) এবং অরফ্যান (Orphan) প্রসেসের মধ্যে পার্থক্য কী? জম্বি প্রসেস কীভাবে নির্মূল করবেন?
**উত্তর:**
- **Orphan Process:**
  - যখন একটি চাইল্ড প্রসেস চলারত অবস্থায় তার প্যারেন্ট প্রসেস মারা যায়, তখন চাইল্ড প্রসেসটিকে **Orphan** বলা হয়।
  - লিনাক্স কার্নেল তাৎক্ষণিকভাবে সমস্ত অরফ্যান প্রসেসকে সিস্টেমের মূল প্রসেস **PID 1 (`systemd` বা `init`)**-এর অধীনে দত্তক (Adopt) দিয়ে দেয়। PID 1 পর্যায়ক্রমে `wait()` কল করে এদের সঠিকভাবে টার্মিনেট করে।
- **Zombie Process:**
  - চাইল্ড প্রসেস টার্মিনেট করেছে কিন্তু প্যারেন্ট এখনো `wait()` বা `waitpid()` কল করেনি। এটি সিপিইউ বা মেমরি দখল করে না, কিন্তু কার্নেলের **Process Table Slot (`PID`)** দখল করে রাখে।
  - যদি সিস্টেমে অতিরিক্ত জম্বি জমা হয়, তবে কার্নেলের `pid_max` লিমিট শেষ হয়ে যায় এবং নতুন কোনো প্রসেস স্পন করা যায় না!
- **জম্বি নির্মূলের উপায়:**
  - জম্বি প্রসেসকে সরাসরি `kill -9 <zombie_pid>` দিয়ে কিল করা যায় না (কারণ সে ইতিমধ্যেই মৃত!)।
  - সমাধান: তার **প্যারেন্ট প্রসেসকে কিল** করতে হবে (`kill -9 <parent_pid>`)। প্যারেন্ট মারা গেলে জম্বিটি অরফ্যান হয়ে যাবে এবং PID 1 তাৎক্ষণিকভাবে তাকে অ্যাডপ্ট করে রীপ (Reap) করে ফেলবে।

### Q2: ফর্ক বম্ব (Fork Bomb) কী এবং লিনাক্স ওএসে কীভাবে এটি প্রতিরোধ করা যায়?
**উত্তর:**
- **কনসেপ্ট:** একটি শেল স্ক্রিপ্ট যা রিকার্সিভলি ইনফিনিট প্রসেস স্পন করে সিস্টেমের প্রসেস টেবিল ফুল করে দেয়:
  ```bash
  :(){ :|:& };:
  ```
- **প্রতিরোধ (Mitigation):**
  ১. **`ulimit -u`:** ইউজার প্রতি সর্বোচ্চ প্রসেসের সংখ্যা সীমাবদ্ধ করা (যেমন `/etc/security/limits.conf` এ `nproc 1024` সেট করা)।
  ২. **Linux Cgroups (pids controller):** কন্টেইনার বা ইউজারের জন্য `pids.max` ফাইল ব্যবহার করে কঠোর সীমা নির্ধারণ করা।