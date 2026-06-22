# Greedy

## Most Common Greedy Problem Types (Interviews)

Greedy usually appears in 5 major categories:

---

## 1️⃣ Interval / Scheduling Problems

Examples:

* Activity Selection
* Non-overlapping Intervals
* Meeting Rooms
* Job Sequencing

### 🔥 Approach Pattern:

1. Sort (mostly by end time)
2. Pick first valid
3. Skip overlapping
4. Repeat

Core rule:

> Earliest finish = maximum future space

---

## 2️⃣ Resource Allocation Problems

Examples:

* Assign Cookies
* Fractional Knapsack
* Gas Station
* Job Scheduling with profit

### 🔥 Approach Pattern:

1. Sort by some priority:

   * Profit
   * Size
   * Ratio
2. Always pick best available resource

Core rule:

> Use most valuable resource first

---

## 3️⃣ Reachability / Range Expansion

Examples:

* Jump Game
* Jump Game 2
* Minimum Arrows to Burst Balloons

### 🔥 Approach Pattern:

Maintain:

* Current range
* Maximum reachable range

Core rule:

> Always expand reachable boundary

---

## 4️⃣ Local Profit Accumulation

Examples:

* Best Time to Buy/Sell Stock
* Candy Distribution

### 🔥 Approach Pattern:

Keep track of:

* Minimum so far
* Maximum difference

Core rule:

> Lock best local gain

---

## 5️⃣ Canonical Coin-Type Problems

Examples:

* Real currency coin change
* Some digit manipulation problems

⚠️ Dangerous category — greedy sometimes fails.

---

# 🎯 How to Approach Any New Greedy Problem

When you see a problem:

Step 1:
Ask → “Is this selection / scheduling / max-min type?”

Step 2:
Ask → “Can sorting simplify it?”

Step 3:
Try to invent a RULE.

Example rules:

* Pick earliest finish
* Pick highest profit
* Pick maximum reach
* Pick smallest cost

Step 4:
Mentally test:

> Can smaller elements combine to beat my rule?

If no → greedy likely correct.

---

# 🧠 Real Interview Trick

Most greedy problems:

* Don’t require recursion
* Don’t require DP table
* Usually O(n log n) (because sorting)

## Coin Problem:
Coins = **1, 3, 4**
Target = **6**

Simple question:

👉 Agar tum greedy ho, to tum pehla coin kaunsa loge?

Bas ek hi jawab do:
1, 3 ya 4?

Good 👍
Tumne **4** choose kiya — kyunki greedy usually **sabse bada ya best option abhi** choose karta hai.

Ab next step.

Target tha 6.
Tumne 4 le liya.

👉 Ab kitna amount baaki bacha?

Correct 👍 6 - 4 = 2

Ab coins available same hain: 1, 3, 4

👉 2 banane ke liye greedy kya karega?

(Socho: sabse bada coin jo 2 se chhota ya equal ho.)

Correct 👍

2 ke liye greedy 1 lega.
Phir kitna bacha?

👉 2 - 1 = ?

Yes 👍 1 bacha.

Ab greedy fir kya lega?

👉 1 hi lega na?

To total coins hue:

4 + 1 + 1
= 3 coins

---

Ab ek simple sa question:

Kya 6 ko 2 coins me bana sakte hain using {1, 3, 4}?

Thoda soch ke batao.
Koi combination dikh raha?

Exactly 🔥 3 + 3 = 6 (2 coins)

Ab rukte hain. Yahin learning hai.

Tumne khud dekha:

Greedy:
4 → 1 → 1
= 3 coins

Better solution:
3 → 3
= 2 coins

---

## 🧠 Ab main tumse real question poochta hoon

Greedy ne galti kahan ki?

Kya problem yeh thi ki:

* 4 lena wrong tha?
* Ya future combination miss ho gaya?

Ek line me batao tumhe kya lagta hai.

💯 YES Danish.

Exactly yahi greedy ka core samajh hai.

4 lena “galat” nahi tha.
Galti ye thi ki:

> 4 lene se future ka better combination (3 + 3) block ho gaya.

---

Ab dhyaan se sun — yahi difference hai:

### ❌ Greedy Fail Condition

Agar **future combination better ho sakta hai**, aur tumhara current choice usse block kar deta hai → greedy unsafe.

---

Ab hum opposite case dekhte hain — jahan greedy SAFE hota hai.

### 💰 Coins = 1, 2, 5

Target = 11

Greedy kya karega?

👉 Pehla coin kaunsa lega?

(Socho. Sirf first step bolo.)

Good 👍 5 loge.

11 − 5 = ?

Correct 👍 6 bacha.

Ab greedy kya karega?

👉 6 ke liye sabse bada coin ≤ 6 ?

Nice 👍

6 − 5 = 1

Ab 1 bacha → 1 le liya.

Total coins:

5 + 5 + 1
= 3 coins

---

Ab important thinking step 🔥

Kya 11 ko 3 se kam coins me bana sakte ho using {1,2,5}?

Thoda honestly socho.

2 coins me possible hai?
Try combinations mentally.

Batao.

Perfect 👏

Aur yahi turning point hai.

Pehle example me:
Greedy ne future better combo miss kar diya (3+3).

Is example me:
Greedy ne jo liya (5,5,1) — usse better combination exist hi nahi karta.

Isliye greedy SAFE tha.

---

# 🧠 Ab tumhari original problem ka clear answer

Tum pooch rahe the:

> Kaise pata chale future punish karega ya nahi?

Answer:

Tum mentally check karte ho:

👉 “Kya mera current choice kisi better future combination ko block kar raha hai?”

Agar:

* Block kar raha hai → greedy risky
* Block nahi kar raha → greedy safe

---

# 🎯 Greedy ka real root

Greedy ka matlab:

> Har step pe ek rule bana lo
> Aur assume karo woh rule globally correct hai

Example rules:

* “Always take largest coin”
* “Always take earliest finishing activity”
* “Always take smallest cost”
* “Always keep minimum so far”

Greedy = Rule-based decision making.

DP = Explore all combinations.

---

# ⚡ Ab main tumse ek small conceptual test puchta hoon

Agar coins hote:

1, 5, 10, 20, 50, 100  (Indian currency type)

Target = 70

Kya greedy safe hai yahan?

Soch ke batao — yes or no?
Aur kyun?

(Answer short hi dena.)

50, 20

🔥 YES Danish.

Aur tumne reason bhi indirectly de diya.

70 → 50 + 20
= 2 coins

Aur isse better combination exist hi nahi karta.

---

## 🧠 Ab rukte hain. Pattern dekhte hain.

Pehla example (1,3,4) → greedy fail
Dusra example (1,2,5) → greedy safe
Indian coins → greedy safe

Kya difference notice kar rahe ho?

Thoda observe karo:

* 1,3,4 me issue tha
* 1,2,5 me nahi
* 1,5,10,20… me nahi

👉 Tumhe kya common pattern lag raha hai?

Hint: Coin values ka relation dekho.

Bas thinking batao. No pressure.

Good thinking 👏
You’re trying to find a pattern — that’s exactly how DSA grow hoti hai.

But “gap zyada hai” is not the real reason.

Let’s slow down and refine your observation.

---

Compare carefully:

### ❌ Case 1: 1, 3, 4

4 is NOT a multiple of 3
3 is NOT a multiple of 1 in structured way
Structure irregular hai

### ✅ Case 2: 1, 2, 5

5 = 2×2 + 1
2 = 2×1

### ✅ Indian coins:

10 = 2×5
20 = 2×10
50 ≈ multiple structure
100 = 2×50

Notice something?

👉 In safe cases, coin system structured hai.
Chhote coins milke bade coin ko replace nahi kar paate efficiently.

But in 1,3,4 case:

4 ko replace kar sakte ho 3+1 se
Aur 6 ko better bana sakte ho 3+3 se

Yani:

> Chhote coins milke greedy choice ko beat kar sakte hain.

That’s why greedy fail.

---

## 🎯 Real Rule (Interview Level Understanding)

Greedy works in coin change when:

> Coin system is canonical (structured like real currency).

Greedy fails when:

> Smaller coins combine to create better solution than greedy pick.

---

Now I want YOU to think.

Coins: 1, 4, 5
Target = 8

Greedy kya karega?
Aur kya better solution exist karega?

Step by step socho.
First greedy kya lega?

🔥 BOOM. Perfect.

Greedy:
8 → 5 → 1 → 1 → 1
= 4 coins

Better:
4 + 4
= 2 coins

So greedy failed again.

---

Now pause.

You just did real greedy analysis yourself.

No formula.
No theory.
Just thinking.

