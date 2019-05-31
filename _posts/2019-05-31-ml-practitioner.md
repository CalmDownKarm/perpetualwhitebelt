---
title: Machine Learning Is Not Math.
date: 2019-05-31
draft: true
layout: post
categories: tech, data science, machine learning
---

Machine Learning is firmly grounded in math - but why does it never feel like that day to day?. When I sit down to solve a math problem, not only is there a definitive answer, but more often than not there's an ideal way to approach the question. This ideal way never changes. When I sit down to solve an ML problem on the other hand, I always have to go through the same steps-

* Research to see what other people have done in the same problem,
* Collect a list of 3-4 approaches,
* try them all. Have none of them work well,
* Repeat with different approaches until something sticks.

The same problem will often have a whole host of different solutions and I have really no way to determine which to pick.
For example, if you look at the [awesome nlp repository](https://github.com/keon/awesome-nlp#text-embeddings) you'll notice a thumb rule that says `fasttext > glove > word2vec`. On a surface level, I get that fasttext has subword information, so I won't need to worry about out of vocabulary words, because even if that word itself doesn't exist, the sequence of characters that make up that word will, and we'll be able to construct non random vectors anyway. Sounds great, but what if I'm using a library like [pymagnitude](https://github.com/plasticityai/magnitude) which solves the out of vocabulary problem anyway? does the thumb rule still hold true? The only way I can answer that question reliably is if I knew why that thumb rule exists, and there's always a possibility that even the author doesn't know, it's just something anecdotal from their day to day experiments.

Rachael Tatman tweeted this the other day - 
<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">What are y&#39;alls current favorite unsupervised classification/clustering approaches for text? So far I&#39;ve looked at:<br><br>🔡 LDA<br>🔡 Embeddings (doc2vec) + clustering (k-means<br>🔡 Unsupervised keyword extraction (YAKE)<br><br>Is there something else I should consider? 🤔📝</p>&mdash; Rachael Tatman (@rctatman) <a href="https://twitter.com/rctatman/status/1133806246182604800?ref_src=twsrc%5Etfw">May 29, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

What struck me is that each of the replies are different. Obviously if someone recommends LDA and UMAP over TF-IDF and spectral clustering it's because it worked better in their particular use case. Which leads me to think that even small changes in usecase can cause large variations in performance. What works well on twitter data may not be so good on reddit data. What's worse is that having a mathematical understanding of how the method works doesn't solve this problem either - just because I understand SVD and Matrix Factorization, I have really no way to tell LDA tends to work better on longer bodies of text.

Because of stuff like this, I find myself having to go through this benchmarking process every time I start a new problem and that feels like a huge waste of time. Things get murkier when we're dealing with unsupervised techniques, because all of a sudden we no longer have clear cut metrics for performance and even on the same dataset, there are very few objective measures of how well a model performs. What this ends up meaning is that machine learning just isn't like math, and we shouldn't obsess over the math - because mathematical intuition may not necessarily bring us any closer to developing a sense of model performance. The model itself sometimes helps - understanding how random forests approaches a continous variable definitely helps me create rules of thumb, but at least in NLP things aren't always that obvious. Perhaps that's why people like Jeremy Howard call themselves machine learning practitioners - because the only thing that brings us closer to an intuition is to try a bunch of things until they work and then remember what we tried. In the past, I've tried to keep tabs in a physical notebook about some features from the dataset (lengths of sentences, distributions of classes, where the data was from, how it was collected etc) and this somewhat helps, because it'll give me directions in which to start my search from, but inevitably I'll end up running through a bunch of things until I'm satisfied.

All I can say is I'm glad libraries like scikit-learn and pytorch exist that can make this process really simple and fast.
