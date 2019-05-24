---
title:  "Cellular Automata - from Game of Life to Compound"
excerpt: "Evolution from a basic CA to a weird thing with dozens of rules and parameters"
mathjax: true
tags: 
  - automata
  - processing
  - art
---

Some time ago, thanks to [Softology](https://softologyblog.wordpress.com/2018/03/09/multiple-neighborhoods-cellular-automata/) and [slackermanz](https://www.reddit.com/user/slackermanz/) with his awesome shaders, I got addicted to the Multiple Neighborhoods Cellular Automata. That was very fun, and exploring it let to multiple awesome rules and pictures, but then at some point I decided to add an extra complication to it.

And this is where those pictures come from.

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/compound_ca01.png" alt="">
</figure> 

So let's take a look at the evolution, from Game of Life to the Compound weirdness. All provided code is in Processing (Java mode).

### So, what is a Cellular Automaton?

A Cellular Automaton (CA) consists of a **grid** of cells (commonly square, but can be hexagonal or anything weird). It is not a must, but here we will stick to 2d grids. Each cell can be in one of a finite number of **states**. Here, we will work with binary states - 0 or 1, on or off, but it does not have to be. In fact, you can create multistate automata with similar behaviour.  An initional state (generation `t = 0`), is selected by assigning a set to each state either at random or following some pattern of our choice.

Then, for each cell, we define a **neighborhood** relative to it. For example, we could take the cell imediateli above and below a cell.

We obtain the next generation (generation `t = t + 1`) by applying a fixed **rule** to all cells (simultaneously, in all our examples). The rule is some mathematical function, or a set of functions that determines the new state of a cell based on its current state and the states of cells in the neighborhod.

### Game of Life

There are many great explanations and implementations of Game of Life, so I won't provide too many details here.

We have a square grid and 2 states (alive and dead). The neighborhood of a cell consists of 8 cells that are horizontally, vertically, or diagonally adjacent to it.

<figure style="width: 130px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/compound_ca02.png" alt="">
</figure> 

The rules are simple:
- a live cell with 2 or 3 live neighbors lives on
- a live cell with fewer than 2 or more than 3 live neighbors dies
- a dead cell with 3 live neighbors becomes alive
- else, it remains dead

So, how does it work?

We will work with a `n x n` square grid, where each pixel corresponds to a cell. Same height and width are not a must, but they sure help to simplify the notation. You start with two 2D arrays - lets call them A and B. A contains the current states os all cells, while B will store the state at `t+1`.

We populate A with random 1's and 0's. In the example below, I used a probability of 10% for 1, but when exploring more complex patterns, I recommend either using perlin noise or a gradient, to ensure that patterns that require a high density seed will still develop and you will not miss them.

Then, we iterate through A. For each cell `(x,y)`, we calculate the sum of the 8 neighbor cells. Based on this sum (and in this case the state of the cell itself), we apply the rules described above. The result is stored in B.

Finally, update A with values from B, and plot the new generation on screen.

It is easy to implement it in Processing:

```
//variables for current and next state
int A[][];
int B[][];

//initial state
void setup() {
  size(500, 500);
  background(255);
  noStroke();
  //initialize A and B
  A = new int[width][height];
  B = new int[width][height];
  //fill A with random values and B with 0s
  for (int i = 0; i < width; i++) {
    for (int j = 0; j < height; j++) {
      A[i][j] = (random(1) < 0.9) ? 0 : 1;
      B[i][j] = 0;
    }
  }
}

//iterative part
void draw() {
  //for each cell
  for (int i = 1; i < width - 1; i++) {
    for (int j = 1; j < height - 1; j++) {
      //number of living cells in the neighborhood
      int sum = A[i - 1][j - 1] + A[i - 1][j] + A[i - 1][j + 1] + 
                A[i][j - 1] + A[i][j + 1] + 
                A[i + 1][j - 1] + A[i + 1][j] + A[i + 1][j + 1];
      //apply rule
      if (A[i][j] == 1) {
        if (sum >= 2 & sum <= 3) B[i][j] = 1;
        else B[i][j] = 0;
      } else {
        if (sum == 3) B[i][j] = 1;
        else B[i][j] = 0;        
      }
    }
  }
  for (int i = 0; i < width; i++) {
    for (int j = 0; j < height; j++) {
      //fill A with B values
      A[i][j] = B[i][j];
      //draw cell
      fill(A[i][j] * 255);
      rect(i, j, 1, 1);
    }
  }
}
```

As you may have noticed, there is a small issue with the cells such as `(0,0)`: part of the neighborhood is outside of our defined grid. There are several ways to approach this problem:
1. Skip those cells, as I did above. The benefit is that its easy, stable and fast, but then you end up with some weirdness on borders and may not be viable with larger neighborhoods.
2. Wrap your grid into a torus: so, for example, the left neighbour of `(0,0)` will be `(n-1, 0)`, and so on. This requires some extra effort and very likely some extra calculations. And, as consequence, a performance loss.
3. Instead of storing a grid, store the coordinates of the "alive" cells. This may be somewhat disastrous for expanding automata.


### Larger than Life

Larger than Life is a generalization of Game of Life, described by [Kellie Michele Evans](http://emis.impa.br/EMIS/journals/DMTCS/pdfpapers/dmAA0113.pdf) in 1996. The basic idea is that, instead of using a `3 x 3` neighborhood, we can use a `(2r + 1) x (2r + 1)` neighborhood, where r is some positive integer. For example, below a neighborhood for range `r = 3`:

<figure style="width: 226px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/compound_ca03.png" alt="">
</figure> 

Then, the rule is changed to:
- If a cell is dead, it will become alive if `s`, the number of living cells in the neighborhood, is in the closed interval $$[b_1, b_2]$$.
- If a cell is alive, it will remain alive only if `s` is in the closed interval $$[a_1, a_2]$$.
- Else, the cell either remains dead or dies.
