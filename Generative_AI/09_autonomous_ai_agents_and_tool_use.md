# Chapter 09: Autonomous AI Agents, Tool Use & Multi-Agent Orchestration

---

## ১. অটোনোমাস এজেন্টের মূল ধারণা ও অ্যানাটমি (Agent Anatomy)

একটি সাধারণ চ্যাটবট কেবলমাত্র ব্যবহারকারীর প্রম্পটের প্যাসিভ উত্তর প্রদান করে। কিন্তু একটি **অটোনোমাস এআই এজেন্ট (Autonomous AI Agent)** একটি নির্দিষ্ট উচ্চস্তরের লক্ষ্য (Goal) অর্জনের জন্য স্বাধীনভাবে পরিবেশ পর্যবেক্ষণ করে, পরিকল্পনা করে, সিদ্ধান্ত নেয় এবং বাহ্যিক টুলস (APIs, ডাটাবেস, কোড ইন্টারপ্রেটার) ব্যবহার করে অ্যাকশন গ্রহণ করে।

```
                                  +------------------------------------+
                                  |            GOAL / PROMPT           |
                                  +------------------------------------+
                                                     |
                                                     v
                                  +------------------------------------+
                                  |            PERCEPTION              |
                                  | (Parses Environment & State Input) |
                                  +------------------------------------+
                                                     |
                                                     v
                         +---------------------------+---------------------------+
                         |                           |                           |
                         v                           v                           v
              +--------------------+      +--------------------+      +--------------------+
              |      PLANNING      | <--> |       MEMORY       | <--> |     ACTION / TOOLS |
              |  - Subgoal Decomp  |      | - Short-Term (CTX) |      | - Web Search       |
              |  - Reflection/Crit |      | - Long-Term (RAG)  |      | - Python Execution |
              +--------------------+      +--------------------+      | - REST API Calls   |
                         |                                            +--------------------+
                         +---------------------------+---------------------------+
                                                     |
                                                     v
                                  +------------------------------------+
                                  |     ENVIRONMENT FEEDBACK / LOOP    |
                                  +------------------------------------+
```

Lilian Weng (2023)-এর কাঠামো অনুযায়ী একটি এজেন্টের ৪টি প্রধান স্তম্ভ:
1. **প্ল্যানিং (Planning):** একটি জটিল লক্ষ্যকে ছোট ছোট সাব-গোল বা পদক্ষেপে বিভক্ত করা এবং অতীত ভুলের আলোকে পুনরায় পরিকল্পনা করা।
2. **মেমোরি (Memory):**
   - *Short-Term Memory:* বর্তমান চ্যাট হিস্ট্রি এবং ইন-কনটেক্সট এক্সিকিউশন ট্রেইস।
   - *Long-Term Memory:* ভেক্টর ডেটাবেসে সংরক্ষিত অতীত সেশনের অভিজ্ঞতা (Episodic Memory)।
3. **টুলস ও ফাংশন কলিং (Tools / Tool Use):** ক্যালকুলেটর, ওয়েব ব্রাউজার, টার্মিনাল এক্সিকিউশন ও এপিআই কলের মাধ্যমে বাস্তব বিশ্বে প্রভাব ফেলা।
4. **অ্যাকশন লুপ (Action Execution):** পরিবেশের ফলাফল পর্যবেক্ষণ করে পরবর্তী পদক্ষেপ নির্ধারণ করা।

---

## ২. ফাংশন কলিং মেকানিক্স (Function Calling & JSON Schema)

আধুনিক ফ্রন্টিয়ার মডেলগুলো (GPT-4o, Claude 3.5 Sonnet, Gemini 1.5 Pro) বিশেষ টোকেন এবং JSON স্কিমা ব্যবহারের মাধ্যমে টুল কলের অনুরোধ জানায়।

### টুল ডেফিনিশন (JSON Schema):
```json
{
  "type": "function",
  "function": {
    "name": "execute_sql_query",
    "description": "Executes a read-only SQL query against the customer database.",
    "parameters": {
      "type": "object",
      "properties": {
        "query": {
          "type": "string",
          "description": "The SELECT SQL query string."
        }
      },
      "required": ["query"]
    }
  }
}
```