That means interest wapas aa raha hai 😄

---

# 🧠 Now Let’s Lock the Concept

Greedy fails when:

> Smaller elements can combine to beat your current “best looking” choice.

Greedy works when:

> Smaller elements cannot efficiently replace the larger one.

---

But coin change is only ONE type of greedy.

Important point:

Most interview greedy problems are NOT coin-change type.

They are usually:

* Interval problems
* Scheduling problems
* Stock problems
* Jump problems
* Gas station
* Activity selection

---

Let’s switch domain.

Forget coins.

# Meeting:

You have meetings:

(1,2), (2,3), (3,4), (1,3)

Goal: attend maximum meetings (no overlap).

Question:

If I tell you:
“Always pick meeting that finishes earliest”

Intuitively — does that feel safe or risky?

Think logically.
If meeting jaldi khatam hoti hai — kya fayda hota hai?

Good 👏
Bahut basic doubt hai — clear karte hain.

Interval (1,2) ka matlab hota hai:

Start = 1
End = 2

Matlab meeting time 1 se start hoti hai aur 2 pe finish hoti hai.

---

Ab “gap between start and end” ka matlab hota hai:

Duration.

Formula:

duration = end - start

Example:

(1,2) → duration = 1
(1,3) → duration = 2

Simple.

---

But IMPORTANT ⚠️

Greedy interval problems me duration important nahi hota.

End time important hota hai.

Kyuki agar meeting jaldi khatam hogi → future me zyada meetings fit ho sakti hain.

---

Chalo small thinking test karte hain.

Meetings:

(1,2)
(2,3)
(3,4)
(1,3)

Agar tum pehle (1,3) choose karte ho:

To kaun-kaun si meetings lose ho jayengi?

Socho aur batao.

YESSS 🔥

Exactly.

Jaldi khatam wali meeting → future ke liye zyada space deti hai.

Aur yahi greedy ka **real working intuition** hai in interval problems.

---

# 🧠 Ab dhyaan se sun — yahi greedy ka safe case hai

Hum rule banate hain:

> “Always pick the meeting that finishes earliest.”

Kyun?

Because:

* Jaldi finish → maximum remaining timeline free
* Zyada timeline free → zyada meetings fit ho sakti hain
* Aur koi chhoti meeting milke is decision ko beat nahi kar sakti

Yahan future punish nahi karta.

---

# 🎯 Ab tum samjho difference

Coin problem me:
Future combination greedy ko beat kar sakta tha.

Meeting problem me:
Future combination greedy ko beat nahi kar sakta.

Kyuki jaldi finish karna logically always better hai.

---

# ⚡ Ab main tumse ek important question poochta hoon

Agar meetings hote:

(1,4)
(2,3)
(3,5)

Agar hum “earliest start time” ke basis pe choose karein
to pehle kaunsa lenge?

Aur kya woh best hoga?

Socho step by step.
Bas first pick batao.

🔥 EXACTLY Danish.

Tumne khud greedy ka trap pakad liya.

Rule agar banaya:

> “Earliest start pick karo”

To first pick hoga (1,4)

Phir:

* (2,3) ❌ overlap
* (3,5) ❌ overlap

Total = 1 meeting

But better kya tha?

(2,3) → (3,5)
= 2 meetings

---

## 🧠 Ab FINAL clarity point

Greedy works when:

👉 Tumhara rule future ko maximum freedom deta hai.

Earliest start:
❌ future ko block karta hai

Earliest finish:
✅ future ko open karta hai

---

# 🎯 Ab tumhara original sawaal ka FINAL answer

Greedy based question kaise identify kare?

Ask:

1. Kya problem selection/scheduling type hai?
2. Kya main ek logical rule bana sakta hoon?
3. Kya mera rule future options ko expand kar raha hai ya shrink?

Agar expand kar raha hai → strong greedy candidate.

---

Ab main tumse ek reflection question poochta hoon:

Ab tak jo humne coins aur meetings me dekha —

Tum ek line me batao:

Greedy ka real goal kya hota hai?

Main answer nahi dunga.
Tum bolo. 💪

🔥 Danish… THIS is the sentence.

> “Jo selection le rahe hain usse future block nahi hona chahiye.”

Yehi greedy ka practical interview-level intuition hai.

Very good. Now we polish it slightly 👇

---

## 🧠 Refined Version (Interview Ready)

Greedy ka goal hota hai:

> Aisa local decision lena jo future options ko reduce na kare, balki maximize kare.

---

### Coins Example

4 lene se future block ho gaya ❌
3 lene se future better bana ✅

---

### Meeting Example

Early start lene se future block ho gaya ❌
Early finish lene se future open ho gaya ✅

---

Now I want to strengthen your brain one more level.

Answer honestly:

Greedy ka decision generally kya karta hai?

A) Problem ko chhota karta hai
B) Future ko zyada flexible banata hai
C) Dono

Soch ke answer do. No guessing.

Close 👌 but think one level deeper.

When we pick an element in greedy:

* Kya hum future ko flexible bana rahe hote hain? → Yes (meeting example)
* Kya problem ka size bhi kam ho raha hota hai? → Yes (remaining part solve karna hota hai)

Example:
Meeting choose ki → timeline reduce ho gayi
Coin choose ki → remaining target kam ho gaya

So actually answer is:

👉 **C) Dono**

Greedy does two things simultaneously:

1. Problem ko chhota karta hai
2. Future ko maximum flexible banane ki koshish karta hai

---

Ab tum ready ho next clarity ke liye.

### Final Deep Check 🔥

Jump Game type problem:

Array: [2,3,1,1,4]

Har number batata hai max jump length.

Greedy approach hota hai:

> Har step pe maximum reachable index track karo.

Question:

Yeh future ko block karta hai ya expand?

Socho.
Jump zyada reach karna better hai ya chhota jump lena?

Perfect 👏
Yahi sahi jagah pe doubt aaya hai. Chalo bilkul clear karte hain.

---

# 🎯 Jump Game – Actual Problem

Array:
`[2,3,1,1,4]`

Rule:

* Tum index 0 se start karte ho.
* Har element batata hai: **maximum kitne steps jump kar sakte ho.**
* Question:

> Kya tum last index tak pahunch sakte ho?

Bas itna hi problem hai.

---

Example samjho:

Index 0 → value = 2
Matlab tum jump kar sakte ho:

* 1 step (index 1)
* ya 2 step (index 2)

---

Ab main tumse poochta hoon:

Agar tum index 0 pe ho (value = 2),
to tum possible kaun-kaun se index pe ja sakte ho?

Bas options batao.

😂 Theek hai Danish, is baar main explain karta hoon — but dhyaan se read karna.

---

## 🔥 Jump Game – Clear Explanation

Array:
[2,3,1,1,4]

Meaning:

Index 0 → 2 steps max
Index 1 → 3 steps max
Index 2 → 1 step
Index 3 → 1 step
Index 4 → 4 step (last index)

---

### Step 1: Start at index 0

Value = 2
You can go to:

* Index 1
* Index 2

---

### Step 2: Which is better?

Index 1 → value = 3
Index 2 → value = 1

If you go to index 2:
You can only move 1 step → index 3

If you go to index 1:
You can move up to 3 steps:

* index 2
* index 3
* index 4 (last index 🔥)

So obviously index 1 gives more future reach.

---

# 🧠 Greedy Thinking Here

Greedy logic:

> Always keep track of maximum reachable index.

At index 0:
Max reach = 0 + 2 = 2

Move forward and update:

At index 1:
Max reach = max(2, 1 + 3) = 4

Boom 💥
Now you already know you can reach last index.

No need to try all paths.

---

# 🎯 Why Greedy Works Here?

Because:

* Taking longer reach never blocks future
* More reach = more flexibility
* No smaller jump combination can beat larger reach

Future expand ho raha hai, block nahi.

---

# ⚡ Now final clarity question

In Jump Game, greedy ka goal kya hai?

A) Largest value choose karna
B) Maximum future reach maintain karna
C) Minimum jumps lena

Soch ke answer do.



Perfect 👌
Aaj ka session actually bahut important tha. Main crisp, note-friendly summary de raha hoon — tum directly copy kar sakte ho.

---

# 📘 DSA Session Summary – Greedy Understanding (Without Labels)

## 1️⃣ Biggest Realization

* Technique ka naam problem solve nahi karta.
* Pehle logic se solve karo.
* Baad me pattern ka naam lagta hai (Greedy, DP, Two Pointer).

> Solve first → Label later.

---

## 2️⃣ What “Greedy” Actually Means (Simple Version)

