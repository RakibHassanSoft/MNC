# 🖥️ Operating Systems (OS) Master Engineering Curriculum for MNCs
### গুগল, মেটা, অ্যামাজন, মাইক্রোসফট, অ্যাপল ও উবারের জন্য পূর্ণাঙ্গ সিস্টেম ইন্টারভিউ হ্যান্ডবুক

---

## 📌 ওভারভিউ ও ভূমিকা (Overview & Scope)

কম্পিউটার সায়েন্স এবং সিস্টেমস ইঞ্জিনিয়ারিংয়ের ভিত্তি হলো **অপারেটিং সিস্টেম (Operating System - OS)**। গুগল (Google), মেটা (Meta), অ্যামাজন (Amazon), মাইক্রোসফট (Microsoft), অ্যাপল (Apple), উবার (Uber) এবং নেটফ্লিক্স (Netflix)-এর মতো টিয়ার-১ টেক কোম্পানিতে **Backend Systems Engineer**, **Distributed Systems Architect**, **Kernel/Embedded Engineer**, **Site Reliability Engineer (SRE)** এবং **Infrastructure Engineer** পদের ইন্টারভিউতে অপারেটিং সিস্টেমের ইন্টারনাল মেকানিজম, মেমরি মডেল, কনকারেন্সি এবং কার্নেল ডিজাইন থেকে গভীরতম প্রশ্ন করা হয়।

এই কারিকুলামটি সাধারণ টেক্সটবুকের ভাসা-ভাসা তত্ত্বের বাইরে গিয়ে **লিনাক্স কার্নেল (Linux Kernel 6.x)**, **x86-64 / ARM64 হার্ডওয়্যার আর্কিটেকচার**, সিস্টেম কল ইন্টারফেস, মাল্টি-থ্রেডিং ও কনকারেন্সি সিঙ্ক্রোনাইজেশন, ভার্চুয়াল মেমরি ও পেজিং ইন্টারনালস, ডিস্ট্রিবিউটেড ও রিয়েল-টাইম সিস্টেমস (RTOS), এবং ডকার/কন্টেইনারের অভ্যন্তরীণ আর্কিটেকচারকে **১০টি গভীর ও প্রফেশনাল ফেজে (10 Rigorous Phases)** বিভক্ত করেছে।

---

## 🗺️ ১০টি ফেজের মাস্টার রোডম্যাপ (Curriculum Master Map)

```
                                    [ OPERATING SYSTEMS HUB ]
                                                |
        +-------------------+-------------------+-------------------+-------------------+
        |                   |                   |                   |                   |
        v                   v                   v                   v                   v
   [ PHASE 01 ]        [ PHASE 02 ]        [ PHASE 03 ]        [ PHASE 04 ]        [ PHASE 05 ]
 Kernel & Syscalls   Process & Threads    CPU Scheduling     Concurrency & IPC       Deadlocks
 (Ring 0-3, Traps)   (task_struct, COW)   (CFS, vruntime)   (Futex, Semaphores)  (Banker's Alg)
        |                   |                   |                   |                   |
        +-------------------+-------------------+-------------------+-------------------+
        |                   |                   |                   |                   |
        v                   v                   v                   v                   v
   [ PHASE 06 ]        [ PHASE 07 ]        [ PHASE 08 ]        [ PHASE 09 ]        [ PHASE 10 ]
 Memory Management   Virtual Memory & VM  File Systems & IO   Linux Internals     RTOS & MNC Deep
 (TLB, 4-Level Paging)(Belady, Slab/Slub) (VFS, ext4, RAID)   (cgroups, cgroupv2) (Top 10 Scenarios)
```

---

## 📑 ফেজ-ভিত্তিক বিস্তারিত সূচিপত্র (Detailed Table of Contents)

