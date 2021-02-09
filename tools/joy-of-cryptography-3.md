
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