
# 15. Public-Key Encryption

So far, the encryption schemes that we’ve seen are **symmetric-key** schemes. The same key is used to encrypt and decrypt. In this chapter we introduce **public-key** (sometimes called *asymmetric*) encryption schemes, which use different keys for encryption and decryption. The idea is that the encryption key can be made *public*, so that anyone can send an encryption to the owner of that key, even if the two users have never spoken before and have no shared secrets. The decryption key is private, so that only the designated owner can decrypt.

We modify the syntax of an encryption scheme in the following way. A public-key encryption scheme consists of the following three algorithms:

KeyGen: Outputs a *pair (pk, sk)* where *pk* is a public key and *sk* is a private/secret key.

Enc: Takes the public key *pk* and a plaintext $m$ as input, and outputs a ciphertext $c$.

Dec: Takes the secret key *sk* and a ciphertext $c$ as input, and outputs a plaintext $m$.

We modify the correctness condition similarly. A public-key encryption scheme satisfies *correctness* if, for all $m \in \mathcal{M}$ and all ($pk, sk$) $\leftarrow$ KeyGen, we have Dec($sk$, Enc$(pk,m)) = m$ (with probability 1 over the randomness of Enc).


## 15.1 Security Definitions

We now modify the definition of CPA security to fit the setting of public-key encryption. As before, the adversary calls a CHALLENGE subroutine with two plaintexts — the difference between the two libraries is which plaintext is actually encrypted. Of course, the encryption operation now takes the public key.

Then the biggest change is that we would like to make the public key *public*. In other words, the calling program should have a way to learn the public key (otherwise the library cannot model a situation where the public key is known to the adversary). To do this, we simply add another subroutine that returns the public key.

**Definition 15.1** 
*Let $\Sigma$ be a public-key encryption scheme. Then $\Sigma$ is **secure against chosen-plaintext at tacks (CPA secure)** if $\mathcal{L}^{\Sigma}_{\text{pk-cpa-L}} \approx \mathcal{L}^{\Sigma}_{\text{pk-cpa-R}}$, where:*

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad\qquad\quad\mathcal{L}^{\Sigma}_{\text{pk-cpa-L}} \\ \hline
(pk, sk)  \leftarrow \Sigma.\text{KeyGen} \\
\underline{\text{GETPK}():} \\
\quad \text{return } pk \\
\underline{\text{CHALLENGE}(m_L, m_R \in \Sigma. \mathcal{M}):} \\
\quad \text{return } \Sigma. \text{Enc} (pk, m_L) \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline
\qquad\qquad\quad\mathcal{L}^{\Sigma}_{\text{pk-cpa-R}} \\ \hline
(pk, sk)  \leftarrow \Sigma.\text{KeyGen} \\
\underline{\text{GETPK}():} \\
\quad \text{return } pk \\
\underline{\text{CHALLENGE}(m_L, m_R \in \Sigma. \mathcal{M}):} \\
\quad \text{return } \Sigma. \text{Enc} (pk, m_R) \\ \hline
\end{array}
$$

**to-do** 
>*Re-iterate how deterministic encryption still can’t be CPA-secure in the public-key setting.*

### Pseudorandom Ciphertexts

We can modify/adapt the definition of pseudorandom ciphertexts to public-key encryption in a similar way:

**Definition 15.2** 
*Let $\Sigma$ be a public-key encryption scheme. Then $\Sigma$ has **pseudorandom ciphertexts in the presence of chosen-plaintext attacks (CPA$\varPhi$ security)** if $\mathcal{L}^{\Sigma}_{\text{pk-cpa}\Phi\text{-real}} \approx \mathcal{L}^{\Sigma}_{\text{pk-cpa}\Phi\text{-rand}}$, where:*

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad\qquad\mathcal{L}^{\Sigma}_{\text{pk-cpa}\Phi\text{-real}} \\ \hline
(pk, sk)  \leftarrow \Sigma.\text{KeyGen} \\
\underline{\text{GETPK}():} \\
\quad \text{return } pk \\
\underline{\text{CHALLENGE}(m \in \Sigma. \mathcal{M}):} \\
\quad \text{return } \Sigma. \text{Enc} (pk, m) \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline
\qquad\qquad\mathcal{L}^{\Sigma}_{\text{pk-cpa}\Phi\text{-rand}} \\ \hline
(pk, sk)  \leftarrow \Sigma.\text{KeyGen} \\
\underline{\text{GETPK}():} \\
\quad \text{return } pk \\
\underline{\text{CHALLENGE}(m \in \Sigma. \mathcal{M}):} \\
\quad c \leftarrow \Sigma. \mathcal{C} \\
\quad \text{return } c \\ \hline
\end{array}
$$

As in the symmetric-key setting, CPA$\Phi$ security (for public-key encryption) implies CPA security:

**Claim 15.3** 
*Let $\Sigma$ be a public-key encryption scheme. If $\Sigma$ has CPA$\varPhi$ security, then $\Sigma$ has CPA security.*

The proof is extremely similar to the proof of the analogous statement for symmetric key encryption (Theorem 7.3), and is left as an exercise.


## 15.2 One-Time Security Implies Many-Time Security

So far, everything about public-key encryption has been directly analogous to what we’ve seen about symmetric-key encryption. We now discuss a peculiar property that is different between the two settings.

In symmetric-key encryption, we saw examples of encryption schemes that are secure when the adversary sees only one ciphertext, but insecure when the adversary sees more ciphertexts. One-time pad is the standard example of such an encryption scheme.

Surprisingly, if a *public-key* encryption scheme is secure when the adversary sees just one ciphertext, then it is also secure for many ciphertexts! In short, there is no public-key one-time pad that is weaker than full-fledged public-key encryption — there is public-key encryption or nothing.

To show this property formally, we first adapt the definition of one-time secrecy (Definition 2.6) to the public-key setting. There is one small but important technical subtlety: in Definition 2.6 the encryption key is chosen at the last possible moment in the body of CHALLENGE. This ensures that the key is local to this scope, and therefore each value of the key is only used to encrypt one plaintext.

In the public-key setting, however, it turns out to be important to allow the adversary to see the public key before deciding which plaintexts to encrypt. (This concern is not present in the symmetric-key setting precisely because there is nothing public upon which the adversary’s choice of plaintexts can depend.) For that reason, in the public-key setting we must sample the keys at initialization time so that the adversary can obtain the public key via GETPK. To ensure that the key is used to encrypt only one plaintext, we add a counter and a guard condition to CHALLENGE, so that it only responds once with a ciphertext.

**Definition 15.4** 
*Let $\Sigma$ be a public-key encryption scheme. Then $\Sigma$ has **one-time secrecy** if $\mathcal{L}^{\Sigma}_{\text{pk-ots-L}} \approx \mathcal{L}^{\Sigma}_{\text{pk-ots-R}}$, where:*

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad\qquad\quad\mathcal{L}^{\Sigma}_{\text{pk-ots-L}} \\ \hline
(pk, sk)  \leftarrow \Sigma.\text{KeyGen} \\
\colorbox{Yellow}{count := 0} \\
\underline{\text{GETPK}():} \\
\quad \text{return } pk \\
\underline{\text{CHALLENGE}(m_L, m_R \in \Sigma. \mathcal{M}):} \\
\quad \colorbox{Yellow}{count := count + 1} \\
\quad \colorbox{Yellow}{\text{if } count > 1: \text{return null}}\\
\quad \text{return } \Sigma. \text{Enc} (pk, m_L) \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline
\qquad\qquad\quad\mathcal{L}^{\Sigma}_{\text{pk-ots-R}} \\ \hline
(pk, sk)  \leftarrow \Sigma.\text{KeyGen} \\
\colorbox{Yellow}{count := 0} \\
\underline{\text{GETPK}():} \\
\quad \text{return } pk \\
\underline{\text{CHALLENGE}(m_L, m_R \in \Sigma. \mathcal{M}):} \\
\quad \colorbox{Yellow}{count := count + 1} \\
\quad \colorbox{Yellow}{\text{if } count > 1: \text{return null}} \\
\quad \text{return } \Sigma. \text{Enc} (pk, m_R) \\ \hline
\end{array}
$$

**Claim 15.5** 
*Let $\Sigma$ be a public-key encryption scheme. If $\Sigma$ has one-time secrecy, then $\Sigma$ is CPA-secure.*

**Proof** 
Suppose $\mathcal{L}^{\Sigma}_{\text{pk-ots-L}} \approx \mathcal{L}^{\Sigma}_{\text{pk-ots-R}}$. Our goal is to show that $\mathcal{L}^{\Sigma}_{\text{pk-cpa-L}} \approx \mathcal{L}^{\Sigma}_{\text{pk-cpa-R}}$. The proof centers around the following hybrid library $\mathcal{L}_{\text{hyb-h}}$, which is designed to be linked to either $\mathcal{L}_{\text{pk-ots-L}}$ or $\mathcal{L}_{\text{pk-ots-R}}$:

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad\qquad\qquad\mathcal{L}_{\text{hyb-}h} \\ \hline
count := 0 \\
pk := \text{GETPK}() \\
\underline{\text{CHALLENGE}(m_L, m_R \in \Sigma. \mathcal{M}):} \\
\quad count := count + 1 \\
\quad \text{if } count < \colorbox{lightgray}{$h$}: \\
\qquad \text{return } \Sigma. \text{Enc} (pk, m_R) \\
\quad \text{else } count = \colorbox{lightgray}{$h$}: \\
\qquad \text{return CHALLENGE}' (m_L, m_R) \\
\quad \text{else } : \\
\qquad \text{return } \Sigma. \text{Enc} (pk, m_L) \\ \hline
\end{array}
$$