### ১. [Phase 01: কার্নেল আর্কিটেকচার, ডুয়েল-মোড অপারেশন ও সিস্টেম কল ইন্টারনালস](./Phase_01_Kernel_Architecture_and_Dual_Mode_Operation.md)
- অপারেটিং সিস্টেমের মৌলিক দ্বৈত ভূমিকা: Resource Allocator & Control Program।
- x86-64 প্রটেকশন রিং (Ring 0 বনাম Ring 3), ডুয়েল-মোড অপারেশন (User Mode vs Kernel Mode)।
- ট্র্যাপ (Trap), হার্ডওয়্যার ইন্টারাপ্ট (Hardware Interrupt), সফটওয়্যার ইন্টারাপ্ট ও এক্সেপশন (Fault, Trap, Abort)।
- মোড সুইচের অভ্যন্তরীণ হার্ডওয়্যার মেকানিজম: `SYSCALL`/`SYSRET` বনাম লেগ্যাসি `INT 0x80`।
- কার্নেল আর্কিটেকচার মডেল: মনোলিথিক (Linux), মাইক্রোকার্নেল (Mach, MINIX, Fuchsia Zircon), হাইব্রিড (Windows NT, macOS XNU), এবং এক্সোকার্নেল/ইউনিক্যার্নেল (Unikraft)।
- লিনাক্স সিস্টেম কল ডিসপ্যাচিং: glibc র‍্যাপার, রেজিস্টার কনভেনশন (`rax`, `rdi`, `rsi`, `rdx`, `r10`, `r8`, `r9`), `errno`, এবং `strace` আর্কিটেকচার।
- সিস্টেম বুটস্ট্র্যাপিং সিকোয়েন্স: Power-on -> UEFI/BIOS -> Bootloader (GRUB) -> Kernel Decompression -> System Initialization (`systemd` / PID 1)।

### ২. [Phase 02: প্রসেস, থ্রেড, লাইফসাইকেল ও ইন্টার-প্রসেস কমিউনিকেশন (IPC)](./Phase_02_Processes_Threads_and_Process_Lifecycle.md)
- প্রসেস মেমরি লেআউট: Text Segment, Initialized Data (.data), Uninitialized BSS (.bss), Heap, Memory-Mapped Segment, Stack, এবং কার্নেল স্পেস।
- প্রসেস কন্ট্রোল ব্লক (PCB): লিনাক্স `task_struct`, প্রসেস স্টেট ট্রানজিশন (New, Ready, Running, Waiting, Terminated, Zombie, Orphan)।
- প্রসেস তৈরির সিস্টেম কল: `fork()`, `vfork()`, `clone()`, `execve()`, `waitpid()`, `exit()`। Copy-on-Write (COW) এবং পেজ টেবিল ডুপ্লিকেশন।
- থ্রেড আর্কিটেকচার: প্রসেস বনাম থ্রেড, কার্নেল-লেভেল থ্রেড (KLT) বনাম ইউজার-লেভেল থ্রেড (ULT), NPTL (1:1 থ্রেডিং মডেল), Green Threads (M:N মডেল)।
- কনটেক্সট সুইচিং (Context Switching) ফিজিক্স: রেজিস্টার স্টেট ডাম্প, Program Counter (PC), Stack Pointer (SP), CR3 রেজিস্টার সুইচ ও TLB ফ্লাশিং পেনাল্টি।
- ইন্টার-প্রসেস কমিউনিকেশন (IPC) ট্যাক্সোনমি: অ্যানোনিমাস পাইপ (Anonymous Pipes), ফ্লিফো (Named Pipes/FIFO), মেসেজ কিউ (POSIX vs System V), শেয়ার্ড মেমরি (`mmap`, `shmget`), ইউনিক্স ডোমেন সকেটস (UDS) বনাম নেটওয়ার্ক সকেটস, লিনাক্স সিগন্যালস হ্যান্ডলিং (`SIGKILL`, `SIGTERM`, `SIGSEGV`, `SIGCHLD`)।

