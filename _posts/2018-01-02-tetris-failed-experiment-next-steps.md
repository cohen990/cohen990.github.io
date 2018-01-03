---
layout: post
title: "Tetris - Failed Experiment: Next Steps"
date: 2018-01-02 09:25:06
description: The exciting conclusion to the first iteration of my tetris AI experiments and my plans for the future.
tags: 
- AI
- Q-Learning
- CNN
- tetris
---
## Intro

The theoretical background for this experiment is available here: [Tetris AI Experiment 1 & 2]({% post_url 2017-11-13-tetris-ai-experiment-1-2-single-parent-evolutionary-algorithm %})

As before, the source code is available [on github](https://github.com/cohen990/evolution-of-tetris)

Since my last experiment, I have implemented crossover - AKA a 2 parent evolutionary algorithm. I have also tried changing the network structure to have 2 smaller hidden layers instead of 1 large one. The hypothesis was that this would allow more complex structures and concepts to be understood by the network. Both of these approaches have failed and that has led me to conclude that this model is a failure. A network like this is not well suited to tetris.

My new plan involves a search tree and a Q-Learning process using a convolutional neural network as the state evaluator. The key realisation is that tetris is a 2 dimensional grid. It has spatial features that can be analysed similarly to a photograph. Categorisation problems with protographs were only solved once people started using deep convolutional neural networks and I think the same techniques can be applied to tetris.

## What I Was Hoping For

My goal in this experiment has been to achieve a bot that can play tetris indefinitely. Given how quickly each decision is made and how fast the game clock runs, if a bot can survive for more than a second, I would consider that to be effectively sustainable.

## The Nails in the Coffin

I ran 3 different experiments attempting to get a functional tetris bot. They all failed and my final conclusion is that a fully connected neural network such as this one is not the appropriate tool for a tetris playing AI.

### The First Nail: Crossover

My initial hypothesis was that the network wasn't evolving usefully because each network would generate different traits and clusters of understanding, but because they were unable to share them, the variation alone would not be enough to achieve results. I implemented crossover with multiple crossover points and a chance to make a crossover at any given point.

[![graph of results plateauing at 1000 after 1200 generations]({{site.baseurl}}/assets/img/custom/blog/2018-01-02-tetris-failed-experiment-next-steps/crossover.png)]({{site.baseurl}}/assets/img/custom/blog/2018-01-02-tetris-failed-experiment-next-steps/crossover.png)

The graph plateaus at 1000 after a steep learning period between generations 200 and 400. The maximum never exceeds 50,000. 50,000 points is achieved by clearing at most 5 lines.

### The Second Nail: 2 Hidden Layers

The following hypothesis was that a single hidden layer could not unwrap the complexity of the 2d space of the tetris board. I modified my implementation such that there were 2 hidden layers instead. Unfortunately, because my implementation in java was not optimized for space and because I am running my experiments on my home computer, 2 hidden layers at 1000 nodes each required more RAM than I had available. As such, I reduced the number of nodes in each hidden layer to 100. After reading around the subject, I had begun to believe that 1000 was overkill anyway.

This experiment ran for 6000 generations. It was able to run for many more generations because the hidden layers are much smaller and the size of the weight maps is quadratic with the size of the layer. So these 6000 generations took a comparable amount of time to the 1200 generations from the previous experiment with the larger hidden layer.

[![graph of results plateauing at 2000 after 6000 generations]({{site.baseurl}}/assets/img/custom/blog/2018-01-02-tetris-failed-experiment-next-steps/2layers.png)]({{site.baseurl}}/assets/img/custom/blog/2018-01-02-tetris-failed-experiment-next-steps/2layers.png)

### The Final Nail: Maybe It's a Slow Learner?

I finally out of desparation tried to run the experiment for much longer. I ran the experiment for over a week. It completed almost 30,000 generations before I got home from holiday.

[![graph of results plateauing at 3000 after 30000 generations]({{site.baseurl}}/assets/img/custom/blog/2018-01-02-tetris-failed-experiment-next-steps/failed.png)]({{site.baseurl}}/assets/img/custom/blog/2018-01-02-tetris-failed-experiment-next-steps/failed.png)

## Why has it failed?

It is clear by this stage that the vast majority of the learning takes place in the first 2000 generations. This is the period during which the network learns to pack tetrominos into the grid space minimising the amount of empty space. As a reminder, I added a term to the fitness function which takes into account the amount of empty space on the grid. The networks I have build seem to have become somewhat efficient at this. Able to spot gaps and roughly fill them in with the tetromino they are controlling.
some snide comment about how if this limitation in cognition is inherent in the structure of the thinking organism (even a digital organism) then it is clear that the human mind will also be bounded by its structure

This leads me to the conclusion that a fully connected neural network is unable to make decisions about tetris effectively.
## What can we do?

My new concept is 
### The glimmering hope of convolutional neural networks

### Talk about the full overarching plan with Q-Learning

### Talk about tensorflow?
