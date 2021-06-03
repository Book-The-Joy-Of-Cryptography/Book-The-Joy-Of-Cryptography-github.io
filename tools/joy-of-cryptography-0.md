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

 0.  **[Review of Concepts & Notation](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-0/)**
     0.1  Logs & Exponents
     0.2  ModularArithmetic
     0.3  Strings
     0.4  Functions
     0.5  Probability
     0.6  Notation in Pseudocode
     0.7  Asymptotics (Big-*O*)
     
1.  **[One-Time Pad & Kerckhoffs’ Principle](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-1/)**
    1.1  What Is [Not] Cryptography?
    1.2  Specifics of One-Time Pad
    
2.  **[The Basics of Provable Security](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-2/)**
    2.1  How to Write a Security Definition
    2.2  Formalisms for Security Definitions
    2.3  How to Demonstrate Insecurity with Attacks
    2.4  How to Prove Security with The Hybrid Technique
    2.5  How to Compare/Contrast Security Definitions

3.  **[Secret Sharing](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-3/)**
    3.1  Definitions
    3.2  A Simple 2-out-of-2 Scheme
    3.3  Polynomial Interpolation
    3.4  Shamir Secret Sharing
    3.5  $\star$ VisualSecretSharing 

4.  **[Basing Cryptography on Intractable](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-4/)** Computations
    4.1  What Qualifies as a “Computationally Infeasible” Attack?
    4.2  What Qualifies as a “Negligible” Success Probability?
    4.3  Indistinguishability
    4.4  Birthday Probabilities & Sampling With/out Replacement

5.  **[Pseudorandom Generators](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-5/)**
    5.1  Definitions
    5.2  Pseudo random Generators in Practice
    5.3  Application: Shorter Keys in One-Time-Secret Encryption
    5.4  Extending the Stretch of a PRG
    5.5  $\star$ Applications: Stream Cipher & Symmetric Ratchet

6.  **[Pseudorandom Functions & Block Ciphers](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-6/)**
    6.1  Definition
    6.2  PRFs vs PRGs; Variable-Hybrid Proofs
    6.3  Block Ciphers (Pseudorandom Permutations)
    6.4  Relating PRFs and BlockCiphers
    6.5  PRFs and Block Ciphers in Practice
    6.6  $\star$ Strong Pseudorandom Permutations

7.  **[Security Against Chosen Plaintext Attacks](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-7/)**
    7.1  Limits of Deterministic Encryption
    7.2  Pseudorandom Ciphertexts
    7.3  CPA-Secure Encryption Based On PRFs

8.  **[Block Cipher Modes of Operation](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-8/)**
    8.1  A Tour of Common Modes
    8.2  CPA Security and Variable-Length Plaintexts
    8.3  Security of OFB Mode
    8.4  Padding & Ciphertext Stealing

9.  **[Chosen Ciphertext Attacks](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-9/)**
    9.1  Padding Oracle Attacks 
    9.2  What Went Wrong?
    9.3  Defining CCA Security
    9.4  $\star$ A Simple CCA-Secure Scheme

10.  **[Message Authentication Codes](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-10/)**
    10.1  Definition
    10.2  $\star$ A PRF is a MAC
    10.3  MACs for Long Messages
    10.4  Encrypt-Then-MAC

11.  **[Hash Functions](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-11/)**
    11.1  Security Properties for Hash Functions
    11.2  Merkle-Damgård Construction
    11.3  Hash Functions vs. MACs: Length-Extension Attacks

12.  **[Authenticated Encryption & AEAD](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-12/)**
    12.1  Definitions
    12.2  Achieving AE/AEAD
    12.3  Carter-Wegman MACs
    12.4  Galois Counter Mode for AEAD

13.  **[RSA & Digital Signatures](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-13/)**
    13.1  “Dividing” Mod *n*
    13.2  The RSA Function
    13.3  Digital Signatures
    13.4  Chinese Remainder Theorem
    13.5 The Hardness of Factoring *N*

14.  **[Diffie-Hellman Key Agreement](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-14/)**
    14.1  Cyclic Groups
    14.2  Diffie-Hellman Key Agreement
    14.3  Decisional Diffie-Hellman Problem

15.  **[Public-Key Encryption](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-15/)**
    15.1 Security Definitions
    15.2 One-Time Security Implies Many-Time Security
    15.3 ElGamal Encryption
    15.4 Hybrid Encryption
    
**[Index of Security Definitions](https://book-the-joy-of-cryptography.bsafes.com/docs/joy-of-cryptography-index/)**


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