Here the value $\colorbox{lightgray}{\textit{h}}$ is an unspecified value that will be a hard-coded constant, and CHALLENGE' (called by the “elsif” branch) and GETPK refer to the subroutine in $\mathcal{L}_{\text{pk-ots-}\star}$. Note that $\mathcal{L}_{\text{hyb-}h}$ is designed so that it only makes one call to CHALLENGE' — in particular, only when its own CHALLENGE subroutine is called for the $h^{th}$  time.

We now make a few observations:

$\mathcal{L}_{\mathrm{hyb}-1} \diamond \mathcal{L}_{\mathrm{pk}-\mathrm{ots}-\mathrm{L}} \equiv \mathcal{L}_{\mathrm{pk}-\mathrm{cpa}-\mathrm{L}}:$ 

>In both libraries, every call to CHALLENGE encrypts the left plaintext. In particular, the first call to CHALLENGE in $\mathcal{L}_{\text{hyb-1}}$ triggers the “elsif” branch, so the challenge is routed to $\mathcal{L}_{\text{pk-ots-L}}$, which encrypts the left plaintext. In all other calls to CHALLENGE, the “else” branch is triggered and the left plaintext is encrypted explicitly.

$\mathcal{L}_{\mathrm{hyb}-h} \diamond \mathcal{L}_{\mathrm{pk}-\mathrm{ots}-\mathrm{R}} \equiv \mathcal{L}_{\mathrm{hyb}-(h+1)} \diamond \mathcal{L}_{\mathrm{pk}-\mathrm{ots}-\mathrm{L}},$

> for all $\colorbox{lightgray}{\textit{h}}$ . In both of these libraries, the first $h$ calls to CHALLENGE encrypt the right plaintext, and all subsequent calls encrypt the left plaintext.

$\mathcal{L}_{\text {hyb- } h} \diamond \mathcal{L}_{\text {pk-ots-L }} \approx \mathcal{L}_{\text {hyb- } h} \diamond \mathcal{L}_{\text {pk-ots-R }},$

> for all $\colorbox{lightgray}{\textit{h}}$. This simply follows from the fact that $\mathcal{L}_{\text{pk-ots-L}} \approx \mathcal{L}_{\text{pk-ots-R}}$.

$\mathcal{L}_{\text {hyb- } q} \diamond \mathcal{L}_{\text {pk-ots-R }} \equiv \mathcal{L}_{\text {pk-cpa-R }},$

where $q$ is the number of times the calling program calls CHALLENGE. In particular, every call to CHALLENGE encrypts the right plaintext.

Putting everything together, we have that:

$$
\begin{aligned} \mathcal{L}_{\mathrm{pk}-\mathrm{cpa}-\mathrm{L}} & \equiv \mathcal{L}_{\mathrm{hyb}-1} \diamond \mathcal{L}_{\mathrm{pk}-\mathrm{ots}-\mathrm{L}} \approx \mathcal{L}_{\mathrm{hyb}-1} \diamond \mathcal{L}_{\mathrm{pk}-\mathrm{ots}-\mathrm{R}} \\ & \equiv \mathcal{L}_{\mathrm{hyb}-2} \diamond \mathcal{L}_{\mathrm{pk}-\mathrm{ots}-\mathrm{L}} \approx \mathcal{L}_{\mathrm{hyb}-2} \diamond \mathcal{L}_{\mathrm{pk}-\mathrm{ots}-\mathrm{R}} \\
& \ \  \vdots \\
&\equiv \mathcal{L}_{\text {hyb- } q} \diamond \mathcal{L}_{\text {pk-ots-L }} \approx \mathcal{L}_{\text {hyb- } q} \diamond \mathcal{L}_{\text {pk-ots-R }} \\
&\equiv \mathcal{L}_{\text {pk-cpa-R }}
\end{aligned}
$$

and so $\mathcal{L}_{\mathrm{pk}-\mathrm{cpa}-\mathrm{L}} \approx \mathcal{L}_{\mathrm{pk}-\mathrm{cpa}-\mathrm{R}}$

The reason this proof goes through for public-key encryption but not symmetric-key encryption is that *anyone can encrypt* in a public-key scheme. In a symmetric-key scheme, it is not possible to generate encryptions without the key. But in a public-key scheme, the encryption key is public.

