---
title:  "Compound Cellular Automata"
excerpt: "How to come up with cool patterns"
tags: 
  - art
  - automata
  - processing
---

Previously, I described the [Compound Automata](https://tatasz.github.io/compound_ca/). Here, I decided to show a bit more of the pattern finding workflow.

In this example, I will start with some arbitrary rule, $$R_0(\mu)$$, with $$\mu$$ being some parameters. Then, I will combine several instances of $$R_0$$ with different parameters into more complex rules.

## Density

Density, the fraction of "alive" cells is a very important parameter to decide which patterns will be combined together into a more complicated rule.

Below, the outputs of my $$R_0$$ rule with different parameters, resulting in different densities.

<figure style="width: 600px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/compound_ca_02_collage.png" alt="">
</figure> 

In general, the first step for me to generate a large collection of $$R_0$$ parameters with different densities, to later combine them.

## First level combination

Now, lets take a larger neighborhood. The largest neighborhood used in $$R_0$$ has range $$r=14$$, so for $$R_1$$, the next rule, I will use a neighborhood with range $$r=100$$. Lets call this neighborhood $$N_1$$.

Our first compound rule, $$R_1$$, will be defined like this:
- if $$\sum_{N_1} cells < a_1$$, we apply $$R_0(\mu_1)$$, our base rule with some parameters $$\mu_1$$
- if $$\sum_{N_1} cells > b_1$$, we apply $$R_0(\mu_2)$$, our base rule with some parameters $$\mu_2$$, different from $$\mu_1$$
- else, nothing changes.

For this first example, I will take the following two paramsets.

$$R_0(\mu_1)$$ with density 0.64:
<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/compound_ca_02_0.63642pattern-000200.png" alt="">
</figure> 

$$R_0(\mu_2)$$ with density 0.24:
<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/compound_ca_02_0.242584pattern-000212.png" alt="">
</figure> 

There are several values of $$a_1$$ and $$b_1$$ that will work. Lower values will lead to dominance of the low density pattern. In the animation below, I set $$b_1=a_1+50$$, and then slowly increased the values of $$a_1$$. You can observe how we start with low density pattern, then the high density pattern slowly emerges and then dominates it.

<iframe src="https://www.youtube.com/embed/bRMiWoyK6u0" width="560" height="315" frameborder="0"> </iframe>

