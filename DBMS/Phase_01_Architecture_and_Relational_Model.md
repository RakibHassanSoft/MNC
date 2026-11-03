# Phase 01: Core Architecture, Relational Model, Calculus & Keys

---

## ১. ফাইল সিস্টেম বনাম ডিবিএমএস: অপারেটিং সিস্টেম লেভেলের অভ্যন্তরীণ সীমাবদ্ধতা

প্রাথমিক যুগে তথ্য ব্যবস্থাপনার জন্য অপারেটিং সিস্টেমের ফাইল সিস্টেম (যেমন: NTFS, ext4, UFS) ব্যবহৃত হতো। কিন্তু একটি এন্টারপ্রাইজ সিস্টেমের ক্ষেত্রে ফাইল সিস্টেম কেন সম্পূর্ণ অকেজো, তা বুঝতে হলে অপারেটিং সিস্টেমের অভ্যন্তরীণ মেকানিজম বুঝতে হবে।

```
                                  [ FILE SYSTEM VS DBMS ENGINE ]
                                                |
        +---------------------------------------+---------------------------------------+
        |                                                                               |
[ OS File System (e.g. ext4, NTFS) ]                                    [ Enterprise DBMS Engine (Postgres, InnoDB) ]
  - ফাইলকে কেবল একটি আনস্ট্রাকচার্ড বাইট স্ট্রিম                         - স্ট্রাকচার্ড পেজ ফরম্যাট (Slotted Pages, Headers, TIDs)
    (Unstructured Byte Stream) হিসেবে দেখে।                               - ডিক্লেয়ারেটিভ রিলেশনাল কুয়েরি অপটিমাইজেশন (CBO)
  - কুয়েরি প্রসেসিং বা ইনডেক্সিং নেই (Full File Read)।                  - পেজ-লেভেল, রো-লেভেল এবং মাল্টি-ভার্সন আইসোলেশন
  - ফাইল-লেভেল লক (একটি রাইট সম্পূর্ণ ফাইল লক করে)।                     - অ্যাটোমিসিটি ও ডুরাবিলিটির জন্য WAL ও ARIES প্রোটোকল
  - ক্র্যাশ হলে ডেটা করাপশনের চরম ঝুঁকি (No WAL)।                         - ফিল্ড ও কলাম লেভেল অ্যাক্সেস কন্ট্রোল ও সিকিউরিটি
```

### ফাইল সিস্টেমের ৭টি প্রযুক্তিগত ঘাটতি (Detailed OS-Level Analysis):
1. **ডেটা রিডানড্যান্সি ও ইনকনসিস্টেন্সি (Redundancy & Inconsistency):**
   - বিভিন্ন ডিপার্টমেন্ট একই ডেটার অনুলিপি ভিন্ন ভিন্ন ফাইলে রাখে। ডেটা আপডেট করার সময় একটি ফাইলে পরিবর্তন হলেও অন্য ফাইলে পুরনো ডেটা থেকে যায়। ফলে ডেটাবেসের "Single Source of Truth" নষ্ট হয়।
2. **অ্যাক্সেসের জটিলতা (Data Access Complexity):**
   - ফাইল সিস্টেমে নির্দিষ্ট কোনো ফিল্টারের জন্য ডিক্লেয়ারেটিভ কোনো ব্যবস্থা নেই। প্রতিবার একটি নতুন শর্তে সার্চ করতে হলে সি/সি++ বা পাইথন দিয়ে ফাইল ওপেন করে পয়েন্টার ঘুরিয়ে লিনিয়ার স্ক্যান চালাতে হয়।
3. **কনকারেন্ট অ্যাক্সেস ও লক এস্কেলেশন (Lock Contention):**
   - ফাইল সিস্টেমের লকিং গ্র্যানুলারিটি অত্যন্ত স্থূল (Coarse-grained)। একজন ইউজার একটি ফাইলের একটি সিঙ্গেল লাইনে লিখলে সম্পূর্ণ ফাইল লক হয়ে যায়। ডিবিএমএস রো-লেভেল (Row-level) লকিং এবং MVCC প্রদান করে, ফলে লক্ষ লক্ষ ইউজার একই টেবিলে কনকারেন্টলি রিড/রাইট করতে পারে।
