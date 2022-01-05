---
date: 2019-03-11T09:00:00+06:00
lastmod: 2019-03-11T09:00:00+06:00
title: Migrations
tags:
  - Personal
slug: migrations
---
For the second time in two years now, I've decamped and moved cities. Unexpectedly, there's a fair amount of culture shock. In the past year I've gotten accustomed to being in a very quiet city and Delhi is certainly not that. Plus I've had relatives over, other relatives passing away and social obligations that I can't postpone, so things have been fairly busy; a far cry from how introverted I'd become in Hyderabad.  

Moving to Delhi also means that I'll be back to working from home - something I haven't really done before. It's surprising how much of a hit your productivity takes when you're working from your dining table/couch. It's also quite lonely - There are quite a few fairly active developer communities here though. They're also student dominated, which means I should get a lot of practise at learning to explain things better. I ended up giving my first tech talk since college about building data apps with gramex - and bombed miserably. I was underprepared and adapted very poorly to the audience that didn't really have much background with the subject matter; the one thing people seemed to respond to was the fact that gramex can act as a static site generator in roughly 5 lines of configuration. The only person that took something away from that talk was probably my colleague, only because he learned that you could pass pandas kwargs through the YAML config.   

I ran across a series of talks by [Regunath B](https://github.com/regunathb) that talk about [databases](https://www.youtube.com/watch?v=reTvj0l_kLE) that are used at [Scale](https://www.youtube.com/watch?reload=9&v=0RFOPNIro1A). I found this absolutely fascinating, I've never dealt with large distributed systems before, at Gramener, (at least in the year and change that I've been here) we don't really do projects at the Uber/Flipkart/Google Scale - for a variety of reasons, primarily I'm guessing because we haven't had a client that wanted a solution that needed to be run at that scale. The few projects that I've touched, double digit database instances are few and far between, let alone large distributed systems and the problems of maintaining data across shards/replicas. The talks were fascinating, because they talked mostly about the tradeoffs in a really simple manner - for example, how apparently it's relatively simple to get ~2ms or lower response time for queries in key-value pairs because they try to cache stuff as much as possible, however it often means a loss of durability, as your db writes are getting batched - so if you ever have a crash before the batch is flushed to the disk, you lose all the data that was supposed to be written to disk. Something I've observed from watching a bunch of his and my boss's talks is that every time they introduce a concept - they're very quick to emphasize why that feature or concept is important. I'll probably be structuring my talks similarly for the future.

Work itself has been fairly slow other than last minute prep for an upcoming event, and that's a welcome change - with all the family drama going on, it's nice not to be pressured from all sides. Undoubtedly the best part of being in Delhi is being near family - being closer to my mom and my grandparents at this point in their lives gives me a lot of comfort - I don't know how much it'll actually help if something untoward were to happen, but it gives me some sort of reassurance, that since I'm around them and spending time with them, it lets me keep a finger on the pulse of the situation and gives me a false sense of control.

I also bought Data Points by Nathan Yau and haven't gotten through a large part of it yet, but it seems much more interesting than the [Cole Nussbaumer Knaflic](http://www.storytellingwithdata.com/) which is oriented more towards presentations, and somewhat of a slower/more didactic read. 