Greedy =
Har step pe best local decision lena
without exploring all possibilities.

Real meaning:

> Aisa decision lo jo future ko block na kare.

---

## 3️⃣ When Greedy Fails (Coin Example Insight)

Coins: 1, 3, 4
Target: 6

Greedy: 4 + 1 + 1 = 3 coins
Better: 3 + 3 = 2 coins

Lesson:

> Agar smaller elements combine karke better solution bana sakte hain,
> to greedy unsafe ho sakta hai.

---

## 4️⃣ When Greedy Works (Meeting Example Insight)

Rule:
“Pick earliest finishing meeting.”

Why it works:

* Jaldi finish → future ke liye zyada space.
* Decision future expand karta hai.

Core idea:

> Good greedy rule future flexibility increase karta hai.

---

## 5️⃣ Stock Problem Insight (Very Important)

Single transaction:

Logic:

* Minimum price track karo.
* Har step pe profit calculate karo.
* Max profit update karo.

Code pattern:

```java
int minPrice = Integer.MAX_VALUE;
int maxProfit = 0;

for (int price : prices) {
    if (price < minPrice) {
        minPrice = price;
    } else {
        int profit = price - minPrice;
        maxProfit = Math.max(maxProfit, profit);
    }
}
```

Time Complexity: O(n)
Space Complexity: O(1)

Key Insight:

> Ye naturally socha ja sakta hai.
> Isko baad me “Greedy” bolte hain.

---

## 6️⃣ Two Pointer vs Greedy Difference

Greedy:

* Permanent selection
* Rule-based picking
* Future expand karna goal

Two Pointer:

* Candidates eliminate karna
* Search space shrink karna
* Example: Container With Most Water

---

## 7️⃣ Mental Shift (Most Important)

❌ “Ye question greedy hai kya?”
✅ “Main is problem ko logically kaise solve karun?”

Technique ka naam sirf classification hai.

---

## 8️⃣ Your Personal Breakthrough Today

* Tumne stock problem khud solve kiya.
* Tumne code khud likha.
* Tumne complexity sahi batayi.
* Problem manageable laga.

Conclusion:

> DSA tumse ho sakta hai.
> Tum technique ke naam se intimidate hote ho, logic se nahi.

---

Aaj ka session actually productive tha.

Next time:
Hum ek naya problem logic se solve karenge.
No labels.
No pressure.

Take a break now.

Jab ready ho, we continue. 💪

# Interval / Scheduling Problems
Perfect 💪
Let’s complete **Interval / Scheduling category** properly — structured, clear, no confusion.

We will cover:

1️⃣ Merge Intervals
2️⃣ Insert Interval
3️⃣ Non-Overlapping Intervals
4️⃣ Meeting Rooms / Max Meetings
5️⃣ Minimum Arrows to Burst Balloons

All under one mental model.

---

# 🧠 Step 1: Big Picture – Interval Problems Kitne Types Ke Hote Hain?

There are ONLY 2 fundamental types:

## 🟢 Type A – Structure Problems

Goal:

* Merge
* Insert
* Combine
* Clean timeline

👉 We are NOT selecting.
👉 We are restructuring.

### Rule:

Sort by **START**

---

## 🔴 Type B – Selection Problems

Goal:

* Maximum meetings
* Minimum removals
* Maximum non-overlapping
* Minimum arrows

👉 We ARE selecting.

### Rule:

Sort by **END**

---

Now we go one by one.

---

# 1️⃣ Merge Intervals (Structure Type)

Example:
[[1,3],[2,6],[8,10]]

### Why sort by START?

Think:

If sorted by start:
[1,3], [2,6], [8,10]

Now overlap check easy:
If current.start <= previous.end → merge.

If not sorted, overlap detection messy ho jayega.

---

### Core Logic Pattern

```java
sort by start

for each interval:
    if overlap:
        merge
    else:
        add to result
```

No selection logic.
Just combine.

---

# 2️⃣ Insert Interval (Structure Type)

Already sorted by start.

We just:

* Add all intervals before newInterval
* Merge overlapping ones
* Add remaining

Again:
We maintain structure.

👉 So START sort makes sense.

---

Now important shift.

---

# 3️⃣ Non-Overlapping Intervals (Selection Type)

Goal:
Minimum removals.

Same as:
Maximum intervals we can keep.

Question:
When two overlap,
which one should we keep?

Answer:
The one that ends earlier.

Why?

Because it gives more future space.

So:

👉 Sort by END

Then:

```java
count = 0
prevEnd = first.end

for each interval:
    if start < prevEnd:
        count++   // remove this
    else:
        prevEnd = current.end
```

This is pure selection logic.

---

# 4️⃣ Maximum Meetings (Same as Above)

Exactly same logic.

Sort by end.
Pick earliest finishing.

---

# 5️⃣ Minimum Arrows to Burst Balloons

Looks different.
But actually same selection logic.

If balloons overlap,
one arrow burst them.

So:

Sort by end.
Shoot arrow at earliest finishing balloon.
Skip all overlapping.

Same brain.

---

# 🎯 Final Mental Template (VERY IMPORTANT)

When you see interval problem, ask:

### Q1: Are we merging?

→ Sort by START

### Q2: Are we selecting max/min?

→ Sort by END

---

# Interval Scheduling problem required

Perfect.
Now we go **one level higher**. No specific problem.
We build a **mental model for Interval Greedy problems**.

You asked:

> Is there a common step?
> Any mental model?
> How to proceed?

Yes. There is a very clear 5-step framework.

---

# 🧠 THE INTERVAL GREEDY MENTAL MODEL

Whenever you see interval/scheduling problem:

Stop coding.
Do this thinking sequence.

---

## 🟣 STEP 1 — Identify the Goal Type

Ask:

Are we trying to:

A) Combine intervals?
B) Select maximum non-overlapping?
C) Minimize removals?
D) Count resources (rooms, arrows)?

👉 This decides everything.

---

### If goal = combine / clean structure

→ Structure problem
→ Sort by **start**

### If goal = selection / maximize count / minimize removal

→ Selection problem
→ Sort by **end**

This is the first mental branch.

---

## 🟣 STEP 2 — Ask the Core Greedy Question

In selection problems:

> When two intervals overlap, which one should I keep?

The answer must increase future flexibility.

Almost always:

👉 Keep the one that **ends earlier**

Because:

* Smaller end → more timeline free
* More timeline free → more future intervals fit

This is the greedy rule.

---

## 🟣 STEP 3 — Convert to One Variable Tracking

Most interval greedy problems reduce to tracking one thing:

```
previousEnd
```

That’s it.

You don’t need complex state.

---

## 🟣 STEP 4 — Overlap Condition

The most important line:

```
if current.start < previousEnd → overlap
```

(Or <= depending on problem definition)

Everything flows from this.

---

## 🟣 STEP 5 — Decide Action

If overlap:

* In selection problems → remove one (or skip)
* In arrow problem → same arrow
* In room problem → count new resource
* In merge problem → merge

Same overlap check.
Different action.

---

# 🧠 Deep Insight (Advanced Level)

All interval greedy problems are based on:

> Timeline management.

You are managing a line.

You either:

* Compress it (merge)
* Select segments from it
* Allocate resources over it

But the core logic is always:

1. Sort
2. Compare with previous
3. Update state

---

# 🧩 Let’s Build the Meta-Template

### Structure Type (Merge)

```java
sort by start

for each interval:
    if overlap:
        merge
    else:
        add
```

---

### Selection Type (Max meetings / Min removals)

```java
sort by end

prevEnd = -∞
count = 0

for each interval:
    if start >= prevEnd:
        take it
        prevEnd = end
    else:
        skip it
```

---

# 🧠 Ultra Important Realization

The reason sorting changes:

Because in structure problems
we care about timeline order.

In selection problems
we care about earliest finishing.

Goal decides sorting.

Sorting decides greedy rule.

Greedy rule decides code.

---

# 🎯 Now I test your mental model

If problem says:

“Find minimum number of arrows to burst balloons.”

Balloons overlap in x-axis.

Is this structure problem
or selection problem?

And what will you sort by?

Think calmly. No rush.

Perfect. ✅

Exactly.

Minimum Arrows = **Selection problem**
Sort by **end**
Shoot arrow at **earliest end**

Why?

Because:

* Agar tum earliest ending balloon pe arrow shoot karte ho
* To jitne bhi balloons us point ko cover karte hain, sab burst ho jaate hain
* Aur future ke liye maximum space free ho jata hai

Same brain as:

* Maximum meetings
* Non-overlapping intervals
* Minimum removals

---

# 🧠 Now Let’s Elevate One More Level

Tumne poocha tha:

> Is there a common mental model?

Yes. Ab usko compress karte hain into 3 core rules.

---

# 🎯 The 3 Golden Greedy Rules (Interval Category)

### 1️⃣ Define the Goal Clearly

* Merge? → Structure problem
* Maximize count / Minimize removal? → Selection problem

Goal decides sorting.

---

### 2️⃣ Identify the Conflict

Whenever two intervals overlap:

Ask:

> Which one gives me more future flexibility?

Answer almost always:
→ The one that ends earlier.

---

### 3️⃣ Reduce State to One Variable

Most interval greedy problems reduce to:

```java
previousEnd
```

That’s it.

No DP table.
No complex recursion.

---

# 🧠 Final Clarity Check

Suppose problem:

“Given meetings, find minimum rooms required.”

This is NOT pure selection anymore.

Why?

Because here we are not skipping meetings.
We must attend all.

So question:

Is this:
A) Pure selection greedy
B) Structure greedy
C) Resource allocation variant

Think carefully before answering.

Perfect 👌
Now we’ll formalize the **3 buckets of Interval Greedy Problems** with clear Java code and mental model.

We’ll cover:

1️⃣ Structure (Merge / Insert)
2️⃣ Selection (Max Non-overlap / Min Removal / Arrows)
3️⃣ Resource Allocation (Meeting Rooms)

---

# 🟢 1️⃣ STRUCTURE PROBLEMS

(Merge Intervals)

### 🧠 Mental Model

We are NOT selecting.
We are cleaning timeline structure.

So:
👉 Sort by **start**

---

### ✅ Java Code – Merge Intervals

```java
import java.util.*;

class Solution {
    public int[][] merge(int[][] intervals) {
        Arrays.sort(intervals, (a, b) -> a[0] - b[0]);  // sort by start
        
        List<int[]> result = new ArrayList<>();
        
        for (int[] interval : intervals) {
            if (result.isEmpty() || result.get(result.size() - 1)[1] < interval[0]) {
                result.add(interval);
            } else {
                result.get(result.size() - 1)[1] =
                        Math.max(result.get(result.size() - 1)[1], interval[1]);
            }
        }
        
        return result.toArray(new int[result.size()][]);
    }
}
```

### 🔎 Why start sort?

Because merging depends on comparing current start with previous end.

---

# 🔴 2️⃣ SELECTION PROBLEMS

(Non-overlapping Intervals)

### 🧠 Mental Model

We want maximum intervals.
So when overlap happens:

👉 Keep the one that ends earlier.

So:
👉 Sort by **end**

---

### ✅ Java Code – Non Overlapping Intervals

```java
import java.util.*;

class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        Arrays.sort(intervals, (a, b) -> a[1] - b[1]);  // sort by end
        
        int count = 0;
        int prevEnd = intervals[0][1];
        
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < prevEnd) {
                count++;  // remove this interval
            } else {
                prevEnd = intervals[i][1];
            }
        }
        
        return count;
    }
}
```

### 🔎 Why end sort?

Earlier finish → more future space.

---

# 🔵 3️⃣ RESOURCE ALLOCATION PROBLEMS

(Meeting Rooms II)

### 🧠 Mental Model

We cannot remove meetings.
We must count maximum overlap.

So we track:
👉 How many meetings active at same time.

This is NOT pure selection.
This is overlap counting.

---

### ✅ Java Code – Meeting Rooms II

```java
import java.util.*;

class Solution {
    public int minMeetingRooms(int[][] intervals) {
        Arrays.sort(intervals, (a, b) -> a[0] - b[0]); // sort by start
        
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        
        for (int[] interval : intervals) {
            if (!minHeap.isEmpty() && minHeap.peek() <= interval[0]) {
                minHeap.poll(); // room becomes free
            }
            minHeap.offer(interval[1]); // occupy room
        }
        
        return minHeap.size();
    }
}
```

### 🔎 Why heap?

We track:

* Earliest ending meeting
* If new meeting starts after that → reuse room
* Else → need new room

Heap stores end times.

---

# 🎯 Final Comparison Table

| Category             | Sort By      | Core Idea              |
| -------------------- | ------------ | ---------------------- |
| Merge / Insert       | Start        | Clean structure        |
| Non-overlap / Arrows | End          | Select earliest finish |
| Meeting Rooms        | Start + Heap | Count overlaps         |

---

# 🧠 Ultimate Mental Model

When you see interval problem:

Step 1: Ask goal.

* Combine?
* Select?
* Allocate resources?

Step 2: Sorting rule depends on goal.

Step 3: Reduce problem to:

* previousEnd tracking
* OR heap for overlap count

---

Now let’s test depth.

If problem says:

“Find minimum platforms needed for trains.”

Which bucket?

A) Structure
B) Selection
C) Resource Allocation

Think carefully.

💯 Correct — **C) Resource Allocation**.

Because:

* Sab trains ko handle karna hai (skip nahi kar sakte)
* Agar 2 trains same time pe station pe hain → 2 platforms chahiye
* So we are counting **maximum overlap at any time**

Very good 👍

---

# 🚆 Minimum Platforms – Mental Model

Ye exactly Meeting Rooms II jaisa hai.

Difference sirf story ka hai.

---

## 🧠 Approach 1 (Two Sorted Arrays – Cleanest Thinking)

Instead of heap, ek aur elegant method hota hai:

1. Separate arrival[] and departure[]
2. Dono sort karo
3. Two pointers se overlap count karo

---

### ✅ Java Code – Minimum Platforms (Two Pointer)

```java
import java.util.*;

class Solution {
    public int minPlatforms(int[] arrival, int[] departure) {
        Arrays.sort(arrival);
        Arrays.sort(departure);

        int i = 0, j = 0;
        int platforms = 0, maxPlatforms = 0;

        while (i < arrival.length) {
            if (arrival[i] <= departure[j]) {
                platforms++;
                maxPlatforms = Math.max(maxPlatforms, platforms);
                i++;
            } else {
                platforms--;
                j++;
            }
        }

        return maxPlatforms;
    }
}
```

---

## 🧠 Why This Works?

Think timeline:

* If next arrival happens before earliest departure → new platform needed
* If departure happens first → platform free ho gaya

We are literally tracking active trains.

---

# 🔥 Now Let’s Lock the Interval Category Completely

Interval problems fall into:

### 1️⃣ Structure

Sort by start → merge

### 2️⃣ Selection

Sort by end → keep earliest finish

### 3️⃣ Resource Allocation

Sort by start → track active overlaps (heap or 2 pointers)

---

You now understand:

* Why sorting changes
* Why greedy rule changes
* Why heap needed sometimes
* Why sometimes just previousEnd enough

This is not memorization anymore.
This is structural understanding.

---

Now I’ll ask you something deeper:

If I give you this:

(1,10), (2,3), (4,5), (6,7)

And ask:

“Maximum non-overlapping intervals?”

Without writing code —

Which one will you remove?

Explain your reasoning.

Perfect 💪
Now I’ll give you a **complete Interval Greedy Mental Model summary** — structured, clean, and interview-ready.

This will cover:

* Structure Problems
* Selection Problems
* Resource Allocation Problems
* Sorting logic
* Overlap logic
* Touching rule
* Code templates
* Decision flow

You can directly paste this into your notes.

---

# 📘 INTERVAL GREEDY – COMPLETE MENTAL MODEL

Interval problems look different,
but internally they fall into **3 major buckets**.

---

# 🧠 STEP 1: CLASSIFY THE PROBLEM FIRST

Before coding, ask:

### ❓ What is the goal?

### 🟢 A) Structure Problem

* Merge intervals
* Insert interval
* Combine overlapping intervals

👉 We are NOT skipping intervals.
👉 We are cleaning the timeline.

---

### 🔴 B) Selection Problem

* Maximum non-overlapping intervals
* Minimum removals
* Maximum meetings
* Minimum arrows

👉 We ARE choosing which intervals to keep.
👉 Goal: maximize count or minimize removals.

---

### 🔵 C) Resource Allocation Problem

* Minimum meeting rooms
* Minimum platforms
* Maximum concurrent events
* Peak load

👉 We must keep ALL intervals.
👉 We count maximum overlap.

---

# 🧠 STEP 2: SORTING RULE DEPENDS ON CATEGORY

This is the most important rule.

| Category            | Sort By | Why                                    |
| ------------------- | ------- | -------------------------------------- |
| Structure           | Start   | Timeline order needed for merging      |
| Selection           | End     | Earliest finish gives max future space |
| Resource Allocation | Start   | Process events chronologically         |

---

# 🟢 STRUCTURE PROBLEMS (Merge / Insert)

