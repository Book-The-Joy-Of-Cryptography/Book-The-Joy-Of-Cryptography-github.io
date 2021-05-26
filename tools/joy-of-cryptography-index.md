# Index of Security Definitions

One-time uniform ciphertexts for symmetric-key encryption (Definition 2.5):

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad \qquad \mathcal{L}_{\text{ots}\Phi-\text{real}}^{\Sigma} \\ \hline
\underline{\text{CTXT}(m\in\Sigma.\mathcal{M}):}\\
\quad k\leftarrow \Sigma.\text{KeyGen}\\
\quad  c\leftarrow \Sigma.\text{Enc}(k,m)\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{|l|} \hline
\qquad \qquad \mathcal{L}_{\text{ots}\Phi-\text{rand}}^{\Sigma} \\ \hline
\underline{\text{CTXT}(m\in\Sigma.\mathcal{M}):}\\
\quad  c\leftarrow \Sigma.C\\
\quad \text{return}\ c\\\hline
\end{array}
$$
One-time secrecy for symmetric-key encryption (Definition 2.6):

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad \qquad \mathcal{L}_{\text{ots-L}}^{\Sigma} \\ \hline
\underline{\text{EAVESDROP}(m_L,m_R\in\Sigma.\mathcal{M}):}\\
\quad k\leftarrow \Sigma.\text{KeyGen}\\
\quad  c\leftarrow \Sigma.\text{Enc}(k,m_L)\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{|l|} \hline
\qquad \qquad \mathcal{L}_{\text{ots-R}}^{\Sigma} \\ \hline
\underline{\text{EAVESDROP}(m_L,m_R\in\Sigma.\mathcal{M}):}\\
\quad k\leftarrow \Sigma.\text{KeyGen}\\
\quad  c\leftarrow \Sigma.\text{Enc}(k,m_R)\\
\quad \text{return}\ c\\\hline
\end{array}
$$

t-out-of-$n$ secret sharing (Definition 3.3):

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

Pseudorandom generator (Definition 5.1):

$$
\def\arraystretch{1.5} 
\begin{array}{|l|} \hline 
\quad\mathcal{L}_{\mathrm{prg}-\mathrm{real}}^{G}\\ \hline
\underline{\text{QUERY():}}\\
\quad s\leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^\lambda\\
\quad \text{return}\ G(s)\\\hline
\end{array}
\qquad
\begin{array}{|l|} \hline 
\quad\mathcal{L}_{\mathrm{prg}-\mathrm{rand}}^{G}\\ \hline
\underline{\text{QUERY():}}\\
\quad s\leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda+\ell}\\
\quad \text{return}\ r\\\hline
\end{array}
$$

Pseudorandom function (Definition 6.1):

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad \qquad\mathcal{L}_{\text{prf-real}}^F\\\hline
k \leftarrow\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\lambda}\\
\underline{\text {LOOKUP}(x \in\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\text {in }}):}\\
\quad {\text { return } F(k, x)}\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \qquad\mathcal{L}_{\text{prf-rand}}^F\\\hline
T:=\text{empty assoc. array}\\\\
\underline{\text {LOOKUP}(x \in\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\text {in }}):}\\
\quad\text{if}\ T[x]\ \text{undefined:}\\
\qquad T[x]\leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^{\text {out }}\\
\quad {\text { return } T[x]}\\\hline
\end{array}
$$

Pseudorandom permutation (Definition 6.6):

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad \qquad\mathcal{L}_{\text{prp-real}}^F\\\hline
k\leftarrow\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\\\\
\underline{\text{LOOKUP}(x\in\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{blen}):}\\
\quad \text{return}\ F(k,x)\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \qquad \quad\mathcal{L}_{\text{prp-rand}}^F\\\hline
T:=\text{empty assoc. array}\\\\
\underline{\text{LOOKUP}(x\in\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{blen}):}\\
\quad \text{if}\ T[x]\ \text{undefined:}\\
\qquad T[x]\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{blen}\backslash T.values\\
\quad \text{return}\ T[x]\\\hline
\end{array}
$$

