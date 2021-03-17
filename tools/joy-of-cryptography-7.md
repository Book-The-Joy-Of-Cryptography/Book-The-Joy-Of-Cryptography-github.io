# 7 Security Against Chosen Plaintext Attacks

Our previous security definitions for encryption capture the case where a key is used to encrypt only one plaintext. Clearly it would be more useful to have an encryption scheme that allows many plaintexts to be encrypted under the same key.

Fortunately we have arranged things so that we get the “correct” security definition when we modify the earlier definition in a natural way. We simply let the libraries choose a secret key once and for all, which is used to encrypt all plaintexts. More formally:

**Definition 7.1 (CPA security)**
*Let $\Sigma$ be an encryption scheme. We say that $\Sigma$ has **security against chosen-plaintext attacks (CPA security**) if $\mathcal{L}_{\text{cpa-L}}^{\Sigma}\approx\mathcal{L}_{\text{cpa-R}}^{\Sigma}$ where:*

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad\qquad \qquad \ \mathcal{L}_{\text{cpa-L}}^\Sigma\\\hline
k\leftarrow\Sigma.\text{KeyGen}\\\\
\underline{\text{EAVESDROP}(m_L,m_R\in \Sigma.\mathcal{M}):}\\
\quad c:=\Sigma.\text{Enc}(k,\colorbox{yellow}{m}_L)\\
\quad \text{return}\ c\\\hline
\end{array}\quad
\begin{array}{|l|}\hline
\qquad\qquad \qquad \ \mathcal{L}_{\text{cpa-R}}^\Sigma\\\hline
k\leftarrow\Sigma.\text{KeyGen}\\\\
\underline{\text{EAVESDROP}(m_L,m_R\in \Sigma.\mathcal{M}):}\\
\quad c:=\Sigma.\text{Enc}(k,\colorbox{yellow}{m}_R)\\
\quad \text{return}\ c\\\hline
\end{array}
$$

Notice how the key k is chosen at initialization time and is static for all calls to Enc. CPA security is often called “IND-CPA” security, meaning “indistinguishability of ciphertexts under chosen-plaintext attack.”

## 7.1 Limits of Deterministic Encryption

We have already seen block ciphers / PRPs, which seem to satisfy everything needed for a secure encryption scheme. For a block cipher, $F$ corresponds to encryption, $F ^{-1}$ corresponds to decryption, and all outputs of $F$ look pseudorandom. What more could you ask for in a good encryption scheme?

**Example**
*We will see that a block cipher, when used “as-is,” is **not** a CPA-secure encryption scheme. Let F denote the block cipher and suppose its block length is $blen$.*

*Consider the following adversary $\mathcal{A}$, that tries to distinguish the $\mathcal{L}_{\text{cpa}-\star}$ libraries:*

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad\qquad \qquad \ \mathcal{A}\\\hline
c_1:=\text{EAVESDROP}(\textcolor{brown}{0}^{blen},\textcolor{brown}{0}^{blen})\\
c_2:=\text{EAVESDROP}(\textcolor{brown}{0}^{blen},\textcolor{brown}{1}^{blen})\\
\text{return}\ c_1\stackrel{?}{=} c_2\\\hline
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad\qquad \qquad \ \mathcal{A}\\\hline
c_1:=\text{EAVESDROP}(\textcolor{brown}{\colorbox{yellow}{0}}^{blen},\textcolor{brown}{0}^{blen})\\
c_2:=\text{EAVESDROP}(\textcolor{brown}{\colorbox{yellow}{0}}^{blen},\textcolor{brown}{1}^{blen})\\
\text{return}\ c_1\stackrel{?}{=} c_2\\\hline
\end{array}
\diamond
\begin{array}{|l|}\hline
\qquad\qquad \quad \ \mathcal{L}_{\text{cpa-L}}^\Sigma\\\hline
k\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^\lambda\\\\
\underline{\text{EAVESDROP}(m_L,m_R):}\\
\quad c:=F(k,\colorbox{yellow}{m}_L)\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{l}
\textit{When $\mathcal{A}$ is linked to $\mathcal{L}_{\text{cpa-L}}$, the}\\
\textit{EAVESDROP \textit{algorithm} will encrypt}\\
\textit{its first argument. So, $c_1$ and $c_2$}\\
\textit{will both be computed as $F(k,\textcolor{brown}{0}^{blen})$.}\\
\textit{Since $F$ is a deterministic function, this}\\
\textit{results in identical outputs from EAVESDROP.}\\
\textit{In other words $c_1 = c_2$, and $\mathcal{A} \diamond \mathcal{L}_{\text{cpa-L}}$}\\ \bm{always} \ \textit{outputs 1.}
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad\qquad \qquad \ \mathcal{A}\\\hline
c_1:=\text{EAVESDROP}(\textcolor{brown}{\colorbox{yellow}{0}}^{blen},\textcolor{brown}{0}^{blen})\\
c_2:=\text{EAVESDROP}(\textcolor{brown}{\colorbox{yellow}{0}}^{blen},\textcolor{brown}{1}^{blen})\\
\text{return}\ c_1\stackrel{?}{=} c_2\\\hline
\end{array}
\diamond
\begin{array}{|l|}\hline
\qquad\qquad \ \ \mathcal{L}_{\text{cpa-R}}^\Sigma\\\hline
k\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^\lambda\\\\
\underline{\text{EAVESDROP}(m_L,m_R):}\\
\quad c:=F(k,\colorbox{yellow}{m}_R)\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{l}
\textit{When $\mathcal{A}$ is linked to $\mathcal{L}_{\text{cpa-R}}$, the}\\
\textit{EAVESDROP \textit{algorithm} will encrypt}\\
\textit{its second argument. So, $c_1$ and $c_2$}\\
\textit{are computed as $c_1=F(k,\textcolor{brown}{0}^{blen})$.}\\
\textit{and $c_2=F(k,\textcolor{brown}{1}^{blen}).$ Since F is a}\\
\textit{permutation, $c_1 \neq c_2$, so $\mathcal{A} \diamond \mathcal{L}_{\text{cpa-R}}$}\\ \bm{never} \ \textit{outputs 1.}
\end{array}
$$

