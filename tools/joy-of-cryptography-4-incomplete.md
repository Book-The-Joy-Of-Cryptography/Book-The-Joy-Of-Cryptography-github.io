



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