## 🧠 Mental Model

We want to combine overlapping intervals.

Sort by start.

If current.start <= previous.end → merge.

---

## ✅ Java Template – Merge Intervals

```java id="l0q3az"
Arrays.sort(intervals, (a, b) -> a[0] - b[0]);

List<int[]> result = new ArrayList<>();

for (int[] interval : intervals) {
    if (result.isEmpty() || result.get(result.size()-1)[1] < interval[0]) {
        result.add(interval);
    } else {
        result.get(result.size()-1)[1] =
            Math.max(result.get(result.size()-1)[1], interval[1]);
    }
}

return result.toArray(new int[result.size()][]);
```

---

## 🔎 Key Idea

Structure problems = merge overlaps.

No selection logic.

---

# 🔴 SELECTION PROBLEMS (Max Non-overlapping / Min Removal)

## 🧠 Mental Model

When two intervals overlap:

> Keep the one that ends earlier.

Because:
Earlier finish → more future space → more intervals fit.

---

## ✅ Java Template – Non Overlapping

```java id="v1mtxr"
Arrays.sort(intervals, (a, b) -> a[1] - b[1]);

int count = 0;
int prevEnd = intervals[0][1];

for (int i = 1; i < intervals.length; i++) {
    if (intervals[i][0] < prevEnd) {
        count++;   // remove
    } else {
        prevEnd = intervals[i][1];
    }
}

return count;
```

---

## 🔎 Core Variable

```java id="mgq8k3"
prevEnd
```

Selection greedy usually reduces to tracking previous end.

---

# 🔵 RESOURCE ALLOCATION PROBLEMS (Rooms / Platforms)

## 🧠 Mental Model

We cannot skip intervals.

We track how many intervals overlap at peak time.

Each interval:
+1 at start
-1 at end

Maximum active intervals = answer.

---

## ✅ Java Template – Meeting Rooms (Heap Version)

```java id="1nykpd"
Arrays.sort(intervals, (a, b) -> a[0] - b[0]);

PriorityQueue<Integer> minHeap = new PriorityQueue<>();

for (int[] interval : intervals) {
    if (!minHeap.isEmpty() && minHeap.peek() <= interval[0]) {
        minHeap.poll();   // free room
    }
    minHeap.offer(interval[1]);
}

return minHeap.size();
```

---

## Alternative – Two Pointer Version

```java id="df7v8x"
Arrays.sort(arrival);
Arrays.sort(departure);

int i = 0, j = 0;
int active = 0, maxActive = 0;

while (i < arrival.length) {
    if (arrival[i] < departure[j]) {
        active++;
        maxActive = Math.max(maxActive, active);
        i++;
    } else {
        active--;
        j++;
    }
}

return maxActive;
```

---

# 🧠 TOUCHING RULE (Very Important)

Always read problem carefully.

### Case 1: Touching allowed

(1,3) and (3,5) → NOT overlapping

Use:

```java
arrival < departure
```

---

### Case 2: Touching NOT allowed

(1,3) and (3,5) → overlapping

Use:

```java
arrival <= departure
```

This small operator change matters.

---

# 🧠 OVERLAP CONDITION SUMMARY

General overlap check:

```java
if (current.start < previousEnd)
```

or

```java
if (current.start <= previousEnd)
```

Depends on touching rule.

---

# 🧠 TIME COMPLEXITY

All interval greedy problems:

Sorting → O(n log n)
Traversal → O(n)

Overall → O(n log n)

---

# 🧠 COMPLETE DECISION FLOW (Interview Use)

When you see interval problem:

1️⃣ Can I skip intervals?

* Yes → Selection
* No → Resource Allocation

2️⃣ Is problem about merging?

* Yes → Structure

3️⃣ Apply sorting rule accordingly.

4️⃣ Use minimal state:

* Structure → merge previous
* Selection → prevEnd
* Resource → active count or heap

---

# 🧠 ULTRA-COMPRESSED MEMORY VERSION

Structure → Sort by START → Merge
Selection → Sort by END → Keep earliest finish
Resource → Sort by START → Count overlaps

---

# 🎯 FINAL IMPORTANT INSIGHT

All interval greedy problems are timeline problems.

You are either:

* Cleaning the timeline
* Selecting segments
* Counting overlaps

That’s it.

No mystery beyond this.

---

If you want next level:

We can now mix random interval problems
and you classify first before coding.

That will lock this permanently. 💪

Perfect 👌
Now I’ll give you a **clean, structured, interview-level summary** of:

# 📘 Resource Allocation Greedy (Complete Notes)

This is different from normal greedy (selection/merge).
Understand this deeply — many interview problems come from here.

---

# 🧠 1️⃣ What is Resource Allocation Greedy?

These problems ask:

* Minimum meeting rooms
* Minimum train platforms
* Minimum servers needed
* Maximum concurrent events
* Peak load

Core idea:

> We are NOT skipping intervals.
> We must process ALL intervals.
> We want to know maximum simultaneous overlap.

---

# 🎯 2️⃣ Core Question Behind All These Problems

Reduce problem to:

> At any point in time, how many intervals are active simultaneously?

That maximum number = answer.

This is called:

👉 **Peak Overlap**

---

# 🧠 3️⃣ How To Identify This Category

Ask these questions:

### ❓ Can I skip intervals?

* If YES → Selection problem
* If NO → Resource Allocation

### ❓ Is question asking:

* Minimum rooms?
* Minimum platforms?
* Minimum CPUs?
* Maximum concurrent users?
* Peak traffic?

If YES → Resource Allocation.

---

# 🧠 4️⃣ Mental Model (Very Important)

Each interval does two things:

* At start → needs a resource (+1)
* At end → releases resource (-1)

So think in terms of:

```
+1 at start
-1 at end
```

We track the maximum active count.

---

# 🧠 5️⃣ Two Standard Approaches

---

## 🔹 Approach 1: Min Heap (Most Interview Friendly)

### Why heap?

Because we need to know:

> Which meeting ends earliest?

If earliest ending meeting finishes before current start → reuse resource.

Else → allocate new resource.

---

### ✅ Java Code (Meeting Rooms)

```java
Arrays.sort(intervals, (a, b) -> a[0] - b[0]); // sort by start

PriorityQueue<Integer> minHeap = new PriorityQueue<>();

for (int[] interval : intervals) {
    if (!minHeap.isEmpty() && minHeap.peek() <= interval[0]) {
        minHeap.poll();  // room freed
    }
    minHeap.offer(interval[1]);  // occupy room
}

return minHeap.size();
```

---

### 🔎 Why Sort by Start?

Because we process events in chronological order.

---

## 🔹 Approach 2: Two Pointer (Arrival & Departure Arrays)

Cleaner thinking model.

### Steps:

1. Separate arrival[] and departure[]
2. Sort both arrays
3. Compare using two pointers

---

### ✅ Java Code

```java
Arrays.sort(arrival);
Arrays.sort(departure);

int i = 0, j = 0;
int active = 0, maxActive = 0;

while (i < arrival.length) {
    if (arrival[i] < departure[j]) {  // condition depends on touching rule
        active++;
        maxActive = Math.max(maxActive, active);
        i++;
    } else {
        active--;
        j++;
    }
}

return maxActive;
```

---

# 🧠 6️⃣ Very Important: Touching Rule (Interview Trap)

Always check problem statement:

### Case A: Touching allowed (most common)

(1,3) and (3,5) → NOT overlapping

Use:

```
arrival < departure
```

Because arrival == departure means room is free.

---

### Case B: Touching NOT allowed

(1,3) and (3,5) → overlapping

Use:

```
arrival <= departure
```

Because equal time still needs resource.

---

# 🧠 7️⃣ Selection vs Resource Allocation Difference

| Selection Greedy    | Resource Allocation |
| ------------------- | ------------------- |
| Skip some intervals | Keep all intervals  |
| Avoid overlap       | Count overlap       |
| Track previousEnd   | Track active count  |
| Sort by end         | Sort by start       |

---

# 🧠 8️⃣ Time Complexity

* Sorting → O(n log n)
* Heap operations → O(n log n)
* Two pointer approach → O(n log n) (because sorting)

Space:

* Heap → O(n)
* Two arrays → O(n)

---

# 🧠 9️⃣ Typical Problems in This Category

* Meeting Rooms II
* Minimum Platforms
* Maximum Overlapping Intervals
* Peak Bandwidth
* CPU Scheduling
* Server Load
* Concurrent Users

All reduce to:

> Find maximum overlap.

---

# 🧠 🔥 Ultimate Compression (1 Line Memory)

