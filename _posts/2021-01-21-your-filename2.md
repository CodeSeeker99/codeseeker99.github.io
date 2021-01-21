---
published: false
---
---
published: false
---

_Short tutorial on using technologies like Django, Docker, Nginx and Postgres. In this part, we use docker-compose to run up containers of an Nginx webserver, a Gunicorn application server, a Django app, and a postgres database. This is a continuation of Django Build 1, and focuses on the deployment aspect of the application._

_**Note**: This tutorial requires you to have basic knowledge of python and linux commands._

---

<h2 style="background-color:black; color:white">BACKGROUND</h2>

In the previous tutorial, we made a django application and connected it to a local postgres database instance. 

#### Now, how do we deploy this application? 

Should we just simply take a computer, run the default django server on it and open its ports? Well, then who will handle the load balancing if we have multiple apps on the same project? Who will handle security, from say, certain types of DDoS? All this is not present in that server. This is why we called the given server in django "light weight". It cannot handle production level load. So, here we need a **web server**.

A web server is an entirely different class of software, which requires a lot of customization, so Django cannot possibly ship with a one-size-fits-all production server out of the box. This is where we bring in **Nginx**. 

<h3 style="text-align:left">Nginx</h3>
![nginx image]({{site.baseurl}}/images/NGINXLOGO.png)

Pronounced Engine-x, is a web server that can also be used as a reverse proxy, load balancer, mail proxy and HTTP cache. We will be using Nginx as a reverse proxy, and will ignore the other functions for this tutorial. As a reverse proxy, Nginx server will read all the incoming traffic, and then forward it to our application. Basically, it will handle request traffic, but it does no sort of processing.

_Great, so now we'll just connect Nginx to Django right?_

#### Nope

The issue is, Nginx is written to be a very fast http web server but it has no way of handling different types of applications. It does not know how to talk to different frameworks like Django, Flask etc. So for running our django application, we need what is called a **Application server**. This is where we will use **Gunicorn**.

<h3 style="text-align:left">Gunircorn</h3>
![gunicorn image]({{site.baseurl}}/images/gunicornLogo.jpeg)

Gunicorn is an application server written to work with any WSGI compatible framework. Web Server Gateway Interface (WSGI) is a standardised way of receiving requests inside an application. It stardardizes the recieving method's arguments, outputs etc. Now, the Gunicorn server is what will host our django code, and help it communicate with Nginx.

#### So, finally, our stack looks something like this

![Stack image]({{site.baseurl}}/images/webserverdiagram.png)

<h3 style="text-align:left">Docker</h3>

We have our application, database server, application server, and web server ready. So, how do we deploy this REST API? We could buy a server machine, install the same OS as ours on it, install the dependencies, and run all the servers on it (since its a small app). Sure. Is that portable though? What if you need to run it on multiple machines? What if you bought a machine with a different OS? You'll have to reinstall the OS on that. 

So, to make our application more portable, and to make sure it runs exactly the same as it does on our machine. We use **Docker**. Docker creates **containers** that are basically packages containing, our application, its dependecies and a small OS image consisting of only user-space funcionalities. Basically, a container becomes a fully independent package capable of running on any Docker compatible machine. So, you don't have to worry about installing different operating systems or writing multiple sets of instructions. The explanation here is kept short for the purpose of the tutorial, refer [this link](https://www.infoworld.com/article/3310941/why-you-should-use-docker-and-containers.html) to find out more. Also read [this article](https://devopscon.io/blog/docker/docker-vs-virtual-machine-where-are-the-differences/) to know about another technology called **Virtual Machines** that can be used for the same purpose, but is less efficient for our use. 

<h2 style="background-color:black; color:white">TUTORIAL</h2>

<h3 style="text-align:left">Installation and set up</h3>

Before we move on to the steps, lets first install docker on our machine. You can find steps for your machine on [Docker's installation page](https://docs.docker.com/engine/install/). 

Furthermore, we will also require installation of **Docker Compose**, which is a software required to run multiple dockerised containers. Find the instructions [here](https://docs.docker.com/compose/install/).

Also, since we will be running multiple containers, Its best to put your Django ```project/``` directory in a new directory. Let's called it ```Django-Docker/```
<h3 style="text-align:left">Dockerizing the Django application</h3>

Let's first dockerize the existing application from the last tutorial. 

Now, each docker container is instantiated from a **Docker image**. A Docker image is a read-only template that contains a set of instructions for creating a container that can run on the Docker platform. Now, the docker container we are going to make, will require some dependencies, and installed software. We get this by using a **parent image**. These are available for the most common types of software on [Dockerhub](https://hub.docker.com/).

