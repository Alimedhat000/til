when dealing with long sequences it's not practical to pass the full sequence into one decimator, interpolator, or any other filter.

So we decompose the sequence into $k$ polyphase components process it and then convert it back as follows:
$$
\begin{align*}
X_k(z) &= \sum_{n=-\infty}^\infty x_k[n]z^{-n} = \sum_{n=-\infty}^\infty x[Mn+k]z^{-n} & 0 \leq k \leq M-1 \\
x_k[n] &= x[Mn+k] & 0 \leq k \leq M-1
\end{align*}
$$

where $x_k(n)$ and $X_k(z)$ are the polyphase components of $x[n]$ and $X[z]$.
![[Pasted image 20250120231143.png]]![[Pasted image 20250120231352.png]]

### Polyphase decomposition of filters
For FIR/Stable IIR filters, the polyphase decomposition of its transfer function is in the form 
${H(z) = \sum_{k=0}^{M-1} z^{-k} E_k(z^{-M})}$. Where $E_k(z)$ is the polyphase component, $M$ is the number of polyphase components , $0\leq k\leq M-1$

**Note**: $E_k(z^{-M})$ means that for every instance of z in $E_k(z)$ is replaced by $z^{-M}$ 
 
**Example:**  
Consider the FIR transfer function  
$$
H(z) = h[0] + h[1]z^{-1} + h[2]z^{-2} + h[3]z^{-3} + h[4]z^{-4} + h[5]z^{-5} + h[6]z^{-6} + h[7]z^{-7} + h[8]z^{-8}.
$$
Grouping the coefficients into even/odd

$$
H(z) = \left( h[0] + h[2]z^{-2} + h[4]z^{-4} + h[6]z^{-6} + h[8]z^{-8} \right) + z^{-1} \left( h[1] + h[3]z^{-2} + h[5]z^{-4} + h[7]z^{-6}\right).
$$
The two polyphase components 

$$
E_0(z) = h[0] + h[2]z^{-1} + h[4]z^{-2} + h[6]z^{-3} + h[8]z^{-4}

E_1(z) = h[1] + h[3]z^{-1} + h[5]z^{-2} + h[7]z^{-3}
$$
So the transfer function can be written as 
$$
\begin{align*}
H(z) = E_0(z^2) + z^{-1} E_1(z^2).
\end{align*}
$$
**Example**:
As shown by the colors and by following 
$$
X_k(z) = \sum_{n=-\infty}^\infty x_k[n]z^{-n} = \sum_{n=-\infty}^\infty x[Mn+k]z^{-n}, \quad 0 \leq k \leq M-1
$$
We get all the Polyphase components and to get $E(z)$ he will use this equation:
$$
E(z) = E_0(z^4) + E_1(z^4)z^{-1} + E_2(z^4)z^{-2} + E_3(z^4)z^{-3}
$$
![[Pasted image 20250120233942.png]]

### Decimator with polyphase implementation
The decimator works by filtering $x[n]$ and then keeps only the $M^{th}$sample by using the polyphase decomposition of the filter $H(z)$ and the first noble identity, one
can implement an efficient decimator.
![[Pasted image 20250120234930.png]]
**Noble Identity for Down-sampling Operation**:
- a down-sampler can be moved before the polyphase filter component. However, the polyphase filter component will be down-sampled by a factor of M
![[Pasted image 20250120234749.png]]
**Computational efficiency**:


|                                                 | Direct decimation                                                         | Polyphase implementation                                                                |
| ----------------------------------------------- | ------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Filter length                                   | length of $H(z)$ is $N$                                                   | length of $E_k$ is$N_k$ ,<br>$N = \sum_{k=0}^{M-1} N_k$<br>No samples are wasted        |
|                                                 | After processing all the samples only the multiplies of M stay.           | No samples are wasted as we down-sample before the filter                               |
| Computational<br>requirements per output sample | N multiplications and<br>(N − 1) additions                                | $N_k$ multiplications and <br>($N_k$ − 1) additions<br>per output sample per sub-filter |
| Idle Time                                       | The arithmetic units are idle during the rest of (N − 1) sampling periods | The arithmetic units are all operative at all instance of the output sampling period    |

### interpolator with polyphase implementation
The interpolator works by filtering $x[n]$ after increasing its sampling rate
by using the polyphase decomposition of the filter $H(z)$ and the __second noble identity__,
one can implement an efficient interpolator.
![[Pasted image 20250121000234.png]]
**Noble Identity for Up-sampling Operation**:
- an up-sampler can be moved after the polyphase filter component. However, the polyphase filter component will down-sampled by a factor of L
![[Pasted image 20250121000310.png]]

If there's a system like ![[Pasted image 20250121000810.png]]
An equivalent system is obtained by expressing $H(z)$ in polyphase using Express $H(z) = \sum_{k=0}^{L−1}z^{−k}E_k(z^L)$ and using the noble identity on the up-sampler or the down-sampler.
![[Pasted image 20250121001148.png]]
**IMPORTANT NOTE**:
	Up-sampler must be before the down-sampler for them to cancel each other
	If it was the other way around the down-sampler would introduce aliasing.
Links:[[Multi-rate Signal Processing]]