4. **অ্যাটোমিসিটি ও ডুরাবিলিটি ঘাটতি (Crash Vulnerability):**
   - ফাইল রাইট করার সময় বিদ্যুৎ চলে গেলে বা ওএস ক্র্যাশ করলে ফাইল অর্ধেক লিখিত অবস্থায় করাপ্ট (Torn Write) হয়ে যায়। ডেটাবেস ইঞ্জিন Write-Ahead Logging (WAL) এবং ডাবল-রাইট বাফার (Doublewrite Buffer) ব্যবহার করে শতভাগ অ্যাটোমিক রিকভারি নিশ্চিত করে।
5. **ইন্টিগ্রিটি কনস্ট্রেইন্ট প্রয়োগের অভাব:**
   - অ্যাকাউন্টের ব্যালেন্স ঋণাত্মক হতে পারবে না বা বয়স ১৮ এর নিচে হতে পারবে না—এই ধরনের লজিক্যাল কনস্ট্রেইন্ট ফাইল সিস্টেমে নেই।
6. **ডেটা আইসোলেশন ও ফরম্যাট ফ্র্যাগমেন্টেশন:**
   - ডেটা বিভিন্ন অ্যাপ্লিকেশনের দ্বারা বিভিন্ন বাইনারি বা টেক্সট স্ট্রাকচারে সংরক্ষিত হওয়ায় একাধিক ফাইলের ডেটা একত্রিত করে জয়েন করা অসম্ভব হয়ে পড়ে।
7. **সিকিউরিটি ও ভিউ আইসোলেশনের অভাব:**
   - ওএস কেবল ফাইল রিড/রাইট পারমিশন দিতে পারে। কিন্তু টেবিলের নির্দিষ্ট কলাম (যেমন: গ্রাহকের পাসওয়ার্ড বা ক্রেডিট কার্ডের CVV) গোপন রাখার কোনো কলাম-লেভেল সিকিউরিটি ফাইল সিস্টেমে সম্ভব নয়।

---

## ২. ৩-লেয়ার ANSI-SPARC আর্কিটেকচার ও ডেটা স্বাধীনতা (Data Independence)

১৯৭৫ সালে ANSI-SPARC ডেটাবেস সিস্টেমকে তিনটি স্বতন্ত্র স্তরে বিভক্ত করে যাতে ফিজিক্যাল স্টোরেজের পরিবর্তন অ্যাপ্লিকেশনের কোডকে ব্যাহত না করে।

```
                                [ External Level: Views ]
                      +-------------------+   +-------------------+
                      |   View 1 (Admin)  |   |  View 2 (Analyst) |
                      +-------------------+   +-------------------+
                                        \       /
                            Logical Data Independence
                                          v
                               [ Conceptual Level ]
                      +-------------------------------------------+
                      | - Entities, Relationships & Constraints   |
                      | - Logical Schema Definition (DDL)         |
                      +-------------------------------------------+
                                          |
                           Physical Data Independence
                                          v
                                [ Internal Level ]
                      +-------------------------------------------+
                      | - Physical Page Layout (Slotted Pages)    |
                      | - B+ Tree, Hash Indexes, Compression      |
                      | - File Paths, Raw Disk Extents            |
                      +-------------------------------------------+
```

### ম্যাপিং মেকানিজম (The Mappings):
1. **External / Conceptual Mapping:** বাহ্যিক ভিউ থেকে কনসেপচুয়াল স্কিমার মধ্যে ডেটা ট্রান্সলেশন করে।
2. **Conceptual / Internal Mapping:** কনসেপচুয়াল টেবিলগুলোকে ফিজিক্যাল ডিস্ক পেজ ও রেকর্ডে কীভাবে রূপান্তর করা হবে তা পরিচালনা করে।

