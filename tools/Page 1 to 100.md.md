# The Joy of Cryptography
Mike Rosulek (mike@joyofcryptography.com)
School of Electrical Engineering & Computer Science
Oregon State University, Corvallis, Oregon, USA

*Draft of January 3, 2021*



# Preface
*The Joy of Cryptography* is an undergraduate textbook in cryptography. This book evolved from lecture notes I developed for the cs427 course at Oregon State University (and before that, cs473 at the University of Montana).
Yes, I know that the title is ridiculous. All of the serious titles were already taken. I hope you understand that **actual joy is not guaranteed.**

## What Is This Book About?

This book is about the **fundamentals of provable security.**

 - **Security:** Cryptography is about controlling access to information. We break apart the nebulous concept of “security” into more specific goals: confidentiality, authenticity, integrity.
 - **Provable:** We can formally define what it means to be secure, and then mathematically prove claims about security. One prominent theme in the book is the logic of composing building blocks together in secure ways.
 - **Fundamentals:** This is an introductory book on the subject that covers the basics. After completing this course, you will have a solid theoretical foundation that you can apply to most real-world situations. You will also be equipped to study more advanced topics in cryptography.

This book is not a handbook telling you which cryptographic algorithm to use in every situation, nor a guide for securely implementing production-ready cryptographic libraries. We do not discuss specific cryptographic software (*e.g.*, PGP, Tor, Signal, TrueCrypt) or crypto*currencies* like Bitcoin. You won’t learn how to become a hacker by reading this book.

## Who Is This Book For?

This book is for anyone who might need to secure information with cryptography, and who is curious about what makes some things “secure” (and what makes other things insecure). I don’t imagine that most readers of this book will develop their own novel cryptography (*e.g.*, designing new block ciphers), but they will be far more likely to use and combine cryptographic building blocks — thus our focus on the logic of composition.

## What Background Is Needed To Understand This Book?

You will get the most out of this book if you have a solid foundation in standard undergraduate computer science material:

 - Discrete mathematics (of the kind you typically nd in year 2 or 3 of an undergraduate CS program) is **required background**. The book assumes that you are familiar with basic modular arithmetic, discrete probabilities, simple combinatorics, and especially proof techniques. Chapter 0 contains a brief review of some of these topics.
 - Algorithms & data structures background is **highly recommended**, and theory of computation (automata, formal languages & computability) is also **recommended**. We deal with computations and algorithms at a high level of abstraction, and with mathematical rigor. Prior exposure to this style of thinking will be helpful.

## Why Is Cryptography A Difficult Subject?

**It’s all the math, right?** Cryptography has a reputation of being a difficult subject because of the amount of difficult math, but I think this assessment misses the mark. A former victim, I mean student, summed it up bluntly when he shared in class (paraphrased):

> *Some other students were thinking of taking your course but were worried that it is really math-heavy. I wouldn’t say that this course is a lot of math exactly. It’s somehow even worse!*

Thanks, I think.
Anyway, many corners of cryptography use math that most CS undergrads would find quite advanced (advanced factoring algorithms, elliptic curves, isogenies, even algebraic geometry), but these aren’t the focus of this book. Our focus is instead on the logic of composing different building blocks together in provably secure ways. Yes, you will probably learn some new math in this book — enough to understand RSA, for example. And yes, there are plenty of “proofs.” But I honestly believe you’ll be fine if you did well in a standard discrete math course. I always tell my cs427 students that I’m not expecting them to love math, proofs, and theory — I only ask them to choose **not to be scared** of it.

**If not math, then what?** In an algorithms course, I could introduce and explain concepts with concrete examples — here’s what happens step-by-step when I run merge sort on this particular array, here’s what happens when I run Dijkstra’s algorithm on this particular graph, here are 42 examples of a spanning tree. You could study these concrete examples, or even make your own, to develop your understanding of the general case.

Cryptography is different because our main concerns are **higher up the ladder of abstraction** than most students are comfortable with.[^1] Yes, I can illustrate what happens step-by-step when you run a cryptographic algorithm on a particular input. This might help you understand **what the algorithm does**, but it can never illustrate **why the algorithm is secure**. This question of “why” is the primary focus of this book.

[^1]: Of course, abstraction is the heart of math. I may be making a false distinction by saying “it’s not the *math*, it’s the *abstraction*.” But I think there’s something to the distinction between a CS major’s typical math-aversion and what is really challenging about cryptography.

 - Security is a **global property** about the behavior of a system *across all possible inputs*. You can’t demonstrate security by example, and there’s nothing to see in a particular execution of an algorithm. Security is about a higher level of abstraction.
 - Most security definitions in this book are essentially: “*the thing is secure if its outputs look like random junk.*” If I give an example that is concrete enough to show actual inputs and outputs, and if things are working as they should, then all the outputs will just look like meaningless garbage. Unfortunately, no one ever learned very much by staring at meaningless garbage.

Systems are *insecure* when they fail to adequately look like random junk. Occasionally they fail so spectacularly that you can actually see it by looking at concrete input and output values (as in the case of the ECB penguin). But more often, the reason for insecurity is far from obvious. For example, suppose an encryption scheme was insecure because the XOR of the first two output blocks is the same as the XOR of the third and fourth output blocks. I’m not convinced that it would be helpful to show concrete example values with this property. What’s more, sometimes the reason for insecurity only “jumps off the page” on specific, non-obvious, choices of inputs.

If you want to be equipped to answer questions like “why is this thing secure but this other very similar thing is not?”, then you must develop an understanding at this higher level of abstraction. You’ll have to directly come to terms with abstract ideas like “this algorithm’s outputs look like random junk, under these circumstances,” and the consequences of these kinds of ideas. It’s hard to arrive at understanding without the usual scafolding of concrete examples (seeing algorithms executed on specific inputs), but this book is my best effort at making the path as smooth as I know how.

## Known Shortcomings

 - I’ve used this book as a primary course reference for several years now, but I still consider it to be a draft. Of course I try my best to ensure the accuracy of the content, but there are sure to be plenty of bugs, ranging in their severity. *Caveat emptor!*
I welcome feedback of all kinds — not just on errors and typos but also on the selection, organization, and presentation of the material.
 - I usually cover essentially this entire book during our 10-week quarters. There is probably not enough material to sustain an entire 16-week semester, though. I always find it easier to polish existing material than to add completely new material. Someday I hope to add more chapters (see the roadmap below), but for now you’ll have to get by without some important and interesting topics.
 - There is no solutions manual, and I currently have no plans to make one.

## Code-Based Games Philosophy

The security definitions and proofs in these notes are presented in a style that is known to the research community as *code-based* games. I’ve chosen this style because I think it offers significant pedagogical benefits:

 - Every security definition can be expressed in the same style, as the indistinguishability of two games. In my terminology, the games are *libraries* with a common interface/API but different internal implementations. An adversary is any calling program on that interface. These libraries use a concrete pseudocode that reduces ambiguity about an adversary’s capabilities. For instance, the adversary controls arguments to subroutines that it calls and sees only the return value. The adversary cannot see any variables that are privately scoped to the library.
 - A consistent framework for definitions leads to a consistent process for *proving* and *breaking security* — the two fundamental activities in cryptography. 
 In these notes, *breaking* a construction always corresponds to writing a program that expects a particular interface and behaves as differently as possible in the presence of two particular implementations of the interface.
*Proving security* nearly always refers to showing a sequence of libraries (called *hybrids*), each of which is indistinguishable from the previous one. Each of these hybrids is written in concrete pseudocode. By identifying what security property we wish to prove, we identify what the endpoints of this sequence must be. The steps that connect adjacent hybrids are stated in terms of syntactic rewriting rules for pseudocode, including down-to-earth steps like factoring out and inlining subroutines, changing the value of unused variables, and so on.
 - Cryptography is full of conditional statements of security: “*if A is a secure thingamajig, then B is a secure doohickey.*” A conventional proof of such a statement would address the contrapositive: “*given an adversary that attacks the doohickey-security of B, I can construct an attack on the thingamajig-security of A.*”
In my experience, students struggle to find the right way to transform an abstract, hypothetical B-attacking adversary into a successful A-attacking adversary. By defining security in terms of games/libraries, we can avoid this abstract challenge, and indeed avoid the context switch into the contrapositive altogether. In these notes, the thingamajig-security of A gives the student a new *constructive rewriting rule* that can be placed in his/her toolbox and used to bridge hybrids when proving the doohickey-security of B.

Code-based games were first proposed by Shoup[^2] and later expanded by Bellare & Rogaway.[^3] These notes adopt a simplified and unified style of games, since the goal is not to encompass every possible security definition but only the fundamental ones. The most significant difference in style is that the games in these notes have no explicit INITIALIZE or FINALIZE step. As a result, all security definitions are expressed as *indistinguishability* of two games/libraries, even security definitions that are fundamentally about unforgeability. Yet, we can still reason about unforgeability properties within this framework. For instance, to say that no adversary can forge a MAC, it suffices to say that no adversary can distinguish a MAC-verification subroutine from a subroutine that always returns false. An index of security definitions has been provided at the end of the book.

[^2]: Victor Shoup: *Sequences of Games: A Tool for Taming Complexity in Security Proofs.* ia.cr/2004/332
[^3]: Mihir Bellare & Philip Rogaway: *Code-Based Game-Playing Proofs and the Security of Triple Encryption.*
ia.cr/2004/331

One instance where the approach falls short, however, is in defining collision resistance. I have not been able to define it in this framework in a way that is both easy to use and easy to interpret (and perhaps I achieved neither in the end). See Chapter 11 for my best attempt.

## Other Boring Stuff

### Copyright

This work is copyright by Mike Rosulek and made available under the Creative Commons BY-NC-SA 4.0 license. Under this license, you are free to:

$\qquad$ **Share:** copy and redistribute the material in any medium or format.

$\qquad$ **Adapt:** remix, transform, and build upon the material.

The licensor cannot revoke these freedoms as long as you follow the following license terms:

$\qquad$ **Attribution:** You must give appropriate credit, provide a link to the license, and indicate if changes were made. You may do so in any reasonable manner, but not in any way that suggests the licensor endorses you or your use.

$\qquad$ **NonCommercial:** You may not use the material for commercial purposes.

$\qquad$ **ShareAlike:** If you remix, transform, or build upon the material, you must distribute your contributions under the same license as the original.

You may not apply legal terms or technological measures that legally restrict others from doing anything the license permits.

### About the cover