### ৩. [Phase 03: সিপিইউ শিডিউলিং অ্যালগরিদম ও লিনাক্স সিএফএস (CFS)](./Phase_03_CPU_Scheduling_and_Linux_CFS.md)
- শিডিউলিং ক্রাইটেরিয়া: CPU Utilization, Throughput, Turnaround Time, Waiting Time, Response Time, Fairness বনাম Starvation।
- ক্লাসিক্যাল শিডিউলিং অ্যালগরিদম (গাণিতিক প্রমাণ ও গ্যান্ট চার্ট): FCFS (Convoy Effect), SJF (সর্বনিম্ন গড় অপেক্ষার সময়ের গাণিতিক প্রমাণ), SRTF, Priority Scheduling (Starvation দূরীকরণে Aging টেকনিক), Round Robin (টাইম কোয়ান্টাম $\tau$-এর গাণিতিক অপ্টিমাইজেশন)।
- মাল্টি-লেভেল কিউ (MLQ) এবং মাল্টি-লেভেল ফিডব্যাক কিউ (MLFQ): প্রায়োরিটি বুস্টিং, CPU-bound বনাম I/O-bound ডাইনামিক অ্যাডাপ্টেশন।
- মাল্টি-প্রসেসর শিডিউলিং: সিমেট্রিক মাল্টিপ্রসেসিং (SMP), প্রসেসর অ্যাফিনিটি (Soft vs Hard Affinity), NUMA আর্কিটেকচার ও মেমরি লোকালিটি।
- লিনাক্স কমপ্লিটলি ফেয়ার শিডিউলার (CFS): রেড-ব্ল্যাক ট্রি (`rb_node`), ভার্চুয়াল রানটাইম (`vruntime`), নাইসনেস (-20 to 19) ওয়েট স্কেলিং ফর্মুলা, রিয়েল-টাইম শিডিউলিং ক্লাসেস (`SCHED_FIFO`, `SCHED_RR`, `SCHED_DEADLINE`)।

