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

Before we move on to the steps, lets first install docker on our machine. You can find steps for your machine on [Docker's installation page](https://docs.docker.com/engine/install/). Furthermore, we will also require installation of **Docker Compose**, which is a software required to run multiple dockerised containers. Find the instructions [here](https://docs.docker.com/compose/install/).

Create a ```requirements.txt``` file in your ```project/``` directory to list the python libraries we need. You can do this simply by typing the following while inside your virtual environment.
```bash
(django): $pip freeze > requirements.txt
```
We can use this file later to install python dependencies on any fresh python installation.

Also, since we will be running multiple containers, Its best to put your ```project/``` directory in a new directory. Let's called it ```Django-Docker/```. We will refer to this as the root directory from now.

<h3 style="text-align:left">Dockerizing the Django application</h3>

Let's first dockerize the existing application from the last tutorial. To start with this, create a new file **Dockerfile** in the ```project/``` directory. And write this inside it.
```docker
# Docker Image
FROM python:3.9.1-alpine

# Create directory for user
RUN mkdir -p /home/app

# create the app user. Root user is bad practice
RUN addgroup -S app && adduser -S app -G app

# Set env variables
ENV HOME=/home/app
ENV APP_HOME=/home/app/web
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

# Create the appropriate directories
RUN mkdir $APP_HOME
WORKDIR $APP_HOME

# Install development tools for alpine
RUN apk add --no-cache postgresql-libs
RUN apk add --no-cache --virtual .build-deps gcc musl-dev postgresql-dev

# install dependencies
RUN pip install --upgrade pip
COPY ./requirements.txt .
RUN pip install -r requirements.txt --no-cache-dir
RUN apk --purge del .build-deps

# copy stuff
COPY . $APP_HOME

# Give permissions to the new user for file useage
RUN chown -R app:app $APP_HOME

# Change root user to app user
USER app
```

Basically what we're doing here is, writing instructions for the container (which you can imagine as a new server/terminal) to install things for our application and then run.

  - **Docker Image** : Now, each docker container is instantiated from a **Docker image**. A Docker image is a read-only template that contains a set of instructions for creating a container that can run on the Docker platform. Now, the docker container we are going to make, will require some dependencies, and installed python engine on it. We get this by using a **parent image python-3.9.1-alpine**. This contains instructions for an alpine distribution of LINUX with python-3.9.1 installed on it. Images are available for the most common types of software on [Dockerhub](https://hub.docker.com/). 
  
  - **User creation and ownership** : While we need the root user to create and install things in the beginning. It is a good deployment practice to change the user to a non-superuser after creation. Because a super-user on the container, can gain access to the host system as a root user in case they manage to break the container. At the end of the script, you can see that we copy our entire project to the $APP_HOME directory and give the new user ownership of the $APP directory. We then change our user to the new user.
  
  - **Environment variables**: The HOME variables are directories for the user and the app itself, on the container instance. We also set our WORKDIR to $APP_HOME, so that the current working directory becomes $APP_HOME.
  
  - **Installation**: First we install some libraries that we will need to install dependencies on python. Remember this was also the case when installing **psycopg2** in the previous tutorial. After this, we install all our python dependencies in one go, using ```pip install -r``` command followed by filename. After installation, we delete some of the dev libraries as theyre not required for running the program. 

Now our Django application is dockerized. So, now we can create an image of this:
```bash
:Django-Docker/project$ sudo docker build -t app .
```
This will create an image called app from the current folder's Dockerfile (If we had named the file something else, we could still use it by mentioning it in the command). Now, we use this umage to spin up a container using: (written in 2 lines for easy reading)
```bash
:Django-Docker/project$ sudo docker run --detach --expose 8000 \
-p 8000:8000 -t app python manage.py runserver 0.0.0.0:8000
```
Using ```--detach```, we run the command in a separate fork, so our terminal is not binded to the container (this is optional). Using ```--expose```, we expose the port 8000 of the container to listen to requests, and with ```-p 8000:8000``` we bind our computer's port 8000 to the container's port 8000. So if we send a request to our computer's port 8000, it will go to the container, and then to the application, since we are hosting on 0.0.0.0:8000.

But if you run this command right now, you might be getting a database connection error. This is simply because, _there is no Postgres database instance in that container!_. **So can we install postgres in this container too?** Sure!, but that is not a good practice. Ideally, each component should run on separate containers. That would allow us to run the database server on a different machine as well. 

<h3 style="text-align:left">Setting up database container</h3>

This is where we need **Docker-compose**. This tool will help run the ```docker run``` command for us for multiple containers, and then allow us to network between them. Let's start by creating a **docker-compose.yml** file in the ```Django-Docker``` directory.




  