The cover design consists of assorted shell illustrations from *Bibliothèque conchyliologique,* published in 1846. The images are no longer under copyright, and were obtained from the Biodiversity Heritage Library (http://biodiversitylibrary.org/bibliography/11590).
Why shells? Just like a properly deployed cryptographic primitive, a properly deployed shell is the most robust line of defense for a mollusk. To an uniformed observer, a shell is just a shell. However, there are many kinds of shells, each of which provides protection against a different kind of attack. The same is true of the cryptographic building blocks we study in this course.

### Acknowledgements

Some financial support for writing this book has been kindly provided by the National Science Foundation (awards #1149647, #1617197) and the Oregon State University Open Textbook Initiative.
Thanks to Brent Carmer & Leo Reyzin for many thoughtful suggestions and comments about the material. I am also grateful for the many students in cs427 who have reported countless bugs.

### Changelog

**2021-01-03** 
Chapter 2 (provable security basics) is now much more explicit about how security definitions are a “template” that we “fill in” with specific algorithms (*e.g.*, Enc, Dec). Chapter 5 (PRGs) now compares/contrasts two approaches for extending the stretch of a PRG — one secure and one insecure. This chapter also introduces a “socratic dialogue” approach to thinking about security proofs (previously there was only one such dialogue in Chapter 7). Hints to the exercises are now upside-down for extra security!

**2020-02-05**
 Overhaul of Chapter 2 (provable security fundamentals). The structure is arguably more coherent now. The total number of examples is increased. I now also include both a successful security proof and an example of where an attempted security proof goes wrong (since the scheme is actually insecure).

**2020-01-09**
 High-frequency winter revisions are continuing. This update focuses entirely on Chapter 13 (RSA): Many many more examples are included, *in Sage!* Discussion of CRT is (hopefully) clearer. Digital signatures content is finally there. There’s a new discussion of how to actually compute modular exponentiation on huge numbers, and a couple fun new exercises.

**2020-01-05**
 Revising in preparation for teaching CS427 during Winter term.

 - Chapter 0: More examples. Expanded treatment of modular arithmetic. Tips&tricks for modular arithmetic and probabilities.
 - Chapter 1: Moderate reorganization of “things that cryptographers blissfully ignore.”
 - Chapters 12–15: Moved AEAD chapter into position as chapter 12. Public-key stuff is now chapters 13–15.
 - Chapter 13 (RSA): More (but not enough) examples of multiplicative inverses. New discussion of algorithmic aspects of exponentiation mod $N$. This chapter will eventually focus on signatures exclusively, but we’re not year that. Expect updates over the next few months.

**2019-03-21** 
Chapter 11 (hash functions) significant revisions: no more impenetrable security definition for collision-resistance; explicit treatment of salts; better examples for Merkle-Damgård and length-extension. New draft Chapter 15 on AEAD (after next revision will be inserted after Chapter 11).

**2019-01-07**
Extensive revisions; only the major ones listed here. Lots of homework problems added/updated throughout. I tried to revise the entire book in time for my Winter 2019 offering, but ran out of time.

 - Added a changelog!
 - Chapter 1: Kerckhoffs’ Principle now discussed here (previously only mentioned for the first time in Ch 2).
 - Chapter 2: Now the concepts are introduced in context of specific one-time security definition, not in the abstract. More examples of interchangeable libraries.
 - Chapter 3: Polynomial interpolation now shown explicitly with LaGrange polynomials (rather than Vandermonde matrices). Full interpolation example worked out.
 - Chapter 4: Better organization. Real-world contextual examples of extreme (large & small) $2^n$ values. Full proof of bad-event lemma. Generalized avoidance-sampling libraries.
 - Chapter 5: Motivate PRGs via pseudo-OTP idea. Better illustration of PRG function, and conceptual pitfalls. How NOT to build a PRG. New section on stream cipher & symmetric ratchet.
 - Chapter 6: Combined PRF & PRP chapters. Motivate PRFs via $m \mapsto (r, F (k, r) \oplus m)$ construction. Better discussion of eager vs. lazy sampling of exponentially large table. How NOT to build a PRF. New section on constructing PRG from PRF, and more clarity on security proofs with variable number of hybrids. Better illustrations & formal pseudocode for Feistel constructions.
 - Chapter 7: Other ways to avoid insecurity of deterministic encryption (stateful & nonce-based). Ridiculous Socratic dialog on the security of the PRF-based encryption scheme.
 - Chapter 8: Compare & contrast CTR & CBC modes.

### Road Map

The following topics are shamefully missing from the book, but are planned or being considered:

 1. authenticated key agreement, secure messaging / ratcheting (high priority)
2. random oracle & ideal cipher models (medium priority)
3. elliptic curves, post-quantum crypto (but I would need to learn them first)
4. DH-based socialist millionaires, PSI, PAKE, simple PIR, basic MPC concepts (low priority)



# Contents

 0. [Review of Concepts & Notation](#chapter0)
	 0.1 [Logs & Exponents](#ch0section1) 
	 0.2 [Modular Arithmetic](#ch0section2)  
	 0.3 [Strings](#ch0section3)  	 
	 0.4 [Functions](#ch0section4)  	 
	 0.5 [Probability](#ch0section5) 
	 0.6 [Notation in Pseudocode](#ch0section6) 
	 0.7 [Asymptotics (Big-O)](#ch0section7) 	 	  	 	 	 
 1. [One-Time Pad & Kerckhoffs’ Principle](#chapter1)



# 0.	Review of Concepts & Notation  <a name="chapter0"></a>

The material in this section is meant as a review. Despite that, **many students report that they find this review useful** for the rest of the book.

## 0.1	Logs & Exponents <a name="ch0section1"></a>

You probably learned (and then forgot) these identities in middle school or high school:

$$
\begin{aligned}
(x^{a})(x^{b}) &=x^{a+b} \\
(x^{a})^{b} &=x^{a b} \\
\log _{x}(a b) &=\log _{x} a+\log _{x} b \\
a \log _{x} b &=\log _{x}(b^{a})
\end{aligned}
$$

Well, it’s time to get reacquainted with them again.

In particular, **never ever** write $$(x^{a})(x^{b}) &=x^{a+b}$$. If you write this, your cryptography instructor will realize that life is too short, immediately resign from teaching, and join a traveling circus. But not before changing your grade in the course to a zero.

## 0.2	Modular Arithmetic <a name="ch0section2"></a>

We write the set of integers as:

$$
\mathbb{Z} \overset{\text{def}}{=} \{\ldots, -2, -1, 0, 1, 2, \ldots\},
$$

and the set of natural numbers (nonnegative integers) as:

$$
\mathbb{N} \overset{\text{def}}{=} \{0, 1, 2, \ldots\}.
$$

Note that 0 is considered a natural number.

**Definition 0.1**
*For $x,n \in \mathbb{Z}$, we say that $n$ **divides** $x$ (or $x$ **is a multiple of** $n$), and write $n \mid x$, if there exists an integer $k$ such that $x = kn$*.

Remember that the definitions apply to both positive and negative numbers (and to zero). We generally only care about this definition in the case where $n$ is positive, but it is common to consider both positive and negative values of $x$.

**Example**
*$7$ divides $84$ because we can write* $84 = 12 \cdot 7$.\
*$7$ divides $0$ because we can write* $0 = 0 \cdot 7$.\
*$7$ divides $-77$ because we can write* $-77 = (-11) \cdot 7$.\
*$-7$ divides $42$ because we can write* $42 = (-6) \cdot (-7)$.\
*$1$ divides every integer (so does $-1$). The only integer that 0 divides is itself*.
 
**Definition 0.2 (% operator)**
*Let $n$ be $a$ positive integer, and let $a$ be any integer. The expression $a$ % $n$ (usually read as “$a$ **mod** $n$”) represents the remainder after dividing $a$ by $n$. More formally, $a$ % $n$ is the **unique** $r \in \{0, \ldots, n - 1\}$ such that $n| (a - r)$.* [^4]

[^4]: The fact that only one value of r has this property is a standard fact proven in most introductory courses on discrete math.

Pay special attention to the fact that $a$ % $n$ is always a *nonnegative* number, even if $a$ is negative. A good way to remember how this works is:

$$
\def\arraystretch{1.5}
\begin{array}{|c|} \hline
a \text{ is } (a \%  n) \text{ more than a multiple of } n. \\ \hline
\end{array}
$$

**Example**
$21$ % $7 = 0$ *because* $21 = 3 \cdot 7 + \underline{0}$.\
$20$ % $7 = 6$ *because* $20 = 2  \cdot 7 + \underline{6}$.\
$-20$ % $7 = 1$ *because* $-20 = (-3) \cdot 7 + \underline{1}$. $(-20$ *is one more than a multiple of* $7.)$
$-1$ % $7 = 6$ *because* $-1 = (-1) \cdot 7 + \underline{6}$.

Unfortunately, some programming languages define % for negative numbers as $(-a)$% $n = -(a$ % $n)$, so they would define $-20$ % $7$ to be $-(20$ % $7) = -6$. This is madness, and it’s about time we stood up to these programming language designers and smashed them over the head with some mathematical truth! For now, if you are using some programming
environment to play around with the concepts in the class, be sure to check whether it defines % in the correct way.

**Definition 0.3 $(\mathbb{Z}_n)$**
*For positive $n$, we write $\mathbb{Z}_n \overset{\text{def}}{=} \{0, \ldots , n - 1\}$ to denote the set of **integers modulo** $n$. These are the possible remainders one obtains by dividing by $n$*. [^5] 

[^5]: Mathematicians may recoil at this definition in two ways: (1) the fact that we call it $\mathbb{Z}_n$ and not $\mathbb{Z}/(n\mathbb{Z})$; and (2) the fact that we say that this set contains integers rather than congruence classes of integers. If you appreciate the distinction about congruence classes, then you will easily be able to mentally translate from the style in this book; and if you don’t appreciate the distinction, there should not be any case where it makes a difference.

**Definition 0.4 $(\equiv n)$**
*For positive $n$, we say that integers $a$ and $b$ are **congruent modulo** $n$, and write $a \equiv_n b$, if $n | (a - b)$. An alternative definition is that $a \equiv_n b$ if and only if $a$ % $n = b$ % $n$.*

You’ll be in a better position to succeed in this class if you can understand the (subtle) distinction between $a \equiv_n b$ and $a = b$ % $n$:

$a \equiv_n b$: In this expression, $a$ and $b$ can be integers of any size, and any sign. The left and right side have a certain relationship modulo $n$.

$a = b$ % $n$: This expression says that two integers are equal. The “$=$” rather than “$\equiv$” is your clue that the expression refers to equality over the integers. “$b$ % $n$” on the right-hand side is an operation performed on two integers that returns an integer result. The result of $b$ % $n$ is an integer in the range $\{0, \ldots , n - 1\}$.

**Example** 
*“$99 \equiv_{10} 19$” is true. Applying the definition, you can see that $10$ divides $99 - 19$.*

On the other hand, “$99 = 19$ % $10$” is false. The right-hand side evaluates to the integer $9$, but $99$ and $9$ are different integers.

In short, expressions like $a \equiv_n b$ make sense for any $a$, $b$ (including negative!), but expressions like $a = b$ % $n$ make sense only if $a \in \mathbb{Z}_n$.

Most other textbooks will use notation “$a \equiv b$ (mod $n$)” instead of “$a \equiv_n b$.” I dislike this notation because “(mod $n$)” is easily mistaken for an operation or action that only affects the right-hand side, when in reality it is like an adverb that modifies the *entire* expression $a \equiv b$. Even though $\equiv_n$ is a bit weird, I think the weirdness is worth it.

If $d \mid x$ and $d \mid y$, then $d$ is a **common divisor** of $x$ and $y$. The largest possible such $d$ is called the **greatest common divisor (GCD)**, denoted gcd$(x,y)$. If gcd$(x,y) = 1$, then we say that $x$ and $y$ are **relatively prime**. The oldest “algorithm” is the recursive process that Euclid described for computing GCDs (ca. $300$ BCE):

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\text{GCD}(x,y): // Euclid’s\ algorithm \\ 
\quad \text{if }y = 0 \text{ then return }x \\
\quad \text{else return GCD}(y, x \% y) \\ \hline
\end{array}
$$

### Tips & Tricks

You may often be faced with some complicated expression and asked to find the value of that expression mod $n$. This usually means: find the unique value in $\mathbb{Z}_n$ that is congruent to the result. The straightforward way to do this is to first compute the result *over the integers*, and then reduce the answer mod $n$ (*i.e.*, with the % $n$ operator).

While this approach gives the correct answer (and is a good anchor for your understanding), it’s usually advisable to **simplify intermediate values mod** $n$. Doing so will result in the same answer, but will usually be easier or faster to compute:

**Example** 
*We can evaluate the expression $6 \cdot 7 \cdot 8 \cdot 9 \cdot 10$ % $11$ without ever calculating that product over the integers, by using the following reasoning:*

$$
\begin{array}{rll}
6 \cdot 7 \cdot 8 \cdot 9 \cdot 10 &=& (42) \cdot 8 \cdot 9 \cdot 10 \\
& \equiv_{11}& 9 \cdot 8 \cdot 9 \cdot 10 \\
&=& (72) \cdot 9 \cdot 10 \\
& \equiv_{11}& 6 \cdot 9 \cdot 10 \\
&=& (54) \cdot 10 \\
& \equiv_{11}& 10 \cdot 10 \\
&=& 100 \\
& \equiv_{11}& 1
\end{array}
$$

*In the steps that only work mod $11$, we write “$\equiv_{11}$”. We can write “=” when the step holds over the integers, although it wouldn’t be wrong to write “$\equiv_{11}$” in those cases too. If two expressions represent the same integer, then they surely represent values that are congruent mod $11$.*

My advice is to simplify intermediate values mod $n$, but “simplify” doesn’t always mean “reduce mod $n$ with the % $n$ operation.” Sometimes an expression can by “simplified” by substituting a value with something congruent, but *not* in the range $\{0, \ldots , n - 1\}$:

**Example** 
*I can compute $7^{500}$ % $8$ in my head, by noticing that $7 \equiv_8 -1$ and simplifying thusly:*

$$
7^{500} \equiv_{8}(-1)^{500}=1.
$$

*Similarly, I can compute $89^{2}$ % $99$ in my head, although I have not memorized the integer $89^{2} .$ All I need to do is notice that $89 \equiv{ }_{99}-10$ and compute this way:*

$$
89^{2} \equiv_{99}(-10)^{2}=100 \equiv_{99} 1
$$

*You can compute either of these examples the “hard way” to verify that these shortcuts lead to the correct answer.*

Since addition, subtraction, and multiplication are defined over the integers (*i.e.*, adding/subtracting/multiplying integers always results in an integer), these kinds of tricks can be helpful.

On the other hand, dividing integers doesn’t always result in an integer. Does it make sense to use division when working mod $n$, where the result always has to lie in $\mathbb{Z}_n$? We will answer this question later in the book.

## 0.3	Strings <a name="ch0section3"></a>

We write $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^n$ to denote the set of $n$-bit binary strings, and $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{*}$ to denote the set of all (finite-length) binary strings. When $x$ is a string of bits, we write $\mid x \mid$ to denote the length (in bits) of that string, and we write $\overline{x}$ to denote the result of flipping every bit in $x$. When it’s clear from context that we’re talking about strings instead of numbers, we write $\textcolor{brown}{0}^n$ and $\textcolor{brown}{1}^n$ to denote strings of $n$ zeroes and $n$ ones, respectively (rather than the result of raising the *integers* $0$ or $1$ to the $n$ power). As you might have noticed, I also try to use a different font and color for characters (including bits, anything that could be used to build a string through concatenation) vs. integers.

**Definition 0.5 ($\oplus$, XOR)**
*When $x$ and $y$ are strings of the same length, we write $x \oplus y$ to denote the bitwise exclusive-or (XOR) of the two strings. The expression $x \oplus y$ is generally not defined when the strings are different lengths, but in rare occasions it is useful to consider the shorter string being padded with $\textcolor{brown}{0}$s. When that’s the case, we must have an explicit convention about whether the shorter string is padded with leading $\textcolor{brown}{0}$s or trailing $\textcolor{brown}{0}$s.*

For example, $\textcolor{brown}{0011} \oplus \textcolor{brown}{0101} = \textcolor{brown}{0110}$. The following facts about the XOR operation are frequently useful:

$$
\begin{array}{rlll}
x \oplus x &=\textcolor{brown}{000 \cdots} & \text{XOR’ing a string with itself results in zeroes.} \\
x \oplus \textcolor{brown}{000 \cdots} &=x  & \text{XOR’ing with zeroes has no effect.}\\
x \oplus \textcolor{brown}{111 \cdots} &=\bar{x} & \text{XOR’ing with ones flips every bit.} \\
x \oplus y &=y \oplus x & \text{XOR is symmetric.} \\
(x \oplus y) \oplus z &=x \oplus(y \oplus z) & \text{XOR is associative.} \\
\end{array}
$$

See if you can use these properties to derive the very useful fact below:

$$
a = b \oplus c \Longleftrightarrow b = a \oplus c \Longleftrightarrow c = a \oplus b.
$$

There are a few ways to think about XOR that may help you in this class:

 - **Bit-flipping:** Note that XOR’ing a bit with $\textcolor{brown}{0}$ has no effect, while XOR’ing with $\textcolor{brown}{1}$ flips that bit. You can think of $x \oplus y$ as: “starting with $x$, flip the bits at all the positions where $y$ has a $\textcolor{brown}{1}$.” So if $y$ is all $\textcolor{brown}{1}$’s, then $x \oplus y$ gives the bitwise-complement of $x$. If $y = \textcolor{brown}{1010 \cdots}$ then $x \oplus y$ means “(the result of) flipping every other bit in $x$.”
 Many concepts in this course can be understood in terms of bit-flipping. For example, we might ask “what happens when I flip the first bit of $x$ and send it into the algorithm?” This kind of question could also be phrased as “what happens when I send $x \oplus \textcolor{brown}{1000 \cdots}$ into the algorithm?” Usually there is nothing special about flipping just the first bit of a string, so it will often be quite reasonable to generalize the question as “what happens when I send $x \oplus y$ into the algorithm, for an arbitrary (not-all-zeroes) string $y$?”
 - **Addition mod-2:** XOR is just addition mod $2$ in every bit. This way of thinking about XOR helps to explain why “algebraic” things like $(x \oplus y) \oplus z = x \oplus (y \oplus z)$ are true. They are true for addition, so they are true for XOR.
This also might help you remember why $x \oplus x$ is all zeroes. If instead of XOR we used addition, we would surely write $x + x = 2x$. Since $2 \equiv_2 0$, we get that $2x$ is congruent to $0x = 0$.

**Definition 0.6 ($||$, concatenation)**
*We write $x || y$ to denote the result of **concatenating** $x$ and $y$*. 

## 0.4	Functions <a name="ch0section4"></a>

Let $X$ and $Y$ be finite sets. A function $f : X  \to Y$ is:

**injective** (1-to-1) if it maps distinct inputs to distinct outputs. Formally: $x \neq x' \Rightarrow f(x) \neq f(x')$. If there is an injective function from $X$ to $Y$, then we must have $\mid Y \mid \geq \mid X \mid$.

**surjective** (onto) if every element in $Y$ is a possible output of $f$. Formally: for all $y \in Y$ there exists an $x \in X$ with $f(x) = y$. If there is a surjective function from $X$ to $Y$, then we must have $\mid Y \mid \leq \mid X \mid$.

**bijective** (1-to-1 correspondence) if $f$ is both injective and surjective. If there is a bijective function from $X$ to $Y$, then we must have $\mid X \mid = \mid Y \mid$.

## 0.5	Probability

**Definition 0.7 (Distribution)**
*A **(discrete) probability distribution** over a set $X$ of **outcomes** is usually written as a function* “Pr” *that associates each outcome $x \in X$ with a probability* Pr[x]. *We often say that the distribution **assigns** probability* Pr[x] *to outcome $x$*.

*For each outcome $x \in X$, the probability distribution must satisfy the condition $0 \leq$* Pr[x] *$\leq 1$. Additionally, the sum of all probabilities $\sum_{x \in X}$* Pr[x] *must equal $1$*.

**Definition 0.8 (Uniform)**
*A special distribution is the **uniform distribution** over a finite set $X$, in which every $x \in X$ is assigned probability* Pr[x] $= 1/[X]$.

We also extend the notation Pr to **events**, which are collections of outcomes. If you want to be formal, an event $A$ is any subset of the possible outcomes, and its probability is defined to be Pr[A] $= \sigma_{x \in A}$ Pr[x]. We always simplify the notation slightly, so instead of writing Pr[{$x \mid x$ satisfies some conditiong}], we write Pr[condition].

**Example** 
*A $6$-sided die has faces numbered $\{1, 2, \ldots , 6\}$. Tossing the die (at least for a mathematician) induces a uniform distribution over the choice of face. Then* Pr[$3$ *is rolled*] $= 1/6$, *and* Pr[ *an odd number is rolled*] $= 1/2$ and Pr[*a prime is rolled*] $= 1/2$.

### Tips & Tricks

Knowing one of the probabilities Pr[A] and Pr[$\neg$A] (which is “the probability that A *doesn’t* happen”) tells you exactly what the other probability is, via the relationship

$$
\text{Pr}[A] = 1 - \text{Pr}[\neg A].
$$

This is one of the most basic facts about probability, but it can be surprisingly useful since one of Pr[$A$] and Pr[$\neg A$] is often much easier to calculate than the other. If you get stuck trying to come up with an expression for Pr[$A$], try working out an expression for Pr[$\neg A$] instead.

**Example**
*I roll a six-sided die, six times. What is the probability that there is some repeated value? Let’s think about all the ways of getting a repeated value. Well, two of the rolls could be $1$, or three of rolls could be $1$, or all of them could be $1$, two of them could be $1$ and the rest could be $2$, etc. Oh no, am I double-counting repeated $2$s and repeated $1$s? Uhh $\ldots$*

*An easier way to attack the problem is to realize that the probability we care about is actually* $1-$Pr[*all 6 rolls are distinct*]. *This complementary event (all $6$ rolls distinct) happens exactly when the sequence of dice rolls spell out a permutation of $\{1, \ldots , 6\}$. There are $6! = 720$ such permutations, out of $6^6 = 46656$ total possible outcomes. Hence, the answer to the question is*

$$
1-\frac{6 !}{6^{6}}=1-\frac{720}{46656}=\frac{45936}{46656} \approx 0.9846
$$

Another trick is one I like to call **setting breakpoints** on the universe. Imagine stopping the universe at a point where some random choices have happened, and others have not yet happened. This is best illustrated by example:

**Example** 
*A classic question asks: when rolling two $6$-sided dice what is the probability that the dice match? Here is a standard (and totally correct way) to answer the question:*

> *When rolling two $6$-sided dice, there are $6^2 = 36$ total outcomes (a pair of numbers), so each has probability $1/36$ under a uniform distribution. There are $6$ outcomes that make the dice match: both dice $1$, both dice $2$, both dice $3$, and so on. Therefore, the probability of rolling matching dice is $6/36 = 1/6$.*

*A different way to arrive at the answer goes like this:*

> *Imagine I roll the dice one after another, and I pause the universe (set a breakpoint) after rolling the first die but before rolling the second one. The universe has already decided the result of the first die, so let’s call that value d. The dice will match only if the second roll comes up d. Rolling d on the second die (indeed, rolling any particular value) happens with probability $1/6$.*

This technique of setting breakpoints is simple but powerful and frequently useful. Some other closely related tricks are: (1) postponing a random choice until the last possible moment, just before its result is used for the first time, and (2) switching the relative order of independent random choices.

### Precise Terminology

It is tempting in this course to say things like “$x$ is a random string.” But a statement like this is sloppy on several accounts.

First, is 42 a random number? Is “heads” a random coin? What is even being asked by these questions? Being “random” is not a property of an outcome (like a number or a side of a coin) but a property of the process that generates an outcome.[^6] Instead of saying “$x$ is a random string,” it’s much more precise to say “$x$ was chosen randomly.”

[^6]: There is something called Kolmogorov complexity that can actually give coherent meaning to statements like “$x$ is a random string.” But Kolmogorov complexity has no relevance to this book. The statement “$x$ is a random string” remains meaningless with respect to the usual probability-distribution sense of the word “random.”

Second, usually when we use the word “random,” we don’t mean any old probability distribution. We usually mean to refer to the uniform distribution. Instead of saying “$x$ was chosen randomly,” it’s much more precise to say “x was chosen uniformly” (assuming that really is what you mean).

Every cryptographer I know (yes, even your dear author) says things like “$x$ is a random string” all the time to mean “$x$ was chosen uniformly [from some set of strings].” Usually the meaning is clear from context, at least to the other cryptographers in the room. But all of us could benefit by having better habits about this sloppy language. Students especially will benefit by internalizing the fact that **randomness is a property of the *process*, not of the individual outcome.**

## 0.6	Notation in Pseudocode

We’ll often describe algorithms/processes using pseudocode. In doing so, we will use several different operators whose meanings might be easily confused:

$\leftarrow$ When $\mathcal{D}$ is a probability distribution, we write “$x \leftarrow \mathcal{D}$” to mean “sample $x$ according to the distribution $\mathcal{D}$.”

If $\mathcal{A}$ is an algorithm that takes input and also makes some internal random choices, then it is natural to think of its output $\mathcal{A}(y)$ as a distribution — possibly a different distribution for each input $y$. Then we write “$x \leftarrow \mathcal{A}(y)$” to mean the natural thing: “run $\mathcal{A}$ on input $y$ and assign the output to $x$.”

We overload the “$\leftarrow$” notation slightly, writing “$x \leftarrow X$” when $X$ is a *finite set* to mean that $x$ is sampled from the *uniform distribution* over $X$.

$:=$ We write $x := y$ for assignments to variables: “take the value of expression $y$ and assign it to variable $x$.”

$\overset{?}{=}$ We write comparisons as $\overset{?}{=}$ (analogous to “$==$” in your favorite programming language). So $x \overset{?}{=} y$ doesn’t modify $x$ (or $y$), but rather it is an expression which returns true if $x$ and $y$ are equal.

You will often see this notation in the conditional part of an if-statement, but also in return statements as well. The following two snippets are equivalent:

$$
\def\arraystretch{1.5}
\begin{array}{|c|} \hline
\text{return } x \overset{?}{=} y \\ \hline 
\end{array} 
\qquad \Leftrightarrow \qquad
\begin{array}{|l|} \hline
\text{if } x \overset{?}{=} y \\
\quad	\text{return } \texttt{true} \\
\text{else:} \\
\quad	\text{return } \texttt{false} \\ \hline 
\end{array} 
$$

In a similar way, we write $x \overset{?}{\in} S$ as an expression that evaluates to true if $x$ is in the set $S$.

### Subroutine conventions

We’ll use mathematical notation to define the *types* of subroutine arguments:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline 
\underline{\text{FOO (}x \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{*}):} \\
\quad \cdots \\ \hline
\end{array}
\qquad \text{means} \qquad
\texttt{"void foo(string x) \{ \ldots\ \}"}
$$

## 0.7	Asymptotics (Big-$O$)

Let $f : \mathbb{N} \to \mathbb{N}$ be a function. We characterize the asymptotic growth of f in the following ways:

$$
\begin{aligned}
f(n) \text { is } O(g(n)) & \stackrel{\text { def }}{\Leftrightarrow} \lim _{n \rightarrow \infty} \frac{f(n)}{g(n)}<\infty \\
& \Leftrightarrow \exists c>0: \text { for all but finitely many } n: f(n)<c \cdot g(n) \\
f(n) \text { is } \Omega(g(n)) & \stackrel{\text { def }}{\Leftrightarrow} \lim _{n \rightarrow \infty} \frac{f(n)}{g(n)}>0 \\
&\Leftrightarrow \exists c>0: \text { for all but finitely many } n: f(n)>c \cdot g(n) \\
f(n) \text{ is } \Theta(g(n)) & \stackrel{\text { def }}{\Leftrightarrow} f(n) \text{ is } O(g(n)) \text{ and } f(n) \text{ is } \Omega(g(n)) \\
&\Leftrightarrow 0<\lim _{n \rightarrow \infty} \frac{f(n)}{g(n)}<\infty \\
&\Leftrightarrow \exists c_{1}, c_{2}>0: \text{ for all but finitely many } n: \\
& \qquad\qquad\qquad c_{1} \cdot g(n)<f(n)<c_{2} \cdot g(n)
\end{aligned}
$$

### Exercises

 0.1. Rewrite each of these expressions as something of the form $2^x$.
	(a) $\left(2^{n}\right)^{n}=? ?$
	(b) $2^{n}+2^{n}=? ?$
	(c ) $\left(2^{n}\right)\left(2^{n}\right)=? ?$
	(d) $\left(2^{n}\right) / 2=? ?$
	(e) $\sqrt{2^{n}}=? ?$	
	(f) $\left(2^{n}\right)^{2}=? ?$

0.2. (a) What is $0 + 1 + 2 + \cdots + (n - 2) + (n - 1) \% n$, when n is an odd integer? Prove your answer!
	(b) What is $0 + 1 + 2 + \cdots + (n - 2) + (n - 1) \% n$, when n is even? Prove your answer!

0.3.	What is $(-99) \% 10$?

0.4.	Without using a calculator, what are the last two digits of $357998^6$?

0.5.	Without using a calculator, what is $1000! \% 427$? (That’s not me being excited about the number one thousand, it’s one thousand *factorial!*)

0.6.	Which values $x \in \mathbb{Z}_{11}$ satisfy $x^2 \equiv_{11} 5$? Which satisfy $x^2 \equiv_{11} 6$?

0.7. What is the result of XOR’ing every $n$ bit string? For example, the expression below is the xor of every 5-bit string:
$$
\textcolor{brown}{00000} \oplus \textcolor{brown}{00001} \oplus \textcolor{brown}{00010} \oplus \textcolor{brown}{00011} \oplus \cdots \oplus \textcolor{brown}{11110} \oplus \textcolor{brown}{11111}
$$
Give a convincing justification of your answer.

0.8.	Consider rolling several d-sided dice, where the sides are labeled $\{0, \ldots , d - 1\}$.
(a) When rolling two of these dice, what is the probability of rolling *snake-eyes* (a pair of 1s)?
(b) When rolling two of these dice, what is the probability that they *don’t* match?
(c ) When rolling **three** of these dice, what is the probability that they all match?
(d) When rolling three of these dice, what is the probability that they **don’t** all match (including the case where two match)?
(e) When rolling three of these dice, what is the probability that at least two of them match (including the case where all three match)?
(f) When rolling three of these dice, what is the probability of seeing at least one 0? 

0.9.	When rolling two 6-sided dice, there is some probability of rolling snake-eyes (two 1s). You determined this probability in the previous problem. In some game, I roll both dice each time it is my turn. What is the smallest value $t$ such that:
$$
\text{Pr[I have rolled snake-eyes in at least one of my first } t \text{ turns]} \geq 0.5?
$$
In other words, how many turns until my probability of getting snake-eyes exceeds 50%?




# 1. One-Time Pad & Kerckhoffs’ Principle

You can’t learn about cryptography without meeting Alice, Bob, and Eve. This chapter is about the classic problem of **private communication**, in which Alice has a message that she wants to convey to Bob, while also keeping the contents of the message hidden from an eavesdropper[^7] Eve. You’ll soon learn that there is more to cryptography than just private communication, but it is the logical place to start.

[^7]: “Eavesdropper” refers to someone who secretly listens in on a conversation between others. The term originated as a reference to someone who literally hung from the eaves of a building in order to hear conversations happening inside.

## 1.1 What Is [Not] Cryptography?

> *“To define is to limit.”*
—Oscar Wilde

Cryptography is not a magic spell that solves all security problems. Cryptography can provide solutions to cleanly defined problems that often abstract away important but messy real-world concerns. Cryptography can give guarantees about what happens in the presence of certain well-defined classes of attacks. These guarantees may not apply if real-world attackers “don’t follow the rules” of a cryptographic security model. 
Always keep this in mind as we define (*i.e.*, limit) the problems that we solve in this course.

### Encryption Basics & Terminology

Let’s begin to formalize our scenario involving Alice, Bob, and Eve. Alice has a message $m$ that she wants to send (privately) to Bob. We call $m$ the **plaintext**. We assume she will somehow transform that plaintext into a value $c$ (called the **ciphertext**) that she will actually send to Bob. The process of transforming $m$ into $c$ is called encryption, and we will
use Enc to refer to the encryption algorithm. When Bob receives $c$, he runs a corresponding decryption algorithm Dec to recover the original plaintext $m$.
We assume that the ciphertext may be observed by the eavesdropper Eve, so the (informal) goal is for the ciphertext to be meaningful to Bob but meaningless to Eve.

$$
\textcolor{red}{\text{Image screenshot here}}
$$

### Secrets & Kerckhoffs’ Principle

Something important is missing from this picture. If we want Bob to be able to decrypt $c$, but Eve to *not* be able to decrypt $c$, then Bob must have some information that Eve doesn’t have (do you see why?). Something has to be kept secret from Eve.
You might suggest to make the details of the Enc and Dec algorithms secret. This is how cryptography was done throughout most of the last 2000 years, but it has major drawbacks. If the attacker does eventually learn the details of Enc and Dec, then the only way to recover security is to *invent new algorithms*. If you have a system with many users, then the only way to prevent everyone from reading everyone else’s messages is to *invent new algorithms* for each pair of users. Inventing even one good encryption method is already hard enough!
The first person to articulate this problem was Auguste Kerckhoffs. In 1883 he formulated a set of cryptographic design principles. Item #2 on his list is now known as **Kerckhoffs’ principle**:

> **Kerckhoffs’ Principle:**
*“Il faut qu’il n’exige pas le secret, et qu’il puisse sans inconvénient tomber entre les mains de l’ennemi.”*
**Literal translation:** [The method] must not be required to be secret, and it must be able to fall into the enemy’s hands without causing inconvenience.
**Bottom line:** Design your system to be secure even if the attacker has complete knowledge of all its algorithms.

If the algorithms themselves are not secret, then there must be some other secret information in the system. That information is called the **(secret) key**. The key is just an extra piece of information given to both the Enc and Dec algorithms. Another way to interpret Kerckhoffs’ principle is that *all of the security of the system should be concentrated in the secrecy of the key*, not the secrecy of the algorithms. If a secret key gets compromised, you only need to choose a new one, not reinvent an entirely new encryption algorithm. Multiple users can all safely use the same encryption algorithm but with independently chosen secret keys.
The process of choosing a secret key is called **key generation**, and we write KeyGen to refer to the (randomized) key generation algorithm. We call the collection of three algorithms (Enc, Dec, KeyGen) an **encryption scheme**. Remember that Kerckhoffs’ principle says that we should assume that an attacker knows the details of the KeyGen algorithm. But also remember that knowing the details (i.e., source code) of a randomized algorithm doesn’t mean you know the *specific output* it gave when the algorithm was executed.

$$
\textcolor{red}{\text{Image screenshot here}}
$$

### Excuses, Excuses

Let’s practice some humility. Here is just a partial list of issues that are clearly important for the problem of private communication, but which are not addressed by our definition of the problem.

 - We are not trying to hide *the fact that Alice is sending something* to Bob, we only want to hide the contents of that message. Hiding the existence of a communication channel is called *steganography*.
 - We won’t consider the question of how c reliably gets from Alice to Bob. We’ll just take this issue for granted.
 - For now, we are assuming that Eve just passively observes the communication between Alice & Bob. We aren’t considering an attacker that tampers with $c$ (causing Bob to receive and decrypt a different value), although we will consider such attacks later in the book.
 - We won’t discuss *how* Alice and Bob actually obtain a common secret key in the real world. This problem (known as **key distribution**) is clearly incredibly important, and we will discuss some clever approaches to it much later in the book.
In my defense, the problem we are solving is already rather non-trivial: once two users have established a shared secret key, how can they use that key to communicate privately?
 - We won’t discuss how Alice and Bob keep their key secret, even after they have established it. One of my favorite descriptions of cryptography is due to Lea Kissner (former principal security engineer at Google): “*cryptography is a tool for turning lots of different problems into key management problems.*”
 - Throughout this course we simply assume that the users have the ability to uniformly sample random strings. Indeed, without randomness there is no cryptography. In the real world, obtaining uniformly random bits from deterministic computers is extremely non-trivial. John von Neumann famously said, “*Any one who considers arithmetical methods of producing random digits is, of course, in a state of sin.*” Again, even when we take uniform randomness for granted, we still face the non-trivial question of how to use that randomness for private communication (and other applications), and also how to use only a *manageable amount* of randomness.

### Not Cryptography

People use many techniques to try to hide information, but many are “non-cryptographic” since they don’t follow Kerckhoffs’ principle:

 - Encoding/decoding methods like base64 . . .
$$
\textcolor{brown}{\texttt{joy of cryptography}} \quad \leftrightarrow \textcolor{brown}{\texttt{b25seSBuZXJkcyB3aWxsIHJlYWQgdGhpcw==}}
$$
. . . are useful for incorporating arbitrary binary data into a structured le format that supports limited kinds of characters. But since base64 encoding/decoding involves no secret information, it adds nothing in terms of *security*.
 - Sometimes the simplest way to describe an encryption scheme is with operations on binary strings (i.e., $\textcolor{brown}{0}$s and $\textcolor{brown}{1}$s) data. As we will see, one-time pad is defined in terms of plaintexts represented as strings of bits. (Future schemes will require inputs to be represented as a bitstring of a specific length, or as an element of $\mathbb{Z}_n$, etc.) 
In order to make sense of some algorithms in this course, it may be necessary to think about data being converted into binary representation. Just like with base64, representing things in binary has no effect on security since it does not involve any secret information. **Writing something in binary is not a security measure!**

## 1.2 Specifics of One-Time Pad

People have been trying to send secret messages for roughly 2000 years, but there are really only 2 useful ideas from before 1900 that have any relevance to modern cryptography. The first idea is Kerckhoffs’ principle, which you have already seen. The other idea is **one-time pad (OTP)**, which illustrates several important concepts, and can even still be found hiding deep inside many modern encryption schemes.
One-time pad is sometimes called “Vernam’s cipher” after Gilbert Vernam, a telegraph engineer who patented the scheme in 1919. However, an earlier description of one-time pad was rather recently discovered in an 1882 text by Frank Miller on telegraph encryption.[^8]

[^8]: See the article Steven M. Bellovin: “Frank Miller: Inventor of the One-Time Pad.” *Cryptologia* 35 (3), 2011.

In most of this book, secret keys will be strings of bits. We generally use the variable $\lambda$ to refer to the length (# of bits) of the secret key in a scheme, so that keys are elements of the set $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$. In the case of one-time pad, the choice of $\lambda$ doesn’t affect security ($\lambda = 10$ is “just as secure” as $\lambda = 1000$); however, the length of the keys and plaintexts must be compatible. In future chapters, increasing $\lambda$ has the effect of making the scheme harder to
break. For that reason, $\lambda$ is often called the **security parameter** of the scheme.
In one-time pad, not only are the keys $\lambda$-bit strings, but plaintexts and ciphertexts are too. You should consider this to be just a simple coincidence, because we will soon encounter schemes in which keys, plaintexts, and ciphertexts are strings of different sizes.
The specific KeyGen, Enc, and Dec algorithms for one-time pad are given below:

**Construction 1.1 (One-time pad)**

$$
\def\arraystretch{1.5} 
\begin{array}{|lll|} \hline 
\underline{\text{KeyGen}:} & \underline{\text{Enc (}k,m \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}):} & \underline{\text{Dec (}k,c \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}):}\\
\quad k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \quad {\text{return } k \oplus m} &
\quad {\text{return } k \oplus c} \\
\quad \text{return } k \\ \hline 
\end{array}
$$

Recall that “$k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$” means to sample $k$ uniformly from the set of $\lambda$-bit strings. This uniform choice of key is the only randomness in all of the one-time pad algorithms. As we will see, all of its security stems from this choice of using the uniform distribution; keys that are chosen differently do not provide equivalent security.

**Example**
*Encrypting the following 20-bit plaintext $m$ under the 20-bit key $k$ using OTP results in the ciphertext $c$ below:*

$$
\begin{array}{lll} 
& \textcolor{brown}{11101111101111100011} & (m) \\
\oplus & \textcolor{brown}{00011001110000111101} & (k) \\
\hline & \textcolor{brown}{11110110011111011110} & (c=\operatorname{Enc}(k, m))
\end{array}
$$

*Decrypting the following ciphertext $c$ using the key $k$ results in the plaintext $m$ below:*

$$
\begin{array}{lll} 
& \textcolor{brown}{00001001011110010000} & (c) \\
\oplus & \textcolor{brown}{10010011101011100010} & (k) \\
\hline & \textcolor{brown}{10011010110101110010} & (m=\operatorname{Dec}(k, c))
\end{array}
$$

Note that Enc and Dec are essentially the same algorithm (return the XOR of the two arguments). This results in some small level of convenience and symmetry when implementing one-time pad, but it is more of a coincidence than something truly fundamental about encryption (see Exercises 1.12 & 2.5). Later on you’ll see encryption schemes whose encryption & decryption algorithms look very different.

### Correctness

The first property of one-time pad that we should confirm is that the receiver does indeed recover the intended plaintext when decrypting the ciphertext. Without this property, the thought of using one-time pad for communication seems silly. Written mathematically:

**Claim 1.2**
*For all $k$, $m \in \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$, it is true that $\text{Dec}(k, \text{Enc}(k,m)) = m$.*

**Proof** 
This follows by substituting the definitions of OTP Enc and Dec, then applying the properties of XOR listed in Chapter 0.3. For all $k$, $m \in \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$, we have:

$$
\begin{aligned}
\operatorname{Dec}(k, \operatorname{Enc}(k, m)) &=\operatorname{Dec}(k, k \oplus m) \\
&=k \oplus(k \oplus m) \\
&=(k \oplus k) \oplus m \\
&=\textcolor{brown}{0}^{\lambda} \oplus m \\
&=m
\end{aligned}
$$

**Example**
*Encrypting the following plaintext $m$ under the key $k$ results in ciphertext $c$, as follows:*

$$
\begin{array}{lll} 
& \textcolor{brown}{00110100110110001111} & (m) \\
\oplus & \textcolor{brown}{11101010011010001101} & (k) \\
\hline & \textcolor{brown}{11101010011010001101} & (c)
\end{array}
$$

*Decrypting $c$ using the same key $k$ results in the original $m$:*

$$
\begin{array}{lll} 
& \textcolor{brown}{11011110101100000010} & (c) \\
\oplus & \textcolor{brown}{11101010011010001101} & (k) \\
\hline & \textcolor{brown}{00110100110110001111} & (m)
\end{array}
$$

### Security

Suppose Alice and Bob are using one-time pad but are concerned that an attacker sees their ciphertext. They can’t presume what an attacker will do after seeing the ciphertext. But they would like to say something like, *“because of the specific way the ciphertext was generated, it doesn’t reveal any information about the plaintext to the attacker, no matter what the attacker does with the ciphertext.”*

We must first precisely specify how the ciphertext is generated. The Enc algorithm already describes the process, but it is written from the point of view of Alice and Bob. When talking about security, we have to think about what Alice and Bob do, but from the eavesdropper’s point of view! From Eve’s point of view, Alice uses a key that was chosen in a specific way (uniformly at random), she encrypts a plaintext with that key using OTP, and finally reveals only the resulting ciphertext (and not the key) to Eve.

More formally, from Eve’s perspective, seeing a ciphertext corresponds to receiving an output from the following algorithm:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline 
\underline{\text{EAVESDROP}(m \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}):} \\
\quad k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}  \\ 
\quad c:= k \oplus m \\ 
\quad \text{return } c \\ \hline 
\end{array}
$$

It’s crucial that you appreciate what this eavesdrop algorithm represents. It is meant to describe **not what the attacker *does***, but rather the process (carried out by Alice and Bob!) that produces **what the attacker *sees***. We always treat the attacker as some (unspecified) process that receives output from this eavesdrop algorithm. Our goal is to say something like “the output of EAVESDROP doesn’t reveal the input *m*.”

EAVESDROP is a *randomized* algorithm—remember that “$k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}$” means to sample $k$ from the uniform distribution on $\lambda$-bit strings. If you call eavesdrop several times, even on the same input, you are likely to get different outputs. Instead of thinking of “EAVESDROP($m$)” as a single string, you should think of it as a *probability distribution* over strings. Each time you call EAVESDROP($m$), you see a **sample** from that distribution.

**Example**
*Let’s take $\lambda = 3$ and work out by hand the distributions EAVESDROP($\textcolor{brown}{010}$) and EAVESDROP($\textcolor{brown}{111}$). In each case eavesdrop chooses a value of $k$ uniformly in $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^3$ — each of the possible values with probability 1/8. For each possible choice of $k$, we can compute what the output of EAVESDROP ($c$) will be:*

$$
\def\arraystretch{1.5} 
\begin{array}{c}
EAVESDROP(\textcolor{brown}{010}): \\ \hline 
\begin{array}{ccc}
\underline{Pr} & \underline{k} & \underline{output\ c = k \oplus \textcolor{brown}{010}} \\
\frac{1}{8} & \textcolor{brown}{000} & \textcolor{brown}{010} \\
\frac{1}{8} & \textcolor{brown}{001} & \textcolor{brown}{011} \\
\frac{1}{8} & \textcolor{brown}{010} & \textcolor{brown}{000} \\
\frac{1}{8} & \textcolor{brown}{011} & \textcolor{brown}{001} \\
\frac{1}{8} & \textcolor{brown}{100} & \textcolor{brown}{110} \\
\frac{1}{8} & \textcolor{brown}{101} & \textcolor{brown}{111} \\
\frac{1}{8} & \textcolor{brown}{110} & \textcolor{brown}{100} \\
\frac{1}{8} & \textcolor{brown}{111} & \textcolor{brown}{101} \\
\end{array}
\end{array}
\qquad
\begin{array}{c}
EAVESDROP(\textcolor{brown}{111}): \\ \hline 
\begin{array}{ccc}
\underline{Pr} & \underline{k} & \underline{output\ c = k \oplus \textcolor{brown}{111}} \\
\frac{1}{8} & \textcolor{brown}{000} & \textcolor{brown}{111} \\
\frac{1}{8} & \textcolor{brown}{001} & \textcolor{brown}{110} \\
\frac{1}{8} & \textcolor{brown}{010} & \textcolor{brown}{101} \\
\frac{1}{8} & \textcolor{brown}{011} & \textcolor{brown}{100} \\
\frac{1}{8} & \textcolor{brown}{100} & \textcolor{brown}{011} \\
\frac{1}{8} & \textcolor{brown}{101} & \textcolor{brown}{010} \\
\frac{1}{8} & \textcolor{brown}{110} & \textcolor{brown}{001} \\
\frac{1}{8} & \textcolor{brown}{111} & \textcolor{brown}{000} \\
\end{array}
\end{array}
$$

*So the distribution EAVESDROP($\textcolor{brown}{010}$) assigns probabilty 1/8 to $\textcolor{brown}{010}$, probability 1/8 to $\textcolor{brown}{011}$, and so on.*

In this example, notice how every string in $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^3$ appears exactly once in the $c$ column of EAVESDROP($\textcolor{brown}{010}$). This means that eavesdrop assigns probability 1/8 to every string in $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^3$, which is just another way of saying that the distribution is the *uniform distribution* on $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^3$. The same can be said about the distribution EAVESDROP($\textcolor{brown}{111}$), too. Both distributions are just the uniform distribution in disguise!

There is nothing special about $\textcolor{brown}{010}$ or $\textcolor{brown}{111}$ in these examples. For any $\lambda$ and any $m \in \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$, the distribution EAVESDROP($m$) is the uniform distribution over $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$.

**Claim 1.3**
*For every $m \in \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$, the distribution EAVESDROP($m$) is the uniform distribution on $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$. Hence, for all $m,m' \in \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$, the distributions EAVESDROP($m$) and EAVESDROP($m'$) are identical.*

**Proof**
 Arbitrarily $m, c \in \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$. We will calculate the probability that EAVESDROP($m$)  produces output $c$. That event happens only when

$$
c = k \oplus m \Longleftrightarrow k = m \oplus c.
$$

The equivalence follows from the properties of XOR given in Section 0.3. That is,

$$
\text{Pr[EAVESDROP}(m) = c] = \text{Pr}[k = m \oplus c].
$$

where the probability is over uniform choice of $k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$.

We are considering a specific choice for $m$ and $c$, so there is only one value of $k$ that makes $k = m \oplus c$ true (causes $m$ to encrypt to $c$), and that value is exactly $m \oplus c$. Since $k$ is chosen *uniformly* from $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$, the probability of choosing the particular value $k = m \oplus c$ is $1/2^{\lambda}$.

In summary, for every $m$ and $c$, the probability that EAVESDROP($m$) outputs $c$ is exactly $1/2^{\lambda}$. This means that the output of EAVESDROP($m$), for any $m$, follows the uniform distribution.

One way to interpret this statement of security in more down-to-earth terms:

>If an attacker sees a *single* ciphertext, encrypted with one-time pad, where the key is chosen uniformly and kept secret from the attacker, then the ciphertext appears uniformly distributed.

Why is this significant? Taking the eavesdropper’s point of view, suppose someone chooses a plaintext $m$ and you get to see the resulting ciphertext—a sample from the distribution EAVESDROP($m$). But this is a distribution that you can sample from yourself, even if you don’t know $m$! You could have chosen a totally different $m'$ and run EAVESDROP($m$)in your imagination, and this would have produced the same distribution as EAVESDROP($m$). The “real” ciphertext that you see *doesn’t carry any information about* $m$ if it is possible to sample from the same distribution without even knowing $m$!

### Discussion

 - **Isn’t there a paradox?** Claim 1.2 says that $c$ can always be decrypted to get $m$, but Claim 1.3 says that c contains no information about $m$! The answer to this riddle is that Claim 1.2 talks about what can be done with knowledge of the key $k$ (Alice & Bob’s perspective). Claim 1.3 talks about the output distribution of the eavesdrop algorithm, which doesn’t include k (Eve’s perspective). In short, if you know $k$, then you can decrypt $c$ to obtain $m$, if you don’t know $k$, then $c$ carries no information about $m$ (in fact, it looks uniformly distributed). This is because $m$, $c$, $k$ are all *correlated* in a delicate way.[^9]

[^9]: This correlation is explored further in Chapter 3.

 - **Isn’t there another paradox?** Claim 1.3 says that the output of EAVESDROP($m$) doesn’t depend on $m$, but we can see the eavesdrop algorithm literally using its argument $m$ right there in the last line! The answer to this riddle is perhaps best illustrated by the previous illustrations of the EAVESDROP($\textcolor{brown}{010}$) and EAVESDROP($\textcolor{brown}{111}$) distributions. The two tables of values are indeed different (so the choice of $m \in \{\textcolor{brown}{010}, \textcolor{brown}{111}\}$ clearly has some effect), but they represent the *same probability distribution* (since order doesn’t matter). Claim 1.3 considers only the resulting probability distribution.
 - You probably think about security in terms of a concrete “goal” for the attacker: recover the key, recover the plaintext, etc. Claim 1.3 doesn’t really refer to attackers in that way, and it certainly doesn’t specify a goal. Rather, we are thinking about security by comparing to some hypothetical “ideal” world. I would be satisfied if the attacker sees only a source of uniform bits, because in this hypothetical world there are no keys and no plaintexts to recover! Claim 1.3 says that when we actually use OTP, it looks just like this hypothetical world, from the attacker’s point of view. If we imagine any “goal” at all for the attacker in this kind of reasoning, it’s to detect that ciphertexts don’t follow a uniform distribution. By showing that the attacker can’t even achieve this modest goal, it shows that the attacker couldn’t possibly achieve other, more natural, goals like key recovery and plaintext recovery.

### Limitations

One-time pad is incredibly limited in practice. Most notably:
 - Its keys are as long as the plaintexts they encrypt. This is basically unavoidable (see Exercise 2.11) and leads to a kind of chicken-and-egg dilemma in practice: If two users want to privately convey a $\lambda$-bit message, they first need to privately agree on a $\lambda$-bit string.
 - A key can be used to encrypt only one plaintext (hence, “one-time” pad); see Exercise 1.6. Indeed, we can see that the eavesdrop subroutine in Claim 1.3 provides no way for a caller to guarantee that two plaintexts are encrypted with the same key, so it is not clear how to use Claim 1.3 to argue about what happens in one-time pad when keys are intentionally reused in this way.

Despite these limitations, one-time pad illustrates fundamental ideas that appear in most forms of encryption in this course.

### Exercises

 1.1. The one-time pad encryption of plaintext $\textcolor{brown}{\texttt{mario}}$ (when converted from ASCII to binary in the standard way) under key $k$ is:

$$
\textcolor{brown}{1000010000000111010101000001110000011101}.
$$

What is the one-time pad encryption of $\textcolor{brown}{\texttt{luigi}}$ under the same key?

1.2. Alice is using one-time pad and notices that when her key is the all-zeroes string $k = \textcolor{brown}{0}^{\lambda}$, then Enc$(k,m) = m$ and her message is sent in the clear! To avoid this problem, she decides to modify KeyGen to exclude the all-zeroes key. She modifies KeyGen to choose a key uniformly from $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \ \{\textcolor{brown}{0}\}^{\lambda}$, the set of all $\lambda$-bit strings except $\textcolor{brown}{0}^{\lambda}$. In this way, she guarantees that her plaintext is never sent in the clear.

Is it still true that the eavesdropper’s ciphertext distribution is uniformly distributed on $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$? Justify your answer.

1.3. When Alice encrypts the key k itself using one-time pad, the ciphertext will always be the all-zeroes string! So if an eavesdropper sees the all-zeroes ciphertext, she learns that Alice encrypted the key itself. Does this contradict Claim 1.3? Why or why not?

1.4. What is so special about defining OTP using the XOR operation? Suppose we use the bitwise-AND operation (which we will write as ‘&’) and define a variant of OTP as follows:

$$
\def\arraystretch{1.5} 
\begin{array}{|ll|} \hline 
\underline{\text{KeyGen:}} & \underline{\text{Enc (}k,m \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}):}\\
\quad k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \quad {\text{return } \colorbox{yellow}{k \& m}} \\
\quad \text{return } k \\ \hline
\end{array}
$$

Is this still a good choice for encryption? Why / why not?

1.5. Describe the aw in this argument:

> Consider the following attack against one-time pad: upon seeing a ciphertext $c$, the eavesdropper tries every candidate key $k \in \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$ until she has found the one that was used, at which point she outputs the plaintext $m$. This contradicts the argument in Section 1.2 that the eavesdropper can obtain no information about $m$ by seeing the ciphertext.

Suppose Alice encrypts two plaintexts $m$ and $m'$ using one-time pad with the same key $k$. What information about $m$ and $m'$ is leaked to an eavesdropper by doing this (assume the eavesdropper knows that Alice has reused $k$)? Be as specific as you can!

1.7. You (Eve) have intercepted two ciphertexts:

$$
c_1 = \textcolor{brown}{1111100101111001110011000001011110000110}
$$

$$
c_2 = \textcolor{brown}{1111101001100111110111010000100110001000}
$$

You know that both are OTP ciphertexts, encrypted with the *same key*. You know that **either** $c_1$ is an encryption of $\textcolor{brown}{\texttt{alpha}}$ and $c_2$ is an encryption of $\textcolor{brown}{\texttt{bravo}}$ **or** $c_1$ is an encryption of $\textcolor{brown}{\texttt{delta}}$ and $c_2$ is an encryption of $\textcolor{brown}{\texttt{gamma}}$ (all converted to binary from ascii in the standard way). 
Which of these two possibilities is correct, and why? What was the key $k$?

1.8. A **known-plaintext attack** refers to a situation where an eavesdropper sees a ciphertext $c =$ Enc$(k,m)$ and also learns/knows what plaintext $m$ was used to generate $c$.
	(a) Show that a known-plaintext attack on OTP results in the attacker learning the key $k$.
	(b) Can OTP be secure if it allows an attacker to recover the encryption key? Is this a contradiction to the security we showed for OTP? Explain.

1.9. Suppose we modify the subroutine discussed in Claim 1.3 so that it also returns $k$:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\underline{\text { EAVESDROP }^{\prime}\left(m \in\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\right) :} \\
\quad k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad c:=k \oplus m \\
\quad \text {return }(\colorbox{yellow}{k}, c) \\ \hline
\end{array} \ .
$$

Is it still true that for every $m$, the output of EAVESDROP$'(m)$ is distributed uniformly in $(\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda})^2$? Or is the output distribution different for different choice of $m$?

1.10. In this problem we discuss the security of performing one-time pad encryption twice:
(a) Consider the following subroutine that models the result of applying one-time pad encryption with two *independent* keys:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\underline{\text { EAVESDROP }^{\prime}\left(m \in\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\right) :} \\
\quad k_1 \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad k_2 \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad c:=k_2 \oplus (k_1 \oplus m) \\
\quad \text {return } c \\ \hline
\end{array} \ .
$$

Show that the output of this subroutine is uniformly distributed in $\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}$.
(b) What security is provided by performing one-time pad encryption twice with the *same key*?

1.11. We mentioned that one-time pad keys can be used to encrypt only one plaintext, and how this was reflected in the eavesdrop subroutine of Claim 1.3. Is there a similar restriction about re-using *plaintexts* in OTP (but with independently random keys for different ciphertexts)? If an eavesdropper *knows* that the same plaintext is encrypted twice (but doesn’t know what the plaintext is), can she learn anything? Does Claim 1.3 have anything to say about a situation where the same plaintext is encrypted more than once?

1.12. There is nothing exclusively special about strings and XOR in OTP. We can get the same properties using integers mod $n$ and addition mod $n$.
This problem considers a variant of one-time pad, in which the keys, plaintexts, and ciphertexts are all elements of $\mathbb{Z}_n$ instead of $\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}$.
(a) What is the decryption algorithm that corresponds to the following encryption algorithm?

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\underline{\text { Enc }(k,m \in\mathbb{Z}_n):} \\
\quad \text{return } (k+m) \% n \\ \hline
\end{array} \ .
$$

(b) Show that the output of the following subroutine is uniformly distributed in $\mathbb{Z}_n$:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\underline{\text { EAVESDROP }^{\prime}\left(m \in \mathbb{Z}_n\right) :} \\
\quad k \leftarrow \mathbb{Z}_n \\
\quad c:= (k + m) \% n \\
\quad \text {return }c \\ \hline
\end{array} \ .
$$

(c ) It’s not just the distribution of keys that is important. The way that the key is combined with the plaintext is also important. Show that the output of the following subroutine is **not** necessarily uniformly distributed in $\mathbb{Z}_n$:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\underline{\text { EAVESDROP }^{\prime}\left(m \in \mathbb{Z}_n\right) :}\\
\quad k \leftarrow \mathbb{Z}_n \\
\quad c:= (k \cdot m) \% n \\
\quad \text {return }c \\ \hline
\end{array} \ .
$$



# 2. The Basics of Provable Security

Edgar Allan Poe was not only an author, but also a cryptography enthusiast. He once wrote, in a discussion on the state of the art in cryptography:[^10]

[^10]: Edgar Allan Poe, “A Few Words on Secret Writing,” *Graham’s Magazine*, July 1841, v19.

> “Human ingenuity cannot concoct a cipher which human ingenuity cannot resolve.”

This was an accurate assessment of the cryptography that existed in 1841. Whenever someone would come up with an encryption method, someone else would inevitably find a way to break it, and the cat-and-mouse game would repeat again and again.

Modern 21st-century cryptography, however, is different. This book will introduce you to many schemes whose security we can prove in a very specific sense. The codemakers *can* win against the code-breakers.

It’s only possible to *prove* things about security by having *formal definitions* of what it means to be “secure.” This chapter is about the fundamental skills that revolve around security definitions: how to write them, how to understand & interpret them, how to prove security using the *hybrid technique*, and how to demonstrate insecurity using attacks against the security definition.

## 2.1 How to Write a Security Definition

So far the only form of cryptography we’ve seen is one-time pad, so our discussion of security has been rather specific to one-time pad. It would be preferable to have a vocabulary to talk about security in a more general sense, so that we can ask whether *any* encryption scheme is secure.

In this section, we’ll develop two security definitions for encryption.

### What *Doesn’t* Go Into a Security Definition

A security definition should give guarantees about what can happen to a system in the presence of an attacker. But not all important properties of a system refer to an attacker. For encryption specifically:

 - We don’t reference any attacker when we say that the Enc algorithm takes two arguments (a key and a plaintext), or that the KeyGen algorithm takes no arguments. Specifying the types of inputs/outputs (*i.e.*, the “function signature”) of the various algorithms is therefore not a statement about security. We call these properties the **syntax** of the scheme.
 - Even if there is no attacker, it’s still important that decryption is an inverse of encryption. This is not a security property of the encryption scheme. Instead, we call it a **correctness** property.

Below are the generic definitions for syntax and correctness of symmetric-key encryption:

**Definition 2.1 (Encryption syntax)**
*A **symmetric-key encryption (SKE) scheme** consists of the following algorithms:*

 - KeyGen: *a randomized algorithm that outputs a **key** $k \in \mathcal{K}$*.
 - Enc: *a (possibly randomized) algorithm that takes a key $k \in \mathcal{K}$ and **plaintext** $m \in \mathcal{M}$ as input, and outputs a **ciphertext** $c \in \mathcal{C}$*.
 - Dec: *a deterministic algorithm that takes a key $k \in \mathcal{K}$ and ciphertext $c \in \mathcal{C}$ as input, and outputs a plaintext $m \in \mathcal{M}$*.

*We call $\mathcal{K}$ the **key space**, $\mathcal{M}$ the **message space**, and $\mathcal{C}$ the **ciphertext space** of the scheme. Sometimes we refer to the entire scheme (the collection of all three algorithms) by a single variable $\Sigma$. When we do so, we write* $\Sigma$.KeyGen, $\Sigma$.Enc, $\Sigma$.Dec, $\Sigma.\mathcal{K}$, $\Sigma.\mathcal{M}$, *and* $\Sigma.\mathcal{C}$ *to refer to its components.*

**Definition 2.2 (SKE correctness)**
*An encryption scheme $\Sigma$ satisfies **correctness** if for all $k \in \Sigma.\mathcal{K}$ and all $m \in \Sigma.\mathcal{M}$,*

$$
\operatorname{Pr} \left[\Sigma . \operatorname{Dec}(k, \Sigma . \operatorname{Enc}(k, m))=m\right]=1.
$$

The definition is written in terms of a probability because Enc is allowed to be a randomized algorithm. In other words, decrypting a ciphertext with the same key that was used for encryption must *always* result in the original plaintext.

**Example**
*An encryption scheme can have the appropriate syntax but still have degenerate behavior like Enc$(k,m) = \textcolor{brown}{0}^{\lambda}$ (i.e., every plaintext is “encrypted” to $\textcolor{brown}{0}^{\lambda}$). Such a scheme would not satisfy the correctness property.*
*A different scheme defined by* Enc($k,m) = m$ (i.e., *the “ciphertext” is always equal to the plaintext itself) and* Dec($k, c) = c$ *does satisfy the correctness property, but would not satisfy any reasonable security property.*

### “Real-vs-Random” Style of Security Definition

Let’s try to make a security definition that formalizes the following intuitive idea:

> *“an encryption scheme is a good one if its ciphertexts look like random junk to an attacker.”*

Security definitions always consider **the attacker’s view** of the system. What is the “interface” that Alice & Bob expose to the attacker by their use of the cryptography, and does that particular interface benefit the attacker?
In this example, we’re considering a scenario where the attacker gets to observe ciphertexts. How *exactly* are these ciphertexts generated? What are the inputs to Enc (key and plaintext), and how are they chosen?

 - **Key:** It’s hard to imagine any kind of useful security if the attacker knows the key. Hence, we consider that the key is kept secret from the attacker. Of course, the key is generated according to the KeyGen algorithm of the scheme.
At this point in the course, we consider encryption schemes where the key is used to encrypt only one plaintext. Somehow this restriction must be captured in our security definition. Later, we will consider security definitions that consider a key that is used to encrypt many things.
 - **Plaintext:** It turns out to be useful to consider that the attacker actually *chooses* the plaintexts. This a “pessimistic” choice, since it gives much power to the attacker. However, if the encryption scheme is indeed secure when the attacker chooses the plaintexts, then it’s also secure in more realistic scenarios where the attacker has some uncertainty about the plaintexts.

These clarifications allow us to fill in more specifics about our informal idea of security:

> *“an encryption scheme is a good one if its ciphertexts look like random junk to an attacker $\ldots$ when each key is secret and used to encrypt only one plaintext, even when the attacker chooses the plaintexts.”

A concise way to express all of these details is to consider **the attacker as a calling program** to the following subroutine:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\underline{\mathrm{CTXT}\left(m \in \sum . \mathcal{M}\right):} \\
\quad k \leftarrow \Sigma . \text { KeyGen } \\ 
\quad c:=\Sigma . \text { Enc }(k, m)\\
\quad \operatorname{return} c \\ \hline
\end{array} \ .
$$

A calling program can choose the argument to the subroutine (in this case, a plaintext), and see *only* the resulting return value (in this case, a ciphertext). The calling program *can’t* see values of privately-scoped variables (like $k$ in this case). If the calling program makes many calls to the subroutine, a fresh key $k$ is chosen each time.
The interaction between an attacker (calling program) and this CTXT subroutine appears to capture the relevant scenario. We would like to say that the outputs from the CTXT subroutine are uniformly distributed. A convenient way of expressing this property is to say that this CTXT subroutine should have the same effect on *every* calling program as a CTXT subroutine that (explicitly) samples its output uniformly.

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\underline{\mathrm{CTXT}\left(m \in \sum . \mathcal{M}\right):} \\
\quad k \leftarrow \Sigma . \text { KeyGen } \\ 
\quad c:=\Sigma . \text { Enc }(k, m)\\
\quad \operatorname{return} c \\ \hline
\end{array}
\quad \text{vs} \quad
\begin{array}{|l|} \hline
\underline{\mathrm{CTXT}\left(m \in \sum . \mathcal{M}\right):} \\
\quad c \leftarrow \Sigma . \mathcal{C} \\ 
\quad \operatorname{return} c \\ \hline
\end{array}\ .
$$

Intuitively, no calling program should have any way of determining which of these two implementations is answering subroutine calls. As an analogy, one way of saying that “foo is a correct sorting algorithm” is to say that “no calling program would behave differently if foo were replaced by an implementation of mergesort.”

In summary, we can define security for encryption in the following way:

> *“an encryption scheme is a good one if, when you plug its* KeyGen *and* Enc *algorithms into the template of the CTXT subroutine above, the two implementations of CTXT induce identical behavior in every calling program.”*

In a few pages, we introduce formal notation and definitions for the concepts introduced here. In particular, both the calling program and subroutine can be randomized algorithms, so we should be careful about what we mean by “identical behavior.”

**Example**
*One-time pad is defined with* KeyGen *sampling k uniformly from $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$ and* Enc$(k,m) =
k \oplus m$. *It satisfies our new security property since, when we plug in this algorithms into the above template, we get the following two subroutine implementations:*

$$
\def\arraystretch{1.5} 
\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} & \textcolor{gray}{// \text{ KeyGen } of\  OTP} \\ 
\quad c:=k \oplus m & \textcolor{gray}{// \text{ Enc } of\  OTP}\\
\quad \operatorname{return} c \\ \hline
\end{array}
\quad vs. \quad
\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad c \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} & \textcolor{gray}{//\ \mathcal{C}\ of\  OTP} \\
\quad \operatorname{return} c \\ \hline
\end{array} \ ,
$$

*and these two implementations have the same effect on all calling programs.*

### “Left-vs-Right” Style of Security Definition

Here’s a different intuitive idea of security:

> *“an encryption scheme is a good one if encryptions of $m_L$ look like encryptions of $m_R$ to an attacker (for all possible $m_L$, $m_R$)”*

As above, we are considering a scenario where the attacker sees some ciphertext(s). These ciphertexts are generated with some key; where does that key come from? These ciphertexts encrypt either some $m_L$ or some $m_R$; where do $m_L$ and $m_R$ come from? We can answer these questions in a similar way as the previous example. Plaintexts $m_L$ and $m_R$ can be chosen by the attacker. The key is chosen according to KeyGen so that it remains secret from the attacker (and is used to generate only one ciphertext).

> *“an encryption scheme is a good one if encryptions of $m_L$ look like encryptions of $m_R$ to an attacker, when each key is secret and used to encrypt only one plaintext, even when the attacker chooses $m_L$ and $m_R$.”*

As before, we formalize this idea by imagining the attacker as a program that calls a particular interface. This time, the attacker will choose **two** plaintexts $m_L$ and $m_R$, and get a ciphertext in return.[^11] Depending on whether $m_L$ or $m_R$ is actually encrypted, those interfaces are implemented as follows:

[^11]: There may be other reasonable ways to formalize this intuitive idea of security. For example, we might choose to give the attacker two ciphertexts instead of one, and demand that the attacker can’t determine which of them encrypts $m_L$ and which encrypts $m_R$. See Exercise 2.15.

$$
\def\arraystretch{1.5} 
\begin{array}{|ll|} \hline
\underline{\text { EAVESDROP }\left(m_{L}, m_{R} \in \sum . \mathcal{M}\right):} \\
\quad k \leftarrow \Sigma . \text { KeyGen } \\
\quad c:=\sum . \text { Enc }\left(k, m_{L}\right) \\
\quad \operatorname{return} c \\ \hline
\end{array} \ ;
\qquad
\begin{array}{|ll|} \hline
\underline{\text { EAVESDROP }\left(m_{L}, m_{R} \in \sum . \mathcal{M}\right):} \\
\quad k \leftarrow \Sigma . \text { KeyGen } \\
\quad c:=\sum . \text { Enc }\left(k, m_{R}\right) \\
\quad \operatorname{return} c \\ \hline
\end{array} \ .
$$

Now the formal way to say that encryptions of $m_L$ “look like” encryptions of $m_R$ is:

> *“an encryption scheme is a good one if, when you plug its* KeyGen *and* Enc *algorithms into the template of the EAVESDROP subroutines above, the two implementations of EAVESDROP induce identical behavior in every calling program.”*

**Example**
*Does one-time pad satisfy this new security property? To find out, we plug in its algorithms to the above template, and obtain the following implementations:*

$$
\def\arraystretch{1.5} 
\begin{array}{|ll|} \hline
\underline{\mathrm{EAVEDROP}\left(m_L, m_R\right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} & \textcolor{gray}{// \text{ KeyGen } of\  OTP} \\ 
\quad c:=k \oplus m_L & \textcolor{gray}{// \text{ Enc } of\  OTP}\\
\quad \operatorname{return} c \\ \hline
\end{array}
\quad vs. \quad
\begin{array}{|ll|} \hline
\underline{\mathrm{EAVEDROP}\left(m_L, m_R\right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} & \textcolor{gray}{//\ \text{KeyGen } of\  OTP} \\
\quad c:=k \oplus m_R & \textcolor{gray}{// \text{ Enc } of\  OTP}\\
\quad \operatorname{return} c \\ \hline
\end{array} \ ,
$$

*If these two implementations have the same effect on all calling programs (and indeed they do), then we would say that OTP satisfies this security property.*

Is this a better/worse way to define security than the previous way? One security definition considers an attacker whose goal is to distinguish real ciphertexts from random values (real-vs-random paradigm), and the other considers an attacker whose goal is to distinguish real ciphertexts of two different plaintexts (left-vs-right paradigm). Is one “correct” and the other one “incorrect?” We save such discussion until later in the chapter.


## 2.2 Formalisms for Security Definitions

So far, we’ve defined security in terms of a single, self-contained subroutine, and imagined the attacker as a program that calls this subroutine. Later in the course we will need to generalize beyond a single subroutine, to a *collection* of subroutines that share common (private) state information. Staying with the software terminology, we call this collection a **library**:

**Definition 2.3 (Libraries)**
*A **library** $\mathcal{L}$ is a collection of subroutines and private/static variables. A library’s interface consists of the names, argument types, and output type of all of its subroutines (just like a Java interface). If a program $\mathcal{A}$ includes calls to subroutines in the interface of $\mathcal{L}$, then we write $\mathcal{A} \diamond \mathcal{L}$ to denote the result of **linking** $\mathcal{A}$ to $\mathcal{L}$ in the natural way (answering those subroutine calls using the implementation specified in $\mathcal{L}$). We write $\mathcal{A} \diamond \mathcal{L} \Rightarrow z$ to denote the event that program $\mathcal{A} \diamond \mathcal{L}$ outputs the value $z$.*

If $\mathcal{A}$ or $\mathcal{L}$ is a program that makes random choices, then the output of $\mathcal{A} \diamond \mathcal{L}$ is a random variable. It is often useful to consider probabilities like Pr[$\mathcal{A} \diamond \mathcal{L} \Rightarrow$ true].

**Example**
 Here is a familiar library:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline 
\begin{array}{c} 
\qquad \mathcal{L} \\ 
\end{array} \\ \hline 
\underline{\text{CTXT(}m):} \\
\quad k \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1} \}^{\lambda} \\
\quad c:= k \oplus m \\ 
\quad \text{return } c \\ \hline 
\end{array}
$$

*And here is one possible calling program:*

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline 
\begin{array}{c} 
\qquad \mathcal{A}: \\ 
\end{array} \\ \hline 
m \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1} \}^{\lambda} \\
c:= \text{CTXT} (m) \\ 
\text{return } m \overset{?}{=} c \\ \hline 
\end{array}
$$

*You can hopefully convince yourself that*

$$
\operatorname{Pr}[\mathcal{A} \diamond \mathcal{L} \Rightarrow \text { true }]=1 / 2^{\lambda} .
$$

*If this $\mathcal{A}$ is linked to a different library, its output probability may be different. If a different calling program is linked to this $\mathcal{L}$, the output probability may be different.*

**Example**
*A library can contain several subroutines and private variables that are kept static between subroutine calls. For example, here is a simple library that picks a string $s$ uniformly and allows the calling program to guess $s$.*

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline 
\begin{array}{c} 
\qquad \mathcal{L} \\ 
\end{array} \\ \hline 
s \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1} \}^{\lambda} \\
\underline{\text{RESET():}} \\
\quad  s \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1} \}^{\lambda} \\
\underline{\text{GUESS}( x \in \{\textcolor{brown}{0},\textcolor{brown}{1} \}^{\lambda}):} \\
\quad \text{return } x \overset{?}{=} s \\ \hline 
\end{array}
$$

*Our convention is that code outside of a subroutine (like the first line here) is run once at initialization time. Variables defined at initialization time (like $s$ here) are available in all subroutine scopes (but not to the calling program).*

### Interchangeability

The idea that “no calling program behaves differently in the presence of these two libraries” still makes sense even for libraries with several subroutines. Since this is such a common concept, we devote new notation to it:

**Definition 2.4 (Interchangeable)**

*Let $\mathcal{L}_{\text{left}}$ and $\mathcal{L}_{\text{right}}$ be two libraries that have the same interface. We say that $\mathcal{L}_{\text{left}}$ and $\mathcal{L}_{\text{right}}$ are **interchangeable**, and write $\mathcal{L}_{\text{left}} \equiv \mathcal{L}_{\text{right}}$, if for all programs A that output a boolean value,*

$$
\operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {left }} \Rightarrow \text { true }\right]=\operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {right }} \Rightarrow \text { true }\right].
$$

