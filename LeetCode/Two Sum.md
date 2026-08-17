# Intuition

  
The obvious approach is brute force. For each item in the input array, search for a complementary value in the array. If one is found, return both items. This has a runtime of $O(n^2)$. However, we can do better.

# Approach

Our approach leverages a hash table to more efficiently solve the problem. We create a hash table `found`, and loop through the input array. For each entry, we calculate the value $i$ that would satisfy the target value. We then check the hash table. If that value has already been discovered in the array, we return a list with the two indexes. Otherwise, we store that value in the hash table and continue on to the next value in the input array.

# Complexity

#### Time complexity:
As previously stated, this solution is O(n) complexity

#### Space complexity:
Space complexity is O(n). For each entry in the input array, we create one entry in the hash table `found`.

  

# Code

```python3 []

class Solution:

def twoSum(self, nums: List[int], target: int) -> List[int]:

	found = {}

	for idx, num in enumerate(nums):

		i = target-num

		if i in found.keys():

			return [idx, found[i]]

		else:

			found[num] = idx
```