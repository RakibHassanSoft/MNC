# Phase 01: কার্নেল আর্কিটেকচার, ডুয়েল-মোড অপারেশন ও সিস্টেম কল ইন্টারনালস (Kernel Architecture & Syscalls)

---

## 📌 ১. অপারেটিং সিস্টেমের মৌলিক ভূমিকা (Fundamental Roles of an OS)

একটি অপারেটিং সিস্টেম (OS) মূলত কম্পিউটার হার্ডওয়্যার এবং ইউজার অ্যাপ্লিকেশনের মধ্যবর্তী একটি জটিল সফটওয়্যার লেয়ার। সিস্টেমস আর্কিটেকচারে অপারেটিং সিস্টেম মূলত দুটি প্রধান ভূমিকা পালন করে:

```
+-------------------------------------------------------------------------+
|                  User Applications (Python, C++, Java, Node.js)         |
+-------------------------------------------------------------------------+
|                    Standard C Library (glibc / musl libc)               |
+=========================================================================+
|   System Call Interface (SYSCALL / SYSRET, POSIX API, Trap to Ring 0)   |
+=========================================================================+
|                              LINUX KERNEL                               |
|  +-------------------+  +-------------------+  +---------------------+  |
|  | Process Scheduler |  | Virtual Memory    |  | Virtual File System |  |
|  | (CFS, rb_node)    |  | (Paging, MMU)     |  | (VFS, Inodes, ext4) |  |
|  +-------------------+  +-------------------+  +---------------------+  |
|  | Inter-Process Com |  | Network Subsystem |  | Device Drivers      |  |
|  | (Futex, Pipes)    |  | (TCP/IP Stack)    |  | (Block, Char, Net)  |  |
|  +-------------------+  +-------------------+  +---------------------+  |
+=========================================================================+
|               Hardware Abstraction Layer (HAL) & CPU Drivers            |
+=========================================================================+
|         HARDWARE (CPU Core, MMU, Registers, RAM, NVMe SSD, NIC)         |
+-------------------------------------------------------------------------+
```

১. **রিসোর্স অ্যালোকেটর (Resource Allocator):**
   - **CPU Time:** বিভিন্ন প্রসেস ও থ্রেডের মধ্যে ফেয়ারনেস নিশ্চিত করে সিপিইউ কোর বণ্টন করা।
   - **Physical RAM:** একাধিক প্রসেসের মেমরি আইসোলেশন ও ফ্র্যাগমেন্টেশন হ্যান্ডেল করা।
   - **I/O Devices & Storage:** ডিস্ক অ্যাক্সেস, নেটওয়ার্ক প্যাকেট হ্যান্ডলিং ও পেরিফেরাল শেয়ারিং নিয়ন্ত্রণ করা।

২. **কন্ট্রোল প্রোগ্রাম (Control Program):**
   - ইউজার কোডের ত্রুটিপূর্ণ এক্সিকিউশন যাতে পুরো সিস্টেম ক্র্যাশ না করায় তা পর্যবেক্ষণ করা।
   - সিকিউরিটি পলিসি, মেমরি ভায়োলেশন প্রিভেনশন এবং হার্ডওয়্যার ডিভাইসে অবৈধ অ্যাক্সেস ব্লক করা।

---

## ⚙️ ২. সিপিইউ প্রটেকশন রিং ও ডুয়েল-মোড অপারেশন (CPU Protection Rings & Dual-Mode)

আধুনিক প্রসেসরসমূহ (যেমন x86-64 এবং ARM64) হার্ডওয়্যার লেভেলেই প্রিভিলেজ লেভেল আইসোলেশন সমর্থন করে।

### ক. x86-64 Hierarchical Protection Rings
x86-64 আর্কিটেকচারে ৪টি প্রটেকশন রিং থাকে ($0, 1, 2, 3$):

```
                     +----------------------------------+
                     |    Ring 3: User Mode             |
                     |    (Web Browsers, NGINX, Python) |
                     |    CPL = 3 (Least Privileged)    |
                     |  +----------------------------+  |
                     |  |  Ring 1 & 2: Device Drivers|  |
                     |  |  (Rarely used in modern OS)|  |
                     |  |  +----------------------+  |  |
                     |  |  | Ring 0: Kernel Mode  |  |  |
                     |  |  | (Core Kernel, Drivers|  |  |
                     |  |  | CPL = 0 (Privileged) |  |  |
                     |  |  +----------------------+  |  |
                     |  +----------------------------+  |
                     +----------------------------------+
```