This definition considers calling programs that give boolean output. Imagine a calling program / attacker whose only goal is to distinguish two particular libraries (indeed, we often refer to the calling program as a **distinguisher**). A boolean output is enough for that task. You can think of the output bit as the calling program’s “guess” for which library the calling program thinks it is linked to.

The distinction between “calling program outputs $\texttt{true}$” and “calling program outputs $\texttt{false}$” is not significant. If two libraries don’t affect the calling program’s probability of outputting true, then they also don’t affect its probability of outputting $\texttt{false}$:

$$
\begin{array}{lrll}
& \operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {left }} \Rightarrow \texttt{ true }\right] &=& \operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {right }} \Rightarrow \texttt{ true }\right] \\
\Leftrightarrow & 1-\operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {left }} \Rightarrow \texttt{ true }\right] &=& 1-\operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {right }} \Rightarrow \texttt{ true }\right] \\
\Leftrightarrow & \operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {left }} \Rightarrow \texttt{ false }\right] &=& \operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {right }} \Rightarrow \texttt{ false }\right] .
\end{array}
$$

**Example** 
*Here are some very simple and straightforward ways that two libraries may be interchangeable. Hopefully it’s clear that each pair of libraries has identical behavior, and therefore identical effect on all calling programs.*
*Despite being very simple examples, each of these concepts shows up as a **building block** in a real security proof in this book.*

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\underline{\text{FOO}(x):} \\
\quad \text{if } x \text{ is even:} \\
\qquad \text{return } 0 \\
\quad \text{else if } x \text{ is odd:} \\
\qquad \text{return } 1 \\
\quad \text{else:} \\
\qquad \colorbox{yellow}{\text{return } -1} \\ \hline
\end{array} \equiv
\begin{array}{|l|} \hline
\underline{\text{FOO}(x):} \\
\quad \text{if } x \text{ is even:} \\
\qquad \text{return } 0 \\
\quad \text{else if } x \text{ is odd:} \\
\qquad \text{return } 1 \\
\quad \text{else:} \\
\qquad \colorbox{yellow}{\text{return }}  \infin  \\ \hline
\end{array} \quad
\begin{array}{l}
\textit{Their only difference happens in an}\\
\textit{unreachable block of code.}
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\underline{\text{FOO}(x):} \\
\quad \text{return BAR}(x, \colorbox{yellow}{x}) \\
\underline{\text{BAR}(a,b):} \\
\quad k \rightarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\
\quad \text{return } k \oplus a \\ \hline
\end{array} \equiv
\begin{array}{|l|} \hline
\underline{\text{FOO}(x):} \\
\quad \text{return BAR}(x, \colorbox{yellow}{\textcolor{brown}{0}}^{\lambda}) \\
\underline{\text{BAR}(a,b):} \\
\quad k \rightarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\
\quad \text{return } k \oplus a \\ \hline
\end{array} \quad
\begin{array}{l}
\textit{Their only difference is the value they assign}\\
\textit{to a variable that is never actually used.}
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\underline{\text{FOO}(x,n):} \\
\quad \text{for } i=1 \text{ to } \lambda: \\
\qquad \text{BAR}(x,i) \\ \hline
\end{array} \equiv
\begin{array}{|l|} \hline
\underline{\text{FOO}(x,n):} \\
\quad \text{for } i=1 \text{ to } n: \\
\qquad \text{BAR}(x,i) \\ 
\quad \text{for } i=n+1 \text{ to } \lambda: \\
\qquad \text{BAR}(x,i) \\ \hline
\end{array} \quad
\begin{array}{l}
\textit{Their only difference is that one library unrolls}\\
\textit{a loop that occurs in the other library.}
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\underline{\text{FOO}(x):} \\
\quad k \rightarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\
\quad y \rightarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\
\quad \text{return } k \oplus \colorbox{yellow}{y} \oplus \colorbox{yellow}{x} \\ \hline
\end{array} \equiv
\begin{array}{|l|} \hline
\underline{\text{FOO}(x):} \\
\quad k \rightarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\
\quad \text{return } k \oplus \colorbox{yellow}{\text{ BAR}(x)} \\
\underline{\text{BAR}(x):} \\
\quad y \rightarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\
\quad \text{return } y \oplus x \\ \hline
\end{array} \quad
\begin{array}{l}
\textit{Their only difference is that one library inlines}\\
\textit{a subroutine call that occurs in the other library.}
\end{array}
$$

**Example**
*Here are more simple examples of interchangeable libraries that involve randomness:*

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\underline{\text{FOO}():} \\
\quad x \rightarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\
\quad y \rightarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\
\quad \text{return } x \mid \mid y \\ \hline
\end{array} \equiv
\begin{array}{|l|} \hline
\underline{\text{FOO}():} \\
\quad z \rightarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{2\lambda} \\
\quad \text{return } z \\ \hline
\end{array} \quad
\begin{array}{l}
\textit{The uniform distribution over strings acts independently on}\\
\textit{different characters in the string (“||” refers to concatenation).}
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
k \rightarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\
\underline{\text{FOO}():} \\
\quad \text{return } k \oplus x \\ \hline
\end{array} \equiv
\begin{array}{|l|} \hline
\underline{\text{FOO}():} \\
\quad \text{if } k \text{ not defined:} \\
\qquad k \rightarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\
\quad \text{return } k \oplus x \\ \hline
\end{array} \quad
\begin{array}{l}
\textit{Sampling a value “eagerly” (as soon as possible) vs. sampling a value}\\
\textit{“lazily” (at the last possible moment before the value is needed). We assume} \\
\textit{that k is static/global across many calls to foo, and initially undefined.}
\end{array}
$$

### Formal Restatements of Previous Concepts

We can now re-state our security definitions from the previous section, using this new terminology.

Our “real-vs-random” style of security definition for encryption can be expressed as follows:

**Definition 2.5 (Uniform ctxts)**
*An encryption scheme $\Sigma$ has **one-time uniform ciphertexts** if:*

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\quad \qquad\mathcal{L}_{\text {ots\$-real }}^{\Sigma}\\ \hline
\underline{\operatorname{CTXT}\left(m \in \sum . \mathcal{M}\right):}\\
\quad k \leftarrow \Sigma . \text { KeyGen } \\
\quad c \leftarrow \Sigma . \operatorname{Enc}(k, m) \\
\quad \text {return } c \\ \hline
\end{array} \equiv
\begin{array}{|l|} \hline
\quad \qquad\mathcal{L}_{\text {ots\$-rand }}^{\Sigma}\\ \hline
\underline{\operatorname{CTXT}\left(m \in \sum . \mathcal{M}\right):}\\
\quad c \leftarrow \Sigma . \mathcal{C} \\
\quad \text {return } c \\ \hline
\end{array}
$$

