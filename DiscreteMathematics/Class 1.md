- Discrete mathematics
	- basic to most other course in MPCS
- We will cover:
	- Logic and proof
	- Number Theory
	- Counting
	- Probability
	- Midterm
	- Graph theory
## Logic and Proof
- what logical forms mathematical statements may take and how to proove them
- Propositions
	- "A *proposition* is a statement which is either true or false"
		- e,.q, $\sqrt{3} \text{ is an irrational number}$
		- $\text{Every even integer is the sum of two primes}$
			- Goldbach Conjecture
				- nobody knows if it's true or false, but it's one or the other
		- $1 + 1 = 5$
			- false
	- Non-proposition examples
		- $\text{Is} 2+2=4$
			- not a proposition since it's a question
		- $x^2 + 3x = 5$
			- depends on the value of x
- propositions can be combined to create more complicated propositions
	- the simplest compound propositions are connected by and, or, or not
		- "boolean connectors"
	- Conjunction: $p \land q$
		- True only when $P$ and $Q$ are both true
	- Disjunction: $p \lor q$
		- True when at least one of $P$ or $Q$ is true
	- Negation: $\not p$
		- True when $P$ is false
	- These are called *propositional formulas*
- A proposition that is always true regardless of the constituent variables is called a *tautology*
- A proposition that is always false regardless of the constituent variables is called a *contradiction*
	- ex. $p \lor \not p$ is a tautology
		- one must be true
	- $p \land \not p$ is a contradiction
		- at least one must be false
- a useful tool to describe the possible inputs of a propostiion is a *truth table*

## Truth Tables for Boolean Connectives
|p q | p and q |
|----|----------|
T T | T |
T F | F |
F T | F |
F F | F |

| p q | p or q |
|-----|--------|
T T | T |
T F | T |
F T | T |
F F | F |

| p | not p |
T | F |
F | T |

- *Implication*: $p \implies q$ (p implies q)
	- critical in proof
	- the same as $if\ p,\ then\ q$
	- P is called the hypothesis, Q is called the conclusion
	- Ex. $\text{If you HW and Exams are} \geq 90 \text{ percent, you will get an A in the course}$
		- if both are true, or both are false, then $p \implies q$
			- can only prove the implication false when only one of $p$ and $q$ is false
- Truth table
$p$ $q$ | $p \implies q$
=========
T T | T
T F | F
F T | T
F F | T
- if $p$ is false, the implication is what is referred to as *vacuously true*

- if both $p \implies q$ and $q \implies p$ are both true, we say $p \text{ if and only if } q$
- $p \iff q$
	- *biconditional*
	- true when $p$ and $q$ have the same truth values
- Truth Table for the biconditional
$p$ $q$ | $p \iff q$
=========
T T | T
T F | F
F T | F
F F | T
- another way to write the biconditional
	- $(p \implies q) \land (q \implies p)$
$p$ $q$ | $(p \implies q) \land (q \implies p)$
=============
$T \land T = F$
$F \land T = F$
$T \land F = F$
$T \land T = T$
- the truth values for the biconditional are the same no matter which format you write them in
	- they are called *logically equivalent*
		- they are set to equal with an equals sign with 3 bars

- $\text{Given implication } p \implies q \text{, define its}$
- Contrapositive: $\not q \implies \not p$
	- logically consistent
- Converse: $q \implies p$
- 

$p$ $q$ | $\not p$ $\not q$ | $\not q \implies \not p$ | $q \implies p$
========
T T | F F |  T | T
T F | F T | F | T
F T | T F | T | F
F F | T T | T | T

- Thus, $\not q \implies \not p$ and $q \implies p$ is logically equivalent
- since $p \implies q$ and $q \implies p$ do not match perfectly on the truth table, they are not logically equivalent
- De Morgan's Laws
	- tell us how to negative a compount propsoition
	- $\not (p \land q)$ is logically equivalent to $\not p \lor \not q$
	- $\not (p \lor q)$ is logically equivalent to $\not p \land \not q$
		- solve this via truth tables
- Commutative, Associative and Distributive Laws
	- from grade school arithmetic, but hold for boolean logic for boolean connectives
	- can be proven, but they're not that interesting
	- can simply be stated when proving, or perhaps even omitted
		- everyone knows what you mean
- We have propositional variables, but we don't have variables inside the proposition
	- that's limiting
		- can't say $x \lt 1 \implies x + 1 \lt 2$
