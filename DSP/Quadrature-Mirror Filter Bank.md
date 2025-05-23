It's a Maximally sampled filter bank with 2 stages and
$H_0$ and $G_0$ are lowpass filters.
$H_1$ and $G_1$ are highpass filters
![[Pasted image 20250121021239.png]]
### Analysis if the QMF Filter bank
$$
\begin{align*}
V_k(z) &= H_k(z)X(z) \\
U_k(z) &= \frac{1}{2}\left\{ V_k(z^{1/2}) + V_k(-z^{1/2}) \right\} \space \text{As $V_k$ is downsampled} \\
\hat{V_{k}}(z) &= U_k(z^2) \qquad\qquad 0 \leq k \leq 1 \\
\hat{V_{k}}(z) &= \frac{1}{2}\left\{ V_k(z) + V_k(-z) \right\} \\
&= \frac{1}{2}\left\{ H_k(z)X(z) + H_k(-z)X(-z) \right\} \qquad 0 \leq k \leq 1
\end{align*}
$$

Thus the reconstructed signal at output is:

$$
Y(z) = G_0(z) \hat{V}_0(z) + G_1(z) \hat{V}_1(z)
$$

$$
Y(z) = \frac{1}{2} \{ H_0(z)G_0(z) + H_1(z)G_1(z) \}X(z) + \frac{1}{2} \{ H_0(-z)G_0(z) + H_1(-z)G_1(z) \}X(-z)
$$

$$
Y(z) = T(z)X(z) + A(z)X(-z)
$$

The distortion transfer function:

$$
T(z) = \frac{1}{2} \{ H_0(z)G_0(z) + H_1(z)G_1(z) \}
$$

The aliasing component:

$$
A(z) = \frac{1}{2} \{ H_0(-z)G_0(z) + H_1(-z)G_1(z) \}
$$
We want to minimize the aliasing component and achieve the PR condition
Thus
$$
A(z) = 0
$$

$$
\begin{align*}
\Rightarrow & \quad H_0(-z)G_0(z) + H_1(-z)G_1(z) = 0 \\
\Rightarrow & \quad \frac{G_0(z)}{G_1(z)} = -\frac{H_1(-z)}{H_0(-z)}
\end{align*}
$$
for example:
$$
\begin{align*}
\Rightarrow & \quad G_1(z) = -H_0(-z) \quad \text{HPF} \\
\Rightarrow & \quad G_0(z) = H_1(-z)  \quad \text{LPF} \\ 
\Rightarrow & \quad A(z) = 0
\end{align*}
$$
perfect reconstruction condition
$$
\begin{align*}
\Rightarrow & \quad Y(z) = T(z)X(z) \\
\Rightarrow & \quad T(z) = \frac{1}{2} \{ H_0(z)G_0(z) + H_1(z)G_1(z)\} \\ 
\Rightarrow & \quad y[n] = x[n-\Delta]\quad \text{y is a delayed version of x}\\
\Rightarrow & \quad T(z) = z^\Delta \\
\Rightarrow & \quad H_0(z)G_0(z) + H_1(z)G_1(z) = 2z^\Delta \\

\end{align*}
$$

##### General Rules when solving QMF
$$\begin{align*}
	H_1(z) &= H_0(-z) \\
	G_0(z) &= H_1(-z) = H_0(z) \\ 	
	G_1(z) &= -H_0(-z) = -H_1(z) \\
	T(z) &= \frac{1}{2} \{ H_0(z)G_0(z) + H_1(z)G_1(z)\} \\ 
	A(z) &= \frac{1}{2} \{ H_0(-z)G_0(z) + H_1(-z)G_1(z) \}	
\end{align*}$$

### Polyphase Implementation of the Two-Channel QMF
![[Pasted image 20250121024721.png]]Thus the Transfer will be 
$$
\begin{align*}
	T(z) &= \frac{1}{2} \{ H_0(z)G_0(z) + H_1(z)G_1(z)\} \\
	T(z) &= 2z^{-1}E_0(z^2)E_1(z^2) \\  
\end{align*}
$$
And after using the noble identities
![[Pasted image 20250121025301.png]]
Now we notice that it turned into a DFT filterbank [[Filter Banks#^e46583]]