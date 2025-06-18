# What is a Proof?
## Propositions
- **Definition** - A *proposition* is a statement that is either true or false
	- both of the following are propositions
		- `2+3 = 5` # this is true
		- `1+1=3` # this is false
	- excludes statements where the truthiness of the statement may vary, i.e. "it is now 5 o clock"
	- in general, you can't check a claim about an infinite set by checking a finite set of its elements, no matter how large it's finite set
		- claims about *all* numbers or all items are extremely common and carry a common notation. For example, these statements are identical:
			- For every nonnegative integer, n, the value of n<sup>2</sup> + n + 41 is prime
			- $\forall n \in \mathbb{N},\ p(n)\ \text{is prime.}$
				- $\forall$ is read as "for all"
				- $\mathbb{N}$ is read as the set of nonnegative integers
				- $\in$ is read as "is a member of", "belongs to", or "is in"
		- similar, this can be written as follows:
			- The equation a<sup>4</sup> + b<sup>4</sup> + c<sup>4</sup> = d<sup>4</sup> has no solution when a, b, c, and d are positive integers
			- $\forall a \in \mathbb{Z}^+\, \forall b \in \mathbb{Z}^+\, \forall c \in \mathbb{Z}^+\, \forall d \in \mathbb{Z}^+,\ a^4 + b^4 + c^4 \ne d^4$
			- In this equation, $\mathbb{Z}^+$ is a symbol for positive integers
			- Strings of $\forall$'s are generally abbreviated for easier reading, like so:
			- $\forall a, b, c, d  \in \mathbb{Z}^+. \ a^4 + b^4 + c^4 \ne d^4$
	- Take this proposition:
	- $\ 313(x^3 + y^3) = z^3 has\ no\ solution\ when\ x,y,z \in \mathbb{Z}^+$
		- in plain language, the math equation has no solution when x, y, and z are all positive
		- this proposition is false
			- the smallest counterexample has over 1000 digits!
	- "Every map can be colored with 4 colors so that adjacent regions have different colors"
		- Four Color Theorem
		- years were spent trying to prove it, but the eventual proof was too big to be checked without a computer
			- eventually proven correct, but a more intelligible proof was later found
	- $\ There\ are\ no\ positive\ integers\ x,\ y,\ and\ z\ such\ that\ x^n + y^n = z^n for\ some\ integer\ n > 2$
		- known as "Fermat's Last Theorem"
			- Fermat claimed to have a proof of this, but didn't have enough space to write it down
			- eventually proven to hold for all `n` up to 4,000,000
				- not sufficient to prove that it holds for all `n`
			- later proven for all `n`
	- $\ Every\ even\ integer\ greater\ than\ 2 is\ the\ sum\ of\ two\ primes$
		- known as "Goldbach's Conjecture"
		- known to hold for all numbers up to 10<sup>18</sup>, but not known whether to be true or false
- for a computer scientist, some of the most important things to prove are the correctness of programs and systems
	- whether it does what its "supposed" to
	- programs are notiously buggy
## Predicates
- a *predicate* can be understood as a proposition whose truth depends on the value of one or more variables
	- "`n` is a perfect square" is a predicate because you can't say whether it's true or not until you know what the value of `n` happens to be
		- if `n=4`, it's true because 4 is a perfect square
		- if `n=5`, it's false because 5 is not a perfect square
			- it's still a proposition because propositions can be either true or false
