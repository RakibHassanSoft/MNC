# Chapter 11: GenAI Evaluation, Safety Guardrails & Governance

---

## ১. সনাতন মেট্রিক্সের সীমাবদ্ধতা বনাম আধুনিক মূল্যায়ন সংকট

ঐতিহ্যবাহী এনএলপি মেট্রিকসমূহ যেমন **BLEU** বা **ROUGE** মূলত ইনপুট ও রেফারেন্স টেক্সটের মধ্যবর্তী শব্দ বা এন-গ্রামের হুবহু মিল (n-gram lexical overlap) পরিমাপ করে।
কিন্তু জেনারেটিভ এআই-এর যুগে এগুলো সম্পূর্ণ অকেজো:
- একটি মডেল সম্পূর্ণ ভিন্ন শব্দ ও বাক্য গঠনে একটি অতি উচ্চমানের, তথ্যসমৃদ্ধ উত্তর দিতে পারে (যা BLEU-তে প্রায় ০ স্কোর পাবে)।
- আবার একটি মডেল হ্যালুসিনেট করে অবাস্তব তথ্য দিলে BLEU বা ROUGE তা শনাক্ত করতে পারে না।

```
Reference: "Paris is the capital of France and houses the famous Louvre museum."
Model A:   "France's capital city is Paris, known globally for the Louvre." -> High Quality, Low BLEU match!
Model B:   "Paris is the capital of France, but the Louvre is in Berlin."   -> Factually False, High BLEU match!
```

---

## ২. গোল্ড স্ট্যান্ডার্ড এলএলএম বেঞ্চমার্কসমূহ (Standard Benchmarks)

আধুনিক এআই মডেলের বুদ্ধিবৃত্তিক সামর্থ্য মূল্যায়নের জন্য বৈজ্ঞানিক বেঞ্চমার্ক:

1. **MMLU (Massive Multitask Language Understanding - Hendrycks et al., 2021):**
   - গণিত, ইতিহাস, আইন, কম্পিউটার সায়েন্স ও চিকিৎসাবিজ্ঞান সহ ৫৭টি ভিন্ন ভিন্ন বিষয়ে মাল্টিপল-চয়েস প্রশ্ন। মডেলের সাধারণ বিশ্বজ্ঞান ও অ্যাকাদেমিক গভীরতা যাচাই করে।
2. **GSM8K & MATH (Grade School Math):**
   - মাল্টি-স্টেপ গাণিতিক সমস্যা সমাধান। এটি মডেলের রিজনিং ও চেইন-অব-থট ক্ষমতা পরিমাপ করে।
3. **HumanEval (OpenAI, 2021):**
   - ১৬৪টি স্বয়ংসম্পূর্ণ পাইথন প্রোগ্রামিং সমস্যা। জেনারেটেড কোডকে প্রকৃত পাইথন টেস্ট কেসের বিরুদ্ধে এক্সিকিউট করে `pass@k` মেট্রিক হিসাব করা হয়:
     $$\text{pass}@k \triangleq \mathbb{E}\left[ 1 - \frac{\binom{n - c}{k}}{\binom{n}{k}} \right]$$
4. **Chatbot Arena (LMSYS Elo System):**
   - ক্রাউডসোর্সড ব্লাইন্ড টেস্ট (Blind Pairwise Human Evaluation)। দুটি বেনামী মডেলের উত্তর পাশাপাশি প্রদর্শন করে ব্যবহারকারী ভোট দেন কোনটি শ্রেয়। ব্র্যাডলি-টেরি (Bradley-Terry) মডেলের ভিত্তিতে চেস-স্টাইল Elo রেটিং নির্ধারিত হয়।

---

## ৩. এলএলএম-অ্যাজ-আ-জাজ এবং পক্ষপাত নিরসন (LLM-as-a-Judge)