*This adversary has advantage 1 in distinguishing the libraries, so the bare block cipher $F$ is **not** a CPA-secure encryption scheme.*

### Impossibility of Deterministic Encryption
The reason a bare block cipher does not provide CPA security is that it is **deterministic**. Calling Enc$(k,m)$ twice — with the same key and same plaintext — leads to the same ciphertext.
Even one-time pad is deterministic. [^1] One of the first and most important aspects of CPA security is that it is incompatible with deterministic encryption. Deterministic encryption can never be CPA-secure! In other words, we can attack the CPA-security of any scheme $\Sigma$, knowing only that it has deterministic encryption. The attack is a simple generalization of our attack against a bare PRP:
 [^1]:  Remember, we can always consider what will happen when running one-time pad encryption twice with the same key + plaintext. The one-time secrecy definition doesn’t give us any security guarantees about using one-time pad in this way, but we can still consider it as a thought experiment.
 
$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad\qquad \qquad \qquad \quad \mathcal{A}\\\hline
\text{arbitrarily choose \textit{distinct} plaintexts}\ x,y\in\mathcal{M}\\
c_1:=\text{EAVESDROP}(x,x)\\
c_2:=\text{EAVESDROP}(x,y)\\
\text{return}\ c_1\stackrel{?}{=} c_2\\\hline
\end{array}
$$

A good way to think about what goes wrong with deterministic encryption is that it **leaks whether two ciphertexts encode the same plaintext**, and this is not allowed by CPA security. Think of sealed envelopes as an analogy for encryption. I shouldn’t be able to tell whether two sealed envelopes contain the same text! We are only now seeing this issue because this is the first time our security definition allows an adversary to see multiple ciphertexts encrypted under the same key.

### Avoiding Deterministic Encryption
Is CPA security even possible? How exactly can we make a non-deterministic encryption scheme? This sounds challenging! We must design an Enc algorithm such that calling it twice with the same plaintext and key results in different ciphertexts (otherwise the attack $\mathcal{A}$ above violates CPA security). What’s more, it must be possible to decrypt all of those different encryptions of the same plaintext to the correct value!

There are 3 general ways to design an encryption scheme that is not deterministic:

 - Encryption/decryption can be **stateful**, meaning that every call to Enc or Dec will actually modify the value of k. The symmetric ratchet construction described in Section 5.5 could be thought of as such a stateful construction. The key is updated via the ratchet mechanism for every encryption. A significant drawback with stateful encryption is that synchronization between sender and receiver is fragile and can be broken if a ciphertext is lost in transit.
 
 - Encryption can be **randomized**. Each time a plaintext is encrypted, the Enc algorithm chooses fresh, independent randomness specific to that encryption. The main challenge in designing a randomized encryption method is to incorporate randomness into each ciphertext in such a way that decryption is still possible. Although this sounds quite challenging, we have already seen such a method, and we will prove its CPA security in the next sections. In this book we will focus almost entirely on randomized encryption.
 - Encryption can be **nonce-based**. A “nonce” stands for “number used only once,” and it refers to an extra argument that is passed to the Enc and Dec algorithms. A nonce does not need to be chosen randomly; it does not need to be secret; it only needs to be **distinct** among all calls made to Enc. By guaranteeing that some input to Enc will be different every time (even when the key and plaintext are repeated), the Enc algorithm can be deterministic and still provide CPA security.
 
     Nonce-based encryption requires a change to the interface of encryption, and therefore a change to the correctness & security definitions as well. The encryption/decryption algorithms syntax is updated to $\text{Enc}(k,v,m)$ and $\text{Dec}(k,v, c)$, where $v$ is a nonce. The correctness property is that $\text{Dec}(k,v,\text{Enc}(k,v,m))$ = $m$ for all $k,v,m$, so both encryption & decryption algorithms should use the same nonce. The security definition allows the adversary to choose the nonce, but gives an error if the adversary tries to encrypt multiple ciphertexts with the same nonce. In this way, the definition enforces that the nonces are distinct.

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
k\leftarrow\Sigma.\text{KeyGen}\\
\colorbox{yellow}{\textit{V} :=}\emptyset\\\\
\underline{\text{EAVESDROP}(\colorbox{yellow}{\textit{v}},m_L,m_R\in\Sigma.\mathcal{M}):}\\
\quad \colorbox{yellow}{\text{if}\ \textit{v}}\in V:\text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad \colorbox{yellow}{\it{V} := V}\cup\{v\}\\
\quad c:=\Sigma.\text{Enc}(k,\colorbox{yellow}{\textit{v}},m_L)\\
\quad\text{return}\ c\\\hline
\end{array}
\approx
\begin{array}{|l|}\hline
k\leftarrow\Sigma.\text{KeyGen}\\
\colorbox{yellow}{\it{V}:=}\emptyset\\\\
\underline{\text{EAVESDROP}(\colorbox{yellow}{\textit{v}},m_L,m_R\in\Sigma.\mathcal{M}):}\\
\quad \colorbox{yellow}{\text{if}\ \textit{v}}\in V:\text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad \colorbox{yellow}{\it{V} := V}\cup\{v\}\\
\quad c:=\Sigma.\text{Enc}(k,\colorbox{yellow}{\textit{v}},m_R)\\
\quad\text{return}\ c\\\hline
\end{array}
$$

> Note that the calling program provides a single value $v$ (not a $v_L$ and $v_R$). Both libraries use the nonce $v$ that is given, and this implies that the encryption scheme does not need to *hide* $v$. If something is the same between both libraries, then it is not necessary to hide it in order to make the libraries indistinguishable.


If an encryption scheme does not fall into one of these three categories, it cannot satisfy our definition of CPA-security. You can and should use deterministic encryption as a sanity check against any proposed encryption algorithm.

## 7.2 Pseudorandom Ciphertexts