Resource Allocation Greedy =

> Count maximum simultaneous intervals on timeline.

---

# 🧠 10️⃣ Interview Thinking Flow

When you see interval problem:

1. Can I skip intervals?

   * Yes → Selection
   * No → Resource Allocation

2. Resource Allocation?

   * Sort by start
   * Track active intervals
   * Return maximum active

---

# 🎯 Your Personal Learning Insight

Today you understood:

* Why sorting changes
* Why `<` vs `<=` matters
* Why heap is used
* Why this is different from selection greedy

This is real structural clarity.

---

If you want next session:

We can do:

* 2–3 mixed interval problems
* You classify first
* Then we solve

That will solidify this permanently. 💪

Perfect 💪
Now I’ll give you a **complete, structured, beginner-to-advanced summary** of:

# 📘 Reachability / Range Expansion Greedy

This category includes:

Jump Game I

Jump Game II

Minimum Taps to Water Garden

Minimum Number of Arrows (range covering variant)

Video Stitching

Gas Station (partially similar thinking)

## Jump Game I & Jump Game II

You can directly copy this into your notes.

---

# 🧠 1️⃣ What Type of Greedy Is This?

This category is different from:

* Interval Merge
* Interval Selection
* Resource Allocation

Here we are solving:

> Can we reach the end?
> OR
> What is the minimum number of jumps to reach the end?

Core idea:

👉 We expand a reachable range gradually.

---

# 🎯 2️⃣ Core Mental Model

Each element `nums[i]` means:

> From index i, you can jump at most `nums[i]` steps.

So from index i,
you can reach up to:

```text
i + nums[i]
```

---

# 🧠 KEY CONCEPT: REACHABLE RANGE

Imagine we maintain a boundary:

```text
[0 -------- maxReach]
```

It means:

> So far, we can reach up to maxReach.

We keep expanding this boundary.

That is called:

👉 Range Expansion Greedy.

---

# 🟢 Jump Game I – Can Reach End?

## 🎯 Requirement

Return true if last index reachable.
Return false if stuck.

We DO NOT count jumps.

---

## 🧠 Logic

Maintain:

```java
int maxReach = 0;
```

At every index:

1. If `i > maxReach`
   → we cannot reach this index → return false

2. Otherwise:
   update `maxReach = max(maxReach, i + nums[i])`

---

## ✅ Java Code

```java id="jump1code"
public boolean canJump(int[] nums) {
    int maxReach = 0;

    for (int i = 0; i < nums.length; i++) {
        if (i > maxReach) return false;
        maxReach = Math.max(maxReach, i + nums[i]);
    }

    return true;
}
```

---

## 🧠 Dry Run Example 1

`[2,3,1,1,4]`

Start:

```
maxReach = 0
```

i=0 → maxReach = 2
i=1 → maxReach = 4
i=2 → maxReach = 4
i=3 → maxReach = 4
i=4 → reachable

Return TRUE ✅

---

## 🧠 Dry Run Example 2

`[3,2,1,0,4]`

i=0 → maxReach = 3
i=1 → maxReach = 3
i=2 → maxReach = 3
i=3 → maxReach = 3
i=4 → 4 > 3 → return FALSE ❌

---

## 🧠 Important Insight

We always ensure:

> We never step outside reachable boundary.

---

# 🟡 Jump Game II – Minimum Jumps

## 🎯 Requirement

Return minimum jumps to reach last index.

Now counting matters.

---

## 🧠 Mental Model

Think in layers.

At any time we maintain:

```java
int currentEnd
int farthest
```

Meaning:

* `currentEnd` → max index reachable with current number of jumps
* `farthest` → max index reachable with next jump

---

## 🧠 Logic

Loop from 0 to n-2:

1. Update farthest:

```java
farthest = max(farthest, i + nums[i]);
```

2. If `i == currentEnd`
   → current jump exhausted
   → increment jump
   → update currentEnd = farthest

---

## ✅ Java Code

```java id="jump2code"
public int jump(int[] nums) {
    int jumps = 0;
    int currentEnd = 0;
    int farthest = 0;

    for (int i = 0; i < nums.length - 1; i++) {
        farthest = Math.max(farthest, i + nums[i]);

        if (i == currentEnd) {
            jumps++;
            currentEnd = farthest;
        }
    }

    return jumps;
}
```

---

## 🧠 Why loop till n-1?

Because once we reach last index,
we don’t need another jump.

---

## 🧠 Dry Run

`[2,3,1,1,4]`

Start:

```
jumps=0
currentEnd=0
farthest=0
```

i=0 → farthest=2
i==currentEnd → jumps=1, currentEnd=2

i=1 → farthest=4
i=2 → farthest=4
i==currentEnd → jumps=2, currentEnd=4

Stop.

Answer = 2 ✅

---

# 🧠 Difference Between Game I & II

| Feature       | Jump Game I  | Jump Game II                 |
| ------------- | ------------ | ---------------------------- |
| Goal          | Can reach?   | Min jumps?                   |
| Variable      | maxReach     | currentEnd, farthest         |
| Failure check | i > maxReach | Usually guaranteed reachable |
| Layer logic   | No           | Yes                          |

---

# 🧠 Why Different If Conditions?

Game I:

```java
if (i > maxReach)
```

We must ensure index reachable.

Game II:

```java
if (i == currentEnd)
```

We finished current jump range.
Time to increment jump.

---

# 🧠 Why This Greedy Works?

Because:

> Expanding maximum reachable range early never hurts.

More reach = more flexibility.

There is no better hidden combination.

So greedy safe.

---

# 🧠 Complexity

Time: O(n)
Space: O(1)

---

# 🧠 How To Identify This Category

Ask:

1. Is each position giving reach?
2. Is problem about reaching end?
3. Is it asking minimum steps/jumps?

If YES → Range Expansion Greedy.

---

# 🎯 Ultra Short Memory Version

Jump Game I:

> Track maximum reachable index.

Jump Game II:

> Expand range layer by layer and count expansions.

---

# 🧠 Common Mistakes

❌ Forgetting i > maxReach check
❌ Looping till n instead of n-1 in Game II
❌ Not understanding currentEnd meaning

---

# 🧠 Final Concept Lock

Game I:

"Can I reach?"

Game II:

"How many times must I extend my reach?"

---

Now before we move ahead, I want to check something.

If array is:

`[1,1,1,1]`

Jump Game II answer kya hoga?

And why?

Explain step by step in your words. 💪

Perfect 🔥
Now we go to another Greedy category:

# 💰 Local Profit Accumulation Greedy

This includes problems like:

* Best Time to Buy and Sell Stock (I & II)
* Maximum Subarray (Kadane – greedy flavor)
* Candy Distribution (partially greedy)
* Gas Station (profit balance logic)
* Assign Cookies (gain matching logic)

This category is different from:

* Interval Greedy
* Range Expansion
* Resource Allocation

---

# 🧠 Core Idea of Local Profit Greedy

These problems ask:

> At each step, can I gain something?
> If yes, accumulate it.
> If not, reset or skip.

The key pattern:

👉 Track best local gain
👉 Update global best
👉 Never revisit past combinations

---

# 🟢 Example 1 – Best Time to Buy & Sell Stock (Single Transaction)

## 🎯 Problem

Buy once, sell once.
Maximum profit.

---

## 🧠 Mental Model

At each day:

1. Track minimum price so far
2. Calculate profit if sold today
3. Update maximum profit

We accumulate the best gain.

---

## ✅ Java Code

```java id="stock1"
public int maxProfit(int[] prices) {
    int minPrice = Integer.MAX_VALUE;
    int maxProfit = 0;

    for (int price : prices) {
        if (price < minPrice) {
            minPrice = price;
        } else {
            int profit = price - minPrice;
            maxProfit = Math.max(maxProfit, profit);
        }
    }

    return maxProfit;
}
```

---

## 🧠 Dry Run

`[7,1,5,3,6,4]`

minPrice=7
minPrice=1
profit=4
profit=2
profit=5 → maxProfit=5
profit=3

Answer = 5

---

## 🔥 Why Greedy Works?

Because:

> Buying at smallest so far always maximizes future profit.

Future cannot beat a smaller buy price.

---

# 🟡 Example 2 – Best Time to Buy & Sell Stock II (Multiple Transactions)

## 🎯 Problem

Unlimited transactions allowed.

---

## 🧠 Mental Model

Whenever price increases from previous day:

Take the profit.

Why?

Because:

1 → 3 → 5
Instead of 1 → 5 (profit=4)

You can:
1 → 3 (2)
3 → 5 (2)
Total=4

Same result.

---

## ✅ Java Code

