---
title: "Autonomous Navigation of drones using Q-Learning"
category: articles
author_profile: false
permalink: /articles/2018-03-autonomous-navigation-q-learning
---

<p style="text-align:justify">
This project implements autonomous navigation of an ARDrone in Gazebo simulator. Q-Learning algorithm was used in a simple gridworld setting. The state space included the current coordinates of the drone. A reward of -1 was given upon not ending up in the goal state and a reward of +100 was awarded for reaching to the goal.In the demonstration below, white spot shows the starting point and the red spot is the goal.
</p>

[[code](https://github.com/ioarun/rl4drone)]

{% include image.html img="/images/2018/drone/drone_qlearning.gif" url="http://arunkrweb.github.io/images/2018/drone/drone_qlearning.gif" title="Q Learning" caption="Autonomously navigating to the goal" %}
<!-- <div style="justify-content: center;">
<a href="http://arunkrweb.github.io/images/2018/drone/drone_qlearning.gif"><img src="/images/2018/drone/drone_qlearning.gif" style="width: 600px; height: 200px;"/></a>
</div> -->
<p style="text-align:justify; font-size: 12px;">
Reference paper : Pham, Huy X., Hung M. La, David Feil-Seifer, and Luan V. Nguyen. "Autonomous UAV Navigation Using Reinforcement Learning." arXiv preprint arXiv:1801.05086 (2018).
</p>

 
 