When we introduced one-time security of encryption (in Section 2.2), we had two variants of the definition. The more general variant said, roughly, that encryptions of $m_L$ should look like encryptions of $m_R$. The more specific variant said that encryptions of every m should look uniform.

We can do something similar for CPA security, by defining a security definition that says “encryptions of $m$ look uniform.” Note that it is not sufficient to use the same security libraries from the one-time security definition. It is important for the library to allow multiple encryptions under the same key. Just because a single encryption is pseudorandom, it doesn’t mean that multiple encryptions appear *jointly* pseudorandom. In particular, they may not look *independent* (this was an issue we saw when discussing the difficulty of constructing a PRF from a PRG).

**Definition 7.2 (CPA$\Phi$ security)**
*Let $\Sigma$ be an encryption scheme. We say that $\Sigma$ has **pseudorandom ciphertexts in the presence of chosen-plaintext attacks (CPA$\varPhi$ security)** if $\mathcal{L}_{\text{cpa}\Phi-\text{real}}^\Sigma\approx\mathcal{L}_{\text{cpa}\Phi-\text{rand}}^\Sigma$ where:*

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad\ \ \mathcal{L}_{\text{cpa}\Phi-\text{real}}^\Sigma\\\hline
k\leftarrow\Sigma.\text{KeyGen}\\\\
\underline{\text{CTXT}(m\in\Sigma.\mathcal{M}):}\\
\quad c:=\Sigma.\text{Enc}(k,m)\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad\ \ \mathcal{L}_{\text{cpa}\varPhi-\text{rand}}^\Sigma\\\hline
\underline{\text{CTXT}(m\in\Sigma.\mathcal{M}):}\\
\quad c\gets\Sigma.\mathcal{C}\\
\quad \text{return}\ c\\\hline
\end{array}
$$

This definition is also called “IND$-CPA”, meaning “indistinguishable from random under chosen plaintext attacks.” This definition will be useful to use since:

 - It is easier to prove CPA$\Phi$ security than to prove CPA security. Proofs for CPA security tend to be about twice as long and twice as repetitive, since they involve getting to a “half-way hybrid” and then performing the same sequence of hybrids steps in reverse. Taking the proof only to the same half-way point is generally enough to prove CPA$\Phi$ security

 - CPA$\Phi$ security implies CPA security. We show this below, but the main idea is the same as in the case of one-time security. If encryptions of all plaintexts look uniform, then encryptions of $m_L$ look like encryptions of $m_R$. 
 
 - Most of the schemes we will consider achieve CPA$\varPhi$ anyway.

Still, most of our high-level discussion of security properties will be based on CPA security. It is the “minimal” (i.e., least restrictive) definition that appears to capture our security intuitions.

**Claim 7.3**
*If an encryption scheme has CPA$\varPhi$ security, then it also has CPA security.*

**Proof**
We want to prove that $\mathcal{L}_{\text{cpa-L}}^\Sigma\approx\mathcal{L}_{\text{cpa-R}}^\Sigma$, *using* the assumption that  $\mathcal{L}_{\text{cpa}\Phi-\text{real}}^\Sigma\approx\mathcal{L}_{\text{cpa}\Phi-\text{rand}}^\Sigma$. The sequence of hybrids follows:

$$
\mathcal{L}_{\text{cpa-L}}^\Sigma: 
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad\ \ \mathcal{L}_{\text{cpa-L}}^\Sigma\\\hline
k\leftarrow\Sigma.\text{KeyGen}\\\\
\underline{\text{EAVESDROP}(m_L,m_R):}\\
\quad c:=\Sigma.\text{Enc}(k,m_L)\\
\quad \text{return}\ c\\\hline
\end{array}\qquad \text{The starting point is}\ \mathcal{L}_{\text{cpa-L}}^\Sigma, \text{as expected.}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\underline{\text{EAVESDROP}(m_L,m_R):}\\
\quad c:=\colorbox{yellow}{\text{CTXT}}(m_L)\\
\quad \text{return}\ c\\\hline
\end{array}
\diamond
\begin{array}{|l|}\hline
\qquad\ \ \mathcal{L}_{\text{cpa}\varPhi-\text{real}}^\Sigma\\\hline
k\leftarrow\Sigma.\text{KeyGen}\\\\
\underline{\text{CTXT}(m):}\\
\quad c:=\Sigma.\text{Enc}(k,m)\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{l}
\text{It may look strange, but we have further}\\
\text{factored out the call to Enc into a subroutine.}\\
\text{It looks like} \ everything \ \text{from $\mathcal{L}_{\text{cpa-L}}$ has been}\\
\text{factored out, but actually the original library}\\
\text{still “makes the choice” of which of $m_L,m_R$}\\
\text{to encrypt.}
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\underline{\text{EAVESDROP}(m_L,m_R):}\\
\quad c:=\text{CTXT}(m_L)\\
\quad \text{return}\ c\\\hline
\end{array}
\diamond
\begin{array}{|l|}\hline
\ \  \ \mathcal{L}_{\text{cpa}\Phi-\text{rand}}^\Sigma\\\hline
\underline{\text{CTXT}(m):}\\
\quad c:=\Sigma.\mathcal{C}\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{l}
\text{We have replaced $\mathcal{L}_{\text{cpa}\Phi-\text{real}}^\Sigma$\ with\ $\mathcal{L}_{\text{cpa}\Phi-\text{rand}}^\Sigma$.}\\
\text{By our assumption, the change is indistinguishable.}
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\underline{\text{EAVESDROP}(m_L,m_R):}\\
\quad c:=\text{CTXT}(\colorbox{yellow}{m}_R)\\
\quad \text{return}\ c\\\hline
\end{array}
\diamond
\begin{array}{|l|}\hline
\ \  \ \mathcal{L}_{\text{cpa}\Phi-\text{rand}}^\Sigma\\\hline
\underline{\text{CTXT}(m):}\\
\quad c\leftarrow\Sigma.\mathcal{C}\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{l}
\text{We have changed the argument being}\\
\text{passed to CTXT. This has no effect on the}\\
\text{library’s behavior since CTXT completely}\\
\text{ignores its argument in these hybrids.}
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\underline{\text{EAVESDROP}(m_L,m_R):}\\
\quad c:=\text{CTXT}(m_R)\\
\quad \text{return}\ c\\\hline
\end{array}
\diamond
\begin{array}{|l|}\hline
\ \  \ \mathcal{L}_{\text{cpa}\Phi-\text{real}}^\Sigma\\\hline
k\leftarrow\Sigma.\text{KeyGen}\\\\
\underline{\text{CTXT}(m):}\\
\quad c:=\Sigma.\text{Enc}(k,m)\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{l}
\text{The mirror image of a previous step; we}\\
\text{replace $\mathcal{L}_{\text{cpa}\Phi-\text{rand}}^\Sigma$ with $\mathcal{L}_{\text{cpa}\Phi-\text{real}}^\Sigma$.}
\end{array}
$$

