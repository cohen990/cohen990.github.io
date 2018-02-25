---
layout: post
title: "Tetris AI - Q Learning"
description: A redesign of my original tetris AI engine
tags: 
- Q-Learning
- Neural Networks
- Machine Learning
---

# Experimenting with ReLU
## ReLU -> Leaky ReLU
## Leaky ReLU -> Weird Asymptotic ReLU

# Escaping local minima with exploration

# Search algorithm
## Optimisations?

# Learning rate?
Learning rate could be reduced for the very early turns. Proportional to how late the turn is?
alpha * (turn_number/total_number_of_turns)
in that way, the closer you are to the end, the closer you are to the max learning rate and the closer to the start, the closer to the minimum learning rate

# Experiments
## Weird Leaky ReLU
## Weird Leaky ReLU with back prop of bias bug fixed
