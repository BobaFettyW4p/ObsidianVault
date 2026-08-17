# Problem

You are given an integer `mass`, which represents the original mass of a planet. You are further given an integer array `asteroids`, where `asteroids[i]` is the mass of the `ith` asteroid.

You can arrange for the planet to collide with the asteroids in **any arbitrary order**. If the mass of the planet is **greater than or equal to** the mass of the asteroid, the asteroid is **destroyed** and the planet **gains** the mass of the asteroid. Otherwise, the planet is destroyed.

Return `true` _if **all** asteroids can be destroyed. Otherwise, return_ `false`_._

**Example 1:**

**Input:** mass = 10, asteroids = [3,9,19,5,21]
**Output:** true
**Explanation:** One way to order the asteroids is [9,19,5,3,21]:
- The planet collides with the asteroid with a mass of 9. New planet mass: 10 + 9 = 19
- The planet collides with the asteroid with a mass of 19. New planet mass: 19 + 19 = 38
- The planet collides with the asteroid with a mass of 5. New planet mass: 38 + 5 = 43
- The planet collides with the asteroid with a mass of 3. New planet mass: 43 + 3 = 46
- The planet collides with the asteroid with a mass of 21. New planet mass: 46 + 21 = 67
All asteroids are destroyed.

**Example 2:**

**Input:** mass = 5, asteroids = [4,9,23,4]
**Output:** false
**Explanation:** 
The planet cannot ever gain enough mass to destroy the asteroid with a mass of 23.
After the planet destroys the other asteroids, it will have a mass of 5 + 4 + 9 + 4 = 22.
This is less than 23, so a collision would not destroy the last asteroid.

**Constraints:**

- `1 <= mass <= 105`
- `1 <= asteroids.length <= 105`
- `1 <= asteroids[i] <= 105`

# Intuition

A greedy approach is valid here. If you order the asteroids from smallest to largest, this will always return True if there are any valid solutions
# Approach

We sort the list of asteroids from smallest to largest, and then iterate over the list of asteroids. We compare the size of the planet to the size of the asteroid, and if it can be absorbed, we continue. If it can't, we return False. After the loop exits, the planet has absorbed all the asteroids, so we return True.

# Complexity

#### Time Complexity
The `sort()` function in Python utilizes the Timsort, which carries a runtime of $O(n\log n)$. We then iterate over every asteroid and perform a constant amount of work, which carries a runtime of $O(n)$. 
$\therefore$. our solution has a time complexity of $O(n \log n)$
#### Space Complexity
The `asteroids` list contains one entry for every $n$ in the input. It is modified in place, and minimal additional space is needed. This gives our solution a space complexity of $O(n)$

# Code
```
class Solution:

def asteroidsDestroyed(self, mass: int, asteroids: List[int]) -> bool:

	asteroids.sort()

	for a in asteroids:

		if mass < a:

			return False

		else:

			mass += a

	return True
```