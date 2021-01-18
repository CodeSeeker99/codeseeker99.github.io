---
published: true
---

_Project meant to help ramp myself up on technologies like Django, Docker, Nginx and Postgres. Used Django backend to create a REST API with a simple CRUD functionality on a local postgres database instance._

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