In other words, if you fill in the specifics of $\Sigma$ (*i.e.*, the behavior of its KeyGen and Enc) into these two library “templates,” and you get two libraries that are interchangeable (*i.e.*, have the same effect on all calling programs), we will say that $\Sigma$ has one-time uniform ciphertexts.
Throughout this course, we will use the “\$” symbol to denote randomness (as in realvs-random).[^12]

[^12]: It is quite common in CS literature to use the “\$” symbol when referring to randomness. This stems from thinking of randomized algorithms as algorithms that “toss coins.” Hence, randomized algorithms need to have spare change (i.e., money) sitting around. By convention, randomness comes in US dollars.

Our “left-vs-right” style of security definition can be expressed as follows:

**Definition 2.6 (One-time secrecy)**
*An encryption scheme $\Sigma$ has **one-time secrecy** if:*

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad\qquad\qquad\mathcal{L}_{\text {ots-L }}^{\Sigma}\\ \hline
\underline{\operatorname{EAVESDROP}\left(m_L, m_R \in \sum . \mathcal{M}\right):}\\
\quad k \leftarrow \Sigma . \text { KeyGen } \\
\quad c \leftarrow \Sigma . \operatorname{Enc}(k, m_L) \\
\quad \text {return } c \\ \hline
\end{array} \equiv
\begin{array}{|l|} \hline
\qquad\qquad\qquad\mathcal{L}_{\text {ots-L }}^{\Sigma}\\ \hline
\underline{\operatorname{EAVESDROP}\left(m_L, m_R \in \sum . \mathcal{M}\right):}\\
\quad c \leftarrow \Sigma . \mathcal{C} \\
\quad \text {return } c \\ \hline
\end{array}
$$

Previously in Claim 1.3 we argued that one-time-pad ciphertexts follow the uniform distribution. This actually shows that OTP satisfies the uniform ciphertexts definition:

**Claim 2.7 (OTP rule)**
*One-time pad satisfies the one-time uniform ciphertexts property. In other words:*

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad\qquad\mathcal{L}_{\text {otp-real }}\\ \hline
\underline{\mathrm{EAVESDROP}\left(m \in \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \quad \textcolor{gray}{// OPT.\text{KeyGen }} \\ 
\quad \operatorname{return } k \oplus m \quad \textcolor{gray}{// OPT.\text{Enc}(k,m)}\\ \hline
\end{array}
\quad \equiv \quad
\begin{array}{|ll|} \hline
\qquad\qquad\qquad\mathcal{L}_{\text {otp-rand }}\\ \hline
\underline{\mathrm{EAVESDROP}\left(m \in \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \right):} \\
\quad c \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \quad \textcolor{gray}{// OPT.\mathcal{C}} \\ 
\quad \operatorname{return } c\\ \hline
\end{array}
$$

Because this property of OTP is quite useful throughout the course, I’ve given these two libraries special names (apart from $\mathcal{L}^{OTP}_{\text{ots}\varPhi-\text{real}}$ and $\mathcal{L}^{OTP}_{\text{ots}\varPhi-\text{rand}}$).

### Discussion, Pitfalls

It is a common pitfall to imagine the calling program $\mathcal{A}$ being *simultaneously* linked to both libraries, but this is not what the definition says. The definition of $\mathcal{L}_1 \equiv \mathcal{L}_2$ refers to two different executions: one where $\mathcal{A}$ is linked only to $\mathcal{L}_1$ for its entire lifetime, and one where $\mathcal{A}$ is linked only to $\mathcal{L}_2$ for its entire lifetime. There is never a time where some of $\mathcal{A}$’s subroutine calls are answered by $\mathcal{L_1}$ and others by $\mathcal{L}_2$. This is an especially important distinction when $\mathcal{A}$ makes several subroutine calls in a single execution.

Another common pitfall is confusion about the difference between the algorithms of an encryption scheme (e.g., what is shown in Construction 1.1) and the libraries used in a security definition (e.g., what is shown in Definition 2.6). The big difference is:

 - The algorithms of the scheme show a regular user’s view of things. For example, the Enc algorithm takes two inputs: a key and a plaintext. Is there any way of describing an algorithm that takes two arguments other than writing something like Construction 1.1?
 - The libraries capture the attacker’s view of of a particular scenario, where the users use the *cryptographic algorithms in a very specific way*. For example, when we talk about security of encryption, we don’t guarantee security when Alice lets the attacker choose her encryption key! But letting the attacker choose the plaintext is fine; we can guarantee security in that scenario. That’s why Definition 2.5 describes a subroutine that calls Enc on a plaintext that is chosen by the calling program, but on a key $k$ chosen by the library.
A security definition says that some task (*e.g.*, distinguishing ciphertexts from random junk) is impossible, when the attacker is allowed certain influence over the inputs to the algorithms (*e.g.*, full choice of plaintexts, but no influence over the key), and is allowed to see certain outputs from those algorithms (*e.g.*, ciphertexts).

It’s **wrong** to summarize one-time secrecy as: “I’m not allowed to choose what to encrypt, I have to ask the attacker to choose for me.” The correct interpretation is: “If I encrypt only one plaintext per key, then I am safe to encrypt things even if the attacker sees the resulting ciphertext and even if she has some influence or partial information on what I’m encrypting, because this is the situation captured in the one-time secrecy library.”

### Kerckhoffs’ Principle, Revisited

Kerckhoffs’ Principle says to assume that the attacker has complete knowledge of the algorithms being used. Assume that the choice of keys is the *only* thing unknown to the attacker. Let’s see how Kerckhoffs’ Principle is reflected in our formal security definitions. 

Suppose I write down the source code of two libraries, and your goal is to write an effective distinguisher. So you study the source code of the two libraries and write the best distinguisher that exists. It would be fair to say that your distinguisher “knows” what algorithms are used in the libraries, because it was designed based on the source code of these libraries. The definition of interchangeability considers *literally every* calling program, so it must also consider calling programs like yours that “know” what algorithms are being used.

However, there is an important distinction to make. If you know you might be linked to a library that executes the statement “$k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}$”, that doesn’t mean you know the actual *value* of k that was chosen at runtime. Our convention is that all variables within the library are privately scoped, and the calling program can learn about them only indirectly through subroutine outputs. In the library-distinguishing game, you are not allowed to pick a different calling program based on random choices that the library makes! After we settle on a calling program, we measure its effectiveness in terms of probabilities that take into account all possible outcomes of the random choices in the system.

In summary, the calling program “knows” what algorithms are being used (and how they are being used!) because the choice of the calling program is allowed to depend on the 2 specific libraries that we consider. The calling program “doesn’t know” things like secret keys because the choice of calling program isn’t allowed to depend on the outcome of random sampling done at runtime.

> **Kerckhos’ Principle, applied to our formal terminology:**
*Assume that the attacker knows every fact in the universe, except for:*
> 1. *which of the two possible libraries it is linked to in any particular execution, and*
> 2. *the random choices that the library will make during any particular execution (which are usually assigned to privately scoped variables within the library).*

## 2.3 How to Demonstrate Insecurity with Attacks

We always define security with respect to two libraries — or, if you like, two library *templates* that describe how to insert the algorithms of a cryptographic scheme into two libraries. If the two libraries that you get (after filling in the specifics of a particular scheme) are interchangeable, then we say that the scheme satisfies the security property. If we want to show that some scheme is *insecure*, we have to demonstrate **just one calling program** that behaves differently in the presence of those two libraries.

Let’s demonstrate this process with the following encryption scheme, which is like one-time pad but uses bitwise-AND instead of XOR:

**Construction 2.8**

$$
\def\arraystretch{1.5} 
\begin{array}{|rll|} \hline
\mathcal{K}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \underline{\text { KeyGen: }} & \underline{\operatorname{Enc}(k, m):} \\
\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \quad {k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}} & \quad \text {return } k \& m \quad \textcolor{gray}{//\ bitwise-AND} \\
\mathcal{C}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \quad \text {return } k \\ \hline
\end{array}
$$

I haven’t shown the Dec algorithm, because in fact there is no way to write one that satisfies the correctness requirement. But let’s pretend we haven’t noticed that yet, and ask whether this encryption scheme satisfies the two security properties defined previously.

**Claim 2.9**
 *Construction 2.8 does **not** have one-time uniform ciphertexts (Denition 2.5).*

**Proof**
To see whether Construction 2.8 satisfies uniform one-time ciphertexts, we have to plug in its algorithms into the two libraries of Definition 2.5 and see whether the resulting libraries are interchangeable. We’re considering the following two libraries:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad\quad\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-real }}\\ \hline
\underline{\mathrm{CTXT}\left(m \in \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \quad \textcolor{gray}{// \Sigma.\text{KeyGen }} \\ 
\quad c := k \& m \quad\ \ \textcolor{gray}{// \Sigma.\text{Enc}}\\ 
\quad \operatorname{return } c\\ \hline
\end{array}
\quad 
\begin{array}{|ll|} \hline
\qquad\qquad\quad\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-rand }}\\ \hline
\underline{\mathrm{CTXT}\left(m \in \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \right):} \\
\quad c \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \quad \textcolor{gray}{// \Sigma.\mathcal{C}} \\ 
\quad \operatorname{return } c\\ \hline
\end{array}
$$

To show that these two libraries are **not** interchangeable, we need to write a calling program that behaves differently in their presence. The calling program should make one or more calls to the CTXT subroutine. That means it needs to choose the input $m$ that it passes, and it must make some conclusion (about which of the two libraries it is linked to) based on the return value that it gets. What $m$ should the calling program choose as input to CTXT? What should the calling program look for in the return values?

There are many valid ways to write a good calling program, and maybe you can think of several. One good approach is to observe that bitwise-AND with $k$ can never “turn a $\textcolor{brown}{0}$ into a $\textcolor{brown}{1}$.” So perhaps the calling program should choose $m$ to consist of all $\textcolor{brown}{0}$s. When $m = \textcolor{brown}{0}^{\lambda}$, the $\mathcal{L}_{\text {ots}\varPhi\text{-real }}$ library will always return all zeroes, but the $\mathcal{L}_{\text {ots}\varPhi\text{-rand }}$ library may return strings with both $\textcolor{brown}{0}$s and $\textcolor{brown}{1}$s.

We can formalize this idea with the following calling program:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\quad\mathcal{A}:\\ \hline
c := \mathrm{CTXT}\left(\textcolor{brown}{0}^{\lambda}\right): \\
\operatorname{return } c \overset{?}{=} \textcolor{brown}{0}^{\lambda}\\ \hline
\end{array}
$$

Next, let’s ensure that this calling program behaves differently when linked to each of these two libraries.

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\quad\mathcal{A}:\\ \hline
c := \mathrm{CTXT}\left(\textcolor{brown}{0}^{\lambda}\right): \\
\operatorname{return } c \overset{?}{=} \textcolor{brown}{0}^{\lambda}\\ \hline
\end{array}
\quad \diamond \quad
\begin{array}{|l|} \hline
\quad\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-real }}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\ 
\quad c := k \& m \\ 
\quad \operatorname{return } c\\ \hline
\end{array}
$$

When $\mathcal{A}$ is linked to $\mathcal{L}_{\text {ots}\varPhi\text{-real }},\ c$ is computed as $k\& \textcolor{brown}{0}^{\lambda}$. No matter what k is, the result is always all-zeroes. Therefore, $\mathcal{A}$ will always return true.

In other words, Pr[$\mathcal{A} \diamond \mathcal{L}_{\text {ots}\varPhi\text{-real }} \Rightarrow$ true] $= 1.$

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\quad\mathcal{A}:\\ \hline
c := \mathrm{CTXT}\left(\textcolor{brown}{0}^{\lambda}\right): \\
\operatorname{return } c \overset{?}{=} \textcolor{brown}{0}^{\lambda}\\ \hline
\end{array}
\quad \diamond \quad
\begin{array}{|l|} \hline
\quad\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-rand }}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad c \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\ 
\quad \operatorname{return } c\\ \hline
\end{array}
$$

When $\mathcal{A}$ is linked to $\mathcal{L}_{\text {ots}\varPhi\text{-rand }},\ c$ is chosen uniformly from $\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$. The probability that $c$ then happens to be all-zeroes is $1/2^{\lambda}$.

In other words, P[$\mathcal{A} \diamond \mathcal{L}_{\text {ots}\varPhi\text{-real }} \Rightarrow$ true] $= 1/2^{\lambda}$.

Since these two probabilities are different, this shows that $\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-real }}  \neq \mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-rand }}$. In other words, the scheme does not satisfy this uniform ciphertexts property.

So far we have two security definitions. Does this encryption scheme satisfy one but not the other?

**Claim 2.10** 
*Construction 2.8 does **not** satisfy one-time secrecy (Definition 2.6).*

**Proof**
This claim refers to a different security definition, which involves two different libraries. When we plug in the details of Construction 2.8 into the libraries of Definition 2.6, we get the following:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad\qquad\mathcal{L}^{\Sigma}_{\text {ots-L}}\\ \hline
\underline{\mathrm{EAVESDROP}\left(m_L, m_R \right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \quad \textcolor{gray}{// \Sigma.\text{KeyGen }} \\ 
\quad c := k \&  m_L  \quad \textcolor{gray}{// \Sigma.\text{Enc}(k,m_L)} \\
\quad \operatorname{return } c \\ \hline
\end{array}
\quad \equiv \quad
\begin{array}{|ll|} \hline
\qquad\qquad\qquad\mathcal{L}^{\Sigma}_{\text {ots-R}}\\ \hline
\underline{\mathrm{EAVESDROP}\left(m_L, m_R \right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \quad \textcolor{gray}{// \Sigma.\text{KeyGen }} \\ 
\quad c := k \&  m_R  \quad \textcolor{gray}{// \Sigma.\text{Enc}(k,m_R)} \\
\quad \operatorname{return } c \\ \hline
\end{array}
$$

Now we need to write a calling program that behaves differently in the presence of these two libraries. We can use the same overall idea as last time, but not the same actual calling program, since these libraries provide a different interface. In this example, the calling program needs to call the EAVESDROP subroutine which takes *two* arguments $m_L$ and $m_R$. How should the calling program choose $m_L$ and $m_R$? Which two plaintexts have different looking ciphertexts?

A good approach is to choose $m_L$ to be all zeroes and $m_R$ to be all ones. We know from before that an all-zeroes plaintext always encrypts to an all-zeroes ciphertext, so the calling program can check for that condition. More formally, we can define the calling program:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad\quad\mathcal{A}:\\ \hline
c := \mathrm{EAVESDROP}\left(\textcolor{brown}{0}^{\lambda}, \textcolor{brown}{1}^{\lambda}\right) \\
\operatorname{return } c \overset{?}{=} \textcolor{brown}{0}^{\lambda}\\ \hline
\end{array}
$$

Next, we need to compute its output probabilities in the presence of the two libraries.

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad\quad\mathcal{A}:\\ \hline
c := \mathrm{EAVESDROP}\left(\colorbox{yellow}{\textcolor{brown}{0}}^{\lambda}, \textcolor{brown}{1}^{\lambda}\right) \\
\operatorname{return } c \overset{?}{=} \textcolor{brown}{0}^{\lambda}\\ \hline
\end{array}
\quad\diamond\quad
\begin{array}{|l|} \hline
\qquad\qquad\qquad\mathcal{L}^{\Sigma}_{\text {ots-L}}\\ \hline
\underline{\mathrm{EAVESDROP}\left(\colorbox{yellow}{m}_L, m_R \right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\ 
\quad c := k \&  \colorbox{yellow}{m}_L \\
\quad \operatorname{return } c \\ \hline
\end{array}
$$

When $\mathcal{A}$ is linked to $\mathcal{L}_{\text{ots-L}}$, $c$ is computed as an encryption of $m_L = \textcolor{brown}{0}^{\lambda}$. No matter what k is, the result is always all-zeroes. So,

$$
\text{Pr[}\mathcal{A} \diamond \mathcal{L}_{\text{ots-L}} \Rightarrow \text{true}] = 1.
$$

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad\quad\mathcal{A}:\\ \hline
c := \mathrm{EAVESDROP}\left(\textcolor{brown}{0}^{\lambda}, \colorbox{yellow}{\textcolor{brown}{1}}^{\lambda}\right) \\
\operatorname{return } c \overset{?}{=} \textcolor{brown}{0}^{\lambda}\\ \hline
\end{array}
\quad\diamond\quad
\begin{array}{|l|} \hline
\qquad\qquad\qquad\mathcal{L}^{\Sigma}_{\text {ots-R}}\\ \hline
\underline{\mathrm{EAVESDROP}\left(m_L, \colorbox{yellow}{m}_R \right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\ 
\quad c := k \&  \colorbox{yellow}{m}_R \\
\quad \operatorname{return } c \\ \hline
\end{array}
$$

When $\mathcal{A}$ is linked to $\mathcal{L}_{\text {ots-R}}$, $c$ is
computed as an encryption of $m_R = \textcolor{brown}{1}^{\lambda}$. In other words, $c := k \& \textcolor{brown}{1}^{\lambda}$. But the bitwise-AND of any string $k$ with all $\textcolor{brown}{1}$s is just $k$ itself. So $c$ is just equal to $k$, which was chosen uniformly at random. The probability that a uniformly random $c$ happens to be all-zeroes is

$$
\text{Pr[}\mathcal{A} \diamond \mathcal{L}_{\text{ots-R}} \Rightarrow \text{true}] = 1/2^{\lambda}.
$$

Since these two probabilities are different, $\mathcal{L}^{\Sigma}_{\text{ots-L}} \neq \mathcal{L}^{\Sigma}_{\text{ots-R}}$ and the scheme does not have one-time secrecy.

## 2.4 How to Prove Security with The Hybrid Technique

We proved that one-time pad satisfies the uniform ciphertexts property (Claim 1.3) by carefully calculating certain probabilities. This will not be a sustainable strategy as things get more complicated later in the course. In this section we will introduce a technique for proving security properties, which usually avoids tedious probability calculations.

### Chaining Several Components

Before getting to a security proof, we introduce a convenient lemma. Consider a compound program like $\mathcal{A} \diamond \mathcal{L}_1 \diamond \mathcal{L}_2$. Our convention is that subroutine calls only happen from left to right across the $\diamond$ symbol, so in this example, $\mathcal{L}_1$ can make calls to subroutines in $\mathcal{L}_2$, but not vice-versa. Depending on the context, it can sometimes be convenient to
interpret $\mathcal{A} \diamond \mathcal{L}_1 \diamond \mathcal{L}_2$ as:

 - $(\mathcal{A} \diamond \mathcal{L}_1) \diamond \mathcal{L}_2$: a **compound calling program** linked to $\mathcal{L}_2$. After all, $\mathcal{A} \diamond \mathcal{L}_1$ is a program that makes calls to the interface of $\mathcal{L}_2$.
 - or: $\mathcal{A} \diamond (\mathcal{L}_1 \diamond \mathcal{L}_2)$: $\mathcal{A}$ linked to a **compound library**. After all, $\mathcal{A}$ is a program that makes calls to the interface of $(\mathcal{L}_1 \diamond \mathcal{L}_2)$.

The placement of the parentheses does not affect the functionality of the overall program, just like how splitting up a real program into different source files doesn’t affect its functionality.

In fact, every security proof in this book will have some intermediate steps that involve compound libraries. We will make heavy use of the following helpful result:

**Lemma 2.11 (Chaining)**
*If $\mathcal{L}_{\text{left}} \equiv \mathcal{L}_{\text{right}}$ then, for any library $\mathcal{L}^{\star}$, we have $\mathcal{L}^{\star} \diamond \mathcal{L}_{\text{left}} \equiv \mathcal{L}^{\star} \diamond \mathcal{L}_{\text{right}}$.

**Proof**
Note that we are comparing $\mathcal{L}^{\star} \diamond \mathcal{L}_{\text{left}}$ and $\mathcal{L}^{\star} \diamond \mathcal{L}_{\text{right}}$ as compound libraries. Hence we consider a calling program $\mathcal{A}$ that is linked to either $\mathcal{L}^{\star} \diamond \mathcal{L}_{\text{left}}$ or $\mathcal{L}^{\star} \diamond \mathcal{L}_{\text{right}}$.

Let $\mathcal{A}$ be such an arbitrary calling program. We must show that $\mathcal{A} \diamond\left(\mathcal{L}^{*} \diamond \mathcal{L}_{\text {left }}\right)$ and $\mathcal{A} \diamond\left(\mathcal{L}^{*} \diamond \mathcal{L}_{\text {right }}\right)$ have identical output distributions. As mentioned above, we can interpret $\mathcal{A} \diamond \mathcal{L}^{*} \diamond \mathcal{L}_{\text {left }}$ as a calling program $\mathcal{A}$ linked to the library $\mathcal{L}^{*} \diamond \mathcal{L}_{\text{left}}$, but also as a calling program $\mathcal{A} \diamond \mathcal{L}^{*}$ linked to the library $\mathcal{L}_{\text {left. }}$ Since $\mathcal{L}_{\text {left }} \equiv \mathcal{L}_{\text {right }},$ swapping $\mathcal{L}_{\text {left }}$ for $\mathcal{L}_{\text {right }}$ has no effect on the output of any calling program. In particular, it has no effect when the calling program happens to be the compound program $\mathcal{A} \diamond \mathcal{L}^{*}$. Hence we have:

$$
\begin{array}{rlr}
\operatorname{Pr}\left[\mathcal{A} \diamond\left(\mathcal{L}^{*} \diamond \mathcal{L}_{\text {left }}\right) \Rightarrow \text { true }\right] & =\operatorname{Pr}\left[\left(\mathcal{A} \diamond \mathcal{L}^{*}\right) \diamond \mathcal{L}_{\text {left }} \Rightarrow \text { true }\right] & \text { (change of perspective) } \\
& =\operatorname{Pr}\left[\left(\mathcal{A} \diamond \mathcal{L}^{*}\right) \diamond \mathcal{L}_{\text {right }} \Rightarrow \text { true }\right] & \text { (since } \left.\mathcal{L}_{\text {left }} \equiv \mathcal{L}_{\text {right }}\right) \\
& =\operatorname{Pr}\left[\mathcal{A} \diamond\left(\mathcal{L}^{*} \diamond \mathcal{L}_{\text {right }}\right) \Rightarrow\right. \text { true]. } & \text { (change of perspective) }
\end{array}
$$

Since $\mathcal{A}$ was arbitrary, we have proved the lemma.


### An Example Hybrid Proof

In this section we will prove something about the following scheme, which encrypts twice with OTP, using independent keys:

**Construction 2.12 (“Double OTP”)**

$$
\def\arraystretch{1.5} 
\begin{array}{|rlll|} \hline
\mathcal{K}=(\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda})^2 & \underline{\text { KeyGen: }} & \underline{\operatorname{Enc}((k_1, k_2),m):} & \underline{\operatorname{Dec}((k_1, k_2),c_2):} \\
\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \quad {k_1 \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}} & \quad c_1 := k_1 \oplus m & \quad c_1 := k_2 \oplus c_2 \\
\mathcal{C}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \quad{k_2 \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}}  & \quad c_2 := k_2 \oplus c_1 & \quad m := k_1 \oplus c_1 \\
& \quad \text {return } (k_1, k_2) & \quad \text {return } c_2 & \quad \text {return } m \\ \hline
\end{array}
$$

It would not be too hard to directly show that ciphertexts in this scheme are uniformly distributed, as we did for plain OTP. However, the new hybrid technique will allow us to leverage what we already know about OTP in an elegant way, and avoid any probability calculations.

**Claim 2.13** 
*Construction 2.12 has one-time uniform ciphertexts (Definition 2.6).*

**Proof** 
In terms of libraries, we must show that:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-real}}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\left.\begin{array}{l}
\quad k_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad k_2 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} 
\end{array}\right\} \textcolor{gray}{ \text{KeyGen }} \\ 
\left.\begin{array}{l}
\quad c_1 := k_1 \oplus  m \\
\quad c_2 := k_2 \oplus  c_1 \\
\quad \operatorname{return } c_2 \\
\end{array}\right\}
\textcolor{gray}{\text{Enc}} \\ \hline
\end{array}
\quad \equiv \quad
\begin{array}{|ll|} \hline
\quad\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-rand}}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad c \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\ 
\quad \operatorname{return } c \\ \hline
\end{array}
$$

Instead of directly comparing these two libraries, we will introduce some additional libraries $\mathcal{L}_{\text{hyb-1}}$, $\mathcal{L}_{\text{hyb-2}}$, $\mathcal{L}_{\text{hyb-3}}$, and show that:

$$
\mathcal{L}_{\text {ots}\varPhi\text{-real }}^{\Sigma} \equiv \mathcal{L}_{\mathrm{hyb}-1} \equiv \mathcal{L}_{\mathrm{hyb}-2} \equiv \mathcal{L}_{\mathrm{hyb}-3} \equiv \mathcal{L}_{\text {ots}\varPhi\text{-rand }}^{\Sigma}
$$

Since the $\equiv$ symbol is transitive, this will achieve our goal.

The intermediate libraries are called **hybrids**, since they will contain a mix of characteristics from the two “endpoints” of this sequence. These hybrids are chosen so that it is very easy to show that consecutive libraries in this sequence are interchangeable. The particular hybrids we have in mind here are:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-real}}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\begin{array}{l}
\quad k_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad k_2 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} 
\end{array} \\ 
\begin{array}{l}
\quad c_1 := k_1 \oplus  m \\
\quad c_2 := k_2 \oplus  c_1 \\
\quad \operatorname{return } c_2 \\
\end{array} \\ \hline
\end{array}
\quad \equiv \quad
\underbrace{\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad k_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad c_1 := k_1 \oplus m \\
\quad c_2 := \text{CTXT}' (c_1) \\ 
\quad \operatorname{return } c_2 \\ \hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\qquad\mathcal{L}_{\text {otp-real}}\\ \hline
\underline{\mathrm{CTXT}'\left(m'\right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \operatorname{return } k \oplus m' \\ \hline
\end{array}
}_{\mathcal{L}_{\text{hyb-1}}}
\quad \equiv \quad
\underbrace{\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad k_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad c_1 := k_1 \oplus m \\
\quad c_2 := \text{CTXT}' (c_1) \\ 
\quad \operatorname{return } c_2 \\ \hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\qquad\mathcal{L}_{\text {otp-rand}}\\ \hline
\underline{\mathrm{CTXT}'\left(m'\right):} \\
\quad c \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \operatorname{return } c \\ \hline
\end{array}
}_{\mathcal{L}_{\text{hyb-2}}}
\quad \equiv \quad
\underbrace{\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad k_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad c_1 := k_1 \oplus m \\
\quad c_2 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\ 
\quad \operatorname{return } c_2 \\ \hline
\end{array}
}_{\mathcal{L}_{\text{hyb-3}}}
\quad \equiv \quad
\begin{array}{|l|} \hline
\qquad\mathcal{L}^{\Sigma}_{\text {otp-rand}}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad c_2 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \operatorname{return } c_2 \\ \hline
\end{array}
$$

Next, we provide a justification for each “$\equiv$” in the expression above. For each pair of adjacent libraries, we highlight their differences below:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-real}}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\begin{array}{l}
\quad k_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \colorbox{yellow}{k}_2 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} 
\end{array} \\ 
\begin{array}{l}
\quad c_1 := k_1 \oplus  m \\
\quad c_2 := \colorbox{yellow}{k}_2 \oplus  c_1 \\
\quad \operatorname{return } c_2 \\
\end{array} \\ \hline
\end{array}
\quad \equiv \quad
\underbrace{\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad k_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad c_1 := k_1 \oplus m \\
\quad c_2 := \colorbox{yellow}{\text{CTXT}'} (c_1) \\ 
\quad \operatorname{return } c_2 \\ \hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\qquad\mathcal{L}_{\text {otp-real}}\\ \hline
\underline{\mathrm{CTXT}'\left(m'\right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \operatorname{return } k \oplus m' \\ \hline
\end{array}
}_{\mathcal{L}_{\text{hyb-1}}}
$$

The only difference between these two libraries is that the highlighted expressions have been factored out into a separate subroutine, and some variables have been renamed. In both libraries, $c_2$ is chosen as the XOR of $c_1$ and a uniformly chosen string. These differences make no effect on the calling program. Importantly, the subroutine that we have factored out is exactly the one in the $\mathcal{L}_{\text{otp-real}}$ library (apart from renaming the subroutine).

$$
\def\arraystretch{1.5} 
\underbrace{\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad k_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad c_1 := k_1 \oplus m \\
\quad c_2 := \text{CTXT}' (c_1) \\ 
\quad \operatorname{return } c_2 \\ \hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\qquad\mathcal{L}_{\text {otp-real}}\\ \hline
\underline{\mathrm{CTXT}'\left(m'\right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \operatorname{return } k \oplus m' \\ \hline
\end{array}
}_{\mathcal{L}_{\text{hyb-1}}}
\quad \equiv \quad
\underbrace{\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad k_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad c_1 := k_1 \oplus m \\
\quad c_2 := \text{CTXT}' (c_1) \\ 
\quad \operatorname{return } c_2 \\ \hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\qquad\mathcal{L}_{\text {otp-rand}}\\ \hline
\underline{\mathrm{CTXT}'\left(m'\right):} \\
\quad c \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \operatorname{return } c \\ \hline
\end{array}
}_{\mathcal{L}_{\text{hyb-2}}}
$$

Claim 2.7 says that $\mathcal{L}_{\text{otp-real}} \equiv \mathcal{L}_{\text{otp-rand}}$, so Lemma 2.11 says that we can replace an instance of $\mathcal{L}_{\text{otp-real}}$ in a compound library with $\mathcal{L}_{\text{otp-rand}}$, as we have done here. This change will have no effect on the calling program.

$$
\def\arraystretch{1.5} 
\underbrace{\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad k_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad c_1 := k_1 \oplus m \\
\quad c_2 \colorbox{yellow}{:= \text{CTXT}'} (c_1) \\ 
\quad \operatorname{return } c_2 \\ \hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\qquad\mathcal{L}_{\text {otp-rand}}\\ \hline
\underline{\mathrm{CTXT}'\left(m'\right):} \\
\quad \colorbox{yellow}{c} \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \operatorname{return } c \\ \hline
\end{array}
}_{\mathcal{L}_{\text{hyb-2}}}
\quad \equiv \quad
\underbrace{\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad k_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad c_1 := k_1 \oplus m \\
\quad c_2 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\ 
\quad \operatorname{return } c_2 \\ \hline
\end{array}
}_{\mathcal{L}_{\text{hyb-3}}}
\quad \equiv \quad
\begin{array}{|l|} \hline
\qquad\mathcal{L}^{\Sigma}_{\text {otp-rand}}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad c_2 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \operatorname{return } c_2 \\ \hline
\end{array}
$$

The only difference between these two libraries is that a subroutine call has been inlined. This difference has no effect on the calling program.

$$
\def\arraystretch{1.5} 
\underbrace{\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad \colorbox{yellow}{k}_1 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \colorbox{yellow}{c}_1 := k_1 \oplus m \\
\quad c_2 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} \\ 
\quad \operatorname{return } c_2 \\ \hline
\end{array}
}_{\mathcal{L}_{\text{hyb-3}}}
\quad \equiv \quad
\begin{array}{|l|} \hline
\qquad\mathcal{L}^{\Sigma}_{\text {otp-rand}}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad c_2 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \operatorname{return } c_2 \\ \hline
\end{array}
$$

The only difference between these two libraries is that the two highlighted lines have been removed. But it should be clear that these lines have no effect: $k_1$ is used only to compute $c_1$, which is never used again. Hence, this difference has no effect on the calling program.

The final hybrid is exactly $\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-rand}}$ (although with a variable name changed). We have shown that $\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-rand}} \equiv \mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-rreal}}$, meaning that this encryption scheme has one-time uniform ciphertexts.


