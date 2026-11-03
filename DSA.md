# Master Handbook: Data Structures & Algorithms (DSA) for MNC Technical Interviews

---

## ১. ভূমিকা ও বহুজাতিক কোম্পানির (MNC) ইন্টারভিউ মূল্যায়ন কাঠামো

গুগল (Google), মেটা (Meta), অ্যামাজন (Amazon), মাইক্রোসফট (Microsoft), অ্যাপল (Apple), উবার (Uber) এবং নেটফ্লিক্স (Netflix)-এর মতো শীর্ষস্থানীয় বহুজাতিক প্রযুক্তি প্রতিষ্ঠানগুলোতে (MNC) কোডিং ইন্টারভিউতে প্রার্থীর ৪টি প্রধান দক্ষতা কঠোরভাবে মূল্যায়ন করা হয়:

```
                            [ MNC INTERVIEW EVALUATION PILLARS ]
                                              |
      +---------------------------------------+---------------------------------------+
      |                                                                               |
[ 1. Problem Exploration & Clarification ]                            [ 2. Algorithmic Design & Trade-offs ]
  - প্রশ্ন পুরোপুরি বোঝার জন্য Clarifying Questions করা।                  - Brute-force থেকে Optimal সমাধান প্রস্তাব করা।
  - ইনপুট/আউটপুট টাইপ, বাউন্ডস ও Edge Cases শনাক্ত করা।                 - Time ও Space Complexity (Big-O) নিখুঁতভাবে বলা।
      |                                                                               |
      +---------------------------------------+---------------------------------------+
      |                                                                               |
[ 3. Clean, Modular & Bug-Free Coding ]                               [ 4. Proactive Verification & Dry Run ]
  - রিডেবল ভ্যারিয়েবল নেমিং ও পরিষ্কার মডুলার কোড।                       - ইন্টারভিউয়ার বলার আগেই টেস্ট কেস দিয়ে Dry Run করা।
  - অফ-বাই-ওয়ান (Off-by-one) ও নাল পয়েন্টার এরর প্রতিরোধ।              - বাউন্ডারি ও চরম প্রান্তিক কেস যাচাই করা।
```

### সমস্যা সমাধানের ৬-ধাপী মেথডোলজি (The 6-Step MNC Playbook):
1. **Clarify (২-৩ মিনিট):** ইনপুটের সাইজ ($N \le 10^5$ নাকি $N \le 20$?), নেগেটিভ সংখ্যা আছে কিনা, ডুপ্লিকেট থাকতে পারে কিনা, মেমোরি লিমিট কত।
2. **Brainstorm & Communicate (৫ মিনিট):** সরাসরি কোড লেখা শুরু করবেন না! প্রথমে ব্রুট-ফোর্স সমাধান বলুন ($O(N^2)$), এরপর কেন এটি অপ্টিমাল নয় তা ব্যাখ্যা করে কাঙ্ক্ষিত প্যাটার্ন ($O(N)$ বা $O(N \log N)$) প্রস্তাব করুন।
3. **Confirm Signature & Algorithm:** ইন্টারভিউয়ারের সম্মতি নিয়ে অ্যালগরিদমের হাই-লেভেল স্টেপগুলো বলুন।
4. **Code with Clarity (১৫-২০ মিনিট):** আত্মবিশ্বাসের সাথে পরিষ্কার, প্রোডাকশন-গ্রেড পাইথন কোড লিখুন।
5. **Dry Run (৫ মিনিট):** একটি সাধারণ ইনপুট এবং একটি এজ কেস (Empty array, Single element, Duplicates) নিয়ে লাইন-বাই-লাইন ভ্যারিয়েবল ট্র্যাকিং করুন।
6. **Complexity Recap:** অমায়িকভাবে অমনি-প্রেজেন্ট Big-O Time এবং Auxiliary Space Complexity পুনরায় উপস্থাপন করুন।

---

## 🗺️ অ্যালগরিদমিক প্যাটার্ন ইনডেক্স ও নেভিগেশন (The 14 Master Patterns)