$$
\mathcal{L}_{\text{cpa-R}}^\Sigma:
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad \qquad  \ \mathcal{L}_{\text{cpa-R}}^\Sigma\\\hline
\colorbox{yellow}{k}\leftarrow\Sigma.\text{KeyGen}\\\\
\underline{\text{EAVESDROP}(m_L,m_R):}\\
\quad \colorbox{yellow}{c:=}\Sigma.\text{Enc}(k,m_R)\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{l}
\text{The $\mathcal{L}_{\text{cpa}\Phi-\text{real}}$ library has been inlined,}\\
\text{and the result is $\mathcal{L}_{\text{cpa-R}}^\Sigma$.}
\end{array}
$$

The sequence of hybrids shows that $\mathcal{L}_{\text{cpa-L}}^\Sigma\approx\mathcal{L}_{\text{cpa-R}}^\Sigma$, as desired.

## 7.3 CPA-Secure Encryption Based On PRFs

CPA security presents a significant challenge; its goals seem difficult to reconcile. On the one hand, we need an encryption method that is randomized, so that each plaintext $m$ is mapped to a large number of potential ciphertexts. On the other hand, the decryption method must be able to recognize all of these various ciphertexts as being encryptions of $m$.

However, we have already seen a way to do this! In Chapter 6 we motivated the concept of a PRF with the following encryption technique. If Alice and Bob share a huge table $T$ initialized with uniform data, then Alice can encrypt a plaintext $m$ to Bob by saying something like "this is encrypted with one-time pad, using key #674696273" and sending $T[674696273] \oplus m$. Seeing the number 674696273 doesn't help the eavesdropper know what $T[674696273]$ is. A PRF allows Alice $\&$ Bob to do the same encryption while sharing only a short key $k$. Instead of a the huge table $T$, they can instead use a PRF $F(k, \cdot)$ to derive a common pseudorandom value. Knowing a value $r$ doesn't help the adversary predict $F(k, r),$ when $k$ is secret.

So, translated into more precise PRF notation, an encryption of $m$ will look like $(r, F(k, r) \oplus m) .$ Since Bob also has $k,$ he can decrypt any ciphertext of this form by computing $F(k, r)$ and XOR'ing the second ciphertext component to recover $m .$

It remains to decide how exactly Alice will choose $r$ values. We argued, informally, that as long as these $r$ values don't repeat, security is preserved. This is indeed true, and the distinctness of the $r$ values is critical. Recall that there are 3 ways to avoid deterministic encryption, and all 3 of them would work here:
- In a **stateful** encryption, $r$ could be used as a counter. Use $r=i$ to encrypt/decrypt the $i$ th ciphertext.
- In a **randomized** encryption, choose $r$ uniformly at random for each encryption. If the $r$ values are long enough strings, then repeating an $r$ value should be negligibly likely.
- In a **nonce-based** encryption, we can simply let $r$ be the nonce. In the nonce-based setting, it is guaranteed that these values won't repeat.

In this section we will show the security proof for the case of randomized encryption, since it is the most traditional setting and also somewhat more robust than the others.

The exercises explore how the nonce-based approach is more fragile when this scheme is
extended in natural ways.

**Construction 7.4**
Let $F$ be a secure PRF with *in* = $\lambda$. Define the following encryption scheme based on $F$ :

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\begin{array}{ll}
& \underline{\text { Enc }(k, m):}\\
\mathcal{K}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}  &\quad r \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\ \mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\text {out }} &\quad x:=F(k, r) \oplus m \\
 C=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \times\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\text {out }} &\quad \text { return }(r, x) \\\\
 \underline{\text { KeyGen: }} & \underline{\operatorname{Dec}(k,(r, x)):}\\
 \quad k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} &\quad m:=F(k, r) \oplus x\\
 \quad\text{return}\ k &\quad \text{return}\ m
 \end{array}\\\hline
\end{array}
$$

It is easy to check that the scheme satisfies the correctness property.

**Claim 7.5**
Construction 7.4 has CPAS security (and therefore CPA security) if $F$ is a secure PRF.

The proof has more steps than other proofs we have seen before, and some steps are subtle. So let us use a Socratic dialogue to illustrate the strategy behind the proof:

**SALVIATI:** 
The ciphertexts of Construction 7.4 are indistinguishable from uniform randomness.

**SIMPLICIO:** 
Salviati, you speak with such confidence! Do tell me why you say that these ciphertexts appear pseudorandom.

**SALVIATI:** 
Simple! The ciphertexts have the form $(r, F(k, r) \oplus m) .$ By its very definition, $r$ is chosen uniformly, while $F(k, r) \oplus m$ is like a one-time pad ciphertext which is also uniformly distributed.

**SIMPLICIO:** 
 Your statement about $r$ is self-evident but $F(k, r) \oplus m$ confuses me. This does not look like the one-time pad that we have discussed. For one thing, the same $k$ is used "every time," not "one-time."

**SALVIATI:** 
 I did say it was merely "like" one-time pad. The one-time pad "key" is not k but $F(k, r)$. And since $F$ is a pseudorandom function, all its outputs will appear independently uniform (not to mention uncorrelated with their respectiver), even when the same seed is used every time. Is this not what we require from a one-time pad key?