- **Ring 0 (Supervisor / Kernel Mode):**
  - Current Privilege Level ($\text{CPL} = 0$)।
  - এখানে চলা কোড যেকোনো সিপিইউ ইন্সট্রাকশন চালাতে পারে (যেমন: `cli` - Disable Interrupts, `sti`, `invlpg`, `lgdt`, `lidt`, `mov %cr3, %rax` যা পেজ ডিরেক্টরি বেস রেজিস্টার পরিবর্তন করে)।
  - সরাসরি সমস্ত ফিজিক্যাল মেমরি এবং আই/ও পোর্টে অ্যাক্সেস পায়।
- **Ring 3 (User Mode):**
  - $\text{CPL} = 3$।
  - ইউজার কোড শুধুমাত্র আনপ্রিভিলেজড ইন্সট্রাকশন চালাতে পারে। যদি ইউজার মোডে থাকা কোনো প্রসেস `cli` বা মেমরি প্রটেকশন বিট বাইপাস করার চেষ্টা করে, তবে সিপিইউ তাৎক্ষণিকভাবে একটি **General Protection Fault (`#GP`, Exception 13)** তৈরি করে এবং কার্নেল প্রসেসটিকে `SIGSEGV` পাঠায়।
- **Ring -1 (Ring Minus One - Hypervisor / VMX Root):**
  - Intel VT-x বা AMD-V ভার্চুয়ালাইজেশনে হাইপারভাইজার (যেমন KVM) চালানোর জন্য ব্যবহৃত হয়।

### খ. ARM64 Exception Levels (EL0 to EL3)
ARMv8/ARMv9 আর্কিটেকচারে রিংয়ের বদলে **Exception Levels (EL)** ব্যবহার করা হয়:
- **EL0:** User Applications (Unprivileged)।
- **EL1:** Operating System Kernel (Linux, Android)।
- **EL2:** Hypervisor (KVM, Xen)।
- **EL3:** Secure Monitor / Firmware (ARM TrustZone)।

---

## 🛑 ৩. ট্র্যাপ, ইন্টারাপ্ট এবং এক্সেপশন (Traps, Interrupts & Exceptions)

সিপিইউ যখন ইউজার মোড (Ring 3) থেকে কার্নেল মোডে (Ring 0) ট্রানজিশন করে, তখন তিনটি পদ্ধতির যেকোনো একটি ঘটে:

```
                              [ CPU EXECUTION EVENT ]
                                         |
         +-------------------------------+-------------------------------+
         |                               |                               |
         v                               v                               v
[ Hardware Interrupt ]                [ Trap ]                    [ Exception ]
 - Asynchronous (External)        - Synchronous (Internal)    - Synchronous (Internal)
 - Keyboard, NIC packet, Timer    - Intentional syscall       - Fault, Trap, Abort
 - Handled via IDT vector         - Generated via SYSCALL     - Page Fault, Div-by-Zero
```

### ক. বিস্তারিত শ্রেণিবিভাগ

| ইভেন্টের ধরন | উৎস (Source) | সিনক্রোনাস / অ্যাসিনক্রোনাস | উদাহরণ |
| :--- | :--- | :--- | :--- |
| **হার্ডওয়্যার ইন্টারাপ্ট (Interrupt)** | বাহ্যিক ডিভাইস (Hardware Timer, Network NIC, Keyboard, NVMe) | **Asynchronous:** বর্তমান চলমান সিপিইউ নির্দেশনার সাথে সম্পর্কহীন | Timer tick (`APIC timer`), নেটওয়ার্ক কার্ডে প্যাকেট আগমন |
| **সফটওয়্যার ট্র্যাপ (Trap)** | ইউজার প্রোগ্রাম কোড | **Synchronous:** ইচ্ছাকৃতভাবে ইউজার প্রোগ্রাম কল করে | `syscall`, `sysenter`, `int 0x80`, ডিবাগার ব্রেকপয়েন্ট (`int3`) |
| **এক্সেপশন (Exception)** | সিপিইউর অভ্যন্তরীণ এরর কন্ডিশন | **Synchronous:** নির্দিষ্ট কোনো ইন্সট্রাকশন এক্সিকিউট করতে গিয়ে ত্রুটি | Page Fault (`#PF`), Divide by Zero (`#DE`), Stack Overflow |

