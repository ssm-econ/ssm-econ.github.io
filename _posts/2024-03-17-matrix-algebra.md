---
title: 'Matrix Algebra: 4 perspectives'
date: 2024-03-17
permalink: /posts/2024/10/matrixalgebra/
excerpt: "A quick intro to linear algebra concepts"
tags:
  - linear algebra
  - geometry
---


1. *Linear algebra is just mixing*

Say $x$ is a n-dimensional vector and $A$ is a m x n matrix
The operation of multiplying the matrix by $x \rightarrow Ax$ is just mixing the columns of A (each of length m) in the proportion suggested by the elements in $x$ yielding $x_1C_1+x_2C_2+\dots x_n C_n$ where $C_1,C_2, \dots ,C_n$ are columns of $A$.

The result is a linear combination of the columns of $A$ (and hence of the same dimensions as the column itself)

If instead, we multiply $A$ by a n x k matrix $B$, just think of B as $k$ column vectors of length $n$ stacked side by side.

We apply the same intuition to see that each of these columns will get mapped to a new column vector and we get a m-by-k matrix.
2. *Understand matrices in terms of their "effects"*

Say
 $$ A =
\begin{bmatrix} 
1 & 0\\
1 & 1\\
\end{bmatrix}
$$

You are supposed to find $A^{81}$. Applying the matrix multiplication rule is an obvious, albeit not fun way of computing this. What else can we do instead? Notice how the matrix $A$ operates on a vector $v=\begin{bmatrix} x\\ y \\ \end{bmatrix}$.

$$Av=\begin{bmatrix} x\\ x+y \\ \end{bmatrix}$$

So applying this operation amounts to keeping the first element the same and changing the second element to the sum of the two.

Applying this operation twice would result in:

$$A(Av)=A^2v=\begin{bmatrix}x\\2x+y\end{bmatrix}$$

Easy to see now that $$A^n v=\begin{bmatrix} x\\nx+y\end{bmatrix}$$

And the matrix corresponding to the operator $A^n$ should then be

$$A^n=\begin{bmatrix} 1 & 0\\n & 1 \end{bmatrix}$$

If we apply the same logic to work out what the inverse would be: The inverse should, when applied to the resultant of operator A applied on $v$ give back $v$. That's how we define the inverse of an operator!

$$A: \begin{bmatrix} x\\y \end{bmatrix} \rightarrow \begin{bmatrix} x\\x+y \end{bmatrix}$$

$$A^{-1}(Av)=v$$
$$A^{-1}\begin{bmatrix} x\\x+y \end{bmatrix} = \begin{bmatrix} x\\y \end{bmatrix}$$

