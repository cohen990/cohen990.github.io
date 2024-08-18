# Tetris - Convolutional Neural Network

##Long overdue
The last update I published about this was 18 months ago ([read here]({{ site.baseurl }}{% link _posts/2018-01-02-tetris-failed-experiment-next-steps.md %})). I did this work almost a year ago and have been struggling to write out a blog post for it. The number of changes I went through to get from A->B made it very difficult and I got lost in the detail. The wonderful thing about human memory however is that details fade. So now, 18 months later, unburdened by all of the details, I am finally ready to write up the results! Enjoy. If you have any questions or comments, feel free to contact me at samantha@thoughtress.tech

## Long story short - it worked

Let's make a very quick comparison.

[![A: The old system]({{site.baseurl}}/assets/img/custom/blog/2018-01-02-tetris-failed-experiment-next-steps/failed.png)]({{site.baseurl}}/assets/img/custom/blog/2018-01-02-tetris-failed-experiment-next-steps/failed.png)
[![B: The new system]({{site.baseurl}}/assets/img/custom/blog/2019-06-14-tetris-overdue/new-system.png)]({{site.baseurl}}/assets/img/custom/blog/2019-06-14-tetris-overdue/new-system.png)

In A: We see the results after 30,000 generations. The maximum score ever achieved was 60,000. In that old system, in order to over-weight the success case, I would multiply all points from line-clears by 10,000. Therefore the maximum points ever achieved were actually just 6. In 30,000 generations, my tetris bot only learned how to clear lines very irregularly.

In B: We see the results after 160,000 generations. Here, we see the maximum points ever acheived were in excess of 15,000. Actually I think it was closer to 17,500. In this system, I was multiplying the points by only 100. That means that the new system managed to score 175 points in a game! That's an improvement of almost 30x. The new system is able to score points very consistently and furthermore is able to sustain itself for a long time in a single game.

So, what's the difference between System A and System B? Time for a quick recap. If you've been binge reading these posts, feel free to skip ahead.

## System A vs System B

### System A