In more detail, the $\mathcal{L}_{\text {hyb- } h}$ library can indeed obtain $p k$ from $\mathcal{L}_{\text {pk-ots }-\star .}$. It therefore has enough information to perform the encryptions for all calls to CHALLENGE. Indeed, you can think of $\mathcal{L}_{\text {hyb- } 0}$ as doing everything that $\mathcal{L}_{\text {pk-cpa-L }}$ does, even though it doesn't know the secret key. We let $\mathcal{L}_{\text {hyb-} h}$ designate the $\colorbox{lightgray}{\textit{h}}^{\text{th}}$ call to CHALLENGE as a special one to be handled by $\mathcal{L}_{\mathrm{pk}-\mathrm{ots}-\star} .$ This allows us to change the $h^{\text {th }}$ encryption from using $m_{L}$ to $m_{R}$


## 15.3 ElGamal Encryption

ElGamal encryption is a public-key encryption scheme that is based on DHKA.

**Construction 15.6 (ElGamal)**
*The public parameters are a choice of cyclic group $\mathbb{G}$ with n elements and generator $g$.*

$$
\def\arraystretch{1.5}
\begin{array}{|llll|} \hline
\begin{array}{l}
\mathcal{M}=\mathbb{G}\\
\mathcal{C}=\mathbb{G}^2
\end{array}
&
\begin{array}{l}
\underline{\text{KeyGen:}}\\
\quad sk:=a\leftarrow\mathbb{Z}_n\\
\quad pk:=A:=g^a\\
\quad\text{return} (pk,sk)
\end{array}
&
\begin{array}{l}
\underline{\text{Enc}(A,M\in\mathbb{G}):}\\
\quad b\leftarrow \mathbb{Z}_n\\
\quad B:=g^b\\
\quad\text{return} (B,M\cdot A^b)
\end{array}
&
\begin{array}{l}
\underline{\text{Dec}(a,(B,X)):}\\
\quad \text{return} \ X(B^a)^{-1}
\end{array}\\\hline
\end{array}
$$

The scheme satisfies correctness, since for all $M$ :
$$
\begin{aligned}
\operatorname{Dec}(s k, \operatorname{Enc}(p k, M)) &=\operatorname{Dec}\left(s k,\left(g^{b}, M \cdot A^{b}\right)\right) \\
&=\left(M \cdot A^{b}\right)\left(\left(g^{b}\right)^{a}\right)^{-1} \\
&=M \cdot\left(g^{a b}\right)\left(g^{a b}\right)^{-1}=M .
\end{aligned}
$$
### Security
Imagine an adversary who is interested in attacking an ElGamal scheme. This adversary sees the public key $A=g^{a}$ and a ciphertext $\left(g^{b}, M g^{a b}\right)$ go by. Intuitively, the Decisional Diffie-Hellman assumption says that the value $g^{a b}$ looks random, even to someone who has seen $g^{a}$ and $g^{b}$. Thus, the message $M$ is masked with a pseudorandom group element-as we've seen before, this is a lot like masking the message with a random pad as in one-time pad. The only change here is that instead of the XOR operation, we are using the group operation in $\mathbb{G}$. 

More formally, we can prove the security of ElGamal under the DDH assumption:

**Claim 15.7**
*If the DDH assumption in group $\mathbb{G}$ is true, then ElGamal in group $\mathbb{G}$ is CPA$\varPhi$-secure.*

**Proof**
It suffices to show that ElGamal has pseudorandom ciphertexts when the calling program sees only a single ciphertext. In other words, we will show that $\mathcal{L}_{\mathrm{pk}\text {-ots}\Phi-\text{real}}\approx\mathcal{L}_{\mathrm{pk}-\text {ots}\Phi\text{-rand }}$, where these libraries are the $\mathcal{L}_{\mathrm{pk}-\mathrm{cpas}-\star}$ libraries from Definition 15.2 but with the singleciphertext restriction used in Definition 15.4. It is left as an exercise to show that $\mathcal{L}_{\mathrm{pk}\text {-ots}\Phi-\text{real}}\approx\mathcal{L}_{\mathrm{pk}-\text {ots}\Phi\text{-rand }}$ implies CPA$\Phi$ security (which in turn implies CPA security); the proof is very similar to that of Claim 15.5 .

 The sequence of hybrid libraries is given below:
$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad \quad \mathcal{L}_{\text{pk-ots}\Phi-\text{real}} \\ \hline
a\leftarrow \mathbb{Z}_n\\
A:=g^a\\
\text{count}=0\\
\underline{\text{GETPK():}}\\
\quad \text{return $A$}\\
\underline{\text{QUERY}(M\in \mathbb{G}):}\\
\quad \text{count : count+1}\\
\quad \text{if count} > 1:\ \text{return null}\\
\quad b\leftarrow \mathbb{Z}_n\\
\quad B:=g^b\\
\quad X:=M\cdot A^b\\
\quad \text{return} (B,X)\\\hline
\end{array}
\quad
\begin{array}{l}
\text{The starting point is the}\\
\text{$\mathcal{L}_{\text{pk-ots}\varPhi-\text{real}}$ library, shown here}\\
\text{with the details of ElGamal filled}\\
\text{in.}\\
\end{array}
$$

  

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
a\leftarrow \mathbb{Z}_n;b\leftarrow \colorbox{Yellow}{$\mathbb{Z}_n$}\\
A:=g^a;\colorbox{Yellow}{$B:=g^b;C:=A^b$}\\
\textit{count}=0\\
\underline{\text{GETPK():}}\\
\quad \text{return $A$}\\
\underline{\text{QUERY}(M\in \mathbb{G}):}\\
\quad \textit{count : count}+1\\
\quad \text{if count} > 1:\ \text{return null}\\
\quad X:=M\cdot \colorbox{Yellow}{$C$}\\
\quad \text{return} (B,X)\\\hline
\end{array}
\quad
\begin{array}{l}
\text{The main body of QUERY com-}\\
\text{putes some intermediate values}\\
\text{$B$ and $A^b$ . But since those lines}\\
\text{are only reachable one time, it}\\
\text{does not change anything to pre-}\\
\text{compute them at initialization}\\
\text{time.}\\
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\colorbox{Yellow}{$(A,B,C)\leftarrow \text{DHQUERY()}$}\\
\textit{count}=0\\
\underline{\text{GETPK():}}\\
\quad \text{return $A$}\\
\underline{\text{QUERY}(M\in \mathbb{G}):}\\
\quad \text{count : count+1}\\
\quad \text{if count} > 1:\ \text{return null}\\
\quad X:=M\cdot C\\
\quad \text{return} (B,X)\\\hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\qquad\mathcal{L}_{\text{dh-real}}\\\hline
\text{DHQUERY()}\\
\quad a,b\leftarrow \mathbb{Z}_n\\
\quad \text{return} (g^a,g^b,g^{ab})\\\hline
\end{array}
\quad
\begin{array}{l}
\text{We can factor out the genera-}\\
\text{tion of $A, B, C$ in terms of the}\\
\text{$\mathcal{L}_{\text{dh-real}}$ library from the De-}\\
\text{cisional Diffie-Hellman security}\\
\text{definition (Definition 14.5).}\\
\end{array}
$$

 
$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
(A,B,C)\leftarrow \text{DHQUERY()}\\
\textit{count}=0\\
\underline{\text{GETPK():}}\\
\quad \text{return $A$}\\
\underline{\text{QUERY}(M\in \mathbb{G}):}\\
\quad \textit{count : count}+1\\
\quad \text{if}\ \textit{count} > 1:\ \text{return null}\\
\quad X:=M\cdot C\\
\quad \text{return} (B,X)\\\hline
\end{array}
\quad
\begin{array}{|l|} \hline
\qquad\mathcal{L}_{\text{dh-rand}}\\\hline
\text{DHQUERY()}\\
\quad a,b,\colorbox{Yellow}c\leftarrow \mathbb{Z}_n\\
\quad \text{return} (g^a,g^b,\colorbox{Yellow}{$g^{c}$})\\\hline
\end{array}
\quad
\begin{array}{l}
\text{Applying the security of DDH,} \\
\text{we can replace $\mathcal{L}_{\text{dh-real}}$ with} \\
\text{$\mathcal{L}_{\text{dh-rand}}$} \\
\end{array}
$$

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\colorbox{Yellow}{$(a,b,c)\leftarrow \mathbb{Z}_n$}\\
\colorbox{Yellow}{$A:=g^a;B:=g^b;C:=g^c$}\\
\textit{count}=0\\
\underline{\text{GETPK():}}\\
\quad \text{return $A$}\\
\underline{\text{QUERY}(M\in \mathbb{G}):}\\
\quad \textit{count : count}+1\\
\quad \text{if}\ \textit{count} > 1:\ \text{return null}\\
\quad X:=M\cdot C\\
\quad \text{return} (B,X)\\\hline
\end{array}
\qquad \qquad \qquad \qquad
\begin{array}{l}
\text{The call to DHQUERY has been in-}\\
\text{lined.}\\
\end{array}
$$


