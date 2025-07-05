**Elliptic curve cryptography** (ECC) is a public key cryptographic algorithm used to perform critical security functions, including **encryption**, **authentication**, and **digital signatures**. ECC is based on the **elliptic curve theory**, which generates keys through the properties of the elliptic curve equation, compared to the traditional method of factoring very large prime numbers (RSA).

## Elliptic Curve 

An elliptic curve, despite its name, **does not have the shape of an ellipse**. It is a two-dimensional curve defined by a specific cubic equation. It's a smooth, projective, algebraic curve with a **genus of one** and a specified point called **O**. When visualized, it appears as a looping line on a graph, intersecting two axes.

> In the context of algebraic curves, the genus is a non-negative integer that describes the "number of holes" in the curve. A curve with genus 0 is topologically equivalent to a sphere (or a plane), while a curve with genus 1 is topologically equivalent to a torus (a donut shape).

Usually Elliptic Curve is visualized like this but it could have multiple shapes. and what makes each curve different is the values of $a$ and $b$

![[Pasted image 20250701171957.png|center]]

$$
y^2 = x^3 +ax + b
$$

## How do we get keys from this curve?

Elliptic curves has a cool property where 