**SIMPLICIO:** 
 I see, but surely the outputs of $F$ appear independent only when its inputs are distinct? I know that $F$ is deterministic, and this may lead to the same "onetime pad key" being used on different occasions.

**SALVIATI:** 
Your skepticism serves you well in this endeavor, Simplicio. Indeed, the heart of your concern is that Alice may choose $r$ such that it repeats. I say that this is negligibly likely, so that we can safely ignore such a bothersome event.

**SIMPLICIO:** 
 Bothersome indeed, but why do you say that $r$ is unlikely to repeat?
 
**SALVIATI:**
Oh Simplicio, now you are becoming bothersome! This value $r$ is $\lambda$ bits long and chosen uniformly at random each time. Do you not recall our agonizingly long discussion about the birthday paradox? 

**SIMPLICIO:** 
Oh yes, now I remember it well. Now I believe I understand all of your reasoning: Across all ciphertexts that are generated, $r$ is unlikely to repeat because of the birthday paradox. Now, provided that $r$ never repeats, Alice invokes the PRF on distinct inputs. A PRF invoked on distinct inputs provides outputs that are uniformly random for all intents and purposes. Hence, using these outputs as one-time pads completely hides the plaintext. Is that right, Salviati?

**SALVIATI:**
Excellent! Now we may return to discussing the motion of the Sun and Earth $\ldots$

Look for Simplicio’s final summary to be reflected in the sequence of hybrids used in the formal proof:

**Proof**
We prove that $\mathcal{L}_{\text{cpa}\varPhi-\text{real}}^\Sigma\approx\mathcal{L}_{\text{cpa}\varPhi-\text{rand}}^\Sigma$ using the hybrid technique:

$$
\def\arraystretch{1.5}
 \mathcal{L}_{\text{cpa}\varPhi-\text{real}}^\Sigma:\ \ 
\begin{array}{|l|}\hline
\qquad  \mathcal{L}_{\text{cpa}\varPhi-\text{real}}^\Sigma\\\hline
k\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\\\
\underline{\text{CTXT}(m):}\\
\quad \colorbox{yellow}{r}\leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad \colorbox{yellow}{x:=}F(k,r)\oplus m\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{l}
\text{The starting point is $\mathcal{L}_{\text{cpa}\varPhi-\text{real}}^\Sigma$. The details}\\
\text{of $\Sigma$ have been filled in and highlighted.}
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\underline{\text{CTXT}(m):}\\
\quad r\leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^\lambda\\
\quad \colorbox{yellow}{z:=}\text{LOOKUP}(r) \\
\quad x:=\colorbox{yellow}{z}\oplus m\\
\quad \text{return}\ (r,x)\\\hline
\end{array}
\diamond
\begin{array}{|l|}\hline
\qquad \mathcal{L}_{\text{prf-real}}^F\\\hline
\quad r\leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^\lambda\\\\
\underline{\text{LOOKUP}(r):}\\
\quad \text{return}\ F(k,r)\\\hline
\end{array}
\quad
\begin{array}{l}
\text{The statements pertaining to the PRF have}\\
\text{been factored out in terms of the  $\mathcal{L}_{\text{prf-real}}^F$}\\
\text{library.}
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\underline{\text{CTXT}(m):}\\
\quad r\leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^\lambda\\
\quad z:=\text{LOOKUP}(r) \\
\quad x:=z\oplus m\\
\quad \text{return}\ (r,x)\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \mathcal{L}_{\text{prf-rand}}^F\\\hline
T:=\text{empty}\\\\
\underline{\text{LOOKUP}(r):}\\
\quad\text{if}\ T [r ]\ \text{undefined:}\\
\qquad T[r]\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{out}\\
\quad \text{return}\ T[r]\\\hline
\end{array}
\quad
\begin{array}{l}
\text{We have replaced $\mathcal{L}_{\text{prf-real}}^F$ with $\mathcal{L}_{\text{prf-rand}}^F$.}\\
\text{From the PRF security of $F$ , these two hybrids}\\
\text{are indistinguishable.}
\end{array}
$$

At this point in the proof, it is easy to imagine that we are done. Ciphertexts have the form $(r , x)$, where $r$ is chosen uniformly and $x$ is the result of encrypting the plaintext with what appears to be a one-time pad. Looking more carefully, however, the “one-time pad key" is $T[r]-$ a value that could potentially be used more than once if $r$ is ever repeated! 

As Simplicio rightly pointed out, a PRF gives independently random(-looking) outputs when called on distinct inputs. But in our current hybrid there is no guarantee that PRF inputs are distinct! Our proof must explicitly contain reasoning about why PRF inputs are unlikely to be repeated. We do so by appealing to the sampling-with-replacement lemma of Lemma 4.11 .

We first factor out the sampling of $r$ values into a subroutine. The subroutine corresponds to the $\mathcal{L}_{\text {samp-L }}$ library of Lemma 4.11 :

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\underline{\text{CTXT}(m):}\\
\quad r\leftarrow\colorbox{yellow}{SAMP()}\\
\quad z:=\text{LOOKUP}(r) \\
\quad x:=z\oplus m\\
\quad \text{return}\ (r,x)\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \mathcal{L}_{\text{prf-rand}}^F\\\hline
T:=\text{empty}\\\\
\underline{\text{LOOKUP}(r):}\\
\quad\text{if}\ T [r ]\ \text{undefined:}\\
\qquad T[r]\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{out}\\
\quad \text{return}\ T[r]\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \mathcal{L}_{\text{samp-L}}\\\hline
\underline{\text{SAMP():}}\\
\quad r\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\\
\quad \text{return}\ r\\\hline
\end{array}
$$

