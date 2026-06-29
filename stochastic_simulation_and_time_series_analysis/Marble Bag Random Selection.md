- 3 bags each contain 10k marbles
	- Bag1 has 7575 Red and 2525 Blue
	- Bag 2 has 6060 Red and 4040 Blue
	- Bag 3 has 4545 Red and 5555 blue
- Choose one of the bags at random and then pick a marble from the chosen bag at random.
	- what is the probability that the chosen marble is red?
- Let $R$ be the event that the marble chose is red
- Let $B_i$ be the event that bag $i$ is chosen. Thus:

$$
P(R|B_1) = 0.75; P(R|B_2) = 0.60; P(R|B_3) = 0.45
$$
Since $B_1, B_2, B_3 form a valid partition, then:

$$
P(R) = P(R|B_1)P(B_1) + P(R|B_2)P(B_2) + P(R|B_3)P(B3)
$$
$$
=(0.75)(1/3) + (0.60)(1/3) + (0.45)(1/3) = 0.60
$$