- Quantifiers
	- part of the magic of proofs is we can prove statements about infinite sets of objects with a finite sized proof
		- how do we formally write down a statement about infinitely many objects?
	- Ex. $\text{For all integers x, x is either even or odd}$
	- $P(x): \text{x is even}$
	- $Q(x): \text{x is odd}$
	- $P(x) \lor Q(x)$
		- called a *predicate* or a *propositional function*
			- denote a property
	- we have written the variable as a function notation
		- missing a quantifier (representing $\text{for all integers x}$)
	- Universal Quantifier: $\forall$ ("for all")
	- Existential Quantifier: $\exists$ ("exists", "there exists")
	- $(\forall x \in \mathbb{z})(P(x) \lor Q(x)); \mathbb{z} = {\dots -2, -1, 0, 1, 2, \dots}$
		- better way to phrase the example
	- Ex. $\text{There is an even integer}$
		- $(\exists x \in \mathbb{Z}) P(x)$
	- there is an implicit universe $u$ where variables are quantified
		- $u = \mathbb{Z}$
- Ex. $( \forall x \in \mathbb{Z}) (\exists y \in \mathbb{Z}) (x \lt y)$ : Given any integer, there is a larger one (True)
- $(\exists y \in \mathbb{Z}) (\forall x \in \mathbb{Z}) (x \lt y)$:  There is a largest integer (False)
	- only difference between these two statements is the order of the quantifiers
		- however, they have different truth values
	- moral of the story: **Order of quantifiers matters**
- Negation: $\not (\forall x P(x))$ is logically consistent with $(\exists x)(\not P(x))$
	- $\not(\exists x P(X))$ is logically consistent with $(\forall x) (\not P(X))$
		- truth tables is impractible because we're iterating on an infinite set
			- infinite rows
	- no need to limit ourselves to one quantifier
	- $\not (\forall x \exists y P(x,y))$ is logically consistent with $\exists x \not(\exists y P(x,y))$
		- $Q(x,y) = \exists y P(x,y,)$
		-  *prenex normal form* is when the predicates are nested in the front
		- flip the quantifiers, universal becomes existential and vice versa
			- drive the quantifers down as far as you can
## Proof
- Intuitive, it's familiar because we all assert things and don't like to say things are false
	- proofs are a way of guaranteeing our claims
		- require a clearly stated proposition to be proven
- what is a proof? how do we prove that a proposition is true?
- An *axiom* is accepted as true without proof
	- allow us to start somewhere
	- also don't have to prove *definitions*
- A proof is a sequence of formal statements
	- $P_1$
	- $P_2$
	- $P_3$
	- $\dots$
	- $q$ - the thing that is intended to be proven
	- each statement is either an axiom, a definition, or follows easily from the previous statement
		- sort of loose
		- in practice, we routinely skip steps so proofs are readable, understandable, and of reasonable length
			- how do we know what steps to skip?
				- depends on the audience
				- may be more explicit at the beginning of the class
### Direct Proof
- they take different forms
- the schema for a direct proof:
- if $p \implies q$
- $\text{assume } p$
- $\dots$
- $\text{(therefore) } q$ 

- Theorem: $\text { Let x be an integer. If x is odd, then x+1 is even}$
- Definition: $\text{An integer x is odd if } x=2k+1 \text{for some integer k}$
- Definition: $\text{An integer x is even if } x = 2k \text{for some integer k}$

 - Proof: $\text{Assume x is odd. Then } x = 2k+1 \text{ for some integer k}$
 - $\text{Adding 1 to both sides yields} x+1 = 2k+2$
 - $x+1 = 2(k+1)$
 - $\text{therefore, x+1 is even}$

- Theorem: $\text{The sum of two even integers is even}$
	- i.e. $\text{Let x and y be integers. If x is even and y is even, then x+y is even}$
- Proof:
	- First, assume $p$
		- $\text{Assume x,y are even. Then} x = 2k \text { and } y = 2l \text{for some integers k,l}$
		- $x + y = 2k + 2l$
		-  $= 2(k+l)$
		- $\text{therefore, } x+y \text{ is even}$
 - Theorem: $\text{The sum of two odd integers is even}$
- Claim: $\text{The sum of any two integers is even}$
- False: Proof: Counterexample: $1 + 2 = 3$

### Proof by Contraposition
- Schema:
- $\text{Assume } \not q$
- $\dots$
- $\text{Therefore} \not p$
- Theorem: $\text{Let x be an integer. If } x^2 \text{is even, then x is even}$
	- Try to prove everything directly first
- Proof: $\text{Assume } x^2 \text{is even. Then } x^2 = 2k \text{ for some integer k}$
- $x = \sqrt{2k}$ # square root of 2k is an irrational number, no easy way to proceed
	- if we prove the contrapositive, we prove the implication
- Contrapositive: $\text{If x is not even, then } x^2 \text{ is not even}$
- Axiom: $\text{Every integer is either even or odd. Therefore, if x not even, it is odd}$
- $\text{If x is odd, then } x^2 \text{is odd}$
- Proof: $\text{Assume x is odd. Then } x= 2k+1 \text{ for some integer k.}$
- $\text{Squaring, we obtain } x^2 = (2k+1)^2 = 4k^2 + 4k +1 = 2(2k^2 + 2k) +1$ # this is form of an odd integer
- $\text{Therefore, } x^2 \text{ is odd}$

