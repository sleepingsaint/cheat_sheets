## Django Deployement Cheat Sheet

Cheat Sheet for deploying Django application on **Heroku**

## Contents

* [Installing GIT](#installing-git)
* [Creating Heroku Account](https://www.heroku.com/)
* [Installing Heroku CLI Tools](#installing-heroku-cli-tools)
* [Login to Heroku from terminal](#logging-into-heroku-from-terminal)
* [Intializing Git in project root](#initializing-git-in-project-root)
* [Creating app in Heroku](#creating-app-in-heroku)
* [Making app production ready](#making-app-production-ready)
    * [Environment & Config Variables](#environment-and-config-variables)
    * [Installing Dependencies](#installing-dependencies)
    * [Modifying settings.py](#modifying-settings)
    * [Creating Required Files](#creating-required-files)
    * [Adding Postgres](#adding-postgres) (Optional)
* [Deploying](#deploying)

## Installing GIT

* Link to download [**GIT**](https://git-scm.com/downloads)
* Learn more about GIT [**here!**](https://www.youtube.com/playlist?list=PL4cUxeGkcC9goXbgTDQ0n_4TBzOO0ocPR)
    * Note: You don't need to dive deep into GIT commands to deploy your app. The following commands are enough.
        * git add
        * git commit
        * git push
        * git remote

## Creating Heroku Account

* First we need to create a account in Heroku. [**Click Here!**](https://www.heroku.com/) to create one.

## Installing Heroku CLI Tools

* After creating a Heroku account we need to install **Heroku CLI Tools** to deploy our apps from terminal.
    * GIT is required for Heroku CLI Tools, ( if not ) [**download**](https://git-scm.com/downloads) it first.
* Link to download [**Heroku CLI Tools**](https://devcenter.heroku.com/articles/heroku-cli#download-and-install).

## Logging into Heroku from Terminal

* Open your terminal / command prompt and type the following command

        heroku login

    * It will open browser to verify the user, ( if needed provide login credentials ).
    * Now you will be logged in to the Heroku.


## Initializing Git in Project Root

        $ git init
        Initialized empty Git repository in .git/

        $ git add .
        $ git commit -m "My first commit"

## Creating App in Heroku

* From Terminal / Command Prompt

    * To create app with random name

            $ heroku create
            Creating app... done, ⬢ <random_app_name>
            https://<random_app_name>.herokuapp.com/ | https://git.heroku.com/<random_app_name>.git 
    
        * The heroku create CLI command creates a new empty application ( with random name ) on Heroku, along with an associated empty Git repository. If you run this command from your app’s root directory, the empty Heroku Git repository is automatically set as a remote for your local repository.

    * To create app with the <app_name> you want

            $ heroku create <app_name>

        * If the name of the app is not already taken it will throw an error saying "<app_name> is already taken try another name".

        * If the <app_name> is available it will create a app and show the success response as follows 

        ```    Creating app... done, ⬢ <app_name>
            https://<app_name>.herokuapp.com/ | https://git.heroku.com/<app_name>.git
        ```

* From Web Interface or GUI

    * Open your browser and log into your account and navigate to your dashboard.
    * There you will be provided with a **new button** on top.
    * Click on that, it will prompt you to enter the name you want to represent your app with.
        * If the name of the app is not already taken it will throw an error saying "<app_name> is already taken try another name".

        * If the <app_name> is available it will create a app and redirect you to the newly created app page.

    * After Creating app with GUI, open up your terminal and follow the following steps:

        ```
        $ heroku login
        ```
        * To login into heroku from the terminal, it will open up your browser to verify the user, ( if needed provide login credentials ).

        * Now you will be logged into Heroku.

    * After creating your Heroku app, you can easily add a remote to your local repository with the heroku git:remote command. All you need is your Heroku app’s name. Navigate into your project root folder and execute the following command

        ``` 
        heroku git:remote -a <app_name>
        set git remote heroku to https://git.heroku.com/<app_name>.git
        ```


## Making app production ready

## Environment and Config Variables

* First step is to take care of the sensitive information like 
    * SECRET_KEY
    * API_KEYS
    * EMAIL_USERNAME
    * EMAIL_PASSWORD

* One way to handle this problem is with **environment variables**
    * Environment varibles are safer when compared to having them as raw text in python files.
    * ***In Heroku, environment variables are referred as Config Variables.***

* Saving Environment Variables

    * From terminal

        * **Listing Config Varibles**

            ```
            $ heroku config
            GITHUB_USERNAME: joesmith
            OTHER_VAR:    production

            $ heroku config:get GITHUB_USERNAME
            joesmith
            ```

        * **Setting Config Variables**
        
            ```
            $ heroku config:set GITHUB_USERNAME=joesmith
            Adding config vars and restarting <app_name>... done, v12
            GITHUB_USERNAME: joesmith
            ```
        
        * **Unsetting Config Variables**
        
            ```
            $ heroku config:unset GITHUB_USERNAME
            Unsetting GITHUB_USERNAME and restarting myapp... done, v13
            ```

    * ***You can also edit config vars from your app’s Settings tab in the Heroku Dashboard***


* They can be accessed as following
    
    ```python
        import os

        SECRET_KEY = os.environ['SECRET_KEY']
        API_KEY = os.environ['API_KEY']
        EMAIL_USERNAME = os.environ['EMAIL_USERNAME']
        EMAIL_PASSWORD = os.environ['EMAIL_PASSWORD']
    ```

## Installing Dependencies

* Whitenoise, Gunicorn, django_heroku

    ```python
        $ pip3 install whitenoise
        $ pip3 install gunicorn
        $ pip3 install django_heroku
    ```

    * **WhiteNoise** allows your web app to serve its own static files, making it a self-contained unit that can be deployed anywhere without relying on nginx, Amazon S3 or any other external service.

        * More about **WhiteNoise** [here!](https://devcenter.heroku.com/articles/django-assets)

    * **Gunicorn** is a pure-Python HTTP server for WSGI applications. It allows you to run any Python application concurrently by running multiple Python processes within a single dyno. It provides a perfect balance of performance, flexibility, and configuration simplicity.

        * More about **Gunicorn and Heroku** [here!](https://devcenter.heroku.com/articles/python-gunicorn)

    * **django_heroku** is a Django library for Heroku applications that ensures a seamless deployment and development experience.
    
        * More about **django_heroku** [here!](https://pypi.org/project/django-heroku/)


## Modifying Settings

* Open your **project/settings.py** and make the following changes.

* Change Debug from True ( for development ) to False ( for production )

    ```python
    DEBUG = False
    ```

* Change Allowed Hosts

    ```python
    ALLOWED_HOSTS=['https://<app_name>.herokuapp.com']
    ```
    
* Adding (if not there) MEDIA_ROOT and STATIC_ROOT
   
   ```python
   MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
   STATIC_ROOT = os.path.join(BASE_DIR, 'static')
   ```
   
   * The above two lines indicates the folders where the static and media files will be stored by running command **python manage.py collectstatic**, this command will be run automatically while deploying the code, so no need to worry about that. 
   
*   For Whitenoise

    * Edit your settings.py file and add WhiteNoise to the MIDDLEWARE_CLASSES list, above all other middleware apart from Django’s SecurityMiddleware:

        ```python
        MIDDLEWARE = [
            # 'django.middleware.security.SecurityMiddleware',
            'whitenoise.middleware.WhiteNoiseMiddleware',
            # ...
        ]
        ```
    * Want forever-cacheable files and compression support? Just add this to your settings.py:

        ```python
        STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
        ```

* For django_heroku

    * In settings.py, at the very bottom:
        ```python
        # Configure Django App for Heroku.
        import django_heroku
        django_heroku.settings(locals())
        ```
        * This will automatically configure **DATABASE_URL, ALLOWED_HOSTS, WhiteNoise (for static assets), Logging, and Heroku CI** for your application.If you set the SECRET_KEY environment variable, it will automatically be used in your Django settings, too!


## Creating Required Files

* Inside the project root folder ( the folder where manage.py exists ) create the following files

    * Procfile
    * .gitignore
    * requirements.txt
    * runtime.txt

* Insert the following line in Procfile

        web: gunicorn bootcamp.wsgi --log-file -

* From shell execute the following commands

        $ pip3 freeze > requirements.txt

* Inside the runtime.txt add the python runtime version number

        python-3.7.2

    * Check out the supported list of [python runtimes on heroku](https://devcenter.heroku.com/articles/python-runtimes)


## Adding Postgres

* Postgres is widely used and popular database. To add postgres to our app, in terminal where you are logged in heroku type the following command:

    ```
    $ heroku addons:create heroku-postgresql:hobby-dev
    Creating heroku-postgresql:hobby-dev on ⬢ sushi... free
    Database has been created and is available
    ! This database is empty. If upgrading, you can transfer
    ! data from another database with pg:copy
    Created postgresql-concave-52656 as DATABASE_URL
    ```

    * Here hobby-dev is postgres database on free plan which provides only 10k rows, which is enough for to test our app before upgrading. Check more about that [here!](https://devcenter.heroku.com/articles/heroku-postgresql) 

---

## Deploying 

* Now the final step of pushing our production ready django app to the heroku

* In the terminal where you logged into heroku, run the following commands:

    * Pushing app to the heroku servers

        ```
        $ git push heroku master
        ```

    * Running migrations for our app from the same terminal

        ```
        $ heroku run python manage.py migrate

        $ heroku run python manage.py migrate --run-syncdb
        ```
        * First Command makes the migrations of the inbuilt models
        * Second commands creates the tables for the models we created in our app/models.py