### Summary of the Hybrid Technique

We have now seen our first example of the hybrid technique for security proofs. All security proofs in this book use this technique.

 - Proving security means showing that two particular libraries, say $\mathcal{L}_{\text{left}}$ and $\mathcal{L}_{\text{right}}$, are interchangeable. 
 - Often $\mathcal{L}_{\text{left}}$ and $\mathcal{L}_{\text{right}}$ are significantly different, making them hard to compare directly. To make the comparison more manageable, we can show a sequence of hybrid libraries, beginning with $\mathcal{L}_{\text{left}}$ and ending with $\mathcal{L}_{\text{right}}$. The idea is to break up the large “gap” between $\mathcal{L}_{\text{left}}$ and $\mathcal{L}_{\text{right}}$ into smaller ones that are easier to justify.
 - It is helpful to think of “starting” at $\mathcal{L}_{\text{left}}$, and then making a sequence of small modifications to it, with the goal of eventually reaching $\mathcal{L}_{\text{right}}$. You must justify why each modification doesn’t affect the calling program (*i.e.,* why the two libraries before/after your modification are interchangeable).
 - As discussed in Section 2.2, simple things like inlining/factoring out subroutines, changing unused variables, changing unreachable statements, or unrolling loops are always “allowable” modifications in a hybrid proof since they have no effect on the calling program. As we progress in the course, we will see more kinds of useful modifications.


### A Contrasting Example

Usually the boundary between secure and insecure is razor thin. Let’s make a small change to the previous encryption scheme to illustrate this point. Instead of applying OTP to the plaintext twice, with independent keys, what would happen if we use the *same key?*

**Construction 2.14 (“dOuB$\ell \exist$ OTP”)**
$$
\def\arraystretch{1.5} 
\begin{array}{|rlll|} \hline
\mathcal{K}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \underline{\text { KeyGen: }} & \underline{\operatorname{Enc}(k,m):} & \underline{\operatorname{Dec}(k,c_2):} \\
\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \quad {k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}} & \quad c_1 := k \oplus m & \quad c_1 := k \oplus c_2 \\
\mathcal{C}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \quad \text {return } k  & \quad c_2 := k \oplus c_1 & \quad m := k \oplus c_1 \\
& \quad  & \quad \text {return } c_2 & \quad \text {return } m \\ \hline
\end{array}
$$

You probably noticed that the ciphertext $c_2$ is computed as $c_2 := k \oplus (k \oplus m)$, which is just a fancy way of saying $c_2 := m$. There is certainly no way this kind of “double-OTP” is secure.

For educational purposes, let’s try to repeat the steps of our previous security proof on this (insecure) scheme and **see where things break down**. If we wanted to show that Construction 2.14 has uniform ciphertexts, we would have to show that the following two libraries are interchangeable:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-real}}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\begin{array}{l}
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\end{array} \textcolor{gray}{// \text{KeyGen }} \\ 
\left.\begin{array}{l}
\quad c_1 := k \oplus  m \\
\quad c_2 := k \oplus  c_1 \\
\quad \operatorname{return } c_2 \\
\end{array}\right\}
\textcolor{gray}{\text{Enc}} \\ \hline
\end{array}
\quad \overset{?}{\equiv} \quad
\begin{array}{|ll|} \hline
\quad\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-rand}}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad c \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\ 
\quad \operatorname{return } c \\ \hline
\end{array}
$$

In the previous hybrid proof, the first step was to factor out the statements “$k_2 \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}$; $c_2 := k_2 \oplus c_1$” into a separate subroutine, so we could argue that the result of $c_2$ was uniformly distributed. If we do something analogous with this example, we get:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\mathcal{L}^{\Sigma}_{\text {ots}\varPhi\text{-real}}\\ \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\begin{array}{l}
\quad \colorbox{yellow}{k} \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda} 
\end{array} \\ 
\begin{array}{l}
\quad c_1 := k \oplus  m \\
\quad c_2 := \colorbox{yellow}{k} \oplus  c_1 \\
\quad \operatorname{return } c_2 \\
\end{array} \\ \hline
\end{array}
\quad \overset{?}{\equiv} \quad
\underbrace{\begin{array}{|ll|} \hline
\underline{\mathrm{CTXT}\left(m\right):} \\
\quad c_1 := k \oplus m \textcolor{gray}{\ // ??} \\
\quad c_2 := \colorbox{yellow}{\text{CTXT}}' (c_1) \\ 
\quad \operatorname{return } c_2 \\ \hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\qquad\mathcal{L}_{\text {otp-real}}\\ \hline
\underline{\mathrm{CTXT}'\left(m'\right):} \\
\quad k \leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}  \\
\quad \operatorname{return } k \oplus m' \\ \hline
\end{array}
}_{\mathcal{L}_{\text{hyb}}}
$$

Do you see the problem? In “$\mathcal{L}_{\text{hyb}}$”, we have tried to move the variable $k$ into $\mathcal{L}_{\text{otp-real}}$. Since this scope is private, every operation we want to do with $k$ has to be provided by its container library $\mathcal{L}_{\text{otp-real}}$. But there is a mismatch: $\mathcal{L}_{\text{otp-real}}$ only gives us a way to use $k$ in one XOR expression, whereas we need to use the same $k$ in two XOR expressions to match the behavior of $\mathcal{L}_{\text{ots}\varPhi\text{-real}}$. The compound library $\mathcal{L}_{\text{hyb}}$ has an unresolved reference
to $k$ in the line “$c_1 := k \oplus m$,” and therefore doesn’t have the same behavior as $\mathcal{L}_{\text{ots}\varPhi\text{-real}}$.[^13] This is the step of the security proof that breaks down.

[^13]: I would say that the library “doesn’t compile” due to a scope/reference error.

Here’s a more conceptual way to understand what went wrong here. The important property of OTP is that its ciphertexts look uniform *when the key is used to encrypt only one plaintext*. This “double OTP” variant uses OTP in a way that doesn’t fulfill that condition, and therefore provides no security guarantee. The previous (successful) proof was able to factor out some XOR’s in terms of $\mathcal{L}_{\text{otp-real}}$ without breaking anything, and that’s how we know the scheme was using OTP in a way that is consistent with its security guarantee.

As you can hopefully see, the process of a security proof provides a way to catch these kinds of problems. It is very common in a hybrid proof to factor out some statements in terms of a library from some other security definition. This step can only be done successfully if the underlying cryptography is being used in an appropriate way.


## 2.5 How to Compare/Contrast Security Definitions

In math, a definition can’t really be “wrong,” but it can be “not as useful as you hoped” or it can “fail to adequately capture your intuition” about the concept.

Security definitions are no different. In this chapter we introduced two security definitions: one in the “real-vs-random” style and one in the “left-vs-right” style. In this section we treat the *security definitions themselves* as objects worth studying. Are both of these security definitions “the same,” in some sense? Do they both capture all of our intuitions about security?

### One Security Definition Implies Another

One way to compare/contrast two security definitions is to prove that one implies the other. In other words, if an encryption scheme satisfies definition #1, then it also satisfies definition #2.

**Theorem 2.15** 
*If an encryption scheme $\Sigma$ has one-time uniform ciphertexts (Definition 2.5), then $\Sigma$ also has one-time secrecy (Definition 2.6). In other words:*

$$
\mathcal{L}_{\text {ots}\varPhi\text{-real }}^{\Sigma} \equiv  \mathcal{L}_{\text {ots}\varPhi\text{-rand }}^{\Sigma} \Rightarrow
\mathcal{L}^{\Sigma}_{\mathrm{ots-L}} \equiv \mathcal{L}^{\Sigma}_{\mathrm{ots}-R}
$$

If you are comfortable with what all the terminology means, then the meaning of this statement is quite simple and unsurprising. “If all plaintexts $m$ induce a *uniform* distribution of ciphertexts, then all $m$ induce the *same* distribution of ciphertexts.”

This fairly straight-forward statement can be proven formally, giving us another example of the hybrid proof technique:

**Proof** 
We are proving an if-then statement. We want to show that the “then”-part of the statement is true; that is, $\mathcal{L}^{\Sigma}_{\mathrm{ots-L}} \equiv \mathcal{L}^{\Sigma}_{\mathrm{ots}-R}$. We are allowed to use the fact that the “if”-part is true; that is, $\mathcal{L}_{\text {ots}\varPhi\text{-real }}^{\Sigma} \equiv  \mathcal{L}_{\text {ots}\varPhi\text{-rand }}^{\Sigma}$.

The proof uses the hybrid technique. We will start with the library $\mathcal{L}_{\mathrm{ots-L}}$, and make a small sequence of justifiable changes to it, until finally reaching $\mathcal{L}_{\mathrm{ots-R}}$. Along the way, we can use the fact that $\mathcal{L}_{\text {ots}\varPhi\text{-real }} \equiv  \mathcal{L}_{\text {ots}\varPhi\text{-rand}}$. This suggests some “strategy” for the proof: if we can somehow get $\mathcal{L}_{\text {ots}\varPhi\text{-real }}$ to appear as a component in one of the hybrid libraries, then we can replace it with $\mathcal{L}_{\text {ots}\varPhi\text{-rand}}$ (or vice-versa), in a way that hopefully makes progress
towards our goal of transforming $\mathcal{L}_{\text {ots-L}}$ to $\mathcal{L}_{\text {ots-R}}$.

Below we list the sequence of hybrid libraries, and justify why each one is interchangeable with the previous library.

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad\quad \mathcal{L}_{\text {ots-L }}^{\Sigma} \\ \hline
\underline{\text { EAVESDROP}\left(m_{L}, m_{R}\right):} \\
\quad k \leftarrow \Sigma . \text { KeyGen } \\
\quad c \leftarrow \Sigma . \operatorname{Enc}\left(k, m_{L}\right) \\
\quad \text {return } c \\ \hline
\end{array}
$$

The starting point of our hybrid sequence is $\mathcal{L}_{\text {ots-L }}^{\Sigma}$.

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\underline{\text { EAVESDROP}\left(m_{L}, m_{R}\right):} \\
\quad c := \operatorname{CTXT}\left(m_{L}\right) \\
\quad \text {return } c \\ \hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\qquad\quad \mathcal{L}_{\text {ots}\varPhi\text{-real}}^{\Sigma} \\ \hline
\underline{\text { CTXT}\left(m\right):} \\
\quad k \leftarrow \Sigma . \text { KeyGen } \\
\quad c \leftarrow \Sigma . \operatorname{Enc}\left(k, m\right) \\
\quad \text {return } c \\ \hline
\end{array}
$$

Factoring out a block of statements into a subroutine makes it possible to write the library as a *compound* one, but does not affect its external behavior. Note that the new subroutine is exactly the $\mathcal{L}_{\text {ots}\varPhi\text{-real}}^{\Sigma}$ library from Definition 2.5. This was a strategic choice, because of what happens next.

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\underline{\text { EAVESDROP}\left(m_{L}, m_{R}\right):} \\
\quad c := \operatorname{CTXT}\left(m_{L}\right) \\
\quad \text {return } c \\ \hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\quad \mathcal{L}_{\text {ots}\varPhi\text{-rand}}^{\Sigma} \\ \hline
\underline{\text { CTXT}\left(m\right):} \\
\quad c \leftarrow \Sigma . C\\
\quad \text {return } c \\ \hline
\end{array}
$$

$\mathcal{L}_{\text {ots}\varPhi\text{-real}}^{\Sigma}$ has been replaced with $\mathcal{L}_{\text {ots}\varPhi\text{-rand}}^{\Sigma}$. The chaining lemma Lemma 2.11 says that this change has no effect on the library’s behavior, since the two $\mathcal{L}_{\text {ots}\varPhi-\star}$ libraries are interchangeable.

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\underline{\text { EAVESDROP}\left(m_{L}, m_{R}\right):} \\
\quad c := \operatorname{CTXT}\left(m_{R}\right) \\
\quad \text {return } c \\ \hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\quad \mathcal{L}_{\text {ots}\varPhi\text{-rand}}^{\Sigma} \\ \hline
\underline{\text { CTXT}\left(m\right):} \\
\quad c \leftarrow \Sigma . C\\
\quad \text {return } c \\ \hline
\end{array}
$$

The argument to CTXT has been changed from $m_L$ to $m_R$. This has no effect on the library’s behavior since CTXT *does not actually use its argument* in these hybrids!

The previous transition is the most important one in the proof, as it gives insight into how we came up with this particular sequence of hybrids. Looking at the desired endpoints of our sequence of hybrids — $\mathcal{L}^{\Sigma}_{\text{ots-L}}$ and $\mathcal{L}^{\Sigma}_{\text{ots-R}}$ — we see that they differ only in swapping $m_L$ for $m_R$. If we are not comfortable eyeballing things, we’d like a better justification for why it is “safe” to exchange $m_L$ for $m_R$ (*i.e.,* why it has no effect on the calling program). However, the uniform ciphertexts property shows that $\mathcal{L}^{\Sigma}_{\text{ots-L}}$ in fact has the same behavior as a library $\mathcal{L}_{\text{hyb-2}}$ that doesn’t use either of $m_L$ or $m_R$. In a program that doesn’t use $m_L$ or $m_R$, it is clear that we can switch them!

Having made this crucial change, we can now perform the same sequence of steps, but in reverse.

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\underline{\text { EAVESDROP}\left(m_{L}, m_{R}\right):} \\
\quad c := \operatorname{CTXT}\left(m_{R}\right) \\
\quad \text {return } c \\ \hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\qquad\quad \mathcal{L}_{\text {ots}\varPhi\text{-real}}^{\Sigma} \\ \hline
\underline{\text { CTXT}\left(m\right):} \\
\quad k \leftarrow \Sigma . \text { KeyGen } \\
\quad c \leftarrow \Sigma . \operatorname{Enc}\left(k, m\right) \\
\quad \text {return } c \\ \hline
\end{array}
$$

$\mathcal{L}_{\text {ots}\varPhi\text{-rand}}^{\Sigma}$ has been replaced with $\mathcal{L}_{\text {ots}\varPhi\text{-real}}^{\Sigma}$. This is another application of the chaining lemma.

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad\quad \mathcal{L}_{\text {ots-R}}^{\Sigma} \\ \hline
\underline{\text { EAVESDROP}\left(m_L,m_R\right):} \\
\quad k \leftarrow \Sigma . \text { KeyGen } \\
\quad c \leftarrow \Sigma . \operatorname{Enc}\left(k, m_R\right) \\
\quad \text {return } c \\ \hline
\end{array}
$$

A subroutine call has been inlined, which has no effect on the library’s behavior. The result is exactly $\mathcal{L}_{\text {ots-R}}^{\Sigma}$.

Putting everything together, we showed that $\mathcal{L}_{\mathrm{ots}-\mathrm{L}}^{\Sigma} \equiv \mathcal{L}_{\mathrm{hyb}-1} \equiv \cdots \equiv \mathcal{L}_{\mathrm{hyb}-4} \equiv \mathcal{L}_{\mathrm{ots}-\mathrm{R}}^{\Sigma}$. This completes the proof, and we conclude that $\Sigma$ satisfies the definition of one-time secrecy.


### One Security Definition Doesn’t Imply Another

Another way we might compare security definitions is to identify any schemes that satisfy one definition without satisfying the other. This helps us understand the boundaries and “edge cases” of the definition.

A word of warning: If we have two security definitions that both capture our intuitions rather well, then any scheme which satisfies one definition and not the other is bound to appear **unnatural and contrived**. The point is to gain more understanding of the *security definitions themselves*, and unnatural/contrived schemes are just a means to do that.

**Theorem 2.16** 
*There is an encryption scheme that satisfies one-time secrecy (Definition 2.6) but not one-time uniform ciphertexts (Definition 2.5). In other words, one-time secrecy **does not** necessarily imply one-time uniform ciphertexts.*

**Proof** 
One such encryption scheme is given below:

$$
\def\arraystretch{1.5} 
\begin{array}{|rlll|} \hline
\mathcal{K}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \underline{\text { KeyGen: }} & \underline{\operatorname{Enc}(k,m \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}):} & \underline{\operatorname{Dec}(k,c \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda+2}):} \\
\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} & \quad {k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}} & \quad c' := k \oplus m & \quad c' := \text{first } \lambda \text{ bits of } c \\
\mathcal{C}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda+2} & \quad \text {return } k  & \quad c := c' || \textcolor{brown}{00} & \quad \quad \text {return } k \oplus c' \\
& \quad  & \quad \text {return } c &  \\ \hline
\end{array}
$$

This scheme is just OTP with the bits $\textcolor{brown}{00}$ added to every ciphertext. The following facts about the scheme should be believable (and the exercises encourage you to prove them formally if you would like more practice at that sort of thing):

 - This scheme satisfies one-time one-time secrecy, meaning that encryptions of $m_L$ are distributed identically to encryptions of $m_R$, for any $m_L$ and $m_R$ of the attacker’s choice. We can characterize the ciphertext distribution in both cases as “$\lambda$ uniform bits followed by $\textcolor{brown}{00}$.” Think about how you might use the hybrid proof technique to formally prove that this scheme satisfies one-time secrecy!
 - This scheme does not satisfy the one-time uniform ciphertexts property. Its ciphertexts always end with $\textcolor{brown}{00}$, whereas uniform strings end with $\textcolor{brown}{00}$ with probability 1/4. Think about how you might formallize this observation as a calling program /distinguisher for the relevant two libraries!

You might be thinking, surely this can be fixed by redefining the ciphertext space as $\mathcal{C}$ as the set of $\lambda + 2$-bit strings whose last two bits are $\textcolor{brown}{00}$. This is a clever idea, and indeed it would work. If we change the definition of the ciphertext space $\mathcal{C}$ following this suggestion, then the scheme would satisfy the uniform ciphertexts property (this is because the $\mathcal{L}_{\text{ots}\varPhi\text{-rand}}$ library samples uniformly from whatever $\mathcal{C}$ is specified as part of the encryption scheme).

But this observation raises an interesting point. Isn’t it weird that security hinges on how narrowly you define the set $\mathcal{C}$ of ciphertexts, when $\mathcal{C}$ really has no effect on the *functionality* of encryption? Again, no one really cares about this contrived “OTP +$\textcolor{brown}{00}$” encryption scheme. The point is to illuminate interesting edge cases in the *security definition itself!*


### Exercises

2.1. Below are two calling programs $\mathcal{A}_1$, $\mathcal{A}_2$ and two libraries $\mathcal{L}_1$, $\mathcal{L}_2$ with a common interface:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline 
\qquad\quad \mathcal{A}_1 \\  \hline 
r_1 := \text{RAND}(6) \\
r_2 := \text{RAND}(6) \\
\text{return } r_1 \overset{?}{=} r_2 \\ \hline 
\end{array}
\quad
\begin{array}{|l|} \hline 
\qquad\quad \mathcal{A}_2 \\  \hline 
r := \text{RAND}(6) \\
\text{return } r \overset{?}{\geq} 3 \\ \hline 
\end{array}
\quad
\begin{array}{|l|} \hline 
\qquad \mathcal{L}_1 \\  \hline 
\underline{\text{RAND}(n):} \\
\quad r \leftarrow \mathbb{Z}_n \\
\quad \text{return } r \\ \hline 
\end{array}
\quad
\begin{array}{|l|} \hline 
\qquad \mathcal{L}_2 \\  \hline 
\underline{\text{RAND}(n):} \\
\quad \text{return } 0 \\ \hline 
\end{array}
$$

(a) What is $\operatorname{Pr}\left[\mathcal{A}_{1} \diamond \mathcal{L}_{1} \Rightarrow\right.$ true $]$ ?
(b) What is $\operatorname{Pr}\left[\mathcal{A}_{1} \diamond \mathcal{L}_{2} \Rightarrow\right.$ true $] ?$
( c) What is $\operatorname{Pr}\left[\mathcal{A}_{2} \diamond \mathcal{L}_{1} \Rightarrow\right.$ true $]$ ?
(d) What is $\operatorname{Pr}\left[\mathcal{A}_{2} \diamond \mathcal{L}_{2} \Rightarrow\right.$ true $] ?$

2.2. In each problem, a pair of libraries are described. State whether or not $\mathcal{L}_{\text{left}} \equiv \mathcal{L}_{\text{right}}$. If so, show how they assign identical probabilities to all outcomes. If not, then describe a successful *distinguisher.*
Assume that both libraries use the same value of $n$. Does your answer ever depend on the choice of $n$?
In part (a), $\overline{x}$ denotes the bitwise-complement of $x$. In part (d), $x$ & $y$ denotes the bitwise-AND of the two strings:

$$
\def\arraystretch{1.5} 
\text{(a)} \qquad
\begin{array}{|l|} \hline 
\qquad\mathcal{L}_{\text {left }} \\ \hline
\underline{\text {QUERY }():} \\
\quad x \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n} \\
\quad \text {return } x \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline 
\qquad\mathcal{L}_{\text {right }} \\ \hline
\underline{\text {QUERY }():} \\
\quad x \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n} \\
\quad y := \overline{x} \\
\quad \text {return } y \\ \hline
\end{array}
$$

$$
\def\arraystretch{1.5} 
\text{(b)} \qquad
\begin{array}{|l|} \hline 
\qquad\mathcal{L}_{\text {left }} \\ \hline
\underline{\text {QUERY }():} \\
\quad x \leftarrow \mathbb{Z}_n \\
\quad \text {return } x \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline 
\qquad\mathcal{L}_{\text {right }} \\ \hline
\underline{\text {QUERY }():} \\
\quad x \leftarrow  \mathbb{Z}_n \\
\quad y := 2x \% n \\
\quad \text {return } y \\ \hline
\end{array}
$$

$$
\def\arraystretch{1.5} 
\text{(c)} \qquad
\begin{array}{|l|} \hline 
\qquad\qquad\mathcal{L}_{\text {left }} \\ \hline
\underline{\text {QUERY }(c \in \mathbb{Z}_n):} \\
\quad \text{if } c=0 \\
\qquad \text {return null} \\
\quad x \leftarrow \mathbb{Z}_n \\
\quad \text {return } x \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline 
\qquad\mathcal{L}_{\text {right }} \\ \hline
\underline{\text {QUERY }(c \in \mathbb{Z}_n):} \\
\quad \text{if } c=0 \\
\qquad \text {return null} \\
\quad x \leftarrow \mathbb{Z}_n \\
\quad y := cx \% n \\
\quad \text {return } y \\ \hline
\end{array}
$$

$$
\def\arraystretch{1.5} 
\text{(d)} \qquad
\begin{array}{|l|} \hline 
\qquad\mathcal{L}_{\text {left }} \\ \hline
\underline{\text {QUERY }():} \\
\quad x \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n} \\
\quad y \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n} \\
\quad \text {return } x \& y \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline 
\qquad\mathcal{L}_{\text {right }} \\ \hline
\underline{\text {QUERY }():} \\
\quad z \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n} \\
\quad \text {return } z \\ \hline
\end{array}
$$

2.3. Show that the following libraries are interchangeable:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline 
\qquad\qquad\mathcal{L}_{\text {left }} \\ \hline
\underline{\text {QUERY }(m \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}):} \\
\quad x \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad y := x \oplus m \\
\quad \text {return } (x,y) \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline 
\qquad\qquad\mathcal{L}_{\text {right }} \\ \hline
\underline{\text {QUERY }(m \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}):} \\
\quad y \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad x := y \oplus m \\
\quad \text {return } (x,y) \\ \hline
\end{array}
$$

Note that $x$ and $y$ are swapped in the first two lines, but not in the return statement.

2.4. Show that the following libraries are **not** interchangeable. Describe an explicit distinguishing calling program, and compute its output probabilities when linked to both libraries:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline 
\qquad\qquad\qquad\mathcal{L}_{\text {left }} \\ \hline
\underline{\text {EAVESDROP }(m_L, m_R \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}):} \\
\quad k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad c := k \oplus m_L \\
\quad \text {return } (k,c) \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline 
\qquad\qquad\qquad\mathcal{L}_{\text {right }} \\ \hline
\underline{\text {EAVESDROP }(m_L, m_R \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}):} \\
\quad k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad c := k \oplus m_R \\
\quad \text {return } (k,c) \\ \hline
\end{array}
$$

$\star$ 2.5. In abstract algebra, a (finite) **group** is a finite set $\mathbb{G}$ of items together with an operator $\otimes$ satisfying the following axioms:

 - **Closure:** for all $a,b \in \mathbb{G}$, we have $a \otimes b \in \mathbb{G}$.
 - **Identity:** there is a special *identity element* $e \in \mathbb{G}$ that satisfies $e \otimes a = a \otimes e = a$ for all $a \in \mathbb{G}$. We typically write “1” rather than e for the identity element.
 - **Associativity:** for all $a,b, c \in \mathbb{G}$, we have $(a \otimes b) \otimes c = a \otimes (b \otimes c).$
 - **Inverses:** for all $a \in \mathbb{G}$, there exists an *inverse element* $b \in \mathbb{G}$ such that $a \otimes b = b \otimes a$ is the identity element of $\mathbb{G}$. We typically write “$a^{-1}$” for the inverse of $a$.

Define the following encryption scheme in terms of an arbitrary *group* $(\mathbb{G}, \otimes):$

$$
\def\arraystretch{1.5}
\begin{array}{|rlll|} \hline
\mathcal{K}=\mathbb{G} & \underline{\text { KeyGen: }} & \underline{\operatorname{Enc}(k, m):} & \underline{\operatorname{Dec}(k, c)} \\
\mathcal{M}=\mathbb{G} & \quad {k \leftarrow \mathbb{G}} & \quad {\text {return } k} \otimes m & \quad ?? \\
\mathcal{C}=\mathbb{G} & \quad \text {return } k \\ \hline
\end{array} 
$$

(a) Prove that $\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}$ is a group with respect to the XOR operator. What is the identity element, and what is the inverse of a value $x \in \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}$ ?
(b) Fill in the details of the Dec algorithm and prove (using the group axioms) that the scheme satisfies correctness.
( c) Prove that the scheme satisfies one-time secrecy.

2.6. In the proof of Claim 2.9 we considered an attacker / calling program that calls CTXT$(\textcolor{brown}{0}^{\lambda})$.
(a) How does this attacker’s effectiveness change if it calls CTXT$(\textcolor{brown}{1}^{\lambda})$ instead?
(b) How does its effectiveness change if it calls CTXT$(m)$ for a uniformly chosen $m$?

2.7. The following scheme encrypts a plaintext by simply reordering its bits, according to the secret permutation $k$.

$$
\def\arraystretch{1.5}
\begin{array}{|llll|} \hline
\begin{array}{l}
\mathcal{K}=\left\{\begin{array}{l}
\text{permutations} \\
\text{of } \{1, \ldots, \lambda\}
\end{array} \right\}  \\
\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}  \\
\mathcal{C}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}  \\
\end{array} &
\begin{array}{l}
\underline{\text {Enc}(k, m):} \\
\quad \text {for } i:=1 \text { to } \lambda: \\
\qquad c_{k(i)}:=m_{i} \\
\quad \text {return } c_{1} \cdots c_{\lambda}
\end{array}
\\
\begin{array}{l}
\underline{\text {KeyGen}:} \\
\quad k \leftarrow \mathcal{K} \\
\quad \text {return } k
\end{array} &
\begin{array}{l}
\underline{\text {Dec}(k, c):} \\
\quad \text {for } i:=1 \text { to } \lambda: \\
\qquad m_i:= c_{k(i)} \\
\quad \text {return } m_{1} \cdots m_{\lambda}
\end{array}
\\ \hline
\end{array} 
$$

Show that the scheme does **not** have one-time secrecy, by constructing a program that distinguishes the two relevant libraries from the one-time secrecy definition.

2.8. Show that the following encryption scheme does **not** have one-time secrecy, by constructing a program that distinguishes the two relevant libraries from the one-time secrecy definition.

$$
\def\arraystretch{1.5}
\begin{array}{|llll|} \hline
\mathcal{K}=\{1, \ldots, 9\} & \underline{\text { KeyGen: }} & \underline{\operatorname{Enc}(k, m):}  \\
\mathcal{M}=\{1, \ldots, 9\} & \quad {k \leftarrow \{1, \ldots, 9\}} & \quad {\text {return } k} \times m \% 10  \\
\mathcal{C}=\mathbb{Z}_{10} & \quad \text {return } k \\ \hline
\end{array} 
$$

2.9. Consider the following encryption scheme. It supports plaintexts from $\mathcal{M} = \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}$ and ciphertexts from $\mathcal{C} = \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{2\lambda}$. Its keyspace is:

$$
\mathcal{K}=\left\{k \in\{\textcolor{brown}{0},\textcolor{brown}{1},\textcolor{brown}{\_}\}^{2 \lambda} \mid k \text { contains exactly } \lambda "\textcolor{brown}{\_} \text { " characters }\right\}
$$

To encrypt plaintext $m$ under key $k$, we “fill in” the $\textcolor{brown}{\_}$ characters in $k$ using the bits of $m$.

Show that the scheme does **not** have one-time secrecy, by constructing a program that distinguishes the two relevant libraries from the one-time secrecy definition.

*Example:* Below is an example encryption of $m = \textcolor{brown}{1101100001}$.

