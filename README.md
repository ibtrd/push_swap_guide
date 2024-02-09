
# push_swap_guide

A proposition on how to solve 42cursus's project Push_swap !




## Introduction

First of all, I would like to point that the solution i'm going to talk about is heavily based on a guide writen by [EeeUnS](https://github.com/EeeUnS) from 🇰🇷42seoul. If you'd like to check out their original post, here's a [link](https://eeeuns.github.io/2022/04/15/push-swap/) to it!

Although decyphering their guide using Google Translate gave us some trouble, my program ended up crushing the operation requierements for a ✅100/100 grade on the mandatory part.

![100](https://github.com/ibtrd/push_swap_guide/blob/main/imgs/complexity100.jpg?raw=true)

![500](https://github.com/ibtrd/push_swap_guide/blob/main/imgs/complexity500.jpg?raw=true)

⬆️ Generated using [push_swap_tester](https://github.com/SimonCROS/push_swap_tester)


⚠️ Note that we won't talk about actual code here. We'll focus on the logic behind the algorithm, what makes it work. Implementing it will be up to you! Don't panic though, you'll be able to do so with basic C programming.



## Preparation

Before we dive into the algorithm, let's make sure we're on the same page. This guide will be focused on the sorting part of the project. We'll ommit anything about argument parsing and the way both stack's data are stored.

If you haven't already, I'd suggest you to work on both of these parts first, aswell as a function for each of the push_swap instuctions available to us (sa, sb, ss, pa, pb, ra, rb, rr, rra, rrb, rrr).


## ⏺️ PART 1 - Indexing

In order to sort our list of integers we'll not only need to compare them to each other, we'll also need to know if they are part of the X smallests of the list.

To be able to figure this out, we're going to replace every integer's value by it's expected index in the final sorted list. Yes, in order to sort the list using push_swap instructions, the first thing our algorithm's gonna do is ... sort the list 😆

#### One simple rule can be followed to assign an index to our integers: 

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

This step is crutial as it lets us work with **consecutive** values, aswell as their targeted position in our final list
## ⏩ PART 2 - Presort

### Initial state

![start](https://github.com/ibtrd/push_swap_guide/blob/main/imgs/visualizer_start.jpg?raw=true)
⬆️ Generated using [push_swap_visualizer](https://github.com/o-reo/push_swap_visualizer)

Alright, everything's setup! How about some actual sorting?

Because we're restricted to push_swap instructions (sa, sb, ss, ...) which only ever affect the top elements of **stack A** to sort it, unless it constains a really low amount of elements it's actualy impossible to sort it without pushing most of the elements to **stack B**. Then in the end, sorted elements must all end in stack A so any element pushed to stack B must be pushed back to stack A at some point. Since it would be terribly inneficient to push everything to stack B in a sorted state only to push then back in the same order, we're going to exploit this back and forth motion ```A -> B | A <- B``` with a presorting pass.

### Goal

    📝 Push every element from stack A to stack B, using their index to regroup them in an approximate ascending order

To achieve this we'll need to keep rotating through stack A, checking the top element's index to figure out if it meets a couple conditions. If one of them is met, we'll push the element to stack B, if they are not we'll keep rotating until a valid element ends up on top.

For these two conditions we'll need to keep track of two important values:

- The amount of elements we've pushed to stack B this far, which starts at zero and gets incremented by one for each push during this presorting pass.<br>From now on, we'll call this value ***pushed***!

- A constant value that will be determined by the total amount of element we're trying to sort.<br>We'll discuss on how to quantify it later down the line but from now on, we'll call this constant ***chunck***!

Out of simplicity for the following part, the index of stack A's top element will be refered as ***topa***.

#### First condition

- *topa* is lower than *pushed*.

#### Second condition

- *topa* is higher or equal to *pushed* and lower than *pushed* plus *chunck*

In order to regroup silimar elements as best as we can, after each push for the second condition we'll also apply a rotation to stack B to bring it at the very bottom of the stack. This way, every element pushed from the first condition will end up of the top part of stack B and every element pushed from the second one wil end up on bottom part of stack B.

### chunck value

As refered in [@EeeUnS](https://github.com/EeeUnS)'s [original guide](https://eeeuns.github.io/2022/04/15/push-swap/). They tried different chunck values for multiple amount of elements to sort in order to figure out a polynomial allowing us to set this value in our program.

For **x** elements to be sorted

*chunck* = ```0.000000053x^2 + 0.03x + 14.5```

### Presort state

In the end stack A should be empty, while stack B should contain all of our elements, with lowest elements located towards the middle of the stack and growing towards both of it's extremities.

![presort](https://github.com/ibtrd/push_swap_guide/blob/main/imgs/visualizer_presort.jpg?raw=true) 




## ⏪ PART 3 - Final sort



For this final step, we're going to push every element back to stack A, in their final position to end up with a sorted stack

### Goal

    📝 Push every element back to stack A, starting from biggest to smallest.

For this, we'll need to figure out which is the biggest element in stack B, rotate until this element ends up on top of the stack then push it to stack A. Repeat this action until stack B end up empty will result in a fully sorted stack A!

From now on, we'll call stack B's biggest element ***biggestB***

To find out biggestB, we'll scan all of the elements in the stack, consecutively comparing them to the biggest value encoutered yet. Start by setting *biggestB* to the value of the first element in the stack, then compare it to the value of the next element.
- If the next elements value is smaller than the previously saved one, *biggestB* does not change.
or
- If the next elements value is bigger than the previously saved one, *biggestB* gets overwritten by this new one. 

-  🔁 Repeat the comparison with the next element in the list, until you reach the end of it.


### Rotation

This part works efficiently beacause the presorting pass placed evey element close to elements of similar index, meaning that since we are now specificaly targeting each index consecutively, we are garrenteed to find each of them somewhat close to the previous one. So, for each targeted element that we want to push to stack A, there is just one last thing we need to do in order to move it on top efficiently:

    📝 figure out if we should rotate or reverse rotate stack B.

To do this, once we've figured out our targeted element (biggest in stack B) we will need to check where it is positioned in our current stack

- If it's placed in the first half of the stack, we'll **rotate** until it ends up on top.
or
- If it's placed in the second half of the stack, we'll **reverse rotate** until it ends up on top.

We've now raised biggestB on top of the stack, ready to be pushed back to A!

### Recap

Let's recap the suite of actions to follow to complete this part.

- Figure out which element is the biggest in stack B
- Find out if it is closer to the top or the bottom of the stack
- Rotate in the according direction until the element ends up on top of the stack
- Push it to stack A
- 🔁 Repeat the previous actions for each element left




### Sorted state

![end](https://github.com/ibtrd/push_swap_guide/blob/main/imgs/visualizer_sorted.jpg?raw=true)
## Optimization

Upcomming part
## 🔗 Links

- [@EeeUnS](https://github.com/EeeUnS)'s [original guide](https://eeeuns.github.io/2022/04/15/push-swap/)
- [push_swap_tester](https://github.com/SimonCROS/push_swap_tester)
- [push_swap_visualizer](https://github.com/o-reo/push_swap_visualizer)


## Contribution

Special thanks to

cdomet-d