$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
a\leftarrow \mathbb{Z}_n\\
A:=g^a\\
\textit{count}=0\\
\underline{\text{GETPK():}}\\
\quad \text{return $A$}\\
\underline{\text{QUERY}(M\in \mathbb{G}):}\\
\quad \textit{count : count}+1\\
\quad \text{if}\ \textit{count} > 1:\ \text{return null}\\
\quad \colorbox{Yellow}{$b,c\leftarrow \mathbb{Z}_n$}\\
\quad \colorbox{Yellow}{$B:=g^b:C:=g^c$}\\
\quad X:=M\cdot C\\
\quad \text{return} (B,X)\\\hline
\end{array}
\qquad \qquad\qquad\qquad
\begin{array}{l}
\text{As before, since the main body of}\\
\text{QUERY is only reachable once, we}\\
\text{can move the choice of $B$ and $C$}\\
\text{into that subroutine instead of at}\\
\text{initialization time.}\\
\end{array}
$$

    

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad \quad \mathcal{L}_{\text{pk-ots}\Phi-\text{rand}} \\ \hline
a\leftarrow \mathbb{Z}_n\\
A:=g^a\\
\textit{count}=0\\
\underline{\text{GETPK():}}\\
\quad \text{return $A$}\\
\underline{\text{QUERY}(M\in \mathbb{G}):}\\
\quad \textit{count : count}+1\\
\quad \text{if}\ \textit{count} > 1:\ \text{return null}\\
\quad b, \colorbox{Yellow}{$x$}\leftarrow \mathbb{Z}_n\\
\quad B:=g^b: \colorbox{Yellow}{$X:=g^x$}\\
\quad \text{return} (B,X)\\\hline
\end{array}
\qquad \qquad\qquad\qquad
\begin{array}{l}
\text{When $b$ is sampled uniformly}\\
\text{from $\mathbb{Z}n$, the expression $B = g^b$}\\
\text{is a uniformly distributed ele-}\\
\text{ment of $\mathbb{G}$. Also recall that when}\\
\text{$C$ is a uniformly distributed el-}\\
\text{ement of $\mathbb{G}$, then $M \cdot C$ is uni-}\\
\text{formly distributed — this is anal-}\\
\text{ogous to the one-time pad prop-}\\
\text{erty (see Exercise 2.5). Applying}\\
\text{this change gives the library to}\\
\text{the left.}\\
\end{array}
$$

     

In the final hybrid, the response to QUERY is a pair of uniformly distributed group elements $(B, X) .$ Hence that library is exactly $\mathcal{L}_{\mathrm{pk}-\text{ots}\Phi\text { -rand }},$ as desired. $\qquad\blacksquare$

## 15.4 Hybrid Encryption
As a rule, public-key encryption schemes are much more computationally expensive than symmetric-key schemes. Taking ElGamal as a representative example, computing $g^{b}$ in a cryptographically secure cyclic group is considerably more expensive than one evaluation of AES. As the plaintext data increases in length, the difference in cost between public-key and symmetric-key techniques only gets worse.

A clever way to minimize the cost of public-key cryptography is to use a method called **hybrid encryption**. The idea is to use the expensive public-key scheme to encrypt a *temporary key* for a symmetric-key scheme. Then use the temporary key to (cheaply) encrypt the large plaintext data.

To decrypt, one can use the decryption key of the public-key scheme to obtain the temporary key. Then the temporary key can be used to decrypt the main payload.

**Construction 15.8 (Hybrid Enc)**
Let $\Sigma_{\text {pub }}$ be a public-key encryption scheme, and let $\Sigma_{\text {sym }}$ be a symmetric-key encryption scheme, where $\Sigma_{\text {sym. }} \mathcal{K} \subseteq \Sigma_{\text {pub. }} \mathcal{M}-$ that is, the public-key scheme is capable of encrypting keys of the symmetric-key scheme. 

Then we define $\Sigma_{\text {hvb }}$ to be the following construction:

$$
\def\arraystretch{1.5}
\begin{array}{|llll|} \hline
\begin{array}{l}
\mathcal{M}=\Sigma_{sym}.\mathcal{M}\\
\mathcal{C}=\Sigma_{pub}.\mathcal{C}\times \Sigma_{sym}.\mathcal{C}\\\\
\underline{\text{KeyGen:}}\\
\quad (pk,sk)\leftarrow\Sigma_{pub}.\text{KeyGen}\\
\quad\text{return} (pk,sk)
\end{array}
&
\begin{array}{l}
\underline{\text{Enc}(pk,m):}\\
\quad tk\leftarrow \Sigma_{sym}.\text{KeyGen}\\
\quad c_{pub}\leftarrow \Sigma_{pub}.\text{Enc}(tk,m)\\
\quad\text{return} (c_{pub},c_{sym})\\
\underline{\text{Dec}(sk,(c_{pub},c_{sym})):}\\
\quad tk:=\Sigma_{pub}.\text{Dec}(sk,c_{pub})\\
\quad\text{return}\ \Sigma_{sym}.\text{Dec}(tk,c_{sym})
\end{array}\\\hline
\end{array}
$$

Importantly, the message space of the hybrid encryption scheme is the message space of the symmetric-key scheme (think of this as involving very long plaintexts), but encryption and decryption involves expensive public-key operations only on a small temporary key (think of this as a very short string). 