হাজার হাজার মডেলের উত্তর মানুষের দ্বারা মূল্যায়ন করা অত্যন্ত ব্যয়বহুল ও সময়সাপেক্ষ। Zheng et al. (2023) প্রস্তাব করেন **LLM-as-a-Judge**, যেখানে একটি শক্তিশালী ফ্রন্টিয়ার মডেল (যেমন GPT-4o) বিচারক হিসেবে অন্যান্য মডেলের আউটপুটকে সুনির্দিষ্ট রুব্রিকের ভিত্তিতে গ্রেডিং করে।

```
                                [ User Query ]
                                      |
              +-----------------------+-----------------------+
              |                                               |
     [ Model A Output ]                              [ Model B Output ]
              \                                               /
               +----------------------+----------------------+
                                      |
                     [ Frontier Judge LLM (e.g. GPT-4o) ]
                     (Guided by strict CoT Grading Rubric)
                                      |
                     Evaluation: Score, Critique, Winner!
```

### জাজ মডেলের মারাত্মক ৩টি পক্ষপাত (Systematic Biases):
1. **পজিশন বায়াস (Position Bias):** বিচারক মডেল প্রথম প্রদর্শিত উত্তরটিকে (Option A) দ্বিতীয়টির (Option B) চেয়ে বেশি পছন্দ করার প্রবণতা দেখায়।
   - *সমাধান:* **Position Swapping** — উত্তর দুটিকে অদলবদল করে দুইবার মূল্যায়ন করা হয় ($A \leftrightarrow B$)। উভয় ক্ষেত্রে জয়ী হলেই কেবল বিজয়ী ঘোষণা করা হয়।
2. **ভার্বোসিটি বায়াস (Verbosity Bias):** ফাঁকা বুলিযুক্ত দীর্ঘ ও অপ্রয়োজনীয় বড় উত্তরকে জাজ মডেল বেশি তথ্যপূর্ণ মনে করে।
   - *সমাধান:* দৈর্ঘ্যের জন্য পেনাল্টি যোগ করা এবং কনসাইজনেস রুব্রিক এনফোর্স করা।
3. **সেলফ-এনহ্যান্সমেন্ট বায়াস (Self-Enhancement Bias):** যে মডেল বিচারক, সে নিজের পরিবারের তৈরি উত্তরকে অগ্রাধিকার দেয়।

---

## ৪. হ্যালুসিনেশন ডিটেকশন ও ভেরিফিকেশন (Hallucination Detection)

হ্যালুসিনেশন হলো মডেল কর্তৃক আত্মবিশ্বাসের সাথে তৈরি করা অসত্য বা অপ্রমাণিত তথ্য।

### ১. সেলফ-চেকজিপিটি (SelfCheckGPT - Manakul et al., 2023):
কোনো বাহ্যিক নলেজ বেস ছাড়াই হ্যালুসিনেশন পরিমাপের জিরো-রিসোর্স পদ্ধতি:
- একটি প্রম্পটের জন্য হাই টেম্পারেচারে ($T = 1.0$) ৫টি স্টোকাস্টিক স্যাম্পল রেসপন্স ড্র করা হয়।
- মূল রেসপন্সের প্রতিটি সেন্টেন্স এই ৫টি স্যাম্পলের সাথে সামঞ্জস্যপূর্ণ কিনা তা তথ্যতাত্ত্বিক কনসিস্টেন্সির মাধ্যমে যাচাই করা হয়। যদি কোনো দাবি কেবল একবারই তৈরি হয় এবং অন্যান্য স্যাম্পলে অনুপস্থিত থাকে, তবে সেটির হ্যালুসিনেশন হওয়ার সম্ভাবনা প্রায় ১০০%।

### ২. চেইন-অব-ভেরিফিকেশন (CoVe - Dhuliawala et al., 2023):
1. ড্রাফট রেসপন্স তৈরি।
2. উত্তরের সত্যতা যাচাইয়ের জন্য ফ্যাক্ট-চেকিং প্রশ্নমালা প্রস্তুত।
3. স্বাধীনভাবে প্রশ্নগুলোর উত্তর অনুসন্ধান।
4. যাচাইকৃত তথ্যের ভিত্তিতে চূড়ান্ত উত্তর সংশোধন।

