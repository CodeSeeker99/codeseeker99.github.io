---
published: true
---

_Short tutorial on using technologies like Django, Docker, Nginx and Postgres. Used Django backend to create a REST API with a simple CRUD functionality on a local postgres database instance._
**Note**: This tutorial requires you to have basic knowledge of python and linux commands.

---

## BACKGROUND

![Theory image]({{site.baseurl}}/images/Screenshot from 2021-01-18 20-10-37.png)
<p style="color:gray;">Lorem ipsum...</p>

### REST API and CRUD

An __API__ or __Application Programming Interface__, in simple terms is a bunch of code that helps interact with a lot of backend code. It provides definite input, output, and error pathways to the backend code.You can write a basic API using functions in Python.  

A __REST__ API or a __REpresentational State Transfer__ API is an API that follows constraints and operating standards called REST. This standard is popularly used in web services. To list the tenets of a REST API is worth an entire article. Lets just say they standardise service responses so that reading them becomes device/platform independent. This includes, giving responses in JSON/XML, and using only a few HTTP headers (POST, PUT, GET, DELETE). In this project we're going to create a REST API using Django REST framework. A framework is a set of libraries that do most of the heavy lifting for us, and allow us to focus on the functionality of the API rather than thinking about its low-level details.

__Create, Read, Update, and Delete__ or __CRUD__ are a set of basic functions which any service that interacts with a persistent storage (i.e Database) requires. In this project, we will be making an app which does CRUD operations on a Postgres database.

### Django

Django is a web framework based on python. It promotes fast development by pushing a lot of general details such as security and scaling into the framework backend, hence giving the programmer more time to focus on useful code and business logic. It also ships with a default light-weight server, which helps in beginners with quick learning and adoption. Since Django is based on python it also enjoys the huge amount of support in terms of libraries that the python community provides. Django follows the Dont Repeat Yourself (DRY) philosophy, which means, it maximises the reusability of code. The developers claim that 

> Django was designed to help developers take applications from concept to completion as quickly as possible.

**Django REST framework** allows you to use the same backend of django for RESTful services as well. A good feature of this framework is how easy it is to return web-browsable reponses using the standard Django template, which helps in easy debugging. 

### Virtual Environment

A virtual environment is a tool that helps keep your dependencies from multiple projects well separated and documented. This is extremely helpful when you need to export your project and require a list of project-specific dependencies.

---

## TUTORIAL
_Follow along with these instructions to create your own Django app!_

### Installation
Let's begin by installation some necessary packages.

#### Python

Install python3 on your machine. The steps are different for different distributions, so its best to refer the internet for this. Once you have it installed, verify the installation using.
```bash
$ python3 --version
```
This should return the version of python installed.

Python comes with its own virtual environment command called venv. On linux and Mac distributions this will suffice. On windows, you might have to download virtualenvwrapper. To make a new virtual environment, type:
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


#### Dependencies
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


4. We can now finally start with the project. Create a django project using:
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

  - __wsgi.py__ : This is the python file that helps in deployment on different types of production servers (these are called Web Server Gateway Interface (WSGI) compatible servers). We will ignore this right now.
