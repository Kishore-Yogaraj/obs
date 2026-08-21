### Learning to Code
To learn C++ we're going to follow the general learning protocol we have now it goes as follows:

We will be using this as our main source of information to learn from https://www.learncpp.com/
- Start a chapter
- Read the topic of the chapter
	- You know you understand the topic when you can read a topic and write in your own words what you learned
	- Do this again for the next chapter
- After the end of the chapter you will need to recall that entire chapter on a blank page recall
- Make sure you follow the retrieval process for all chapter until you are done

**These are the following chapters that need to be finished before you can begin learning about DSA**
- Chapters 1-13
- Chapters 16-17
- Topic 20.3 (Recursion pulled from chapter 20)

**These are the following chapters that need to be finished before you can begin understanding ROS2**
- Chapter 14-15
- Chapter 20.6 - 20.7
- Chapter 22
- Chapter 24

### Learning DSA
An **invariant** is something that does not change. It stays the same even when you apply rules, move things or run computer code.

You can think of this as a rule or condition that must stay true while a process runs


A **surface dressing** just means the cosmetic details of a problem. The stuff that changes how it looks without chaning the underlying pattern or logic needed to solve it.
- Data type change
- Specific operation change (sum vs product)
- Problem story change
#### Pattern order of learning
- **Two pointers**
- **Sliding window**
- **Fast & slow pointers**
- **Binary search (and its variants)**
- **DFS/BFS (trees, then graphs)**
- **Recursion/backtracking**
- **Dynamic programming (1D, then 2D)**
- **Greedy**
- **Heap/priority queue**
- **Union-Find, tries, etc. as needed later**

#### Acquisition phase - for each new pattern before practice
- **Abstract explanation first**
	- What invariant does this pattern require, in words, before touching code. What problem shape signals it.
	- What needs to stay true, in order for this pattern to work
- **Worked example #1**
	- Read only
	- Trace it by hand on the actual input. Self-explain and connect each move back to the invariant
- **Worked example #2**
	- Predict then check
	- Cover the next move, predict it from the invariant, reveal, check. If you miss and don't understand why even after seeing the answer, go back to narrating the invariant at every step before continuing (do not push forward).
	- Clean miss with immediate recognition is expected
- **Solo attempt
	- Same surface dressing as the worked examples (refer above to what surface dressing is)
- **Spaced redo**
	- Same problem or problems the next day without reference

Move on one predictions land cleanly (two in a row) and solo attempt succeeds with new problems

#### Interleaving
Start mixing patterns when 2 independent patterns have been learned (gone through above step). Pull small sets (4-6 problems) spanning the acquired patterns in random order, unlabeled. As new patterns are acquired add to this pool.

#### Spaced retrieval, at the pattern level
Have an existing tracking log with columns for:
- Pattern tag
- Date
- Surface dressing type
- Pass/fail
- Failure mode (unable to recognize pattern, recognized but wrong execution, missed an edge vase)
When due date of problem approaches, solve same problem again and explain step by step what you're doing.


### Key Things to Know
- **Time, consistency, and intensity will always win.** You need to give yourself time to be learn and do this
- **Be comfortable failing.** You will lose and lose and lose and lose some more until it comes together at once when you least expect it. Trust the process. A day where you don't try is the day you fail
- **Spaced retrieval is the ultimate.** Without retrieval and repetition you lose the skill you learned. Exercise it well
- **When learning a new pattern, exhaust your resources to learn it.** Books, videos, people, anything. There is no method to encoding other than asking questions until things are clear. The only other way to get better at encoding is to have some context from from the topic from the past.
- **Sleep.** You are simply nothing without sleep.