```java id="stock2"
public int maxProfit(int[] prices) {
    int profit = 0;

    for (int i = 1; i < prices.length; i++) {
        if (prices[i] > prices[i-1]) {
            profit += prices[i] - prices[i-1];
        }
    }

    return profit;
}
```

---

## 🧠 Dry Run

`[7,1,5,3,6,4]`

1→5 = +4
3→6 = +3

Total = 7

---

## 🔥 Why Greedy Works?

Because:

> Taking every upward movement never reduces final profit.

Small gains accumulate perfectly.

---

# 🟣 Example 3 – Maximum Subarray (Kadane’s Algorithm)

## 🎯 Problem

Find maximum sum contiguous subarray.

---

## 🧠 Mental Model

At each element:

If current sum becomes negative → reset.

Because:

Negative prefix will only reduce future gain.

---

## ✅ Java Code

```java id="kadane"
public int maxSubArray(int[] nums) {
    int currentSum = nums[0];
    int maxSum = nums[0];

    for (int i = 1; i < nums.length; i++) {
        currentSum = Math.max(nums[i], currentSum + nums[i]);
        maxSum = Math.max(maxSum, currentSum);
    }

    return maxSum;
}
```

---

## 🧠 Why Greedy Works?

If current sum becomes negative:

Keeping it will only hurt future.

So reset.

---

# 🧠 Common Mental Model for Local Profit Problems

When you see such problem, ask:

1️⃣ Is it about maximizing gain?
2️⃣ Can I track local improvement?
3️⃣ Does accumulating local gains lead to global optimum?

If yes → greedy possible.

---

# 🧠 Universal Pattern

Most Local Profit problems reduce to:

```java id="localpattern"
track bestSoFar
track currentGain
update answer
```

---

# 🧠 How To Identify This Category

Look for keywords:

* Maximum profit
* Maximum gain
* Maximum sum
* Optimize gain
* Accumulate value

Usually O(n), single pass.

---

# 🎯 Deep Difference From Other Greedy Types

| Category            | Core Idea        |
| ------------------- | ---------------- |
| Interval            | Manage timeline  |
| Range Expansion     | Expand boundary  |
| Resource Allocation | Count overlap    |
| Local Profit        | Accumulate gains |

---

# 🧠 Ultra Compression (For Notes)

Local Profit Greedy =

> Keep accumulating beneficial moves,
> discard harmful ones immediately.

---

# 🔥 Final Important Insight

Local greedy works when:

> Local improvement never blocks a better future combination.

Stock II works
Kadane works
But coin example failed because smaller coins beat greedy choice.

---

Now I want to test your clarity.

Array:
`[-2,1,-3,4,-1,2,1,-5,4]`

In Kadane’s logic,
at which point do we reset current sum?

Explain conceptually. 💪

Good. This is the exact place where most people get confused. Let’s slow down and break it completely.

You’re asking:

> How exactly is `currentSum` getting reset when it becomes negative?

Let’s look at the core line again:

```java
currentSum = Math.max(nums[i], currentSum + nums[i]);
```

This single line is the reset logic.

---

# 🧠 What This Line Really Means

At every index `i`, we are deciding:

👉 Should I:

A) Continue the previous subarray?
`currentSum + nums[i]`

OR

B) Start a new subarray from here?
`nums[i]`

Whichever is larger, we take that.

That’s it.

If previous sum is negative, continuing it will make things worse.

So we “reset” by choosing `nums[i]`.

---

# 🔥 Let’s Dry Run Properly

Array:

```
[-2, 1, -3, 4, -1, 2, 1, -5, 4]
```

Initialize:

```
currentSum = -2
maxSum = -2
```

---

## i = 1 (value = 1)

Option 1: continue → -2 + 1 = -1
Option 2: start fresh → 1

Take max(1, -1) = 1

So:

```
currentSum = 1
maxSum = 1
```

👉 This is where reset happened.

Because previous sum (-2) was hurting us.

---

## i = 2 (value = -3)

Option 1: 1 + (-3) = -2
Option 2: -3

Take max(-2, -3) = -2

```
currentSum = -2
maxSum = 1
```

---

## i = 3 (value = 4)

Option 1: -2 + 4 = 2
Option 2: 4

Take max(4, 2) = 4

👉 Reset again.

```
currentSum = 4
maxSum = 4
```

Because starting fresh is better than carrying -2.

---

# 🧠 So What Is “Reset”?

Reset happens automatically when:

```
nums[i] > currentSum + nums[i]
```

Which only happens when `currentSum` is negative.

Because:

If currentSum < 0
Then:

```
currentSum + nums[i] < nums[i]
```

So starting new is better.

---

# 🎯 Core Insight (Important)

If running sum becomes negative,

Keeping it will only reduce future gain.

So we drop it.

That is greedy thinking.

---

# 🔥 Simplified Version (Same Logic, Clearer)

Some people write Kadane like this:

```java
currentSum += nums[i];

if (currentSum < nums[i]) {
    currentSum = nums[i];
}
```

Or even more clearly:

```java
if (currentSum < 0) {
    currentSum = 0;
}
```

(But that version needs special handling for all-negative arrays.)

---

# 🧠 Final Intuition

Kadane is basically saying:

> Never carry a negative prefix forward.

Because negative prefix only hurts.

---

Now I’ll check if this is really clear.

If array is:

```
[-5, -1, -8]
```

What will Kadane return?

And why?

Think carefully before answering. 💪

Very good catch 👏
Tumne bilkul sahi point uthaya.

Tum bol rahe ho:

> Code me kahin bhi `nums[i] > currentSum + nums[i]` likha hi nahi hai.

Bilkul sahi. Wo condition directly likhi hui nahi hai.

But wo condition implicitly `Math.max(...)` ke andar check ho rahi hai.

Chalo isko bilkul seedha tod ke samjhte hain.

---

# 🧠 Ye Line Actually Kya Kar Rahi Hai?

```java
currentSum = Math.max(nums[i], currentSum + nums[i]);
```

Math.max internally kya karta hai?

It compares:

Option 1 → nums[i]
Option 2 → currentSum + nums[i]

Aur jo bada hoga, wo return karega.

Matlab internally ye ho raha hai:

```java
if (nums[i] > currentSum + nums[i]) {
    currentSum = nums[i];
} else {
    currentSum = currentSum + nums[i];
}
```

Bas yehi hai reset logic.

Condition likhi nahi hai,
but compare ho rahi hai.

---

# 🧠 Reset Kab Hota Hai?

Reset tab hota hai jab:

```java
nums[i] > currentSum + nums[i]
```

Isko thoda simplify karo.

Subtract nums[i] dono side se:

0 > currentSum

Matlab:

👉 Reset tab hota hai jab currentSum negative ho.

Yahi main logic hai.

---

# 🔥 Example Se Dekhte Hain

Suppose:

currentSum = -5
nums[i] = 4

Compare:

Option 1 → 4
Option 2 → -5 + 4 = -1

4 > -1 → true

To Math.max(4, -1) = 4

Reset ho gaya.

---

# 🎯 Important Insight

Kadane ka reset condition directly likha hua nahi hai.

Wo is line me hidden hai:

```java
Math.max(nums[i], currentSum + nums[i])
```

Ye decide karta hai:

Continue karein ya fresh start karein.

---

# 🧠 Ek Aur Simple Version

Isko aur simple likhna ho to:

```java
currentSum += nums[i];

if (currentSum < nums[i]) {
    currentSum = nums[i];
}
```

Ye bilkul same logic hai.

---

Ab main check karta hoon ki clarity aa gayi ya nahi.

Agar:

currentSum = -3
nums[i] = -2

To reset hoga ya continue?

Soch ke batao.

Perfect 🔥
Now we go to another Greedy category:

# 🪙 Canonical Coin-Type Greedy Problems

This category is very important because:

* Sometimes greedy works perfectly.
* Sometimes greedy completely fails.
* And interviews LOVE this trap.

So we must understand the mental model deeply.

---

# 🧠 1️⃣ What Are Coin-Type Problems?

These problems usually look like:

* Minimum number of coins to make amount
* Make change problem
* Reduce number using operations
* Make target sum using denominations
* Digit manipulation problems

They usually ask:

> Minimize number of steps / coins / operations

---

# 🎯 2️⃣ Core Greedy Idea (Coin Type)

Greedy rule usually tries:

> Always pick the largest possible denomination first.

Example:
Coins: 1, 5, 10, 20
Amount: 70

Greedy:
50 + 20 = 2 coins ✅

Works perfectly.

---

But sometimes it fails.

Example:
Coins: 1, 3, 4
Amount: 6