$$
\begin{aligned}
k &= \textcolor{brown}{1\_\_ 0\_\_11010\_1\_0\_0\_\_\_} \\
m &= ~~ 11~~01 ~~~~~~~~~~~ 1~~0~~0~~001 \\
\Rightarrow \operatorname{Enc}(k, m) &=\textcolor{brown}{11100111010110000001}
\end{aligned}
$$

2.10. Suppose we modify the scheme from the previous problem to first permute the bits of $m$ (as in Exercise 2.7) and then use them to fill in the “$\textcolor{brown}{\_}$” characters in a template string. In other words, the key specifies a random permutation on positions $\{1, \ldots , \lambda\}$ as well as a random template string that is $2\lambda$ characters long with $\lambda$ “$\textcolor{brown}{\_}$” characters.

Show that even with this modification the scheme does not have one-time secrecy.

$\star$ 2.11. Prove that if an encryption scheme $\Sigma$ has $|\Sigma.\mathcal{K}| < |\Sigma.\mathcal{M}|$ then it cannot satisfy onetime secrecy. Try to structure your proof as an explicit attack on such a scheme (*i.e.,* a distinguisher against the appropriate libraries).

The Enc algorithm of one-time pad is deterministic, but our definitions of encryption allow Enc to be randomized (*i.e.,* it may give different outputs when called twice with the same $k$ and $m$). For full credit, you should prove the statement even for the case of Enc is randomized. However, you may assume that Dec is deterministic.

**Hint:**
The definition of interchangeability does not place any restriction on the running time of the distinguisher/calling program. Even an exhaustive brute-force attack would be valid.

2.12. Let $\Sigma$ denote an encryption scheme where $\Sigma.\mathcal{C} \subseteq \Sigma.\mathcal{M}$ (so that it is possible to use the scheme to encrypt its own ciphertexts). Define $\Sigma^2$ to be the following **nested-encryption** scheme:

$$
\def\arraystretch{1.5}
\begin{array}{|llll|} \hline
\begin{array}{l}
\mathcal{K}=(\Sigma . \mathcal{K})^2  \\
\mathcal{M}=\Sigma . \mathcal{M} \\
\mathcal{C}=\Sigma . \mathcal{C} \\
\\
\underline{\text {KeyGen}:} \\
\quad k_1 \leftarrow \Sigma .\mathcal{K} \\
\quad k_2 \leftarrow \Sigma .\mathcal{K} \\
\quad \text {return } (k_1, k_2)
\end{array} &
\begin{array}{l}
\underline{\operatorname{Enc}\left(\left(k_{1}, k_{2}\right), m\right):} \\
\quad c_{1}:=\sum \cdot \operatorname{Enc}\left(k_{1}, m\right) \\
\quad c_{2}:=\sum \cdot \operatorname{Enc}\left(k_{2}, c_{1}\right) \\
\quad \text {return } c_{2}
\end{array}
&
\begin{array}{l}
\underline{\operatorname{Dec}\left(\left(k_{1}, k_{2}\right), c_2\right):} \\
\quad c_{1}:=\sum \cdot \operatorname{Dec}\left(k_{2}, c_2\right) \\
\quad m:=\sum \cdot \operatorname{Dec}\left(k_{1}, c_{1}\right) \\
\quad \text {return } m
\end{array}\\
\\ \hline
\end{array} 
$$

Prove that if $\Sigma$ satisfies one-time secrecy, then so does $\Sigma^2$.

2.13. Let $\Sigma$ denote an encryption scheme and define $\Sigma^2$ to be the following **encrypt-twice** scheme:

$$
\def\arraystretch{1.5}
\begin{array}{|llll|} \hline
\begin{array}{l}
\mathcal{K}=(\Sigma . \mathcal{K})^2  \\
\mathcal{M}=\Sigma . \mathcal{M} \\
\mathcal{C}=\Sigma . \mathcal{C} \\
\\
\underline{\text {KeyGen}:} \\
\quad k_1 \leftarrow \Sigma .\mathcal{K} \\
\quad k_2 \leftarrow \Sigma .\mathcal{K} \\
\quad \text {return } (k_1, k_2)
\end{array} &
\begin{array}{l}
\underline{\operatorname{Enc}\left(\left(k_{1}, k_{2}\right), m\right):} \\
\quad c_{1}:=\sum \cdot \operatorname{Enc}\left(k_{1}, m\right) \\
\quad c_{2}:=\sum \cdot \operatorname{Enc}\left(k_{2}, m\right) \\
\quad \text {return } (c_1,c_{2})
\end{array}
&
\begin{array}{l}
\underline{\operatorname{Dec}\left(\left(k_{1}, k_{2}\right), (c_1, c_2)\right):} \\
\quad m_{1}:=\sum \cdot \operatorname{Dec}\left(k_{1}, c_1\right) \\
\quad m_2:=\sum \cdot \operatorname{Dec}\left(k_{2}, c_{2}\right) \\
\quad \text{if } m_1 \neq m_2 \text { return } \textcolor{brown}{\texttt{err}} \\
\quad \text {return } m_1
\end{array}\\
\\ \hline
\end{array} 
$$

Prove that if $\Sigma$ satisfies one-time secrecy, then so does $\Sigma^2$.

2.14. Prove that an encryption scheme $\Sigma$ satisfies one-time secrecy **if and only if** the following two libraries are interchangeable:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad \mathcal{L}_{\text {left}}^{\Sigma} \\ \hline
\underline{\text {FOO}\left(m \in \Sigma.\mathcal{M} \right):} \\
\quad k \leftarrow \Sigma . \text { KeyGen } \\ \\
\quad c \leftarrow \Sigma . \operatorname{Enc}\left(k, \colorbox{yellow}{m}\right) \\
\quad \text {return } c \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline
\qquad\qquad \mathcal{L}_{\text {right}}^{\Sigma} \\ \hline
\underline{\text {FOO}\left(m \in \Sigma.\mathcal{M} \right):} \\
\quad k \leftarrow \Sigma . \text { KeyGen } \\
\quad m' \leftarrow \Sigma . \mathcal{M} \\
\quad c \leftarrow \Sigma . \operatorname{Enc}\left(k, \colorbox{yellow}{m}'\right) \\
\quad \text {return } c \\ \hline
\end{array}
$$

*Note:* you must prove both directions of the if-and-only-if with a hybrid proof.

2.15. Prove that an encryption scheme $\Sigma$ has one-time secrecy **if and only if** the following two libraries are interchangeable:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline
\qquad\qquad \mathcal{L}_{\text {left}}^{\Sigma} \\ \hline
\underline{\text {FOO}\left(m_L, m_R \in \Sigma.\mathcal{M} \right):} \\
\quad k_1 \leftarrow \Sigma . \text { KeyGen } \\
\quad c_1 := \Sigma . \operatorname{Enc}\left(k_1, m_L\right) \\
\quad k_2 \leftarrow \Sigma . \text { KeyGen } \\
\quad c_2 := \Sigma . \operatorname{Enc}\left(k_2, m_R\right) \\
\quad \text {return } (c_1, c_2) \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline
\qquad\qquad \mathcal{L}_{\text {right}}^{\Sigma} \\ \hline
\underline{\text {FOO}\left(m_L, m_R \in \Sigma.\mathcal{M} \right):} \\
\quad k_1 \leftarrow \Sigma . \text { KeyGen } \\
\quad c_1 := \Sigma . \operatorname{Enc}\left(k_1, m_R\right) \\
\quad k_2 \leftarrow \Sigma . \text { KeyGen } \\
\quad c_2 := \Sigma . \operatorname{Enc}\left(k_2, m_L\right) \\
\quad \text {return } (c_1, c_2) \\ \hline
\end{array}
$$

*Note:* you must prove both directions of the if-and-only-if with a hybrid proof.

2.16. Formally define a variant of the one-time secrecy definition in which the calling program can obtain two ciphertexts (on chosen plaintexts) encrypted under the same key. Call it two-time secrecy.
(a) Suppose someone tries to prove that one-time secrecy implies two-time secrecy. Show where the proof appears to break down.
(b) Describe an attack demonstrating that one-time pad does not satisfy your definition of two-time secrecy.

2.17. In this problem we consider modifying one-time pad so that the key is not chosen uniformly. Let $\mathcal{D}_{\lambda}$ denote the probability distribution over $\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}$ where we choose each bit of the result to be $\textcolor{brown}{0}$ with probability 0.4 and $\textcolor{brown}{1}$ with probability 0.6.

Let $\Sigma$ denote one-time pad encryption scheme but with the key sampled from distribution $\mathcal{D}_{\lambda}$ rather than the uniform distribution on $\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}$.

(a) Consider the case of $\lambda = 5$. A calling program $\mathcal{A}$ for the $\mathcal{L}^{\Sigma}_{\text{ots-}\star}$ libraries calls EAVESDROP$(\textcolor{brown}{01011,\ 10001})$ and receives the result $\textcolor{brown}{01101}$. What is the probability that this happens, assuming that $\mathcal{A}$ is linked to $\mathcal{L}_{\text{ots-L}}$? What about when $\mathcal{A}$ is linked to $\mathcal{L}_{\text{ots-R}}$?
(b) Turn this observation into an explicit attack on the one-time secrecy of $\Sigma$.

2.18. Complete the proof of Theorem 2.16.
(a) Formally prove (using the hybrid technique) that the scheme in that theorem satisfies one-time secrecy.
(b) Give a distinguishing calling program to show that the scheme doesn’t satisfy one-time uniform ciphertexts.





# 3. Secret Sharing
DNS is the system that maps human-memorable Internet domains like $\textcolor{brown}{\texttt{irs.gov}}$ to machine-readable IP addresses like  $\textcolor{brown}{166.123.218.220}$. If an attacker can masquerade as the DNS system and convince your computer that $\textcolor{brown}{\texttt{irs.gov}}$ actually resides at some other IP address, it might result in a bad day for you.

To protect against these kinds of attacks, a replacement called DNSSEC has been proposed. DNSSEC uses cryptography to make it impossible to falsify a domain-name mapping. The cryptography required to authenticate DNS mappings is certainly interesting, but an even more fundamental question remains: *Who can be trusted with the master cryptographic keys to the system?* The non-profit organization in charge of these kinds of things (ICANN) has chosen the following system. The master key is split into 7 pieces and distributed on smart cards to 7 geographically diverse people, who keep them in safe-deposit boxes. 

>At least five key-holding members of this fellowship would have to meet at a secure data center in the United States to reboot [DNSSEC] in case of a very unlikely system collapse.
>“If you round up five of these guys, they can decrypt [the root key] should the West Coast fall in the water and the East Coast get hit by a nuclear bomb," [said] Richard Lamb, program manager for DNSSEC at ICANN[^c3-1].
>[^c3-1]:[http://www.livescience.com/6791-internet-key-holders-insurance-cyber-attack.html](http://www.livescience.com/6791-internet-key-holders-insurance-cyber-attack.html)

How is it possible that any 5 out of the 7 key-holders can reconstruct the master key, but (presumably) 4 out of the 7 cannot? The solution lies in a cryptographic tool called a **secret-sharing scheme**, the topic of this chapter.

## 3.1 Definitions
We begin by introducing the syntax of a secret-sharing scheme:

**Definition 3.1 (Secret-sharing)**
*At **-out-of-$n$ threshold secret-sharing scheme (TSSS)** consists of the following algorithms:*
 - *Share: a randomized algorithm that takes a* **message** $m\in\mathcal{M}$ *as input, and outputs a sequence* $s=(s_1,\ldots, s_n)$ of **shares**.
 - *Reconstruct: a deterministic algorithm that takes a collection of t or more shares as input, and outputs a message.*
 
*We call* $\mathcal{M}$ *the* **message space** *of the scheme, and t its **threshold**. *As usual, we refer to the parameters/components of a scheme** $\Sigma$ as $\Sigma .t$, $\Sigma .n,$ $\Sigma .\mathcal{M},$ $\Sigma.$ Share $\Sigma.$Reconstruct.
 
 In secret-sharing, we number the users as $\{1,\ldots,n\}$, with user $i$ receiving share $s_i$. Let $U\subseteq\{1,\ldots,n\}$ be a subset of users. Then $\{s_i |i \in U\}$ refers to the set of shares belonging to users $U$ . If $U\geqslant t$, we say that $U$ is **authorized**;  otherwise it is **unauthorized**. The goal of secret sharing is for all authorized sets of users/shares to be able to reconstruct the secret, while all unauthorized sets learn nothing.

**Definition 3.2 (TSSS correctness)**
*At -out-of-n TSSS satisfies **correctness** if, for all authorized sets* $U\subseteq \{1,\ldots,n\}$ (*i.e.,* $|U|\geqslant t$) *and for all $s\leftarrow$ Share($m$), we have Reconstruct $(\{s_i|i\in U\})=m.$*

$$
\textcolor{red}{\text{Image screenshot here}}
$$

**Security Definition**
We’d like a security guarantee that says something like:
>if you know only an unauthorized set of shares, then you learn no information about the choice of secret message.

To translate this informal statement into a formal security definition, we define two libraries that allow the calling program to learn a set of shares (for an *unauthorized set*), and that differ only in which secret is shared. If the two libraries are interchangeable, then we conclude that seeing an unauthorized set of shares leaks no information about the choice of secret message. The definition looks like this:

**Definition 3.3 (TSSS security)**
*Let $\Sigma$ be a threshold secret-sharing scheme. We say that $\Sigma$ is **secure if** $\mathcal{L}_{\mathrm{tsss}-\mathrm{L}}^{\Sigma} \equiv \mathcal{L}_{\mathrm{tsss}-\mathrm{R}}^{\Sigma},$ where:*

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline 
\qquad\qquad\mathcal{L}_{\mathrm{tsss}-\mathrm{L}}^{\Sigma}\\ \hline
\underline{\text{SHARE}(m_L, m_R \in \Sigma.\mathcal{M},U):}\\
\quad \text{if}\ |U| \geqslant \Sigma . t: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad s \leftarrow \Sigma.\text{Share}(m_L)\\
\quad \text{return} \{s_i | i\in U\}\\\hline
\end{array}
\qquad
\begin{array}{|l|} \hline 
\qquad\qquad\mathcal{L}_{\mathrm{tsss}-\mathrm{R}}^{\Sigma}\\ \hline
\underline{\text{SHARE}(m_L, m_R \in \Sigma.\mathcal{M},U):}\\
\quad \text{if}\ |U| \geqslant \Sigma . t: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad s \leftarrow \Sigma.\text{Share}(m_R)\\
\quad \text{return} \{s_i | i\in U\}\\\hline
\end{array}
$$

*In an attempt to keep the notation uncluttered, we have not written the type of the argument* $U,$ which is $U\subseteq\{1,\ldots,\Sigma.n\}$.

### Discussion & Pitfalls

 - Similar to the definition of one-time secrecy of encryption, we let the calling program choose the two secret messages that will be shared. As before, this models an attack scenario in which the adversary has partial knowledge or influence on the secret $m$ being shared.
 - The calling program also chooses the set $U$ of users’ shares to obtain. The libraries make it impossible for the calling program to obtain the shares of an *authorized set* (returning $\textcolor{brown}{\texttt{err}}$ in that case). This does **not** mean that a user is never allowed to distribute an authorized number of shares (this would be strange indeed, since it would make any future reconstruction impossible). It just means that we want a *security
definition* that says something about an attacker who sees only an unauthorized set of shares, so we formalize security in terms of libraries with this restriction.
 - Consider a 6-out-of-10 threshold secret-sharing scheme. With the libraries above, the calling program can receive the shares of users $\{1,\ldots, 5\}$ (an unauthorized set) in one call to share, and then receive the shares of users $\{6,\ldots, 10\}$ in another call. It might seem like the calling program can then combine these shares to reconstruct the secret (if the same message was shared in both calls). However, this is not the case because these two sets of shares came from two *independent executions* of the Share algorithm. Shares generated by one call to Share should not be expected to function with shares generated by another call, even if both calls to Share used the
same secret message.
 - Recall that in our style of defining security using libraries, it is only the internal *differences* between the libraries that must be hidden. Anything that is the *same* between the two libraries need not be hidden. One thing that is the same for the two libraries here is the fact that they output the shares belonging to the same set of users $U$ . This security definition does not require shares to hide *which user they belong to*. Indeed, you can modify a secret-sharing scheme so that each user’s identity is appended to his/her corresponding share, and the result would still satisfy the security definition above.
 - Just like the encryption definition does not address the problem of key distribution, the secret-sharing definition does not address the problem of who should run the Share algorithm (if its input $m$ is so secret that it cannot be entrusted to any single person), or how the shares should be delivered to the $n$ different users. Those concerns are considered out of scope by the problem of secret-sharing (although we later discuss clever approaches to the first problem). Rather, the focus is simply on whether it is even possible to encode data in such a way that an unauthorized set of shares gives no information about the secret, while any authorized set completely reveals the secret.
 
### An Insecure Approach

One way to understand the security of secret sharing is to see an example of an “obvious” but insecure approach for secret sharing, and study why it is insecure.

Let’s consider a 5-out-of-5 secret-sharing scheme. This means we want to split a secret into 5 pieces so that any 4 of the pieces leak nothing. One way you might think to do this is to *literally chop up the secret* into 5 pieces. For example, if the secret is 500 bits, you might give the first 100 bits to user 1, the second 100 bits to user 2, and so on.

**Construction 3.4 ($\textcolor{brown}{\bf{Insecure}}$ TSSS)**
$$
\def\arraystretch{1.5} 
\begin{array}{|ccc|} \hline 
\begin{aligned}
\mathcal{M}&=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{500}\\
t&=5\\
n&=5
\end{aligned}
 & 
 \begin{array}{l}
 \underline{\text{Share}(m):}\\
 \quad\text{split}\ m\  \text{into}\ m=s_1||\cdots||s_5,\\
 \qquad\text{where each}\ |s_1|=100\\
 \quad\text{return} (s_1,\ldots,s_5)
\end{array}
 & 
 \begin{array}{l}
 \underline{\text{Reconstruct}}(s_1,\ldots, s_5):\\
 \quad \text{return}\ s_1||\cdots||s_5
\end{array}
\\\hline
\end{array}
$$
It is true that the secret can be constructed by concatenating all 5 shares, and so this construction satisfies the correctness property. (The only authorized set is the set of all 5 users, so we write Reconstruct to expect all 5 shares.)

However, the scheme is **insecure** (as promised). Suppose you have even just 1 share. It is true that you don’t know the secret in its *entirety*, but the security definition (for 5- out-of-5 secret sharing) demands that a single share reveals *nothing* about the secret. Of course knowing 100 bits of something is not the same as than knowing *nothing* about it.

We can leverage this observation to make a more formal attack on the scheme, in the form of a distinguisher between the two $\mathcal{L}_{\text{tsss}-\star}$ libraries. As an extreme case, we can distinguish between shares of an all-$\textcolor{brown}{0}$ secret and shares of an all-$\textcolor{brown}{1}$ secret:

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline 
\qquad\qquad\qquad\mathcal{A}\\ \hline
s_1:=\text{SHARE}(\textcolor{brown}{0}^{500},\textcolor{brown}{1}^{500},\{1\})\\
\quad\text{return}\ s_1\stackrel{?}{=}\textcolor{brown}{0}^{100}\\\hline
\end{array}
$$

Let’s link this calling program to both of the $\mathcal{L}_{\text{tsss}-\star}$ libraries and see what happens:

$$
\def\arraystretch{1.5} 
\begin{array}{ccc}
\begin{array}{|l|} \hline 
\qquad\qquad\qquad\mathcal{A}\\ \hline
s_1:=\text{SHARE}(\textcolor{brown}{0}^{500},\textcolor{brown}{1}^{500},\{1\})\\
\quad \text{return}\ s_1\stackrel{?}{=}\textcolor{brown}{0}^{100}\\\hline
\end{array}\diamond\begin{array}{|l|} \hline 
\qquad\qquad\mathcal{L}_{\mathrm{tsss}-\mathrm{L}}\\\hline
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad\text{if}\ |U| \geqslant  t: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad s \leftarrow \text{Share}(\colorbox{yellow}{$m_L$})\\
\quad \text{return} \{s_i | i\in U\}\\\hline
\end{array}
\end{array}
$$

When $\mathcal{A}$ is linked to $\mathcal{L}_{\text{tsss-L}}$, it receives a share of $\textcolor{brown}{0}^{500}$ which will be a string of all ones. In this case, $\mathcal{A}$ outputs 1 with probability 1.

$$
\def\arraystretch{1.5} 
\begin{array}{ccc}
\begin{array}{|l|} \hline 
\qquad\qquad\qquad\mathcal{A}\\ \hline
s_1:=\text{SHARE}(\textcolor{brown}{0}^{500},\textcolor{brown}{1}^{500},\{1\})\\
\quad \text{return}\ s_1\stackrel{?}{=}\textcolor{brown}{0}^{100}\\\hline
\end{array}\diamond\begin{array}{|l|} \hline 
\qquad\qquad\mathcal{L}_{\mathrm{tsss}-\mathrm{R}}\\\hline
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad\text{if}\ |U| \geqslant t: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad s \leftarrow \text{Share}(\colorbox{yellow}{$m_R$})\\
\quad \text{return} \{s_i | i\in U\}\\\hline
\end{array}
\end{array}
$$

When $\mathcal{A}$ is linked to $\mathcal{L}_{tsss-R}$, it receives a share of $\textcolor{brown}{1}^{500}$ which will be a string of all ones. In this case, $\mathcal{A}$ outputs 1 with probability 0.


We have constructed a calling program which behaves very differently (indeed, as differently as possible) in the presence of the two libraries. Hence, this secret-sharing scheme is not secure.

Hopefully this example demonstrates one of the main challenges (and amazing things) about secret-sharing schemes. It is easy to reveal information about the secret *gradually* as more shares are obtained, like in this insecure example. However, the security definition of secret sharing is that the shares must leak *absolutely* no information about the secret,
until the number of shares passes the threshold value.

## 3.2 A Simple 2-out-of-2 Scheme
Believe it or not, we have already seen a simple secret-sharing scheme! In fact, it might even be best to think of one-time pad as the simplest secret-sharing scheme.

**Construction 3.5 (2-out-of-2 TSSS)**
$$
\def\arraystretch{1.5} 
\begin{array}{|ccc|} \hline 
\begin{array}{c}
\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
t=2\\
n=2
\end{array}
 & 
 \begin{array}{l}
 \underline{\text{Share}(m):}\\
\quad s_1\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
\quad s_2:=s_1 \oplus m\\
\quad \text{return} (s_1,s_2)
\end{array}
 & 
 \begin{array}{l}
 \underline{\text{Reconstruct}(s_1, s_2):}\\
 \quad \text{return}\ s_1\oplus s_2
\end{array}
\\\hline
\end{array}
$$
Since it’s a 2-out-of-2 scheme, the only authorized set of users is $\{1, 2\}$, so Reconstruct is written to expect both shares $s_1$ and $s_2$ as its inputs. Correctness follows easily from what we’ve already learned about the properties of XOR.

**Example**
*If we want to share the string $m=\textcolor{brown}{1101010001}$ then the Share algorithm might choose*
$$\begin{array}{lll}s_1 & := & \textcolor{brown}{0110000011}\\
s_2 & := & s_1\oplus m\\
& = & \textcolor{brown}{0110000011}\oplus \textcolor{brown}{1101010001}= \textcolor{brown}{1011010010}.\end{array}$$

*Then the secret can be reconstructed by XORing the two shares together, via:*
$$s_1 \oplus s_2 = \textcolor{brown}{0110000011} \oplus  \textcolor{brown}{1011010010} = \textcolor{brown}{1101010001} =m.$$

*Remember that this example shows just one possible execution of Share $(\textcolor{brown}{1101010001})$, but Share is a randomized algorithm and many other values of $(s_1, s_2)$ are possible.*

**Theorem 3.6**
*Construction 3.5 is a secure 2-out-of-2 threshold secret-sharing scheme.*

**Proof** 
Let $\Sigma$ denote Construction 3.5. We will show that $\mathcal{L}_{\text{tsss-L}}^{\Sigma}=\mathcal{L}_{\text{tsss-R}}^{\Sigma}$ using a hybrid proof.

$$
\def\arraystretch{1.5}
\mathcal{L}_{\text{tsss-L}}^{\Sigma}:\ 
\begin{array}{|l|} \hline 
\qquad\qquad\mathcal{L}_{\mathrm{tsss}-\mathrm{L}}^{\Sigma}\\ \hline
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad\text{if}\ |U| \geqslant 2: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad s_1 \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
\quad s_2:=s_1\oplus m_L\\
\quad \text{return} \{s_i | i\in U\}\\\hline
\end{array}\quad \begin{array}{l}
\text{As usual, the starting point is}\\
\mathcal{L}_{\text{tsss-L}}^\Sigma, \text{shown here with the}\\
\text{details of the secret-sharing}\\
\text{scheme filled in (and the types}\\
\text{of the subroutine arguments}\\
\text{omitted to reduce
clutter).}\end{array}$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad \text{if}\ |U| \geqslant 2: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad \text{if}\ |U| \ = \{1\}:\\
\qquad s_1 \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
\qquad s_2:=s_1\oplus m_L\\
\qquad  \text{return} \{s_1\}\\
\quad \text{elsif}\ U = \{2\}\\
\qquad s_1 \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
\qquad s_2:=s_1\oplus m_L\\
\qquad \text{return} \{s_2\}\\
\quad \text{else return}\ \emptyset\\\hline
\end{array}\quad \begin{array}{l}
\text{It has no effect on the library’s}\\
\text{behavior if we duplicate
the}\\
\text{main body of the
library into 3}\\
\text{branches of
a new if-statement.}\\
\text{The
reason for doing so is that}\\
\text{the scheme generates $s_1$ and
$s_2$}\\
\text{differently. This means
that our}\\
\text{proof will eventually
handle the}\\
\text{3 different
unauthorized sets
}\\
\text{(\{1\}, \{2\},and $\emptyset$) in fundamentally}\\
\text{different
ways.}\end{array}$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad \text{if}\ |U|\geqslant 2: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad \text{if}\ |U| \ = \{1\}:\\
\qquad s_1 \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
\qquad s_2:=s_1\oplus m_R\\
\qquad \text{return} \{s_1\}\\
\quad \text{elsif}\ U = \{2\}\\
\qquad s_1 \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
\qquad s_2:=s_1\oplus m_L\\
\qquad \text{return} \{s_2\}\\
\quad \text{else return}\ \emptyset\\\hline
\end{array}\quad \begin{array}{l}
\text{The definition of $s_2$ has
been changed}\\
\text{in the first
if-branch. This has no effect}\\
\text{on the library’s behavior
since $s_2$ is}\\
\text{never actually
used in this branch.}\\
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad \text{if}\ |U| \geqslant 2: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad \text{if}\ |U| \ = \{1\}:\\
\qquad s_1 \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
\qquad s_2:=s_1\oplus m_R\\
\qquad \text{return} \{s_1\}\\
\quad \text{elsif}\ U = \{2\}\\
\qquad s_2\leftarrow \text{EAVESDROP}(m_L,m_R)\\
\qquad \text{return}\ \{s_2\}\\
\quad \text{else return}\ \emptyset \\\hline
\end{array}\diamond\ \begin{array}{|l|} \hline
\qquad\qquad\mathcal{L}_{\text{ots-L}}^{\text{OTP}} \\ \hline
\underline{\text{EAVESDROP} (m_L,m_R):}\\
\quad k\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^\ell\\
\quad c:= k \oplus m_L\\
\quad\text{return}\ c
 \\\hline
\end{array} \quad \begin{array}{l}
\text{Recognizing the second}\\
\text{branch of the if-statement as}\\
\text{a one-time pad encryption}\\
\text{(of $m_L$ under key $s_1$), we}\\
\text{factor out the generation}\\
\text{of $s_2$ in terms of the library}\\
\text{$\mathcal{L}_{ots-L}^{OTP}$\ from the one-time}\\
\text{secrecy definition. This has}\\
\text{no effect on the library’s}\\
\text{behavior. Importantly, the}\\
\text{subroutine in $\mathcal{L}_{ots-L}^{OTP}$\ expects}\\
\text{two arguments, so that is}\\
\text{what we must pass. We}\\
\text{choose to pass $m_L$ and $m_R$}\\
\text{for reasons that should}\\
\text{become clear very soon.}
\end{array}
$$
$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad\text{if}\ |U| \geqslant 2: \text{return}\ \textcolor{brown}\quad{\texttt{err}}\\
\text{if}\ |U| \ = \{1\}:\\
\quad s_1 \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
\quad s_2:=s_1\oplus m_R\\
\quad\text{return} \{s_1\}\\
\text{elsif}\ U = \{2\}\\
\quad s_2\leftarrow \text{EAVESDROP}(m_L,m_R)\\
\quad \text{return}\ \{s_2\}\\
\text{else return}\ \emptyset \\\hline
\end{array}\diamond\ \begin{array}{|l|} \hline
\qquad \qquad\mathcal{L}_{ots-R}^{OTP} \\\hline
\underline{\text{EAVESDROP} (m_L,m_R):}\\
k\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^\ell\\
c:= k \oplus m_R\\
\text{return}\ c
 \\\hline
\end{array} \quad \begin{array}{l}
\text{We have replaced $\mathcal{L}_{ots-L}^{OTP}$\ with }\\
\text{$\mathcal{L}_{ots-R}^{OTP}$. From the one-time secrecy}\\
\text{of one-time pad (and the}\\
\text{composition lemma), this change}\\
\text{has no effect on the library’s behavior.}\\
\end{array}$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\text{if}\ |U| \geqslant 2: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\text{if}\ |U| \ = \{1\}:\\
\quad s_1 \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
\quad s_2:=s_1\oplus m_R\\
\quad \text{return} \{s_1\}\\
\text{elsif}\ U = \{2\}\\
\quad s_1 \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
\quad s_2:=s_1\oplus m_R\\
\quad \text{return} \{s_2\}\\
\text{else return}\ \emptyset\\\hline
\end{array}\quad \begin{array}{l}
\text{A subroutine has been inlined;}\\
\text{no effect on the library’s
behavior.}\\
\end{array}$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\qquad \quad\mathcal{L}_{tsss-R}^{\Sigma}\\\hline
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad\text{if}\ |U| \geqslant 2: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad s_1 \leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\ell}\\
\quad s_2:=s_1\oplus m_R\\
\quad \text{return} \{s_i\ |\ i \in U\}\\\hline
\end{array}\quad \begin{array}{l}
\text{The code has been simplified.}\\
\text{Specifically, the
branches of the}\\
\text{if-statement
can all be unified, with}\\
\text{no effect
on the library’s behavior.
}\\
\text{The result is $\mathcal{L}_{tsss-R}^\Sigma$}\end{array}$$