### খ. এক্সেপশনের তিনটি সূক্ষ্ম রূপ (Fault, Trap, Abort)
১. **Fault:** এটি রিকভারেবল। কার্নেল হ্যান্ডলার সমস্যাটি সমাধান করে এবং **ফেল করা ইন্সট্রাকশনটি পুনরায় এক্সিকিউট (Restart)** করে।
   - *উদাহরণ:* **Page Fault (Vector 14):** পেজ মেমরিতে না থাকলে কার্নেল ডিস্ক থেকে পেজ এনে দেয় এবং সেই ইনস্ট্রাকশনটি আবার চলে।
২. **Trap:** রিকভারেবল কিন্তু এটি সম্পন্ন হওয়ার পর পরবর্তী ইন্সট্রাকশন থেকে এক্সিকিউশন শুরু হয়।
   - *উদাহরণ:* ব্রেকপয়েন্ট বা সফটওয়্যার ডিবাগিং ইন্সট্রাকশন।
৩. **Abort:** নন-রিকভারেবল হার্ডওয়্যার বা কার্নেল ক্র্যাশ। প্রসেসটি তাৎক্ষণিকভাবে বন্ধ করা হয়।
   - *উদাহরণ:* Machine Check Exception (`#MC`), Double Fault (`#DF`)।

---

## ⚡ ৪. মোড সুইচিং মেকানিজম: `SYSCALL`/`SYSRET` বনাম `INT 0x80`

ইউজার মোড থেকে কার্নেল মোডে প্রবেশের ঐতিহাসিক রূপান্তরটি সিস্টেমস পারফরম্যান্সের একটি প্রধান মাইলফলক:

### ক. লেগ্যাসি মেথড: `INT 0x80` (Software Interrupt Gate)
- **মেকানিজম:**
  1. ইউজার প্রসেস `%eax` এ সিস্টেম কল নাম্বার এবং অন্যান্য রেজিস্টারে আর্গুমেন্ট রাখে।
  2. `INT 0x80` ইন্সট্রাকশন এক্সিকিউট করে।
  3. সিপিইউ **Interrupt Descriptor Table (IDT)**-এর ১২৮তম এন্ট্রিতে যায়।
  4. Task State Segment (TSS) থেকে কার্নেল স্ট্যাক পয়েন্টার (`ESP0`) লোড করে।
  5. ইউজার স্ট্যাকের মান (`SS`, `ESP`, `EFLAGS`, `CS`, `EIP`) কার্নেল স্ট্যাকে পুশ করে।
  6. প্রিভিলেজ লেভেল ০-তে পরিবর্তন করে এবং সিস্টেম কল হ্যান্ডলারে জাম্প করে।
- **সমস্যা:** এটি অত্যন্ত ধীরগতির। প্রতিটি সিস্টেমে কল করতে ~১০০ ন্যানোসেকেন্ড (ns) সময় লাগত এবং অনেকগুলো মেমরি মেমরি বাস রিড/রাইট হতো।

### খ. আধুনিক মেথড: x86-64 `SYSCALL` / `SYSRET` (Fast System Call)
AMD এবং Intel আধুনিক ৬৪-বিট প্রসেসরে ডেডিকেটেড হার্ডওয়্যার ইন্সট্রাকশন প্রবর্তন করেছে:
- **কনফিগারেশন রেজিস্টার (Model-Specific Registers - MSRs):**
  - `IA32_LSTAR` (Long System Target Address Register): এখানে কার্নেলের সিস্টেম কল এন্ট্রি পয়েন্ট (`entry_SYSCALL_64`) এর মেমরি অ্যাড্রেস বুট টাইমে লিখে রাখা হয়।
  - `IA32_STAR`: User ও Kernel এর Segment Selectors ধারণ করে।
  - `IA32_FMASK`: সিস্টেম কলের সময় EFLAGS এর কোন কোন বিট ক্লিয়ার করা হবে (যেমন Interrupt Flag IF ক্লিয়ার করে ইন্টারাপ্ট ডিজেবল করা) তা নির্ধারণ করে।
