---
id: 6
title: 'Galileo-Link - Connect THINGS to Social Network'
date: 2016-01-01T21:56:19+00:00
author: arun
author_profile: false
excerpt: "Hello people!The following project is a basic implementation of what can later give rise to a dedicated social network for sensors. Have you ever wondered how would it be if all your inanimate things can talk on social network like Facebook, twitter? Green-house updating it’s health on it’s timeline? This project is an effort to give social life to the “things”."

permalink: /posts/2016/01/galileo-link/
categories:
  - Academic Works
  - Unpublished
tags:
  - intel-galileo
  - iot
---


Hello people!The following project is a basic implementation of what can later give rise to a dedicated social network for sensors. Have you ever wondered how would it be if all your inanimate things can talk on social network like Facebook, twitter? Green-house updating it’s health on it’s timeline? This project is an effort to give social life to the “things”.
<!--more-->

{% include image.html img="images/2016/galileo-link/2016-01-01-galileo-link.jpg" title="CIFAR-10 example images" caption="Galileo-link posts room temperature and light values on it's timeline" %}

I have implemented the project using Intel Galileo board as the sensor gateway, you can use any other board running Linux OS with python installed. I tried making my home talk. That is, posting sensors data installed on Intel Galileo kept in my room to the Facebook account by name “Galileo Link” wall. So all we need is access to Facebook Graph API and a python script.

To access the Graph API, we need to create a Facebook App on [facebook developers](https://developers.facebook.com/apps) page.Now follow these steps:
1. Click `Add a New App` -> `Choose platform WWW` -> `Choose a new name for your app` -> Click `Create New Facebook App ID` -> `Create a New App ID` -> `Choose Category` -> Click `Create App ID` again.

2.Go back to Apps Dashboard -> `Select the new app` -> `Settings` -> `Basic` -> `Enter Contact Email`.

3.Go to `Status & Review` -> `Do you want to make this app and all its live features available to the general public?` -> Toggle the button to `Yes` -> `Make App Public?` -> `Yes`. This will enable others to see posts by your app in their timelines – otherwise, only you will see the wall posts by the app.

4.Now – you should see a green dot next to app’s name, and the text `This app is public and available to all users`.

5.Make a note of the `App ID` and `App Secret` (Click Show next to it; you will be asked to re-enter your Facebook password). Now that the app has been created, the next step is to get Facebook OAuth Token.

## Steps to get Facebook OAuth Token :

* Go to [Graph API Explorer](https://developers.facebook.com/tools/explorer/)-> In the Application drop down -> Select the app created -> Click `Get Access Token` -> In Permissions popup go to `Extended Permissions` tab -> Select `manage_pages`, and `publish_actions`.These permissions will allow your app to publish posts acting as the page -> Click `Get Access Token` -> You will see a message saying `{App} would like to post publicly to Facebook for you. Who do you want to share these posts with?` -> I chose `Public for maximum visibility` – as I wanted to post to a public page.

* Make a note of the short-lived token shown in Graph API Explorer.

* Facebook has deprecated offline access, the next best thing is long-lived token which expires in 60 days. We will convert the short-lived access token noted above to a long-lived token. For that, fill in the values in the URL below and open it in a browser:

[https://graph.facebook.com/oauth/access_token?client_id{APP_ID}&client_secret{APP_SECRET}&grant_type=fb_exchange_token& fb_exchange_token={EXISTING_ACCESS_TOKEN}](https://graph.facebook.com/oauth/access_token?client_id{APP_ID}&client_secret{APP_SECRET}&grant_type=fb_exchange_token& fb_exchange_token={EXISTING_ACCESS_TOKEN})

Replace the values in {}.

* You should see `access_token={...}&expires={...}`. This new `access_token` is the long-lived token we will use in our Python script.

* Long-lived token will also expire eventually, be prepared to perform this Step 3 again before that happens!

* We will use [Facebook Python SDK](https://github.com/pythonforfacebook/facebook-sdk) to access Facebook’s Graph API. You can install it using pip: `pip install facebook-sdk`.

```python
import facebook
print facebook.__path__

```
The path to the facebook will be displayed.Go to the directory delete facebook packages and then re-install it again.Problem solved!

Now the next important thing is the page ID which can be found in About tab of the Facebook account (usually it is a big number). Finally the code on Intel Galileo will post to Facebook wall of the Account.
Follow this [link](https://github.com/ioarun/galileo-link) to my github and find the python script for this project.
Here is the [link](https://www.facebook.com/profile.php?id=100009541012933) to my Galileo-Link!
Do follow for more interesting posts!