- predicates are often named with a letter and use a function-like notation to denote a predicate with specific variable values
	- to use the name `P` for the above predicate:
		- $\ P(n) ::= "n\ is\ a\ perfect\ square".$
			- you can then assert that P(4) is true and P(5) is false
		- If P is a predicate, then P(n) is either true or false (we can't tell until n is defined)
			- if p is an ordinary function, such as $\ p(n) = n^2 + 1$, then p(n) is a numerical quantity
				- do not confuse these
## The Axiomatic Method
- the standard procedure for establishing truth in mathematics was invented by Euclid
	- he began with 5 *assumptions* about geometry, which seemed undeniable based on direct experience
		- e.g. "there is a straight line segment between every pair of points"
			- propositions like these that are simply accepted as true are called *axioms*
		- he used these axioms to establish the truth of other propisitions by providing "proofs"
	- a *proof* is a sequence of logical deductions from axioms and previously proven statements that concludes with the proposition in question
	- there are several common terms for a proposition that has been proved. Each different term hints at the role of proposition within a larger body of work
		- important true propositions are called *theorems*
		- a *lemma* is a preliminary preposition useful for proving later propositions
		- a *corollary* is a proposition that follows in just a few logical steps from a theorem
	- Euclid's axiom and proof approach remains the foundation for mathematics today
		- called the *axiomatic method*
## Our Axioms
- The ZFC axioms are important in studying and justifying the foundations of mathematics, but are too primitive for practical purposes
	- we'll accept all familiar facts from high school math
	- an imprecise specification of axioms is troubling, a good rule of thumb is be straightforward about what is being assumed
### Logical Deductions
- logical deductions, or *inference rules* are used to prove new propositions using previously proven ones
- a fundamental inference rule is *modus ponens*
	- a proof of P together with a proof that $P\ implies\ Q$ is a proof of Q
- inference rules are written in a notation like this:
$$
\frac{P,\quad P\ IMPLIES\ Q}{Q}
$$
- the statements above the line, called *antecedents* are proved, then we consider the statements below the line, called the *conclusion* or *consequent* to also be proved
- a key requirement of an inference rule is that it must be *sound*
	- an assignment of truth values values to the letters $\ P , Q, ...,$ that makes all antecedents true must also make the consequent true
		- **if we start with true axioms and apply sound inference rules, everything we prove will also be true**
- there are many natural, sound inference rules:
- Rule:
$$
\frac{P\ IMPLIES\ Q, Q\ IMPLIES\ R}{P\ IMPLIES\ R}
$$
- Rule:
$$
\frac{NOT(P)\ IMPLIES\ NOT(Q)}{Q\ IMPLIES\ P}
$$
- Non-rule:
$$
\frac{NOT(P)\ IMPLIES\ NOT(Q)}{P\ IMPLIES\ Q}
$$
	- this is not sound
		- if *P* is assigned **T** and *Q* is assigned **F**, then the antecedent is true and the consequent is not
- each step in a proof should be "clear" and "logical"
	- you should state what previously proved facts are used to derive each new conclusion
### Patterns of proof
- in principle, a proof can be *any* sequence of logical deductions from axioms and previously proved statements that conclude with the preposition in question
	- this can be overwhelming: how do you start a rpoof?
- many proofs follow a handful of standard templates
	- each proof has its own details, but these templates provide you an outline to fill in
		- these templates can work together - one gives you a top level outline while others help at the next level of detail

## Proving an Implication
- propositions of the form "If P, then Q" are called *implications*
	- this is often rephrased as "P IMPLIES Q"
- examples:
	- if $\ ax^2 + bx + c = 0\ and\ a \ne 0, then\ x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}$
		- Quadratic formula
	- "If n is an even integer greater than 2, then n is a sum of two primes"
		- Goldbach's conjecture
	- if $\ 0 \leq x \leq 2, \text{ then } -x^3 + 4x + 1 \le 0$
- there are a couple of standard methods to prove an implication
### Method 1
- in order to prove that $\ \text{P IMPLIES Q}$
	- Write, $\text{Assume P}$
	- Show that $Q$ logically follows
#### Example
- $\ If\ 0 \leq x \leq 2, then\ -x^3 + 4x + 1 \gt 0$
- this is obviously correct for $x=0$
- however, it doesn't appear that $x^3$ doesn't increase greater than $4x$ until $x=2$
- so presumably, $-x^3 + 4x$ should be non-negative for all $x$ between 0 and 2 
- sounds good, but we have to prove it
- we can factor the $-x^3 +4x$ part to get a better view of what's occurring:
- $-x^3 + 4x = x(2-x)(2+x)$
- within the range, all of the values are therefore non-negative
	- a product of non-negative terms is also non-negative
- Proof:
- Assume $0 \leq x \leq 2$
- Then, $x, 2-x, and\ 2+x$ are all non-negative
- Adding 1 to this product gives a positive number regardless of the non-negative value, so:
- $x(2-x)(2+x) + 1 \gt 0$
- multiplying out on the left side proves that:
- $-x^3 + 4x + 1 \gt 0$
- a couple points that approve to all proofs
	- scratchwork should be kept separate from the final proof
	- Proofs typically begin with "Proof" and end with a delimited like "QED"
		- this purpose is to clarify where proofs begin and end
### Method 2: Prove to Contrapositive
- The implication $\text{"P IMPLIES Q"}$ is logically equivalent to its *contrapositive* $\text{NOT(Q) IMPLIES NOT(P)}$
	- proving one is as good as proving the other
	- if so, we can proceed as follows:
		- write "We prove the contrapositive" and state the contrapositive
		- proceed as in Method 1