- **এক্সিকিউশন ফিজিক্স:**
  1. ইউজার প্রোগ্রাম `SYSCALL` এক্সিকিউট করে।
  2. সিপিইউ কোনো স্ট্যাক পুশ করে না! বরং সরাসরি `%rcx` রেজিস্টারে বর্তমান Instruction Pointer (`RIP`) এবং `%r11` রেজিস্টারে বর্তমান Flags (`RFLAGS`) সংরক্ষণ করে।
  3. সিপিইউ সরাসরি `IA32_LSTAR` অ্যাড্রেসে জাম্প করে এবং $\text{CPL}=0$ তে চলে আসে।
  4. কার্নেল `swapgs` ইন্সট্রাকশন দিয়ে ইউজার GS বেস রেজিস্টারকে কার্নেল পার-সিপিইউ ডেটা স্ট্রাকচারে পরিবর্তন করে।
  5. এটি মাত্র **~১০ থেকে ১৫ ন্যানোসেকেন্ডে (ns)** সম্পন্ন হয়!

```
[ User Program (Ring 3) ]
       |
       |  1. Setup: %rax=sys_read (0), %rdi=fd, %rsi=buf, %rdx=count
       v
[ SYSCALL Instruction ]
       |
       +---> Hardware: %rcx <= %rip, %r11 <= %rflags, %rip <= IA32_LSTAR
       |               CPL transitions from 3 to 0 (Kernel Mode)
       v
[ Linux entry_SYSCALL_64 (Ring 0) ]
       |
       |  2. swapgs (switch to per-CPU kernel data)
       |  3. Switch to Kernel Stack (RSP = kernel_stack_top)
       |  4. Save user registers to pt_regs struct on kernel stack
       |  5. Call sys_call_table[%rax]
       v
[ sys_read() Handler executes ]
       |
       |  6. Restore user registers from pt_regs
       |  7. swapgs (back to user thread-local storage)
       v
[ SYSRETQ Instruction ]
       |
       +---> Hardware: %rip <= %rcx, %rflags <= %r11, CPL transitions to 3
       v
[ User Program Resumes ]
```

---

## 🏛️ ৫. কার্নেল আর্কিটেকচার মডেলসমূহ (Kernel Architecture Models)

সিস্টেম ডিজাইনে কার্নেলের কম্পোনেন্টগুলো কীভাবে মেমরিতে অর্গানাইজড থাকে তার উপর ভিত্তি করে কার্নেলকে ৪টি প্রধান ভাগে ভাগ করা যায়:

```
   MONOLITHIC KERNEL (Linux)                 MICROKERNEL (seL4, MINIX)
+-------------------------------+       +-------------------------------+
| User Applications (Web, DB)   |       | User Apps | File Sys | Drivers|
| (Ring 3 - User Mode)          |       | (Ring 3 - User Mode)          |
+===============================+       +===============================+
| VFS | Network | Sched | Memory|       | Microkernel: IPC, Scheduler,  |
| Drivers | IPC | File Systems  |       | Basic Virtual Memory Only     |
| (Ring 0 - Kernel Mode)        |       | (Ring 0 - Kernel Mode)        |
+-------------------------------+       +-------------------------------+
```

### ক. আর্কিটেকচারভিত্তিক গভীর তুলনামূলক বিশ্লেষণ