System A was written in Java. I wrote the mathematics for the neural network by myself. It was a neuro-evolution algorithm. This means that there was a neural network that evolved, not by back propagation as would be expected ([watch this if you're unfamiliar with back prop](https://www.youtube.com/watch?v=Ilg3gGewQ5U)) but by a 2-parent evolutionary algorithm. The basic idea is that each neural network can be represented as a genome. Each successful network is able to 'breed' with another successful network. By using concepts such as mutation and crossover, you are able to tease new features out of these two parent networks. Each generation has 1000 members. The 500 most successful (the ones that score highest) will survive into the next generation, and also be used to produce 500 new offspring which are the other half of the next generation. [See here]({{ site.baseurl }}{% link _posts/2017-11-13-tetris-ai-experiment-1-2-single-parent-evolutionary-algorithm.md %}) [and here]({{ site.baseurl }}{% link _posts/2018-01-02-tetris-failed-experiment-next-steps.md %}) for an in-depth explanation of System A.

By doing this, after many generations, features of the neural network which are able to make point-scoring decisions will be preserved and new ones will be evolved. Eventually, a network that can make good tetris-playing decisions will be born. _Ideally_. In truth, that never happened.

I think a large part of the reason for that, is because of the decisions being made by System A. You see, System A was making micro decisions. For each game tick, the system was choosing between Left, Right, Down, Clockwise, Counter-Clockwise. It wasn't aware of macro-decisions.

Other limitations to the system include the RAM demand. The generation size was limited to 1000 individuals because if it was any larger, I would use up all the RAM on my machine and it would crash.

### System B

System B as described at the end of [this blog post]({{ site.baseurl }}{% link _posts/2018-01-02-tetris-failed-experiment-next-steps.md %}). This system is much more sophisticated. It was capable of making macro-decisions in ways that A was not. Instead of choosing between different inputs, it was choosing between different final positions. It has a rules evaluation engine able to filter out illegal final positions and a neural network that is able to evaluate the potential points from each board state.

This time, the neural network was to be trained with back propagation. Instead of running 1,000 networks, generating a new generation and then running 1,000 more, it is able to run a game of tetris through a single network and evaluate it on its own merits. Then make adjustments and continue. This is an enormous increase in evolutionary efficiency. Instead of being able to evaluate once per 1,000 games, it's able to evaluate once per move. How? Q-Learning.

The network was trained using a process known as Q-Learning. This is a process geared towards problems where the result is not known until the end of the process. A game of tetris includes many moves. And the final score is not known until it is over. Therefore the value given by the first move is not known until the game is over. When using Q-Learning, you keep all the decisions made by the network in memory. After the score is finished evaluating, you can go back and run back-propagation for each decision.

## Getting from A to B

There was a lot that happened in between System A and System B. The initial plan for System B was a broad outline of the architecture of the system. It was to have a rules evaluator, a fitness evaluator and was going to be making macro decisions instead of micro decisions. All of that was retained through the process. A lot of other things changed.

### Python

I swapped over from Java to python. Writing mathematical code in Java feels weird. When I was doing my physics degree, all my maths work was done in python. Feels much more natural to me. Plus I was bored of Java. Hey, it's a pet project!

### The First System

The process for playing a game would look like this.

1. The system is given a Game State and a tetromino (the pieces in tetris are called tetrominoes, yes).
2. The system discovers all legal final positions for that tetromino
   1. If there are no legal final positions, then the game is over
3. The system passes each of these possible positions into the neural network and the network evaluates some kind of "expected score". E.g. "If you make this move, I think you'll finish the game with 351 points". All of these evaluations are stored in memory.
4. The system will most of the time choose the position with the highest expected score
   1. Sometimes the system will pick a random position. This is known as "exploration"
5. A new tetromino is randomly generated.
6. The board state is updated
7. Back to 1

The process for training the network is like this.

1. After completion of a game, each move and the network's evaluation of it are retrieved from memory.
2. You need both an evaluated result and an actual result. e.g. "I thought it would score 351 points in the end, but it was actually more like 120"
   1. At first, it was enough to use the final score for the actual result of all of these positions, although that ended up changing later.
3. This is used to adjust the neural network via back propagation

After training, the next game would begin.

My first attempt at the network itself was a Fully Connected Neural Network (FCNN). This is your very basic neural network. Each node in the previous layer was connected to each node in the next. The input layer was simply the play grid but flattened into a 1D array. My hope was that the network would be able to pick up on patterns that existed between the different layers and somehow be able to recognise the periodicity of the rows and columns. It didn't work.

### Swapping out the home-spun for Keras

As it turns, out, swapping over to python was a very good idea. Python has some of the most popular machine learning libraries! I initially started by writing everything. Because I like to punish myself. I even wrote a testing framework for some reason. Anyway, I wrote the testing framework and all the mathematical logic of the neural network. Getting the maths right took hours and hours. The tests weren't particularly well written either, so any change to the maths or model left me fiddling with the tests for hours more to get everything working again. Absolute nightmare. I stuck with it (mostly because I'm a masochist) until I realised that an FCNN was simply not cutting it any more. I couldn't get the damn thing to learn. I had decided that I needed a Convolutional Neural Network (CNN). From what I'd read online - writing a full CNN was a PhD level project. Not something I was going to dig into in my spare time. Time to give up on writing everything myself!

I swapped over to a popular python machine learning library called Keras. It took my hundreds of lines of badly written maths and replaced it with about 5 lines of configuration. There was a bit of a learning curve to be fair but it was very much worth the effort. Especially since it allowed me to convert my network from a Fully Connected Neural Network into a Convolutional Neural Network.

### Convolutional Neural Network

Convolutional neural networks are responsible for a lot of breakthroughs in machine learning. Most notably, image recognition. AlexNet was entered into a visual recognition challenge in 2012 and completely blew the competition out of the water. This paved the way for an enormous rennaissance in computer vision. In short: Convolutional neural networks are very very good at image recognition. I recognised that the tetris board is _kind of_ just a very low resolution image (20x10). So a convolutional neural network should be pretty well suited to decision making based on the board.

Well I swapped it in and hey! Look at that! It's working a lot better.... It's not quite right yet though.

### Changing the type of error

A neural network is a very strange machine. Essentially, the way they work is they have an _Error Function_. This error function is how you give feedback to the network. Think of a child. You ask the child to draw a dog and the child instead draws a cat. You gently explain to the child that a cat doesn't have ears like that and ask them to try again (assuming some kind of idealised child). This time the child draws a cat with dog ears and you explain how a dog's nose would look. Etc etc. Eventually the child can draw a dog! Well a neural network is _kind of_ like that. The error function is a "No, that's not quite right. It should be more like this.". The neural network can read this feedback in back propagation and use it to correct its behaviour.

The _actual_ goal of a neural network is to minimise this error. Every iteration, every time it trains, every single thing it learns is an attempt to reduce this error.

I had a problem.
The way I was calculating the error was a Mean Square Error function. So you take the difference between the expected score and the actual score and then square it.

Mean Square Error roughly: (Actual - Estimated)^2

Sounds fine, right?

Well... Not really. So when the network starts, it's basically randomly guessing how good each move is. And because it's randomly guessing, usually the games don't go that well. So it'll guess that a move is worth 100 points where actually it scores 0 points. The mean square error of this is in the region of 10,000 (0 - 100)^2. So the network is like "Whoa! Ok that was totally wrong. Let's adjust by a lot!". This time it guesses a move is worth 15 points. It's still not very good at what it does so it scores 0 again. This time the error is in the region of 225 (0 - 15)^2. So actually, the network is doing pretty well so far. The error is going down. It's getting more accurate at predicting.

Ok looks good so far. The problem is when the network starts getting more capable.

So at some point, the system will accidentally play a move that scores a point! Wow! The network has gotten used to zeros at this stage so maybe it evaluated it at 0 points. But in the end it got 1 point! That's great! This time the error is something more like 1 (1x1 - 0x0) but really the point is that the network will start to learn to recognise moved that can produce higher points! That's good, right? So now the network is able to play games that score more points!

So the games continue. Maybe 100 games down the line, the system manages to score 10 points! It evaluated each move at maybe 1 or 2 points. This time the error is something like 64 (10 - 2)^2... Ok the error spiked up but it's ok because the system is getting smarter, right?

10000 games down the line and the game scores 10,000 points. It was evaluated at only 9,400 points... This time the error is about 360,000 (10,000 - 9,400)^2! That's a big error. The evaluation was only off by 6%. It's still a very accurate evaluation. Does it really deserve such a heavy punishment?

We've ended up in a situation whereby a system designed explicitly to minimise its error is accidentally increasing its error exponentially! Because the score of tetris can climb ever higher, as the system performs better its error is going to climb faster and faster.

It's very hard for me prove. A neural network is a black box. My assumption is that the network would get to a certain level of performance and then, because of over the top back propagation, it would start wildly overshooting the mark on back propagation and therefore being unable to make a progress.

It seemed to me that the only way to get it to perform correctly was to get rid of this exponential growth of error.

#### Introducing the MSLE (another acronym?)

The MSLE is the Mean Square Logarithmic Error.

roughly speaking: (log(Actual + 1) - log(Estimated + 1))^2

Without digging into the maths too much, an exponential is a function that grows more and more quickly and the input grows. A logarithm is the opposite. It's a function that grows more and more slowly as the input grows. In fact it's exactly the opposite. If you take the log of an exponential, they cancel each other out.

10^3 = 1000
Log(1000) = Log(10^3) = 3

Let's take the numbers we were playing with before and run them through the MSLE.

| Actual | Estimated | Mean Square | Mean Square Logarithmic (to 2 sig.fig.) |
| ------ | --------- | ----------- | --------------------------------------- |
| 0      | 100       | 10,000      | 4                                       |
| 0      | 15        | 225         | 1.4                                     |
| 1      | 0         | 1           | 0.091                                   |
| 10     | 2         | 64          | 0.31                                    |
| 10,000 | 9,400     | 360,000     | 0.00072                                 |

So what you can see here is a harsh punishment when under-evaluating (0 against 100) but a very very light punishment when the percentage difference is small (10,000 against 9,400).

This allows the network to continue to function properly, long after it becomes a viable tetris player.

Episodes
Epochs

It's not working. It's never really worked. How can I simplify?
Reduce problem scope - cut down to only 1 piece
O-tetromino tests - looping
L-tetromino tests - much more difficult to loop

Get it working with only one piece

The error was still climbing
why?
mean square logarithmic error

reintroduce all the pieces

IT WORKS!

Next steps?
In any game of tetris, you are able to see the next piece and plan ahead. That's the next step for my bot. It needs to be able to see the future.