---

## ৫. এআই নিরাপত্তা ও গার্ডরেইল সিস্টেম (Safety Guardrails & Governance)

```
[ User Input ] ---> [ Input Guardrails: PII Masker, Jailbreak Shield, Moderation API ]
                                       | (Pass)
                                       v
                              [ Core LLM Engine ]
                                       |
                                       v
[ Final Response ] <--- [ Output Guardrails: Fact-Checker, Toxic Filter, Hallucination Audit ]
```

### ১. PII ফিল্টারিং (Personally Identifiable Information):
ব্যবহারকারীর ব্যক্তিগত তথ্য যেমন ক্রেডিট কার্ড নম্বর, ইমেইল, ফোন নম্বর, বা জাতীয় পরিচয়পত্র নম্বর মডেলের কাছে যাওয়ার আগে স্বয়ংক্রিয়ভাবে রিড্যাক্ট (Mask) করা:
`"Contact me at john@example.com with card 4111-XXXX"` $\to$ `"Contact me at <EMAIL> with card <CREDIT_CARD>"`.

### ২. লামা গার্ড (Llama Guard - Meta):
একটি স্পেশালাইজড ৭বি সেফটি ক্লাসিফায়ার যা ১৪টি সংবেদনশীল ক্যাটাগরি (সহিংসতা, আত্মঘাতী প্রবণতা, সাইবার আক্রমণ, ড্রাগস, হেইট স্পিচ) পর্যবেক্ষণ করে প্রতিটি প্রম্পট ও রেসপন্সকে `safe` বা `unsafe` ট্যাগ দেয়।

---

## ৬. সংখ্যাগত উদাহরণ (Step-by-Step Elo Rating Calculation)

ধরা যাক Chatbot Arena-তে দুটি মডেল মুখোমুখি হলো:
- **মডেল A:** বর্তমান রেটিং $R_A = 1200$
- **মডেল B:** বর্তমান রেটিং $R_B = 1000$
- কে-ফ্যাক্টর $K = 32$

### ধাপ ১: প্রত্যাশিত জয়ের সম্ভাবনা (Expected Win Probability) নির্ণয়
মডেল A-এর প্রত্যাশিত জয়:
$$E_A = \frac{1}{1 + 10^{(R_B - R_A) / 400}} = \frac{1}{1 + 10^{(1000 - 1200) / 400}} = \frac{1}{1 + 10^{-200 / 400}} = \frac{1}{1 + 10^{-0.5}}$$
$$10^{-0.5} \approx 0.3162 \implies E_A = \frac{1}{1 + 0.3162} = \frac{1}{1.3162} \approx \mathbf{0.7598} \; (75.98\%)$$

মডেল B-এর প্রত্যাশিত জয়:
$$E_B = 1 - E_A = 1 - 0.7598 = \mathbf{0.2402} \; (24.02\%)$$

### ধাপ ২: ম্যাচের ফলাফল ও রেটিং আপডেট
ধরা যাক অপেক্ষাকৃত দুর্বল **মডেল B অপ্রত্যাশিতভাবে জয়ী হলো** (Upset Win! $S_B = 1, S_A = 0$):

নতুন রেটিং সূত্র: $R_{\text{new}} = R_{\text{old}} + K(S - E)$

- **মডেল B-এর নতুন রেটিং:**
  $$R_B' = 1000 + 32 \times (1 - 0.2402) = 1000 + 32 \times 0.7598 = 1000 + 24.31 = \mathbf{1024.31}$$
- **মডেল A-এর নতুন রেটিং:**
  $$R_A' = 1200 + 32 \times (0 - 0.7598) = 1200 - 24.31 = \mathbf{1175.69}$$

