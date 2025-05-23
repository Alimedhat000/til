In most countries including the United States, two types of multiplexing schemes are used to transmit multiple low-frequency voice signals over a wide-band channel:
1. FDM
2. TDM
![[Pasted image 20250122192740.png]]
Below is the L-channel QMF
![[Pasted image 20250122193733.png]]
The transmultiplexer is a multi-input, multi-output, multirate structure as shown below
![[Pasted image 20250122193123.png]]
- It consists of an L-channel synthesis filter bank followed by an L-channel analysis filter bank and exactly the opposite of a L-channel QMF.

- Typically, 12 digitized speech signals are interpolated by a factor of 12, modulated by SSB modulation, digitally summed, and then converted into an FDM analog signal by D/A conversion.

- At the receiving end, the analog signal is converted into a digital signal by A/D conversion.

- The analog FDM signal is passed through a bank of 12 SSB demodulators whose outputs are then decimated, resulting in the low-frequency speech signals

- The speech signals have a bandwidth of 4 kHz and are sampled at 8-kHz rate.

- The FDM signal signal occupies the band 60 kHz to 108 kHz.

- The interpolation and the single-sideband modulation can be performed by up-sampling and appropriate filtering.

- Likewise, the single-sideband demodulation and decimation can be implemented by appropriate filtering and down-sampling.

- So to determine the input-output relation of the transmultiplexer, consider one typical path from the Kth input to the Lth output as shown below.
 ![[Pasted image 20250122194808.png]]
We can polyphase decompose the middle filter thus resulting in
![[Pasted image 20250122194944.png]]
![[Pasted image 20250122195008.png]]
Thus resulting in 
![[Pasted image 20250122195027.png]]where where $E_0(z)$ is the zeroth polyphase term of
$H(z)$, the equivalent representation of the $(k,l)th$ path is equivalent to the structure below:
![[Pasted image 20250122195141.png]]
Thus the input-output relation is given by 
$$
Y_k(z) = \sum^{L-1}_{l=0} F_{kl}(z)X_l(z), \quad 0\le k \le L-1
$$
We can rewrite the input-output relation as  
$$Y(z) = F(z)X(z)$$ 
where $F(z)$ is an $L \times L$ matrix whose $(k, l)th$ element is given by $F_{kl}(z)$:  

$$F(z) =
\begin{bmatrix}
    F_{00}(z) & F_{01}(z) & \cdots & F_{0,L-1}(z) \\
    F_{10}(z) & F_{11}(z) & \cdots & F_{1,L-1}(z) \\
    \vdots & \vdots & \ddots & \vdots \\
    F_{L-1,0}(z) & F_{L-1,1}(z) & \cdots & F_{L-1,L-1}(z)
\end{bmatrix}$$

- To ensure that $y_k$ is a reasonable replica of $x_k$:
- If $y_k[n]$ contains contributions from $x_r[n]$ with $r \ne k$ , then there is cross-talk between the $k-th$ and $r-th$ channels
- So cross-talk is totally absent if $F(z)$ is a diagonal matrix, in which case the above equation is reduced to
$$Y_k(z) = F_{kk}(z)X_k(z) \quad 0\le k\le L-1$$
It will be a perfect reconstruction system if 
$$F_{kk}(z) = \alpha z^{-nk} \quad 0\le k\le L-1$$
where $\alpha_k$ is a nonzero constant

Thus for a Perfect reconstruction system 
$$y_k[n] = \alpha_kx_k[n-n_k]$$