### Proof by Contradiction
- Schema:
- $\text{Assume p is false}$
	- the desire is to eventually prove something that is always false
		- thus, $p$ must be true
- $\text{assume } \not p$
- $\dots$
- $r$
- $\dots$
- $\not r$ # this is a contradiction
- $\therefore p$
- Ex. $\sqrt{2} \text{ is irrational}$
- Definition: $\text{A number x is rational if there are two integers a,b such that } x=\frac{a}{b}, b \neq 0. \text{Assume a,b, have no common factor besides 1}$
- $\text{A number x is irrational if it is not rational. If } x  \neq \frac{a}{b} \text{for every a,b integers with } b \neq 0$

$\text{ Is  } x = 0.121212121212... \text{a rational number?}$
- Yes. Note: $100x - x = 12$
- $\text{so }x = \frac{12}{99}$
	- this meets the definition of rational

- Theorem: $\sqrt{2} is irrational$
- Proof: By contradiction $p: \sqrt{2} \text{ is irrational}$
- $\text{ Assume } \not p = \sqrt{2} \text{ is rational}$
- $\text{So } \sqrt{2} = \frac{a}{b} \text{ , where a,b have no common factor except 1, } b \neq 0$
- $\sqrt{2}b = a$
- $2b^2 = a^2$
- $\therefore a^2 \text{ is even}$
- $\therefore \text{a is even (by previosu theorem)}$
- $\text{then }a = 2k \text{ for some integer k}$
- $\text{then } a^2 = 4k^2$
- $\text{so } 2b^2 = 4k^2$
- $\text{ so } b^2 = 2k^2$
- $\therefore b^2 \text{ is even}$
- $\therefore a,b \text{have a common factor 2 (not 1)}$
- $\therefore \text {we have a contradiction}$
- $\therefore \not p \text{ is false}$
- $\therefore p \text{ is true, i.e. } \sqrt{2} \text{ is irrational.}$

### Mathematical Induction
- method to prove a proof is true for all positive, all negative integers
- Ex. $\text{For all positive integers n} \geq 1 \text{ , } 1+ 2 + 3 + \dots + n = \frac{n(n+1)}{2}$
- $P(1): 1 = \frac{1(1+1)}{2}$
- $P(2): 1+2 = \frac{2(2+1)}{2}$
- $P(3): 1+2+3 = \frac{3(3+1)}{2}$
- $\dots$
- Infinite $ of propositions
- Principle of mathematical induction:
	- $\text{Let P(n) be a property defined on positive integers.}$
	- $\text{If P(1) is True and...}$
		- Basis step
	- $P(k) \implies P(k+1) \text{for all integers } k \geq 1$
		- Inductive step
	- $\text{Then P(n) is True for all integers } n \geq 1$
	- this is an Axiom
		- "You can prove P(n) is true for all integers $n \geq 1$ in 2 steps"
			- Basis step (base case): $\text{Prove P(1) is True}$
			- Inductive Step: $\text{for any integer } k \geq 1 \text{ , if P(k) is True, then P(k+1) is True}$
	- $P(k)$ is called the *inductive hypothesis*
		- we can assume this is true, but $P(k+1)$ must be proven
- Theorem: $\text{For all positive integers n} \geq 1 \text{ , } 1+ 2 + 3 + \dots + n = \frac{n(n+1)}{2}$
- Proof: $\text{By induction on n}$
- Basis Step: Let $n =1$. $P(1): 1 = \frac{1(1+1)}{2}$ # True since both sides are equal
- Inductive Step: $\text{Assume } P(k) \text{ is True}$ $(P(k) is IH)$
- $P(k): 1+2+ \dots + k = \frac{k(k+1)}{2}$
- need to prove $P(k+1)$ is true
	- add $k+1$ to both sides:
	- $1+2+3+\dots+k+(k+1) =  \frac{k(k+1)}{2} + (k+1)$
	- $= \frac{k(k+1) + 2(k+1)}{2}$
	- $= \frac{(k+1)(k+2)}{2}$
	- $\therefore \text{P(k+1) is true}$

- Ex. Tiling problem
- Definition: A right tromino is a 2x2 square tile with a missing 1x1 square
- Theorem: A $2^n x 2^n \text{board with one missing square can be tiled by right trominos}$
- Defintion: Tiling of a figure is an exact coverage by trominos without overcapping or extending over the figure

### Strong Induction
- $\text{Let P(n) be a property defined on positive integers}$
- $\text{If P(1) is True and}$
- $\text{for all integers } 1 \leq m \leq k, P(1) \land \dot P(k) \implies P(k+1)$
- $\text{then } P(n) \text{ is true for all integers } n \geq 1$
- Theorem: $\text{Every integer } n \geq 2 \text{ is either prime, or can be written as a product of primes}$
- 