Greedy:
4 + 1 + 1 = 3 coins

Better:
3 + 3 = 2 coins ❌

---

# 🧠 3️⃣ When Does Greedy Work?

Greedy works when coin system is “canonical”.

Meaning:

> Smaller coins cannot combine to beat a larger coin.

Example:

1, 2, 5, 10
1, 5, 10, 20, 50
Powers of 2
Powers of 10

These are structured systems.

---

# 🧠 4️⃣ When Does Greedy Fail?

Greedy fails when:

> Smaller denominations combine to give better result than largest denomination.

Like:
1, 3, 4

Because:
4 looks best locally,
but 3 + 3 beats it globally.

---

# 🎯 5️⃣ Mental Model for Coin Greedy

When you see coin problem:

Ask:

1️⃣ Are denominations structured like currency?
2️⃣ Is each larger coin roughly multiple of smaller?
3️⃣ Can smaller coins combine to beat larger coin?

If yes → Greedy unsafe → Use DP.

If no → Greedy safe.

---

# 🟢 Case 1: Greedy Safe Example

Coins: 1, 2, 5
Amount: 11

Greedy:

5 + 5 + 1 = 3 coins

No better combination exists.

---

## ✅ Java Code (Greedy Version)

```java id="coinGreedy"
public int coinChangeGreedy(int[] coins, int amount) {
    Arrays.sort(coins);
    int count = 0;

    for (int i = coins.length - 1; i >= 0; i--) {
        while (amount >= coins[i]) {
            amount -= coins[i];
            count++;
        }
    }

    return amount == 0 ? count : -1;
}
```

---

## 🧠 Dry Run

Coins = {1,2,5}
Amount = 11

Take 5 → 6
Take 5 → 1
Take 1 → 0

Answer = 3

---

# 🔴 Case 2: Greedy Fail Example

Coins = {1,3,4}
Amount = 6

Greedy:

Take 4 → 2
Take 1 → 1
Take 1 → 0

Count = 3

But optimal = 3 + 3 = 2

---

Greedy cannot see future combination.

So DP required.

---

# 🧠 6️⃣ Deep Insight

Coin-type greedy is safe only when:

> Local best choice cannot be beaten by combination of smaller elements.

If combination possible → greedy unsafe.

---

# 🎯 7️⃣ How To Proceed in Interview?

Step 1: Try greedy mentally.
Step 2: Try small counterexample.
Step 3: If counterexample exists → switch to DP.
Step 4: If no counterexample → greedy safe.

---

# 🧠 8️⃣ Time Complexity

Greedy:
O(n log n) for sorting + linear scan

DP:
O(n × amount)

---

# 🧠 9️⃣ Ultra Compression for Notes

Coin-Type Greedy =

> Always pick largest denomination
> ONLY IF smaller denominations cannot combine to beat it.

---

# 🧠 10️⃣ Compare With Other Greedy Types

| Category        | Core Idea               |
| --------------- | ----------------------- |
| Interval        | Manage timeline         |
| Range Expansion | Expand boundary         |
| Resource        | Count overlaps          |
| Local Profit    | Accumulate gains        |
| Coin-Type       | Largest first (if safe) |

---

# 🔥 Final Important Insight

Coin problems are dangerous because:

They look greedy,
but many require DP.

So always test with small counterexample.

---

Now I want to test your understanding.

Coins: {1, 7, 10}
Amount: 14

Greedy kya karega?
Aur kya wo optimal hoga?

Step by step socho. 💪

Perfect 💪
Now I’ll give you a **clean, structured, final summary of all 5 Greedy categories** — beginner to interview level.

You can directly paste this into your notes.

---

# 📘 COMPLETE GREEDY MENTAL MODEL (All 5 Types)

Greedy means:

> Make the best local decision at each step
> and never revisit it.

But greedy problems appear in **5 different structural forms**.

Understanding structure is more important than memorizing problems.

---

# 🟢 1️⃣ Interval Greedy (Timeline Management)

### 🔹 Used In:

* Merge Intervals
* Insert Interval
* Non-overlapping Intervals
* Maximum Meetings
* Minimum Arrows

---

## 🧠 Core Idea

We are managing segments on a timeline.

First classify:

### A) Structure Problems

* Merge / Insert
* Sort by **start**
* Combine overlaps

### B) Selection Problems

* Max non-overlap
* Min removals
* Sort by **end**
* Keep earliest finishing interval

---

## ✅ Core Templates

### Merge (Structure)

```java
Arrays.sort(intervals, (a,b)->a[0]-b[0]);
```

### Selection

```java
Arrays.sort(intervals, (a,b)->a[1]-b[1]);
```

---

## 🎯 Memory Trick

Structure → sort by START
Selection → sort by END

---

# 🟡 2️⃣ Range Expansion Greedy

### 🔹 Used In:

* Jump Game I
* Jump Game II
* Minimum Taps
* Video Stitching

---

## 🧠 Core Idea

Expand maximum reachable boundary.

We track:

```java
maxReach
```

or

```java
currentEnd
farthest
```

---

### Jump Game I

Check if reachable.

```java
if (i > maxReach) return false;
maxReach = max(maxReach, i + nums[i]);
```

---

### Jump Game II

Count boundary expansions.

```java
farthest = max(farthest, i + nums[i]);

if (i == currentEnd) {
    jumps++;
    currentEnd = farthest;
}
```

---

## 🎯 Memory Trick

Always expand maximum boundary.

---

# 🔵 3️⃣ Resource Allocation Greedy

### 🔹 Used In:

* Meeting Rooms
* Minimum Platforms
* Peak Users
* CPU Scheduling

---

## 🧠 Core Idea

We cannot skip intervals.

We count maximum simultaneous overlap.

Each interval:
+1 at start
-1 at end

Peak overlap = answer.

---

### Heap Approach

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
```

Sort by start.

Reuse room if earliest end <= current start.

---

### Two Pointer Approach

Sort arrival and departure arrays.

If arrival < departure → active++
Else → active--

---

## 🎯 Memory Trick

Selection avoids overlap
Resource counts overlap

---

# 🟣 4️⃣ Local Profit Accumulation Greedy

### 🔹 Used In:

* Stock I & II
* Maximum Subarray (Kadane)
* Candy
* Gas Station

---

## 🧠 Core Idea

Keep beneficial gains.
Drop harmful ones immediately.

---

### Stock I

Track minimum so far.

```java
minPrice = min(minPrice, price);
profit = max(profit, price - minPrice);
```

---

### Stock II

Take every upward difference.

```java
if (prices[i] > prices[i-1])
    profit += prices[i] - prices[i-1];
```

---

### Kadane

Reset when negative.

```java
currentSum = max(nums[i], currentSum + nums[i]);
```

---

## 🎯 Memory Trick

Accumulate positive gain, discard negative prefix.

---

# 🪙 5️⃣ Coin-Type Greedy

### 🔹 Used In:

* Coin Change (sometimes)
* Change-making
* Reduce number problems

---

## 🧠 Core Idea

Always pick largest denomination first.

BUT ⚠️ Only safe if:

Smaller coins cannot combine to beat larger coin.

---

### Greedy Template

```java
for (int i = coins.length-1; i>=0; i--) {
    while (amount >= coins[i]) {
        amount -= coins[i];
        count++;
    }
}
```

---

## 🎯 Important

If smaller denominations can combine better
→ Greedy fails → Use DP.

---

# 🧠 MASTER DECISION TREE

When you see a greedy problem:

### Step 1: Identify structure

Is it:

* Timeline segments? → Interval
* Reach problem? → Range expansion
* Counting overlaps? → Resource
* Profit maximization? → Local profit
* Coin reduction? → Coin-type

---

### Step 2: Ask key greedy question

> Does local best choice ever block better future solution?

If YES → greedy unsafe
If NO → greedy safe

---

# 🧠 COMPLETE COMPARISON TABLE

| Category     | Core Logic      | Main Variable   |
| ------------ | --------------- | --------------- |
| Interval     | Manage segments | prevEnd         |
| Range        | Expand reach    | maxReach        |
| Resource     | Count overlap   | active / heap   |
| Local Profit | Accumulate gain | runningSum      |
| Coin-Type    | Largest first   | remainingAmount |

---

# 🎯 FINAL ULTRA-COMPRESSED VERSION

Greedy is about:

* Managing timeline
* Expanding reach
* Counting overlap
* Accumulating profit
* Reducing using largest step

But always remember:

> Greedy works only if local best = global best.

---

You now have full structural map of greedy.

If you want next level:

We can do mixed problems
and you classify first before solving.

That will make you interview-ready. 💪
