---
title: Fast.AI Notebook Weekend
date: 2018-09-16
draft: true
layout: post
categories: python, bash, deeplearning, fastai, iiitd
comments: true
---

I'm spending most of this long discontinuous weekend to run through all the fast.ai notebooks - while I find this course really helped me get a theoretical understanding of what neural nets are and how they work, I find that it takes me an inordinate amount of time to produce actual code - probably due to the fact that I never wrote anything while doing the course.

So this time around, I'm simply recreating each of the notebooks as quickly as I can, but ensuring that I'm actually implementing everything myself as compared to just running cells in their notebooks

Since I don't have a GPU on my laptop, I'm using the server that the [MIDAS team @IIIT Delhi](http://midas.iiitd.edu.in/) gave me access to.

The server they have is paltry compared to the insane amounts of computation groups like OpenAI and Google mention so casually in their blog posts and papers - but it's significantly more than what I need at the moment. Prior to this, I used a Paperspace account, and still have one, but the significant advantage I have here is that I can edit files on the server in real time since the latency is much lower.

Whether I'm using paper space or this server, I wanted to document a little setup that makes getting a notebook up and running even faster than manually running through each of the steps you normally have to do. Quite honestly, this is really trivial, but it shaves a few seconds off my time, so I thought I'd share.

## using a ssh config to forward jupyter notebooks on the server to my localmachine
```bash
Host paperspace
     HostName 209.51.171.251  # put your server's ip address/hostname here
     IdentityFile ~/.ssh/id_rsa # point it to your ssh key
     User paperspace
     LocalForward 8888 localhost:8888

Host iiitd
     HostName 192.152.25.36
     IdentityFile ~/.ssh/acer.pub
     User karmanya
     LocalForward 8888 localhost:8888
```
This one is very straightforward, and the fast.ai course recommends that you do this in lesson 1 itself. What this does is allow to run to any terminal and simply type in `ssh iiitd` or `ssh paperspace` and connect to the server. You'd also need to add your ssh public key to an `authorized_hosts` file on your server.

## Editing the bash profile
I don't see many people doing this, (and on the IIITD Server it doesn't make much sense since the server is always running) but on paperspace since you pay per hour, I turn the server off when I'm not using it. 
I add 2 lines to my `~/.profile` file in the server. 
```bash
conda activate fastai && cd ~/fastai
jupyter notebook
```
What these do simple activate the conda environment, navigate to the directory where I keep all my notebooks and run jupyter from that directory. I started doing this after I noticed the lines that conda inserts into your `bashrc` file to explicitly remind you that you always have the base environment activated when you open a terminal and do python-y things. The reason this is in `.profile` and not `.bashrc` is that `bashrc` will cause the commands to run every time you open a terminal as opposed .profile or bash_profile which will run every time I log into the server.

This setup isn't permanent, in that I'll eventually move towards putting things into a docker image, since I've been on a docker kick after I saw jesse frazelle's [really cool talk](https://www.youtube.com/watch?v=cYsVvV1aVss), but yeah. It works well enough for now.