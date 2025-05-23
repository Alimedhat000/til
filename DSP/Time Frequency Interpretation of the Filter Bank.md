### Introduction

We segment the data $x[n]$ into blocks of length M then Weight Data using the window whose polyphase components are $P_i(z)$ after that we take DFT of the weighted data

$$\begin{align}
y_k[m] &= \sum^{M-1}_{n = 0}(x[n]w[n-m])e^{-j2\pi hn/M}
\end{align}
$$
Which is simply the date $x[n]$ multiplied by the weighted window $w[n-m]$ and then taking DFT by multiplying with the carrier![[Pasted image 20250121052751.png]]![[Pasted image 20250121052805.png]]

### Short-Time Fourier Transform (STFT)

Which is suitable for non-stationary signals (e.g., speech, audio, video, etc...)
It's also represented by 
$$\begin{align}
	y_k[m] &= \sum^{M-1}_{n = 0}(x[n]w[n-m])e^{-j2\pi hn/M}
\end{align}
$$
- Fixed Resolution
![[Pasted image 20250121053314.png]]

### Multilevel Filterbank “tree-structured filterbank”

If we had a [[Quadrature-Mirror Filter Bank]] structure but in every stage we had another QMF
this would give us this shape
![[Pasted image 20250121053512.png]]
If we use the noble identity on the outer(Level 1) down-samplers and up-samplers we'll get this:
![[Pasted image 20250121053745.png]]
Where equivalent analysis filters:
$$
\begin{cases} 
H_{00}(z) = H_0(z)H_0(z^2) \\ 
H_{01}(z) = H_0(z)H_1(z^2) \\ 
H_{10}(z) = H_1(z)H_0(z^2) \\ 
H_{11}(z) = H_1(z)H_1(z^2) 
\end{cases}
$$

And equivalent synthesis filters:

$$
\begin{cases} 
G_{00}(z) = G_0(z)G_0(z^2) \\ 
G_{01}(z) = G_0(z)G_1(z^2) \\ 
G_{10}(z) = G_1(z)G_0(z^2) \\ 
G_{11}(z) = G_1(z)G_1(z^2) 
\end{cases}$$
**Remember**: $H(z^2)$ is equivalent to up-sampling by two

![[Pasted image 20250121054212.png]]
Notice that in the above image after multiplying the filters we get **Fixed Resolution** 
i.e. fixed sized windows across the frequency domain

### Multilevel Filterbank “multiresolution”
If we did the same of the previous section but with only one stage split.
![[Pasted image 20250121054625.png]]
we'll get the equivalent system below:
![[Pasted image 20250121054727.png]]
Where equivalent analysis filters:
$$
\begin{cases} 
H_{00}(z) = H_0(z)H_0(z^2) \\ 
H_{01}(z) = H_0(z)H_1(z^2) \\ 
H_{1}(z) \\ 
\end{cases}
$$

And equivalent synthesis filters:

$$
\begin{cases} 
G_{00}(z) = G_0(z)G_0(z^2) \\ 
G_{01}(z) = G_0(z)G_1(z^2) \\ 
G_{1}(z)
\end{cases}$$
![[Pasted image 20250121054907.png]]
Now there we go notice how there's some bits with low resolution. Generally it will be the low frequencies as we want to catch voice, music, images, videos etc.. with a bit more resolution than higher frequencies

If we did that one more time we will get even more resolution in lower frequencies:
![[Pasted image 20250121055245.png]]

### Heisenberg uncertainty principal “Resolution”

Time-resolution and frequency-resolution are inversely proportional
$$
\sigma_t \sigma_\omega \leq \frac{1}{2}
$$
![[Pasted image 20250121055500.png]]
