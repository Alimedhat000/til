As discussed before LTI DT systems is characterized by LCCDE or by Rational System Function 
where the zeros and poles of this system depends on the choice of $b_k$ and $a_k$. and determine the frequency response characteristics.

Linear constant-coefficient difference equation **LLCDE** :
$$
y(n) = - \sum^{N}_{k = 1}a_ky(n-k) + \sum^{M}_{k = 0}b_kx(n-k)
$$

^805b8b

 Rational system function :
$$
y(n) = \frac{\sum^{M}_{k = 0}b_kz^{-k}}{1 + \sum^{N}_{k = 1}a_kz^{-k}}
$$

The major factors that influence our choice of a specific realization are:
- Computational complexity: Number of arithmetic, fetch, and comparison operations.
- Memory requirements: Number of memory locations.
- Finite word length effects: (finite-precision effects) The quantization effects.

### Structure for FIR Systems
In general an FIR system is described by:
$$
y(n) = \sum^{M-1}_{K=0}b_kx(n-k)
$$

^64793a

Or
$$
y(n) = \sum^{M-1}_{K=0}b_kz^{-k}
$$
The Unit sample response is  identical to the coefficients $b_k$

$$
h(n) = \begin{cases} 
b_n, & 0 \leq n \leq M-1 \\
0, & \text{otherwise}
\end{cases}
$$

There are several methods to implement an FIR system:
1. The simplest structure, called the **direct form**. 
2. The **cascade** form realization. 
3. The **frequency-sampling** realization. 
4. The **lattice** realization.

#### Direct Form Structure
The direct form realization follows immediately from the non-recursive difference equation given by  $$y(n) = \sum^{M-1}_{K=0}b_kx(n-k)$$
or by the convolution summation $$y(n) = \sum_{k=0}^{M-1} h(k)x(n-k)$$![[Pasted image 20250122070348.png]]
This structure requires $M - 1$ memory locations and it has a complexity of $M$ multiplications and $M - 1$ additions per output point.

Consequently, the direct-form realization is often called a **transversal** or **tapped-delay-line** filter.

When the FIR system has linear phase, the unit sample response of the system satisfies either symmetry of asymmetry condition $\Rrightarrow h(n) =\pm h(M-1-n)$

For such system the number of multiplications is reduced to $M/2$ for even $M$ and $(M-1)/2$ for odd $M$
![[Pasted image 20250122070851.png]]

#### Cascaded Form Structure
The cascaded realization follows naturally from the system function given by$$H(z) = \sum^{M-1}_{K=0}b_kz^{-k}$$
It's a simple matter to factor $H(z)$ into $2^{\text{nd}}$-order FIR systems so that
$$
H(z) = \prod_{k=1}^K H_k(z)
$$
where 
$$
H_k(z) = b_{k0} + b_{k1}z^{-1} + b_{k2}z^{-2}, \quad k = 1, 2, ..., K
$$
and $K$ is the integer part of $(M+1)/2$

The filter parameter $𝒃_0$ may be equally distributed among the 𝑲 filter sections, such that $𝒃_0=𝒃_{10}𝒃_{20}…𝒃_{K0}$ or it may be assigned to a single filter section. 

It's always desirable to form pairs of complex-conjugate roots so that the coefficients $b_{ki}$ are real valued. Real-valued roots can be paired in any arbitrary manner.
![[Pasted image 20250122071843.png]]

In the case of linear-phase FIR filters, the symmetry in $h(n)$ implies that the zeros of $H(z)$ also exhibit a form of symmetry.

In particular, if $𝒛_k$ and $𝒛_𝒌^∗$ are a pair of complex-conjugate zeros then $𝟏/𝒛_𝒌$ and $𝟏/𝒛_𝒌^∗$ are also a pair of complex-conjugate zeros

❑ Consequently, we gain some simplification by forming fourth-order sections of the FIR system as follows:

$$\begin{align}


H_k(z) &= c_{k0}(1 - z_kz^{-1})(1 - z_k^*z^{-1})(1 - z^{-1}/z_k)(1 - z^{-1}/z_k^*) \\
&= c_{k0} + c_{k1}z^{-1} + c_{k2}z^{-2} + c_{k1}z^{-3} + c_{k0}z^{-4}
\end{align}
$$