Strong pseudorandom permutation (Definition 6.13):

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad\qquad \ \ \mathcal{L}_{\text{sprp-real}}^F\\\hline
\quad r\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{\lambda}\\\\
\underline{\text{LOOKUP}(x\in\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{blen}):}\\
\quad \text{return}\ F(k,x)\\\\
\underline{\text{INVLOOKUP}(y\in\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{blen}):}\\
\quad \text{return}\ F^{-1}(k,y)\\\hline
\end{array}\quad
\begin{array}{|l|}\hline
\qquad\qquad \ \ \mathcal{L}_{\text{sprp-rand}}^F\\\hline
T,T_{inv}:=\text{empty assoc. arrays}\\\\
\underline{\text{LOOKUP}(x\in\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{blen}):}\\
\quad \text{if}\ T[x]\ \text{undefined}:\\
\qquad y\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{blen}\backslash T.\text{values}\\
\qquad T[x]:=y:\quad T_{inv}[y]:=x\\
\quad \text{return}\ T[x]\\
\underline{\text{INVLOOKUP}(y\in\{\textcolor{brown}{0},\textcolor{brown}{1}\}^{blen}):}\\
\quad \text{if}\ T_{inv}[y]\ \text{undefined}:\\
\qquad x\leftarrow \{\textcolor{brown}{0},\textcolor{brown}{1}\}^{blen}\backslash T_{inv}.\text{values}\\
\qquad T_{inv}[y]:=x;\quad T[x]:=y\\
\quad \text{return}\ T_{inv}[y]\\\hline
\end{array}
$$
CPA security for symmetric-key encryption (Definition 7.1, Section 8.2):

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad\qquad \qquad \ \mathcal{L}_{\text{cpa-L}}^\Sigma\\\hline
k\leftarrow\Sigma.\text{KeyGen}\\\\
\underline{\text{EAVESDROP}(m_L,m_R\in \Sigma.\mathcal{M}):}\\
\quad\text{if}\ |m_L|\neq |m_R|\ \text{return\ \textcolor{brown}{\texttt{err}}}\\
\quad c:=\Sigma.\text{Enc}(k,{m}_L)\\
\quad \text{return}\ c\\\hline
\end{array}\quad
\begin{array}{|l|}\hline
\qquad\qquad \qquad \ \mathcal{L}_{\text{cpa-R}}^\Sigma\\\hline
k\leftarrow\Sigma.\text{KeyGen}\\\\
\underline{\text{EAVESDROP}(m_L,m_R\in \Sigma.\mathcal{M}):}\\
\quad\text{if}\ |m_L|\neq |m_R|\ \text{return\ \textcolor{brown}{\texttt{err}}}\\
\quad c:=\Sigma.\text{Enc}(k,{m}_R)\\
\quad \text{return}\ c\\\hline
\end{array}
$$

CPA$\Phi$ security for symmetric-key encryption (Definition 7.2, Section 8.2):

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad\ \ \mathcal{L}_{\text{cpa}\Phi-\text{real}}^\Sigma\\\hline
k\leftarrow\Sigma.\text{KeyGen}\\\\
\underline{\text{CHALLENGE}(m\in\Sigma.\mathcal{M}):}\\
\quad c:=\Sigma.\text{Enc}(k,m)\\
\quad \text{return}\ c\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad\quad \ \mathcal{L}_{\text{cpa}\Phi-\text{rand}}^\Sigma\\\hline
\underline{\text{CHALLENGE}(m\in\Sigma.\mathcal{M}):}\\
\quad c:=\Sigma.\mathcal{C}(|m|)\\
\quad \text{return}\ c\\\hline
\end{array}
$$

CCA security for symmetric-key encryption (Definition 9.1):

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad \qquad \quad\mathcal{L}_{\text {cca-L }}^{\Sigma}\\\hline
k\leftarrow \Sigma.\text{KeyGen}\\
S:=\emptyset\\\\
\underline{\text{EAVESDROP}(m_L,m_R\in \Sigma.\mathcal{M}):}\\
\quad\text{if} |m_L|\neq |m_R|\ \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad c:=\Sigma.\text{Enc}(k,m_L)\\
\quad S:=S\cup\{c\}\\
\quad\text{return}\ c\\\\
\underline{\text{DECRYPT}(c\in \Sigma.C):}\\
\quad \text{if}\ c\in S\ \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad \text{return}\ \Sigma.\text{Dec}(k,c)\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \qquad \quad\mathcal{L}_{\text {cca-R }}^{\Sigma}\\\hline
k\leftarrow \Sigma.\text{KeyGen}\\
S:=\emptyset\\\\
\underline{\text{EAVESDROP}(m_L,m_R\in \Sigma.\mathcal{M}):}\\
\quad\text{if} |m_L|\neq |m_R|\ \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad c:=\Sigma.\text{Enc}(k,m_R)\\
\quad S:=S\cup\{c\}\\
\quad\text{return}\ c\\\\
\underline{\text{DECRYPT}(c\in \Sigma.C):}\\
\quad \text{if}\ c\in S\ \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad \text{return}\ \Sigma.\text{Dec}(k,c)\\\hline
\end{array}
$$