- Example:
- $\text{ If r is irrational, then} \sqrt{r} \text{ is also irrational}$
- a number is *rational* when it equals a quotient of integers, i.e. if it equals $m/n$ for some integers $m$ and $n$
	- if it's not rational, it's *irrational*
- we must show that if $r$ is not a ratio of integers, then $\sqrt{r}$ is also not a ratio of integers
	- this is complicated, using the contrapositive is simpler
- Proof. We prove the contrapositive: if $\sqrt{r}$ is rational, then $r$ is rational
- Assume that $\sqrt{r}$ is rational. Then there exists integers $m$ and $n$ such that:
$$
\sqrt{r} = \frac{m}{n}
$$
- squaring both sides give:
$$
r = \frac{m^2}{n^2}
$$
Since $m^2$ and $n^2$ are both integers, $r$ is also rational

## Proving an "If and Only If"
- many theorems assert that two statements are logically equivalent
	- i.e. one holds if and only if the other does
	- "Two triangles have same length sides if and only if two side lengths and the angle between them are the same"
- "if and only if" comes up so oten it is often abbreviated "iff"
### Method 1: Prove Each Statement Implies the Other
The statement $\text{P IFF Q}$ is equivalent tot he two statements $\text{P IMPLIES Q}$ and $\text{Q IMPLIES P}$
You can prove an "iff" by proving *two* implications:
- "We prove $P$ implies $Q$ and vice versa"
- "First, we show $P$ implies $Q$"
- "Now, we show $Q$ implies $P$"
### Method 2: Construct a Chain of Iffs
- in order to prove that $P$ is true iff $Q$ is true:
	- "We construct a chain of if and only if implications"
	- Prove $P$ is equivalent to a second statement which is equivalent to a third statement and so forth until you reach $Q$
- requires more ingenuity, but the result can be a short elegant proof
- Example:
- The *standard deviation* of a sequence of values $x_1, x_2,...,x_n$ is defined to be:
$$
\sqrt{\frac{(x_1-\mu)^2 + (x_2 - \mu)^2 + ... + (x_n - \mu)^2}{n}}
$$
where $\mu$ is the average or mean of the values:
$$
\mu ::= \frac{x_1 + x_2 + ... + x_n}{n}
$$
#### Theorem
- The standard deviation of a sequence of values $x_1, ... , x_n$ is zero iff all the values are equal to the mean
- i.e. the standard deviation of test scores is zero if and only if everyone scored exactly the class average
Proof:
- We construct a chain of "iff" implications, starting with the statement that the standard deviation is zero:
$$
\sqrt{\frac{(x_1 - \mu)^2 + (x_2 - \mu)^2 + ... + (x_n - \mu)^2}{n}} = 0
$$
- Since zero is the only number whose square root is zero, this equation holds iff:
$$
(x_1 - \mu)^2 + (x_2 - \mu)^2 + ... + (x_n - \mu)^2 = 0
$$
- squares of real numbers are always nonnegative so every term on the left hand side is nonnegative
- this means it holds iff every term on the left hand side of the equation is zero
- but a term $(x_i - \mu)^2$ is zero iff $x_i = \mu$ so "every term on the left hand side of the equation is zero" is true iff
- Every $x_i$ equals the mean
## Proof by cases
- breaking a proof into cases and proving each case separately is a common, useful proof strategy
- let's agree that any two people have either met or they haven't. If every pair of people in a group has met, it is a *club*. If every pair of people in a group has not met, we'll call it a group of *strangers*
- Theorem: Every collection of 6 people includes a club of 3 people or a group of 3 strangers
- Proof: The proof is by case analysis. Let $x$ denote one of the six people.There are two cases:
	- Among 5 other people besides $x$, at least 3 have met $x$
	- Among the 5 other people, at least 3 have not met $x$
- We have to be sure that at least one of these two cases must hold
	- we've split the 5 people in two groups: those who have met $x$ and those who have not
		- at least one must have half the people
- Case 1: Suppose at least 3 people did meet $x
	- this case splits into two subcases:
	- Case 1.1: No pair among those people met each other.
		- these people are a group of at least 3 strangers
			- the theorem holds
	- Case 1.2: Some pair among these people have met each other
		- this pair, along with $x$, form a club of 3 people
			- the theorem holds
	- since the theorem holds under both subcases, it holds under the case
