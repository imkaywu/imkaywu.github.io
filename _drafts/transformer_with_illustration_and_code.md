---
title: Transformer with Illustration and Implementation
categories: 
  - Research
tags:
  - Computer Vision
---

This is a in-depth walk-through of Transformer, accompanied by clear illustrations and
straightforward implementations. This post is inspired by [The Annotated Transformer](http://nlp.seas.harvard.edu/annotated-transformer/) and [Transformers from Scratch](https://e2eml.school/transformers.html), and attempt to consolidate clear visual and code in one place for educational purpose.

## Overview
- [Matrix Multiplication](#matmul)
- [Tokenizer](#tokenizer)
- [Embedding](#embedding)
- [Self Attention](#self_attention)
- [Single-head attention](#single_head_attn)
- [Multi-head attention](#multi_head_attn)
- [Positional encoding](#pos_encoding)
- [Residual connection, FFN and LayerNorm](#)
- [De-embedding](#de_embedding)
- [EncoderLayer](#encoder_layer)
- [Encoder](#encoder)
- [Cross attention](#cross_attn)
- [DecoderLayer](#decoder_layer)
- [Decoder](#decoder)
- [A toy example](#toy_exp)
- [References](#references)

<a id="matmul"></a>
## Matrix Multiplication

<div class="img_row">
    <img class="col half" src="/assets/img/research/transformer/matmul0.png" alt="matrix multiply" title="Matrix Multiplication"/>
    <img class="col half" src="/assets/img/research/transformer/matmul0.png" alt="matrix multiply" title="Matrix Multiplication"/>
</div>
<div class="col three caption">
  Matrix Multiplication. When a matrix multiplies a column vector, the multiplication can be interpreted as weighted sum of the column vectors. Similarly when a row vector multiplies a matrix, it can be interpreted as weighted sum of the row vectors.
</div>

<a id="tokenizer"></a>
## Tokenizer

<a id="embedding"></a>
## Embedding
The $N$ inputs are first encoded into a matrix $A \in R^{NxN}$, where the $n$th
row corresponds to the $n$th word and is a $Nx1$ one-hot vector. The input
embedding are computed as $X=\Omega_e * A$, and $\Omega_e$ is learned like any
other network parameters. {from UDL}

<a id="intuition"></a>
## Intuition of Attention
Let's say there is a sentence with length $n$. The row vector represents the correlation/similarity/weight between the first word $x1$ and each word of the sentence. Each row of the $nxd$ matrix is the $d-$dimensional representation of each word. 

We would like to represent the word $x1$ as a weighted sum of all the words in
the sentence. As it turns out, it can be easily achieved by a row vector and
matrix multiplication. If we want to compute the representation for all $n$
words, we simply stack $n$ weight vectors vertically.

The network input is a series of high-dimensional embeddings representing words
or word fragments.

A word is not simply a word, it is the linear combination of all words that associated with it. For instance, in the following senstense, the word "it" has different meanings.

attention weights combine the values from different inputs.

<div class="img_row">
    <img class="col half" src="/assets/img/research/transformer/attention_intuition0.png" alt="intuition of attention" title="Intuition of Attention"/>
    <img class="col half" src="/assets/img/research/transformer/attention_intuition1.png" alt="intuition of attention" title="Intuition of Attention"/>
</div>
<div class="col three caption">
  Attention intuition: weighted sum.  
</div>

<a id="references"></a>
## References
