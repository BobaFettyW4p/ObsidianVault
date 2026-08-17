# Problem

You are given a string `word`. A letter is called **special** if it appears **both** in lowercase and uppercase in `word`.

Return the number of **special** letters in `word`.

**Example 1:**

**Input:** word = "aaAbcBC"

**Output:** 3

**Explanation:**

The special characters in `word` are `'a'`, `'b'`, and `'c'`.

**Example 2:**

**Input:** word = "abc"

**Output:** 0

**Explanation:**

No character in `word` appears in uppercase.

**Example 3:**

**Input:** word = "abBCab"

**Output:** 1

**Explanation:**

The only special character in `word` is `'b'`.

**Constraints:**

- `1 <= word.length <= 50`
- `word` consists of only lowercase and uppercase English letters.
# Intuition

We don't care how many times an uppercase or lowercase letter appears. If a single upper and lowercase letter appear, that letter is special.
# Approach

We use a set to store all of the unique characters in the input string, and then sum up the number of unique letters that appear in lower and upper case 
# Complexity

Creating the set will have a runtime of $O(n)$ as the interpreter will need to parse every letter in the input string. Crafting the sum to return can be assumed to occur in $O(1)$ time as the word only consists of English letters, so the set will only contain a max of 52 letters, which does not scale with input size.
# Code
```
class Solution: 
	def numberOfSpecialChars(self, word: str) -> int: 
		seen = set(word) 
		return sum(char in seen and char.upper() in seen for char in string.ascii_lowercase)
```

# Results

![[Pasted image 20260531103711.png]]