The correctness of the scheme can be verified via:
$$
\begin{aligned}
\operatorname{Dec}(s k, \operatorname{Enc}(p k, m)) &=\operatorname{Dec}\left(s k,\left(\Sigma_{\mathrm{pub}} \cdot \operatorname{Enc}(p k, t k), \Sigma_{\mathrm{sym}} \cdot \operatorname{Enc}(t k, m)\right)\right) \\
&=\Sigma_{\mathrm{sym}} \cdot \operatorname{Dec}\left(\sum_{\mathrm{pub}} \cdot \operatorname{Dec}\left(s k, \Sigma_{\mathrm{pub}} \cdot \operatorname{Enc}(p k, t k)\right), \Sigma_{\mathrm{sym}} \cdot \operatorname{Enc}(t k, m)\right) \\
&=\Sigma_{\mathrm{sym}} \cdot \operatorname{Dec}\left(t k, \Sigma_{\mathrm{sym}} \cdot \operatorname{Enc}(t k, m)\right) \\
&=m
\end{aligned}
$$
To show that hybrid encryption is a valid way to encrypt data, we prove that it provides CPA security, when its two components have appropriate security properties:

**Claim 15.9**
If $\Sigma_{\text {sym }}$ is a one-time-secret symmetric-key encryption scheme and $\Sigma_{\text {pub is }}$ a CPA-secure publickey encryption scheme, then the hybrid scheme $\Sigma_{\text {hyb }}$ (Construction 15.8) is also a CPA-secure public-key encryption scheme.

Note that $\Sigma_{\text {sym }}$ does not even need to be CPA-secure. Intuitively, one-time secrecy suffices because each temporary key $t k$ is used only once to encrypt just a single plaintext.

**Proof**
As usual, our goal is to show that $\mathcal{L}_{\mathrm{pk}-\text { cpa-L }}^{\Sigma_{\text {hyb }}} \approx \mathcal{L}_{\text {pk-cpa-R }}^{\Sigma_{\text {hyb }}},$ which we do in a standard sequence of hybrids:

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad \qquad \mathcal{L}_{\text{pk-cpa-L}}^{\Sigma \text{hyb}} \\ \hline
(pk,sk)\leftarrow\Sigma_{pub}.\text{KeyGen}\\
\underline{\text{GETPK():}}\\
\quad \text{return $pk$}\\
\underline{\text{CHALLENGE}(m_L,m_R):}\\
\quad tk\leftarrow \Sigma_{sym}.\text{KeyGen}\\
\quad c_{pub}\leftarrow \Sigma_{pub}.\text{Enc}(pk,tk)\\
\quad c_{sym}\leftarrow \Sigma_{sym}.\text{Enc}(tk,m_L)\\
\quad\text{return} (c_{pub},c_{sym})\\\hline
\end{array}
$$
The starting point is $\mathcal{L}_{\mathrm{pk}-\mathrm{cpa}-\mathrm{L}},$ shown here with the details of $\Sigma_{\text {hyb }}$ filled in.

Our only goal is to somehow replace $m_{L}$ with $m_{R}$. Since $m_{L}$ is only used as a plaintext for $\Sigma_{\text {sym }}$, it is tempting to simply apply the one-time-secrecy property of $\Sigma_{\text {sym }}$ to argue that $m_{L}$ can be replaced with $m_{R} .$ Unfortunately, this cannot work because the key used for that ciphertext is $t k$, which is used elsewhere. In particular, it is used as an argument to $\Sigma_{\text {pub. }}$ Enc.