### ডেটা স্বাধীনতা (Data Independence Analysis):
- **লজিক্যাল ডেটা স্বাধীনতা (Logical Data Independence):**
  - কনসেপচুয়াল স্কিমাতে কোনো টেবিল যুক্ত হলে, টেবিল বিভক্ত হলে বা নতুন অ্যাট্রিবিউট যোগ হলে বিদ্যমান বাহ্যিক ভিউ বা অ্যাপ্লিকেশন কোড পরিবর্তন না করার সামর্থ্য। এটি অর্জন করা অপেক্ষাকৃত জটিল।
- **ফিজিক্যাল ডেটা স্বাধীনতা (Physical Data Independence):**
  - ইন্টারনাল স্টোরেজে নতুন B+ Tree ইনডেক্স তৈরি করলে, ডেটা ব্লক সাইজ ৮KB থেকে ১৬KB করলে, বা ডেটা কম্প্রেশন অ্যালগরিদম বদলালে কনসেপচুয়াল টেবিল বা SQL কোয়েরিতে কোনো পরিবর্তন আনার প্রয়োজন হয় না। আধুনিক রিলেশনাল ডেটাবেস শতভাগ ফিজিক্যাল ডেটা স্বাধীনতা নিশ্চিত করে।

---

## ৩. ডক্টর ই. এফ. কডের ১২টি গোল্ডেন রুলস (Codd's 12 Rules)

১৯৭০ সালে এডগার এফ. কড একটি প্রকৃত রিলেশনাল ডেটাবেস ম্যানেজমেন্ট সিস্টেম (RDBMS) নির্ধারণের জন্য ১২টি মৌলিক নিয়ম প্রস্তাব করেন (নিয়ম ০ থেকে ১২):

```
Rule 0: The Foundation Rule
A system must qualify as relational, as a database, and as a management system solely through its relational capabilities.

Rule 1: The Information Rule
All information in the database is represented explicitly at the logical level in exactly one way — by values in tables (relations).

Rule 2: Guaranteed Access Rule
Every datum (atomic value) is logically addressable by providing: Table Name + Primary Key Value + Column Name.

Rule 3: Systematic Treatment of NULL Values
NULL represents missing or inapplicable data systematically, distinct from zero or empty strings, independent of data type.

Rule 4: Dynamic On-line Catalog Based on the Relational Model
The database metadata (data dictionary) must be stored in relational tables, queryable using standard SQL just like regular user tables.

Rule 5: Comprehensive Data Sublanguage Rule
The system must support at least one well-defined declarative language (like SQL) supporting DDL, DML, view definition, integrity constraints, authorizations, and transactions.

Rule 6: View Updating Rule
All views that are theoretically updatable must also be updatable by the DBMS.

Rule 7: High-level Insert, Update, and Delete (Set-at-a-time)
The system must support set-oriented operations, not just record-at-a-time cursors.

Rule 8: Physical Data Independence
Application programs remain logically intact when physical storage structures or hardware changes.

Rule 9: Logical Data Independence
Application programs remain logically intact when information-preserving changes are made to the base tables.

Rule 10: Integrity Independence
Integrity constraints must be definable in the relational data sublanguage and stored in the catalog, not in application programs.

Rule 11: Distribution Independence
The data manipulation language must function identically whether the database is centralized or distributed across multiple network nodes.

Rule 12: Nonsubversion Rule
If the system provides a low-level interface (e.g., procedural API), that interface cannot be used to bypass or subvert relational security or integrity constraints.
```

---

## ৪. রিলেশনাল ক্যালকুলাস: TRC বনাম DRC (Relational Calculus)

MNC তাত্ত্বিক ইন্টারভিউতে প্রায়ই জানতে চাওয়া হয়: *"রিলেশনাল অ্যালজেব্রা এবং রিলেশনাল ক্যালকুলাসের মধ্যে পার্থক্য কী?"*
- **রিলেশনাল অ্যালজেব্রা (Relational Algebra):** একটি **প্রসিডিউরাল (Procedural)** ভাষা—এটি নির্দিষ্ট করে কোন কোন অপারেশন কোন অর্ডারে চালিয়ে কাঙ্ক্ষিত ফলাফল আনতে হবে।
- **রিলেশনাল ক্যালকুলাস (Relational Calculus):** একটি **নন-প্রসিডিউরাল বা ডিক্লেয়ারেটিভ (Declarative)** ভাষা—এটি কেবল সংজ্ঞায়িত করে আমাদের কোন শর্তের ডেটা প্রয়োজন, কীভাবে আনতে হবে তা বলে না। আধুনিক SQL মূলত রিলেশনাল ক্যালকুলাসের একটি প্রায়োগিক রূপ!