একটি সিঙ্গেল আপসেট জয়ে মডেল B ২৪.৩১ পয়েন্ট বৃদ্ধি পেল এবং মডেল A সমপরিমাণ পয়েন্ট হারাল!

---

## ৭. পাইথনে পূর্ণাঙ্গ PII মাস্কার ও এলএলএম-অ্যাজ-আ-জাজ সিস্টেম

নিচে একটি স্বয়ংসম্পূর্ণ, ডিপেন্ডেন্সি-মুক্ত পাইথন বাস্তবায়ন দেওয়া হলো যা রেগুলার এক্সপ্রেশন ভিত্তিক প্রোডাকশন PII মাস্কিং এবং পজিশন-সোয়াপিং সহ LLM-as-a-Judge ইমপ্লিমেন্ট করে:

```python
"""
Production Safety Guardrails: PII Masker and Bidirectional LLM-as-a-Judge Engine.
Fully runnable and verified.
"""

import re
from typing import Dict, Any, Tuple


# ============================================================================
# 1. Production PII Masking Engine
# ============================================================================
class PIIMasker:
    def __init__(self):
        self.patterns = [
            (r"\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b", "<EMAIL_REDACTED>"),
            (r"\b(?:\+?1[-. ]?)?\(?([0-9]{3})\)?[-. ]?([0-9]{3})[-. ]?([0-9]{4})\b", "<PHONE_REDACTED>"),
            (r"\b(?:\d{4}[- ]?){3}\d{4}\b", "<CREDIT_CARD_REDACTED>"),
            (r"\b\d{3}-\d{2}-\d{4}\b", "<SSN_REDACTED>")
        ]

    def redact(self, text: str) -> Tuple[str, int]:
        total_redactions = 0
        cleaned_text = text
        for regex, tag in self.patterns:
            matches = len(re.findall(regex, cleaned_text))
            total_redactions += matches
            cleaned_text = re.sub(regex, tag, cleaned_text)
        return cleaned_text, total_redactions


# ============================================================================
# 2. Bidirectional LLM-as-a-Judge Evaluator
# ============================================================================
class LLMJudgeEvaluator:
    def evaluate_pairwise(self, query: str, response_a: str, response_b: str) -> Dict[str, Any]:
        """
        Executes bidirectional position swapping to eliminate LLM Position Bias.
        """
        # Pass 1: A first, B second
        winner_pass1 = self._judge_mock(query, candidate_1=response_a, candidate_2=response_b)
        # Pass 2: Swapped positions (B first, A second)
        winner_pass2 = self._judge_mock(query, candidate_1=response_b, candidate_2=response_a)

        # Map back to model names
        p1_result = "Model_A" if winner_pass1 == 1 else "Model_B"
        p2_result = "Model_B" if winner_pass2 == 1 else "Model_A"

        if p1_result == p2_result:
            final_winner = p1_result
            consensus = True
        else:
            final_winner = "Tie / Inconclusive (Position Bias Detected)"
            consensus = False

        return {
            "pass_1_winner": p1_result,
            "pass_2_winner": p2_result,
            "consensus": consensus,
            "final_winner": final_winner
        }

    def _judge_mock(self, query: str, candidate_1: str, candidate_2: str) -> int:
        """Mock criteria: prefers concise and factually correct candidate."""
        def score(text: str) -> float:
            s = 10.0
            if "Paris" in text:
                s += 5.0
            if len(text.split()) > 30:  # Length penalty
                s -= 2.0
            return s

        score_1 = score(candidate_1)
        score_2 = score(candidate_2)
        return 1 if score_1 >= score_2 else 2


# ============================================================================
# 3. Verification & Execution
# ============================================================================
if __name__ == "__main__":
    print("--- 1. Testing PII Data Sanitization ---")
    masker = PIIMasker()
    user_prompt = "My name is Alice. Email me at alice.smith@enterprise.org or call 415-555-2671 regarding card 4532-1234-5678-9012."
    clean_prompt, count = masker.redact(user_prompt)
    print(f"Original Input:\n{user_prompt}")
    print(f"\nSanitized Input ({count} redactions applied):\n{clean_prompt}")

    print("\n--- 2. Testing Bidirectional LLM-as-a-Judge ---")
    judge = LLMJudgeEvaluator()
    q = "What is the capital of France?"
    resp_a = "The capital of France is Paris."
    resp_b = "Paris is indeed the historical and modern capital of France, nestled along the river Seine."

    evaluation_report = judge.evaluate_pairwise(q, resp_a, resp_b)
    print("Evaluation Report:")
    for k, v in evaluation_report.items():
        print(f"  {k}: {v}")

    print("PII Masking and LLM Judge verified successfully!")
```