CCA$\Phi$ security for symmetric-key encryption (Definition 9.2):

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad \qquad\mathcal{L}_{\text {cca}\Phi-\text{real }}^{\Sigma}\\\hline
k\leftarrow \Sigma.\text{KeyGen}\\
S:=\emptyset\\\\
\underline{\text{CTXT}(m\in \Sigma.\mathcal{M}):}\\
\quad {c:=}\Sigma.\text{Enc}(k,m)\\
\quad S:=S\cup\{c\}\\
\quad\text{return}\ c\\\\
\underline{\text{DECRYPT}(c\in \Sigma.C):}\\
\quad \text{if}\ c\in S\ \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad \text{return}\ \Sigma.\text{Dec}(k,c)\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \qquad \mathcal{L}_{\text {cca}\Phi-\text{rand }}^{\Sigma}\\\hline
k\leftarrow \Sigma.\text{KeyGen}\\
S:=\emptyset\\\\
\underline{\text{CTXT}(m\in \Sigma.\mathcal{M}):}\\
\quad {c}\leftarrow\Sigma.\textit{C}(|m|)\\
\quad S:=S\cup\{c\}\\
\quad\text{return}\ c\\\\
\underline{\text{DECRYPT}(c\in \Sigma.C):}\\
\quad \text{if}\ c\in S\ \text{return}\ \textcolor{brown}{\texttt{err}}\\
\quad \text{return}\ \Sigma.\text{Dec}(k,c)\\\hline
\end{array}
$$

MAC (Definition 10.2):

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad\qquad\mathcal{L}^{\Sigma}_{\text{mac-real}} \\ \hline
k \leftarrow \Sigma.\text{KeyGen} \\
\underline{\text{GETTAG}(m \in \Sigma.\mathcal{M}):} \\
\quad \text{return } \Sigma.\text{MAC}(k,m) \\ 
\underline{\text{CHECKTAG}(m \in \Sigma.\mathcal{M},t):} \\
\quad \text{return } t \overset{?}{=} \Sigma.\text{MAC}(k,m) \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline
\qquad\qquad\mathcal{L}^{\Sigma}_{\text{mac-fake}} \\ \hline
k \leftarrow \Sigma.\text{KeyGen} \\
\mathcal{T} := \empty \\
\underline{\text{GETTAG}(m \in \Sigma.\mathcal{M}):} \\
\quad t := \Sigma.\text{MAC}(k,m) \\ 
\quad \mathcal{T} := \mathcal{T} \cup \{(m,t)\}\\
\quad \text{return } t \\
\underline{\text{CHECKTAG}(m \in \Sigma.\mathcal{M},t):} \\
\quad \text{return } (m,t) \overset{?}{\in} \mathcal{T} \\ \hline
\end{array}
$$
Collision resistance (Definition 11.1):

