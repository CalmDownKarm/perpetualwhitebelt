---
layout: page
title: NLP
permalink: /nlp/
comments: true
---

# This page is primarily intended to document my notes about NLP, and be an aggregator of links that I'd like to remember.

## My Blog Posts
- [Word2Vec](./2019/word2vec)
- [LDA](./)
- [Hindi Dependency Parsing](./)
- [CNNs for NLP](/2018/yoon-kim)

## Useful External Links
- [indicnlp](http://indicnlp.org/)
- [CS224n Deep Learning for NLP by Christopher Manning](http://cs224n.stanford.edu/) - Currently going through this, really amazing course, enjoying learning some of the math going on behind the scene 
- [Distill's post on Memorization in RNNs and LSTMs](https://distill.pub/2019/memorization-in-rnns/), their post on [Attention](https://distill.pub/2016/augmented-rnns/) - Distill is indubitably the gold standard in ml explanations today. 

## External Blog Posts and Seminal Papers that I've read and found useful. 
 - [WildML, on Yoon Kim's Paper on CNNs for NLP](http://www.wildml.com/2015/11/understanding-convolutional-neural-networks-for-nlp/)
    - Similar to the convolutions in signal processing.
    - CNNS are layers of convolutions with some sort of activation function determining output between layers. Thus 'regions of input' are connected to output neurons as opposed to individual neurons being connected to output neurons.
    - affine layers are fully connected layers (typical in feed forward networks)
    - sentences/words are converted to vectors that contain word embeddings - this is what word2vec and GLOVE do. You could also potentially have a 1 hot encoded vector that indexes a word in a dictionary of some sort.
    - Thus a 10 word sentence using 100 dimensional vectors forms an 10X100 Matrix as the input.
    - Typical filters in CNNS for NLP will have row sized windows so entire words are examined at a time.
    - Sliding windows over 2-5 words at a time is typical.
    - Stride Size- amount of overlap between subsequent sliding windows, literature typically uses 1. Larger stride sizes will create heirarchial/tree like structures.
    - Wide/Narrow Convolution - whether zero padding is used for the edge inputs of the matrix.
    - Pooling layers - example 1 max pooling - the output of the filter can be represented by some subsample of the filter output. 1 max pooling reduces it to the highest single value in the filter. Pooling provides a fixed size output layer and reduces dimensionality. In images helps with invariance due to pixel shifts.  
    - Input channels for images this is usually RGB/opacity. In text, it's often n-gram layers so 3,5,7 etc is pretty normal.