| বৈশিষ্ট্য | ১. মনোলিথিক কার্নেল (Monolithic) | ২. মাইক্রোকার্নেল (Microkernel) | ৩. হাইব্রিড কার্নেল (Hybrid) | ৪. ইউনিক্যার্নেল (Unikernel) |
| :--- | :--- | :--- | :--- | :--- |
| **বাস্তব উদাহরণ** | Linux, FreeBSD, OpenBSD | seL4, MINIX 3, QNX Neutrino, Google Fuchsia (Zircon) | Windows NT (10/11), macOS (XNU / Darwin) | MirageOS, OSv, Unikraft |
| **কার্নেল স্পেস সাইজ** | বিশাল (মিলিয়ন লাইন কোড) | অতি ক্ষুদ্র (~১০ হাজার লাইন কোড) | মাঝারি থেকে বৃহৎ | কোনো স্পেস ডিভিশন নেই |
| **ড্রাইভার এক্সিকিউশন** | Ring 0 (কার্নেল স্পেস) | Ring 3 (ইউজার প্রসেস হিসেবে) | Ring 0 (কিছু ইউজার মোড ড্রাইভার UMDF সহ) | অ্যাপ্লিকেশনের সাথে লিংকড |
| **যোগাযোগ মাধ্যম (IPC)** | ডিরেক্ট সি ফাংশন কল ও পয়েন্টার ডিরিফারেন্সিং | মেসেজ পাসিং (IPC) সিস্টেম কল | ফাংশন কল ও কার্নেল মাইক্রো-আইপিসি | ডিরেক্ট ইন-মেমরি কল |
| **পারফরম্যান্স** | সর্বোচ্চ (জিরো আইপিসি ওভারহেড) | কম (বারংবার ট্র্যাপ ও কনটেক্সট সুইচ) | উচ্চ (তবে মনোলিথিকের কাছাকাছি) | চরম দ্রুত (বুট টাইম মিলিসেকেন্ড) |
| **ফল্ট টলারেন্স** | নিম্ন (ড্রাইভার ক্র্যাশ মানে কার্নেল প্যানিক) | চরম উচ্চ (ক্র্যাশ করা ফাইল সিস্টেম রিস্টার্ট করা যায়) | মাঝারি (BSOD ঘটে বাজে ড্রাইভারের কারণে) | সিঙ্গেল প্রসেস ফেইল করলে সম্পূর্ণ VM ডাই করে |

> **MNC Interview Insight (The Tanenbaum-Torvalds Debate):**
> অ্যান্ড্রু টানেনবম ১৯৯২ সালে লিনাস টরভাল্ডসকে বলেছিলেন, *"Linux is obsolete because it is monolithic."* কিন্তু বাস্তবে লিনাক্স ইন্ডাস্ট্রি শাসন করছে কারণ:
> ১. **Loadable Kernel Modules (LKM):** লিনাক্স মনোলিথিক হলেও এটি ডায়নামিকালি মডিউল লোড ও আনলোড করতে পারে, ফলে কার্নেল রি-কম্পাইল করতে হয় না।
> ২. **IPC Cost:** মাইক্রোকার্নেলে একটি সাধারণ ফাইল রাইট করতে ইউজার প্রসেস -> কার্নেল -> ফাইল সার্ভার -> কার্নেল -> ডিস্ক ড্রাইভার -> কার্নেল -> ইউজার প্রসেসে অন্তত ৪টি অতিরিক্ত কনটেক্সট সুইচ এবং ৮টি মেমরি কপি করতে হয়, যা হাই-থ্রুপুট সিস্টেমে অগ্রহণযোগ্য।

---

## 💻 ৬. লিনাক্স সিস্টেম কল সাবসিস্টেম ও রেজিস্টার কনভেনশন

লিনাক্সে প্রতিবার সিস্টেম কল চালানোর সময় একটি নির্দিষ্ট Calling Convention মানা হয়:

### ক. x86-64 System Call Calling Convention
| রেজিস্টার (Register) | ভূমিকা (Purpose) |
| :--- | :--- |
| `%rax` | সিস্টেম কল নম্বর ইনপুট হিসেবে নেয় এবং এক্সিকিউশন শেষে **রিটার্ন ভ্যালু** ধারণ করে |
| `%rdi` | প্রথম আর্গুমেন্ট (1st Argument) |
| `%rsi` | দ্বিতীয় আর্গুমেন্ট (2nd Argument) |
| `%rdx` | তৃতীয় আর্গুমেন্ট (3rd Argument) |
| `%r10` | চতুর্থ আর্গুমেন্ট (4th Argument - লক্ষ্য করুন ইউজার স্পেস ফাংশন কলে এটি `%rcx` হলেও সিস্টেম কলে `%r10` কারণ `SYSCALL` ইন্সট্রাকশন `%rcx` ওভাররাইট করে) |
| `%r8`  | পঞ্চম আর্গুমেন্ট (5th Argument) |
| `%r9`  | ষষ্ঠ আর্গুমেন্ট (6th Argument) |

### খ. পিওর অ্যাসেম্বলিতে ডিরেক্ট সিস্টেম কল বাস্তবায়ন (No C Runtime / No Glibc)
নিচে একটি স্বয়ংসম্পূর্ণ x86-64 অ্যাসেম্বলি প্রোগ্রাম দেওয়া হলো যা কোনো লাইব্রেরি ছাড়া সরাসরি কার্নেল সিস্টেম কল ব্যবহার করে কনসোলে স্ট্রিং প্রিন্ট করে এবং এক্সিট হয়:

```nasm
section .data
    msg db "Hello MNC System Engineering!", 0xA  ; 0xA = Newline (\n)
    len equ $ - msg

section .text
    global _start

_start:
    ; 1. sys_write(int fd, const char *buf, size_t count)
    ; Syscall number: 1
    mov rax, 1          ; %rax = 1 (sys_write)
    mov rdi, 1          ; %rdi = 1 (stdout file descriptor)
    mov rsi, msg        ; %rsi = pointer to buffer
    mov rdx, len        ; %rdx = string length
    syscall             ; Trigger fast system call transition to Ring 0

    ; 2. sys_exit(int status)
    ; Syscall number: 60
    mov rax, 60         ; %rax = 60 (sys_exit)
    xor rdi, rdi        ; %rdi = 0 (exit status code 0)
    syscall             ; Terminate process
```

### গ. রিটার্ন ভ্যালু ও `errno` মেকানিজম
কার্নেল স্পেস কখনো গ্লোবাল ভ্যারিয়েবল `errno` সেট করে না। কার্নেল যদি কোনো ত্রুটির সম্মুখীন হয়, তবে রিটার্ন রেজিস্টার `%rax`-এ একটি নেগেটিভ মান পাঠায় (যেমন `-EACCES = -13`, `-ENOENT = -2`)।
ইউজার স্পেসের `glibc` র‍্যাপার ফাংশন এই রিটার্ন ভ্যালু চেক করে:
$$\text{If } \%rax < 0 \implies \text{errno} = -\%rax, \quad \text{return } -1$$
$$\text{If } \%rax \ge 0 \implies \text{return } \%rax$$

---

## 🚀 ৭. সিস্টেম বুটস্ট্র্যাপিং সিকোয়েন্স (System Bootstrapping Lifecycle)

কম্পিউটারের পাওয়ার বাটন চাপার পর থেকে অপারেটিং সিস্টেমের প্রথম প্রসেস চালু হওয়া পর্যন্ত সম্পূর্ণ সিকোয়েন্স:

```
[ Power Supply ON ]
        |
        v
[ CPU Reset Vector: 0xFFFFFFF0 ] (Executes in 16-bit Real Mode)
        |
        v
[ UEFI / BIOS Firmware Initialization & POST ] (Power-On Self-Test)
        |
        v
[ Boot Device Selection ] (Reads EFI System Partition ESP or MBR)
        |
        v
[ Stage 1 & Stage 2 Bootloader: GRUB2 ]
        |
        +---> Loads Kernel Image (vmlinuz) and Initial RAM Disk (initramfs) into RAM
        |
        v
[ Linux Kernel Initialization (Long Mode 64-bit) ]
        |
        +---> Page tables setup (Swapper page table)
        +---> Interrupt Descriptor Table (IDT) & Global Descriptor Table (GDT) loaded
        +---> Device driver probing & hardware detection
        +---> Mounts root filesystem (/)
        |
        v
[ Spawns PID 1: /sbin/init (systemd) ]
        |
        +---> Spawns User Services, Daemons, TTY, Network, and Display Manager
```

১. **POST ও Reset Vector:** পাওয়ার অন করার পর প্রসেসর ১৬-বিট রিয়েল মোডে শুরু হয় এবং হার্ডওয়্যার রেজিস্টার `CS:IP` নির্দিষ্ট অ্যাড্রেস `0xFFFF0` পয়েন্ট করে যেখানে মাদারবোর্ডের UEFI/BIOS রম মেমরি থাকে।
২. **GRUB2 (Grand Unified Bootloader):** এটি কার্নেল ইমেজ (`vmlinuz` - যা একটি কমপ্রেসড bzImage) এবং `initramfs` (Initial RAM File System) মেমরিতে লোড করে।
৩. **Kernel Entry & Uncompression:** কার্নেল নিজেকে আনকম্প্রেস করে, পেজ টেবিল চালু করে এবং প্রটেক্টেড মোড পার হয়ে ৬৪-বিট লং মোডে (Long Mode) প্রবেশ করে।
৪. **PID 1 Execution:** কার্নেল প্রথম ইউজার-স্পেস প্রসেস হিসেবে `/sbin/init` (আধুনিক লিনাক্সে `systemd`) রান করায়। PID 1 হলো সমস্ত ইউজার প্রসেসের মূল পূর্বপুরুষ (Ancestor)।

