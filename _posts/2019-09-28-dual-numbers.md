---
layout: post
title:  "Dual Numbers"
date:   2019-09-28 16:39:41 -0400
categories: jekyll update
---

# What are dual numbers?

__From wikipedia:__

Dual numbers extend the real numbers by adding a new element $$\varepsilon$$ with the property 

$$\varepsilon^2 = 0 $$

Multiplication of dual numbers is given by:

$$(a + b\varepsilon)(c + d\varepsilon) = ac + (ad + bc)\varepsilon$$

Dual numbers form a two-dimensional commutative unital associate algebra over the real numbers

Every dual number has the form $$ z = a + b\varepsilon$$

## Definitions


### Algebra over a field -- set with operations
An algebra over a __field__ is a __vector space__ together with
a bilinear product. 

#### One way to define bilinear
* __Right distributivity__ - (x+y)*z = xz + yz
* __Left distributivity__ - x(y+z) = xy + xz
* __Mapping of scalars__ - (ax)(by) = (ab)(xy)

So an algebra is an __algebraic structure__, which consists
of a set, together with multiplication, addition, and scalar multiplication
by elements of the underlying field. It must also satisfy the axioms
implied by "vector space" and "bilinear". 

### Algebraic Structure -- the operations
An algebraic structure on a set A (carrier/underlying set)
is a collection of finitary operations on A. The 
combination of A with this structure is called an algebra.

### Vector Space
A vector space is a set V that is closed under vector addition
and scalar multiplication. 

In general, the scalars are members of a field F, in which case
V is called a vector space over F. 

### Field
A field is a set with __addition__, __subtraction__, __multiplication__, 
and __division__.

#### Axioms:
* Associativity of + and * 
* Commutativity of + and * 
* Identity for + and * 
* Inverse for + and * 
* Distributivity of * over + 



## Back to Dual Numbers
Dual numbers can also be represented as matrices

$$
\varepsilon = \begin{pmatrix}0 & 1\\\ 0 & 0\end{pmatrix} 
~~~~ and ~~~~
a + b\varepsilon = \begin{pmatrix}a & b \\ 0 & a \end{pmatrix} 
$$