```
                                  [ RELATIONAL FORMALISMS ]
                                              |
      +---------------------------------------+---------------------------------------+
      |                                                                               |
[ Tuple Relational Calculus (TRC) ]                             [ Domain Relational Calculus (DRC) ]
  - ভ্যারিয়েবলগুলো টেবিলের সারি বা টাপল (t) নির্দেশ করে।           - ভ্যারিয়েবলগুলো নির্দিষ্ট অ্যাট্রিবিউট বা ডোমেইন নির্দেশ করে।
  - ফরম্যাট: { t | P(t) }                                          - ফরম্যাট: { <x1, x2, ..., xn> | P(x1, x2, ..., xn) }
```

### ১. টাপল রিলেশনাল ক্যালকুলাস (TRC):
একটি কুয়েরির সাধারণ রূপ:

$$\{ t \mid P(t) \}$$

যেখানে $t$ হলো রেজাল্ট্যান্ট টাপল এবং $P(t)$ হলো ফার্স্ট-অর্ডার প্রেডিকেট লজিক শর্ত (যাতে অস্তিত্ববাচক কোয়ান্টিফায়ার $\exists$ বা সার্বজনীন কোয়ান্টিফায়ার $\forall$ ব্যবহৃত হতে পারে)।
- *উদাহরণ:* "যেসকল কর্মচারীর বেতন $৫০,০০০-এর বেশি তাদের সমস্ত তথ্য দিন:"
  $$\{ t \mid t \in \text{Employees} \land t[\text{salary}] > 50000 \}$$

### ২. ডোমেইন রিলেশনাল ক্যালকুলাস (DRC):
একটি কুয়েরির সাধারণ রূপ:

$$\{ \langle x_1, x_2, \dots, x_n \rangle \mid P(x_1, x_2, \dots, x_n) \}$$

- *উদাহরণ:* "আইটি ডিপার্টমেন্টের কর্মচারীদের নাম ও বেতন দিন:"
  $$\{ \langle n, s \rangle \mid \exists i, d (\langle i, n, d, s \rangle \in \text{Employees} \land d = \text{'IT'}) \}$$

### রিলেশনাল সম্পূর্ণতা (Codd's Relational Completeness):
একটি কুয়েরি ল্যাঙ্গুয়েজকে তখন **রিলেশনালি কমপ্লিট (Relationally Complete)** বলা হয়, যদি তা বেসিক রিলেশনাল অ্যালজেব্রা অথবা সেফ টাপল ক্যালকুলাসে প্রকাশযোগ্য যেকোনো কুয়েরি প্রকাশ করার সমান শক্তিশালী হয়।

---

## ৫. রিলেশনাল অ্যালজেব্রা অপারেটরসমূহ (Mathematical Relational Algebra)

```
Table R: Students                        Table S: Enrolled
+--------+-------+-----+                 +--------+-----------+
| sid    | name  | age |                 | sid    | course_id |
+--------+-------+-----+                 +--------+-----------+
| 1      | Alice | 21  |                 | 1      | CS101     |
| 2      | Bob   | 22  |                 | 1      | CS102     |
| 3      | Carol | 20  |                 | 2      | CS101     |
+--------+-------+-----+                 +--------+-----------+
```

### ১. মৌলিক অপারেটরসমূহ (Fundamental Operators):
1. **সিলেকশন ($\sigma_p(R)$):** শর্ত $p$-এর ওপর ভিত্তি করে টাপল ফিল্টার করে।
   $$\sigma_{\text{age} > 20}(R) \implies \text{Alice (21), Bob (22)}$$
