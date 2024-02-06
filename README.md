
# Push_Swap_Guide

A proposition on how to solve 42cursus's project Push_swap !




## Introduction

First of all, I would like to point that the solution i'm going to talk about is based on a guide writen by [EeeUnS](https://github.com/EeeUnS) from 🇰🇷42seoul. If you'd like to check out their original post, here's a [link](https://eeeuns.github.io/2022/04/15/push-swap/) to it!

Although decyphering the guide using Google Translate gave us some trouble, my push_swap ended up crushing the operation requierement for a ✅100/100 grade on the mandatory part.

![100](https://github.com/ibtrd/push_swap_guide/blob/main/imgs/complexity100.jpg?raw=true)
![500](https://github.com/ibtrd/push_swap_guide/blob/main/imgs/complexity500.jpg?raw=true)

⬆️ Generated using [push_swap_tester](https://github.com/SimonCROS/push_swap_tester)

⚠️ Note that we won't talk about actual code here. I'm only going to talk about the logic behind the algorithm, what makes it work. It'll be up to you to implement it 




## Preparation

Before we dive into the algorithm, let's make sure we're on the same page. This guide will be focused on the sorting part of the project. We'll ommit anything about argument parsing and the way both stack's data are stored.

If you haven't already, I'd suggest you to work on both of these parts first, aswell as a function for each of the push_swap instuctions available to us (sa, sb, ss, pa, pb, ra, rb, rr, rra, rrb, rrr).


## PART 1 - Indexing

In order to sort our list of integers we'll not only need to compare them to each other, we'll also need to know if they are part of the X smallests of the list.

To be able to figure this out, we're going to replace every integer's value by it's expected index in the final sorted list. Yes, in order to sort the list using push_swap instructions, the first thing our algorithm's gonna do is ... sort the list 😆

One simple rule can be followed to assign an index to our integers: 

    📝 Each integer's index is equal to the amount of integers in the list that are lower than itself.

As an example, let's take this small list of integers:

- ```-9```
- ```42```
- ```12```
- ```36```

**0** integers are lower than ```-9``` -> index 0

- ```-9 | 0```
- ```42 | .```
- ```12 | .```
- ```36 | .```

**3** integers (```-9 12 36```) are lower than ```42``` -> index 3

- ```-9 | 0```
- ```42 | 3```
- ```12 | .```
- ```36 | .```

**1** integer (```-9```) is lower than ```12``` -> index 1
- ```-9 | 0```
- ```42 | 3```
- ```12 | 1```
- ```36 | .```

**2** integers (```-9 12```) are lower than ```36``` -> index 2

- ```-9 | 0```
- ```42 | 3```
- ```12 | 1```
- ```36 | 2```

We are now able to sort our list using either the initial value of our integers, or their respective indexes.

- ```-9 | 0```
- ```12 | 1```
- ```36 | 2```
- ```42 | 3```

This step is crutial as it lets us work with **consecutive** values, aswell as it's targeted position in our final list
## PART 2 - Presort