Next, $\mathcal{L}_{\text{samp-L}}$ is replaced by $\mathcal{L}_{\text{samp-R}}$. By Lemma 4.11, the difference is indistinguishable:

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\underline{\text{CTXT}(m):}\\
\quad r\leftarrow\colorbox{yellow}{SAMP()}\\
\quad z:=\text{LOOKUP}(r) \\
\quad x:=z\oplus m\\
\quad \text{return}\ (r,x)\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \mathcal{L}_{\text{prf-rand}}^F\\\hline
T:=\text{empty}\\\\
\underline{\text{LOOKUP}(r):}\\
\quad\text{if}\ T [r ]\ \text{undefined:}\\
\qquad T[r]\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{out}\\
\quad \text{return}\ T[r]\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \mathcal{L}_{\text{samp-R}}\\\hline
R:=\emptyset\\\\
\underline{\text{SAMP():}}\\
\quad r\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\backslash R\\
\quad R:=R\cup\{r\}\\
\quad \text{return}\ r\\\hline
\end{array}
$$

Inspecting the previous hybrid, we can reason that the arguments to lookup are guaranteed to never repeat. Therefore the $\mathcal{L}_{\text{prf-rand}}^F$ library can be greatly simplified. In particular, the if-condition in $\mathcal{L}_{\text{prf-rand}}^F$ is always true. Simplifying has no effect on the library’s output behavior:

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\underline{\text{CTXT}(m):}\\
\quad r\leftarrow SAMP()\\
\quad z:=\text{LOOKUP}(r) \\
\quad x:=z\oplus m\\
\quad \text{return}\ (r,x)\\\hline
\end{array}
\diamond
\begin{array}{|l|}\hline
\underline{\text{LOOKUP}(r):}\\
\qquad t\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{out}\\
\quad \text{return}\ t\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \mathcal{L}_{\text{samp-R}}\\\hline
R:=\emptyset\\\\
\underline{\text{SAMP():}}\\
\quad r\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\backslash R\\
\quad R:=R\cup\{r\}\\
\quad \text{return}\ r\\\hline
\end{array}
$$

Now we are indeed using unique one-time pads to mask the plaintext. We are in much better shape than before. Recall that our goal is to arrive at a hybrid in which the outputs of CTXT are chosen uniformly. These outputs include the value $r$ , but now $r$ is *no longer being chosen uniformly*! We must revert $r$ back to being sampled uniformly, and then we are nearly to the finish line.

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\underline{\text{CTXT}(m):}\\
\quad r\leftarrow SAMP()\\
\quad z:=\text{LOOKUP}(r) \\
\quad x:=z\oplus m\\
\quad \text{return}\ (r,x)\\\hline
\end{array}
\diamond
\begin{array}{|l|}\hline
\underline{\text{LOOKUP}(r):}\\
\quad t\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{out}\\
\quad \text{return}\ t\\\hline
\end{array}
\diamond
\begin{array}{|l|}\hline
\quad \mathcal{L}_{\text{samp-L}}\\\hline
\underline{\text{SAMP():}}\\
\quad r\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\\
\quad \text{return}\ r\\\hline
\end{array}
\quad
\begin{array}{l}
\text{As promised, $\mathcal{L}_{\text{samp-R}}$ has been}\\
\text{replaced by $\mathcal{L}_{\text{samp-L}}$. The}\\
\text{difference is indistinguishable}\\
\text{due to Lemma 4.11.}
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\underline{\text{CTXT}(m):}\\
\quad \colorbox{yellow}{r}\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\\
\quad \colorbox{yellow}{z}\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{out}\\
\quad x:=z\oplus m\\
\quad \text{return}\ (r,x)\\\hline
\end{array}
\quad
\begin{array}{l}
\text{All of the subroutine calls have}\\
\text{been inlined; no effect on the}\\
\text{library’s output behavior.}
\end{array}
$$

$$
\def\arraystretch{1.5}
\mathcal{L}_{\text{cpa}\varPhi-\text{rand}}^\Sigma:\ 
\begin{array}{|l|}\hline
\qquad\mathcal{L}_{\text{cpa}\varPhi-\text{rand}}^\Sigma\\\hline
\underline{\text{CTXT}(m):}\\
\quad r\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\\
\quad \colorbox{yellow}{x}\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{out}\\
\quad \text{return}\ (r,x)\\\hline
\end{array}
\quad
\begin{array}{l}
\text{We have applied the one-time pad rule with respect to variables $z$}\\
\text{and $x$, but omitted the very familiar steps (factor out, replace library,}\\
\text{inline) that we have seen several times before. The resulting library is}\\
\text{precisely $\mathcal{L}_{\text{cpa}\varPhi-\text{rand}}^\Sigma$ since it samples uniformly from $\Sigma.\mathcal{C}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\times \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{text{out}}$.}
\end{array}
$$

The sequence of hybrids shows that $\mathcal{L}_{\text{cpa}\varPhi-\text{real}}^\Sigma \approx \mathcal{L}_{\text{cpa}\varPhi-\text{rand}}^\Sigma$, so $\Sigma$ has pseudorandom ciphertexts.

### Exercises

7.1. Let $\Sigma$ be an encryption scheme, and suppose there is a program $\mathcal{A}$ that recovers the key from a chosen plaintext attack. More precisely, $\text{Pr}[\mathcal{A}\diamond\mathcal{L}\ \text{outputs}\ k]$ is non-negligible, where $\mathcal{L}$ is defined as:

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad \qquad \quad\ \ \mathcal{L}\\\hline
k\leftarrow \Sigma.\text{KeyGen}\\
\underline{\text{CHALLENGE}(m\in\Sigma.\mathcal{M}):}\\
\quad c:=\Sigma.\text{Enc}(k,m)\\
\quad \text{return}\ c\\\hline
\end{array}
$$

Prove that if such an $\mathcal{A}$ exists, then $\Sigma$ does not have CPA security. Use $\mathcal{A}$ as a subroutine in a distinguisher that violates the CPA security definition.

In other words, CPA security implies that it should be hard to determine the key from seeing encryptions of chosen plaintexts.

7.2. Let $\Sigma$ be an encryption scheme with CPA$ security. Let $\Sigma'$ be the encryption scheme defined by:

$$\Sigma'.\text{Enc}(k,m)=\textcolor{brown}{00}\|\Sigma.\text{Enc}(k,m)$$

The decryption algorithm in $\Sigma'$ simply throws away the first two bits of the ciphertext and then calls $\Sigma$.Dec.

(a) Does $\Sigma'$ have CPA$\varPhi$ security? Prove or disprove (if disproving, show a distinguisher and calculate its advantage).
(b) Does $\Sigma'$ have CPA security? Prove or disprove (if disproving, show a distinguisher and
calculate its advantage).

7.3. Suppose a user is using Construction 7.4 and an adversary observes two ciphertexts that
have the same $r$ value.

(a) What exactly does the adversary learn about the plaintexts in this case?
(b) How do you reconcile this with the fact that in the proof of Claim 7.5 there is a hybrid where $r$ values are *never* repeated?

7.4. Construction 7.4 is a randomized encryption scheme, but we could also consider dening it as a **nonce-based** scheme, interpreting r as the nonce: $\text{Enc}(k,r,m)=(r,F(k,r)\oplus m)$. Formally prove that it is secure as a deterministic, nonce-based scheme. In other words, show that the following two libraries are indistinguishable, where $\Sigma$ refers to Construction 7.4.

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
k\leftarrow \Sigma.\text{KeyGen}\\
V:=\emptyset\\\\
\underline{\text{EAVESDROP}(v, m_L,m_R\in\Sigma.\mathcal{M}):}\\
\quad \text{if}\ v\in V:\ \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad V:=V\cup\{v\}\\
\quad c:=\Sigma.\text{Enc}(k,v,m_L)\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
k\leftarrow \Sigma.\text{KeyGen}\\
V:=\emptyset\\\\
\underline{\text{EAVESDROP}(v, m_L,m_R\in\Sigma.\mathcal{M}):}\\
\quad \text{if}\ v\in V:\ \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad V:=V\cup\{v\}\\
\quad c:=\Sigma.\text{Enc}(k,v,m_R)\\
\quad \text{return}\ c\\\hline
\end{array}
$$

7.5. Let $F$ be a secure PRP with blocklength $blen = \lambda$. Consider the following randomized encryption scheme:

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\begin{array}{lll}
\mathcal{K}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}  & \underline{\text { KeyGen: }} & \underline{\text { Enc }(k, m):}\\
\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} &\quad k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}   &\quad r \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\ C=(\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda})^2&\quad \text{return}\ k &\quad x:=F(k, r) \oplus m \\
  & &\quad \text { return }(r, x) 
 \end{array}\\\hline
\end{array}
$$

(a) Give the decryption algorithm for this scheme.
(b) Prove that the scheme has CPA$\varPhi$ security.
(c ) Suppose that we interpret this scheme as a nonce-based scheme, where v is the nonce. Show that the scheme does not have nonce-based CPA security. The libraries for this definition are given in the previous problem.
Note: Even in the standard CPA libraries, $v$ is given to the adversary and it is unlikely to repeat. However, in the nonce-based libraries the adversary can choose $v$, and this is what leads to problems.

7.6. Let $F$ be a secure PRP with blocklength $blen =\lambda$. Show the the following scheme has pseudorandom ciphertexts:
$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\begin{array}{ll}
& \underline{\text { Enc }(k, m):}\\
\mathcal{K}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}  &\quad s \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\ \mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} &\quad z:=F(k, s \oplus m) \oplus m \\
 C=(\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda})^2 &\quad \text { return }(s\oplus m, z) \\\\
 \underline{\text { KeyGen: }} & \underline{\operatorname{Dec}(k,(r, z)):}\\
 \quad k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} &\quad  \text{return}\ F(k, r) \oplus x\\
 \quad\text{return}\ k &
 \end{array}\\\hline
\end{array}
$$
> Hint: Rewrite Enc to include a new variable $r := s \oplus m$ and write the output in terms of $r$ instead of $s$. You might then recognize a familiar face

7.7. Let $F$ be a secure PRP with blocklength $blen$ $=\lambda$. Below are several encryption schemes, each with $\mathcal{K}=\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}$ and $C=\left(\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\right)^{2} .$ For each one:
- Give the corresponding Dec algorithm.
- State whether the scheme has CPA security. (Assume KeyGen samples the key uniformly from $\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} .$ ) If so, then give a security proof. If not, then describe a successful adversary and compute its distinguishing bias.
$$
\def\arraystretch{1.5}
\text{(a)}\quad
\begin{array}{|l|}\hline
\underline{\text { Enc }(k, m):}\\
\quad r \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad z:=F(k, m) \oplus r \\
\quad \text { return }(r, z) \\\hline
\end{array}
$$

$$
\def\arraystretch{1.5}
\text{(b)}\quad
\begin{array}{|l|}\hline
\underline{\text { Enc }(k, m):}\\
\quad r \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad s:=r\oplus m\\
\quad x:=F(k, r)  \\
\quad \text { return }(s, x) \\\hline
\end{array}
$$

$$
\def\arraystretch{1.5}
\text{(c)}\quad
\begin{array}{|l|}\hline
\underline{\text { Enc }(k, m):}\\
\quad r \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad x:=F(k, r)  \\
\quad y:=r\oplus m\\
\quad \text { return }(x, y) \\\hline
\end{array}
$$

$$
\def\arraystretch{1.5}
\text{(d)}\quad
\begin{array}{|l|}\hline
\underline{\text { Enc }(k, m):}\\
\quad r \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad x:=F(k, r)  \\
\quad y:=F(k, r)\oplus m\\
\quad \text { return }(x, y) \\\hline
\end{array}
$$

$$
\def\arraystretch{1.5}
\text{(e)}\quad
\begin{array}{|l|}\hline
\underline{\text { Enc }(k, m):}\\
\quad r \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad x:=F(k, r)  \\
\quad y:=r\oplus F(k, m)\\
\quad \text { return }(x, y) \\\hline
\end{array}
$$

