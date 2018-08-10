---
title: "Playing Around with Word2Vec, FastText to Cluster Documents"
date: 2018-06-26
draft: true
layout: post
categories: python, datascience, word2vec, clustering
---
# Todo
 - Insert quick note about word2vec and how it works
 - Evaluate lda2vec, can you build your own version using fasttext vectors since 45% of the words in your text are outside the word2vec trained vocabulary?
 - If not, can you use the underlying idea in some way? (essentially using LDA to convert wordvectors into concept/sentence vectors)
 - gensim has a doc2vec which might do a quick and dirty clustering for me. 

This is mostly placeholder, since this project isn't complete yet, however - 
The rough outline of how this works so far
- Use Word2Vec/FastText (FastText might be more comprehensive) to convert words into 300 dimensional vectors, cluster the vectors to get similar groups of words
- For each of the documents, find the most important words and map these to ours clusters, create an ownership metric of some sort and calculate cosine distance
- Cluster hierarchical based upon these sentence vectors to map to our output 15 categories.