### আন্ডার-দ্য-হুড এক্সিকিউশন প্রবাহ:
1. ইউজার প্রম্পট এবং টুলের স্কিমা মডেলে পাঠানো হয়।
2. মডেল টেক্সট জেনারেট না করে একটি বিশেষ স্টপ রিজন (`tool_calls`) সহ নির্দিষ্ট আর্গুমেন্ট জেনারেট করে:
   `{"name": "execute_sql_query", "arguments": "{\"query\": \"SELECT count(*) FROM users;\"}"}`
3. ব্যাকএন্ড সিস্টেম মডেলের অনুরোধকৃত ফাংশনটি নিরাপদে এক্সিকিউট করে।
4. ফাংশনের ফলাফল একটি নতুন মেসেজ (`role: "tool"`, `content: "15,420"`) হিসেবে মডেলে ফেরত পাঠানো হয়।
5. মডেল এই ফলাফলের ওপর ভিত্তি করে তার চূড়ান্ত প্রাকৃতিক ভাষার উত্তর প্রদান করে।

---

## ৩. রিফ্লেকশন ও সেলফ-কারেকশন (Reflexion: Shinn et al., 2023)

একটি এজেন্ট কোনো কোড বা কাজ করতে গিয়ে ব্যর্থ হলে যদি কেবল অন্ধের মতো ট্রাই করতে থাকে, তবে সে একই ভুলের লুপে আটকে যায়।
**Reflexion** ফ্রেমওয়ার্ক এজেন্টকে প্রতিটি ব্যর্থতার পর একটি মৌখিক স্ব-মূল্যায়ন (verbal self-critique) তৈরি করতে বাধ্য করে:

```
[ Trial t ] ---> Generates Code ---> Execution Fails (IndexError: list index out of range)
      |
      v
[ Evaluator ] ---> Detects Failure
      |
      v
[ Self-Reflection ] ---> "I assumed the list has at least 3 elements without checking len(arr).
                          Next time, I will add an explicit guard condition: if len(arr) < 3: return."
      |
      v
(Appended to Episodic Memory)
      |
      v
[ Trial t + 1 ] ---> Incorporates reflection, generates robust code with boundary checks ---> Success!
```

মডেলের ওজন আপডেট না করেই এই রিফ্লেকশন মেমোরি এজেন্টের সাকসেস রেট ৩০% থেকে ৯০%-এ উন্নীত করে!

---

## ৪. মাল্টি-এজেন্ট প্যাটার্নস (Multi-Agent Architectures)

জটিল সফটওয়্যার ইঞ্জিনিয়ারিং বা রিসার্চ টাস্কে একক এজেন্টের চেয়ে একাধিক স্পেশালাইজড এজেন্টের দল (Multi-Agent Swarm) বহুগুণ বেশি কার্যকর:

```
[ 1. Hierarchical Supervisor Pattern ]
                   [ Supervisor / Orchestrator Agent ]
                                     |
              +----------------------+----------------------+
              |                                             |
              v                                             v
     [ Researcher Agent ]                          [ Coder Agent ]
     (Searches documentation)                     (Writes unit tests)
              \                                             /
               +---------------------+---------------------+
                                     |
                                     v
                          [ Reviewer / QA Agent ]

[ 2. Society of Mind / Debate Pattern ]
   [ Agent A: Proposes Solution ] <==== Debate ====> [ Agent B: Devil's Advocate ]
                                     |
                                     v
                     Consensus / Synthesized Strategy
```

1. **হায়ারার্কিকাল সুপারভাইজার (Hierarchical Supervisor):** মূল প্ল্যানার এজেন্ট কাজকে ভাগ করে সাব-এজেন্টদের কাছে ডেলিগেট করে এবং তাদের ফলাফল সমন্বয় করে।
2. **পিয়ার-টু-পিয়ার ডিবেট (Debate Pattern):** একাধিক মডেল একই সমস্যার ভিন্ন ভিন্ন দৃষ্টিকোণ উপস্থাপন করে যুক্তি-তর্কের মাধ্যমে চূড়ান্ত সিদ্ধান্তে পৌঁছায়, যা হ্যালুসিনেশন প্রায় শূন্যে নামিয়ে আনে।

---

## ৫. হিউম্যান-ইন-দ্য-লুপ (Human-in-the-Loop - HITL) ও নিরাপত্তা

বাস্তব জগতে এজেন্ট ডেপ্লয় করার সময় আনচেকড অটোনমি মারাত্মক বিপর্যয় ডেকে আনতে পারে (যেমন: ভুল করে প্রোডাকশন ডেটাবেস ডিলিট করা বা আর্থিক লেনদেন সম্পন্ন করা)।