2. **প্রজেকশন ($\pi_{A}(R)$):** নির্দিষ্ট কলাম নির্বাচন করে এবং **স্বয়ংক্রিয়ভাবে ডুপ্লিকেট বাদ দেয়**।
   $$\pi_{\text{name}}(R) \implies \{\text{Alice, Bob, Carol}\}$$
3. **কার্টেসিয়ান প্রোডাক্ট ($R \times S$):**
   $$|R \times S| = |R| \cdot |S| = 3 \times 3 = 9 \text{ tuples}, \quad \text{Degree} = 3 + 2 = 5$$
4. **ইউনিয়ন ($R \cup S$), ইন্টারসেকশন ($R \cap S$), ও ডিফারেন্স ($R - S$):**
   এই সেট অপারেশনগুলো চালানোর জন্য উভয় রিলেশনকে **Union-Compatible (Type Compatible)** হতে হয় (অর্থাৎ তাদের অ্যাট্রিবিউটের সংখ্যা ও ডোমেইনের ধরন হুবহু এক হতে হবে)।

### ২. প্রাপ্ত অপারেটরসমূহ (Derived Operators):
1. **ন্যাচারাল জয়েন ($R \bowtie S$):**
   $$R \bowtie S = \pi_{\text{sid, name, age, course\_id}}(\sigma_{R.\text{sid} = S.\text{sid}}(R \times S))$$
2. **ডিভিশন অপারেটর ($R \div S$ - Universal Quantifier "For All"):**
   ধরা যাক $S = \text{Courses}\{\text{CS101, CS102}\}$।
   "কোন কোন শিক্ষার্থী **সমস্ত** কোর্স সম্পন্ন করেছে?"
   $$\text{Result} = \text{Enrolled} \div \text{Courses} = \{\text{sid: 1 (Alice)}\}$$

---

## ৬. ডেটাবেস কি-সমূহের গভীর শ্রেণিবিন্যাস (Keys Taxonomy)

MNC কোডিং ও আর্কিটেকচার ইন্টারভিউতে কি-এর প্রকারভেদ এবং তাদের পারফরম্যান্স ইমপ্যাক্ট অত্যন্ত গুরুত্বপূর্ণ।

```
                                  [ KEYS TAXONOMY ]
                                          |
        +---------------------------------+---------------------------------+
        |                                                                   |
[ Candidate Keys ]                                                  [ Foreign Keys ]
  - Minimal Super Key (No redundant attribute)                        - Referential integrity enforcement
  - Chosen one -> PRIMARY KEY                                         - Cascading: CASCADE, SET NULL, RESTRICT
  - Unchosen   -> ALTERNATE KEYS
        |
        +---------------------------------+---------------------------------+
        |                                                                   |
[ Natural Keys ]                                                    [ Surrogate Keys ]
  - Business domain data (Email, SSN)                                 - Artificial DB-generated identifiers
  - Disadvantage: Can change, large string                            - Advantage: Compact, immutable
```

### সারোগেট কি-এর আধুনিক রূপরেখা (Surrogate Key Options in High-Scale Systems):

| কি ফরম্যাট (Key Type) | সাইজ (Size) | সময়-ক্রমানুসারী? (Time-Ordered) | স্কেলেবিলিটি ও ডিস্ট্রিবিউটেড সেফ? | পারফরম্যান্স প্রভাব (B+ Tree) |
| :--- | :---: | :---: | :--- | :--- |
| **Auto-Increment BIGINT** | ৮ বাইট | ✅ হ্যাঁ | ❌ না (সিঙ্গেল সেন্ট্রাল ডেটাবেস লাগে) | সর্বোচ্চ (No page splits) |
| **UUID v4 (Random)** | ১৬ বাইট | ❌ সম্পূর্ণ র্যান্ডম | ✅ হ্যাঁ (জিরো কনফ্লিক্ট) | **মারাত্মক ধীর (Severe Page Splits)** |
| **UUID v7 (Time-Ordered)**| ১৬ বাইট | ✅ মিলিসেকেন্ড ক্রমানুসারী | ✅ হ্যাঁ (ডিস্ট্রিবিউটেড পারফেক্ট) | **অসাধারণ (B+ Tree Friendly)** |
| **ULID** | ১৬ বাইট | ✅ মিলিসেকেন্ড ক্রমানুসারী | ✅ হ্যাঁ (Base32 স্ট্রিং) | অসাধারণ |
| **Twitter Snowflake ID** | ৮ বাইট | ✅ হ্যাঁ (Timestamp + Node + Seq)| ✅ হ্যাঁ | সর্বোচ্চ ও কম্প্যাক্ট |

