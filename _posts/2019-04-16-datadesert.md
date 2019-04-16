---
title: डेटा डेजर्ट
date: 2019-04-16
draft: true
layout: post
categories: tech, data science, machine learning
---

The other day, I was helping out some people at my research lab write a couple of sentiment analysis classifiers for the Hindi Language and I was surprised to find that almost every famous English Language Model(Glove, FastText, Word2vec) has a Hindi equivalent that is very easy to find. I was even more surprised to find despite this, there is a dearth of well put together, annotated datasets for modern NLP Tasks. 

It's not like people in India haven't been working on NLP, far from it.  Amazing people, much smarter than me at IIT Bombay and other big name universities have done a lot in terms of rules based parsing, releasing corpora, entity extraction tools and building wordnets. That being said, thanks to Jio and its ilk, the hindi side of the internet is growing at insane rates right now - and there doesn't seem to be enough attention towards building datasets for sentiment analysis, document classification or sequence to sequence models. 

So, why not build a few? Well, dataset creation is a pretty much thankless activity. Creating large datasets doesn't really scale if me, or all of my friends are the sole annotators. Plus I have a lot more experience building tooling than I do building datasets. Taking a look at current market tooling, something like Prodigy.ai is hands down the best thing I've seen. The design is slick, it's relatively easy to use and their active learning capability is really awesome. However, it's really pricey. Moreover, I'd like to leverage the large number of motivated engineering undergrad students that are looking for causes to contribute to, and are perfectly capable of general annotation tasks (if we take care inter-annotator agreement), and we'd need to gamify the process. 

The way I envision this playing out is a simple web app that anyone that wishes get annotated can host, add datasets, (optionally)add a simple test for all annotators to have to pass, and then they or anyone can export the annotated data at any point of time, in any format they like. The annotators would get shuffled rows from the dataset that they can annotate. At the time of export, the median of all annotations for that row would be exported as the appropriate label. 
Gamification comes through an internal leaderboard as well `free` github PRs towards a repository that holds contributor info - thus the hordes of students that run around on hacktoberfest adding their names to 'signup sheets' and word lists can contribute to something slightly more productive. 

In order to make annotations easier, I'd need some sort of learning or classification process - I'm not sure how Prodigy.ai does it, but something that should work is to create document vectors for every row and every time a row gets labelled, look for other similar rows. Alternatively, I could do this at the word level - I don't expect the output to be wildly different, just perhaps it would work better while there are fewer labelled rows. 

Currently the project sits on [my github](https://github.com/CalmDownKarm/annotation-tool) where the backend is completed, but the frontend needs to be built, as does the gamification aspect of it. The backend is currently based upon gramex, the platform that my company builds, simply because of my familiarity with it more than anything else, though it does offer some conveniences. 

Obviously PRs welcome, and feel free to drop an issue/reach out to me over email if you'd like to work on this. 


