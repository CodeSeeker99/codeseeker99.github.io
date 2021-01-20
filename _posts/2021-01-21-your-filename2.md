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
![Theory image]({{site.baseurl}}/images/webserverdiagram.png)

In the previous tutorial, we made a django application and connected it to a local postgres database instance. 

#### Now, how do we deploy this application? 

Should we just simply take a computer, run the default django server on it and open its ports? Well, then who will handle the load balancing if we have multiple apps on the same project? Who will handle security, from say, certain types of DDoS? All this is not present in that server. This is why we called the given server in django "light weight". It cannot handle production level load. So, here we need a **web server**.

A web server is an entirely different class of software, which requires a lot of customization, so Django cannot possibly ship with a one-size-fits-all production server out of the box. This is where we bring in **Nginx**. 

<h3 style="text-align:left">Nginx</h3>

Pronounced Engine-x, is a web server that can also be used as a reverse proxy, load balancer, mail proxy and HTTP cache. We will be using Nginx as a reverse proxy, and will ignore the other functions for this tutorial. As a reverse proxy, Nginx server will read all the incoming traffic, and then forward it to our application. Basically, it will handle request traffic, but it does no sort of processing.

_Great, so now we'll just connect Nginx to Django right?_

#### Nope

The issue is, Nginx is written to be a very fast http web server but it has no way of handling different types of applications. It does not know how to talk to different frameworks like Django, Flask etc. So for running our django application, we need what is called a **Application server**. This is where we will use **Gunicorn**.

<h3 style="text-align:left">Gunircorn</h3>

Gunicorn is an application server written to work with any WSGI compatible framework. This application server is what 