> **[!TIP] MNC Best Practice:**
> ডিস্ট্রিবিউটেড হাই-রাইট সিস্টেমে কখনোই এলোমেলো **UUID v4**-কে প্রাইমারি কি করবেন না! সর্বদা **UUID v7** অথবা **Snowflake ID** ব্যবহার করবেন, কারণ এগুলো সময় অনুসারে সাজানো থাকে ($48$-bit timestamp prefix), ফলে B+ Tree ইনডেক্সে পেজ স্প্লিট ও ফ্র্যাগমেন্টেশন সম্পূর্ণ প্রতিরোধ হয়।

---

## ৭. ইন্টারভিউ প্রশ্নোত্তর (MNC Interview Checklist)

1. **প্রশ্ন: Relational Algebra-তে Selection ($\sigma$) এবং Projection ($\pi$) এর মধ্যে স্থান বিনিময়যোগ্যতা (Commutativity) কখন সম্ভব?**
   - **উত্তর:** সিলেকশন শর্তে যে সমস্ত অ্যাট্রিবিউট ব্যবহৃত হয়েছে ($p$), সেগুলো যদি প্রজেকশন লিস্টের ($A$) অন্তর্ভুক্ত থাকে, তবেই কেবল তারা স্থান বিনিময় করতে পারে:
     $$\pi_A(\sigma_p(R)) = \sigma_p(\pi_A(R)) \quad \text{if and only if } \text{Attributes}(p) \subseteq A$$
     যদি শর্তের কোনো অ্যাট্রিবিউট প্রজেকশনে বাদ পড়ে যায়, তবে আগে প্রজেক্ট করলে সিলেকশন চালানোর মতো ডেটা পাওয়া যাবে না। কুয়েরি অপটিমাইজারে সিলেকশন পুশডাউন করার সময় এটি যাচাই করা হয়।
2. **প্রশ্ন: Foreign Key কলামে ইনডেক্স তৈরি করা কি জরুরি?**
   - **উত্তর:** হ্যাঁ, অত্যন্ত গুরুত্বপূর্ণ! রিলেশনাল ডেটাবেস প্রাইমারি কি-তে স্বয়ংক্রিয়ভাবে B+ Tree ইনডেক্স তৈরি করলেও **ফরেন কি-তে স্বয়ংক্রিয় ইনডেক্স তৈরি করে না**। ফরেন কি কলামে ইনডেক্স না থাকলে প্যারেন্ট টেবিলের সাথে জয়েন করার সময় প্রতিবার Full Table Scan হবে এবং প্যারেন্ট টেবিল থেকে কোনো রেকর্ড ডিলিট করার সময় চাইল্ড টেবিলে রেফারেন্স আছে কিনা তা চেক করতে চাইল্ড টেবিলের সমগ্র পেজ স্ক্যান করতে হবে, যা সিস্টেমকে স্থবির করে দেয়।
3. **প্রশ্ন: Codd's Rule 3 অনুযায়ী `NULL = NULL` এর মান কী?**
   - **উত্তর:** SQL-এ `NULL = NULL` এর মান `TRUE` নয় বরং **`UNKNOWN`** (Three-Valued Logic: True, False, Unknown)। কারণ `NULL` কোনো সুনির্দিষ্ট মান নয়; এটি তথ্যের অনুপস্থিতি (absence of value) নির্দেশ করে। দুটি অজানা তথ্য কখনোই সমান বলা যায় না। এই কারণে SQL-এ চেক করতে হয় `WHERE column IS NULL`।