---

## 🎯 ৮. শীর্ষ এমএনসি ইন্টারভিউ প্রশ্ন ও সমাধান (MNC Deep Dives)

### Q1: `strace` টুলটি কীভাবে একটি প্রসেসের সিস্টেম কল ইন্টারসেপ্ট করে?
**উত্তর:**
`strace` লিনাক্সের `ptrace(2)` (Process Trace) সিস্টেম কল ব্যবহার করে।
১. ট্র্যাকার প্রসেস `ptrace(PTRACE_ATTACH, pid, ...)` বা `PTRACE_TRACEME` চালায়।
২. এরপর লিনাক্স কার্নেলের আর্কিটেকচার-স্পেসিফিক সিস্টেম কল এন্ট্রি পয়েন্টে (`entry_SYSCALL_64`) কার্নেল চেক করে যে প্রসেসের `TIF_SYSCALL_TRACE` ফ্ল্যাগ অন আছে কি না।
৩. ফ্ল্যাগ অন থাকলে কার্নেল সিস্টেম কল এক্সিকিউট করার ঠিক পূর্বে প্রসেসটিকে সাসপেন্ড করে এবং ট্রেসার প্রসেসকে (`strace`) একটি `SIGTRAP` পাঠায়।
৪. `strace` তখন `ptrace(PTRACE_GETREGS)` দিয়ে `%rax`, `%rdi`, `%rsi` ইত্যাদি রেজিস্টারের মান পড়ে প্রিন্ট করে।
৫. `ptrace(PTRACE_SYSCALL)` কল করার পর সিস্টেম কল এক্সিকিউট হয় এবং বের হওয়ার মুখে আবার `SIGTRAP` পাঠানো হয় যাতে রিটার্ন ভ্যালু ক্যাপচার করা যায়।

### Q2: Meltdown এবং Spectre হার্ডওয়্যার ভালনারেবিলিটি কীভাবে কার্নেল আইসোলেশন ভেঙেছিল? এবং KPTI কী?
**উত্তর:**
- **সমস্যা:** পারফরম্যান্স বাড়ানোর জন্য আধুনিক সিপিইউতে ইউজার মোডে চলাকালীনও পুরো কার্নেল মেমরির পেজ টেবিল ম্যাপিং (Supervisor Bit সহ) মেমরিতে রাখা হতো। যদিও Ring 3-তে ইউজার কার্নেল মেমরি পড়তে পারত না, কিন্তু মডার্ন সিপিইউর **Speculative Execution** এবং **Out-of-Order Execution** মেমরি চেক হওয়ার আগেই অবৈধ মেমরি লোকেশন স্পেকুলেটিভলি রিড করত এবং সিপিইউ ক্যাশে (L1/L2) ডাটা ট্রেস রেখে দিত। অ্যাটাকার ক্যাশ টাইমিং সাইড-চ্যানেল অ্যাটাক (Flush+Reload) চালিয়ে কার্নেল মেমরির গোপন পাসওয়ার্ড বের করতে পারত।
- **সমাধান - KPTI (Kernel Page Table Isolation):**
  লিনাক্স কার্নেল পেজ টেবিলকে দুটি ভাগে আলাদা করেছে:
  - **User Page Table:** যখন কোড User Mode-এ চলে, তখন কার্নেলের কোড ও মেমরির কোনো ম্যাপিং পেজ টেবিলে থাকেই না (শুধুমাত্র ট্র্যাপ হ্যান্ডলিংয়ের জন্য ন্যূনতম স্টাব থাকে)।
  - **Kernel Page Table:** যখন `SYSCALL` দিয়ে Ring 0-তে ঢোকা হয়, তখন কার্নেল সম্পূর্ণ পেজ টেবিল (CR3 রেজিস্টার) লোড করে।
  - *ট্রেড-অফ:* প্রতিটি সিস্টেম কলে CR3 রেজিস্টার সুইচ করতে হয় যা TLB ফ্লাশ করে এবং সিস্টেমের পারফরম্যান্স ৫% থেকে ৩০% পর্যন্ত কমে যায় (PCID হার্ডওয়্যার ফিচার দিয়ে এটি কিছুটা কমানো হয়েছে)।