### ৪. [Phase 04: কনকারেন্সি, সিঙ্ক্রোনাইজেশন প্রিমিটিভস ও ক্লাসিক্যাল আইপিসি সমস্যা](./Phase_04_Concurrency_Synchronization_and_IPC_Problems.md)
- রেস কন্ডিশন (Race Condition) ও ক্রিটিক্যাল সেকশন প্রবলেম: ৩টি আবশ্যিক শর্ত (Mutual Exclusion, Progress, Bounded Waiting)।
- সফটওয়্যার ভিত্তিক সমাধান: পিটারসনের অ্যালগরিদম (Peterson's Algorithm - গাণিতিক প্রমাণ ও আধুনিক Out-of-Order CPU-তে Memory Barrier-এর গুরুত্ব), ল্যাম্পোর্টের বেকারি অ্যালগরিদম (Lamport's Bakery Algorithm)।
- হার্ডওয়্যার সিঙ্ক্রোনাইজেশন ইন্সট্রাকশন: Test-And-Set (TAS), Compare-And-Swap (CAS), Fetch-And-Add (FAA), Load-Linked / Store-Conditional (LL/SC), মেমরি অর্ডার (`std::memory_order_seq_cst`, `acquire-release`)।
- হাই-লেভেল প্রিমিটিভস: স্পিনলক (Spinlock) বনাম মিউটেক্স (Mutex), কাউন্টিং ও বাইনারি সেমাফোর (Dijkstra's P/V Wait-Signal), কন্ডিশন ভেরিয়েবলস, মনিটরস।
- লিনাক্স ফাস্ট ইউজার-স্পেস লকিং: `futex` (Fast Userspace Mutex) ইন্টারনালস।
- ক্লাসিক্যাল কনকারেন্সি প্রবলেমস (কমপ্লিট সি কোড ও সেমাফোর দিয়ে বাস্তবায়ন):
  - বাউন্ডেড বাফার (Producer-Consumer Problem)
  - রিডার্স-রাইটার্স প্রবলেম (Reader-Preference, Writer-Preference, Starvation-Free Fair RWLock)
  - ডাইনিং ফিলোসফার্স প্রবলেম (Resource Hierarchy, Chandy-Misra, এবং মনিটর সমাধান)
  - স্লিপিং বারবার প্রবলেম (Sleeping Barber Problem)।

### ৫. [Phase 05: ডেডলক: প্রতিরোধ, পরিহার, শনাক্তকরণ ও সমাধান](./Phase_05_Deadlocks_Prevention_Avoidance_and_Recovery.md)
- ডেডলকের আনুষ্ঠানিক সংজ্ঞা ও সিস্টেম মডেল।
- কফম্যানের ৪টি শর্ত (Coffman Conditions): Mutual Exclusion, Hold and Wait, No Preemption, Circular Wait।
- রিসোর্স অ্যালোকেশন গ্রাফ (RAG): রিকোয়েস্ট এজ, অ্যাসাইনমেন্ট এজ, সাইকেল ডিটেকশন থিওরেম (সিঙ্গেল বনাম মাল্টিপল ইনস্ট্যান্স রিসোর্স)।
- ডেডলক প্রিভেনশন (Deadlock Prevention): ৪টি কফম্যান শর্ত ভাঙার বাস্তবসম্মত সিস্টেমস কৌশল।
- ডেডলক পরিহার (Deadlock Avoidance): Safe State বনাম Unsafe State বনাম Deadlock ট্রানজিশন ডায়াগ্রাম।
- ডাইকস্ট্রার ব্যাঙ্কার্স অ্যালগরিদম (Banker's Algorithm): গাণিতিক ডেটা স্ট্রাকচার (Available, Max, Allocation, Need), সেফটি অ্যালগরিদম ও রিসোর্স-রিকোয়েস্ট অ্যালগরিদমের সম্পূর্ণ হ্যান্ড-ক্যালকুলেশন।
- ডেডলক ডিটেকশন ও রিকভারি: ওয়েট-ফর গ্রাফ (Wait-For Graph - WFG), প্রসেস টার্মিনেশন স্ট্র্যাটেজি, রিসোর্স প্রিয়েম্পশন এবং ভিকটিম সিলেকশন।
- আধুনিক ওএসে উটপাখি পদ্ধতি (Ostrich Algorithm): লিনাক্স ও উইন্ডোজ কেন প্রোডাকশনে ওস্ট্রিচ মেথড ব্যবহার করে।

### ৬. [Phase 06: মেমরি ম্যানেজমেন্ট: পেজিং, সেগমেন্টেশন ও ফিজিক্যাল অ্যাড্রেসিং](./Phase_06_Memory_Management_Paging_and_Segmentation.md)
- লজিক্যাল বনাম ফিজিক্যাল অ্যাড্রেস স্পেস, মেমরি ম্যানেজমেন্ট ইউনিট (MMU), বেস এবং লিমিট রেজিস্টার।
- কনটিগুয়াস মেমরি অ্যালোকেশন ও ফ্র্যাগমেন্টেশন: ইন্টারনাল বনাম এক্সটারনাল ফ্র্যাগমেন্টেশন, ডাইনামিক স্টোরেজ অ্যালোকেশন (First Fit, Best Fit, Worst Fit), মেমরি কম্প্যাকশন।
- সেগমেন্টেশন আর্কিটেকচার: সেগমেন্ট টেবিল, প্রটেকশন বিটস, সেগমেন্টেশন ফল্ট।
- পেজিং ফান্ডামেন্টালস: ফ্রেম, পেজ, পেজ টেবিল আর্কিটেকচার, পেজ নাম্বার ($p$) ও অফসেট ($d$) অ্যাড্রেস ট্রান্সলেশন।
- ট্রান্সলেশন লুকাসাইড বাফার (TLB): অ্যাসোসিয়েটিভ হার্ডওয়্যার মেমরি, টিএলবি হিট/মিস ল্যাটেন্সি, ইফেক্টিভ মেমরি অ্যাক্সেস টাইম (EMAT) গাণিতিক হিসাব।
- মাল্টি-লেভেল পেজিং (Hierarchical Paging): 32-bit x86 টু-লেভেল পেজিং এবং 64-bit x86-64 ফোর-লেভেল/ফাইভ-লেভেল পেজিং (PML4, PGD, P4D, PUD, PMD, PTE) ট্রান্সলেশন আর্কিটেকচার ও মেমরি ওভারহেড ক্যালকুলেশন।
- ইনভার্টেড পেজ টেবিল (Inverted Page Table) ও হ্যাশড পেজ টেবিল: ট্রেড-অফ ও মেমরি সেভিংস।

### ৭. [Phase 07: ভার্চুয়াল মেমরি, পেজ রিপ্লেসমেন্ট অ্যালগরিদম ও স্ল্যাব অ্যালোকেটর](./Phase_07_Virtual_Memory_Page_Replacement_and_Allocation.md)
- ডিমান্ড পেজিং (Demand Paging): হার্ডওয়্যার পেজ ফল্ট ট্র্যাপ থেকে শুরু করে ডিস্ক রিড ও ইনস্ট্রাকশন রিস্টার্ট পর্যন্ত ১২-ধাপের সম্পূর্ণ সিকোয়েন্স।
- ডিমান্ড পেজিং পারফরম্যান্স: পেজ ফল্ট রেট ($p$) এবং ইফেক্টিভ অ্যাক্সেস টাইম (EAT) ইকুয়েশন।
- পেজ রিপ্লেসমেন্ট অ্যালগরিদম:
  - FIFO এবং বেলাডির অসঙ্গতি (Belady's Anomaly - রেফারেন্স স্ট্রিং `1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5` দিয়ে গাণিতিক প্রমাণ)
  - অপ্টিমাল পেজ রিপ্লেসমেন্ট (OPT / MIN)
  - Least Recently Used (LRU) - হার্ডওয়্যার কাউন্টার বনাম ডাবল-লিঙ্কড লিস্ট স্ট্যাক ইমপ্লিমেন্টেশন
  - LRU অ্যাপ্রক্সিমেশন: Second-Chance (Clock Algorithm), Enhanced Second-Chance (Reference & Dirty bits)
  - কাউন্টিং ভিত্তিক: LFU এবং MFU।
- ফ্রেম অ্যালোকেশন ও থ্র্যাশিং (Thrashing): থ্র্যাশিংয়ের কারণ, ওয়ার্কিং-সেট মডেল (Working-Set Model $\Delta$), পেজ-ফল্ট ফ্রিকোয়েন্সি (PFF) স্ট্র্যাটেজি।
- লিনাক্স কার্নেল মেমরি অ্যালোকেশন: বাডি সিস্টেম (Buddy Allocator for physical pages), স্ল্যাব/স্লাব/স্লব অ্যালোকেটর (Slab/Slub/Slob Allocators for kernel objects), সোয়াপিং (Swap Space) ও আউট-অব-মেমরি কিলার (OOM Killer badness score)।

### ৮. [Phase 08: ফাইল সিস্টেমস, স্টোরেজ আর্কিটেকচার ও আই/ও সাবসিস্টেম](./Phase_08_File_Systems_and_Storage_Architecture.md)
- স্টোরেজ হার্ডওয়্যার মেকানিক্স: HDD ফিজিক্স (Seek Time, Rotational Latency, Transfer Time), NVMe/SSD NAND Flash (Erase Blocks, Wear Leveling, TRIM)।
- ডিস্ক শিডিউলিং অ্যালগরিদম (ট্র্যাক ট্রাভার্সাল ক্যালকুলেশন সহ): FCFS, SSTF, SCAN (Elevator Algorithm), C-SCAN, LOOK, C-LOOK।
- ফাইল কনসেপ্ট, অ্যাক্সেস মেথড ও ডিরেক্টরি স্ট্রাকচার (ট্রি বনাম অ্যাসাইক্লিক গ্রাফ, হার্ড লিংক বনাম সিম্বলিক সফট লিংক)।
- ফাইল অ্যালোকেশন মেথডস: কনটিগুয়াস, লিঙ্কড অ্যালোকেশন (FAT - File Allocation Table), এবং ইনডেক্সড অ্যালোকেশন।
- ইউনিক্স/লিনাক্স ইনোড (Inode) আর্কিটেকচার: মোড, পারমিশন, টাইমস্ট্যাম্প, ডাইরেক্ট পয়েন্টার, সিঙ্গেল/ডাবল/ট্রিপল ইনডাইরেক্ট পয়েন্টার দিয়ে সর্বোচ্চ ফাইল সাইজ গাণিতিক ক্যালকুলেশন।
- ভার্চুয়াল ফাইল সিস্টেম (VFS): সুপারব্লক (`super_block`), ইনোড (`inode`), ডেন্ট্রি (`dentry`), এবং ফাইল (`file`) স্ট্রাকচার অবজেক্টস।
- মডার্ন জার্নালিং ফাইল সিস্টেম: ext4 (Extents, Journaling মোডস: Journal, Ordered, Writeback), ZFS এবং Btrfs (Copy-on-Write, স্ন্যাপশট, চেকসাম)।
- রেড আর্কিটেকচার (RAID): RAID 0, RAID 1, RAID 5 (XOR Parity Math), RAID 6 (Dual Parity Reed-Solomon), RAID 10।
- আই/ও সাবসিস্টেম: পোলিং, ইন্টারাপ্ট-ড্রিভেন আই/ও, ডিরেক্ট মেমরি অ্যাক্সেস (DMA), স্ক্যাটার-গ্যাদার আই/ও, এবং অ্যাসিনক্রোনাস লিনাক্স আই/ও (`epoll` বনাম মডার্ন `io_uring`)।

### ৯. [Phase 09: লিনাক্স কার্নেল ইন্টারনালস, কন্টেইনারাইজেশন ও ভার্চুয়ালাইজেশন](./Phase_09_Linux_Kernel_Internals_and_Containers.md)
- লিনাক্স কার্নেল আর্কিটেকচার: সাবসিস্টেমস, লোডেবল কার্নেল মডিউল (LKM - `init_module`, `cleanup_module`), `dmesg`, `insmod`/`rmmod`।
- লিনাক্স নেমস্পেস (Namespaces - কন্টেইনার আইসোলেশন ইঞ্জিন): PID, Mount, Net, IPC, UTS, User, এবং Cgroup নেমস্পেস।
- লিনাক্স কন্ট্রোল গ্রুপস (cgroups v1 বনাম v2): সিপিইউ কোটা, মেমরি ও আই/ও ব্যান্ডউইথ রেট লিমিটিং মেকানিক্স।
- ডকার এবং ওআইসি (OCI) কন্টেইনার কীভাবে কাজ করে: `clone()` সিস্টেম কল, `unshare()`, `setns()`, `pivot_root()`, এবং OverlayFS (LowerDir, UpperDir, MergedDir, WorkDir)।
- ভার্চুয়ালাইজেশন মেকানিক্স: টাইপ-১ হাইপারভাইজার (Bare-metal: KVM, Xen, ESXi) বনাম টাইপ-২ (VirtualBox), হার্ডওয়্যার-অ্যাসিস্টেড ভার্চুয়ালাইজেশন (Intel VT-x, AMD-V, EPT/NPT, VMCS), প্যারাভার্চুয়ালাইজেশন (VirtIO)।
- সিস্টেম সিকিউরিটি স্যান্ডবক্সিং: DAC (Discretionary Access Control) বনাম MAC (Mandatory Access Control: SELinux, AppArmor), Seccomp-BPF (Secure Computing ফিল্টার), এবং লিনাক্স ক্যাপাবিলিটিস (Capabilities)।

### ১০. [Phase 10: রিয়েল-টাইম সিস্টেমস, ডিস্ট্রিবিউটেড ওএস ও এমএনসি ইন্টারভিউ ব্লুপ্রিন্ট](./Phase_10_Real_Time_Systems_and_MNC_Interview_Scenarios.md)
- রিয়েল-টাইম অপারেটিং সিস্টেম (RTOS): হার্ড বনাম সফট রিয়েল-টাইম, রেট মনোটোনিক শিডিউলিং (RMS - Liu & Layland bound $U \le n(2^{1/n}-1)$), Earliest Deadline First (EDF) ডাইনামিক অপ্টিমাল শিডিউলিং।
- প্রায়োরিটি ইনভার্সন (Priority Inversion Problem) এবং প্রায়োরিটি ইনহেরিটেন্স প্রোটোকল (Priority Inheritance Protocol - মার্স পাথফাইন্ডার নভোযান ডিজাস্টারের কেস স্টাডি)।
- ডিস্ট্রিবিউটেড ওএস ও ক্লাউড সিস্টেমস: ল্যাম্পোর্ট লজিক্যাল ক্লক, ভেক্টর ক্লক, নেটওয়ার্ক ফাইল সিস্টেম (NFS), রিমোট প্রসিডিউর কল (gRPC/RPC)।
- লিনাক্স পারফরম্যান্স প্রোফাইলিং টুলকিট: `top`, `htop`, `vmstat`, `iostat`, `perf`, এবং আধুনিক eBPF (`bpftrace`, BCC) ফ্রেমগ্রাফস (FlameGraphs)।
- শীর্ষ ১০টি এমএনসি সিস্টেমস ইন্টারভিউ দৃশ্যপট ও গভীর প্রশ্ন বিশ্লেষণ:
  1. শেল-এ কমান্ড লিখে Enter চাপলে কার্নেল লেভেলে কী ঘটে? (Fork-Exec-Wait, TTY, Signals, File Descriptors)
  2. পেজ ফল্ট হলে হার্ডওয়্যার ট্র্যাপ থেকে প্রসেস রিজুমিং পর্যন্ত পূর্ণাঙ্গ স্টেপ-বাই-স্টেপ বিশ্লেষণ।
  3. `malloc()` এবং `free()` কীভাবে কাজ করে? (`brk`/`sbrk` বনাম `mmap`, glibc ptmalloc chunks, arenas, bins, double free vulnerability)
  4. স্পিনলক বনাম মিউটেক্স: মাল্টি-কোর ক্যাশ কোহেরেন্সি পেনাল্টি ও ল্যাটেন্সি অ্যানালাইসিস।
  5. `fork()` এর সময় Copy-on-Write (COW) কীভাবে কাজ করে এবং কেন এটি আবশ্যক?
  6. লিনাক্স `epoll` কেন `select()` এবং `poll()`-এর চেয়ে $O(1)$ পারফরম্যান্স দেয়?
  7. সি প্রোগ্রামের পূর্ণাঙ্গ মেমরি লেআউট এবং বাফার ওভারফ্লো প্রতিরোধে ASLR, DEP/NX, Stack Canaries কীভাবে কাজ করে?
  8. জম্বি (Zombie) বনাম অরফ্যান (Orphan) প্রসেস: PID 1 কেন অরফ্যান প্রসেসকে অ্যাডপ্ট করে এবং রীপ করে?
  9. ইন্টারাপ্ট টপ-হাফ (Top-half) বনাম বটম-হাফ (Bottom-half / SoftIRQ, Tasklets, Workqueues)।
  10. থ্রেড-সেফ কনকারেন্ট এলআরইউ ক্যাশ (Thread-safe Concurrent LRU Cache with $O(1)$ Complexity) সিস্টেম ডিজাইন।

---

## ⚡ কার্নেল আর্কিটেকচার তুলনা ম্যাট্রিক্স (OS Architecture Matrix)

| বৈশিষ্ট্য (Feature) | মনোলিথিক কার্নেল (Monolithic) | মাইক্রোকার্নেল (Microkernel) | হাইব্রিড কার্নেল (Hybrid) | এক্সোকার্নেল / ইউনিক্যার্নেল (Unikernel) |
| :--- | :--- | :--- | :--- | :--- |
| **বাস্তব উদাহরণ** | Linux, FreeBSD | MINIX 3, seL4, QNX, Fuchsia Zircon | Windows NT, macOS XNU | MirageOS, OSv, Unikraft |
| **কার্নেল স্পেসে যা চলে** | VFS, IPC, Drivers, Scheduler, Network Stack | শুধুই IPC, Scheduler, Basic Memory Paging | Drivers, Executive Services, VFS, Micro-IPC | শুধুমাত্র একটি সিঙ্গেল অ্যাপ্লিকেশন ও প্রয়োজনীয় ড্রাইভার |
| **ইউজার স্পেসে যা চলে** | ইউজার অ্যাপ্লিকেশনস ও ইউটিলিটি | File Systems, Device Drivers, Network Stack | ইউজার অ্যাপ্লিকেশনস, Win32/POSIX সাবসিস্টেম | কোনো প্রটেকশন রিং বিভাজন নেই (সবকিছু Ring 0-তে) |
| **কনটেক্সট সুইচ ও IPC ওভারহেড**| খুব কম (ডিরেক্ট ফাংশন কল ও স্ট্রাকচার পাসিং)| অনেক বেশি (বারংবার Message Passing ও মোড সুইচ)| মাঝারি (ইন-কার্নেল ড্রাইভার, বাট মডুলার)| শূন্য (কোনো ট্র্যাপ বা মোড সুইচ নেই)|
| **সিকিউরিটি ও স্ট্যাবিলিটি** | একটি ড্রাইভার ক্র্যাশ করলে পুরো সিস্টেম ক্র্যাশ করে| উচ্চ আইসোলেশন; ক্র্যাশড ড্রাইভার সহজেই রিস্টার্ট হয়| ভালো কিন্তু ড্রাইভার বাগে ব্লু স্ক্রিন (BSOD) হয় | অতি ক্ষুদ্র অ্যাটাক সারফেস; ক্লাউড স্পেসিফিক |
| **কোডবেস সাইজ** | বিশাল (>৩০ মিলিয়ন লাইন লিনাক্স কোড) | ক্ষুদ্র (~১০ হাজার লাইন কোড seL4) | বিশাল | অতি ক্ষুদ্র (কয়েক মেগাবাইট)|

---

## 💡 ইন্টারভিউ প্রস্তুতি ও ব্যবহারের নির্দেশিকা
১. **গাণিতিক সমীকরণ ও ডেরিভেশনসমূহ নিজে হাতে লিখে প্র্যাকটিস করুন:** বিশেষ করে Bounded Buffer-এর সেমাফোর ইনভ্যারিয়েন্ট, Banker's Algorithm-এর Need Matrix, Effective Memory Access Time (EMAT), এবং Multilevel Paging Address Bits ডিভিশন।
২. **লিনাক্স সোর্স কনসেপ্ট লিংক করুন:** প্রতিটি ফেজে লিনাক্স কার্নেলের বাস্তব ডেটা স্ট্রাকচার (`task_struct`, `mm_struct`, `file_operations`, `cfs_rq`) রেফারেন্স হিসেবে দেওয়া হয়েছে।
৩. প্রতিটি ফেজ সম্পূর্ণ ডিপেন্ডেন্সি-মুক্ত এবং স্বয়ংসম্পূর্ণ। আপনি ক্রমানুসারে Phase 01 থেকে 10 পর্যন্ত অধ্যয়ন করতে পারেন।