$$
\def\arraystretch{1.5}
\begin{array}{|l|}\hline
\qquad \qquad \qquad \qquad\mathcal{L}_{\text{cr-real}}^\mathcal{H}\\\hline
s\leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^\lambda\\
\underline{\text{GETSALT():}}\\
\quad \text{return}\ s\\
\underline{\text{TEST}(x,x'\in\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^*):}\\
\quad \text{if $x \neq x'$ and $H(s, x) = H(s, x')$: return}\ \mathtt{true}\\
\quad \text{return}\ \mathtt{false}\\\hline
\end{array}\quad
\begin{array}{|l|}\hline
\qquad \qquad  \mathcal{L}_{\text{cr-fake}}^\mathcal{H}\\\hline
s\leftarrow \{\textcolor{brown}{0}, \textcolor{brown}{1}\}^\lambda\\
\underline{\text{GETSALT():}}\\
\quad \text{return}\ s\\
\underline{\text{TEST}(x,x'\in\{\textcolor{brown}{0}, \textcolor{brown}{1}\}^*):}\\
\quad \text{return}\ \mathtt{false}\\\hline
\end{array}
$$

Digital signatures (Definition 13.6):

$$\begin{array}{|l|}\hline
\qquad \qquad\mathcal{L}_{\text{sig-real}}^\Sigma\\\hline
(vk,sk)\leftarrow \Sigma.\text{KeyGen}\\
\\
\underline{\text{GETVK():}}\\
\quad \text{return $vk$ }\\
\\
\underline{\text{GETSIG}(m):}\\
\quad \text{return $\Sigma.$Sign }(sk,m)\\
\\
\underline{\text{VERSIG}(m,\sigma):}\\
\quad \text{return $\Sigma.$Ver }(vk,m,\sigma)\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \qquad\mathcal{L}_{\text{sig-fake}}^\Sigma\\\hline
(vk,sk)\leftarrow \Sigma.\text{KeyGen}\\
S:=\emptyset\\\\
\underline{\text{GETVK():}}\\
\quad \text{return $vk$ }\\\\
\underline{\text{GETSIG}(m):}\\
\quad\sigma:=\Sigma.\text{Sign}(sk,m)\\
\quad S:=S\cup\{(m,\sigma)\}\\
\quad \text{return $\sigma$}\\\\
\underline{\text{VERSIG}(m,\sigma):}\\
\quad \text{return $(m,\sigma)\stackrel{?}{\in}S$}\\\hline
\end{array}$$

Key agreement (Definition 14.4):

$$
\begin{array}{|l|}\hline
\qquad \qquad \ \ \mathcal{L}_{\text{ka-real}}^\Sigma\\\hline
\underline{\text{QUERY():}}\\
\quad (t,K)\leftarrow \text{EXECPROT}(\Sigma)\\
\quad \text{return}\ (t,K)\\\hline
\end{array}
\quad
\begin{array}{|l|}\hline
\qquad \qquad \ \ \mathcal{L}_{\text{ka-rand}}^\Sigma\\\hline
\underline{\text{QUERY():}}\\
\quad (t,k)\leftarrow \text{EXECPROT}(\Sigma)\\
\quad K'\leftarrow \Sigma.\mathcal{K}\\
\quad \text{return}\ (t,K')\\\hline
\end{array}
$$

Decisional Diffie-Hellman assumption (Definition 14.5):

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad\mathcal{L}_{\text{dh-real}}^\mathbb{G}\\\hline
\underline{\text{QUERY():}}\\
\quad a,b\leftarrow \mathbb{Z}_n\\
\quad \text{return} (g^a,g^b,g^{ab})\\\hline
\end{array}
\quad
\begin{array}{|l|} \hline
\qquad\mathcal{L}_{\text{dh-rand}}^\mathbb{G}\\\hline
\underline{\text{QUERY():}}\\
\quad a,b,c\leftarrow \mathbb{Z}_n\\
\quad \text{return} (g^a,g^b,g^{c})\\\hline
\end{array}
$$

CPA security for public-key encryption (Definition 15.1):

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

CPA$\Phi$ security for public-key encryption (Definition 15.2):

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

One-time secrecy for public-key encryption (Definition 15.4):

$$
\def\arraystretch{1.5}
\begin{array}{|l|} \hline
\qquad\qquad\quad\mathcal{L}^{\Sigma}_{\text{pk-ots-L}} \\ \hline
(pk, sk)  \leftarrow \Sigma.\text{KeyGen} \\
count := 0 \\
\underline{\text{GETPK}():} \\
\quad \text{return } pk \\
\underline{\text{CHALLENGE}(m_L, m_R \in \Sigma. \mathcal{M}):} \\
\quad count := count + 1 \\
\quad \text{if } count > 1: \text{return null}\\
\quad \text{return } \Sigma. \text{Enc} (pk, m_L) \\ \hline
\end{array}
\qquad
\begin{array}{|l|} \hline
\qquad\qquad\quad\mathcal{L}^{\Sigma}_{\text{pk-ots-R}} \\ \hline
(pk, sk)  \leftarrow \Sigma.\text{KeyGen} \\
count := 0 \\
\underline{\text{GETPK}():} \\
\quad \text{return } pk \\
\underline{\text{CHALLENGE}(m_L, m_R \in \Sigma. \mathcal{M}):} \\
\quad count := count + 1 \\
\quad \text{if } count > 1: \text{return null} \\
\quad \text{return } \Sigma. \text{Enc} (pk, m_R) \\ \hline
\end{array}
$$