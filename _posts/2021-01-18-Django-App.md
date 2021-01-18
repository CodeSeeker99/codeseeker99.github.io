---
published: true
---

_Short tutorial on using technologies like Django, Docker, Nginx and Postgres. Used Django backend to create a REST API with a simple CRUD functionality on a local postgres database instance. This tutorial requires you to have basic knowledge of python. This tutorial follows linux commands._

---

## The basics

### REST API and CRUD

An __API__ or __Application Programming Interface__, in simple terms is a bunch of code that helps interact with a lot of backend code. It provides definite input, output, and error pathways to the backend code.You can write a basic API using functions in Python.  

A __REST__ API or a __REpresentational State Transfer__ API is an API that follows constraints and operating standards called REST. This standard is popularly used in web services. To list the tenets of a REST API is worth an entire article. To summarize them, they standardise service responses so that reading them becomes device/platform independent. This includes, giving responses in JSON/XML, and using only a few HTTP headers (POST, PUT, GET, DELETE). In this project we're going to create a REST API using Django REST framework. A framework is a set of libraries that do most of the heavy lifting for us, and allow us to focus on the functionality of the API rather than thinking about its low-level details.

__Create, Read, Update, and Delete__ or __CRUD__ are a set of basic functions which any service that interacts with a persistent storage (i.e Database) requires. In this project, we will be making an app which does CRUD operations on a Postgres database.

### Django

Django is a web framework based on python. It promotes fast development by pushing a lot of general details such as security and scaling into the framework backend, hence giving the programmer more time to focus on useful code and business logic. It also ships with a default light-weight server, which helps in beginners with quick learning and adoption. Since Django is based on python it also enjoys the huge amount of support in terms of libraries that the python community provides. The developers claim that > Django was designed to help developers take applications from concept to completion as quickly as possible.

__Django REST framework__ allows you to use the same backend of django for RESTful services as well. A good feature of this framework is how easy it is to return web-browsable reponses using the standard Django template, which helps in easy debugging. 

### Virtual Environment

A virtual environment is a tool that helps keep your dependencies from multiple projects well separated and documented. This is extremely helpful when you need to export your project and require a list of project-specific dependencies.

## Tutorial

1. Install python3 on your machine. The steps are different for different distributions, so its best to refer the internet for this. Once you have it installed, verify the installation using.
```bash
$ python3 --version
```
This should return the version of python installed.


2. Python comes with its own virtual environment command called venv. On linux and Mac distributions this will suffice. On windows, you might have to download virtualenvwrapper. To make a new virtual environment, type:
```bash
$ python3 -m venv /path/to/new/virtual/environment/name_of_env
```
This will create a new folder with the given name at the given location. I'm going to place mine in a new folder called tutorial. Then run the activate script using the source command. 
```bash
$ python3 -m venv ./tutorial/django
$ source ./tutorial/django/activate
(django):$ 
```
This should bring up the name of your virtual environment in brackets before your current working directory in the terminal. 


3. Now, we need to install the dependencies for this project. Install all of them using:
```bash
(django):$ python3 -m pip install django
(django):$ python3 -m pip install djangorestframework
(django):$ python3 -m pip install psycopg2
```
This will install Django, Django REST framework, and psycopg2 libraries. psycopg2 is required to interact with the postgres database that we will need later. In case you run into an issue with psycopg2 installation. Try installing the following libraries first:
```bash
(django):$ sudo apt install libpq-dev python3-dev
```
These dev libraries will help with the installation steps on psycopg2


4. We can now finally start with the app. Create a django app using:
```bash
(django):$ django-admin startproject project
(django):$ ls
django  project
(django):$ tree ./dummyproject
./dummyproject/
├── db.sqlite3
├── dummyproject
│   ├── asgi.py
│   ├── __init__.py
│   ├── __pycache__
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py
```
As we can see, this command created a new directory called project in our current directory. This directory contains basic django code, and a lightweight server for development and testing locally. There are a few simple components here that need to be understood.
__wsgi.py__ : This is the python file that helps in deployment on different types of production servers (these are called Web Server Gateway Interface (WSGI) compatible servers). We will ignore this right now.
More info [here](https://docs.djangoproject.com/en/1.8/howto/deployment/wsgi/).
__urls.py__ : This file helps in managing routing the request to different components inside your project (called apps). More info [here](https://docs.djangoproject.com/en/1.8/topics/http/urls/).
__settings.py__ : This file defines a lot of environment variables and settings for the project. Including database and allowed hosts. More info [here](https://docs.djangoproject.com/en/1.8/topics/settings/).
__manage.py__ : This file helps you run command line utilities on your django project. This functions similar to django-admin, but with your project's environment variables. You can verify if everything is alright by going inside this new directory and typing:
```bash
(django): /project$ python3 manage.py runserver

Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
January 18, 2021 - 10:20:25
Django version 3.1.5, using settings 'dummyproject.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
This should bring up the text as shown above and you should be able to see a debug webpage on the given link. This is your website, hosted locally.

5. Now we make the actual API. This will be an app, as a part of the project. Each project can contain multiple apps. For example, on Facebook, Newsfeed can be a separate app, while settings menu can be a separate app. To make a new app, do
```bash
(django): /project$ python3 manage.py
```

5. The cool thing about django is that, even while the server is running, any changes you make to the view functions


## Learnings

### Setting up a local database connection to Postgres

This turned out to be more simple that I thought it would be. Django provides the option of using multiple databases in the project by simply extending the DATABASES dictionary in the settings.py file of the project and writing a router for the requests. This project only uses a single db, so I simply changed the sqlite3 backend to postgresql like shown below. 

_NOTE: Django does not set up your postgres database like it does for sqlite3. You have to manually make one using any tool like Pg admin._

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'quredb1',
        'USER': 'postgres',
        'PASSWORD': '1234',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

#### Work still happening on the project. More to add later..

#### Code can be found at [the repository link](https://github.com/CodeSeeker99/PS2_repo/tree/main/Django)