The matrix $A^{-1}$ should leave the first element as it is, the second element should transform to the difference between the second element of the input and the first element (we are noting here that  $y = (x+y) - x$.

Clearly, the matrix corresponding to this operator is 
$$\begin{bmatrix} 1 & 0\\-1 & 1 \end{bmatrix} $$

We calculated the inverse without doing any of the boring algebra. And we gained intuition about what these matrices actually do. 

Consider the matrix 
$$L=\begin{bmatrix} 0 & 1 & 0\\ 0 & 0 & 1 \\0 & 0 & 0\end{bmatrix} $$ and the vector $v=\begin{bmatrix} x\\y\\z \end{bmatrix} $

$$Lv=\begin{bmatrix} y\\z\\0 \end{bmatrix} $$

So, the matrix L has the effect of replacing the first element with the second, the second with the third and replacing the third element with a 0.

Clearly, applying this operator twice, we get
$$L^2v=\begin{bmatrix} z\\0\\0 \end{bmatrix}$$ 
$$L^3v=\begin{bmatrix} 0\\0\\0 \end{bmatrix}$$ 

We can see that the vector just "shifts" upwards after being operated on by L one step, while replacing the last element with 0. 

You might notice that when the matrix $L$ operates on a vector, it "discards" information. For example, no matter what the value of $x$ is, $Lv$ will be the same regardless, as long as $y$ and $z$ remain the same. But this means that the matrix must not be invertible! 

Why? Well, we could say that multiple vectors get mapped to the same vector by the matrix. Hence the map is not **injective** as some mathematicians might say. In mapping vectors, the matrix strips away some information embedded in the original one which makes it impossible to tell exactly how we got to the output. This is precisely what invertibility means. 

The matrix $L$ we just encountered is sometimes referred to as the Lag operator. This is common in time-series analysis since one might imagine v to contain temporally ordered observations $$v_t=\begin{bmatrix} x_t\\x_{t-1} \\x_{t-2} \end{bmatrix}$$ 

$$Lv_t=\begin{bmatrix} x_{t-1}\\ x_{t-2} \\ 0 \end{bmatrix}$$ 

Hence, applying $L$ gives us a vector with lagged observations. One can obviously not construct a linear operator that recovers the original vector after being operated on by $L$, since all information about $x_t$ is just gone and cannot be recovered.


3. *Matrices just rotate and stretch*

A linear mapping is defined as one that maps a line in the domain space into another line in (possibly a different dimensional) range.

A diagonal matrix acts on a vector simply by scaling individual components

$$\begin{bmatrix}
   d_{1} &  &  &\\
   & d_{2} &  & \\
   &  &  \ddots & \\
   &  &   & d_{n}
 \end{bmatrix} 
 \begin{bmatrix} x_1\\ x_2 \\ \vdots \\ x_n \end{bmatrix} = 
\begin{bmatrix} d_1x_1\\d_2x_2 \\\\vdots \\ d_nx_n \end{bmatrix}$$

Individual dimensions get stretched/shurnk, but there is no mixing of rows.

Orthogonal matrices rotate a vector through an angle.

$$OO^T=O^TO=I$$

$$(Ox)^T(Oy)=x^TO^TOy=x^Ty$$

Multiplying with $O$ preserves the inner product (and hence angles between vectors) and lengths (no scaling).

$$
O(\theta)=
\begin{bmatrix}
\cos\theta & -\sin\theta \\[6pt]
\sin\theta & \cos\theta
\end{bmatrix}
$$

$$O(\theta) \begin{bmatrix} x_1 \\ x_2 \end{bmatrix}=
\begin{bmatrix}
x_1 cos(\theta) - x_2 sin(\theta) \\
x_1 sin(\theta) + x_2 cos(\theta) \\
\end{bmatrix}$$

Multiplying with sequence of Orthogonal and Diagonal matrices would amount to stretching and rotating matrices.

4. *Matrix Algebra generalizes all linear operators*

Consider the vector space (fancy way of saying the set) of all polynomials of degree $\le 5$.

This space is "spanned" by the set $$ \{1,x,x^2,x^3,x^4,x^5\} $$

Indeed, any polynomial in the set can be represented as 
$$P(x)=a_0+a_1 x+a_2 x^2 +a_3 x^3 + a_4 x^4 +a_5 x^5$$
 All we need to specify is a vector  $a=\begin{bmatrix} a_0 \\ a_1 \\ \vdots \\ a_5 \end{bmatrix}$ and the polynomial is uniquely identified. 
Since polynomials are represented by vectors, matrix algebra can be used to describe linear operators on these polynomials

**Differentiation is a linear operator**

$$DP(x)=a_1 + 2a_2 x + 3a_3 x^2 + 4 a_4 x^3 +5 a_5 x^4$$

The operator $D$ maps the vector $\begin{bmatrix} a_0 \\
a_1 \\
a_2 \\
a_3 \\
a_4 \\
a_5 \end{bmatrix}$ to $\begin{bmatrix} a_1 \\
2a_2 \\
3a_3 \\
4a_4 \\
5a_5 \\ \end{bmatrix}$
 
This operation can be represented by the matrix
$a=\begin{bmatrix} 0 & 1 & 0 & 0 & 0 & 0 \\
0 & 0 & 2 & 0 & 0 & 0 \\
0 & 0 & 0 & 3 & 0 & 0 \\
0 & 0 & 0 & 0 & 4 & 0 \\
0 & 0 & 0 & 0 & 0 & 5 \\ \end{bmatrix}$

