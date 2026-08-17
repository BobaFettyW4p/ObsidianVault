# Problem
You are given a string `word`. A letter `c` is called **special** if it appears **both** in lowercase and uppercase in `word`, and **every** lowercase occurrence of `c` appears before the **first** uppercase occurrence of `c`.

Return the number of **special** letters in `word`.

**Example 1:**

**Input:** word = "aaAbcBC"

**Output:** 3

**Explanation:**

The special characters are `'a'`, `'b'`, and `'c'`.

**Example 2:**

**Input:** word = "abc"

**Output:** 0

**Explanation:**

There are no special characters in `word`.

**Example 3:**

**Input:** word = "AbBCab"

**Output:** 0

**Explanation:**

There are no special characters in `word`.

**Constraints:**

- `1 <= word.length <= 2 * 105`
- `word` consists of only lowercase and uppercase English letters.

# Intuition

We do not need to track the position of every character in the array. If we know where the last instance of a lowercase letter, and the first instance of an uppercase letter, we are able to ascertain whether a letter is Special.

# Approach

We create two hashmaps, `last_lower` and `first_upper`. For each letter, the index of the last lowercase letter found will be stored as the value of the key of the lowercase letter in `last_lower` and the index of the first uppercase index will be stored as the value of the key of the uppercase letter in `first_upper`. We then iterate over the keys of `last_lower` and compare the values for the letter in `last_lower` and `first_upper`. If they indicate the letter is special, we increment the solution, and then return it at the end.
# Complexity

#### Time Complexity
Our approach loops over the input string once to populate the hashmap, and then once over the hashmaps to calculate the solution. This gives us a runtime of $O(n)$ as the hashmap will have a fixed number of entries that will not grow as the size of the input string increases.

#### Space Complexity
- We utilize two hashmaps, both of which will have one entry for every letter, so the maximum number of entries is 26. This does not correlate with the size of input string, and gives us a space complexity of $O(n)$

# Code
```
class Solution:
    def numberOfSpecialChars(self, word: str) -> int:
        last_lower = {}
        first_upper = {}
        sol = 0
        for idx, char in enumerate(word):
            if char.islower():
                last_lower[char] = idx
            else:
                first_upper.setdefault(char,idx)
        for char in last_lower.keys():
            if char.upper() in first_upper.keys():
                if last_lower[char] < first_upper[char.upper()]:
                    sol += 1
                else:
                    continue
            else:
                continue
        return sol
```

# Results

![[Pasted image 20260531102744.png]]