---
title: "Bipedal Walking Robot using Reinforcement Learning"
category: articles
author_profile: false
permalink: /articles/2018-02-bipedal-walking-robot-using-rl
---
<p style="text-align:justify">

Machine learning algorithms have found several applications in the field of robotics and control systems. The control systems community has started to show interest towards several machine learning algorithms from the sub-domains such as supervised learning, imitation learning and reinforcement learning to achieve autonomous control and intelligent decision making. Amongst many complex control problems, stable bipedal walking has been the most challenging problem. In this paper, we present an architecture to design and simulate a planar bipedal walking robot(BWR) using a realistic robotics simulator, Gazebo. The robot demonstrates successful walking behaviour by learning through several of its trial and errors, without any prior knowledge of itself or the world dynamics. </p>

[[code](https://github.com/ioarun/rl4biped)]

<!-- <div>
<a href="http://arunkrweb.github.io/images/2018/biped/biped_trained.gif"><img src="/images/2018/biped/biped_trained.gif" style="width: 450px; height: 200px; align-content: center; margin-top: 10px; margin-right: 10px; margin-left: 10px; margin-bottom: 10px"/></a>
</div> -->
{% include image.html img="/images/2018/biped/biped_trained.gif" url="http://arunkrweb.github.io/images/2018/biped/biped_trained.gif" title="Biped Walker" caption="BWR walking steadily after 41 hours of training" %}

<p style="text-align:justify">
The autonomous walking of the BWR is achieved using reinforcement learning algorithm called Deep Deterministic Policy Gradient(DDPG). DDPG is one of the algorithms for learning controls in continuous action spaces. After training the model in simulation, it was observed that, with a proper shaped reward function, the robot achieved faster walking or even rendered a running gait with an average speed of 0.83 m/s. The gait pattern of the bipedal walker was compared with the actual human walking pattern. The results show that the bipedal walking pattern had similar characteristics to that of a human walking pattern. The video presenting the project is available at <a href="https://www.youtube.com/watch?v=Q4N78P7cink&feature=youtu.be">this https URL</a>.
</p>


 
 
