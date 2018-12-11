---
title: "Target pose estimation and reaching using supervised learning"
category: articles
author_profile: false
permalink: /articles/2018-01-target-pose-estimation-and-reaching-using-supervised-learning
---
<!-- excerpt: 'We introduce IP over Xylophone Players (IPoXP), a novel Internet protocol between two computers using xylophone-based Arduino interfaces'
date: 2012-05-02
venue: 'Proceedings of CHI (alt.CHI)'
citation: 'Geiger, R. Stuart, Yoon J. Jeong, and Emily Manders (2012). “Black-Boxing the User: Internet Protocol over Xylophone Players.” In Proceedings of the 2012 ACM Conference on Human-Computer Interaction (alt.CHI 2012). New York: ACM Digital Library. http://stuartgeiger.com/ipoxp.pdf' -->

<p>
This is an ongoing project whose main goal is to teach manipulation tasks to the robot by observing humans perform the tasks. This observation is fed to the robot in the form of image sequences or a video. In this post I will try to explain some of the preliminary implementation of one part of the project that is estimating the target position.
</p>

{% include image.html img="/images/2018/imitation-learning/pose-estimation.gif" title="Pose Estimation" caption="CNN predicts the target pose for reaching task" %}

<p> <b>Environment</b> - The simulation is set up in the Gazebo simulator. The robot being used is 7DOF Sawyer robot with an electronic gripper attached to the end-effector. A camera is placed on top of the workspace to capture images to be fed to the network. A table is also spawned in the world on top of which a blue cube randomly spawns.</p>

<p> <b>Task</b> - To predict the blue cube's position on the table and reach to that position.</p>

<p> <b>Architecture</b> -
<!-- 
150-('test cost :', 4.485624e-05, 'pred :', array([[ 0.72932047, -0.3483142 ]], dtype=float32), 'truth :', [array(['0.72', '-0.35'], dtype='|S5')  -->
{% include image.html img="/images/2018/imitation-learning/spatial-softmax-using-cnn.jpg" title="Architecture" caption="CNN architecture to predict cube position" %}

</p>
<!-- <div style="justify-content: center;">
<a href="http://arunkrweb.github.io/images/2018/imitation-learning/pose-estimation.gif"><img src="/images/2018/imitation-learning/pose-estimation.gif" style="width: 600px; height: 200px;"/></a>
</div> -->


 
 