- Case 2: Suppose that at least 3 people did not meet $x$
	- this case also splits into 2 subcases:
	- Case 2.1: Every pair among those people met each other
		- these people are a club of at least 3 people
			- the theorem holds
	- Case 2.2: Some pair among those people have not met each other
		- along with $x$, this pair forms a group of at least 3 strangers
			- the theorem holds
	- thus, the theorem holds under this case
- since the theorem holds under all cases, it holds
## Proof by Contradiction
- In a *proof by contradiction* (also known as *direct proof*), you show that if a proposition were false, then some false fact would be true
	- since a false fact can't be true (by definition), the proposition must be true
- proof by contradiction is *always* viable
	- proofs can be a little convoluted so direct proofs are generally preferred if possible
- Method: In order to prove a proposition $P$ by contradiction:
	- Write "We use proof by contradiction"
	- Write "Suppose $P$ is false"
	- Deduce something known to be false (a logical contradiction)
	- Write "This is a contradiction; thus, $P$ must be true"
- Example: we'll prove by contradiction that $\sqrt{2}$ is irrational
	- a number is rational if it is equal to a ration of integers
- Theorem: $\sqrt{2} \text{ is irrational.}$
- Proof. We use proof by contradiction
- Suppose the claim is false, and $\sqrt{2}$ is rational.
	- We can then write $\sqrt{2}$ as a fraction $\frac{n}{d}$ in lowest terms
- Squaring both sides gives $2 = \frac{n^2}{d^2}$
- So $2d^2 = n^2$
- therefore, $n^2$ must be a multiple of 4
- Since $2d^2 = n^2$, we know $2d^2$  is a multiple of 4 and so $d^2$ is a multiple of 2
- Since the numerator $n$ and denominator $d$ have 2 as a common factor, this contradicts the the fact that $\frac{n}{d}$ is in lowest terms
- Thus $\sqrt{2}$ is irrational
## Good proofs in practice
- one purpose of a proof is to establish the truth of an assertion with absolute certainty
	- mechanically checkable proofs of enormous length or complexity can accomplish this
- humanly intelligible proofs are the only ones that help someone understand the subject
- important mathematical results can't be fully understood until their proofs are understood
- in addition to logical correctness, a good proof must also be clear
	- correctness and clarity often go together:
		- a well written proof is more likely to be a correct one since mistakes are harder to hide
- in practice, the notion of proof is a moving target
- some general tips:
	- **State your game plan**: A good proof begins by explaining the general line of reasoning
		- i.e. "we use case analysis" or "we argue by contradiction"
	- **Keep a linear flow**: the steps of an argument should follow each other in an intelligible order
	- **A proof is an essay, not a calculation**: a good proof looks like an essay with some equations thrown in. Use complete sentences
	- **Avoid excessive symbolism**: Use words where you reasonably can
	- **Revise and simplify**
	- **Introduce notation thoughtfully**: sometimes an argument can be greatly simplified by introducing a variable, devising a special notion, or defining a new term
		- do this sparingly as it forces the reader to remember it
	- **Structure long proofs**: When your proof needs facts that are easily stated but not readily proved, they are best pulled out as preliminary lemmas
		- if you're using the same argument repeatedly, capture it in a lemma that you can cite repeatedly instead
	- **Be wary of the "obvious"**: What is obvious to you may not be obvious to the reader
		- don't use "clearly" or "obviously" to bully the reader to accept something you're having trouble proving
	- **Finish**: Tie everything together yourself once all the facts are established and explain why the original claim follows
# Induction
- *induction* is a powerful method for showing a property is true for all nonnegative integers
	- its use a defining characteristic of *discrete* as opposed to *continuous* math
- there are two versions of induction: Ordinary and Strong
## Ordinary Induction
- Professors and candy bars
	- Suppose there is a professor with a bottomless bag of candy bars
		- she offers to share the candy in a particular way:
			- the student at the beginning of the line gets a candy bar
				- if a student gets a candy bar, the student directly behind them in line also gets a candy bar
	- Phrased mathematically: $\text{If student n gets a candy bar, then student n+1 gets a candy bar, for all nonnegative integers n}$
	- To consider it logically, every student in line is guaranteed a candy bar
### A Rule for Ordinary Induction
- The Induction Principle
	- $\text{Let P be a predicate on nonnegative integers. If}$
	- $\text{P(0) is true, and}$
	- $\text{P(n) IMPLIES P(n+1) for all nonnegative integers, n,}$
	- then
	- $\text{P(m) is true for all nonnegative integers, m}$
