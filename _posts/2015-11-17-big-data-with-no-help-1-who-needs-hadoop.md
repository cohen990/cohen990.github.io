# Who Needs Hadoop?

I went to a MongoDB conference recently. There was a talk by one of the senior Mongo engineers where he did a live demonstration of dealing with big data using MongoDB. I guess this got me thinking about how this stuff really works under the hood.  

Anyway long story short, I'm building a program which processes the data from wikipedia. All the data from wikipedia. The catch is that _I'm not using Hadoop or Spark_ (which are, I believe, the industry standard big data tools).  

The program is being written in Java and the repository is available [here](https://github.com/cohen990/conceptgraph).  

The current state of the program is that it will take a start URL and scrape the page using JSoup. It then grabs all the links from the page and adds them to a queue to be scraped in due time. It takes all the text from that page and splits it by whitespace and punctuation and creates a node for each word. A graph is then built up. Each vertex is either a word or an article title and each edge is weighted by the number of occurrences of a word within an article. There is a few interesting things that can be done to make the data more useful but that will be tackled in due course. This post is going to talk about the Heap Size.  

## Out Of RAM

In Java, there is a maximum heap allocation and exceeding this allocation causes the program to crash. Wikipedia has roughly 5,000,000 articles, each of which has roughly 3,000 unique words. Let's do an [order of magnitude estimation](https://en.wikipedia.org/wiki/Order_of_magnitude).  

Let's say 1,000,000 articles with 1,000 unique words. That's 1,000,000 vertices each with 1,000 edges. Each pointer to a node is 32 bits, each weight is 32 bits and each string is 16 bits * average length of a word (maybe plus some data that java manages internally) which is roughly 5 - so 80 bits per string.  

So, even ignoring details like the size of the hashmap containing the edges, the size of the queue in memory and the size of other data structures I use to track progress and reduce redundant work - we are talking about roughly 10,000,000 bits for the node names, 32,000,000,000 bits for the weights, 32,000,000,000 bits for all the pointers between nodes. so 64,000,000,000 bits or 8,000,000,000 bytes or 8 gigabytes. (If my maths is wrong, please let me know in the comments).  

Unfortunately, my machine only has 8 Gigabytes of RAM - about half of which is taken up by miscellaneous processes + IntelliJ, so I found that after about 1 hour, my process would crash.  

## The Solution

This is the part where I'm afraid of getting lynched.  

My solution is to write to disk after parsing each page and then drop all the connections from that node to free up memory. Originally, I was writing it ALL to the same file but processing 9,000 pages gives me a 10,000,000 line long txt file, so I ended up writing to a _separate file for each vertex_.  

So now I'm writing to .grp files (the extension was chosen so I can ignore them with .gitignore) which are basically just .txt files but if I want, I can define a file format later.  

[An example of the output](https://gist.github.com/cohen990/fcd52ff9ac40999b16be)  

This solution allows for continuous writing, so that if the process exits early, then I still have piles of data to work with. It also allows for reasonably fast lookup for a single vertex (which I have yet to implement) but probably will be very slow at taking data from many different vertices.  

## Next Week On "Why Am I Doing This To Myself?"

There are still many many problems to solve with this program. I'll document it within this blog.  

## Problem 1: It's going to take a month to download Wikipedia

It takes about 10 minutes to process 1,000 pages. With 5,000,000 pages, that means it will take about 34 days to process wikipedia (which, by the way, is not including the various foreign language pages which are not counted in my 5,000,000 estimate, but which my program will count as a separate page).  

## Problem 2: As the program runs, the process slows down.

I'm not sure if this is because Wikipedia throttles my connection or the space demands of the various data structures starts to choke the process.