More info [here](https://docs.djangoproject.com/en/1.8/howto/deployment/wsgi/).

  - __urls.py__ : This file helps in managing routing the request to different components inside your project (called apps). More info [here](https://docs.djangoproject.com/en/1.8/topics/http/urls/).

  - __settings.py__ : This file defines a lot of environment variables and settings for the project. Including database and allowed hosts. More info [here](https://docs.djangoproject.com/en/1.8/topics/settings/).

  - __manage.py__ : This file helps you run command line utilities on your django project. This functions similar to django-admin, but with your project's environment variables. You can verify if everything is alright by going inside this new directory and typing:

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
This should start your server on localhost, and bring up the text as shown above. You should be able to see a debug webpage on the given link. This is your website API, hosted locally.

Now, since we are making a REST API and we will be using Django REST framework as well. Go ahead and add 'rest_framework' in the list of **INSTALLED_APPS** in the **settings.py** file. It should now look something like this:
```python
INSTALLED_APPS = [
    'rest_framework',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

5. Now we make the actual API. This will be an app, as a part of the project. Each project can contain multiple apps. For example, on Facebook, Newsfeed can be a separate app, while settings menu can be a separate app. To make a new app, do
```bash
(django): /project$ python3 manage.py startapp api_module
(django): /project$ ls
api_module  db.sqlite3  project  manage.py
(django): /project$ tree ./api_module/
./api_module/
├── admin.py
├── apps.py
├── __init__.py
├── migrations
│   └── __init__.py
├── models.py
├── tests.py
└── views.py
```
As we can see, it creates a new directory with the app name, containing a bunch of files that will be used to handle the app. Now there are many files here that we do not need to focus on for now. We will focus on
  - __models.py__ : This file helps you define the schema for your database. The framework automatically writes the required database description commands for you using this file.
  - __views.py__ : This is where all the processing functions will be defined. Each request will be processed using functions from this file.
 Now go ahead and create 2 more files:
  - __serializers.py__ : We will use this file to define serializers for our models. Because each model has to be converted into JSON/XML (due to RESTfulness) before being sent out.
  - __urls.py__ : We will use this file to define the URL mapping within the API. Basically, this file will map the last part of the url to a function in views.py file

After this, you should also register your app inside your project. Please add the name of the function in **apps.py**, which is **ApiModuleConfig** in my case, to your list in **project/settings.py**. So your list should look like
```python
INSTALLED_APPS = [
    'firstApi.apps.FirstapiConfig',
    'rest_framework',
    #...other apps
]
```
Also, go **project/urls.py** and adding the import statement ```from django.conf.urls import url, include``` along with an entry in the **urlpatterns** list so it looks like
```python
urlpatterns = [
    url(r'^', include('api_module.urls')),
    path('admin/', admin.site.urls),
]
```
This will redirect all requests to the api_module's url router first. There we can manage it separately. Ideally, we should keep a keyword like "api" in the path, but our service is completely an API, so this is not necessary.


6. The cool thing about django is that, while development the server is running, any changes you save to any .py file will be automatically detected, and updated on the hosted website. So you can keep the server running in the background and don't have to manually restart the server each time. Now, we're defining our databse to contain a very simple model, where each entry contains a name, a description and a date attribute of the last updated date. There is also an ID attribute for each entry to function as primary key. Go ahead and make your model something like this.
```python
from django.db import models

# Create your models here.
class PytorchModel(models.Model):
    name = models.CharField(max_length=40, blank=False, unique=True)
    desc = models.CharField(max_length=100, blank=False)
    last_updated = models.DateField(auto_now_add=True)
```
Notice how I used the auto_now_add attribute of the _DateField_ object. This way, we don't have to specify the date while maing an entry, that gets fetched automatically. I've named my model as PytorchModel because I intend to change this model to eventually save actual serialised model weights in the database. But you can keep it anything for now.

7. Now its time to plug in the Postgres database. First, go to your **settings.py** and change the DATABASES dictionary to the following. When you make multiple apps in the same project, you can even append more databases to this list to use as backups or otherwise according to your requirements.
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'db1',
        'USER': 'postgres',
        'PASSWORD': '1234',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```
You can get rid of the db.sqlite3 file in your project folder as it is no longer required. We are using our own db in this project.

8. Lets run our Postgres server using PGadmin. Go to the PG admin server, and create a new database with the name db1, as mentioned in your **settings.py** file.

After creating this model you can run a utility script ```$ python3 manage.py makemigrations```
this will make a **migrations** directory in your app directory which contains instructions to make the databse changes. 

After this, run ```$ python3 manage.py migrate```. This command will migrate all your models to the database. That is, this will set up all your tables accodring to the model types. Those tables ofcourse, will be empty.

9. Now lets add fill the serializer file with code to transform our models to JSON. The Django REST framework makes this very simple by already having created a class called **rest_frameworks.serializers.ModelSerializer**. Simply make a subclass of this class and add your fields to a Meta class inside this class as follows:
```python
from rest_framework import serializers
from firstApi.models import PytorchModel

class PytorchModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = PytorchModel
        fields = ('id', 'name', 'desc', 'last_updated')
```

10. We're almost done! Now all that is left is to route the request to a view, and implement the view itself. First lets handle the routing, in the **api_module/urls.py**, write the following:
```python
from django.conf.urls import url 
from api_module import views

urlpatterns = [
    url(r'^api/list', views.model_list),
]
```
This defines that, if any request has "api/list" after the domain name, it will be handled by the model_list function in the views file. But we haven't implemented anything yet, so lets do that. Go to **views.py** in api_module folder and implement the request handling something like this
```python
from django.shortcuts import render
from django.http.response import JsonResponse

from api_module.models import PytorchModel
from api_module.serializers import PytorchModelSerializer

from rest_framework.decorators import api_view
from rest_framework.parsers import JSONParser 
from rest_framework import status

# Create your views here.
# pk = primary key = id

@api_view(['GET', 'POST', 'DELETE'])
def pytorchModel_list(request):
    if request.method == 'GET':
        req_id = request.GET.get('id', None)
        if req_id is not None:
            try:
                db_row = PytorchModel.objects.get(pk=req_id)
                serialized_row = PytorchModelSerializer(db_row)
                return JsonResponse(serialized_row.data)
            except PytorchModel.DoesNotExist:
                return JsonResponse({'message': 'The Model does not exist'}, status=status.HTTP_404_NOT_FOUND)
        else:
            db_row = PytorchModel.objects.all()
            serialized_row = PytorchModelSerializer(db_row,many=True)
            return JsonResponse(serialized_row.data, safe=False)

    elif request.method == 'POST':
        data = JSONParser().parse(request)
        instance = PytorchModelSerializer(data=data)
        if instance.is_valid():
            instance.save()
            return JsonResponse(instance.data, status=status.HTTP_201_CREATED)
        return JsonResponse(instance.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        req_id = request.GET.get('id', None)
        if req_id is not None:
            try:
                db_row = PytorchModel.objects.get(pk=req_id)
                db_row.delete()
                return JsonResponse({'message': 'Model deleted successfully'}, status=status.HTTP_200_OK)
            except PytorchModel.DoesNotExist:
                return JsonResponse({'message': 'The Model does not exist'}, status=status.HTTP_404_NOT_FOUND)
        else:
            req_name = request.GET.get('name', None)
            if req_name is not None:
                try:
                    db_row = PytorchModel.objects.get(name=req_name)
                    db_row.delete()
                    return JsonResponse({'message': 'Model with name {} deleted successfully'.format(req_name)}, status=status.HTTP_200_OK)
                except PytorchModel.DoesNotExist:
                    return JsonResponse({'message': 'The Model does not exist'}, status=status.HTTP_404_NOT_FOUND)
        return JsonResponse({'message': 'BAD REQUEST'}, status=status.HTTP_400_BAD_REQUEST)

```

#### Work still happening on the project. More to add later..

#### Code can be found at [the repository link](https://github.com/CodeSeeker99/PS2_repo/tree/main/Django)