- we can refer to this method as *ordinary induction* when it is necessary to distinguish it
$$
\frac{P(0),\quad \forall n \in \mathbb{N}.~P(n)\ IMPLIES\ P(n+1)}{\forall m \in \mathbb{N}.~P(m)}
$$
- Theorem: $\text{For all n} \in \mathbb{N},$
- $1 + 2 + 3 + ... + n = \frac{n(n+1)}{2}$
- To prove the theorem by induction, defined predicate $P(n)$ to be the equation
	- now the theorem can be restated as the claim that $\text{P(n) is true for all n } \in \mathbb{N}$
		- we are now able to use the Induction Principle provided we establish two simpler facts:
			- $\text{P(0) is true}$
				- this is true because the sum of 0 items must be zero
			- $\text{For all n} \in  \mathbb{N}, \text{ P(n) IMPLIES P(n+1)}$
				- this is trickier, but can be managed
					- the basic plan to prove the validity of any assumption: assume the statement on the left and prove the statement on the right
						- We assume $P(n)$ in order to prove $P(n+1)$, which is:
$$
1 + 2 + 3 + ... + n + (n+1) = \frac{(n+1)(n+2)}{2}
$$
- Both equations are quite similar - adding $(n+1)$ to both sides and simplifying the right side  gives:
$$
1 + 2 + 3 + ... + n + (n+1) = \frac{n(n+1)}{2} + (n+1)
$$
$$
1 + 2 + 3 + ... + n + (n+1) = \frac{(n+2)(n+1)}{2}
$$

- Thus, if $P(n)$ is true, so is $P(n+1)$
	- this is valid for every nonnegative integer $n$, so this establishes the second fact required by the induction proof
		- The Induction Principle says the predication $P(m)$ is true for all nonnegative integers, $m$
			- The theorem is proved
### A Template for Induction Proofs
- There are five components to a proof by induction:
	- **State that the proof uses induction** - convey the overall structure of the proof, which helps the reader follow your argument
	- **Define an appropriate predicate $P(n)$** - this is called the *induction hypothesis*
		- the eventual conclusion will be that $P(n)$ is true for all nonnegative $n$
			- clearly stating the induction hypothesis is the most important part of an induction proof
				- it may be able to be lifted directly from the proposition you are trying to prove
	- **Prove that $P(0)$ is true** - typically easy
		- this is called the *base case* or *basis step*
	- **Prove that $P(n)$ implies $P(n+1)$ for every nonnegative integer $n$
		- this is called the *inductive step*
		- the basic plan is always the same: assume $P(n)$ is true and use this assumption to prove $P(n+1)$ is true
			- this argument must be valid for every nonnegative integer $n$, because the goal is to prove that all following implications are true
	- **Invoke induction** - Given the above facts, the induction principle allows you to conclude that $P(n)$ is true for all nonnegative $n$
		- this is the logical capstone to the argument, but it's usual not to mention it explicitly
	- **NOTE** - always be sure to explicitly label the *base case* and *inductive step*
		- this will make your proofs clearer and decrease the chance you forget a key step
### A Clean Writeup
- the previous proof was fine, but a bit wordy, a real proof will be more like this:
- Revised proof: We use induction. The induction hypothesis will be this equation:
$$
1 + 2 + 3 + ... + n = \frac{n(n+1)}{2}
$$
- Base case: $P(0)$ is true, because both sides of the induction hypothesis equal 0 when n=0
- Inductive step: Assume that $P(n)$ is true, that is the equation holds for some nonnegative integer $n$. Adding $n+1$ to both sides of the equation implies that:
$$
1 + 2 + 3 + ... + n + (n+1) = \frac{n(n+1)}{2} + (n+1)
$$
- by simple algebra we get:
$$
1 + 2 + 3 + ... + n + (n+1) = \frac{(n+2)(n+1)}{2}
$$
- which proves $P(n+1)$
	- thus it follows by induction that $P(n) is true for all nonnegative $n$
- this induction led to a proof of this summation formula but did not provide an intuitive way to understand it nor did it explain where the formula came from in the first place
	- this is a weakness and a strength
		- it is a weakness when a proof does not provide insight
		- it is a strength that a proof can provide a reader with a guarantee of correctness without requiring insight
### A Faulty Induction Proof
- False Theorem: $\text{All horses are the same color.}$
	- there is no $n$ mentioned, so we need to rephrase it in a way that makes $n$ explicit:
- False Theorem: $\text{In every set of } n \geq 1 \text{horses, all the horses are the same color.}$
	- slight variation on induction: our base case will be $n=1$ instead of $n=0$
- False Proof: The proof is by induction on n. The induction hypothesis, $P(n)$ will be:
	- $\text{In every set of n horses, all are the same color.}$
- Base case: $(n=1)$. $P(1)$ is true, because in a size 1 set of horses, there is only one horse, and thus they are all the same color
- Inductive Step: Assume that $P(n)$ is true for some $n \geq 1$
	- assume that in every set of $n$ horses, all are the same color
		- suppose we have a set of $n+1$ horses:
$$
h_1, h_2, ... , h_n, h_n+1
$$
- we need to prove these $n+1$ horses are the same color
- by our assumption, the first $n$ horses are the same color
- also by our assumption, the last $n$ horses are the same color too, which means all horses are the same color
- we proved $P(1)$ is true, but since our proof was faulty for $P(2)$, the entire thing falls apart
	- since $P(2)$ is false, $P(3)$, $P(4)$, and so on are not established
## Strong induction
- used for the same purposes as ordinary induction: proving a predicate is true for all nonnegative integers
	- useful when a simple proof that the predicate holds for $n+1$ does not follow just from the fact it holds at n, but does that it holds for other values $\leq n$
### A rule for strong induction
- $\text{Let P be a predicate on nonnegative integers If}$
- $\text{P(0) is true, and}$
- $\text{for all n} \in \mathbb{N}, \text{P(0), P(1), ... , P(n) together imply P(n+1)}$
- $\text{Then P(m) is true for all m} \in \mathbb{N}$

- the only change from ordinary induction is that strong induction allows you to make more assumptions in the inductive step of your proof
	- you may assume that $P(0), P(1), ..., P(n)$ are all true before you go to prove $P(n+1)$
- Strong Induction Rule:
$$
\frac{
P(0), \quad \forall n \in \mathbb{N}. \left( P(0)\ and\ P(1)\ and\ \dots\ and\ P(n)\ IMPLIES\ P(n+1) \right)
}{
\forall m \in \mathbb{N}.~P(m)
}
$$
- more succinctly:
$$
\frac{P(0), [ \forall k \leq n \in \mathbb{N}. P(k)]\ IMPLIES\ P(n+1)}{\forall m \in \mathbb{N}. P(m)}
$$
- the template for strong induction proofs is identical to ordinary induction except:
	- you should state your proof is by strong induction
	- you can assume that $P(0), P(1), \dots , P(n)$ are all true instead of just $P(n)$ during the inductive step
### Product of Primes
- Theorem: Every integer greater than 1 is a product of primes
- Proof: We will prove the theorem by strong induction, letting the induction hypothesis, $P(n)$, be:
$$
\text{n is a product of primes}
$$
- the theorem will follow if we prove that $P(n)$ holds for all $n \geq 2$
- Base case: $(n=2)$: $P(2)$ is true because 2 is prime, so it is a length one product of primes.
- Inductive step: 
	- suppose that $n \geq 2$ and that every number from 2 to $n$ is a product of primes. 
		- We must show that $P(n+1)$ holds
			- e.g. $n+1$ is also a product of primes. We argue by cases:
- if $n+1$ is itself prime, then it is a length one product of primes by convention, and thus $P(n+1)$ holds
	- otherwise, $n+1$ is not prime
		- this means that $n+1 = k*m$ for some integers $k$ and $m$ between 2 and $n$
			- by multiplying these products, it follows that $k * m = n+1$ is a product of primes
				- thus, $P(n+1)$ holds in this case as well
					- so therefore we know $P(n+1)$ holds in any case, which completes the proof by strong deduction
# Asymptotic Notation
- shorthand used to give a quick measure of the behavior of a function $f(n)$ as $n$ grows large
## Little O
 - for functions $f,g: \mathbb{R} \rightarrow \mathbb{R}$, with $g$ nonnegative, we say $f$ is asymptoticalyl smaller than $g$, in symbols:
$$
f(x) = o(g(x))
$$
iff
$$
\lim_{x \to \infty} \frac{f(x)}{g(x)} = 0
$$
- Lemma: $x^a = o(x^b)$ for all nonnegative constants $a<b$
	- using the familiar fact that $\log x <x$$ for all $x>1$
- lemma: $\log x = o(x^\epsilon) \text{ for all } \epsilon \gt 0$
- 