However, using $t k$ as the plaintext argument to $\Sigma_{\text {pub }}$. Enc should hide $t k$ to the calling program, if $\Sigma_{\text {pub }}$ is CPA-secure. That is, the $\Sigma_{\text {pub }}$ -encryption of $t k$ should look like a $\Sigma_{\text {pub }}$ encryption of some unrelated dummy value. More formally, we can factor out the call to $\Sigma_{\text {pub. }}$ Enc in terms of the $\mathcal{L}_{\mathrm{pk}-\mathrm{cpa}-\mathrm{L}}$ library, as follows:

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\underline{\text{CHALLENGE}(m_L,m_R):}\\
\quad tk\leftarrow \Sigma_{sym}.\text{KeyGen}\\
\quad tk'\leftarrow \Sigma_{sym}.\text{KeyGen}\\
\quad c_{pub}\leftarrow \text{CHALLENGE}'(tk,tk')\\
\quad c_{sym}\leftarrow \Sigma_{sym}.\text{Enc}(tk,m_L)\\
\quad\text{return} (c_{pub},c_{sym})\\\hline
\end{array}
\quad
\begin{array}{|l|} \hline
\qquad \qquad \mathcal{L}_{\text{pk-cpa-L}}^{\Sigma \text{pub}} \\ \hline
(pk,sk)\leftarrow\Sigma_{pub}.\text{KeyGen}\\
\underline{\text{GETPK():}}\\
\quad \text{return $pk$}\\
\underline{\text{CHALLENGE}'(tk_L,tk_R):}\\
\quad \text{return} \ \Sigma_{pub}.\text{Enc}(pk,tk_L)\\\hline
\end{array}
$$

Here we have changed the variable names of the arguments of CHALLENGE$'$ to avoid unnecessary confusion. Note also that challenge now chooses *two* temporary keys — one which is actually used to encrypt $m_L$ and one which is not used anywhere. This is because syntactically we must have two arguments to pass into CHALLENGE$'$ .

Now imagine replacing $\mathcal{L}_{\text{pk-cpa-L}}$ with $\mathcal{L}_{\text{pk-cpa-R}}$ then inlining subroutine calls. The result is:

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
(pk,sk)\leftarrow\Sigma_{pub}.\text{KeyGen}\\
\underline{\text{GETPK():}}\\
\quad \text{return $pk$}\\
\underline{\text{CHALLENGE}(m_L,m_R):}\\
\quad tk\leftarrow \Sigma_{sym}.\text{KeyGen}\\
\quad tk'\leftarrow \Sigma_{sym}.\text{KeyGen}\\
\quad c_{pub}\leftarrow \Sigma_{pub}.\text{Enc}(pk,tk')\\
\quad c_{sym}\leftarrow \Sigma_{sym}.\text{Enc}(tk,m_L)\\
\quad\text{return} (c_{pub},c_{sym})\\\hline
\end{array}
$$

At this point, it does now work to factor out the call to $\Sigma_{sym}$.Enc in terms of the $\mathcal{L}_{\text{ots-L}}$ library. This is because the key $tk$ is not used anywhere else in the library. The result of factoring out in this way is:

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
(pk,sk)\leftarrow\Sigma_{pub}.\text{KeyGen}\\
\underline{\text{GETPK():}}\\
\quad \text{return $pk$}\\
\underline{\text{CHALLENGE}(m_L,m_R):}\\
\quad tk'\leftarrow \Sigma_{sym}.\text{KeyGen}\\
\quad c_{pub}\leftarrow \Sigma_{pub}.\text{Enc}(pk,tk')\\
\quad c_{sym}\leftarrow \text{CHALLENGE}'(m_L,m_R)\\
\quad\text{return} (c_{pub},c_{sym})\\\hline
\end{array}
\diamond
\begin{array}{|l|} \hline
\qquad \qquad \mathcal{L}_{\text{ots-L}}^{\Sigma \text{sym}} \\ \hline
\underline{\text{CHALLENGE}'(m_L,m_R):}\\
\quad tk\leftarrow \Sigma_{sym}.\text{KeyGen}\\
\quad \text{return} \ \Sigma_{pub}.\text{Enc}(tk,m_L)\\\hline
\end{array}
$$

At this point, we can replace $\mathcal{L}_{\text {ots-L }}$ with $\mathcal{L}_{\text {ots-R. }}$ After this change the $\Sigma_{\text {sym }}$ -ciphertext encrypts $m_{R}$ instead of $m_{L} .$ This is the "half-way point" of the proof, and the rest of the steps are a mirror image of what has come before. In summary: we inline $\mathcal{L}_{\text {ots-R }},$ then we apply CPA security to replace the $\Sigma_{\text {pub }}$ -encryption of $t k^{\prime}$ with $t k$. The result is exactly $\mathcal{L}_{\mathrm{pk}-\mathrm{cpa}-\mathrm{R}},$ as desired.

### Exercises
15.1. Prove Claim $15.3 .$

15.2. Show that a 2 -message key-agreement protocol exists if and only if CPA-secure public-key encryption exists.

In other words, show how to construct a CPA-secure encryption scheme from any 2 message KA protocol, and vice-versa. Prove the security of your constructions.

15.3. (a) Suppose you are given an ElGamal encryption of an unknown plaintext $M \in \mathbb{G}$. Show how to construct a different ciphertext that also decrypts to the same $M$.

(b) Suppose you are given two ElGamal encryptions, of unknown plaintexts $M_{1}, M_{2} \in \mathbb{G}$. Show how to construct a ciphertext that decrypts to their product $M_{1} \cdot M_{2}$.

15.4. Suppose you obtain two ElGamal ciphertexts $\left(B_{1}, C_{1}\right),\left(B_{2}, C_{2}\right)$ that encrypt unknown plaintexts $M_{1}$ and $M_{2}$. Suppose you also know the public key $A$ and cyclic group generator $g$.

(a) What information can you infer about $M_{1}$ and $M_{2}$ if you observe that $B_{1}=B_{2} ?$

(b) What information can you infer about $M_{1}$ and $M_{2}$ if you observe that $B_{1}=g \cdot B_{2} ?$

$\star$ (c ) What information can you infer about $M_{1}$ and $M_{2}$ if you observe that $B_{1}=\left(B_{2}\right)^{2} ?$