$$
\def\arraystretch{1.5}
\text{(f)}\quad
\begin{array}{|l|}\hline
\underline{\text { Enc }(k, m):}\\
\quad s_1 \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad s_2:=s_1\oplus m\\
\quad x:=F(k, s_1)  \\
\quad y:=F(k, s_2)  \\
\quad \text { return }(x, y) \\\hline
\end{array}
$$

$$
\def\arraystretch{1.5}
\star\ \text{(g)}\quad
\begin{array}{|l|}\hline
\underline{\text { Enc }(k, m):}\\
\quad r \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad x:=F(k,m\oplus r)\oplus r \\
\quad \text { return }(r, x) \\\hline
\end{array}
$$

>Hint: In all security proofs, you can use the PRP switching lemma (Lemma 6.7) to start with the assumption that $F$ is a PRF.

7.8. Suppose $F$ is a secure PRP with blocklength $n +\lambda$. Below is the encryption algorithm for a scheme that supports plaintext space $\mathcal{M} = \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n}$:

$$
\def\arraystretch{1.5}
\star\ \text{(g)}\quad
\begin{array}{|l|}\hline
\underline{\text { Enc }(k, m):}\\
\quad r \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\
\quad \text { return }\ F(k,m\|r) \\\hline
\end{array}
$$

(a) Describe the corresponding decryption algorithm.
(b) Prove that the scheme satisfies CPA$\varPhi$ security.

$\star$ 7.9. Suppose $F$ is a secure PRP with blocklength $\lambda$. Give the decryption algorithm for the following scheme and prove that it does not have CPA security:

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\begin{array}{lll}
\mathcal{K}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}  & \underline{\text { KeyGen: }} & \underline{\text { Enc }(k, m_1\|m_2):}\\
\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{2\lambda} &\quad k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}   &\quad r \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\ C=(\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda})^3&\quad \text{return}\ k &\quad s:=F(k, r \oplus m_1) \\
& & \quad t:=F(k,r\oplus m_1\oplus F(k,m_1)\oplus m_2)\\
  & &\quad \text { return }(r,s,t) 
 \end{array}\\\hline
\end{array}
$$

$\star$ 7.10. Suppose $F$ is a secure PRP with blocklength $\lambda$. Give the decryption algorithm for the following scheme and prove that it satisfies CPA$\varPhi$ security:

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\begin{array}{lll}
\mathcal{K}=(\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda})^2  & \underline{\text { KeyGen: }} & \underline{\text { Enc }(k, m_1\|m_2):}\\
\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} &\quad k \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}   &\quad s \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda} \\ C=(\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda})^2 & \quad r \leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}&\quad x:=F(k, s) \\
&\quad \text{return}\ (k,r)  & \quad y:=F(k,s\oplus m\oplus r)\\
  & &\quad \text { return }(x,y) 
 \end{array}\\\hline
\end{array}
$$

>Hint: You may find it useful to divide the Enc algorithm into two cases by introducing an “if $m = r$” statement.

Note: If $r = \textcolor{brown}{0}^\lambda$ then the scheme reduces to Exercise 7.7 (f). So it is important that $r$ is secret and random.

7.11. Let $\Sigma$ be an encryption scheme with plaintext space $\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n}$ and ciphertext space $C=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n} .$ Prove that $\Sigma$ cannot have CPA security.

Conclude that direct application of a PRP to the plaintext is not a good choice for an encryption scheme.

$\star 7.12 .$ In all of the CPA-secure encryption schemes that we'll ever see, ciphertexts are at least $\lambda$ bits longer than plaintexts. This problem shows that such ciphertext expansion is essentially unavoidable for CPA security.

Let $\Sigma$ be an encryption scheme with plaintext space $\mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n}$ and ciphertext space $C=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n+\ell}$. Show that there exists a distinguisher that distinguishes the two CPA libraries with advantage $\Omega\left(1 / 2^{\ell}\right)$.

>Hint:  As a warmup, consider the case where each plaintext has exactly $2^\ell$ possible ciphertexts. However, this need not be true in general. For the general case, choose a random plaintextm and argue that with “good probability” (that you should precisely quantify)m has at most $2^{\ell+1}$ possible ciphertexts.

7.13. Show that an encryption scheme $\Sigma$ has CPA security if and only if the following two
libraries are indistinguishable:

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad \qquad \quad\mathcal{L}_{\text{left}}^\Sigma\\\hline
k\leftarrow \Sigma.\text{KeyGen}\\\\
\underline{\text{CHALLENGE}(m\in\Sigma.\mathcal(M):}\\
\quad \text{return}\ \Sigma.\text{Enc}(k,m)\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \qquad \quad\mathcal{L}_{\text{right}}^\Sigma\\\hline
k\leftarrow \Sigma.\text{KeyGen}\\\\
\underline{\text{CHALLENGE}(m\in\Sigma.\mathcal(M):}\\
\quad m'\leftarrow \Sigma.\mathcal{M}\\
\quad \text{return}\ \Sigma.\text{Enc}(k,m')\\\hline
\end{array}
$$

In plain language: if these libraries are indistinguishable, then encryptions of chosen plaintexts are indistinguishable from encryptions of random plaintexts. You must prove both directions!


7.14. Let $\Sigma_{1}$ and $\Sigma_{2}$ be encryption schemes with $\Sigma_{1} \cdot \mathcal{M}=\Sigma_{2} \cdot \mathcal{M}=\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n}$.

Consider the following approach for encrypting plaintext $m \in\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{n}:$ First, secret-share $m$ using any 2 -out-of- 2 secret-sharing scheme. Then encrypt one share under $\Sigma_{1}$ and the other share under $\Sigma_{2}$. Release both ciphertexts.

(a) Formally describe the algorithms of this encryption method.
(b) Prove that the scheme has CPA security if at least one of $\left\{\Sigma_{1}, \Sigma_{2}\right\}$ has CPA security. In other words, it is not necessary that *both* $\Sigma_{1}$ and $\Sigma_{2}$ are secure. This involves proving two cases (assuming $\Sigma_{1}$ is secure, and assuming $\Sigma_{2}$ is secure).