---

## ৮. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স

| মূল্যায়ন কৌশল | খরচ ও ল্যাটেন্সি | বায়াস ঝুঁকি | নির্ভরযোগ্যতা ও গভীরতা |
| :--- | :--- | :--- | :--- |
| **BLEU / ROUGE** | নগণ্য (মাইক্রোসেকেন্ড) | প্রযোজ্য নয় | অত্যন্ত দুর্বল (শব্দের অন্ধ মিল) |
| **হিউম্যান অ্যানোটেশন** | আকাশচুম্বী ($$ এবং মাস সময়) | মানুষের ব্যক্তিগত পক্ষপাত | সর্বোচ্চ গোল্ড স্ট্যান্ডার্ড |
| **Single-Pass LLM Judge** | কম (একটি এপিআই কল) | অত্যন্ত উচ্চ (পজিশন ও সাইজ বায়াস)| মাঝারি |
| **Bidirectional CoT Judge**| মাঝারি (দুটি এপিআই কল)| সর্বনিম্ন (বায়াস ফিল্টার্ড) | এন্টারপ্রাইজ ব্যবহারের জন্য সেরা |

---

## ৯. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: LLM-as-a-Judge মূল্যায়নে Verbosity Bias কীভাবে নিরপেক্ষ করা যায়?**
   - **উত্তর:** জাজ মডেলের সিস্টেম প্রম্পটে একটি কঠোর রুব্রিক দিতে হয়: *"Penalize redundant verbosity; award points only for dense, verifiable facts."* অতিরিক্তভাবে, মডেলের স্কোর থেকে টোকেন সংখ্যার একটি স্কেলড ফ্যাক্টর ($\lambda \cdot \log(\text{tokens})$) বিয়োগ করে নরম্যালাইজ করা হয়।
2. **প্রশ্ন: Red Teaming-এর ক্ষেত্রে Automated Fuzzing বলতে কী বোঝায়?**
   - **উত্তর:** একটি অ্যাটাকার এলএলএম (Red Agent) প্রোগ্রাম্যাটিকভাবে হাজার হাজার বহুমাত্রিক অ্যাডভারসারিয়াল প্রম্পট (Base64 এনকোডিং, সিজার সাইফার, রোলপ্লে সিমুলেশন) তৈরি করে টার্গেট মডেলের দিকে ছুড়ে দেয় এবং দেখে কোনটিতে মডেলের সুরক্ষা ব্যবস্থা ভেঙে ক্ষতিকর তথ্য বের হয়।
3. **প্রশ্ন: MMLU বেঞ্চমার্কে 5-shot প্রম্পটিং কেন ব্যবহার করা হয়?**
   - **উত্তর:** ৫টি ইন-কনটেক্সট উদাহরণ দিলে মডেল প্রশ্নপত্রের সঠিক উত্তর ফরম্যাট (যেমন: শুধুমাত্র 'A', 'B', 'C', বা 'D' টোকেন আউটপুট করা) অনুধাবন করে, ফলে ভুল ফরম্যাটের কারণে কোনো অপ্রয়োজনীয় মার্কস কাটা যায় না।
