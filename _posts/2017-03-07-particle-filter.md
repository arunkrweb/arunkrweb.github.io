---
layout : default
title : Robot Localization using Particle Filter
title-img : images/2017/particle-filter/particle-filter.gif
---

Robot world is exciting! For people completely unaware of what goes inside the robots and how they manage to do what they do, it seems almost magical.In this post, with the help of an implementation, I will try to scratch the surface of one very important part of robotics called robot localization.

<!--more-->

## What is localization?

Imagine that you are blind folded and somehow all you know about the surrounding is as far as you can stretch out your hands to touch the surrounding obstacles.This would give you an approximation of the distance of an obstacle from you in the surrounding but not your orientation in global coordinate system(surrounding).This information is not sufficient to know your exact location in the surrounding.

Trying to find the near exact location of the robot in a given space using noisy sensor data is called as *localization*

Take another example of a moving car.The GPS might show the car coordinates pretty well but when it comes to relying on just GPS values for autonomous driving, it is not that pretty.The error may go as far as 10 meters which can be fatal to the car.

Localization makes sure that we reduce this error by as much as possible from the given sensor information and dynamics of the car.


## Particle Filter

Consider the first example where you had to examine the surrounding by your hands.Suppose there are N of you and are randomly spread out in the surrounding and each of you have a distance vector which contains the distances from each of the obstacles.Since there are 1000 of yous, many of them would be in the vicinity of you near the same obstacle.Ofcourse the orientation may vary.Currently all the human beings are uniformally distributed.Until the next step which is called resampling.In resampling process only the humans near you would remain and others will disappear.You are said to be somewhat localized.

Formally, a particle is a discrete guess of where a robot may be located.Also, regularly, these particles are resampled from the distribution so that the particles consistent with the measurements *survive*.After successfull localization, the particles are collected in a region of high probability of the robot.

## Localize a Car!

Having taken the CS373 course on Udacity on AI for robotics, I had an urge to build my own robotic self-driving car.I am in the process to build it in simulation.This post is one part of it.I tried to build a 2D demo of robot-car localization using Python and Pygame.You can find the source code [here](https://github.com/ioarun/pygame-robotics/blob/master/particle-filter/particle-filter-2.py).

### The robot class

This class contains robot specific methods like 
* `set()` -  to set x, y and orientation
* `set_noise()` - to set forward motion noise, turn noise and bearing noise
* `move()` - to move robot
* `sense()` - to sense the bearing angle (angle between heading of robot and landmark location)

Instantiation of the robot class creates an object with random location and orientation.Let's call this object `car`. Then we will create `particles`. Make an empty list called `particles` and append it with the robot objects called `particle`.I created 1000 such particles.

{% include image.html img="images/2017/particle-filter/initial_condition.png" title="Initial state of particles" caption="1000 particles distributed uniformly in the space" %}

The figure above is a screen shot of my pygame screen.The blue circles are the landmarks.The car sprite is located in the center of the screen in the initial state and particles (green dots) are uniformly distributed on the screen.

At this current frame, several things are happening.The car *senses* the landmarks and generates a *measurements* vector with as many values as their are landmarks.These measurements are *bearing angles* or the angle between car heading and the landmark location.Ofcourse our sensors are noisy.So a bearing noise which is a guassian around mean 0 is added to these measurements.

### Weights

Since the particles are uniformly distributed, some would be closer to the car and some would be far.So the particles nearby car are good estimate of the car's location.*Weights* are the measure of how important the particles are.The larger the weight, the more important it is.
Next we allow the particles to survive at random, but the probability of survival will be proportional to the weights. That is, a particle with a larger weight will survive at a higher proportion than a particle with a small weight. This means that after resampling, which is randomly drawing new particles from the old ones with
replacement in proportion to the importance weight, the particles with a higher importance weight will live on, while the smaller ones will die out.With replacement means high probability particle would be drawn multiple number of times.So at any time there will be total 1000 particles.

There is one *measurement_prob()* method provided in the course that Sebastian Thrun, the course instructor wrote which finds these weights proportional to likelihood of the measurement.The argument consists of single *measurements* vector.Based on the difference in the actual measurement by the car and the predicted measurement by the particle into consideration and the Gaussian the method returns the likelihood of the particle.This is repeated for all the 1000 particles.

### Resampling

In the resampling process, highly weighted particles are selected with a high probability than the ones with low weights.These weights are *normalized* weights meaning, they are divided by the sum total of all the weights.Suppose we have 6 particles - p1, p2, p3, p4, p5, p6 and p3 and p6 have higher weights than the other four particles.We will run a resampling cycle 6 times.So, let's say the first time p6 was chosen.Second time again p6 was chosen, next p1 was chosen followed by p3, p3 and p5.So the new particles list will look like - p1, p3, p3, p5, p6, p6.Eventually after many iterations, the list may look like p6, p6, p6, p6, p6, p6.And we will see 6 of these particles approximately at the car location and will say that our car/robot is localized.

{% include image.html img="images/2017/particle-filter/next_condition.png" title="Localized car" caption="Small green dot is actually 1000 particles on top of each other localizing the car." %}

*Resampling wheel* is one way to implement resampling.It is very efficient 10 lines of code.We represent all the particles and their weights on a big wheel.Each slice on the wheel is proportional to the corresponding weights.Now we chose any index from 0 to 999 at random.Let's say we chose `W6` randomly.Create a variable `beta` and assign a value `2*max(weights)` to it.Now, we follow following steps until the value of `beta` is less than the weight at running index.Subtract `weight[index]` from `beta` and increment `index`.As soon as `beta` drops below the running weight, loop terminates and the particle associated with running index is appended to the new particle list.

Above steps are repeated till all the 1000 particles are not appended to the new particles list.

{% include image.html img="images/2017/particle-filter/resampling-wheel.jpeg" title="Resampling wheel" caption="[Source: Udacity]Resampling wheel" %}

## Next steps!

That was just one frame or instant.In the next frame, the car moves and so do the particles.These particles are the ones from previous step of resampling.The process of localization starts again and repeats as explained above.

{% include image.html img="images/2017/particle-filter/particle-filter.gif" title="Particle Filter Localization" caption="Particle Filter Localization" %}

That's it for now guys.I had fun implementing the particle filter.I am aiming to build a basic implementation of self driving car in pygame.More posts on the same soon.Do follow for updates!