| # | প্যাটার্ন নাম | মূল ডেটা স্ট্রাকচার | সাধারণ টাইম কমপ্লেক্সিটি | শীর্ষ কোম্পানি ট্যাগ |
| :-: | :--- | :--- | :---: | :--- |
| **১** | [Arrays & Hashing (Prefix Sums & Frequency Maps)](#pattern-1-arrays--hashing) | Hash Map / Set | $O(N)$ | Google, Meta, Amazon |
| **২** | [Two Pointers (Opposite & Same Direction)](#pattern-2-two-pointers) | Array, String | $O(N)$ | Meta, Microsoft, Amazon |
| **৩** | [Sliding Window (Fixed & Dynamic / Shrinkable)](#pattern-3-sliding-window) | Array, String, Hash Map | $O(N)$ | Google, Amazon, Uber |
| **৪** | [Stack & Monotonic Stack](#pattern-4-stack--monotonic-stack) | LIFO Stack, Array | $O(N)$ | Google, Bloomberg, Meta |
| **৫** | [Binary Search & Predicate Monotonicity](#pattern-5-binary-search) | Sorted Array / Search Space | $O(\log N)$ | Google, Uber, Apple |
| **৬** | [Linked Lists (Fast-Slow Pointer & In-Place Reversal)](#pattern-6-linked-lists) | Singly / Doubly Linked List | $O(N)$ | Microsoft, Amazon, Meta |
| **৭** | [Trees & Binary Search Trees (DFS, BFS & Paths)](#pattern-7-trees--binary-search-trees) | Binary Tree, BST, Queue | $O(N)$ | Google, Meta, Amazon |
| **৮** | [Tries (Prefix Tree & Word Search)](#pattern-8-tries) | Trie Node, Hash Map | $O(L)$ ($L$ = word length) | Google, Microsoft, Amazon |
| **৯** | [Heaps / Priority Queue (Top K & Stream Median)](#pattern-9-heap--priority-queue) | Min-Heap, Max-Heap | $O(N \log K)$ | Meta, Amazon, Apple |
| **১০** | [Backtracking (Subsets, Permutations & Constraints)](#pattern-10-backtracking) | Recursion Tree, Visited Set | $O(2^N)$ / $O(N!)$ | Google, Meta, Microsoft |
| **১১** | [Graphs (BFS, DFS, Topological Sort, Union-Find)](#pattern-11-graphs--topological-sort--union-find) | Adjacency List, DSU | $O(V + E)$ | Google, Amazon, Uber |
| **১২** | [Dynamic Programming (1D, 2D Grid, Knapsack, Intervals)](#pattern-12-dynamic-programming) | Memoization Table / Array | $O(N)$ / $O(N \cdot M)$ | Google, Meta, Bloomberg |
| **১৩** | [Greedy Algorithms & Interval Scheduling](#pattern-13-greedy-algorithms--intervals) | Sorting, Priority Queue | $O(N \log N)$ | Amazon, Microsoft, Uber |
| **১৪** | [Bit Manipulation & Fast Arithmetic](#pattern-14-bit-manipulation) | Bitwise Operators | $O(1)$ / $O(\log N)$ | Apple, Google, Meta |
| **⭐** | [দ্য আলটিমেট ৩২৫ প্রবলেম কারিকুলাম (Striver A2Z & MNC Master Sheet)](#the-ultimate-325-problem-sheet) | All Core Data Structures & Patterns | 325 Selected Problems | Google, Meta, Amazon, Microsoft, Apple, Uber |

---

<a name="pattern-1-arrays--hashing"></a>
## Pattern 1: Arrays & Hashing (Prefix Sums & Frequency Maps)

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
অ্যারেতে যেকোনো এলিমেন্ট খুঁজতে সাধারণত $O(N)$ সময় লাগে। হ্যাশ টেবিল বা ডিকশনারি ($O(1)$ average time lookups) ব্যবহার করে আমরা সময় জটিলতাকে $O(N^2)$ থেকে এক লাফে $O(N)$-এ নামিয়ে আনতে পারি।
- **Frequency Map / Set:** ডুপ্লিকেট এলিমেন্ট, অ্যানাগ্রাম এবং সাবস্ট্রিং গণনায় ব্যবহৃত হয়।
- **Prefix Sum:** যেকোনো সাব-অ্যারের যোগফল $O(1)$ সময়ে বের করতে: $\text{Sum}(i, j) = \text{Prefix}[j] - \text{Prefix}[i-1]$।

```
Index:         0    1    2    3    4
Array nums:   [2,   4,   1,   7,   3]
Prefix Sum:   [2,   6,   7,  14,  17]
Subarray Sum(1 to 3) = Prefix[3] - Prefix[0] = 14 - 2 = 12 (4 + 1 + 7) in O(1) time!
```

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Two Sum** | Easy | Google, Meta, Amazon, Microsoft | [LeetCode 1](https://leetcode.com/problems/two-sum/) |
| **Contains Duplicate** | Easy | Amazon, Apple, Microsoft | [LeetCode 217](https://leetcode.com/problems/contains-duplicate/) |
| **Valid Anagram** | Easy | Meta, Google, Uber | [LeetCode 242](https://leetcode.com/problems/valid-anagram/) |
| **Group Anagrams** | Medium | Amazon, Meta, Microsoft, Apple | [LeetCode 49](https://leetcode.com/problems/group-anagrams/) |
| **Top K Frequent Elements** | Medium | Meta, Amazon, Google | [LeetCode 347](https://leetcode.com/problems/top-k-frequent-elements/) |
| **Product of Array Except Self** | Medium | Amazon, Meta, Apple, Google | [LeetCode 238](https://leetcode.com/problems/product-of-array-except-self/) |
| **Subarray Sum Equals K** | Medium | Meta, Google, Microsoft | [LeetCode 560](https://leetcode.com/problems/subarray-sum-equals-k/) |
| **Longest Consecutive Sequence** | Medium | Google, Amazon, Meta | [LeetCode 128](https://leetcode.com/problems/longest-consecutive-sequence/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Subarray Sum Equals K):

```python
from typing import List

def subarray_sum(nums: List[int], k: int) -> int:
    """
    Finds the total number of continuous subarrays whose sum equals k.
    Pattern: Prefix Sum + Hash Map (Two Sum inverse logic)
    Time Complexity: O(N) - single pass over nums.
    Space Complexity: O(N) - hash map storing prefix sums.
    """
    prefix_count = {0: 1}  # Base case: a prefix sum of 0 has occurred once.
    current_sum = 0
    total_subarrays = 0

    for num in nums:
        current_sum += num
        # If (current_sum - k) exists in prefix_count, it means a valid subarray ending here sums to k.
        target = current_sum - k
        if target in prefix_count:
            total_subarrays += prefix_count[target]

        # Record the current prefix sum frequency
        prefix_count[current_sum] = prefix_count.get(current_sum, 0) + 1

    return total_subarrays

# Verification
if __name__ == "__main__":
    test_nums = [1, 2, 3, -2, 1, 1]
    target_k = 3
    print("Total Subarrays matching sum k=3:", subarray_sum(test_nums, target_k))
```

---

<a name="pattern-2-two-pointers"></a>
## Pattern 2: Two Pointers (Opposite & Same Direction)

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
সর্টেড বা বিশেষ বৈশিষ্ট্যযুক্ত অ্যারেতে নেস্টেড লুপ ($O(N^2)$) পরিহার করতে দুটি পয়েন্টার ব্যবহার করা হয়:
1. **Opposite Direction (বাম ও ডান প্রান্ত থেকে কেন্দ্রের দিকে):** যেমন পালিনড্রোম যাচাই, টার্গেট পেয়ার যোগফল (Two Sum II, 3Sum), বা আবদ্ধ জল ধারণক্ষমতা (Container With Most Water)।
2. **Same Direction (Slow & Fast Pointers):** যেমন ইন-প্লেস ডুপ্লিকেট রিমুভ করা বা লিংকড লিস্টের সাইকেল ডিটেকশন।

```
Two Sum II on Sorted Array:
nums = [1,  3,  4,  6,  8, 11],  Target = 10
        ^                   ^
       left               right   -> Sum = 1 + 11 = 12 > 10 (Move right leftward: right--)
        ^               ^
       left           right       -> Sum = 1 + 8 = 9 < 10   (Move left rightward: left++)
            ^           ^
           left       right       -> Sum = 3 + 8 = 11 > 10  (right--)
            ^       ^
           left   right           -> Sum = 3 + 6 = 9 < 10   (left++)
                ^   ^
              left right          -> Sum = 4 + 6 = 10 == Target! Found [2, 3]!
```

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Valid Palindrome** | Easy | Meta, Microsoft, Apple | [LeetCode 125](https://leetcode.com/problems/valid-palindrome/) |
| **Two Sum II - Input Array Is Sorted**| Medium | Amazon, Google, Apple | [LeetCode 167](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) |
| **3Sum** | Medium | Meta, Amazon, Microsoft, Google | [LeetCode 15](https://leetcode.com/problems/3sum/) |
| **Container With Most Water** | Medium | Google, Amazon, Meta, Uber | [LeetCode 11](https://leetcode.com/problems/container-with-most-water/) |
| **Trapping Rain Water** | Hard | Google, Amazon, Meta, Microsoft, Bloomberg | [LeetCode 42](https://leetcode.com/problems/trapping-rain-water/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Trapping Rain Water - $O(N)$ Time, $O(1)$ Space):

```python
from typing import List

def trap_rain_water(height: List[int]) -> int:
    """
    Computes total trapped water after raining.
    Pattern: Two Pointers from both ends with max tracking.
    Time Complexity: O(N)
    Space Complexity: O(1)
    """
    if not height:
        return 0

    left, right = 0, len(height) - 1
    left_max, right_max = height[left], height[right]
    trapped_water = 0

    while left < right:
        if left_max <= right_max:
            left += 1
            left_max = max(left_max, height[left])
            trapped_water += left_max - height[left]
        else:
            right -= 1
            right_max = max(right_max, height[right])
            trapped_water += right_max - height[right]

    return trapped_water

if __name__ == "__main__":
    elevation = [0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1]
    print("Total Trapped Water:", trap_rain_water(elevation))  # Expected: 6
```

---

<a name="pattern-3-sliding-window"></a>
## Pattern 3: Sliding Window (Fixed & Dynamic / Shrinkable)

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
ধারাবাহিক সাব-অ্যারে বা সাব-স্ট্রিং সংক্রান্ত সমস্যায় স্লাইডিং উইন্ডো অপরাজেয়।
- **Fixed Size Window ($K$ দৈর্ঘ্য):** উইন্ডোর ডান প্রান্ত এক ঘর বাড়লে বাম প্রান্তও এক ঘর সামনে বাড়ে ($O(N)$)।
- **Dynamic / Shrinkable Window:** ডান প্রান্ত বাড়িয়ে শর্ত পূরণ করা হয় (Expand Window), এবং কোনো অবৈধ শর্ত পূরণ হলে বাম প্রান্ত সরিয়ে উইন্ডো সংকুচিত করা হয় (Shrink Window)।

```
Longest Substring Without Repeating Characters:
String: "p  w  w  k  e  w"
[p]               -> Valid, len = 1
[p  w]            -> Valid, len = 2
[p  w  w]         -> 'w' duplicate! Shrink left pointer past first 'w' -> [w]
[w  k]            -> Valid, len = 2
[w  k  e]         -> Valid, len = 3 (Max!)
[w  k  e  w]      -> 'w' duplicate! Shrink left -> [k  e  w], len = 3
```

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Best Time to Buy and Sell Stock** | Easy | Amazon, Meta, Google, Microsoft | [LeetCode 121](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) |
| **Longest Substring Without Repeating Characters** | Medium | Google, Amazon, Meta, Microsoft | [LeetCode 3](https://leetcode.com/problems/longest-substring-without-repeating-characters/) |
| **Longest Repeating Character Replacement** | Medium | Google, Amazon, Uber | [LeetCode 424](https://leetcode.com/problems/longest-repeating-character-replacement/) |
| **Permutation in String** | Medium | Meta, Microsoft, Apple | [LeetCode 567](https://leetcode.com/problems/permutation-in-string/) |
| **Minimum Window Substring** | Hard | Meta, Google, Amazon, Microsoft, Uber | [LeetCode 76](https://leetcode.com/problems/minimum-window-substring/) |
| **Sliding Window Maximum** | Hard | Amazon, Google, Meta | [LeetCode 239](https://leetcode.com/problems/sliding-window-maximum/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Minimum Window Substring):

```python
from collections import Counter

def min_window(s: str, t: str) -> str:
    """
    Finds minimum window substring of s that contains all characters in t.
    Pattern: Expand right pointer until valid, shrink left pointer to minimize.
    Time Complexity: O(|s| + |t|)
    Space Complexity: O(|s| + |t|)
    """
    if not s or not t:
        return ""

    target_counts = Counter(t)
    required = len(target_counts)
    window_counts = {}

    formed = 0
    left = 0
    ans = (float("inf"), None, None)  # (window_len, left, right)

    for right, char in enumerate(s):
        window_counts[char] = window_counts.get(char, 0) + 1
        if char in target_counts and window_counts[char] == target_counts[char]:
            formed += 1

        # Shrink window from the left until it ceases to be valid
        while left <= right and formed == required:
            curr_char = s[left]
            if (right - left + 1) < ans[0]:
                ans = (right - left + 1, left, right)

            window_counts[curr_char] -= 1
            if curr_char in target_counts and window_counts[curr_char] < target_counts[curr_char]:
                formed -= 1
            left += 1

    return "" if ans[0] == float("inf") else s[ans[1] : ans[2] + 1]

if __name__ == "__main__":
    print("Min Window Substring:", min_window("ADOBECODEBANC", "ABC"))  # Expected: "BANC"
```

---

<a name="pattern-4-stack--monotonic-stack"></a>
## Pattern 4: Stack & Monotonic Stack

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
- **LIFO Stack:** নেস্টেড স্ট্রাকচার, সিনট্যাক্স পার্সিং ও প্যারেন্থেসিস ভ্যালিডেশনে ব্যবহৃত হয়।
- **Monotonic Stack (একঘেয়ে স্ট্যাক):** পরবর্তী নিকটতম বৃহত্তর বা ক্ষুদ্রতর এলিমেন্ট (Next Greater / Smaller Element) $O(N)$ সময়ে খুঁজে বের করতে ব্যবহৃত হয়। স্ট্যাকের ভেতরে এলিমেন্টগুলো সর্বদা কঠোরভাবে আরোহী (increasing) বা অবরোহী (decreasing) ক্রমে সাজানো থাকে।

```
Next Greater Element using Monotonic Decreasing Stack:
Array: [2, 1, 2, 4, 3]
- 2 arrives: Stack = [2]
- 1 arrives: 1 < 2 -> Stack = [2, 1]
- 2 arrives: 2 > 1 -> Pop 1! (Next greater for 1 is 2). Stack = [2, 2]
- 4 arrives: 4 > 2 -> Pop 2, Pop 2! (Next greater for both is 4). Stack = [4]
- 3 arrives: 3 < 4 -> Stack = [4, 3]
Every element is pushed and popped at most ONCE -> O(N) Total Time!
```

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Valid Parentheses** | Easy | Meta, Amazon, Google, Microsoft | [LeetCode 20](https://leetcode.com/problems/valid-parentheses/) |
| **Min Stack** | Medium | Amazon, Bloomberg, Microsoft | [LeetCode 155](https://leetcode.com/problems/min-stack/) |
| **Evaluate Reverse Polish Notation** | Medium | Amazon, Google, Meta | [LeetCode 150](https://leetcode.com/problems/evaluate-reverse-polish-notation/) |
| **Daily Temperatures** | Medium | Meta, Google, Amazon | [LeetCode 739](https://leetcode.com/problems/daily-temperatures/) |
| **Largest Rectangle in Histogram** | Hard | Google, Amazon, Meta, Microsoft | [LeetCode 84](https://leetcode.com/problems/largest-rectangle-in-histogram/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Daily Temperatures):

```python
from typing import List

def daily_temperatures(temperatures: List[int]) -> List[int]:
    """
    Returns array such that result[i] is number of days to wait for a warmer temperature.
    Pattern: Monotonic Decreasing Stack storing indices.
    Time Complexity: O(N)
    Space Complexity: O(N)
    """
    n = len(temperatures)
    answer = [0] * n
    stack = []  # Stores indices of temperatures in decreasing order

    for current_day, current_temp in enumerate(temperatures):
        # Pop previous days that are colder than today
        while stack and temperatures[stack[-1]] < current_temp:
            prev_day = stack.pop()
            answer[prev_day] = current_day - prev_day
        stack.append(current_day)

    return answer

if __name__ == "__main__":
    temps = [73, 74, 75, 71, 69, 72, 76, 73]
    print("Days to Warmer Temp:", daily_temperatures(temps))  # [1, 1, 4, 2, 1, 1, 0, 0]
```

---

<a name="pattern-5-binary-search"></a>
## Pattern 5: Binary Search & Predicate Monotonicity

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
বাইনারি সার্চ কেবল সর্টেড অ্যারেতে সংখ্যা খোঁজার জন্য নয়। আধুনিক MNC ইন্টারভিউতে সবচেয়ে জনপ্রিয় প্যাটার্ন হলো **"Binary Search on Answer Space"**।
যদি কোনো সিদ্ধান্ত ফাংশন (Predicate) মনোটোনিক হয়:
$$\text{feasible}(x) = \text{False, False, False, True, True, True}$$
তবে আমরা উত্তরের সম্পূর্ণ পরিসীমার ওপর বাইনারি সার্চ চালিয়ে $O(\log(\text{Range}) \times \text{Check\_Time})$ জটিলতায় অপ্টিমাল মান বের করতে পারি।

```
Standard Binary Search Template (Avoiding Integer Overflow):
left, right = 0, len(nums) - 1
while left <= right:
    mid = left + (right - left) // 2
    if nums[mid] == target: return mid
    elif nums[mid] < target: left = mid + 1
    else: right = mid - 1
```

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Binary Search** | Easy | Microsoft, Google, Apple | [LeetCode 704](https://leetcode.com/problems/binary-search/) |
| **Search a 2D Matrix** | Medium | Amazon, Meta, Microsoft | [LeetCode 74](https://leetcode.com/problems/search-a-2d-matrix/) |
| **Search in Rotated Sorted Array** | Medium | Meta, Google, Amazon, Microsoft | [LeetCode 33](https://leetcode.com/problems/search-in-rotated-sorted-array/) |
| **Find Minimum in Rotated Sorted Array** | Medium | Amazon, Microsoft, Uber | [LeetCode 153](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) |
| **Koko Eating Bananas** | Medium | Google, Amazon, Meta | [LeetCode 875](https://leetcode.com/problems/koko-eating-bananas/) |
| **Median of Two Sorted Arrays** | Hard | Google, Amazon, Meta, Microsoft, Apple | [LeetCode 4](https://leetcode.com/problems/median-of-two-sorted-arrays/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Koko Eating Bananas - Binary Search on Answer):

```python
import math
from typing import List

def min_eating_speed(piles: List[int], h: int) -> int:
    """
    Finds the minimum integer speed k to eat all bananas within h hours.
    Pattern: Binary Search over search space [1, max(piles)].
    Time Complexity: O(N * log(max(piles)))
    Space Complexity: O(1)
    """
    def can_finish_in_time(speed: int) -> bool:
        total_hours = sum(math.ceil(pile / speed) for pile in piles)
        return total_hours <= h

    left, right = 1, max(piles)
    optimal_speed = right

    while left <= right:
        mid_speed = left + (right - left) // 2
        if can_finish_in_time(mid_speed):
            optimal_speed = mid_speed
            right = mid_speed - 1  # Try searching for an even slower valid speed
        else:
            left = mid_speed + 1   # Speed too slow, must increase

    return optimal_speed

if __name__ == "__main__":
    banana_piles = [3, 6, 7, 11]
    hours_limit = 8
    print("Minimum Eating Speed k:", min_eating_speed(banana_piles, hours_limit))  # 4
```

---

<a name="pattern-6-linked-lists"></a>
## Pattern 6: Linked Lists (Fast-Slow Pointer & In-Place Reversal)

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
- **Dummy Head Node:** নোড ডিলিট বা হেড নোড পরিবর্তনের সময় এজ-কেস জটিলতা এড়াতে ডামি হেড ব্যবহার করা হয়।
- **Fast & Slow Pointers (Floyd's Cycle Finding):** মধ্যবিন্দু নির্ণয় ($O(N)$) এবং লুপ বা সাইকেল ডিটেকশন ($O(1)$ space)।
- **In-Place Reversal:** মেমোরি খরচ না করে পয়েন্টার ঘোরানো (`curr.next = prev`).

```
Reversing Linked List In-Place:
None <- [1]    [2] -> [3] -> None
 ^       ^      ^
prev    curr   next_temp
Step: next_temp = curr.next; curr.next = prev; prev = curr; curr = next_temp
```

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Reverse Linked List** | Easy | Meta, Amazon, Google, Microsoft, Apple | [LeetCode 206](https://leetcode.com/problems/reverse-linked-list/) |
| **Merge Two Sorted Lists** | Easy | Amazon, Microsoft, Meta | [LeetCode 21](https://leetcode.com/problems/merge-two-sorted-lists/) |
| **Reorder List** | Medium | Meta, Amazon, Google | [LeetCode 143](https://leetcode.com/problems/reorder-list/) |
| **Remove Nth Node From End of List** | Medium | Meta, Google, Microsoft | [LeetCode 19](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) |
| **Linked List Cycle II** | Medium | Amazon, Microsoft, Apple | [LeetCode 142](https://leetcode.com/problems/linked-list-cycle-ii/) |
| **LRU Cache** | Medium/Hard | Amazon, Google, Meta, Microsoft, Apple, Bloomberg | [LeetCode 146](https://leetcode.com/problems/lru-cache/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (LRU Cache - Doubly Linked List + Hash Map):

```python
class Node:
    def __init__(self, key: int, val: int):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    """
    Least Recently Used (LRU) Cache in O(1) Get and O(1) Put.
    Combines Doubly Linked List (for O(1) reordering) + Hash Map (for O(1) lookup).
    """
    def __init__(self, capacity: int):
        self.cap = capacity
        self.cache = {}  # key -> Node
        # Dummy head and tail
        self.head = Node(0, 0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node: Node):
        prev_node = node.prev
        next_node = node.next
        prev_node.next = next_node
        next_node.prev = prev_node

    def _insert_to_front(self, node: Node):
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node

    def get(self, key: int) -> int:
        if key in self.cache:
            node = self.cache[key]
            self._remove(node)
            self._insert_to_front(node)  # Mark recently used
            return node.val
        return -1

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self._remove(self.cache[key])
        node = Node(key, value)
        self.cache[key] = node
        self._insert_to_front(node)

        if len(self.cache) > self.cap:
            # Evict least recently used node (before tail)
            lru = self.tail.prev
            self._remove(lru)
            del self.cache[lru.key]

if __name__ == "__main__":
    lru = LRUCache(2)
    lru.put(1, 100)
    lru.put(2, 200)
    print("Get 1:", lru.get(1))  # 100
    lru.put(3, 300)             # Evicts key 2!
    print("Get 2:", lru.get(2))  # -1 (Evicted)
```

---

<a name="pattern-7-trees--binary-search-trees"></a>
## Pattern 7: Trees & Binary Search Trees (DFS, BFS & Paths)

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
- **DFS (Pre-order, In-order, Post-order):** রিকার্শন এবং স্ট্যাক ভিত্তিক ট্রাভার্সাল। BST-তে In-order ট্রাভার্সাল সর্বদা কঠোরভাবে সর্টেড সিকোয়েন্স তৈরি করে!
- **BFS (Level-Order Traversal):** Queue ডেটা স্ট্রাকচার ব্যবহার করে লেয়ার বাই লেয়ার প্রসেসিং (যেমন লেভেল অর্ডার ট্রাভার্সাল, স্বল্পতম দূরত্ব)।

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Invert Binary Tree** | Easy | Google, Amazon, Meta | [LeetCode 226](https://leetcode.com/problems/invert-binary-tree/) |
| **Maximum Depth of Binary Tree** | Easy | Amazon, Microsoft, Apple | [LeetCode 104](https://leetcode.com/problems/maximum-depth-of-binary-tree/) |
| **Diameter of Binary Tree** | Easy | Meta, Amazon, Google | [LeetCode 543](https://leetcode.com/problems/diameter-of-binary-tree/) |
| **Lowest Common Ancestor of a BST** | Medium | Amazon, Microsoft, Meta | [LeetCode 235](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) |
| **Binary Tree Level Order Traversal** | Medium | Amazon, Meta, Microsoft | [LeetCode 102](https://leetcode.com/problems/binary-tree-level-order-traversal/) |
| **Binary Tree Maximum Path Sum** | Hard | Meta, Google, Amazon, Microsoft | [LeetCode 124](https://leetcode.com/problems/binary-tree-maximum-path-sum/) |
| **Serialize and Deserialize Binary Tree**| Hard | Meta, Amazon, Microsoft, Google, Uber | [LeetCode 297](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Binary Tree Maximum Path Sum):

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def max_path_sum(root: TreeNode) -> int:
    """
    Computes maximum path sum across any sequence of nodes in a binary tree.
    Time Complexity: O(N) - visits every node once.
    Space Complexity: O(H) - recursion stack where H is tree height.
    """
    global_max = float("-inf")

    def get_max_gain(node: TreeNode) -> int:
        nonlocal global_max
        if not node:
            return 0

        # Discard negative branches by clamping at 0
        left_gain = max(get_max_gain(node.left), 0)
        right_gain = max(get_max_gain(node.right), 0)

        # Price of current path passing through this node as highest ancestor
        current_path_sum = node.val + left_gain + right_gain
        global_max = max(global_max, current_path_sum)

        # For parent calls, return maximum single path extending downwards
        return node.val + max(left_gain, right_gain)

    get_max_gain(root)
    return global_max

if __name__ == "__main__":
    # Tree: [-10, 9, 20, null, null, 15, 7]
    root = TreeNode(-10)
    root.left = TreeNode(9)
    root.right = TreeNode(20, TreeNode(15), TreeNode(7))
    print("Maximum Path Sum:", max_path_sum(root))  # Expected: 42 (15 + 20 + 7)
```

---

<a name="pattern-8-tries"></a>
## Pattern 8: Tries (Prefix Trees)

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
অটো-কমপ্লিট, স্পেল চেকার এবং প্রিপিক্স অনুসন্ধানে ট্রাই (Trie) অপরিহার্য। সাধারণ হ্যাশ ম্যাপে প্রিপিক্স খুঁজতে সমস্ত কী স্ক্যান করতে হয় ($O(N \cdot L)$), কিন্তু ট্রাই-তে এটি সরাসরি শব্দের দৈর্ঘ্যের সমান সময়ে ($O(L)$) সম্পন্ন হয়।

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Implement Trie (Prefix Tree)** | Medium | Amazon, Google, Microsoft, Meta | [LeetCode 208](https://leetcode.com/problems/implement-trie-prefix-tree/) |
| **Design Add and Search Words Data Structure**| Medium | Meta, Amazon, Microsoft | [LeetCode 211](https://leetcode.com/problems/design-add-and-search-words-data-structure/) |
| **Word Search II** | Hard | Google, Amazon, Microsoft, Uber | [LeetCode 212](https://leetcode.com/problems/word-search-ii/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Implement Trie):

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end_of_word = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        curr = self.root
        for char in word:
            if char not in curr.children:
                curr.children[char] = TrieNode()
            curr = curr.children[char]
        curr.is_end_of_word = True

    def search(self, word: str) -> bool:
        curr = self.root
        for char in word:
            if char not in curr.children:
                return False
            curr = curr.children[char]
        return curr.is_end_of_word

    def starts_with(self, prefix: str) -> bool:
        curr = self.root
        for char in prefix:
            if char not in curr.children:
                return False
            curr = curr.children[char]
        return True

if __name__ == "__main__":
    trie = Trie()
    trie.insert("apple")
    print("Search 'apple':", trie.search("apple"))      # True
    print("Search 'app':", trie.search("app"))          # False
    print("Starts with 'app':", trie.starts_with("app"))# True
```

---

<a name="pattern-9-heap--priority-queue"></a>
## Pattern 9: Heap / Priority Queue (Top K & Stream Median)

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
- **Top K Elements:** সম্পূর্ণ অ্যারে সর্ট না করে ($O(N \log N)$), $K$-সাইজের হিপ ব্যবহার করে $O(N \log K)$ সময়ে সমাধান করা।
- **Two Heaps Pattern (Continuous Streaming Median):**
  - লোয়ার হাফ সংরক্ষণের জন্য একটি **Max-Heap**।
  - আপার হাফ সংরক্ষণের জন্য একটি **Min-Heap**।
  - উভয়ের শীর্ষ মান থেকে $O(1)$ সময়ে মিডিয়ান বের করা যায়!

```
Data Stream: [3, 8, 2, 7, 1]
Left Max-Heap (stores smaller half): [3, 2, 1] (Max at top: 3)
Right Min-Heap (stores larger half): [7, 8]    (Min at top: 7)
Median = 3 (Size unbalanced by 1)
```

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Kth Largest Element in an Array** | Medium | Meta, Amazon, Google, Microsoft | [LeetCode 215](https://leetcode.com/problems/kth-largest-element-in-an-array/) |
| **Task Scheduler** | Medium | Meta, Google, Amazon | [LeetCode 621](https://leetcode.com/problems/task-scheduler/) |
| **Find Median from Data Stream** | Hard | Google, Amazon, Meta, Microsoft, Apple | [LeetCode 295](https://leetcode.com/problems/find-median-from-data-stream/) |
| **Merge k Sorted Lists** | Hard | Amazon, Google, Meta, Microsoft, Apple | [LeetCode 23](https://leetcode.com/problems/merge-k-sorted-lists/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Find Median from Data Stream):

```python
import heapq

class MedianFinder:
    """
    Two-Heap structure for O(log N) insertion and O(1) median lookup.
    """
    def __init__(self):
        self.small = []  # Max-Heap (invert values with -x)
        self.large = []  # Min-Heap

    def add_num(self, num: int) -> None:
        # Push to small max-heap first
        heapq.heappush(self.small, -num)

        # Balance: every element in small must be <= every element in large
        if self.small and self.large and (-self.small[0] > self.large[0]):
            val = -heapq.heappop(self.small)
            heapq.heappush(self.large, val)

        # Size constraint: small heap can have at most 1 element more than large
        if len(self.small) > len(self.large) + 1:
            val = -heapq.heappop(self.small)
            heapq.heappush(self.large, val)
        elif len(self.large) > len(self.small):
            val = heapq.heappop(self.large)
            heapq.heappush(self.small, -val)

    def find_median(self) -> float:
        if len(self.small) > len(self.large):
            return float(-self.small[0])
        return (-self.small[0] + self.large[0]) / 2.0

if __name__ == "__main__":
    mf = MedianFinder()
    for n in [5, 2, 10, 4]:
        mf.add_num(n)
        print(f"Added {n}, Current Median: {mf.find_median()}")
```

---

<a name="pattern-10-backtracking"></a>
## Pattern 10: Backtracking & Recursive Search

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
ব্রুট-ফোর্স রিকার্শনের সাথে **Pruning (ছাঁটাই)** যুক্ত করে ব্যাকট্র্যাকিং কাজ করে। এটি একটি স্টেট স্পেস ট্রিতে ডেপথ-ফার্স্ট অনুসন্ধান করে এবং শর্ত লঙ্ঘন হলে এক ধাপ পেছনে ফিরে এসে (backtrack) অন্য শাখা অনুসন্ধান করে।
- **৩টি মূল ধাপ:** `Choose candidate` $\to$ `Explore recursively` $\to$ `Unchoose (Backtrack)`.

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Subsets** | Medium | Meta, Amazon, Google, Microsoft | [LeetCode 78](https://leetcode.com/problems/subsets/) |
| **Combination Sum** | Medium | Amazon, Meta, Google, Airbnb | [LeetCode 39](https://leetcode.com/problems/combination-sum/) |
| **Permutations** | Medium | Amazon, Meta, Microsoft, Google | [LeetCode 46](https://leetcode.com/problems/permutations/) |
| **Word Search** | Medium | Amazon, Microsoft, Bloomberg | [LeetCode 79](https://leetcode.com/problems/word-search/) |
| **N-Queens** | Hard | Meta, Amazon, Microsoft | [LeetCode 51](https://leetcode.com/problems/n-queens/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Subsets - $O(2^N)$ Time):

```python
from typing import List

def subsets(nums: List[int]) -> List[List[int]]:
    """
    Generates all subsets (Power Set) of unique integers.
    Time Complexity: O(N * 2^N)
    Space Complexity: O(N) recursion stack
    """
    result = []
    current_path = []

    def backtrack(index: int):
        if index == len(nums):
            result.append(list(current_path))
            return

        # 1. Decision to include nums[index]
        current_path.append(nums[index])
        backtrack(index + 1)

        # 2. Backtrack decision (exclude nums[index])
        current_path.pop()
        backtrack(index + 1)

    backtrack(0)
    return result

if __name__ == "__main__":
    print("Power Set:", subsets([1, 2, 3]))
```

---

<a name="pattern-11-graphs--topological-sort--union-find"></a>
## Pattern 11: Graphs (BFS, DFS, Topological Sort, Union-Find)

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
- **BFS (Queue):** আনওয়েটেড গ্রাফে সবচেয়ে কম দূরত্ব বা সবচেয়ে কম পদক্ষেপে পৌঁছানো (Shortest Path in Unweighted Graph)।
- **DFS:** সাইকেল ডিটেকশন, কম্পোনেন্ট ট্রাভার্সাল।
- **Topological Sort (Kahn's Algorithm with In-Degree):** ডিরেক্টেড অ্যাসাইক্লিক গ্রাফে (DAG) নির্ভরতার ক্রম সমাধান (Dependency resolution, Course Schedule)।
- **Disjoint Set Union (Union-Find with Path Compression):** কানেক্টেড কম্পোনেন্ট মার্জ ও সাইকেল পরীক্ষা $O(\alpha(N)) \approx O(1)$ সময়ে।

```
Topological Sort (Kahn's Algorithm):
Course Dependency: 0 -> 1 -> 3
                   0 -> 2 -> 3
In-degrees: [0: 0, 1: 1, 2: 1, 3: 2]
Queue with In-degree 0: [0]
Pop 0 -> Decrement neighbors 1 & 2 -> In-degrees: [1: 0, 2: 0] -> Queue: [1, 2]
Valid Order: [0, 1, 2, 3]
```

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Number of Islands** | Medium | Amazon, Google, Meta, Microsoft, Bloomberg | [LeetCode 200](https://leetcode.com/problems/number-of-islands/) |
| **Clone Graph** | Medium | Meta, Amazon, Microsoft | [LeetCode 133](https://leetcode.com/problems/clone-graph/) |
| **Pacific Atlantic Water Flow** | Medium | Google, Amazon, Meta | [LeetCode 417](https://leetcode.com/problems/pacific-atlantic-water-flow/) |
| **Course Schedule I & II** | Medium | Google, Amazon, Meta, Microsoft, Uber | [LeetCode 207](https://leetcode.com/problems/course-schedule/) & [210](https://leetcode.com/problems/course-schedule-ii/) |
| **Redundant Connection (Union-Find)** | Medium | Google, Amazon | [LeetCode 684](https://leetcode.com/problems/redundant-connection/) |
| **Network Delay Time (Dijkstra)** | Medium | Google, Amazon | [LeetCode 743](https://leetcode.com/problems/network-delay-time/) |
| **Word Ladder** | Hard | Amazon, Google, Meta, Microsoft | [LeetCode 127](https://leetcode.com/problems/word-ladder/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Course Schedule - Topological Sort via Kahn's BFS):

```python
from collections import deque, defaultdict
from typing import List

def can_finish(num_courses: int, prerequisites: List[List[int]]) -> bool:
    """
    Determines if it is possible to finish all courses without dependency deadlock (cycle).
    Time Complexity: O(V + E)
    Space Complexity: O(V + E)
    """
    in_degree = [0] * num_courses
    adj = defaultdict(list)

    for course, prereq in prerequisites:
        adj[prereq].append(course)
        in_degree[course] += 1

    # Queue of courses with 0 prerequisites
    queue = deque([i for i in range(num_courses) if in_degree[i] == 0])
    completed_courses = 0

    while queue:
        curr = queue.popleft()
        completed_courses += 1
        for neighbor in adj[curr]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)

    return completed_courses == num_courses

if __name__ == "__main__":
    print("Can Finish [2 courses, prereq [[1, 0]]]:", can_finish(2, [[1, 0]]))  # True
    print("Can Finish with Cycle [[1, 0], [0, 1]]:", can_finish(2, [[1, 0], [0, 1]]))  # False
```

---

<a name="pattern-12-dynamic-programming"></a>
## Pattern 12: Dynamic Programming (1D, 2D Grid, Knapsack, Intervals)

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
ডাইনামিক প্রোগ্রামিং (DP) হলো পুনরাবৃত্তিমূলক সাব-প্রবলেম (Overlapping Subproblems) এবং অপ্টিমাল সাব-স্ট্রাকচার (Optimal Substructure)-এর সমাধান।
- **Memoization (Top-Down):** সাধারণ রিকার্শনে ক্যাশ যুক্ত করা।
- **Tabulation (Bottom-Up):** বেস কেস থেকে শুরু করে স্টেট ট্রানজিশন সমীকরণ ($DP[i] = f(DP[i-1], \dots)$) দিয়ে টেবিল পূরণ করা।

```
Coin Change Problem (Coins: [1, 2, 5], Target: 11):
State Transition: DP[i] = min(DP[i - coin] + 1) for coin in coins
DP Array:
Index:   0  1  2  3  4  5  6  7  8  9 10 11
DP val: [0, 1, 1, 2, 2, 1, 2, 2, 3, 3, 2, 3] -> Min coins for 11 is 3 (5 + 5 + 1)!
```

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Climbing Stairs** | Easy | Amazon, Google, Microsoft, Apple | [LeetCode 70](https://leetcode.com/problems/climbing-stairs/) |
| **House Robber I & II** | Medium | Google, Amazon, Meta, Microsoft | [LeetCode 198](https://leetcode.com/problems/house-robber/) & [213](https://leetcode.com/problems/house-robber-ii/) |
| **Coin Change** | Medium | Amazon, Meta, Google, Microsoft, Bloomberg | [LeetCode 322](https://leetcode.com/problems/coin-change/) |
| **Longest Increasing Subsequence (LIS)**| Medium | Google, Microsoft, Amazon | [LeetCode 300](https://leetcode.com/problems/longest-increasing-subsequence/) |
| **Word Break** | Medium | Amazon, Meta, Google, Bloomberg | [LeetCode 139](https://leetcode.com/problems/word-break/) |
| **Unique Paths** | Medium | Google, Amazon, Microsoft | [LeetCode 62](https://leetcode.com/problems/unique-paths/) |
| **Longest Common Subsequence (LCS)**| Medium | Amazon, Google, Microsoft | [LeetCode 1143](https://leetcode.com/problems/longest-common-subsequence/) |
| **Edit Distance** | Medium/Hard | Google, Amazon, Microsoft | [LeetCode 72](https://leetcode.com/problems/edit-distance/) |
| **Burst Balloons** | Hard | Google, Amazon | [LeetCode 312](https://leetcode.com/problems/burst-balloons/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Coin Change - Bottom-Up DP):

```python
from typing import List

def coin_change(coins: List[int], amount: int) -> int:
    """
    Computes minimum number of coins needed to make up target amount.
    Time Complexity: O(amount * len(coins))
    Space Complexity: O(amount)
    """
    dp = [float("inf")] * (amount + 1)
    dp[0] = 0

    for i in range(1, amount + 1):
        for coin in coins:
            if i - coin >= 0:
                dp[i] = min(dp[i], dp[i - coin] + 1)

    return dp[amount] if dp[amount] != float("inf") else -1

if __name__ == "__main__":
    print("Min Coins for Amount 11 with [1, 2, 5]:", coin_change([1, 2, 5], 11))  # 3
```

---

<a name="pattern-13-greedy-algorithms--intervals"></a>
## Pattern 13: Greedy Algorithms & Interval Scheduling

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
প্রতিটি পদক্ষেপে দূরদর্শী হিসাব না করে স্থানীয়ভাবে সর্বোত্তম (Locally Optimal) সিদ্ধান্ত গ্রহণ করে বৈশ্বিক অপ্টিমাল সিদ্ধান্তে পৌঁছানোর কৌশলই হলো গ্রিডি।
- **ইন্টারভাল সমস্যা:** সাধারণত শুরুর সময় বা শেষের সময় অনুসারে সর্টিং ($O(N \log N)$) করে ওভারল্যাপিং ইন্টারভাল মার্জ বা রিমুভ করা হয়।

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Maximum Subarray (Kadane's Algorithm)**| Medium | Amazon, Apple, Microsoft, Google | [LeetCode 53](https://leetcode.com/problems/maximum-subarray/) |
| **Jump Game I & II** | Medium | Amazon, Google, Meta | [LeetCode 55](https://leetcode.com/problems/jump-game/) & [45](https://leetcode.com/problems/jump-game-ii/) |
| **Gas Station** | Medium | Google, Amazon, Microsoft | [LeetCode 134](https://leetcode.com/problems/gas-station/) |
| **Merge Intervals** | Medium | Meta, Google, Amazon, Microsoft, Apple, Bloomberg | [LeetCode 56](https://leetcode.com/problems/merge-intervals/) |
| **Non-overlapping Intervals** | Medium | Meta, Amazon, Google | [LeetCode 435](https://leetcode.com/problems/non-overlapping-intervals/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Merge Intervals):

```python
from typing import List

def merge_intervals(intervals: List[List[int]]) -> List[List[int]]:
    """
    Merges overlapping intervals into non-overlapping intervals.
    Time Complexity: O(N log N) - due to sorting
    Space Complexity: O(N)
    """
    if not intervals:
        return []

    # Sort primarily by interval start time
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]

    for current in intervals[1:]:
        prev = merged[-1]
        # Check if current interval overlaps with previous interval
        if current[0] <= prev[1]:
            prev[1] = max(prev[1], current[1])
        else:
            merged.append(current)

    return merged

if __name__ == "__main__":
    sample = [[1, 3], [2, 6], [8, 10], [15, 18]]
    print("Merged Intervals:", merge_intervals(sample))  # [[1, 6], [8, 10], [15, 18]]
```

---

<a name="pattern-14-bit-manipulation"></a>
## Pattern 14: Bit Manipulation & Fast Arithmetic

### মূল ধারণা ও অন্তর্দৃষ্টি (Pattern Intuition):
বিটওয়াইজ অপারেশনগুলো সরাসরি প্রসেসর ইন্সট্রাকশনে এক্সিকিউট হয় ($O(1)$ সময় ও জিরো মেমোরি)।
- **XOR ($A \oplus A = 0$ এবং $A \oplus 0 = A$):** ডুপ্লিকেট সংখ্যা বাতিল করতে।
- **Brian Kernighan's Algorithm ($n \& (n-1)$):** সংখ্যার সর্বনিম্ন সেট বিটটি (Rightmost set bit) তাৎক্ষণিক ০ করে দেয়।

### 🎯 MNC ইন্টারভিউ প্রশ্নাবলি ও লিংক:

| সমস্যা নাম (Problem) | লেভেল | শীর্ষ কোম্পানি ট্যাগ | লিটকোড লিংক |
| :--- | :---: | :--- | :--- |
| **Single Number** | Easy | Amazon, Meta, Google, Apple | [LeetCode 136](https://leetcode.com/problems/single-number/) |
| **Number of 1 Bits (Hamming Weight)**| Easy | Microsoft, Apple, Google | [LeetCode 191](https://leetcode.com/problems/number-of-1-bits/) |
| **Counting Bits** | Easy | Amazon, Apple, Meta | [LeetCode 338](https://leetcode.com/problems/counting-bits/) |
| **Reverse Bits** | Easy | Apple, Amazon, Google | [LeetCode 190](https://leetcode.com/problems/reverse-bits/) |
| **Missing Number** | Easy | Amazon, Microsoft, Meta | [LeetCode 268](https://leetcode.com/problems/missing-number/) |

### ক্যানোনিকাল কোড ইমপ্লিমেন্টেশন (Single Number & Number of 1 Bits):

```python
from typing import List

def single_number(nums: List[int]) -> int:
    """Finds the element that appears once where all others appear twice using XOR."""
    xor_sum = 0
    for n in nums:
        xor_sum ^= n
    return xor_sum

def count_set_bits(n: int) -> int:
    """Brian Kernighan's Algorithm: runs in O(number of set bits)."""
    count = 0
    while n:
        n &= (n - 1)
        count += 1
    return count

if __name__ == "__main__":
    print("Single Number in [4, 1, 2, 1, 2]:", single_number([4, 1, 2, 1, 2]))  # 4
    print("Set bits in 11 (binary 1011):", count_set_bits(11))                   # 3
```

---


---
<a name="the-ultimate-325-problem-sheet"></a>
## 🏆 দ্য আলটিমেট ৩২৫ প্রবলেম কারিকুলাম (Striver A2Z & MNC Master Sheet: 325 Problems)
গুগল, মেটা, অ্যামাজন, মাইক্রোসফট, উবার ও অ্যাপলের কোডিং ইন্টারভিউয়ের জন্য Striver's A2Z DSA Sheet, NeetCode All এবং Blind 75-এর সমন্বয়ে তৈরি ৩২৫টি নির্বাচিত লিটকোড সমস্যার পূর্ণাঙ্গ তালিকা। প্রতিটি সমস্যার সরাসরি লিটকোড লিংক, ডিফিকাল্টি, কী-প্যাটার্ন ও কোম্পানি ট্যাগ অন্তর্ভুক্ত করা হয়েছে:

### 📌 মডিউল ০১: অ্যারে ও হ্যাশিং (Arrays, Prefix Sums & Hashing) (40 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **1** | Two Sum | 🟢 Easy | Hash Map (Complement Lookup) | Google, Meta, Amazon | [LC two-sum](https://leetcode.com/problems/two-sum/) |
| **2** | Contains Duplicate | 🟢 Easy | Hash Set Lookup | Apple, Microsoft, Amazon | [LC contains-duplicate](https://leetcode.com/problems/contains-duplicate/) |
| **3** | Valid Anagram | 🟢 Easy | Frequency Array / Counter | Google, Uber, Amazon | [LC valid-anagram](https://leetcode.com/problems/valid-anagram/) |
| **4** | Group Anagrams | 🟡 Medium | Sorted String / Tuple Key | Meta, Amazon, Apple | [LC group-anagrams](https://leetcode.com/problems/group-anagrams/) |
| **5** | Top K Frequent Elements | 🟡 Medium | Bucket Sort / Min-Heap | Meta, Amazon, Google | [LC top-k-frequent-elements](https://leetcode.com/problems/top-k-frequent-elements/) |
| **6** | Product of Array Except Self | 🟡 Medium | Prefix & Suffix Products | Amazon, Apple, Microsoft | [LC product-of-array-except-self](https://leetcode.com/problems/product-of-array-except-self/) |
| **7** | Longest Consecutive Sequence | 🟡 Medium | Hash Set Sequence Building | Google, Meta, Microsoft | [LC longest-consecutive-sequence](https://leetcode.com/problems/longest-consecutive-sequence/) |
| **8** | Subarray Sum Equals K | 🟡 Medium | Prefix Sum + Frequency Map | Meta, Google, Amazon | [LC subarray-sum-equals-k](https://leetcode.com/problems/subarray-sum-equals-k/) |
| **9** | Continuous Subarray Sum | 🟡 Medium | Prefix Sum Modulo K | Meta, Amazon | [LC continuous-subarray-sum](https://leetcode.com/problems/continuous-subarray-sum/) |
| **10** | Contiguous Array | 🟡 Medium | Prefix Sum Balanced 0/1 | Google, Meta | [LC contiguous-array](https://leetcode.com/problems/contiguous-array/) |
| **11** | Best Time to Buy and Sell Stock | 🟢 Easy | Running Minimum Tracker | Amazon, Meta, Google | [LC best-time-to-buy-and-sell-stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) |
| **12** | Maximum Subarray (Kadane's Algorithm) | 🟡 Medium | Dynamic Programming / Greedy | Google, Amazon, Microsoft | [LC maximum-subarray](https://leetcode.com/problems/maximum-subarray/) |
| **13** | Maximum Sum Circular Subarray | 🟡 Medium | Kadane on Inverted Array | Google, Amazon | [LC maximum-sum-circular-subarray](https://leetcode.com/problems/maximum-sum-circular-subarray/) |
| **14** | Maximum Product Subarray | 🟡 Medium | Tracking Min & Max Products | Google, Amazon, Meta | [LC maximum-product-subarray](https://leetcode.com/problems/maximum-product-subarray/) |
| **15** | Majority Element | 🟢 Easy | Boyer-Moore Voting Algorithm | Google, Amazon, Microsoft | [LC majority-element](https://leetcode.com/problems/majority-element/) |
| **16** | Majority Element II | 🟡 Medium | Extended Boyer-Moore (N/3) | Google, Meta | [LC majority-element-ii](https://leetcode.com/problems/majority-element-ii/) |
| **17** | Sort Colors (Dutch National Flag) | 🟡 Medium | 3-Way Partitioning In-place | Microsoft, Meta, Google | [LC sort-colors](https://leetcode.com/problems/sort-colors/) |
| **18** | Next Permutation | 🟡 Medium | Suffix Pivot Swap & Reverse | Google, Meta, Amazon | [LC next-permutation](https://leetcode.com/problems/next-permutation/) |
| **19** | Rotate Array | 🟡 Medium | 3-Step Array Reversal | Amazon, Microsoft | [LC rotate-array](https://leetcode.com/problems/rotate-array/) |
| **20** | Move Zeroes | 🟢 Easy | In-place Two Pointers | Meta, Apple, Google | [LC move-zeroes](https://leetcode.com/problems/move-zeroes/) |
| **21** | Merge Intervals | 🟡 Medium | Interval Sorting & Merging | Google, Meta, Amazon | [LC merge-intervals](https://leetcode.com/problems/merge-intervals/) |
| **22** | Insert Interval | 🟡 Medium | Linear Partition Scan | Google, Meta, Uber | [LC insert-interval](https://leetcode.com/problems/insert-interval/) |
| **23** | Non-overlapping Intervals | 🟡 Medium | Greedy Earliest End Time | Meta, Google | [LC non-overlapping-intervals](https://leetcode.com/problems/non-overlapping-intervals/) |
| **24** | Set Matrix Zeroes | 🟡 Medium | O(1) Space In-place Marker Flags | Microsoft, Amazon, Meta | [LC set-matrix-zeroes](https://leetcode.com/problems/set-matrix-zeroes/) |
| **25** | Rotate Image | 🟡 Medium | Transpose & Horizontal Flip | Google, Microsoft, Amazon | [LC rotate-image](https://leetcode.com/problems/rotate-image/) |
| **26** | Spiral Matrix | 🟡 Medium | 4-Boundary Traversal | Microsoft, Apple, Google | [LC spiral-matrix](https://leetcode.com/problems/spiral-matrix/) |
| **27** | Pascal's Triangle | 🟢 Easy | Combinatorics Row Generation | Amazon, Google | [LC pascals-triangle](https://leetcode.com/problems/pascals-triangle/) |
| **28** | Find the Duplicate Number | 🟡 Medium | Floyd's Tortoise and Hare Cycle | Amazon, Microsoft, Google | [LC find-the-duplicate-number](https://leetcode.com/problems/find-the-duplicate-number/) |
| **29** | First Missing Positive | 🔴 Hard | Index As Hash Key Placement | Google, Meta, Amazon | [LC first-missing-positive](https://leetcode.com/problems/first-missing-positive/) |
| **30** | 4Sum | 🟡 Medium | Sort + 2 Pointers with Pruning | Amazon, Apple | [LC 4sum](https://leetcode.com/problems/4sum/) |
| **31** | Count Inversions in Array | 🟡 Medium | Modified Merge Sort Counting | Google, Amazon | Solve [Count Inversions in Array](https://www.geeksforgeeks.org/problems/inversion-of-array-1587115620/1) |
| **32** | Reverse Pairs | 🔴 Hard | Modified Merge Sort / Segment Tree | Google, Amazon | [LC reverse-pairs](https://leetcode.com/problems/reverse-pairs/) |
| **33** | Maximum Subarray Sum with One Deletion | 🟡 Medium | Prefix/Suffix Max Arrays | Google | [LC maximum-subarray-sum-with-one-deletion](https://leetcode.com/problems/maximum-subarray-sum-with-one-deletion/) |
| **34** | Find All Duplicates in an Array | 🟡 Medium | In-place Sign Inversion | Amazon, Microsoft | [LC find-all-duplicates-in-an-array](https://leetcode.com/problems/find-all-duplicates-in-an-array/) |
| **35** | Largest Number | 🟡 Medium | Custom Comparator Sort | Amazon, Microsoft | [LC largest-number](https://leetcode.com/problems/largest-number/) |
| **36** | Game of Life | 🟡 Medium | Bitmask State Encoding | Google, Amazon | [LC game-of-life](https://leetcode.com/problems/game-of-life/) |
| **37** | Diagonal Traverse | 🟡 Medium | Simulation / Up-Down Toggle | Google, Meta | [LC diagonal-traverse](https://leetcode.com/problems/diagonal-traverse/) |
| **38** | Grid Game | 🟡 Medium | Prefix Sum Balance Optimization | Google | [LC grid-game](https://leetcode.com/problems/grid-game/) |
| **39** | Subarray Sums Divisible by K | 🟡 Medium | Prefix Sum Modulo Frequency | Amazon, Meta | [LC subarray-sums-divisible-by-k](https://leetcode.com/problems/subarray-sums-divisible-by-k/) |
| **40** | Max Chunks To Make Sorted | 🟡 Medium | Running Max Prefix Property | Google | [LC max-chunks-to-make-sorted](https://leetcode.com/problems/max-chunks-to-make-sorted/) |

### 📌 মডিউল ০২: টু পয়েন্টার্স ও স্লাইডিং উইন্ডো (Two Pointers & Sliding Window) (25 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **41** | Valid Palindrome | 🟢 Easy | Inward Two Pointers | Meta, Microsoft | [LC valid-palindrome](https://leetcode.com/problems/valid-palindrome/) |
| **42** | Two Sum II - Input Array Is Sorted | 🟡 Medium | Bidirectional Two Pointers | Amazon, Google | [LC two-sum-ii-input-array-is-sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) |
| **43** | 3Sum | 🟡 Medium | Sort + Two Pointers + Skip Dups | Meta, Amazon, Google | [LC 3sum](https://leetcode.com/problems/3sum/) |
| **44** | 3Sum Closest | 🟡 Medium | Sort + Two Pointers Min Diff | Meta, Amazon | [LC 3sum-closest](https://leetcode.com/problems/3sum-closest/) |
| **45** | Container With Most Water | 🟡 Medium | Greedy Two Pointers Inward | Google, Meta, Amazon | [LC container-with-most-water](https://leetcode.com/problems/container-with-most-water/) |
| **46** | Trapping Rain Water | 🔴 Hard | Two Pointers with Left/Right Max | Google, Amazon, Meta | [LC trapping-rain-water](https://leetcode.com/problems/trapping-rain-water/) |
| **47** | Remove Duplicates from Sorted Array | 🟢 Easy | Slow & Fast Two Pointers | Meta, Microsoft | [LC remove-duplicates-from-sorted-array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) |
| **48** | Remove Duplicates from Sorted Array II | 🟡 Medium | K-Limit Two Pointers | Meta, Google | [LC remove-duplicates-from-sorted-array-ii](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/) |
| **49** | Squares of a Sorted Array | 🟢 Easy | Outward / Inward Two Pointers | Meta, Google | [LC squares-of-a-sorted-array](https://leetcode.com/problems/squares-of-a-sorted-array/) |
| **50** | Backspace String Compare | 🟢 Easy | Reverse Two Pointers | Google | [LC backspace-string-compare](https://leetcode.com/problems/backspace-string-compare/) |
| **51** | Longest Substring Without Repeating Characters | 🟡 Medium | Dynamic Sliding Window + Set | Amazon, Google, Meta | [LC longest-substring-without-repeating-characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) |
| **52** | Longest Repeating Character Replacement | 🟡 Medium | Frequency Count Window | Google, Amazon | [LC longest-repeating-character-replacement](https://leetcode.com/problems/longest-repeating-character-replacement/) |
| **53** | Permutation in String | 🟡 Medium | Fixed-size Window Frequency Match | Meta, Microsoft | [LC permutation-in-string](https://leetcode.com/problems/permutation-in-string/) |
| **54** | Find All Anagrams in a String | 🟡 Medium | Fixed-size Sliding Window | Meta, Amazon | [LC find-all-anagrams-in-a-string](https://leetcode.com/problems/find-all-anagrams-in-a-string/) |
| **55** | Minimum Window Substring | 🔴 Hard | Shrinkable Window + Target Map | Meta, Google, Uber | [LC minimum-window-substring](https://leetcode.com/problems/minimum-window-substring/) |
| **56** | Sliding Window Maximum | 🔴 Hard | Monotonic Decreasing Deque | Google, Amazon, Meta | [LC sliding-window-maximum](https://leetcode.com/problems/sliding-window-maximum/) |
| **57** | Max Consecutive Ones III | 🟡 Medium | At most K Zeroes Window | Meta, Google | [LC max-consecutive-ones-iii](https://leetcode.com/problems/max-consecutive-ones-iii/) |
| **58** | Fruit Into Baskets | 🟡 Medium | At most 2 Distinct Elements Window | Google | [LC fruit-into-baskets](https://leetcode.com/problems/fruit-into-baskets/) |
| **59** | Subarrays with K Different Integers | 🔴 Hard | Exact(K) = AtMost(K) - AtMost(K-1) | Amazon, Google | [LC subarrays-with-k-different-integers](https://leetcode.com/problems/subarrays-with-k-different-integers/) |
| **60** | Number of Substrings Containing All Three Characters | 🟡 Medium | Last Occurrence Tracking | Google | [LC number-of-substrings-containing-all-three-characters](https://leetcode.com/problems/number-of-substrings-containing-all-three-characters/) |
| **61** | Minimum Size Subarray Sum | 🟡 Medium | Shrinkable Sliding Window | Google, Microsoft | [LC minimum-size-subarray-sum](https://leetcode.com/problems/minimum-size-subarray-sum/) |
| **62** | Binary Subarrays With Sum | 🟡 Medium | Prefix Sum / AtMost Trick | Google | [LC binary-subarrays-with-sum](https://leetcode.com/problems/binary-subarrays-with-sum/) |
| **63** | Count Number of Nice Subarrays | 🟡 Medium | AtMost(K) - AtMost(K-1) | Amazon | [LC count-number-of-nice-subarrays](https://leetcode.com/problems/count-number-of-nice-subarrays/) |
| **64** | Maximum Points You Can Obtain from Cards | 🟡 Medium | Inverted Sliding Window | Google | [LC maximum-points-you-can-obtain-from-cards](https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/) |
| **65** | Minimum Window Subsequence | 🔴 Hard | Two Pointers Forward/Backward | Google, Meta | [LC minimum-window-subsequence](https://leetcode.com/problems/minimum-window-subsequence/) |

### 📌 মডিউল ০৩: বাইনারি সার্চ ও প্রেডিকেট মনোটোনিসিটি (Binary Search 1D/2D & Answer Space) (25 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **66** | Binary Search | 🟢 Easy | Standard Template | Google, Microsoft | [LC binary-search](https://leetcode.com/problems/binary-search/) |
| **67** | Search in Rotated Sorted Array | 🟡 Medium | Half-Sorted Determination | Amazon, Google, Meta | [LC search-in-rotated-sorted-array](https://leetcode.com/problems/search-in-rotated-sorted-array/) |
| **68** | Search in Rotated Sorted Array II | 🟡 Medium | Duplicate Boundary Shrinking | Amazon | [LC search-in-rotated-sorted-array-ii](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/) |
| **69** | Find Minimum in Rotated Sorted Array | 🟡 Medium | Right Boundary Comparison | Microsoft, Amazon | [LC find-minimum-in-rotated-sorted-array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) |
| **70** | Find Peak Element | 🟡 Medium | Gradient Ascent Binary Search | Meta, Google | [LC find-peak-element](https://leetcode.com/problems/find-peak-element/) |
| **71** | Find First and Last Position in Sorted Array | 🟡 Medium | Lower Bound & Upper Bound | Google, Meta, Microsoft | [LC find-first-and-last-position-of-element-in-sorted-array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/) |
| **72** | Search Insert Position | 🟢 Easy | Lower Bound Binary Search | Google, Amazon | [LC search-insert-position](https://leetcode.com/problems/search-insert-position/) |
| **73** | Single Element in a Sorted Array | 🟡 Medium | Even/Odd Index Parity | Amazon, Google | [LC single-element-in-a-sorted-array](https://leetcode.com/problems/single-element-in-a-sorted-array/) |
| **74** | Search a 2D Matrix | 🟡 Medium | Virtual 1D Array Flattening | Microsoft, Amazon | [LC search-a-2d-matrix](https://leetcode.com/problems/search-a-2d-matrix/) |
| **75** | Search a 2D Matrix II | 🟡 Medium | Top-Right Corner Elimination | Amazon, Google | [LC search-a-2d-matrix-ii](https://leetcode.com/problems/search-a-2d-matrix-ii/) |
| **76** | Koko Eating Bananas | 🟡 Medium | Binary Search on Answer Space | Google, Airbnb | [LC koko-eating-bananas](https://leetcode.com/problems/koko-eating-bananas/) |
| **77** | Minimum Number of Days to Make m Bouquets | 🟡 Medium | Predicate Monotonicity | Google | [LC minimum-number-of-days-to-make-m-bouquets](https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/) |
| **78** | Find the Smallest Divisor Given a Threshold | 🟡 Medium | Monotonic Ceiling Sum | Google | [LC find-the-smallest-divisor-given-a-threshold](https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/) |
| **79** | Capacity To Ship Packages Within D Days | 🟡 Medium | Greedy Partitioning on Answer | Google, Amazon | [LC capacity-to-ship-packages-within-d-days](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/) |
| **80** | Magnetic Force Between Two Balls (Aggressive Cows) | 🟡 Medium | Feasibility Predicate Search | Google, Amazon | [LC magnetic-force-between-two-balls](https://leetcode.com/problems/magnetic-force-between-two-balls/) |
| **81** | Book Allocation Problem | 🔴 Hard | Binary Search on Allocation Sum | Google, Microsoft | Solve [Book Allocation Problem](https://www.geeksforgeeks.org/problems/allocate-minimum-number-of-pages0937/1) |
| **82** | Split Array Largest Sum | 🔴 Hard | Binary Search on Subarray Bound | Google, Meta | [LC split-array-largest-sum](https://leetcode.com/problems/split-array-largest-sum/) |
| **83** | Median of Two Sorted Arrays | 🔴 Hard | Binary Search on Partition Cut | Google, Meta, Amazon | [LC median-of-two-sorted-arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) |
| **84** | Kth Element of Two Sorted Arrays | 🟡 Medium | Partition Cut Binary Search | Google, Amazon | Solve [Kth Element of Two Sorted Arrays](https://www.geeksforgeeks.org/problems/k-th-element-of-two-sorted-array1317/1) |
| **85** | Find Peak Element II (2D Matrix Peak) | 🟡 Medium | Column Max + Binary Search | Google | [LC find-a-peak-element-ii](https://leetcode.com/problems/find-a-peak-element-ii/) |
| **86** | Sqrt(x) | 🟢 Easy | Monotonic Square Search | Apple, Microsoft | [LC sqrtx](https://leetcode.com/problems/sqrtx/) |
| **87** | Find Kth Missing Positive Number | 🟢 Easy | Missing Count Search | Meta | [LC kth-missing-positive-number](https://leetcode.com/problems/kth-missing-positive-number/) |
| **88** | Minimize Max Distance to Gas Station | 🔴 Hard | BS on Float Distance Space | Google | [LC minimize-max-distance-to-gas-station](https://leetcode.com/problems/minimize-max-distance-to-gas-station/) |
| **89** | Heaters | 🟡 Medium | Binary Search for Nearest Heater | Google | [LC heaters](https://leetcode.com/problems/heaters/) |
| **90** | Time Based Key-Value Store | 🟡 Medium | Binary Search on Timestamp List | Google, Netflix | [LC time-based-key-value-store](https://leetcode.com/problems/time-based-key-value-store/) |

### 📌 মডিউল ০৪: স্ট্রিংস ও প্যাটার্ন ম্যাচিং (Strings & KMP/Rabin-Karp) (20 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **91** | Valid Parentheses | 🟢 Easy | Matching Stack | Meta, Amazon, Google | [LC valid-parentheses](https://leetcode.com/problems/valid-parentheses/) |
| **92** | Longest Common Prefix | 🟢 Easy | Vertical Scanning | Amazon, Apple | [LC longest-common-prefix](https://leetcode.com/problems/longest-common-prefix/) |
| **93** | String to Integer (atoi) | 🟡 Medium | State Machine / Overflow Handling | Microsoft, Amazon | [LC string-to-integer-atoi](https://leetcode.com/problems/string-to-integer-atoi/) |
| **94** | Find Index of First Occurrence (strStr) | 🟡 Medium | KMP Algorithm / Rolling Hash | Microsoft, Google | [LC find-the-index-of-the-first-occurrence-in-a-string](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/) |
| **95** | Repeated String Match | 🟡 Medium | Rabin-Karp / KMP Matching | Google | [LC repeated-string-match](https://leetcode.com/problems/repeated-string-match/) |
| **96** | Longest Happy Prefix | 🔴 Hard | KMP LPS Array Construction | Google | [LC longest-happy-prefix](https://leetcode.com/problems/longest-happy-prefix/) |
| **97** | Shortest Palindrome | 🔴 Hard | KMP LPS on s + '#' + rev(s) | Google, Microsoft | [LC shortest-palindrome](https://leetcode.com/problems/shortest-palindrome/) |
| **98** | Minimum Add to Make Parentheses Valid | 🟡 Medium | Balance Counter Tracking | Meta | [LC minimum-add-to-make-parentheses-valid](https://leetcode.com/problems/minimum-add-to-make-parentheses-valid/) |
| **99** | Count and Say | 🟡 Medium | Run-Length Encoding | Meta, Amazon | [LC count-and-say](https://leetcode.com/problems/count-and-say/) |
| **100** | Compare Version Numbers | 🟡 Medium | Two Pointers Dot Splitting | Apple, Microsoft | [LC compare-version-numbers](https://leetcode.com/problems/compare-version-numbers/) |
| **101** | Integer to Roman | 🟡 Medium | Greedy Value Subtraction | Amazon, Microsoft | [LC integer-to-roman](https://leetcode.com/problems/integer-to-roman/) |
| **102** | Roman to Integer | 🟢 Easy | Subtraction Precedence Rule | Amazon, Apple | [LC roman-to-integer](https://leetcode.com/problems/roman-to-integer/) |
| **103** | Reverse Words in a String | 🟡 Medium | In-place Word Reversals | Microsoft, Amazon | [LC reverse-words-in-a-string](https://leetcode.com/problems/reverse-words-in-a-string/) |
| **104** | Valid Palindrome II | 🟢 Easy | Single Deletion Branching | Meta | [LC valid-palindrome-ii](https://leetcode.com/problems/valid-palindrome-ii/) |
| **105** | Multiply Strings | 🟡 Medium | Grade-School Multiplication Simulation | Meta, Google | [LC multiply-strings](https://leetcode.com/problems/multiply-strings/) |
| **106** | Basic Calculator II | 🟡 Medium | Operator Precedence Stack | Meta, Amazon | [LC basic-calculator-ii](https://leetcode.com/problems/basic-calculator-ii/) |
| **107** | Minimum Remove to Make Valid Parentheses | 🟡 Medium | Stack Index Tracking | Meta, Amazon | [LC minimum-remove-to-make-valid-parentheses](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/) |
| **108** | Palindromic Substrings | 🟡 Medium | Expand Around Centers | Meta, Google | [LC palindromic-substrings](https://leetcode.com/problems/palindromic-substrings/) |
| **109** | Longest Palindromic Substring | 🟡 Medium | Expand Around Centers / Manacher | Amazon, Microsoft, Meta | [LC longest-palindromic-substring](https://leetcode.com/problems/longest-palindromic-substring/) |
| **110** | Text Justification | 🔴 Hard | Greedy Line Packing Simulation | Google, Uber | [LC text-justification](https://leetcode.com/problems/text-justification/) |

### 📌 মডিউল ০৫: লিঙ্কড লিস্ট ও সাইকেল ডিটেকশন (Linked Lists & In-Place Reversal) (20 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **111** | Reverse Linked List | 🟢 Easy | In-place 3-Pointer Iteration | Google, Amazon, Meta | [LC reverse-linked-list](https://leetcode.com/problems/reverse-linked-list/) |
| **112** | Middle of the Linked List | 🟢 Easy | Fast & Slow Pointers | Amazon, Microsoft | [LC middle-of-the-linked-list](https://leetcode.com/problems/middle-of-the-linked-list/) |
| **113** | Linked List Cycle | 🟢 Easy | Floyd's Tortoise & Hare | Microsoft, Amazon | [LC linked-list-cycle](https://leetcode.com/problems/linked-list-cycle/) |
| **114** | Linked List Cycle II | 🟡 Medium | Floyd's Cycle Entry Math | Microsoft, Google | [LC linked-list-cycle-ii](https://leetcode.com/problems/linked-list-cycle-ii/) |
| **115** | Palindrome Linked List | 🟢 Easy | Find Mid + Reverse + Compare | Meta, Amazon | [LC palindrome-linked-list](https://leetcode.com/problems/palindrome-linked-list/) |
| **116** | Remove Nth Node From End of List | 🟡 Medium | Fast-Slow Pointer Gap N | Google, Meta | [LC remove-nth-node-from-end-of-list](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) |
| **117** | Delete Node in a Linked List | 🟡 Medium | In-place Value Copying | Apple, Microsoft | [LC delete-node-in-a-linked-list](https://leetcode.com/problems/delete-node-in-a-linked-list/) |
| **118** | Odd Even Linked List | 🟡 Medium | Odd/Even Pointers Relinking | Google, Amazon | [LC odd-even-linked-list](https://leetcode.com/problems/odd-even-linked-list/) |
| **119** | Merge Two Sorted Lists | 🟢 Easy | Dummy Node Linear Merge | Amazon, Apple, Microsoft | [LC merge-two-sorted-lists](https://leetcode.com/problems/merge-two-sorted-lists/) |
| **120** | Sort List | 🟡 Medium | Merge Sort on Linked List | Google, Meta | [LC sort-list](https://leetcode.com/problems/sort-list/) |
| **121** | Intersection of Two Linked Lists | 🟢 Easy | Two Pointers Swapping Heads | Microsoft, Amazon | [LC intersection-of-two-linked-lists](https://leetcode.com/problems/intersection-of-two-linked-lists/) |
| **122** | Add Two Numbers | 🟡 Medium | Carry Propagation Simulation | Amazon, Meta, Google | [LC add-two-numbers](https://leetcode.com/problems/add-two-numbers/) |
| **123** | Reverse Nodes in k-Group | 🔴 Hard | Sublist Reversal in K-Batches | Google, Microsoft, Amazon | [LC reverse-nodes-in-k-group](https://leetcode.com/problems/reverse-nodes-in-k-group/) |
| **124** | Rotate List | 🟡 Medium | Ring Connection + Cut at K % N | Microsoft, Amazon | [LC rotate-list](https://leetcode.com/problems/rotate-list/) |
| **125** | Copy List with Random Pointer | 🟡 Medium | In-place Node Interleaving | Amazon, Meta, Microsoft | [LC copy-list-with-random-pointer](https://leetcode.com/problems/copy-list-with-random-pointer/) |
| **126** | Flatten a Multilevel Doubly Linked List | 🟡 Medium | DFS Traversal / Stack | Bloomberg, Uber | [LC flatten-a-multilevel-doubly-linked-list](https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/) |
| **127** | Merge k Sorted Lists | 🔴 Hard | Min-Heap / Divide and Conquer | Amazon, Google, Meta | [LC merge-k-sorted-lists](https://leetcode.com/problems/merge-k-sorted-lists/) |
| **128** | LRU Cache | 🟡 Medium | Hash Map + Doubly Linked List | Google, Meta, Amazon | [LC lru-cache](https://leetcode.com/problems/lru-cache/) |
| **129** | LFU Cache | 🔴 Hard | Frequency Map + Doubly Linked Lists | Amazon, Google | [LC lfu-cache](https://leetcode.com/problems/lfu-cache/) |
| **130** | Remove Duplicates from Sorted List II | 🟡 Medium | Dummy Node Skip All Matches | Microsoft, Amazon | [LC remove-duplicates-from-sorted-list-ii](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/) |

### 📌 মডিউল ০৬: স্ট্যাক, কিউ ও মনোটোনিক স্ট্যাক (Stack, Queue & Monotonic Stack) (25 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **131** | Min Stack | 🟡 Medium | Auxiliary Stack / Encoded Difference | Amazon, Bloomberg | [LC min-stack](https://leetcode.com/problems/min-stack/) |
| **132** | Implement Queue using Stacks | 🟢 Easy | Dual In/Out Stacks | Microsoft, Amazon | [LC implement-queue-using-stacks](https://leetcode.com/problems/implement-queue-using-stacks/) |
| **133** | Implement Stack using Queues | 🟢 Easy | Single Queue Rotation | Google | [LC implement-stack-using-queues](https://leetcode.com/problems/implement-stack-using-queues/) |
| **134** | Next Greater Element I | 🟢 Easy | Monotonic Decreasing Stack + Map | Amazon | [LC next-greater-element-i](https://leetcode.com/problems/next-greater-element-i/) |
| **135** | Next Greater Element II (Circular) | 🟡 Medium | Monotonic Stack on 2N Range | Google, Amazon | [LC next-greater-element-ii](https://leetcode.com/problems/next-greater-element-ii/) |
| **136** | Daily Temperatures | 🟡 Medium | Monotonic Decreasing Stack | Meta, Amazon, Google | [LC daily-temperatures](https://leetcode.com/problems/daily-temperatures/) |
| **137** | Largest Rectangle in Histogram | 🔴 Hard | Monotonic Increasing Stack | Google, Meta, Amazon | [LC largest-rectangle-in-histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/) |
| **138** | Maximal Rectangle | 🔴 Hard | 2D Grid to Histogram Rows | Google, Amazon | [LC maximal-rectangle](https://leetcode.com/problems/maximal-rectangle/) |
| **139** | Online Stock Span | 🟡 Medium | Monotonic Decreasing Stack + Weight | Amazon, Google | [LC online-stock-span](https://leetcode.com/problems/online-stock-span/) |
| **140** | Asteroid Collision | 🟡 Medium | Collision Stack Simulation | Amazon, Uber | [LC asteroid-collision](https://leetcode.com/problems/asteroid-collision/) |
| **141** | Sum of Subarray Minimums | 🟡 Medium | PLE & NLE Monotonic Stack | Amazon, Google | [LC sum-of-subarray-minimums](https://leetcode.com/problems/sum-of-subarray-minimums/) |
| **142** | Sum of Subarray Ranges | 🟡 Medium | Sum(Maxs) - Sum(Mins) Stack | Amazon | [LC sum-of-subarray-ranges](https://leetcode.com/problems/sum-of-subarray-ranges/) |
| **143** | Remove K Digits | 🟡 Medium | Monotonic Increasing Stack | Google, Microsoft | [LC remove-k-digits](https://leetcode.com/problems/remove-k-digits/) |
| **144** | 132 Pattern | 🟡 Medium | Monotonic Stack Backward Scan | Amazon, Google | [LC 132-pattern](https://leetcode.com/problems/132-pattern/) |
| **145** | Evaluate Reverse Polish Notation | 🟡 Medium | Operand Stack Evaluation | Amazon, Google | [LC evaluate-reverse-polish-notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/) |
| **146** | Basic Calculator | 🔴 Hard | Sign & Result Parenthesis Stack | Google, Meta | [LC basic-calculator](https://leetcode.com/problems/basic-calculator/) |
| **147** | Decode String | 🟡 Medium | Multiplier & String Stack | Google, Bloomberg | [LC decode-string](https://leetcode.com/problems/decode-string/) |
| **148** | Simplify Path | 🟡 Medium | Unix Path Directory Stack | Meta, Google | [LC simplify-path](https://leetcode.com/problems/simplify-path/) |
| **149** | Car Fleet | 🟡 Medium | Monotonic Stack on Arrival Time | Google | [LC car-fleet](https://leetcode.com/problems/car-fleet/) |
| **150** | Design Circular Queue | 🟡 Medium | Ring Array with Head/Tail Modulo | Amazon, Microsoft | [LC design-circular-queue](https://leetcode.com/problems/design-circular-queue/) |
| **151** | Design Circular Deque | 🟡 Medium | Double-Ended Ring Array | Google | [LC design-circular-deque](https://leetcode.com/problems/design-circular-deque/) |
| **152** | Trapping Rain Water (Stack Solution) | 🔴 Hard | Monotonic Decreasing Stack | Google, Meta | [LC trapping-rain-water](https://leetcode.com/problems/trapping-rain-water/) |
| **153** | Score of Parentheses | 🟡 Medium | Depth Level Multiplier Stack | Meta | [LC score-of-parentheses](https://leetcode.com/problems/score-of-parentheses/) |
| **154** | Check if Parentheses String Can Be Valid | 🟡 Medium | Forward & Backward Balance | Google | [LC check-if-a-parentheses-string-can-be-valid](https://leetcode.com/problems/check-if-a-parentheses-string-can-be-valid/) |
| **155** | Longest Valid Parentheses | 🔴 Hard | Index Stack / Dynamic Programming | Google, Meta | [LC longest-valid-parentheses](https://leetcode.com/problems/longest-valid-parentheses/) |

### 📌 মডিউল ০৭: রিকার্শন ও ব্যাকট্র্যাকিং (Recursion & Backtracking) (20 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **156** | Subsets | 🟡 Medium | Pick / Don't Pick Recursion | Meta, Google, Amazon | [LC subsets](https://leetcode.com/problems/subsets/) |
| **157** | Subsets II (With Duplicates) | 🟡 Medium | Sort + Duplicate Branch Pruning | Amazon, Meta | [LC subsets-ii](https://leetcode.com/problems/subsets-ii/) |
| **158** | Permutations | 🟡 Medium | Swap / Visited Array Backtracking | Microsoft, Meta, Google | [LC permutations](https://leetcode.com/problems/permutations/) |
| **159** | Permutations II | 🟡 Medium | Frequency Map Branch Pruning | Amazon, Google | [LC permutations-ii](https://leetcode.com/problems/permutations-ii/) |
| **160** | Combination Sum | 🟡 Medium | Unbounded Element Decision Tree | Google, Meta, Amazon | [LC combination-sum](https://leetcode.com/problems/combination-sum/) |
| **161** | Combination Sum II | 🟡 Medium | Single Use + Duplicate Skipping | Meta, Amazon | [LC combination-sum-ii](https://leetcode.com/problems/combination-sum-ii/) |
| **162** | Combination Sum III | 🟡 Medium | K Digits Summing to N DFS | Google | [LC combination-sum-iii](https://leetcode.com/problems/combination-sum-iii/) |
| **163** | Letter Combinations of a Phone Number | 🟡 Medium | Cartesian Product Recursion | Amazon, Meta, Google | [LC letter-combinations-of-a-phone-number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/) |
| **164** | Generate Parentheses | 🟡 Medium | Open/Close Constraint Recursion | Meta, Amazon, Google | [LC generate-parentheses](https://leetcode.com/problems/generate-parentheses/) |
| **165** | Palindrome Partitioning | 🟡 Medium | Prefix Palindrome + Backtrack | Google, Meta | [LC palindrome-partitioning](https://leetcode.com/problems/palindrome-partitioning/) |
| **166** | Word Search | 🟡 Medium | 2D Grid DFS with Cell Masking | Amazon, Microsoft, Meta | [LC word-search](https://leetcode.com/problems/word-search/) |
| **167** | N-Queens | 🔴 Hard | Column & Diagonal Bitmask DFS | Google, Meta, Microsoft | [LC n-queens](https://leetcode.com/problems/n-queens/) |
| **168** | N-Queens II | 🔴 Hard | Bitmask Fast Placement Count | Google | [LC n-queens-ii](https://leetcode.com/problems/n-queens-ii/) |
| **169** | Sudoku Solver | 🔴 Hard | Row/Col/Box Validation Backtrack | Google, Microsoft | [LC sudoku-solver](https://leetcode.com/problems/sudoku-solver/) |
| **170** | Word Break II | 🔴 Hard | Backtracking with Memoization | Google, Amazon | [LC word-break-ii](https://leetcode.com/problems/word-break-ii/) |
| **171** | Restore IP Addresses | 🟡 Medium | 4-Segment Valid Number Partition | Amazon, Microsoft | [LC restore-ip-addresses](https://leetcode.com/problems/restore-ip-addresses/) |
| **172** | Partition to K Equal Sum Subsets | 🟡 Medium | Target Bucket Filling DFS | Google, Meta | [LC partition-to-k-equal-sum-subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/) |
| **173** | Matchsticks to Square | 🟡 Medium | 4 Equal Buckets Backtracking | Google | [LC matchsticks-to-square](https://leetcode.com/problems/matchsticks-to-square/) |
| **174** | Rat in a Maze | 🟡 Medium | 4-Direction Grid Backtracking | Amazon, Microsoft | Solve [Rat in a Maze](https://www.geeksforgeeks.org/problems/rat-in-a-maze-problem/1) |
| **175** | Expression Add Operators | 🔴 Hard | Backtracking with Prev Operand | Meta, Google | [LC expression-add-operators](https://leetcode.com/problems/expression-add-operators/) |

### 📌 মডিউল ০৮: বাইনারি ট্রি ও বিএসটি (Binary Trees & Binary Search Trees) (30 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **176** | Binary Tree Preorder Traversal | 🟢 Easy | Iterative Stack / Morris | Google, Microsoft | [LC binary-tree-preorder-traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/) |
| **177** | Binary Tree Inorder Traversal | 🟢 Easy | Left Spine Stack / Morris | Amazon, Microsoft | [LC binary-tree-inorder-traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/) |
| **178** | Binary Tree Postorder Traversal | 🟢 Easy | 2 Stacks / 1 Stack Peek | Google | [LC binary-tree-postorder-traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/) |
| **179** | Binary Tree Level Order Traversal | 🟡 Medium | Queue BFS Traversal | Amazon, Meta, Google | [LC binary-tree-level-order-traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/) |
| **180** | Binary Tree Zigzag Level Order Traversal | 🟡 Medium | Queue BFS with Level Deque | Amazon, Microsoft | [LC binary-tree-zigzag-level-order-traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/) |
| **181** | Maximum Depth of Binary Tree | 🟢 Easy | Bottom-up Recursive DFS | Amazon, Apple | [LC maximum-depth-of-binary-tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/) |
| **182** | Balanced Binary Tree | 🟢 Easy | Height DFS with Early -1 Exit | Amazon, Google | [LC balanced-binary-tree](https://leetcode.com/problems/balanced-binary-tree/) |
| **183** | Diameter of Binary Tree | 🟢 Easy | Global Max Update during DFS | Meta, Amazon, Google | [LC diameter-of-binary-tree](https://leetcode.com/problems/diameter-of-binary-tree/) |
| **184** | Binary Tree Maximum Path Sum | 🔴 Hard | Max Gain Subtree DFS | Meta, Google, Amazon | [LC binary-tree-maximum-path-sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/) |
| **185** | Same Tree | 🟢 Easy | Structural & Value Recursive Match | Amazon, Google | [LC same-tree](https://leetcode.com/problems/same-tree/) |
| **186** | Symmetric Tree | 🟢 Easy | Mirror DFS on Left/Right Subtrees | Amazon, Microsoft | [LC symmetric-tree](https://leetcode.com/problems/symmetric-tree/) |
| **187** | Binary Tree Right Side View | 🟡 Medium | Reverse Preorder (Root-Right-Left) | Meta, Amazon | [LC binary-tree-right-side-view](https://leetcode.com/problems/binary-tree-right-side-view/) |
| **188** | Top View of Binary Tree | 🟡 Medium | Vertical Column BFS with Map | Amazon, Microsoft | Solve [Top View of Binary Tree](https://www.geeksforgeeks.org/problems/top-view-of-binary-tree/1) |
| **189** | Bottom View of Binary Tree | 🟡 Medium | Vertical Column Overwrite BFS | Amazon | Solve [Bottom View of Binary Tree](https://www.geeksforgeeks.org/problems/bottom-view-of-binary-tree/1) |
| **190** | Vertical Order Traversal of Binary Tree | 🔴 Hard | Column, Row, Value Multi-Key Sorting | Google, Meta, Amazon | [LC vertical-order-traversal-of-a-binary-tree](https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/) |
| **191** | Lowest Common Ancestor of Binary Tree | 🟡 Medium | Subtree Presence Recursive DFS | Meta, Amazon, Google | [LC lowest-common-ancestor-of-a-binary-tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/) |
| **192** | Lowest Common Ancestor of BST | 🟡 Medium | BST Value Split Comparison | Microsoft, Amazon | [LC lowest-common-ancestor-of-a-binary-search-tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) |
| **193** | Construct Tree from Preorder & Inorder | 🟡 Medium | Root Lookup in Inorder Map | Amazon, Microsoft | [LC construct-binary-tree-from-preorder-and-inorder-traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) |
| **194** | Construct Tree from Inorder & Postorder | 🟡 Medium | Postorder Tail as Subtree Root | Amazon, Google | [LC construct-binary-tree-from-inorder-and-postorder-traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/) |
| **195** | Serialize and Deserialize Binary Tree | 🔴 Hard | Preorder BFS with Null Markers | Google, Meta, Amazon | [LC serialize-and-deserialize-binary-tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) |
| **196** | Count Complete Tree Nodes | 🟡 Medium | Binary Search on Leaves via Height | Google | [LC count-complete-tree-nodes](https://leetcode.com/problems/count-complete-tree-nodes/) |
| **197** | All Nodes Distance K in Binary Tree | 🟡 Medium | Parent Mapping + Graph BFS | Meta, Amazon | [LC all-nodes-distance-k-in-binary-tree](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/) |
| **198** | Flatten Binary Tree to Linked List | 🟡 Medium | Right-to-Left Morris Traversal | Meta, Microsoft | [LC flatten-binary-tree-to-linked-list](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/) |
| **199** | Validate Binary Search Tree | 🟡 Medium | Min/Max Interval Bounds DFS | Amazon, Meta, Google | [LC validate-binary-search-tree](https://leetcode.com/problems/validate-binary-search-tree/) |
| **200** | Kth Smallest Element in a BST | 🟡 Medium | Inorder Traversal Counter | Amazon, Google | [LC kth-smallest-element-in-a-bst](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) |
| **201** | Binary Search Tree Iterator | 🟡 Medium | Controlled Inorder Traversal Stack | Meta, Google | [LC binary-search-tree-iterator](https://leetcode.com/problems/binary-search-tree-iterator/) |
| **202** | Two Sum IV - Input is a BST | 🟢 Easy | Dual BST Iterators (Next/Before) | Meta, Amazon | [LC two-sum-iv-input-is-a-bst](https://leetcode.com/problems/two-sum-iv-input-is-a-bst/) |
| **203** | Recover Binary Search Tree | 🟡 Medium | Inorder Inversion Pointer Swap | Google, Microsoft | [LC recover-binary-search-tree](https://leetcode.com/problems/recover-binary-search-tree/) |
| **204** | Maximum Sum BST in Binary Tree | 🔴 Hard | Postorder [isBST, min, max, sum] | Google | [LC maximum-sum-bst-in-binary-tree](https://leetcode.com/problems/maximum-sum-bst-in-binary-tree/) |
| **205** | Delete Node in a BST | 🟡 Medium | Inorder Successor Replacement | Microsoft, Amazon | [LC delete-node-in-a-bst](https://leetcode.com/problems/delete-node-in-a-bst/) |

### 📌 মডিউল ০৯: হিপস ও প্রায়োরিটি কিউ (Heaps & Priority Queues) (15 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **206** | Kth Largest Element in an Array | 🟡 Medium | Min-Heap of Size K / Quickselect | Meta, Amazon, Google | [LC kth-largest-element-in-an-array](https://leetcode.com/problems/kth-largest-element-in-an-array/) |
| **207** | Top K Frequent Words | 🟡 Medium | Min-Heap + Custom Comparator | Amazon, Uber | [LC top-k-frequent-words](https://leetcode.com/problems/top-k-frequent-words/) |
| **208** | Find Median from Data Stream | 🔴 Hard | Two Heaps (Max-Heap & Min-Heap) | Google, Amazon, Meta | [LC find-median-from-data-stream](https://leetcode.com/problems/find-median-from-data-stream/) |
| **209** | Merge k Sorted Lists | 🔴 Hard | Node Pointer Min-Heap | Meta, Amazon, Google | [LC merge-k-sorted-lists](https://leetcode.com/problems/merge-k-sorted-lists/) |
| **210** | Task Scheduler | 🟡 Medium | Max-Heap Frequency / Math Formula | Meta, Google | [LC task-scheduler](https://leetcode.com/problems/task-scheduler/) |
| **211** | Reorganize String | 🟡 Medium | Max-Heap with Cooldown Hold | Google, Amazon | [LC reorganize-string](https://leetcode.com/problems/reorganize-string/) |
| **212** | K Closest Points to Origin | 🟡 Medium | Max-Heap of Size K | Meta, Amazon | [LC k-closest-points-to-origin](https://leetcode.com/problems/k-closest-points-to-origin/) |
| **213** | Design Twitter | 🟡 Medium | Min-Heap K-way Feed Merge | Twitter, Amazon | [LC design-twitter](https://leetcode.com/problems/design-twitter/) |
| **214** | IPO | 🔴 Hard | Two Heaps (Capital Min, Profit Max) | Google | [LC ipo](https://leetcode.com/problems/ipo/) |
| **215** | Smallest Range Covering Elements from K Lists | 🔴 Hard | Min-Heap Tracking (val, list, idx) | Google | [LC smallest-range-covering-elements-from-k-lists](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/) |
| **216** | Maximum Subsequence Score | 🟡 Medium | Sort on Multiplier + Min-Heap | Google, Amazon | [LC maximum-subsequence-score](https://leetcode.com/problems/maximum-subsequence-score/) |
| **217** | Minimum Cost to Connect Sticks | 🟡 Medium | Min-Heap Huffman Greedy | Amazon | [LC minimum-cost-to-connect-sticks](https://leetcode.com/problems/minimum-cost-to-connect-sticks/) |
| **218** | Seat Reservation Manager | 🟡 Medium | Min-Heap of Available Seats | Google | [LC seat-reservation-manager](https://leetcode.com/problems/seat-reservation-manager/) |
| **219** | Single-Threaded CPU | 🟡 Medium | Sort on Arrival + Min-Heap Duration | Google | [LC single-threaded-cpu](https://leetcode.com/problems/single-threaded-cpu/) |
| **220** | Trapping Rain Water II (3D Grid) | 🔴 Hard | Min-Heap Priority Queue BFS | Google, Amazon | [LC trapping-rain-water-ii](https://leetcode.com/problems/trapping-rain-water-ii/) |

### 📌 মডিউল ১০: গ্রিডি অ্যালগরিদম ও ইন্টারভাল শিডিউলিং (Greedy Algorithms & Intervals) (15 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **221** | Assign Cookies | 🟢 Easy | Greedy Sorting on Greed & Size | Amazon | [LC assign-cookies](https://leetcode.com/problems/assign-cookies/) |
| **222** | Lemonade Change | 🟢 Easy | Greedy 5/10/20 Bill Conservation | Amazon | [LC lemonade-change](https://leetcode.com/problems/lemonade-change/) |
| **223** | Jump Game | 🟡 Medium | Running Max Reachable Boundary | Amazon, Meta, Google | [LC jump-game](https://leetcode.com/problems/jump-game/) |
| **224** | Jump Game II | 🟡 Medium | BFS Range Window Greedy Jumps | Amazon, Google | [LC jump-game-ii](https://leetcode.com/problems/jump-game-ii/) |
| **225** | Gas Station | 🟡 Medium | Circular Deficit Balance Tracking | Google, Amazon | [LC gas-station](https://leetcode.com/problems/gas-station/) |
| **226** | Candy | 🔴 Hard | Two-Pass Left & Right Traversal | Google, Amazon | [LC candy](https://leetcode.com/problems/candy/) |
| **227** | Minimum Platforms | 🟡 Medium | Two Pointers on Sorted Arrivals/Dep | Amazon, Microsoft | Solve [Minimum Platforms](https://www.geeksforgeeks.org/problems/minimum-platforms-1587115620/1) |
| **228** | Job Sequencing Problem | 🟡 Medium | Disjoint Set / Profit Slot Sorting | Amazon | Solve [Job Sequencing Problem](https://www.geeksforgeeks.org/problems/job-sequencing-problem-1587115620/1) |
| **229** | N Meetings in One Room | 🟢 Easy | Greedy Sorting by End Time | Microsoft, Amazon | Solve [N Meetings in One Room](https://www.geeksforgeeks.org/problems/n-meetings-in-one-room-1587115620/1) |
| **230** | Partition Labels | 🟡 Medium | Last Occurrence Boundary Window | Amazon, Google | [LC partition-labels](https://leetcode.com/problems/partition-labels/) |
| **231** | Maximum Units on a Truck | 🟢 Easy | Greedy Sorting on Unit Capacity | Amazon | [LC maximum-units-on-a-truck](https://leetcode.com/problems/maximum-units-on-a-truck/) |
| **232** | Meeting Rooms | 🟢 Easy | Interval Sorting Overlap Check | Meta, Google | [LC meeting-rooms](https://leetcode.com/problems/meeting-rooms/) |
| **233** | Meeting Rooms II | 🟡 Medium | Min-Heap on End Times / Sweep-line | Google, Meta, Amazon | [LC meeting-rooms-ii](https://leetcode.com/problems/meeting-rooms-ii/) |
| **234** | Hand of Straights | 🟡 Medium | Frequency Map + Consecutive Greedy | Google | [LC hand-of-straights](https://leetcode.com/problems/hand-of-straights/) |
| **235** | Valid Starting City | 🟡 Medium | Running Minimum Balance Point | Google | [LC gas-station](https://leetcode.com/problems/gas-station/) |

### 📌 মডিউল ১১: গ্রাফস, শর্টেস্ট পাথ, এমএসটি ও ডিএসইউ (Graphs, Dijkstra, MST, DSU) (35 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **236** | Number of Islands | 🟡 Medium | 2D Grid BFS / DFS | Amazon, Google, Meta | [LC number-of-islands](https://leetcode.com/problems/number-of-islands/) |
| **237** | Max Area of Island | 🟡 Medium | Connected Component Size DFS | Meta, Google | [LC max-area-of-island](https://leetcode.com/problems/max-area-of-island/) |
| **238** | Flood Fill | 🟢 Easy | Simple Recursive BFS / DFS | Google, Microsoft | [LC flood-fill](https://leetcode.com/problems/flood-fill/) |
| **239** | Rotting Oranges | 🟡 Medium | Multi-Source BFS with Timer | Amazon, Microsoft | [LC rotting-oranges](https://leetcode.com/problems/rotting-oranges/) |
| **240** | 01 Matrix | 🟡 Medium | Multi-Source BFS from Zero Cells | Google, Amazon | [LC 01-matrix](https://leetcode.com/problems/01-matrix/) |
| **241** | Surrounded Regions | 🟡 Medium | Boundary-Connected DFS Marking | Google, Amazon | [LC surrounded-regions](https://leetcode.com/problems/surrounded-regions/) |
| **242** | Number of Enclaves | 🟡 Medium | Boundary DFS Land Removal | Google | [LC number-of-enclaves](https://leetcode.com/problems/number-of-enclaves/) |
| **243** | Is Graph Bipartite? | 🟡 Medium | 2-Coloring BFS / DFS | Meta, Amazon | [LC is-graph-bipartite](https://leetcode.com/problems/is-graph-bipartite/) |
| **244** | Course Schedule | 🟡 Medium | Cycle Detection in Directed Graph | Google, Amazon, Meta | [LC course-schedule](https://leetcode.com/problems/course-schedule/) |
| **245** | Course Schedule II | 🟡 Medium | Kahn's Algorithm (BFS Topo Sort) | Amazon, Google, Meta | [LC course-schedule-ii](https://leetcode.com/problems/course-schedule-ii/) |
| **246** | Alien Dictionary | 🔴 Hard | Character Precedence Topo Sort | Google, Meta, Amazon | [LC alien-dictionary](https://leetcode.com/problems/alien-dictionary/) |
| **247** | Minimum Height Trees | 🟡 Medium | Leaf Trimming Topological BFS | Google | [LC minimum-height-trees](https://leetcode.com/problems/minimum-height-trees/) |
| **248** | Clone Graph | 🟡 Medium | Visited Map + Recursive DFS | Meta, Google, Amazon | [LC clone-graph](https://leetcode.com/problems/clone-graph/) |
| **249** | Pacific Atlantic Water Flow | 🟡 Medium | Reverse Ocean Boundary BFS | Google, Amazon | [LC pacific-atlantic-water-flow](https://leetcode.com/problems/pacific-atlantic-water-flow/) |
| **250** | Shortest Path in Binary Matrix | 🟡 Medium | 8-Direction BFS Shortest Path | Meta, Google | [LC shortest-path-in-binary-matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/) |
| **251** | Word Ladder | 🔴 Hard | Word Transition BFS with Levels | Amazon, Google, Meta | [LC word-ladder](https://leetcode.com/problems/word-ladder/) |
| **252** | Word Ladder II | 🔴 Hard | BFS Distances + Backtracking DFS | Google, Amazon | [LC word-ladder-ii](https://leetcode.com/problems/word-ladder-ii/) |
| **253** | Network Delay Time | 🟡 Medium | Dijkstra's Algorithm with Min-Heap | Google, Amazon | [LC network-delay-time](https://leetcode.com/problems/network-delay-time/) |
| **254** | Path With Minimum Effort | 🟡 Medium | Dijkstra on 2D Grid Heights | Google | [LC path-with-minimum-effort](https://leetcode.com/problems/path-with-minimum-effort/) |
| **255** | Cheapest Flights Within K Stops | 🟡 Medium | Bellman-Ford / Modified BFS | Google, Amazon | [LC cheapest-flights-within-k-stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/) |
| **256** | Number of Ways to Arrive at Destination | 🟡 Medium | Dijkstra + Combinatorics Ways Array | Google | [LC number-of-ways-to-arrive-at-destination](https://leetcode.com/problems/number-of-ways-to-arrive-at-destination/) |
| **257** | Find City with Smallest Neighbors | 🟡 Medium | Floyd-Warshall All-Pairs Paths | Google | [LC find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/) |
| **258** | Redundant Connection | 🟡 Medium | Union-Find (DSU) Cycle Detection | Google, Meta | [LC redundant-connection](https://leetcode.com/problems/redundant-connection/) |
| **259** | Number of Provinces | 🟡 Medium | Disjoint Set Union (DSU) / DFS | Google, Amazon | [LC number-of-provinces](https://leetcode.com/problems/number-of-provinces/) |
| **260** | Most Stones Removed with Same Row/Col | 🟡 Medium | DSU on Coordinates | Google | [LC most-stones-removed-with-same-row-or-column](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/) |
| **261** | Accounts Merge | 🟡 Medium | DSU on User Email Graphs | Meta, Google | [LC accounts-merge](https://leetcode.com/problems/accounts-merge/) |
| **262** | Number of Operations to Make Network Connected | 🟡 Medium | DSU Components vs Extra Edges | Google | [LC number-of-operations-to-make-network-connected](https://leetcode.com/problems/number-of-operations-to-make-network-connected/) |
| **263** | Making A Large Island | 🔴 Hard | Precomputed Island Sizes + DSU Flip | Google, Meta | [LC making-a-large-island](https://leetcode.com/problems/making-a-large-island/) |
| **264** | Swim in Rising Water | 🔴 Hard | Dijkstra / Binary Search + BFS | Google | [LC swim-in-rising-water](https://leetcode.com/problems/swim-in-rising-water/) |
| **265** | Connecting Cities With Minimum Cost | 🟡 Medium | Kruskal's / Prim's MST | Amazon | [LC connecting-cities-with-minimum-cost](https://leetcode.com/problems/connecting-cities-with-minimum-cost/) |
| **266** | Min Cost to Connect All Points | 🟡 Medium | Prim's Algorithm with Min-Heap | Amazon, Google | [LC min-cost-to-connect-all-points](https://leetcode.com/problems/min-cost-to-connect-all-points/) |
| **267** | Critical Connections in a Network (Bridges) | 🔴 Hard | Tarjan's Algorithm with Discovery/Low | Amazon, Google | [LC critical-connections-in-a-network](https://leetcode.com/problems/critical-connections-in-a-network/) |
| **268** | Articulation Point in Graph | 🔴 Hard | Tarjan's Discovery & Low Time DFS | Amazon | Solve [Articulation Point in Graph](https://www.geeksforgeeks.org/problems/articulation-point-1/1) |
| **269** | Strongly Connected Components (Kosaraju) | 🔴 Hard | 2-Pass DFS on Reversed Graph | Amazon, Google | Solve [Strongly Connected Components (Kosaraju)](https://www.geeksforgeeks.org/problems/strongly-connected-components-kosarajus-algo/1) |
| **270** | Reconstruct Itinerary | 🔴 Hard | Hierholzer's Eulerian Path Algorithm | Google, Uber | [LC reconstruct-itinerary](https://leetcode.com/problems/reconstruct-itinerary/) |

### 📌 মডিউল ১২: ডায়নামিক প্রোগ্রামিং (1D, 2D Grid, Knapsack, LCS, LIS, MCM) (40 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **271** | Climbing Stairs | 🟢 Easy | 1D State Transition (Fibonacci) | Google, Apple | [LC climbing-stairs](https://leetcode.com/problems/climbing-stairs/) |
| **272** | Min Cost Climbing Stairs | 🟢 Easy | 1D Memoization / Tabulation | Amazon | [LC min-cost-climbing-stairs](https://leetcode.com/problems/min-cost-climbing-stairs/) |
| **273** | House Robber | 🟡 Medium | Max(Rob i + Skip i-1) | Google, Meta, Amazon | [LC house-robber](https://leetcode.com/problems/house-robber/) |
| **274** | House Robber II | 🟡 Medium | Circular Array (0..N-2 vs 1..N-1) | Google, Amazon | [LC house-robber-ii](https://leetcode.com/problems/house-robber-ii/) |
| **275** | Frog Jump with K Distance | 🟡 Medium | 1D DP Across K Jump Choices | Google | Solve [Frog Jump with K Distance](https://www.geeksforgeeks.org/problems/minimal-cost/1) |
| **276** | Unique Paths | 🟡 Medium | 2D Grid DP Combinatorics | Google, Meta, Amazon | [LC unique-paths](https://leetcode.com/problems/unique-paths/) |
| **277** | Unique Paths II (With Obstacles) | 🟡 Medium | Grid DP with Zeroed Obstacles | Amazon, Google | [LC unique-paths-ii](https://leetcode.com/problems/unique-paths-ii/) |
| **278** | Minimum Path Sum | 🟡 Medium | Grid DP Min Cost Path | Google, Amazon | [LC minimum-path-sum](https://leetcode.com/problems/minimum-path-sum/) |
| **279** | Triangle | 🟡 Medium | Bottom-up In-place Triangle DP | Apple, Amazon | [LC triangle](https://leetcode.com/problems/triangle/) |
| **280** | Minimum Falling Path Sum | 🟡 Medium | Row-by-Row 3-Way Choice DP | Google | [LC minimum-falling-path-sum](https://leetcode.com/problems/minimum-falling-path-sum/) |
| **281** | Cherry Pickup II | 🔴 Hard | 3D State DP dp(r, c1, c2) | Google | [LC cherry-pickup-ii](https://leetcode.com/problems/cherry-pickup-ii/) |
| **282** | Partition Equal Subset Sum | 🟡 Medium | 0/1 Knapsack Target Sum/2 | Amazon, Meta | [LC partition-equal-subset-sum](https://leetcode.com/problems/partition-equal-subset-sum/) |
| **283** | Subset Sum Problem | 🟡 Medium | Boolean Knapsack Tabulation | Amazon, Microsoft | Solve [Subset Sum Problem](https://www.geeksforgeeks.org/problems/subset-sum-problem-1611555638/1) |
| **284** | Target Sum | 🟡 Medium | Subset Sum Transformation (S+sum)/2 | Meta, Google | [LC target-sum](https://leetcode.com/problems/target-sum/) |
| **285** | Coin Change | 🟡 Medium | Unbounded Knapsack Min Coins | Amazon, Meta, Google | [LC coin-change](https://leetcode.com/problems/coin-change/) |
| **286** | Coin Change II | 🟡 Medium | Unbounded Knapsack Combinations | Amazon, Google | [LC coin-change-ii](https://leetcode.com/problems/coin-change-ii/) |
| **287** | 0/1 Knapsack Problem | 🟡 Medium | Standard Knapsack DP | Amazon, Microsoft | Solve [0/1 Knapsack Problem](https://www.geeksforgeeks.org/problems/0-1-knapsack-problem0945/1) |
| **288** | Unbounded Knapsack | 🟡 Medium | Repetition Allowed Knapsack | Amazon | Solve [Unbounded Knapsack](https://www.geeksforgeeks.org/problems/knapsack-with-duplicate-items4201/1) |
| **289** | Rod Cutting Problem | 🟡 Medium | Unbounded Knapsack Max Profit | Amazon, Google | Solve [Rod Cutting Problem](https://www.geeksforgeeks.org/problems/rod-cutting0840/1) |
| **290** | Longest Common Subsequence (LCS) | 🟡 Medium | 2D Grid Character Match DP | Amazon, Microsoft, Meta | [LC longest-common-subsequence](https://leetcode.com/problems/longest-common-subsequence/) |
| **291** | Longest Common Substring | 🟡 Medium | Reset on Mismatch 2D DP | Amazon | Solve [Longest Common Substring](https://www.geeksforgeeks.org/problems/longest-common-substring1449/1) |
| **292** | Longest Palindromic Subsequence | 🟡 Medium | LCS of String and Reversed String | Amazon, Google | [LC longest-palindromic-subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/) |
| **293** | Minimum Insertions for Palindrome | 🟡 Medium | Length - LPS | Amazon, Google | [LC minimum-insertion-steps-to-make-a-string-palindrome](https://leetcode.com/problems/minimum-insertion-steps-to-make-a-string-palindrome/) |
| **294** | Shortest Common Supersequence | 🔴 Hard | Backtracking on LCS Matrix | Google | [LC shortest-common-supersequence](https://leetcode.com/problems/shortest-common-supersequence/) |
| **295** | Edit Distance | 🔴 Hard | Insert, Delete, Replace DP Matrix | Google, Amazon, Meta | [LC edit-distance](https://leetcode.com/problems/edit-distance/) |
| **296** | Distinct Subsequences | 🔴 Hard | Matching Characters Counting DP | Google | [LC distinct-subsequences](https://leetcode.com/problems/distinct-subsequences/) |
| **297** | Wildcard Matching | 🔴 Hard | '*' and '?' State Machine DP | Google, Meta | [LC wildcard-matching](https://leetcode.com/problems/wildcard-matching/) |
| **298** | Regular Expression Matching | 🔴 Hard | '.' and '*' Kleene Star DP | Google, Meta, Microsoft | [LC regular-expression-matching](https://leetcode.com/problems/regular-expression-matching/) |
| **299** | Best Time to Buy/Sell Stock with Cooldown | 🟡 Medium | State Machine DP (Buy, Sell, Rest) | Google, Amazon | [LC best-time-to-buy-and-sell-stock-with-cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/) |
| **300** | Stock with Transaction Fee | 🟡 Medium | In-flight Fee Deduction State DP | Meta, Google | [LC best-time-to-buy-and-sell-stock-with-transaction-fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/) |
| **301** | Best Time to Buy/Sell Stock III (2 Trades) | 🔴 Hard | 4-Variable State Tracking DP | Amazon, Google | [LC best-time-to-buy-and-sell-stock-iii](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/) |
| **302** | Best Time to Buy/Sell Stock IV (K Trades) | 🔴 Hard | 3D DP / Transaction Parity | Amazon, Google | [LC best-time-to-buy-and-sell-stock-iv](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/) |
| **303** | Longest Increasing Subsequence (LIS) | 🟡 Medium | Patience Sort + Binary Search O(NlogN) | Google, Microsoft, Amazon | [LC longest-increasing-subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) |
| **304** | Number of Longest Increasing Subsequences | 🟡 Medium | Length & Count Parallel Arrays | Google | [LC number-of-longest-increasing-subsequence](https://leetcode.com/problems/number-of-longest-increasing-subsequence/) |
| **305** | Largest Divisible Subset | 🟡 Medium | LIS with Divisibility Condition | Google | [LC largest-divisible-subset](https://leetcode.com/problems/largest-divisible-subset/) |
| **306** | Longest String Chain | 🟡 Medium | Predecessor Hash Map DP | Google | [LC longest-string-chain](https://leetcode.com/problems/longest-string-chain/) |
| **307** | Matrix Chain Multiplication (MCM) | 🔴 Hard | Partition DP [i..k] + [k+1..j] | Amazon, Microsoft | Solve [Matrix Chain Multiplication (MCM)](https://www.geeksforgeeks.org/problems/matrix-chain-multiplication0303/1) |
| **308** | Burst Balloons | 🔴 Hard | Inverted Elimination Partition DP | Google, Amazon | [LC burst-balloons](https://leetcode.com/problems/burst-balloons/) |
| **309** | Palindrome Partitioning II | 🔴 Hard | Min Cuts DP + Precomputed Palindromes | Google | [LC palindrome-partitioning-ii](https://leetcode.com/problems/palindrome-partitioning-ii/) |
| **310** | Maximum Profit in Job Scheduling | 🔴 Hard | Interval Sort + Binary Search DP | Google, Meta | [LC maximum-profit-in-job-scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/) |

### 📌 মডিউল ১৩: ট্রাই ও বিট ম্যানিপুলেশন (Tries & Bit Manipulation) (15 Problems)

| # | Problem Name | Difficulty | Key Pattern / Technique | Top MNC Tags | LeetCode Link |
| :-: | :--- | :---: | :--- | :--- | :---: |
| **311** | Implement Trie (Prefix Tree) | 🟡 Medium | Array/Dict of Child Nodes + End Flag | Google, Amazon, Meta | [LC implement-trie-prefix-tree](https://leetcode.com/problems/implement-trie-prefix-tree/) |
| **312** | Design Add and Search Words Data Structure | 🟡 Medium | Wildcard '.' DFS in Trie | Meta, Amazon | [LC design-add-and-search-words-data-structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/) |
| **313** | Maximum XOR of Two Numbers in an Array | 🟡 Medium | 32-Bit Binary Trie Bit-Flipping | Google | [LC maximum-xor-of-two-numbers-in-an-array](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/) |
| **314** | Maximum XOR With an Element From Array | 🔴 Hard | Offline Queries + Sorted Trie Insert | Google | [LC maximum-xor-with-an-element-from-array](https://leetcode.com/problems/maximum-xor-with-an-element-from-array/) |
| **315** | Word Search II | 🔴 Hard | 2D Grid DFS on Prefix Trie | Google, Amazon, Microsoft | [LC word-search-ii](https://leetcode.com/problems/word-search-ii/) |
| **316** | Single Number | 🟢 Easy | Bitwise XOR Cancellation | Amazon, Apple | [LC single-number](https://leetcode.com/problems/single-number/) |
| **317** | Single Number II | 🟡 Medium | 32-Bit Modulo 3 / Logic Gates | Google, Amazon | [LC single-number-ii](https://leetcode.com/problems/single-number-ii/) |
| **318** | Single Number III | 🟡 Medium | XOR Parity Split via Lowest Set Bit | Google | [LC single-number-iii](https://leetcode.com/problems/single-number-iii/) |
| **319** | Number of 1 Bits | 🟢 Easy | Brian Kernighan's Algorithm n &= (n-1) | Apple, Microsoft | [LC number-of-1-bits](https://leetcode.com/problems/number-of-1-bits/) |
| **320** | Counting Bits | 🟢 Easy | Bit Shift DP: dp[i] = dp[i>>1] + (i&1) | Google, Meta | [LC counting-bits](https://leetcode.com/problems/counting-bits/) |
| **321** | Reverse Bits | 🟢 Easy | Bit Shift Masking | Apple, Microsoft | [LC reverse-bits](https://leetcode.com/problems/reverse-bits/) |
| **322** | Missing Number | 🟢 Easy | XOR Sum of Indices vs Values | Amazon, Microsoft | [LC missing-number](https://leetcode.com/problems/missing-number/) |
| **323** | Subsets using Bitmask | 🟡 Medium | Iterating 0 to 2^N - 1 | Meta, Google | [LC subsets](https://leetcode.com/problems/subsets/) |
| **324** | Divide Two Integers | 🟡 Medium | Bit Shift Doubling Subtraction | Meta | [LC divide-two-integers](https://leetcode.com/problems/divide-two-integers/) |
| **325** | Bitwise AND of Numbers Range | 🟡 Medium | Common Bit Prefix via m & (m-1) | Google | [LC bitwise-and-of-numbers-range](https://leetcode.com/problems/bitwise-and-of-numbers-range/) |

## 🧠 প্যাটার্ন রিকগনিশন ডিসিশন ম্যাট্রিক্স (Interview Pattern Recognition Cheat Sheet)

হোয়াইটবোর্ডে প্রশ্ন দেখার প্রথম ৩০ সেকেন্ডে কীভাবে প্যাটার্ন চিনবেন:

```
                                  [ HOW TO CHOOSE THE RIGHT PATTERN ]
                                                   |
      +--------------------------------------------+--------------------------------------------+
      |                                            |                                            |
[ Input is Sorted Array ]                  [ Subarray / Substring Problem ]            [ Permutations / Combinations ]
  -> Two Pointers (O(N))                     -> Fixed Size -> Sliding Window             -> Backtracking Tree (2^N or N!)
  -> Binary Search (O(log N))                -> Variable Size -> Sliding Window + Hash Map
      |                                            |                                            |
      +--------------------------------------------+--------------------------------------------+
      |                                            |                                            |
[ Top / Smallest / Largest K ]             [ Directed Dependencies ]                   [ Min / Max Cost Optimization ]
  -> Heap / Priority Queue (O(N log K))      -> Topological Sort (Kahn's BFS)            -> Dynamic Programming (Overlapping)
  -> Quickselect (O(N) avg)                  -> Cycle Detection (DFS)                    -> Greedy (Local optimal holds)
      |                                            |                                            |
      +--------------------------------------------+--------------------------------------------+
      |                                            |                                            |
[ Next Greater / Smaller Element ]         [ Connected Components / Islands ]          [ String Prefix Search ]
  -> Monotonic Stack (O(N))                  -> BFS / DFS Grid traversal                 -> Trie (Prefix Tree, O(L))
                                             -> Disjoint Set Union (Union-Find)
```

---

## 🏆 MNC ইন্টারভিউ চেকলিস্ট ও ফাইনাল টিপস

1. **Space-Time Trade-off স্পষ্ট বলুন:** হ্যাশ ম্যাপ বা মেমোইজেশন ব্যবহার করলে মেমোরি খরচ বাড়ে কিন্তু টাইম বাঁচে—এটি ইন্টারভিউয়ারের সামনে স্পষ্ট ভাষায় বলুন।
2. **Global Variable পরিহার করুন:** ফাংশনের ভেতর মডুলার প্যারামিটার পাস করুন।
3. **প্রোডাকশন-গ্রেড পাইথন লিখুন:** `for i in range(len(arr))` এর চেয়ে `for idx, val in enumerate(arr)` ব্যবহার করুন।
4. **টেস্ট কেস নিজের উদ্যোগে দিন:** কোড শেষ করার সাথে সাথে স্বাভাবিক কেস, ফাঁকা ইনপুট (`None`, `[]`), এবং সীমার চরম কেস দিয়ে কোড যাচাই করুন।
