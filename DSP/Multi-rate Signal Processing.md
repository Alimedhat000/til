---
~
---
The basic multi-rate operations are up-sampling and down-sampling
- __interpolation__ is the process of __increasing__ the sampling rate by an integer factor __L__.
up-sampler is used in interpolation.
- __decimation__ is the process of __decreasing__ the sampling rate by an integer factor __M__.
down-sampler is used in decimation.
### Up-sampling
Up-sampling by a factor of __L__ is performed by inserting __L − 1__ zeros between
successive samples like this:
$$
x_u[n] = 
\begin{cases} 
x\left[\frac{n}{L}\right] & \text{if } n = 0, \pm L, \pm 2L, \ldots \\
0 & \text{otherwise}
\end{cases}
$$
- Matrix Representation:
$$
(\uparrow 2) \mathbf{x} = \mathbf{U} \mathbf{x} = 
\begin{bmatrix}
1 & 0 \\
0 & 0 \\
0 & 1 \\
0 & 0
\end{bmatrix}
\begin{bmatrix}
x(0) \\
x(1)
\end{bmatrix}
=
\begin{bmatrix}
x(0) \\
0 \\
x(1) \\
0
\end{bmatrix}
$$
In the frequency Domain up-sampling by __L__ introduces __L − 1__ Images in the spectrum and 
$\omega$ is compressed by __L__
- example :
	up sampling by a factor of __(L = 5)__ induces 4 images in the spectrum.
	it should be noted that only 2 is shown because the drawing is only up to _$\pi$ 
	
![[Pasted image 20250120214149.png]]

### Interpolation 
In time domain, the zero-valued samples introduced by the up-sampler must be
interpolated for an effective sampling rate increase.

In frequency domain, the $L − 1$ unwanted images in the spectra of the up-sampled
$x_u[n]$ introduced by the up-sampler must be removed.

this is performed by using the interpolation filter $H(z)$ with $\omega_c = \pi/L$ with gain $L$
$$
H(e^{j\omega}) = 
\begin{cases} 
L & |\omega_c| \leq \pi / L \\ 
0 & \text{otherwise} 
\end{cases}
$$
Interpolation is performed in the frequency domain by removing the $L − 1$ images of the spectrum using a lowpass filter

![[Pasted image 20250120214716.png]]
- example :
	using the same above example 
	we can see that: 
	- All Zero samples have been filled in __time domain__.
	- Images have been removed in __frequency domain__.
	- The Amplitude in __frequency domain__ is multiplied by a factor of $(L = 5)$

![[Pasted image 20250120215225.png]]
	
#### How to solve Up-sampling and Interpolation
1. Draw unit circle and put your array starting from 0 and going clockwise.(Original Signal). 
2. after Up-sampling put __L__ images of the array going clockwise. (Up-sampled Signal).in time domain insert L-1 zeros after each sample. 
4. To represent the signal after filtering we start from 0 to $\pi$ going clockwise where every element amplitude is 2x except the last one then do the same but going anti-clockwise and fill the remaining with zeros. (interpolated Signal) in time domain just fill the zeros.
![[Pasted image 20250120215714.png]]![[Pasted image 20250120220058.png]]

- example:
![[Pasted image 20250120220409.png]]
### Down-Sampling
Down-sampling by a factor of M is performed by keeping every $M^{th}$ sample
 - Matrix representation:
$$
\left( 1 \ 2 \right) x = Dx =
\begin{bmatrix}
1 & 0 & 0 & 0 \\
0 & 0 & 1 & 0
\end{bmatrix}
\begin{bmatrix}
x(0) \\
x(1) \\
x(2) \\
x(3)
\end{bmatrix}
=
\begin{bmatrix}
x(0) \\
x(2)
\end{bmatrix}
$$
As shown above the Up-sampler is a __right inverse__ of the Down-sampler 
- $D = U^T$
- $DU = I$

In the frequency domain the spectrum is stretched by M, and (M-1) __aliased__ versions at multiples of $2\pi$ are added.
![[Pasted image 20250120221608.png]]

### Decimation
In time domain, the bandwidth of the critically sampled signal must be reduced by a
low-pass filtering __before__ its sampling rate is reduced by a down-sampler

In frequency domain, this low-pass filtering will prevent aliasing from happening
after down-sampling the signal

The low-pass filter $H(z)$ is called the decimation filter with $\omega_c = \pi / M$ with a  gain of 1
$$
\left| H(e^{j\omega}) \right| = 
\begin{cases} 
1 & |\omega_c| \leq \pi / M \\ 
0 & \text{otherwise}
\end{cases}
$$

- example:
	we can see that:
	- Signal rate is indeed lowered without any aliasing. 
	- The Amplitude in __frequency domain__ is divided by a factor of $(M = 2)$  
 ![[Pasted image 20250120222237.png]]
### Changing the Sampling rate by a fraction factor $(L/M)$
A fractional change of the sampling rate by a rational factor can be achieved by cascading a factor of $L$ interpolator with a factor of $M$ decimator,
where both $L$ and $M$ are positive integers.
![[Pasted image 20250120222900.png]]
- Filter Specification:$$
H(e^{j\omega}) = 
\begin{cases} 
L & |\omega_c| \leq \min\left(\frac{\pi}{L}, \frac{\pi}{M}\right) \\ 
0 & \text{otherwise}
\end{cases}
$$
- example : change rate of $x[n]$ by a factor of $5/3$ :
	-  $L = 5$, $M = 3$
	- Input length = 60 samples
	- Output length = 100 samples
	
![[Pasted image 20250120223118.png]]

### Multi-rate Identity
Down-sampling and up-sampling are interchangeable only if $M$ and $L$ are __coprime__.
- Coprime is numbers that have no common divisor other than 1.

- example :
![[Pasted image 20250120224028.png]]![[Pasted image 20250120224113.png]]