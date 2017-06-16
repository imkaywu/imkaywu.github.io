---
title: Least-Squares Fitting of Two 3-D Point Sets
categories:
  - Research
tags:
  - Paper
header:
  teaser: research.jpg
---

## Problem Definition

Two point set \\(\\{p_i\\}\\) and \\(\\{q_i\\}\\); i = 1, 2, ..., N are related by \\(q_i = Rp_i + t + n_i\\), where \\(R\\) is a rotation matrix, \\(t\\) a translation vector, and \\(n_i\\) a noise vector. We want to find the least-squares solution of \\(R\\) and \\(t\\) to minimize

\\[\Sigma^2 = \sum_{i=1}^N \|q_i-(Rp_i+t)\|^2\\]

## SVD

It was shown by Huang, Blostein and Margerum that: if the least-squares solution is \\(\hat{R}\\) and \\(\hat{t}\\), then \\(\{q_i\}\\) and \\(\{p_i'=\hat{R}p_i+\hat{t}\}\\) have the same centroid, i.e.

$$
m_q = m_{p'}
$$

where 

$$
m_q = \frac{1}{N} \sum_{i=1}^N q_i\\
m_{p'} = \frac{1}{N} \sum_{i=1}^N p_i' = \hat{R} * m_p + \hat{t}\\
m_p = \frac{1}{N} \sum_{i=1}^N p_i
$$

Let

$$
c_{p_i} = p_i - m_p\\
c_{q_i} = q_i - m_q
$$

We have

$$
\Sigma^2 = \sum_{i=1}^N \|c_{q_i}+m_q - (R(c_{p_i}+m_p)+t)\|^2\\
\Sigma^2 = \sum_{i=1}^N \|c_{q_i}-Rc_{p_i}+m_q-(Rm_p+t)\|
$$

Therefore, the original least-squares problem is splitted into two parts

* Find \\(\hat{R}\\) to minimize \\(\sum_{i=1}^N \|c_{q_i}-Rc_{p_i}\|^2\\)
* The translation is found by \\(\hat{t}=m_q-\hat{R}m_p\\)

### Algorithm

* From \\(\\{p_i\\}\\), \\(\\{q_i\\}\\) calculate \\(m_p\\), \\(m_q\\); and then \\(\\{c_{p_i}\\}\\), \\(\\{c_{q_i}\\}\\).
* Calculate the matrix
$$
H = \sum_{i=1}^N c_{p_i} c_{q_i}^T
$$
* Find the SVD of H: \\(H=U\Lambda V^T\\)
* Calculate \\(X = VU^T\\)
* Calculate the determinant of \\(X\\). If \\(det(X) = 1\\), then \\(\hat{R}=X\\), if \\(det(x)=-1\\), the algorithm fails

### Derivation

[TBD]

### Degeneracy

## Quarternion

