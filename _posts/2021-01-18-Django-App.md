---
published: true
---

_Short tutorial on using technologies like Django, Docker, Nginx and Postgres. Used Django backend to create a REST API with a simple CRUD functionality on a local postgres database instance. This tutorial requires you to have basic knowledge of python_

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
