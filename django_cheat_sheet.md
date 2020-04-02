# Django Cheat Sheet
Cheat sheet to create web applications by using django framework.

Documentation for Django can be found [here!](https://docs.djangoproject.com/en/3.0/)

* ***For windows users change python3 with python and pip3 with pip***
---

## Contents:

* [Creating Virtual Environment](#virtual-environment-(optional)) (optional)
* [Installing Django](#installing-django)
* [Getting Started](#getting-started-with-the-project)
* [Creating App](#creating-a-app)
* [Creating Views](#creating-views)
* [Creating Models](#creating-models)
* [Managing Templates and Static Files](#templates-and-static-files)
* [Using Templates and Static Files](#using-the-templates-in-views)
* [Creating Models](#creating-models)
* [Saving Models](#saving-models)
* [Querying Models](#querying-models)
* [Django Admin Page](#django-admin-page)

---

## Create a folder for your django project
    
        mkdir <project_folder>

## Virtual Environment (Optional)
* Navigate to the **project_folder**

        cd <project_folder>

* Creating Virtual Environment

        python3 -m venv <virtual_environment_name>


* Activating Virtual Environment

        source <virtual_environment_name>/bin/activate

## Installing Django
* Open your shell (after you activated your virtual environment - not needed) and run the following command

        pip3 install django

## Getting started with the project
* Creating a django project

        django-admin startproject <project>

* Now the project folder looks like

        venv
        <project>/
            manage.py
            <project>/
                asgi.py
                __init__.py
                settings.py
                urls.py
                wsgi.py

* Navigate to the inner project folder

        cd <project>

* This directory is referred as ***Project Root***

## Creating a app

        python3 manage.py startapp <app>

* Now the Inner project Folder looks like

        <project>/
            asgi.py
            __init__.py
            settings.py
            urls.py
            wsgi.py
        <app>/
            admin.py
            apps.py
            __init__.py
            migrations/
                __init__.py
            models.py
            tests.py
            views.py

* Including the app in your project

    In your **project**/settings.py add your **app_name** to the INSTALLED_APPS list :

    ```python
        INSTALLED_APPS = [
            'app'
            #...
        ]
    ```
* migrate to apply the changes

        python3 manage.py migrate


## Creating Views

* Inside your **app**/views.py file add the following lines to create a view named **index_view** 

    ```python
        from django.http import HttpResponse

        def index_view(request):
            return HttpResponse('Your first view response')
    ```

* Inside your **app**/urls.py file add the following lines to attach a view to a specific url

    ```python
        from django.urls import path
        from . import views

        urlpatterns = [
            path('', views.index, name="index")
        ]
    ```

* Inside your **project**/urls.py file add the **app** urls 

    ```python
        from django.urls import path, include

        urlpatterns = [
            path('app/', include('apps.urls')),
            path('admin/', admin.site.urls),
        ]
    ```

* To create a url pattern to the index of the site, use the following urlpattern:

    ```python
        from django.urls import path, include

        urlpatterns = [
            path('', include('apps.urls')),
            path('admin/', admin.site.urls),
        ]
    ```

* ***The url files inside the apps you created are managed by the url file inside project folder***

## Templates and Static Files:

* Inside your ***Project Root*** directory create two folders with name templates, static

    * templates holds the HTML files of the apps
    * static folder holds the CSS, JAVASCRIPT files of the apps

* Now we need to tell the django to look for the templates and static files in these folders whenever needed

* In your project/settings.py make the following changes

    * For templates modify the following lines in the TEMPLATES
        
        ```python
            TEMPLATES = [
                {
                    #..
                    'DIRS': [os.path.join(BASE_DIR, 'templates')],
                    #..
                },
            ]
        ```

    * At the end of the file add the following lines

        ```python
            STATICFILES_DIRS = [
                os.path.join(BASE_DIR, "static")
            ]
        ```

* Now store the template files in templates folders by creating seperate sub folders for each app:

        templates/
            base.html
            app1/
                app1_index.html
            app2/
                app2_index.html

* Similarly store the static files:

        static/
            app1/
                app1_css.css
                app1_js.js
            app2/
                app2_css.css
                app2_js.js

* **base.html** in the templates directory

    * ***base.html*** is used as base file which holds components like navbar, footer which can be used in multiple files.  

        ```html
            <!DOCTYPE html>
            <html>
                <head>
                    <!-- Some meta tags -->
                    {% block style %}

                    {% endblock %}
                </head>

                <body>
                    {% block content %}

                    {% endblock %}

                    {% block script%}

                    {% endblock %}
                </body>
            </html>
        ```

## Using the templates in views

* We modify the **index_view** to use the templates

* creating files for the index_view

        templates/
            app/
                index.html
        
        static/
            app/
                index.css
                index.js

* Index.html

    ```html
        {% extends 'base.html' %}
        {% load static %}

        {% block style %}
        <link type="stylesheet" rel="{% static 'app/index.css' %}">
        {% endblock %}

        {% block content %}
            Content for the index.html
        {% endblock %}

        {% block script %}
        <script src="{% static 'app/index.js' %}">
        {% endblock %}
    ```

    * **{% load static %}** loads the static files into the templates you use for the views

* Now **app**/views.py:

    ```python
        from django.http import HttpResponse
        from django.shortcuts import render

        def index_view(request):
            return render(request, 'app/index.html', name="index")
    ```

## Creating Models

* Models represents the table in the databases

* Creating models

    ```python
        from django.db import models
        from django.contrib.auth.models import User

        class TestModel(models.Model):
            field0 = models.ForeignKey(User, on_delete=models.CASCADE)
            field1 = models.CharField(max_length=10)
            field2 = models.IntegerField()
            field3 = models.TextField()

            def __str__(self):
                return self.field0.username
    ```

* Check more about [Django Model Fields Here!](https://docs.djangoproject.com/en/3.0/topics/db/models/) 

* Migrating Models (To inact changes in your models, use the following commands in your shell):

    ```shell
        python3 manage.py makemigrations <app_name>
        python3 manage.py migrate
    ```

    * Note: including <app_name> is optional.

## Saving Models:

    ```python
        from app.models import TestModel

        test1 = TestModel(field0=User, field1="some characters", field2=0, field3="Some lorem ipsum text")
        
        test1.save()
    ```

## Querying Models

    ```python
        from app.models import TestModel

        test_object = TestModel.objects.get(field2=0)
        test_objects = TestModel.objects.all()
        test_objects = TestModel.objects.filter(field2=0)
    ```

    * .get() returns only one objects,
    * .all() returns all the objects
    * .filter() returns all the objects with the provided the arguments

* Check more about [Django Model Queries Here!](https://docs.djangoproject.com/en/3.0/topics/db/queries/) 

## Django Admin Page

* Django admin page provides us with a friendly user interface to manage our project database.

* By default the models we create in our app/models.py don't get registered in admin page.

* To register the models in the admin site, change the **admin.py** file in the same app as follow

```python
        from django.contrib import admin
        from .models import TestModel

        admin.site.register(TestModel)
```

* But to access admin site we need to create a user with required privilages and that user is **superuser** in django terms.

* Creating Super User

        python3 manage.py createsuperuser

        username(leave blank to use '<your_username>'):
        email:
        Password:
        Password(again):

        # after filling the above details
        Superuser created successfully.