### এক্সেস লেভেল বিভাজন:
- **Read-Only Tools (Autonomous):** তথ্য অনুসন্ধান, ডাটাবেস সিলেক্ট কুয়েরি, ফাইল রিড — এগুলো এজেন্ট সরাসরি এক্সিকিউট করতে পারে।
- **State-Altering / Destructive Tools (HITL Required):** ফাইল ডিলিট, ফান্ড ট্রান্সফার, প্রোডাকশন কোড পুশ, ইমেইল সেন্ড — এই টুলগুলো কল করার আগে এক্সিকিউশন পজ হয়ে সরাসরি মানুষের অনুমোদন (Approval Gate) চায়।

---

## ৬. পাইথনে পূর্ণাঙ্গ মাল্টি-এজেন্ট সিস্টেম ইমপ্লিমেন্টেশন

নিচে একটি স্বয়ংসম্পূর্ণ, ডিপেন্ডেন্সি-মুক্ত পাইথন কোড দেওয়া হলো যা প্ল্যানার এজেন্ট, কোডার এজেন্ট এবং রিফ্লেকশন/রিভিউয়ার এজেন্টের সমন্বয়ে গঠিত একটি সম্পূর্ণ মাল্টি-এজেন্ট ওয়ার্কফ্লো বাস্তবায়ন করে:

```python
"""
Multi-Agent Orchestration Engine: Planner, Coder, and Critic/Verifier.
Fully runnable, verified and robust.
"""

from typing import Dict, Any, List


# ============================================================================
# 1. Specialized Agent Personas
# ============================================================================
class PlannerAgent:
    def plan(self, user_request: str) -> List[str]:
        print(f"\n[Planner Agent]: Decomposing request '{user_request}' into actionable subtasks.")
        # Simulating task decomposition
        subtasks = [
            "Write a Python function to compute the Fibonacci sequence using memoization.",
            "Verify edge cases: n = 0, n = 1, and negative numbers."
        ]
        return subtasks


class CoderAgent:
    def write_code(self, subtask: str, feedback: str = None) -> str:
        print(f"\n[Coder Agent]: Generating code for task: '{subtask}'")
        if feedback:
            print(f"[Coder Agent]: Incorporating Critic's feedback -> '{feedback}'")
            # Improved implementation handling edge cases
            code = (
                "def fibonacci(n: int, memo={}):\n"
                "    if n < 0:\n"
                "        raise ValueError('Negative numbers are not allowed.')\n"
                "    if n in (0, 1):\n"
                "        return n\n"
                "    if n not in memo:\n"
                "        memo[n] = fibonacci(n - 1, memo) + fibonacci(n - 2, memo)\n"
                "    return memo[n]"
            )
        else:
            # Flawed initial implementation (missing negative check)
            code = (
                "def fibonacci(n: int, memo={}):\n"
                "    if n in (0, 1):\n"
                "        return n\n"
                "    return fibonacci(n - 1, memo) + fibonacci(n - 2, memo)"
            )
        return code


class CriticAgent:
    def evaluate(self, code: str) -> Dict[str, Any]:
        print("\n[Critic Agent]: Inspecting code for security, edge cases, and algorithmic correctness.")
        # Inspect code
        if "if n < 0" not in code:
            return {
                "passed": False,
                "feedback": "Code does not handle negative inputs (n < 0). It will cause infinite recursion."
            }
        return {
            "passed": True,
            "feedback": "Code is robust, handles base cases and negative inputs correctly."
        }


# ============================================================================
# 2. Multi-Agent Orchestrator (Supervisor)
# ============================================================================
class Orchestrator:
    def __init__(self):
        self.planner = PlannerAgent()
        self.coder = CoderAgent()
        self.critic = CriticAgent()

    def run_workflow(self, user_goal: str, max_refinements: int = 2) -> str:
        print("="*60)
        print(f"ORCHESTRATOR STARTED: Goal = '{user_goal}'")
        print("="*60)

        tasks = self.planner.plan(user_goal)
        current_code = self.coder.write_code(tasks[0])

        attempts = 0
        while attempts < max_refinements:
            critique = self.critic.evaluate(current_code)
            if critique["passed"]:
                print("\n[Orchestrator]: Verification passed! Final deliverable ready.")
                break
            else:
                attempts += 1
                print(f"\n[Orchestrator]: Verification failed! Triggering refinement iteration {attempts}.")
                current_code = self.coder.write_code(tasks[0], feedback=critique["feedback"])

        print("\n" + "="*60)
        print("FINAL DELIVERABLE CODE:")
        print("="*60)
        print(current_code)
        return current_code


# ============================================================================
# 3. Execution & Verification
# ============================================================================
if __name__ == "__main__":
    system = Orchestrator()
    final_output = system.run_workflow("Build an efficient Fibonacci solver")
```

