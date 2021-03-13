---
layout: post
title: Bit of tho-uce on model selection and "ground truth" 
description: I know I may sound like a knobhead (as I am).
nav: true
comment: true
---

Recently I have been bothered with some tasks where the learning became slow. I tried to figure out why by tuning everything which exhausted me out. It turned out that it's because of the additional neural layers which makes it slow.

### Basics about model selection
So I believe model selection has been both intuitively and mathematically detailed by many brilliant blog posts. For example [this one](https://machinelearningmastery.com/a-gentle-introduction-to-model-selection-for-machine-learning/). In general it is a quite self-explained method: *you want to select best models across different ones for your scenario*. In order to do that one has to design his/her own method so it’s quite intuitive. It can be as simple as in supervised learning, where we simply get several models and compute their pre-designed loss functions on a held-out set from training data, which we usually call ‘validation’ and never got into the main training process; or as complicated as acquiring some weird distribution and compute Kullback-Leibler divergence. The complicated case in one of previous works I got involved [1] - we use simple things like mean-squared error and it lay more on engineering effort, though. 

Of course, some substitutes such as model averaging is also very popular, espeically a hybrid of filtering & averaging - remove bad model below some threshold after each iteration, and calculate average weights across models. Model averaging happens a lot as well for good regression models, and also sometimes for neural networks (e.g. Kaldi raw DNN. Check [this script](https://github.com/kaldi-asr/kaldi/blob/master/egs/wsj/s5/steps/nnet3/train_raw_dnn.py) for more information).

There are probabilistic measurements as well, including things like AIC, BIC… yet to not let things going too Bayesian and cognitive side I’ll not address it too much here. More details can be found in the link above. Here we keep thing intuitive.

-------------------
### But.....sometimes isn't 'ground truth' bothering?
But whichever method we use, we anyway have to do two things to leverage model selection process:
1. Select a proper criteria/loss function.
2. Have a 'ground truth' held-out dataset for computing them.

There are other works that only need step 1 by computing alternate representations from input and calculate them. For example, in spectral analysis, sometimes people use all-pole excitations and derive 'ground truth' spectrum as ground truth. They can use useful either for stats analysis and model training/validation. But sometimes, design of both steps can be pain in the ass
* There are plenty of loss functions available. For different scenarios their efficiency can be different.
* There is nothing like 'ground truth', apart from the labels themselves.

OK, I know somebody may raise things like [minimum description length](https://en.wikipedia.org/wiki/Minimum_description_length) (MDL) or Occam's razor - the former is math implementation of the latter. But that is based on perfect hypothesis across all models, which, especially in the era of those neural network "black-boxes", we can't tell. So if we kick it away, "ground truth" in particular, can be annoying to argue and discuss. We can find loss function from some earlier papers or code, but for "ground truth", especially if sometimes we wanna do **unsupervised** model selection, we never know what's gonna happen but have to explore all options in the wild.

-------------------
### Can we wave 'ground truth' away? --Dropout
Cognitively, we can't. As in our daily life, we measure everything using some reference. But sometimes, such reference can come from nowhere and disruptively come against common rules, which starts from randomness and comes static and natural as time goes, right? Same in machine learning: there has a way to perform model selection by starting from randomness and letting the learning be stable as time goes, which is Dropout [2]. 

OK I know it sounds like a really boring answer and I seems made a huge mistake on what Dropout is from above - Dropout is all about randomness. As seen in the paper, every neuron is assigned by a probability and good forwarding with significant gradient occupation would not guarantee its significance in next iteration, as a result. On top of it Sida et al proposed a more efficient version [3] via Gaussian masking instead of conventional Bernoulli one. But such randomness from Monte Carlo simulation still holds. If we would like to do deterministic selection, we may still needs some external representation. Also, maybe I am terribly wrong - can Dropout be applied to regression models? I am lacking knowledge on this one for sure but as far as I know the answer is no...

-------------------
### Does 'deterministic' Dropout via alternative representation makes sense?
So one crazy idea I have here: at least as a speech researcher, I would like to find a way to have an *unsupervised* model selection method which can let the model figure out which part of its components can be abandoned for this iteration via some deterministic or predictable information? One choice I think and am experimenting on is its alternate representation. This will be much similar to self-supervised learning models (in speech, a good example can be [PASE](https://github.com/santi-pdp). But it also performs conventional Dropout in my understanding, if it has it). But I believe there are some other methods such as statistical thingy. I am investigating on it and we will see how it goes.

-------------------
### A 'final' word on blogging
OK as the first blog I tried to challenge myself by not recalling my previous publications (there is a recent one, but I will talk about it later after more workings) but just drive my thoughts and motivations to write something crazy. This will be the general tempo of this blog - not much mathmatical, nothing very 'nerdy' (you can find much better people on this one. I am not good enough so far), but just random thoughts from my brain with a little bit of sauce from science and sense of rigor - intrisically, that's me.

Again, I hope it can be a good start to write more this year. Looking forward to it.

-------------

UNDERDOG 

2021-03-11

Nancy, 23C, Sunny

[1] [Teacher-student training for text-independent speaker recognition](https://ieeexplore.ieee.org/document/8639564), Raymond W. M. Ng, Xuechen Liu, and Pawel Swietojanski, IEEE SLT 2018.

[2] [Dropout: A Simple Way to Prevent Neural Networks from Overfitting](https://www.cs.toronto.edu/~hinton/absps/JMLRdropout.pdf), Nitish Srivastava, Geoffrey Hinton, Alex Krizhevsky, Ilya Sutskever, Ruslan Salakhutdinov, JMLR 2014.

[3] [Fast dropout training](https://nlp.stanford.edu/pubs/sidaw13fast.pdf), Sida Wang, Christopher Manning, ICML 2013.