---
layout: post
title: IBM Bluemix - IOT Tutorial
---

It’s been quite some time now that I have been playing with IBM Bluemix platform and applying it in various IOT projects.You can make your application on this platform and bind it to some services.
<!--more-->

The services can not only be used by the run time applications on bluemix but also can be accessed by programs running on remote machines.This tutorial will be on how to set up IBM Bluemix for using it's IOTF service.The client that I'll be using is a python client.

This particular implementation publishes the CPU utilisation(in percentage) of my computer to the IoTF cloud. Find the source code on my [GitHub](https://github.com/ioarun/ibmbluemix-python).

## Pre-requisites:

* You need to register on IBM Bluemix.

* You should have Linux machine with paho-mqtt package installed for python.

* Basics of MQTT.

* Knowledge of Python programming.

So let’s get started!

Assuming that you have registered and are logged into IBM Bluemix,you will see a page something like this :

![one]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-one.png)

Few names that are important here : `organisation` , `region`, `space`.

`organisation` – It is like your unique Identity on Bluemix.

`region` – There are two regions(US and UK).By default,my region was US.

`space` – It is like a directory where your work is saved.All the apps and services are added to your space.I have named my space as `dev`.You can give any name.

![two]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-two.png)

After creating the space you can add apps or services to your account.I will add a service called Internet of Things which can be found in the CATALOG tab.This service gives you cloud platform to publish or subscribe the data.The data is published or subscribed using MQTT protocol.I will not elaborate on MQTT.As for now, you should know that it is a machine to machine light weight protocol for implementing Internet of Things.

![three]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-three.png)

Name your IoT service and Leave it unbound.Then hit create.

![four]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-four.png)

You will be asked to add a device.Now device is a “thing” which you want to get connected to the cloud.This “thing” will be called as device hereafter.Any device which intends to publish or subscribe the data needs to be registered first.We do it by adding this device.

![five]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-five.png)

Name your device.Device name will be referred to as device type.You can add description of your device.This is not necessary though.Hit next and you will be asked to add device ID.Now this can be any unique ID.Usually we give MAC address of the device.Any unique sequence of characters will do.Remember that “:” i.e, colons are not valid so you need to remove these from your MAC address.

![six]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-six.png)

![seven]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-seven.png)

![eight]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-eight.png)

![nine]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-nine.png)

![ten]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-ten.png)

Leave the authentication token as blank as it will be automatically generated.Hit next and you will see a page containing all the important credentials required to authenticate your device.You need to put this information in your code that runs on your machine.
That’s it.Now add this newly created device.

You will see your device added in the list.The status of the device initially will be something like this.

![eleven]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-eleven.png)

Now,I will run the python code on my machine and let’s see what happens.

![twelve]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-twelve.png)

![thirteen]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-thirteen.png)

You can see the data on the Bluemix as well.Click on the device name and refresh the prompted page.You will see the data being published by the machine/device.

![fourteen]({{ site.baseurl }}/images/2016/ibm-bluemix/2016-01-18-fourteen.png)

IBM Bluemix provides various other interesting services and supports many runtime languages to build applications.Do explore them and keep learning!

If you found this blog post helpful please follow for more such articles.