where the coefficients ($c_{k1}$) and ($c_{k2}$) are functions of $z_k$.
![[Pasted image 20250122072450.png]]
Thus reducing the number of multiplications by half.

#### Frequency Sampling Structures
an alternative structure for FIR filter where the parameters that characterize such a filter are the values of the desired frequency response instead of the impulse response $h(n)$ 

Such structure is obtained by specifying the desired frequency response $H(\omega) = \sum^{M-1}_{n = 0}h(n)e^{-j\omega n}$
where $\omega$ is obtained from
$$
\begin{align}
\omega_k = \frac{2\pi}{M}(k+\alpha), \quad k=& 0,1,..., \frac{M-1}{2}\quad M\space odd\\
\quad k=& 0,1,..., \frac{M}{2}-1\quad M\space even\\
\quad \alpha=& 0\space or \space \frac{1}{2} 
\end{align}
$$
Thus 
$$
\begin{align}


H(k + \alpha) &= H\left( \frac{2\pi}{M}(k + \alpha) \right) \\

&= \sum_{n=0}^{M-1} h(n)e^{-j2\pi (k+\alpha)n/M}, \quad k = 0, 1, ..., M - 1
\end{align}$$

The set of values $H(k+\alpha)$ are called the frequency samples of $H(\omega)$
In the case where $\alpha = 0$ , $H(k)$ corresponds to the M-point DFT of $h(n)$.

By inverting the previous equation and expressing h(n) in terms of the frequency samples we get:
$$

\begin{align*}
h(n) = \frac{1}{M}\sum_{k=0}^{M-1}H(k+\alpha)e^{j2\pi (k+\alpha)n/M}, \qquad n=0,1,...,M-1
\end{align*}
$$

when $\alpha = 0$ , the previous equation is the IDFT of $H(k)$.
Using the previous equation we can get the $H(z)$
$$
\begin{align*}
H(z) = \sum_{n=0}^{M-1} h(n) z^{-n} = \sum_{n=0}^{M-1} \left[ \frac{1}{M} \sum_{k=0}^{M-1} H(k+\alpha) e^{j2\pi (k+\alpha)n/M} \right] z^{-n}
\end{align*}
$$
after some MATHEMATICS we get
$$
\begin{align*}
H(z) &= \sum_{k=0}^{M-1} H(k+\alpha)\left[\frac{1}{M}\sum_{n=0}^{M-1}(e^{j2\pi (k+\alpha)} M z^{-1})^{-n}\right]\\
&=\frac{1-z^{-M} e^{j2\pi \alpha}}{M}\sum_{k=0}^{M-1}\frac{H(k+\alpha)}{1-e^{j2\pi (k+\alpha)/M}z^{-1}}
\end{align*}
$$Thus the system function $H(z)$ is characterized by the set of frequency samples $H(k+a)$
instead of $h(n)$
This FIR filter can be viewed as a cascade of two filters.
$H_1(z) = 1/M(1-z^{-M}e^{j2\pi a})$  an all zero filter or a comp filter and Its zeros are located at equally spaced points on the unit circle at
$𝒛_𝒌=𝒆^{𝒋𝟐𝝅(𝒌+𝜶)/𝑴},  𝒌=𝟎,𝟏,…,𝑴−𝟏$

The second filter is the summation $\sum_{k=0}^{M-1}\frac{H(k+\alpha)}{1-e^{j2\pi (k+\alpha)/M}z^{-1}}$ Consists of a parallel bank of single-pole filters with resonant frequencies at 
$𝒑_𝒌=𝒆{𝒋𝟐𝝅(𝒌+𝜶)/𝑴},  𝒌=𝟎,𝟏,…,𝑴−𝟏$

The pole locations are identical to the zero locations and that both occur at 𝝎𝒌=𝒆𝒋𝟐𝝅(𝒌 𝜶)/𝑴, which are the frequencies at which the desired frequency response is specified

The gains of the parallel bank of resonant filters are simply the complex-valued parameters {𝑯𝒌+𝜶}.

![[Pasted image 20250122171830.png]]