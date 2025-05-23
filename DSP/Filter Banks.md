After what was discussed before in [[Polyphase]] and [[Multi-rate Signal Processing]].
we can visualize what filter banks means.
### What Are Filter Banks?

A **filter bank** is a collection of filters that are used to decompose a signal into multiple sub-bands or to reconstruct a signal from its sub-bands. Each filter in the bank covers a specific portion of the frequency spectrum, and together, they cover the entire bandwidth of the original signal.

its widely used in: 
- Wideband wireless communication transmission.
- Signal compression.
- Spectrum analyzer.
![[Pasted image 20250121002648.png]]

**Types of Filter Banks**:
1. Analysis: Decomposes the input signal into sub-bands.
2. Synthesis: Reconstructs the signal from the sub-bands.
3. Uniform: All sub-bands have the same bandwidth. 
4. non-Uniform: Sub-bands have different bandwidths.
![[Pasted image 20250121002951.png]]

### Maximally decimated Filter banks
Each sub-band is down-sampled by $M$ due to bandwidth reduction in the sub-bands.
**Types of Decimated Filter Banks**:
- Under-sampled filter bank: $N < M$ “under determined system”
- Critically sampled filter bank: $N = M$
- Over-sampled filter bank: $N > M$ “over determined system”
Where $N$ is number of sub-bands and $M$ is the down-sample number. 
![[Pasted image 20250121003538.png]]
### Uniform Modulated Filter Banks

^e46583

Each sub-band filter is a modulated version of the prototype filter H(z) shifted to it's proper place.
![[Pasted image 20250121005901.png]]
The uniform filter banks can be implemented efficiently using the Polyphase/DFT combinations as follows:
1. If we use the down-sampling noble identity on the analysis part 
2. From the image above we see that the carrier is $e^{j2\pi nk/M}$ which is present in IDFT, so we can represent it by taking M-point IDFT.
2. if we repeat the same steps for the synthesis part we get the following image 

![[Pasted image 20250121010128.png]]
**Example**:
What is the prototype filter here?
- Its a delta as there's no $P_i(z)$ or $\overline{P}_i(z)$ 
![[Pasted image 20250121011035.png]]
![[Pasted image 20250121011525.png]]
#### Nyquist Filter ($L^{th}$-band filter)
$$
	h[n] = \frac{\sin{\frac{\pi \space n}{L}}}{(\pi \space n)}
$$
From the following example we can see that at L = 4 
1. **In Time Domain** 
	- **every 4th sample in the impulse response is zero** (except for the center tap).
	- $h[Ln]=0\enspace for\enspace n=0$
	- The center tap (at $n=0$) has a non-zero value, typically normalized to $1/L$.
2. **In Frequency Domain**
	- The fall-off of the filter is at 1/L
![[Pasted image 20250121012218.png]]

### Perfect Reconstruction M - Band Filter Bank (PR)

The **DFT filter bank** has the disadvantage that the synthesis filters have a much
higher order than the analysis filters, in case of perfect reconstruction.

In general, for perfect reconstruction (PR) filter bank, the filters $H_k(z)$ are NOT a
modulated version of $H_0(z)$

![[Pasted image 20250121013342.png]]
As seen in the above image we first get the polyphase representation of the analysis and synthesis filter banks $H_k(z)$ and, $H_k(z)$ where 
$$
\begin{align*}
\left[\begin{array}{c}
H_0(z) \\
H_1(z) \\
\vdots \\
H_{M-1}(z)
\end{array}\right]
&=
\mathbf{E}(z^M)
\left[\begin{array}{c}
1 \\
z^{-1} \\
\vdots \\
z^{-(M-1)}
\end{array}\right]
\end{align*}
$$

^059d76

$$
[G_0(z) \quad G_1(z) \quad \dots \quad G_{M-1}(z)] = [z^{-(M-1)} \quad z^{-(M-2)} \quad \dots \quad 1] R(z^M)
$$
After That we apply the noble identities:
![[Pasted image 20250121013748.png]]
**Note**:
It should be noted that we can $H_k$ and $G_k$ back from $E_k$ and $R_k$
where $E_k$ is multiplied by an increasing delay and $R_k$ by a decreasing one: ^3f4e04
$$
\begin{align*}
H_k(z) = \sum_{j=0}^{M-1} z^{-j} E_{kj}(z^{M}) \\
G_k(z) = \sum_{j=0}^{M-1} z^{-(M-1-j)} R_{jk}(z^{M}),
\end{align*}
$$

Here we can see that every Row $i$ in $E(z)$ is the polyphase representation of $i^{th}$ filter
and every Column $j$ in $R(z)$ is  is the polyphase representation of $j^{th}$ filter

![[Pasted image 20250121014017.png]]

**Example**:
He gets the polyphase components of $H_0$ and $H_1$ and puts them in the matrix representation as shown before: 
![[Pasted image 20250121014755.png]]

#### Perfect Construction Condition:
- The perfect reconstruction (PR) condition is

$$
\begin{align*}
E(z)R(z) = cI \\ \\
R(z) = cE^{-1}(z)
\end{align*}
$$
- where $(c)$ is a constant.
- In general, the PR filter bank is equivalent to a pure delay $\Updelta$.
$$
\begin{align*}
\text{filterbank delay} = (M - 1) + \Delta \\
\\
E(z^M)R(z^M) = cz^{-\Delta}I
\end{align*}
$$
**Example**:
Determine the analysis $H_i(z)$ and synthesis filters $G_i(z)$ given $E(z^3)$?
Using the equation [[Filter Banks#^059d76]] we can Get $H_i$, then by using the PR condition we can get R(z) and from it we get $G_i$.
![[Pasted image 20250121020108.png]]
Links:
[[Polyphase]][[Multi-rate Signal Processing]]