We showed that $\mathcal{L}_{tsss-L}^\Sigma \equiv \mathcal{L}_{hyb-1}\equiv \cdots \equiv \mathcal{L}_{hyb-5}\equiv \mathcal{L}_{tsss-R}^\Sigma$, and so the secret-sharing scheme is secure. 

We in fact proved a slightly more general statement. The only property of one-time pad we used was its one-time secrecy. Substituting one-time pad for any other one-time secret encryption scheme would still allow the same proof to go through. So we actually proved the following:

**Theorem 3.7**
*If $\Sigma$ is an encryption scheme with one-time secrecy, then the following 2-out-of-2 threshold secret-sharing scheme $S$ is secure:*

$$
\def\arraystretch{1.5} 
\begin{array}{|ccc|} \hline 
\begin{array}{c}
\mathcal{M}=\Sigma.\mathcal{M}\\
t=2\\
n=2
\end{array}
 & 
 \begin{array}{c}
 \underline{\text{Share}(m):}\\
 s_1\leftarrow \Sigma.\text{KeyGen}\\
 s_2\leftarrow \Sigma.\text{Enc}(s_1,m)\\
 \text{return} (s_1,s_2)
\end{array}
 & 
 \begin{array}{c}
 \underline{\text{Reconstruct}(s_1, s_2):}\\
 \text{return}\Sigma.\text{Dec}(s_1,s_2)
\end{array}
\\\hline
\end{array}
$$

## 3.3 Polynomial Interpolation
You are probably familiar with the fact that two points determine a line (in Euclidean geometry). It is also true that 3 points determine a parabola, and so on. The next secretsharing scheme we discuss is based on the following principle:

$$d+1\ \text{points determine a \textit{unique} degree-d polynomial.}$$

A note on terminology: If f is a polynomial that can be written as $f(x)=\Sigma_{i=0}^d f_ix^i$, then we say that $f$ is a **degree-**$d$ polynomial. It would be more technically correct to say that the degree of $f$ is at most d since we allow the leading coefficient $f_d$ to be zero. For convenience, we’ll stick to saying “degree-$d$” to mean “degree at most $d.$”

### Polynomials Over the Reals

**Theorem 3.8 (Poly Interpolation)**
Let $\{(x_1,y_1),\ldots,(x_{d+1},y_{d+1})\ \subseteq \mathbb{R}^2\}$ be a set of points whose $x_i$ values are all distinct. Then there is a **unique** degree-$d$ polynomial $f$ with real coefficients that satisfies $y_i=f(x_i)$ for all $i$.

**Proof**
To start, consider the following polynomial:

$$\ell_1(x)=\dfrac{(x-x_2)(x-x_3)\cdots(x-x_{d+1})}{(x_1-x_2)(x_1-x_3)\cdots(x_1-x_{d+1})}.$$

The notation is potentially confusing. $\ell_1$ is a polynomial with formal variable $x$ (written in bold). The non-bold $x_i$ values are just plain numbers (scalars), given in the theorem statement. Therefore the numerator in $\ell_1$ is a degree-$d$ polynomial in $x$. The denominator is just a scalar, and since all of the $x_i$'s are distinct, we are not dividing by zero. Overall, $\ell_1$ is a degree-d polynomial.

What happens when we evaluate $\ell_1$ at one of the special $x_i$ values?

 - Evaluating $\ell_1(x_1)$ makes the numerator and denominator the same, so $\ell_1(x_1)=1$
 - Evaluating $\ell_1(x_i)$ for $i\neq 1$ leads to a term $(x_i-x_i)$ in the numerator, so $\ell_1(x_i)=0$

Of course, $\ell_1$ can be evaluated at any point (not just the special points $x_1,\cdots, x_{d+1})$, but we don’t care about what happens in those cases.

We can similarly define other polynomials $\ell_j$:

$$\ell_j(x)=\dfrac{(x-x_1)\cdots (x-x_{j-1})(x-x_{j+1})\cdots (x-x_{d+1})}{(x_j-x_1)\cdots (x_j-x_{j-1})(x_j-x_{j+1})\cdots (x_j-x_{d+1})}.$$

The pattern is that the numerator is “missing” the term $(x - x_j)$ and the denominator is missing the term $(x_j -x_j)$, because we don’t want a zero in the denominator. Polynomials of this kind are called **LaGrange polynomials**. They are each degree-$d$ polynomials, and they satisfy the property:

$$
\ell_j(x_i)=\left\{\begin{array}{l}
1\quad \text{if}\ i=j\\
0\quad \text{if}\ i\neq j
\end{array}\right.
$$

Now consider the following polynomial:

$$f(x)=y_1\ell_1(x)+y_2\ell_2(x)+\cdots+y_{d+1}\ell_{d+1}(x).$$

Note that $f$ is a degree-$d$ polynomial since it is the sum of degree-$d$ polynomials (again, the $y_i$ values are just scalars).

What happens when we evaluate $f$ on one of the special $x_i$ values? Since $\ell_i(x_i)=1$ and $\ell_j(x_i)=0$ for $j\neq i$, we get:

$$
\begin{aligned}
f\left(x_{i}\right) &=y_{1} \ell_{1}\left(x_{i}\right)+\cdots+y_{i} \ell_{i}\left(x_{i}\right)+\cdots+y_{d+1} \ell_{d+1}\left(x_{i}\right) \\
&=y_{1} \cdot 0+\cdots+y_{i} \cdot 1+\cdots+y_{d+1} \cdot 0 \\
&=y_{i}
\end{aligned}
$$

so $f(x_i)=y_i$ for every $x_i$, which is what we wanted. This shows that there is some degree-$d$ polynomial with this property.

Now let’s argue that this $f$ is unique. Suppose there are two degree-$d$ polynomials $f$ and $f'$ such that $f(x_i)=f'(x_i)=y_i$ for $i\in\{1,\ldots, d+1\}$. Then the polynomial $g(x)=f(x)-f'(x)$ also is degree-$d$, and it satisfies $g(x_i)=0$ for all $i$. In other words, each $x_i$ is a root of $g$, so $g$ has at least $d + 1$ roots. But the only degree-$d$ polynomial with
$d + 1$ roots is the identically-zero polynomial $g(x)=0$. If $g(x)=0$ then $f=f'$.  In other words, any degree-$d$ polynomial $f'$ that satisfies $f'(x_i)=y_i$ must be equal to $f$. So $f$ is the unique polynomial with this property.

**Example**
*Let’s figure out the degree-3 polynomial that passes through the points
(3, 1), (4, 1), (5, 9), (2, 6):*
$$
\begin{array}{c|cccc}
i & 1 & 2 & 3 & 4 \\
\hline x_{i} & 3 & 4 & 5 & 2 \\
y_{i} & 1 & 1 & 9 & 6
\end{array}
$$

*First, let’s construct the appropriate LaGrange polynomials:*

$$
\begin{array}{l}
\ell_{1}(x)=\dfrac{\left(x-x_{2}\right)\left(x-x_{3}\right)\left(x-x_{4}\right)}{\left(x_{1}-x_{2}\right)\left(x_{1}-x_{3}\right)\left(x_{1}-x_{4}\right)}=\dfrac{(x-4)(x-5)(x-2)}{(3-4)(3-5)(3-2)}=\dfrac{x^{3}-11 x^{2}+38 x-40}{2} \\\\
\ell_{2}(x)=\dfrac{\left(x-x_{1}\right)\left(x-x_{3}\right)\left(x-x_{4}\right)}{\left(x_{2}-x_{1}\right)\left(x_{2}-x_{3}\right)\left(x_{2}-x_{4}\right)}=\dfrac{(x-3)(x-5)(x-2)}{(4-3)(4-5)(4-2)}=\dfrac{x^{3}-10 x^{2}+31 x-30}{-2} \\\\
\ell_{3}(x)=\dfrac{\left(x-x_{1}\right)\left(x-x_{2}\right)\left(x-x_{4}\right)}{\left(x_{3}-x_{1}\right)\left(x_{3}-x_{2}\right)\left(x_{3}-x_{4}\right)}=\dfrac{(x-3)(x-4)(x-2)}{(5-3)(5-4)(5-2)}=\dfrac{x^{3}-9 x^{2}+26 x-24}{6} \\\\
\ell_{4}(x)=\dfrac{\left(x-x_{1}\right)\left(x-x_{2}\right)\left(x-x_{3}\right)}{\left(x_{4}-x_{1}\right)\left(x_{4}-x_{2}\right)\left(x_{4}-x_{3}\right)}=\dfrac{(x-3)(x-4)(x-5)}{(2-3)(2-4)(2-5)}=\dfrac{x^{3}-12 x^{2}+47 x-60}{-6}
\end{array}
$$

*As a sanity check, notice how:*
$$
\begin{array}{l}
\ell_{1}\left(x_{1}\right)=\ell_{1}(3)=\dfrac{3^{3}-11 \cdot 3^{2}+38 \cdot 3-40}{2}=\dfrac{2}{2}=1 \\\\
\ell_{1}\left(x_{2}\right)=\ell_{1}(4)=\dfrac{4^{3}-11 \cdot 4^{2}+38 \cdot 4-40}{2}=\dfrac{0}{2}=0
\end{array}
$$

*It will make the next step easier if we rewrite all LaGrange polynomials to have the same denominator 6:*
$$
\begin{array}{ll}
\ell_{1}(x)=\dfrac{3 x^{3}-33 x^{2}+114 x-120}{6} & \ell_{3}(x)=\dfrac{x^{3}-9 x^{2}+26 x-24}{6} \\
\ell_{2}(x)=\dfrac{-3 x^{3}+30 x^{2}-93 x+90}{6} & \ell_{4}(x)=\dfrac{-x^{3}+12 x^{2}-47 x+60}{6}
\end{array}
$$
*Our desired polynomial is*
$$
\begin{aligned}
f(x) &=y_{1} \cdot \ell_{1}(x)+y_{2} \cdot \ell_{2}(x)+y_{3} \cdot \ell_{3}(x)+y_{4} \cdot \ell_{4}(x) \\\\
&=1 \cdot \ell_{1}(x)+1 \cdot \ell_{2}(x)+9 \cdot \ell_{3}(x)+6 \cdot \ell_{4}(x)\\\\
& =\dfrac{1}{6}\left(\begin{array}{rcr}
 1 &\cdot & (3x^3-33x^2+114x-120)\\
+1&\cdot & (\ -3x^3+30x^2-93x+90)\\
+9&\cdot&(\ \ \ \ \ \ \ \ x^3-9x^2+26x-24)\\
+6&\cdot&(\ \ \ -x^3+12x^2-47x+60)
\end{array}\right)\\\\
& =\dfrac{1}{6}(3x^3-12x^2-27x+114)\\\\
&=\dfrac{x^3}{2}-2x^2-\dfrac{9x}{2}+19
\end{aligned}
$$

*And indeed, $f$ gives the correct values:*

$$
\textcolor{red}{\text{Image screenshot here}}
$$

$$
\begin{array}{l}
f\left(x_{1}\right)=f(3)=\dfrac{3^{3}}{2}-2 \cdot 3^{2}-\dfrac{9 \cdot 3}{2}+19=1=y_{1} \\\\
f\left(x_{2}\right)=f(4)=\dfrac{4^{3}}{2}-2 \cdot 4^{2}-\dfrac{9 \cdot 4}{2}+19=1=y_{2} \\\\
f\left(x_{3}\right)=f(5)=\dfrac{5^{3}}{2}-2 \cdot 5^{2}-\dfrac{9 \cdot 5}{2}+19=9=y_{3} \\\\
f\left(x_{4}\right)=f(2)=\dfrac{2^{3}}{2}-2 \cdot 2^{2}-\dfrac{9 \cdot 2}{2}+19=6=y_{4}
\end{array}
$$

### Polynomials mod $p$
We will see a secret-sharing scheme based on polynomials, whose Share algorithm must choose a polynomial with uniformly random coefficients. Since we cannot have a uniform distribution over the real numbers, we must instead consider polynomials with coefficients in $\mathbb{Z}_p$.

It is still true that $d +1$ points determine a unique degree-$d$ polynomial when working modulo $p$, if $p$ is a prime!

**Theorem 3.9 (Interp mod $p$)**
*$\colorbox{yellow}{Let \textit{p} be a prime,}$* *and let*$\{(x_1,y_1),\ldots,(x_{d+1},y_{d+1})\}\subseteq (\mathbb{Z}_p)^2$ be a set of points whose $x_i$ values are all distinct. Then there is a unique degree-$d$ polynomial $f$ with coefficients from $\mathbb{Z}_p$ that satisfies $y_i\equiv_pf(x_i)$ for all $i$.

The proof is the same as the one for Theorem 3.8, if you interpret all arithmetic modulo $p$. Addition, subtraction, and multiplication mod $p$ are straight forward; the only nontrivial question is how to interpret “division mod $p$,” which is necessary in the definition of the $\ell_j$ polynomials. For now, just accept that you can always “divide” mod $p$ (except by zero) when $p$ is a prime. If you are interested in how division mod $p$ works, look ahead to Chapter 13.

We can also generalize the observation that $d +1$ points uniquely determine a degree-$d$ polynomial. It turns out that:

For any $k$ points, there are exactly $p^{d+1-k}$ polynomials of degree-$d$ that hit those points, mod $p$.

Note how when $k = d +1$, the statement says that there is just a single polynomial hitting the points.

**Corollary 3.10 (# of polys)**
Let $\mathcal{P}=\{(x_1,y_1),\ldots,(x_k,y_k)\}\subseteq (\mathbb{Z}_p)^2$ be a set of points whose $x_i$ values are distinct. Let $d$ satisfy $k\leqslant d+1$ and $p>d$. Then the number of degree-$d$ polynomials $f$ with coefficients in $\mathbb{Z}_p$ that satisfy the condition $y_i\equiv_pf(x_i)$ for all $i$ is exactly $p^{d+1-k}$.

**Proof**
The proof is by induction on the value $d +1-k$. The base case is when $d+1-k=0$. Then we have $k = d + 1$ distinct points, and Theorem 3.9 says that there is a *unique* polynomial satisfying the condition. Since $p^{d+1-k}=p^0=1$, the base case is true.

For the inductive case, we have $l\leqslant d$ points in $\mathcal{P}$. Let $x^*\in \mathbb{Z}_p$ be a value that does not appear as one of the $x_i$’s. Every polynomial must give some value when evaluated at $x^*$. So,

[# of degree-d polynomials passing through points in P]
$$
\begin{aligned}
&=\sum_{y^{*} \in Z_{p}}\left[\# \text { of degree- } d \text { polynomials passing through points in } \mathcal{P} \cup\left\{\left(x^{*}, y^{*}\right)\right\}\right]\\
&\stackrel{(\star)}{=} \sum_{y^{*} \in Z_{p}} p^{d+1-(k+1)}\\
&=p \cdot\left(p^{d+1-k-1}\right)=p^{d+1-k}
\end{aligned}
$$
The equality marked ($\star$) follows from the inductive hypothesis, since each of the terms involves a polynomial passing through a specified set of $k + 1$ points with distinct $x$ coordinates.

**Example**
$$
\textcolor{red}{\text{Image screenshot here}}
$$
***What does a “polynomial mod $p$” look like?** Consider an example degree-2 polynomial:*

$$f(x)=x^2+4x+7$$

*When we plot this polynomial over the real numbers (the picture on the left), we get a familiar parabola.*

*Let’s see what this polynomial “looks like” modulo 11 (i.e., in $\mathbb{Z}_{11}$). Working mod 11 means to “wrap around” every time the polynomial crosses over a multiple of 11 along the y-axis. This results in the blue plot below:*

$$
\textcolor{red}{\text{Image screenshot here}}
$$

This is a picture of a mod-11 parabola. In fact, since we care only about $\mathbb{Z}_{11}$ inputs to $f$, you could rightfully say that **just the 11 highlighted points alone** (not the blue curve) are a picture of a mod-11 parabola.

## 3.4 Shamir Secret Sharing
Part of the challenge in designing a secret-sharing scheme is making sure that *any* authorized set of users can reconstruct the secret. We have just seen that *any* $d + 1$ points on a degree-d polynomial are enough to uniquely reconstruct the polynomial. So a natural approach for secret sharing is to let each user’s share be a point on a polynomial.

That’s exactly what **Shamir secret sharing** does. To share a secret $m \in \mathbb{Z}_p$ with threshold $t$ , first choose a degree-$(t - 1)$ polynomial f that satisfies $f (0) \equiv_p m$, with all other coefficients chosen uniformly in $\mathbb{Z}_p$ . The $i$th user receives the point $(i, f(i) \% p)$ on the polynomial. The interpolation theorem says that any $t$ of the shares can uniquely determine the polynomial $f$ , and hence recover the secret $f (0)$.

**Construction 3.11 (Shamir SSS)**

$$
\def\arraystretch{1.5} 
\begin{array}{|ccc|} \hline 
\begin{array}{c}
\mathcal{M}=\mathbb{Z}_p\\
p:prime\\
n<p\\
t\leqslant n
\end{array}
 & 
 \begin{array}{l}
 \underline{\text{Share}(m):}\\
 f_1,\ldots,f_{t-1}\leftarrow \mathbb{Z}_p\\
 f(x):=m+\Sigma_{j=1}^{t-1}f_jx^j\\
 \text{for}\ i=1\ \text{to}\ n:\\
 \quad s_i:=(i,f(i)\% p)\\
 \text{return}\ s=(s_1,\ldots,s_n)\\\\
 \underline{\text{Reconstruct}(\{s_i\ |\ i\in U\}):}\\
 f(x):= \text{unique degree-(t- 1)}\\
 \qquad \quad \ \ \text{polynomial mod p passing}\\
  \qquad \quad \ \ \text{through points}\{s_i\ |\ i\in U\}\\
  \text{return}\ f(0)
\end{array}
\\\hline
\end{array}
$$

Correctness follows from the interpolation theorem.

**Example**
*Here is an example of 3-out-of-5 secret sharing over $\mathbb{Z}_{11}$ (so $p = 11$). Suppose the secret being shared is $m=7 \in \mathbb{Z}_{11}$. The Share algorithm chooses a random degree-2 polynomial with
constant coecient 7.*

*Let’s say that the remaining two coecients are chosen as $f_2 = 1$ and $f_1 = 4$, resulting in the following polynomial:*

$$f(x)=1x^2+4x+7$$

*This is the same polynomial illustrated in the previous example:*

$$
\textcolor{red}{\text{Image screenshot here}}
$$

*For each user $i \in \{1,\ldots, 5\}$, we distribute the share $(i, f(i)\%11).$  These shares correspond to the highlighted points in the mod-11 picture above.*

$$
\begin{array}{ccc}
\text { user }(i) & f(i) & \operatorname{share}(i, f(i) \% 11) \\
\hline 1 & f(1)=12 & (1,1) \\
2 & f(2)=19 & (2,8) \\
3 & f(3)=28 & (3,6) \\
4 & f(4)=39 & (4,6) \\
5 & f(5)=52 & (5,8)
\end{array}
$$
*Remember that this example illustrates just one possible execution of Share. Because Share is a randomized algorithm, there are many valid sharings of the same secret (induced by dierent choices of the highlighted coecients in $f$ ).*

### Security
To show the security of Shamir secret sharing, we first show a convenient lemma about the distribution of shares in an unauthorized set:

**Lemma 3.12**
Let $p$ be a prime and define the following two libraries:

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\qquad \qquad\mathcal{L}_{\text{hamir-real}}\\\hline
\underline{\text{POLY}(m,t,U\subseteq \{1,\ldots, p\}):}\\
\quad\text{if}\ |U|\geqslant t:\text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad f_1,\ldots,f_{t-1}\leftarrow \mathbb{Z}_p\\
\quad f(x):=m+\Sigma_{j=1}^{t-1}f_jx^j\\
\quad \text{for}\ i\in U:\\
\quad\quad s_i:=(i,f(i)\% p)\\
\quad\text{return}\{s_i\ | i\in U\}  \\\hline
\end{array}\quad\ \begin{array}{|l|} \hline
\qquad \qquad\mathcal{L}_{\text{hamir-rand}}\\\hline
\underline{\text{POLY}(m,t,U\subseteq \{1,\ldots, p\}):}\\
\quad\text{if}\ |U|\geqslant t: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad\text{for}\ i\in U:\\
\quad y_i\leftarrow \mathbb{Z}_p\\
\quad\quad s_i:=(i,y_i)\\
\quad\text{retuen}\ \{s_i | i\in U\}
 \\\hline
\end{array} $$

*$\mathcal{L}_{\text{shamir-real}}$ chooses a random degree-$(t-1)$ polynomial that passes through the point $(0,m)$, then evaluates it at the given $x$-coordinates (specified by $U$ ). $\mathcal{L}_{\text{shamir-rand}}$ simply gives uniformly chosen points, unrelated to any polynomial.*

*The claim is that these libraries are interchangeable: $\mathcal{L}_{\text{shamir-real}}\equiv$$\mathcal{L}_{\text{shamir-rand}}$.*

**Proof**
Fix a message $m\in \mathbb{Z}_p$, fix set $U$ of users with $|U|<t, and for each $i\in U$ fix a value $y_i\in \mathbb{Z}_p$. We wish to consider the probability that a call to $\text{POLY}(m,t,U)$ outputs $\{(i,y_i)|i\in U\}$ in each of the two libraries.[^3-2]
[^3-2]: This is similar to how, in Claim 2.7, we fixed a particular m and c and computed the probability that EAVESDROP $(m)=c$

In library $\mathcal{L}_{\text{shamir-real}}$, the subroutine chooses a random degree-$(t-1)$ polynomial $f$ such that $f(0)\equiv_p m$. From Corollary 3.10, we know there are $p^{t-1}$ such polynomials.
In order for POLY to output points consistent with our chosen $y_i$'s the library must have chosen one of the polynomials that passes through $(0,m)$ and all of the $\{(i,y_i)\ | i\in U\}$ points. The library must have chosen one of the polynomials that passes through a specific choice of $|U|+1$ points, and Corollary 3.10 tells us that there are $p^{t-(|U|+1)}$ such polynomials.

The only way for poly to give our desired output is for it to choose one of the $p^{t-(|U|+1)}$ “good” polynomials, out of the $p^{t-1}$ possibilities.  This happens with probability exactly 

$$
\frac{p^{t-|U|-1}}{p^{t-1}}=p^{-|U|}
$$
Now, in library $\mathcal{L}_{\text{shamir-rand}}$, POLY chooses its $|U|$ output values uniformly in $\mathbb{Z}_p$. There are $p^{|U|}$ ways to choose them. But only one of those ways causes POLY$(m,t,U)$ to output our specific choice of $\{(i,y_i)|i\in U\}$.  Hence, the probability of receiving this output is $p^{-|U|}$.

For all possible inputs to POLY, both libraries assign the same probability to every possible output. Hence, the libraries are interchangeable.

**Theorem 3.13**
Shamir’s secret-sharing scheme (Construction 3.11) is secure according to Denition 3.3.

**Proof**
Let $\mathcal{S}$ denote the Shamir secret-sharing scheme. We prove that $\mathcal{L}_{tsss-L}^\mathcal{S}\equiv$$\mathcal{L}_{tsss-R}^\mathcal{S}$ via a hybrid argument.

$$
\def\arraystretch{1.5}
\mathcal{L}_{tsss-L}^{\Sigma}:\ 
\begin{array}{|l|} \hline 
\qquad \qquad\mathcal{L}_{\mathrm{tsss}-\mathrm{L}}^{\Sigma}\\\hline
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad\text{if}\ |U| \geqslant t: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad f_1,\ldots,f_{t-1}\leftarrow \mathbb{Z}_p\\
\quad f(x):=m_L+\sum_{j=1}^{t-1}f_jx^j\\
\quad \text{for}\ i\in U:\\
\quad\quad s_i:=(i,f(i)\%p)\\
\quad\text{return}\ \{s_i|i\in U\}\\\hline
\end{array}\quad \begin{array}{l}
\text{Our starting point is $\mathcal{L}_{tsss-L}^\mathcal{S}$, }\\
\text{shown here with the details of}\\
\text{Shamir secret-sharing filled in.}\\
\end{array}$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad\text{return \colorbox{yellow}{POLY}}(m_L,t,U)\\\hline
\end{array}\diamond\ \begin{array}{|l|} \hline 
\quad \quad\mathcal{L}_{shamir-real}\\\hline
\underline{\text{POLY}(m, t ,U):}\\
\quad\text{if}\ |U|\geqslant t: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad f_1,\ldots,f_{t-1}\leftarrow \mathbb{Z}_p\\
\quad f(x):=m+\sum_{j=1}^{t-1}f_jx^j\\
\quad\text{for}\ i\in U:\\
\quad\quad s_i:=(i,f(i)\%p)\\
\quad\text{return}\ \{s_i|i\in U\}\\\hline
\end{array} \quad \begin{array}{l}
\text{Almost the entire body of }\\
\text{the share subroutine has}\\
\text{been factored out in terms}\\
\text{of the $\mathcal{L}_{shamir-real}$ library}\\
\text{defined above. The only thing}\\
\text{remaining is the “choice” of}\\
\text{whether to share $m_L$ or $m_R$.}\\
\text{Restructuring the code in}\\
\text{this way has no effect on the}\\
\text{library’s behavior.}\\
\end{array}$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad\text{return \colorbox{yellow}{POLY}}(m_L,t,U)\\\hline
\end{array}\diamond\ \begin{array}{|l|} \hline 
\quad \quad\mathcal{L}_{shamir-rand}\\\hline
\underline{\text{POLY}(m, t ,U):}\\
\quad\text{if}\ |U| \geqslant t: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad\text{for}\ i\in U:\\
\quad y_i\leftarrow\mathbb{Z}_p\\
\quad\quad s_i:=(i,y_i)\\
\quad\text{return}\ \{s_i|i\in U\}\\\hline
\end{array} \quad \begin{array}{l}
\text{By Lemma 3.12, we can replace }\\
\text{$\mathcal{L}_{shamir-real}$ with $\mathcal{L}_{shamir-rand}$}\\
\text{having no effect on the library’s}\\
\text{behavior.}\\
\end{array}$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad\text{return \colorbox{yellow}{POLY}}(m_R,t,U)\\\hline
\end{array}\diamond\ \begin{array}{|l|} \hline 
\quad\quad\mathcal{L}_{shamir-rand}\\\hline
\underline{\text{POLY}(m, t ,U):}\\
\quad\text{if}\ |U| \geqslant t: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad\text{for}\ i\in U:\\
\quad y_i\leftarrow\mathbb{Z}_p\\
\quad\quad s_i:=(i,y_i)\\
\quad\text{return}\ \{s_i|i\in U\}\\\hline
\end{array} \quad \begin{array}{l}
\text{The argument to poly has }\\
\text{been changed from $m_L$ to $m_R$.}\\
\text{This has no effect on the library’s}\\
\text{behavior, since poly is actually}\\
\text{ignoring its argument in these}\\
\text{hybrids.}
\end{array}$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad\text{return POLY}(m_R,t,U)\\\hline
\end{array}\diamond\ \begin{array}{|l|} \hline 
\quad\quad\mathcal{L}_{shamir-real}\\\hline
\underline{\text{POLY}(m, t ,U):}\\
\quad\text{if}\ |U| \geqslant t: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad f_1,\ldots,f_{t-1}\leftarrow \mathbb{Z}_p\\
\quad f(x):=m+\sum_{j=1}^{t-1}f_jx^j\\
\quad \text{for}\ i\in U:\\
\quad\quad s_i:=(i,f(i)\%p)\\
\quad\text{return}\ \{s_i|i\in U\}\\\hline
\end{array} \quad \begin{array}{l}
\text{Applying the same steps}\\
\text{in reverse, we can replace}\\
\text{$\mathcal{L}_{shamir-rand}$ with $\mathcal{L}_{shamir-real}$,}\\
\text{having no effect on the}\\
\text{library’s behavior.}\\
\end{array}$$

$$
\def\arraystretch{1.5}
\mathcal{L}_{tsss-R}^{\mathcal{S}}:\ 
\begin{array}{|l|} \hline 
\quad \quad \qquad\mathcal{L}_{\mathrm{tsss}-R}^{\Sigma}\\\hline
\underline{\text{SHARE}(m_L, m_R ,U):}\\
\quad\text{if}\ |U| \geqslant t: \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad f_1,\ldots,f_{t-1}\leftarrow \mathbb{Z}_p\\
\quad f(x):=m_R+\sum_{j=1}^{t-1}f_jx^j\\
\quad\text{for}\ i\in U:\\
\quad\quad s_i:=(i,f(i)\%p)\\
\quad\text{return}\ \{s_i|i\in U\}\\\hline
\end{array}\quad \begin{array}{l}
\text{A subroutine has been inlined,}\\
\text{which has no effect on the library’s}\\
\text{behavior. The resulting library}\\
\text{is $\mathcal{L}_{tsss-R}^\mathcal{S}$.}\\
\end{array}$$

We showed that $\mathcal{L}_{tsss-L}^{\mathcal{S}}\equiv\mathcal{L}_{hyb-1}\equiv\cdots\equiv\mathcal{L}_{hyb-4}\equiv\mathcal{L}_{tsss-R}^{\mathcal{S}}$, so Shamir’s secret sharing scheme is secure.

## $\star$ 3.5 Visual Secret Sharing
Here is a fun variant of 2-out-of-2 secret-sharing called visual secret sharing. In this variant, both the secret and the shares are black-and-white images. We require the same security property as traditional secret-sharing — that is, a single share (image) by itself reveals no information about the secret (image). What makes visual secret sharing different is that we require the *reconstruction* procedure to be done visually.

More specifically, each share should be printed on transparent sheets. When the two shares are stacked on top of each other, the secret image is revealed visually. We will discuss a simple visual secret sharing scheme that is inspired by the following observations:

Importantly, when stacking shares on top of each other in the rst two cases, the result is a $2 \times 2$ block that is half-black, half-white (let’s call it “gray”); while in the other cases the result is completely black.

The idea is to process each pixel of the source image independently, and to encode each pixel as a $2\times 2$ block of pixels in each of the shares. A white pixel should be shared in a way that the two shares stack to form a “gray” $2 \times 2$ block, while a black pixel is shared in a way that results in a black $2 \times 2$ block.

More formally:

**Construction 3.14**
It is not hard to see that share s1 leaks no information about the secret image $m$, because it consists of uniformly chosen $2 \times 2$ blocks. In the exercises you are asked to prove that $s_2$ also individually leaks nothing about the secret image.

Note that whenever the source pixel is white, the two shares have identical $2\times2$ blocks (so that when stacked, they make a “gray” block). Whenever a source pixel is black, the two shares have opposite blocks, so stack to make a black block.

**Example**

$$
\textcolor{red}{\text{Image screenshot here}}
$$

### Exercises

 

3.1. Generalize Construction 3.5 to be an n-out-of-n secret-sharing scheme, 	and prove that your scheme is correct and secure.

3.2. Prove Theorem 3.7.

3.3. Fill in the details of the following alternative proof of Theorem 3.6: Starting with $\mathcal{L}_{tsss-L}$, apply the first step of the proof as before, to duplicate the main body into 3 branches of a new if-statement. Then apply Exercise 2.3 to the second branch of the if-statement. Argue that $m_L$ can be replaced with $m_R$ and complete the proof.

3.4. Suppose $T$ is a fixed (publicly known) invertible $n \times n$ matrix over $\mathbb{Z}_p$ , where $p$ is a prime. 

 (a)  Show that the following two libraries are interchangeable:
 $$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\quad\ \ \ \mathcal{L}_{\text{left}}\\\hline
\underline{\text{QUERY}():}\\
\quad r\leftarrow (\mathbb{Z}_p)^n\\
\quad\text{return}\ r\\\hline
\end{array}\ ;\ \begin{array}{|l|} \hline 
\quad\ \ \ \mathcal{L}_{\text{right}}\\\hline
\underline{\text{QUERY}():}\\
\quad r\leftarrow (\mathbb{Z}_p)^n\\
\quad \text{return}\ T \times r\\\hline
\end{array}\ .$$
$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\quad\ \ \ \ \mathcal{L}_{\text{left}}\\\hline
\underline{\text{QUERY}():}\\
\quad r\leftarrow (\mathbb{Z}_p)^n\\
\quad\text{return}\ r\\\hline
\end{array}\ ;\ \begin{array}{|l|} \hline 
\quad\ \ \ \mathcal{L}_{\text{right}}\\\hline
\underline{\text{QUERY}():}\\
\quad r\leftarrow (\mathbb{Z}_p)^n\\
\quad \text{return}\ T \times r\\\hline
\end{array}\ .$$
(b) Show that the following two libraries are interchangeable:
$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline 
\quad \qquad\mathcal{L}_{\text{left}}\\\hline
\underline{\text{QUERY}(v \in (\mathbb{Z}_p)^n):}\\
\quad r\leftarrow (\mathbb{Z}_p)^n\\
\quad z:=v+Tr\\
\quad\text{return}\ z\\\hline
\end{array}\ ;\ \begin{array}{|l|} \hline 
\quad \qquad\mathcal{L}_{\text{right}}\\\hline
\underline{\text{QUERY}(v \in (\mathbb{Z}_p)^n):}\\
\quad r\leftarrow (\mathbb{Z}_p)^n\\
\quad\text{return}\ z\\\hline
\end{array}\ .$$
3.5. Consider a *t*-out-of-*n* threshold secret sharing scheme with $\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^\ell$, and where each user’s share is also a string of bits. Prove that if the scheme is secure, then every user’s share must be at least $\ell$ bits long.
>**Hint:** Prove the contrapositive. Suppose the first user’s share is less than $\ell$ bits (and that this fact is known to everyone). Show how users 2 through $t$ can violate security by enumerating all possibilities for the first user’s share. Give your answer in the form of an distinguisher on the relevant libraries.

3.6. $n$ users have shared two secrets using Shamir secret sharing. User $i$ has a share $s_i=(i,y_i)$ of the secret $m$, and a share $s_i'=(i,y_i')$  of the secret $m'$. Both sets of shares use the same prime modulus $p$.

  Suppose each user $i$ locally computes $z_i=(y_i+y_i')\%p$.
  
 (a) Prove that if the shares of $m$ and shares of $m'$ had the same threshold, then the resulting $\{(i,z_i)|i\leqslant n\}$ are a valid secret-sharing of the secret $m+m'.$
 
 (b) Describe what the users get when the shares of $m$ and $m'$ had different thresholds (say, $t$ and $t'$, respectively).
 
3.7. Suppose there are 5 people on a committee: Alice (president), Bob, Charlie, David, Eve. Suggest how they can securely share a secret so that it can only be opened by:

 $\bullet\quad$Alice and any one other person
  $\bullet\quad$Any three people
  
 Describe in detail how the sharing algorithm works and how the reconstruction works (for all authorized sets of users).
 
 Note: It is fine if different users have shares which are of different sizes (e.g., different number of bits to represent), and it is also fine if the Reconstruct algorithm depends on the identities of the users who are contributing their shares.
 
3.8. Suppose there are 9 people on an important committee: Alice, Bob, Carol, David, Eve, Frank, Gina, Harold, & Irene. Alice, Bob & Carol form a subcommittee; David, Eve & Frank form another subcommittee; and Gina, Harold & Irene form another subcommittee.

  Suggest how a dealer can share a secret so that it can only be opened when a majority of each subcommittee is present. Describe why a 6-out-of-9 threshold secret-sharing scheme does not suffice.
$$
\textcolor{red}{\text{Image screenshot here}}
$$
3.9. (a) Generalize the previous exercise. A monotone formula is a boolean function $\phi:\{0,1\}^n\rightarrow\{0,1\}$ that when written as a formula uses only and and or operations (no NOTS). For a set $A\subseteq\{1,\ldots,n\}$, let $\mathcal{X}_A$ be the bitstring where whose $i$th bit is 1 if and only if $i\in A$.

 For every monotone formula $\phi:\{0,1\}^n\rightarrow \{0,1\}$, construct a secret-sharing scheme whose authorized sets are $\{A\subseteq \{1,\ldots,n\}|\phi(\mathcal{X}_A)=1\}$.  Prove that your scheme is secure.
 
(b) Give a construction of a *t*-out-of-*n* secret-sharing scheme in which all shares are binary strings, and the only operation required of Share and Reconstruct is xor (so no mod-$p$ operations).

How big are the shares, compared to the Shamir scheme?

3.10. Prove that share $s_2$ in Construction 3.14 is distributed independently of the secret $m$.

3.11. Using actual transparencies or with an image editing program, reconstruct the secret shared in these two images:

 $$
\textcolor{red}{\text{Image screenshot here}}
$$

3.12. Construct a 3-out-of-3 visual secret sharing scheme. Any two shares should together reveal nothing about the source image, but any three reveal the source image when stacked together.

# 4. Basing Cryptography on Intractable Computations
John Nash was a mathematician who earned the 1994 Nobel Prize in Economics for his work in game theory. His life story was made into a successful movie, A *Beautiful Mind*.

In 1955, Nash was in correspondence with the United States National Security Agency (NSA),[^3] discussing new methods of encryption that he had devised. In these letters, he also proposes some general principles of cryptography (bold highlighting not in the original):
[^3]: The original letters, handwritten by Nash, are available at: [https://www.nsa.gov/Portals/70/documents/news-features/declassified-documents/nash-letters/nash_letters1.pdf.](https://www.nsa.gov/Portals/70/documents/news-features/declassified-documents/nash-letters/nash_letters1.pdf.)

>$\dots$ in principle the enemy needs very little information to begin to break down the process. Essentially, as soon as $\lambda$ bits[^4] of enciphered message have been transmitted the key is about determined. This is no security, for a practical key should not be too long. **But this does not consider how easy or difficult it is for the enemy to make the computation determining the key. If this computation, although possible in principle, were suficiently long at best then the process could still be secure in a practical sense.**

[^4]: Nash originally used r to denote the length of the key, in bits. In all of the excerpts quoted in this chapter, I have translated his mathematical expressions into our notation ($\lambda$).

Nash is saying something quite profound: **it doesn’t really matter whether attacks are impossible, only whether attacks are computationally infeasible**. If his letters hadn’t been kept classified until 2012, they might have accelerated the development of “modern” cryptography, in which security is based on intractable computations. As it stands, he was decades ahead of his time in identifying one of the most important concepts in modern cryptography.

## 4.1 What Qualifies as a “Computationally Infeasible” Attack?

Schemes like one-time pad cannot be broken, even by an attacker that performs a **bruteforce** attack, trying all possible keys (see Exercise 1.5). However, all future schemes that we will see can indeed be broken by such an attack. Nash is quick to point out that, for a scheme with $\lambda-$ bit keys:
>The most direct computation procedure would be for the enemy to try all $2^\lambda$ possible keys, one by one. Obviously this is easily made impractical for the enemy by simply choosing  $\lambda$  large enough.

We call $\lambda$ the **security parameter** of the scheme. It is like a knob that allows the user to tune the security to any desired level. Increasing $\lambda$ makes the difficulty of a bruteforce attack grow exponentially fast. Ideally, when using $\lambda$-bit keys, *every* attack (not just a brute-force attack) will have diculty roughy $2^\lambda$. However, sometimes faster attacks are inevitable. Later in this chapter, we will see why many schemes with $\lambda$-bit keys have attacks that cost only $2^{\lambda/2}$. It is common to see a scheme described as having **$n$-bit security** if the best known attack requires $2^n$ steps.

Just how impractical is a brute-force computation on a 64-bit key? A 128-bit key? Huge numbers like $2^{64}$ and $2^{128}$ are hard to grasp at an intuitive level.

**Example**
It can be helpful to think of the cost of a computation in terms of monetary value, and a convenient way to assign such monetary costs is to use the pricing model of a cloud computing provider. Below, I have calculated roughly how much a computation involving $2^\lambda$ CPU cycles
would cost on Amazon EC2, for various choices of $\lambda$.[^5]
[^5]: As of October 2018, the cheapest class of CPU that is suitable for an intensive computation is the m5.large, which is a 2.5 GHz CPU. Such a CPU performs $2^{43}$ clock cycles per hour. The cheapest rate on
EC2 for this CPU is 0.044 USD per hour (3-year reserved instances, all costs paid upfront). All in all, the cost for a single clock cycle (rounding down) is $2^{-48}$ USD.

$$
\begin{array}{cll}
clock\ cycles & approx\ cost & reference\\\hline
&\\
2^{50} & \$3.50 & cup\ of\ coffee\\
2^{55} & \$100 & decent\ tickets\ to\ a\ Portland\ Trailblazers\ game\\
2^{65} & \$130,000 & median\ home\ price\ in\ Oshkosh,\ WI\\
2^{75} & \$130 million & budget\ of\ one\ of\ the\ Harry\ Potter\ movies\\
2^{85} & \$140 billion & GDP\ of\ Hungary\\
2^{92} & \$20 trillion & GDP\ of\ the\ United\ States\\
2^{99} & \$2 quadrillion & all\ of\ human\ economic\ activity\ since\ 300,000\ BC\\
2^{128} & really\ a\ lot & a\ billion\ human\ civilizations’\ worth\ of\ effort
\end{array}
$$

Remember, this table[^6] only shows the cost to perform $2^\lambda$ clock cycles. A brute-force attack checking $2^\lambda$ keys would take many more cycles than that! But, as a disclaimer, these numbers reflect only the retail cost of performing a computation, on fairly standard general-purpose hardware. A government organization would be capable of manufacturing special-purpose hardware that would signicantly reduce the computation’s cost. The exercises explore some of these issues, as well as non-financial ways of conceptualizing the cost of huge computations.
[^6]: I found some estimates (https://en.wikipedia.org/wiki/Gross_world_product) of the gross world product (like the GDP but for the entire world) throughout human history, and summed them up for every year. 

**Example**
In 2017, the first collision in the SHA-1 hash function was found (we will discuss hash functions later in the course). The attack involved evaluating the SHA-1 function $2^{63}$ times on a cluster of GPUs.  An article in Ars Technica[^7] estimates the monetary cost of the attack as follows:
[^7]: https://arstechnica.com/information-technology/2017/02/at-deaths-door-for-years-widely-used-sha1-function-is-now-dead/

>Had the researchers performed their attack on Amazon’s Web Services platform, it would have cost $560,000 at normal pricing. Had the researchers been patient and waited to run their attack during off-peak hours, the same collision would have cost $110,000.

### Asymptotic Running Time
It is instructive to think about the monetary cost of an enormous computation, but it doesn’t necessarily help us draw the line between “feasible” attacks (which we want to protect against) and “infeasible” ones (which we agreed we don’t need to care about). We need to be able to draw such a line in order to make security denitions that say “only feasible attacks are ruled out.”

Once again, John Nash thought about this question. He suggested to consider the **asymptotic** cost of an attack — how does the cost of a computation scale as the security parameter  $\lambda$  goes to infinity?
>So a logical way to classify enciphering processes is by **the way in which the computation length for the computation of the key increases with increasing length of the key. This is at best exponential** and at worst probably a relatively small power of $\lambda, a \cdot \lambda^2$, or $a \cdot \lambda^3$, as in substitution ciphers.

Nash highlights the importance of attacks that run in polynomial time:

**Definition 4.1**
*A program runs in **polynomial time** if there exists a constant $c > 0$ such that for all sufficiently long input strings $x$, the program stops after no more than $O(|x|^c)$ steps.*

Polynomial-time algorithms scale reasonably well (especially when the exponent is small), but exponential-time algorithms don’t. It is probably no surprise to modern readers to see “polynomial-time” as a synonym for “efficient.” However, it’s worth pointing out that, again, Nash is years ahead of his time relative to the field of computer science.

In the context of cryptography, our goal will be to ensure that no polynomial-time attack can successfully break security. We will not worry about attacks like brute-force that require exponential time.

Polynomial time is not a perfect match to what we mean when we informally talk about “efficient” algorithms. Algorithms with running time $\Theta(n^{1000})$ are technically polynomial time, while those with running time $\Theta\left(n^{\log \log \log n}\right)$ aren’t. Despite that, polynomial-time is extremely useful because of the following **closure property**: repeating a polynomial-time process a polynomial number of times results in a polynomial-time process overall.

### Potential Pitfall: Numerical Algorithms
When we study public-key cryptography, we will discuss algorithms that operate on very large numbers (e.g., thousands of bits long). You must remember that representing the number $N$ on a computer requires only $\sim\log_2N$ bits. This means that $\log_2N$,  rather than $N$, is our security parameter! We will therefore be interested in whether certain operations on the number $N$ run in polynomial-time as a function of $\log_2 N$, rather than in $N$. Keep in mind that the difference between running time $O(\log N)$ and $O(N)$ is the difference between writing down a number and counting to the number.

For reference, here are some numerical operations that we will be using later in the class, and their known efficiencies:

$$
\begin{array}{ll}
\textbf{Efficient algorithm known:} & \textbf{No known efficient algorithm}:\\
\text{Computing GCDs} & \text{Factoring integers}\\
\text{Arithmetic mod}\ N & \text{Computing}\ \phi (N)\ \text{given}\ N\\
 \text{Inverses mod}\ N & \text{Discrete logarithm}\\
 \text{Exponentiation mod}\ N & \text{Square roots mod composite}\ N
\end{array}
$$

Again, “efficient” means polynomial-time. Furthermore, we only consider polynomial time algorithms that run on standard, *classical* computers. In fact, all of the problems in the right-hand column do have known polynomial-time algorithms on *quantum* computers.

## 4.2 What Qualifies as a “Negligible” Success Probability?

It is not enough to consider only the running time of an attack. For example, consider an attacker who just tries to guess a victim’s secret key, making a single guess. This attack is extremely cheap, but it still has a nonzero chance of breaking security!

In addition to an attack’s running time, we also need to consider its success probability. We don’t want to worry about attacks that are as expensive as a brute-force attack, and we don’t want to worry about attacks whose success probability is as low as a blind-guess attack.

An attack with success probability $2^{-128}$ should not really count as an attack, but an attack with success probability 1/2 should. Somewhere in between $2^{-128}$ and $2^{-1}$ we need to nd a reasonable place to draw a line.

**Example**
*Now we are dealing with extremely tiny probabilities that can be hard to visualize. Again, it can be helpful to conceptualize these probabilities with a more familiar reference:*
$$
\def\arraystretch{1.5}
\begin{array}{cll}
 probability & equivalent & \\\hline
\begin{array}{c}
2^{-10}\\
2^{-20}\\
2^{-28}\\
2^{-40}\\
2^{-60}
\end{array} &
\begin{array}{l}
full\ house\ in\ 5-card\ poker\\
royal\ flush\ in\ 5-card\ poker\\
you\ win\ this\ week’s\ Powerball\ jackpot\\
royal\ flush\ in\ 2\ consecutive\ poker\ games\\
the\ next\ meteorite\ that\ hits\ Earth\ lands\ in\ this\ square \rightarrow
\end{array} &
\begin{array}{|c|}\hline
\qquad \qquad \qquad\\
\qquad \qquad\\
\qquad \qquad\\
\qquad \qquad\\\hline
\end{array}
\end{array}\ $$

As before, it is not clear exactly where to draw the line between “reasonable” and “unreasonable” success probability for an attack. Just like we did with polynomial running time, we can also use an **asymptotic** approach to define when a probability is negligibly small. Just as “polynomial time” considers how fast an algorithm’s running time approaches infinity as its input grows, we can also consider how fast a success probability approaches zero as the security parameter grows.

In a scheme with $\lambda-bit$ keys, a blind-guessing attack succeeds with probability $1/2^\lambda$. Now what about an attacker who makes 2 blind guesses, or $\lambda$ guesses, or $\lambda^{42}$ guesses? Such an  attacker would still run in polynomial time, and has success probability $2/2^\lambda,\lambda/2^\lambda$ or ${\lambda^{42}} / 2^\lambda$. However, no matter what polynomial you put in the numerator, the probability still goes to zero. Indeed, $1/2^\lambda$ **approaches zero so fast that no polynomial can “rescue” it**; or, in other words, it approaches zero faster than 1 over any polynomial. This idea leads to our formal definition:

**Definition 4.2 (Negligible)**
*A function $f$ is negligible if, for every polynomial $p$, we have $\lim\limits_{\lambda\rightarrow\infty}p(\lambda)f(\lambda)=0$.*

In other words, a negligible function approaches zero so fast that you can never catch up when multiplying by a polynomial. This is exactly the property we want from a security guarantee that is supposed to hold against all polynomial-time adversaries. If a polynomial-time attacker succeeds with probability $f$ , then repeating the same attack $p$ independent times would still be an overall polynomial-time attack (if $p$ is a polynomial), and its success probability would be $p \cdot f$ .

When you want to check whether a function is negligible, you only have to consider polynomials $p$ of the form $p(\lambda)=\lambda^c$ for some constant $c$:

**Claim 4.3**
*If for every integer $c$, $\lim\limits_{\lambda\rightarrow\infty}\lambda^cf(\lambda)=0$, then $f$ is negligible.*

**Proof**
Suppose $f$ has this property, and take an arbitrary polynomial $p$. We want to show that $\lim\limits_{\lambda\rightarrow\infty}p(\lambda)f(\lambda)=0$.

If $d$ is the degree of $p$, then $\lim\limits_{\lambda\rightarrow\infty}\frac{p(\lambda)}{\lambda^{d+1}=0}.$ Therefore,

$$
\lim _{\lambda \rightarrow \infty} p(\lambda) f(\lambda)=\lim _{\lambda \rightarrow \infty}\left[\frac{p(\lambda)}{\lambda^{d+1}}\left(\lambda^{d+1} \cdot f(\lambda)\right)\right]=\left(\lim _{\lambda \rightarrow \infty} \frac{p(\lambda)}{\lambda^{d+1}}\right)\left(\lim _{\lambda \rightarrow \infty} \lambda^{d+1} \cdot f(\lambda)\right)=0 \cdot 0.
$$

The second equality is a valid law for limits since the two limits on the right exist and are not an indeterminate expression like $0\cdot \infty$. The final equality follows from the hypothesis on $f$.

**Example**
*The function $f(\lambda)=1/2^\lambda$ is negligible, since for any integer $c$, we have:*

$$
\lim _{\lambda \rightarrow \infty} \lambda^{c} / 2^{\lambda}=\lim _{\lambda \rightarrow \infty} 2^{c \log (\lambda)} / 2^{\lambda}=\lim _{\lambda \rightarrow \infty} 2^{c \log (\lambda)-\lambda}=0
$$
since $c\log(\lambda)-\lambda$ approaches $-\infty$ in the limit, for any constant $c$. Using similar reasoning, one can show that the following functions are also negligible:

$$\dfrac{1}{2^{\lambda/2}},\qquad\dfrac{1}{2^{\sqrt\lambda}},\qquad\dfrac{1}{2^{\log^2\lambda}},\qquad\dfrac{1}{\lambda^{\log\lambda}}.$$

Functions like $1/\lambda^5$ approach zero but not fast enough to be negligible.  To see why, we can take polynomial $p(\lambda)=\lambda^6$ and see that the resulting limit does not satisfy the requirement from Definition 4.2:

$$\lim\limits_{\lambda\rightarrow \infty}p(\lambda)\dfrac{1}{\lambda^5}=\lim\limits_{\lambda\rightarrow\infty}\lambda=\infty\neq\infty$$

In this class, when we see a negligible function, it will typically always be one that is easy to recognize as negligible (just as in an undergraduate algorithms course, you won’t really encounter algorithms where it’s hard to tell whether the running time is polynomial).

**Definition 4.4 ($\bf{f \approx g}$)**
*If $f ,g : \mathbb{N} \rightarrow \mathbb{R}$ are two functions, we write $f\approx g$ to mean that $|f(\lambda)-g(\lambda)|$ is a negligible function.*

We use the terminology of negligible functions exclusively when discussing probabilities, so the following are common:

$$
\def\arraystretch{1.5}
\begin{array}{lll}
\text{Pr}[X]\approx 0 & \Leftrightarrow & \text{event $X$ almost never happens”}\\
\text{Pr}[Y]\approx 1 & \Leftrightarrow & \text{event $Y$ almost always happens”}\\
\text{Pr}[A]\approx\text{Pr}[B] & \Leftrightarrow & \text{“events $A$ and $B$ happen with essentially the same probability”}
\end{array}
$$[^8]
[^8]: $Pr[A] \approx Pr[B]$ doesn’t mean that events $A$ and $B$ almost always happen together (when $A$ and $B$ are defined over a common probability space) — imagine $A$ being the event “the coin came up heads” and $B$ being the event “the coin came up tails.” These events have the same probability but never happen together. To say that “$A$ and $B$ almost always happen together,” you’d have to say something like $Pr[A\oplus B]\approx 0$, where $A \oplus B$ denotes the event that exactly one of $A$ and $B$ happens.

Additionally, the $\appox$ symbol is *transitive:*[^9] if $Pr[X]\approx Pr[Y]$ and $Pr[Y]\approx Pr[Z]$, then $Pr[X]\approx Pr[Z]$ (perhaps with a lightly larger, but still negligible, difference).

## 4.3 Indistinguishability
So far we have been writing formal security definitions in terms of interchangeable libraries, which requires that two libraries have *exactly* the *same* effect on *every* calling program. Going forward, our security definitions will not be quite as demanding. First, we only consider polynomial-time calling programs; second, we don’t require the libraries to have exactly the same effect on the calling program, only that the difference in effects is negligible.

**Definition 4.5 (Indistinguishable)**
Let $\mathcal{L}_{\text{left}}$ and $\mathcal{L}_{\text{right}}$ be two libraries with a common interface. We say that $\mathcal{L}_{\text{left}}$ and $\mathcal{L}_{\text{right}}$ are indistinguishable, and write   $\mathcal{L}_{\text{left}}\approx\mathcal{L}_{\text{right}}$ , if for all polynomial-time programs $\mathcal{A}$ that output a single bit, $\operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {left }} \Rightarrow 1\right] \approx \operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {right }} \Rightarrow 1\right]$.

We call the quantity $\operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {left }} \Rightarrow 1\right] -\operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {right }} \Rightarrow 1\right]$ he advantage or bias of $\mathcal{A}$ in distinguishing $\mathcal{L}_{\text{left}}$ from $\mathcal{L}_{\text{right}}$. Two libraries are therefore indistinguishable if all polynomial-time calling programs have negligible advantage in distinguishing them.