---

## ৭. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স

| বৈশিষ্ট্য | একক চ্যাটবট | সিঙ্গেল রিঅ্যাক্ট এজেন্ট | মাল্টি-এজেন্ট সিস্টেম |
| :--- | :--- | :--- | :--- |
| **অটোনমি ও অ্যাকশন** | শূন্য (শুধু টেক্সট) | মাঝারি (টুল কলিং লুপ) | সর্বোচ্চ (স্বাধীন ভূমিকা পালন) |
| **জটিল কাজ সমাধান** | ব্যর্থ | মাঝে মাঝে লুপে আটকে যায় | অত্যন্ত দক্ষ (টাস্ক ডেলিগেশন) |
| **টোকেন খরচ** | সর্বনিম্ন | মাঝারি | উচ্চ (এজেন্টদের পারস্পরিক বার্তা) |
| **এরর রিকভারি** | নেই | সীমিত | অসাধারণ (Critic/Verifier রিফ্লেকশন) |
| **আদর্শ ব্যবহারের ক্ষেত্র** | সাধারণ প্রশ্নোত্তর | ডাটাবেস কোয়েরি, সহজ সার্চ | পূর্ণাঙ্গ সফটওয়্যার ডেভেলপমেন্ট, ইনভেস্টিগেটিভ রিসার্চ |

---

## ৮. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: এআই এজেন্ট সিস্টেমে Infinite Loop বা রান-অ্যাওয়ে এক্সিকিউশন কীভাবে প্রতিরোধ করা যায়?**
   - **উত্তর:** ১. `max_iterations` বা হার্ড স্টেপ লিমিট নির্ধারণ করা (যেমন সর্বোচ্চ ১০-১৫ ধাপ)। ২. টোকেন ও বাজেট ক্যাপ সেট করা (যেমন প্রতি সেশনে সর্বোচ্চ $১ ডলার সমপরিমাণ টোকেন)। ৩. পুনরাবৃত্তিমূলক অ্যাকশন ডিটেকশন (যদি এজেন্ট পরপর ৩ বার একই আর্গুমেন্ট দিয়ে একই টুল কল করে তবে ইন্টারাপ্ট করা)। ৪. নির্দিষ্ট সময় পর পর হিউম্যান কনফার্মেশন প্রম্পট চাওয়া।
2. **প্রশ্ন: Function Calling-এ মডেল কী সরাসরি আপনার পাইথন কোড রান করে?**
   - **উত্তর:** না! মডেল কেবল একটি স্ট্রিং/JSON পেলোড আউটপুট করে যা নির্দেশ করে সে কোন ফাংশনটি কোন প্যারামিটার দিয়ে রান করতে চায়। কোডটি আপনার নিজস্ব সুরক্ষিত স্যান্ডবক্স বা সার্ভার রান করে এবং তার আউটপুট পুনরায় মডেলে টেক্সট আকারে পাঠানো হয়।
3. **প্রশ্ন: অটোনোমাস এজেন্টের লং-টার্ম মেমোরির জন্য কোন ধরনের রিট্রিভাল সবচেয়ে কার্যকর?**
   - **উত্তর:** Generative Agents (Park et al., 2023) অনুযায়ী তিনটি বিষয়ের সমন্বিত স্কোর: $\text{Score} = \alpha_{\text{recency}} \cdot \text{Recency} + \alpha_{\text{importance}} \cdot \text{Importance} + \alpha_{\text{relevance}} \cdot \text{Relevance}$। যেখানে সাম্প্রতিক ঘটনা, অত্যন্ত আবেগীয় বা গুরুত্বপূর্ণ ইভেন্ট এবং বর্তমান পরিস্থিতির সাথে সিম্যান্টিক মিল থাকা স্মৃতিগুলো অগ্রাধিকার পায়।
