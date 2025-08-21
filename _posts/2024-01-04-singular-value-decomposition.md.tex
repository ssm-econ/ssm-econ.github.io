---
title: 'Singular Value Decomposition'
date: 2024-01-04
permalink: /posts/2024/01/svd/
excerpt: "Singular Value Decomposition and its link with the Pythagoras theorem"
tags:
  - linear algebra
  - principal component analysis
  - dimensionality reduction
---

# Singular Value Decomposition: An intuitive look

The Singular Vector Decomposition is perhaps the most widely applied techniques from linear algebra. But perhaps its name makes it look quite intimidating and unintuitive. In this post, I build the bridge from the well-known Pythagoras theorem all the way to de-mystifying SVD.

## Big picture 
From our linear algebra class we know that breaking a vector into perpendicular components makes life much easier. The Singular Vector Decomposition breaks a matrix into "perpendicular" rank-1 (read on to find out what this means) matrices. 

## Vectors: why Pythagoras shows up
Take an orthonormal basis $$\{\mathbf{e}_1,\dots,\mathbf{e}_n\}$$. Any vector $\mathbf{v}$ can be written as
$$
\mathbf{v}=\sum_{i=1}^n \langle \mathbf{v},\mathbf{e}_i\rangle \mathbf{e}_i,
$$
and the squared length splits cleanly:
$$
\|\mathbf{v}\|^2=\sum_{i=1}^n \big|\langle \mathbf{v},\mathbf{e}_i\rangle\big|^2.
$$
Those coordinates are “orthogonal directions”. This is equivalent to taking a vector and dropping projections along some orthogonal directions and adding them up. These represent the "shadows" of the vector. Any $n$ dimensional vector is defined by the shadow it casts in any $n$ orthogonal directions.

## Matrices as “machines”
A matrix $A$ takes inputs in $\mathbb{R}^n$ and spits out outputs in $\mathbb{R}^m$. SVD finds:
- orthonormal **input directions** $\mathbf{v}_1,\dots,\mathbf{v}_r$ in $\mathbb{R}^n$,
- orthonormal **output directions** $\mathbf{u}_1,\dots,\mathbf{u}_r$ in $\mathbb{R}^m$,
- **scaling factors** $\sigma_1\ge \cdots \ge \sigma_r>0$,

such that along each special input direction $\mathbf{v}_i$, the machine scales them (elongates or shrinks) by $\sigma_i$ and points along $\mathbf{u}_i$:
$$
A\mathbf{v}_i=\sigma_i\,\mathbf{u}_i.
$$

This yields the **rank-1 tile** view:
$$
A=\sum_{i=1}^r \sigma_i\,\mathbf{u}_i\mathbf{v}_i^T.
$$

Note that $\mathbf{u}_i\mathbf{v}_i^T$ is just a column-vector times a row-vector. It has only 1 linearly-independent column. For example $\begin{bmatrix} 1 & 4 & 2\end{bmatrix}^T\begin{bmatrix} 2 & 3 \end{bmatrix} =\begin{bmatrix} 2 & 3 \\ 8 & 12 \\ 4 & 6 \end{bmatrix}$

Each tile $\mathbf{u}_i\mathbf{v}_i^\top$ copies “how much of $\mathbf{v}_i$” is in the input and points it onto “the $\mathbf{u}_i$ direction”, stretching it by $\sigma_i$. 

## The right notion of “perpendicular” for matrices
For matrices, the natural inner product is the **Frobenius** inner product:
$$
\langle X,Y\rangle_F=\mathrm{tr}(X^\top Y)=\sum_{i,j}X_{ij}Y_{ij},
\qquad
\|X\|_F^2=\langle X,X\rangle_F.
$$
Under this inner product, the SVD tiles are mutually orthogonal:
$$
\big\langle \mathbf{u}_i\mathbf{v}_i^\top,\ \mathbf{u}_j\mathbf{v}_j^\top\big\rangle_F
=(\mathbf{u}_i^\top\mathbf{u}_j)(\mathbf{v}_i^\top\mathbf{v}_j)=\delta_{ij}.
$$
So SVD really is an **orthonormal expansion** of a matrix.

---

## Pythagoras for matrices (lengths add up)
Because those tiles are perpendicular, energy adds (for matrices, the analogue of length is called the *energy*. This comes from calculating the Frobenius norm):
$$
\|A\|_F^2
=\Big\|\sum_{i=1}^r \sigma_i\,\mathbf{u}_i\mathbf{v}_i^\top\Big\|_F^2
=\sum_{i=1}^r \sigma_i^2.
$$
**Interpretation**: if you think of $A$ as an image, $\|A\|_F^2$ is the total brightness squared; SVD splits that brightness into independent, non-overlapping beams of strength $\sigma_i^2$.

---

## Deconstruction
Keeping only the first \(k\) tiles gives the **best** rank-\(k\) approximation (Eckart–Young):
$$
A_k=\sum_{i=1}^k \sigma_i\,\mathbf{u}_i\mathbf{v}_i^\top,
\qquad
\|A-A_k\|_F^2=\sum_{i=k+1}^r \sigma_i^2.
$$
It’s the matrix analogue of “project onto the top $k$ principal directions.”

*Most* of the information present in a matrix may be captured by its first few principal components. Hence SVD can be used to ''reduce" the dimensionality of the matrix. In some applications, a seemingly high-dimensional matrix may be well-approximated by 2 or 3 **principal components**. This is the core idea behind Principal Component Analysis. 


##  Tiny numerical example
Let
$$
A=\begin{bmatrix}3&0\\[2pt]0&1\end{bmatrix}.
$$
Here $U=I,\ V=I,\ \sigma_1=3,\ \sigma_2=1$. The SVD tiles are
$$
3\,\mathbf{e}_1\mathbf{e}_1^\top=
\begin{bmatrix}3&0\\0&0\end{bmatrix},
\qquad
1\,\mathbf{e}_2\mathbf{e}_2^\top=
\begin{bmatrix}0&0\\0&1\end{bmatrix}.
$$
They’re perpendicular in the Frobenius sense, and
$$
\|A\|_F^2 = 3^2+1^2 = 10
\quad\text{equals}\quad
\sigma_1^2+\sigma_2^2=10.
$$
Truncating to \(k=1\) keeps only $$\begin{bmatrix}3&0\\0&0
\end{bmatrix}$$ and the error energy is $1^2=1$. This represents the magnitude of information we are losing out on when we make a rank-1 approximation.


## Takeaway
SVD is “Pythagoras for matrices.” It expresses $A$ as a sum of orthogonal, rank-1 patterns whose energies add up cleanly. That’s why SVD is the go-to tool for compression, denoising, and understanding what a matrix really does, providing a better description of the stretching and rotations that a matrix performs.