From the properties of the "$\approx$" symbol, we can see that indistinguishability of libraries is also transitive, which allows us to carry out hybrid proofs of security in the same way as before.

**Example**
Here is a very simple example of two indistinguishable libraries:
$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\quad \quad\mathcal{L}_{\text{left}}\\\hline
\underline{\text{PREDICT}(x):}\\
\quad s\leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^\lambda\\
\quad \text{return}\ x\stackrel{?}{=} s\\\hline
\end{array}\quad \begin{array}{|l|}\hline
\quad \quad \mathcal{L}_{\text{right}}\\\hline
\underline{\text{PREDICT}(x):}\\
\quad \text{return false}\\\hline
\end{array}
$$
Imagine the calling program trying to predict which string will be chosen when uniformly sampling from $\{\textcolor{brown}{0},\textcolor{brown}{1}\}^\lambda$. The left library tells the calling program whether its prediction was correct. The right library doesn’t even bother sampling a string, it just always says “sorry, your prediction was wrong.”

Here is one obvious strategy (maybe not the best one, we will see) to distinguish these libraries. The calling program $\mathcal{A}_{\text{obvious}}$ calls predict many times and outputs 1 if it ever received true as a response. Since it seems like the argument to predict might not have any effect, let’s just use the string of all-$\textcolor{brown}{0}$ as argument every time.

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\quad \qquad \quad\mathcal{A}_{\text{obvious}}\\\hline
\text{do}\ q\ \text{times:}\\
\quad\text{if PREDICT}(\textcolor{brown}{0}^\lambda)=\text{true} \\
\quad \quad\text{return}\ 1\\
\text{return}\ 0\\\hline
\end{array}
$$

 - *$\mathcal{L}_{\text{right}}$ can never return true, so $\text{Pr}[\mathcal{A}_{\text{obvious}}\diamond\mathcal{L}_{\text{right}}\Rightarrow 1]=0$*
 - *In $\mathcal{L}_{\text{left}}$ each call to PREDICT has an independent probability $1/2^\lambda$ of returning true. So $\text{Pr}[\mathcal{A}_{\text{obvious}}\diamond \mathcal{L}_{\text{left}}\Rightarrow 1]$ is surely non-zero. Actually, the exact probability is a bit cumbersome to write:*
$$
\begin{aligned}
\operatorname{Pr}\left[\mathcal{A}_{\text {obvious }} \diamond \mathcal{L}_{\text {left }} \Rightarrow 1\right]&=1-\operatorname{Pr}\left[\mathcal{A}_{\text {obvious }} \diamond \mathcal{L}_{\text {left }} \Rightarrow 0\right]\\
&=1-\operatorname{Pr}[\text { all } q \text { independent calls to PREDICT return } \mathrm{false}]\\
&=1-\left(1-\frac{1}{2^{\lambda}}\right)^{q}
\end{aligned}
$$
Rather than understand this probability, we can just compute an upper bound for it. Using the union bound, we get:
$$
\begin{aligned}
\operatorname{Pr}\left[\mathcal{A}_{\text {obvious }} \diamond \mathcal{L}_{\text {left }} \Rightarrow 1\right]& \leqslant \operatorname{Pr}[\text { first call to PREDICT returns true }] \\
&+\operatorname{Pr}[\text { second call to PREDICT returns true }]+\cdots \\
&=q \frac{1}{2^{\lambda}}
\end{aligned}
$$
This is an overestimate of some probabilities (e.g., if the rst call to predict returns true, then the second call isn’t made). More fundamentally, $q/2^\lambda$  exceeds 1 when $q$ is large. But nevertheless, $\text{Pr}[\mathcal{A}_{\text{obvious}}\diamond \mathcal{L}_{\text{left}}\Rightarrow 1]\leqslant q/2^\lambda.$

*We showed that $\mathcal{A}_{\text{obvious}}$ has non-zero advantage. This is enough to show that $\mathcal{L}_{\text{left}}\neq\mathcal{L}_{\text{right}}$.
We also showed that $\mathcal{A}_{\text{obvious}}$ has advantage at most $q/2^\lambda$. Since $\mathcal{A}_{\text{obvious}}$ runs in polynomial
time, it can only make a polynomial number q of queries to the library, so $q/2^\lambda$ is negligible. However, this is not enough to show that $\mathcal{L}_{\text{left}}\approx\mathcal{L}_{\text{right}}$ since it considers only $a$ single calling program. To show that the libraries are indistinguishable, we must show that **every** calling program’s advantage is negligible.*

In a few pages, we will prove that for any $\mathcal{A}$ that makes $q$ calls to PREDICT,
$$
\left|\operatorname{Pr}\left[\mathcal{F} \diamond \mathcal{L}_{\text {left }} \Rightarrow 1\right]-\operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {right }} \Rightarrow 1\right]\right| \leqslant \frac{q}{2^{\lambda}}.
$$
For any polynomial-time $\mathcal{A}$, the number $q$ of calls to PREDICT will be a polynomial in $\lambda$, making $q/2^\lambda$ a negligible function. Hence, $\mathcal{L}_{\text{left}}\approx\mathcal{L}_{\text{right}}$. 

### Other Properties

**Lemma 4.6($\bf{\approx}$ facts)**
$$
\text { If } \mathcal{L}_{1} \equiv \mathcal{L}_{2} \text { then } \mathcal{L}_{1} \approx \mathcal{L}_{2} . \text { Also, if } \mathcal{L}_{1} \approx \mathcal{L}_{2} \approx \mathcal{L}_{3} \text { then } \mathcal{L}_{1} \approx \mathcal{L}_{3}
$$
Analogous to Lemma 2.11, we also have the following library chaining lemma, which you are asked to prove as an exercise: 

**Lemma 4.7 (Chaining)** 
If $\mathcal{L}_{\text{left}}\approx\mathcal{L}_{\text{right}}$ then $\mathcal{L}^*\diamond \mathcal{L}_{\text{left}}\approx\mathcal{L}^*\diamond\mathcal{L}_{\text{right}}$ for any polynomial-time library $\mathcal{L}^*$.

### Bad-Event Lemma
A common situation is when two libraries are expected to execute exactly the same statements, until some rare & exceptional condition happens. In that case, we can bound an attacker’s distinguishing advantage by the probability of the exceptional condition.

More formally,

**Lemma 4.8 (Bad events)**
*Let $\mathcal{L}_{\text{left}}$ and $\mathcal{L}_{\text{right}}$ be libraries that each dene a variable named ‘bad’ that is initialized to 0. If $\mathcal{L}_{\text{left}}$ and $\mathcal{L}_{\text{right}}$  have identical code, except for code blocks reachable only when bad = 1 (e.g., guarded by an “if bad = 1” statement), then*

$$
\left|\operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {left }} \Rightarrow 1\right]-\operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {right }} \Rightarrow 1\right]\right| \leqslant \operatorname{Pr}\left[\mathcal{A} \diamond \mathcal{L}_{\text {left }} \text { sets bad }=1\right]
$$

**$\bf{\star}$Proof**
Fix an arbitrary calling program $\mathcal{A}$. In this proof, we use conditional probabilites[^9] to isolate the cases where bad is changed to 1. We define the following events:
[^9]:The use of c
