# Problem

You are given two **0-indexed** integer permutations `A` and `B` of length `n`.

A **prefix common array** of `A` and `B` is an array `C` such that `C[i]` is equal to the count of numbers that are present at or before the index `i` in both `A` and `B`.

Return _the **prefix common array** of_ `A` _and_ `B`.

A sequence of `n` integers is called a **permutation** if it contains all integers from `1` to `n` exactly once.

**Example 1:**

**Input:** A = [1,3,2,4], B = [3,1,2,4]
**Output:** [0,2,3,4]
**Explanation:** At i = 0: no number is common, so C[0] = 0.
At i = 1: 1 and 3 are common in A and B, so C[1] = 2.
At i = 2: 1, 2, and 3 are common in A and B, so C[2] = 3.
At i = 3: 1, 2, 3, and 4 are common in A and B, so C[3] = 4.

**Example 2:**

**Input:** A = [2,3,1], B = [3,1,2]
**Output:** [0,1,3]
**Explanation:** At i = 0: no number is common, so C[0] = 0.
At i = 1: only 3 is common in A and B, so C[1] = 1.
At i = 2: 1, 2, and 3 are common in A and B, so C[2] = 3.

**Constraints:**

- `1 <= A.length == B.length == n <= 50`
- `1 <= A[i], B[i] <= n`
- `It is guaranteed that A and B are both a permutation of n integers.`

# Intuition

As the input lists are both a permutation, once an integer occurs in that list, it is guaranteed it will not appear again. This means we do not have to track seen for both lists independently. We can track them together and compare against the same object as we iterate.

Furthermore, our solution list is guaranteed to be monotonically increasing as it is a cumulative collection. Moving from one index to the next will either increase the number by 0 (in the event the item in either list has been seen before), 1 (in the event the item in one list has been seen before), or 2 (when both items have been seen before in the other list).

# Approach

We create a set, `seen` and initialize a counter, `i`. We then loop over the indexes in `A` (as the length of both lists is guaranteed to be the same, we could loop over B with no difference). For each index, if the item is in our seen set, we increment the counter. If it is not, it is added to seen. After each comparison, we append the value of `i` to the solution list and continue.

Once we exit the loop, we return the solution.
# Complexity

#### Time Complexity
We loop over every item in the array exactly once, and perform a constant amount of work. This gives us a runtime of $O(n)$.

#### Space Complexity
Our input is 2 lists of $n$ entries. Our `seen` set will have at most $2n$ items in it, which will give us a space complexity of $O(2n + 2n) = O(n)$.

# Code

```
class Solution:
    def findThePrefixCommonArray(self, A: List[int], B: List[int]) -> List[int]:
        seen = set()
        i = 0
        sol = []
        for idx in range(0, len(A)):
            if A[idx] in seen:
                i += 1
            else:
                seen.add(A[idx])
            if B[idx] in seen:
                i +=1
            else:
                seen.add(B[idx])
            sol.append(i)
                
        return sol
        
```
# Results

![[Pasted image 20260531104120.png]]