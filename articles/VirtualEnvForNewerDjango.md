
<!--
.. title: VirtualEnv for newer Django
.. slug: VirtualEnvForNewerDjango
.. date: 2015-05-13 14:35:28 UTC+01:00
.. tags:
.. category:
.. link:
.. description:
.. type: text
-->





##Guide to setting up a virtualenv for PythonAnywhere Web apps


A virtualenv is a way to have your own private Python environment that has different versions of packages to the system default. You can have many virtualenvs, each with its own versions of installed packages. On PythonAnywhere, this is a great way to use newer (or older) versions of software than the ones we have installed. 

One reason you might want to do this is to use a newer version of Django. For Python 2.7, our system default is currently 1.3.7, while the latest version is 1.9. These instructions explain what to do if you want to use Django 1.9; if it's a different package that you want to upgrade (or you need a different version of Django), then the changes should be pretty obvious. 


###Instructions


Log in to PythonAnywhere, and create a new Web app: 

  * Go to the "Web" tab. 
  * Select the "Add a new web app" option on the left-hand side. 
  * If you have a Web Developer account, specify the domain you want your web app to appear on, then click "Next" 
  * Select the "Manual configuration" option from the list. 
  * Click "Next", and wait for the system to tell you that the web app has been created. 

Now you can check that you have a web app -- go to *yourusername*.pythonanywhere.com in your browser to see the simple site we generate for you. 

You may have noticed a *Virtualenv path* option. Let's go and create a virtualenv, so we have something to put in there. 


###Creating the virtualenv and installing (eg) django


Go to the "Consoles" tab and start a *Bash console*

    14:50 ~ $ mkvirtualenv django19


***TIP: if you want to use Python 3 for your virtualenv, use `mkvirtualenv --python=/usr/bin/python3.4 django19`***

***TIP: if you see an error saying `mkvirtualenv: command not found`, check out [InstallingVirtualenvWrapper](/pages/InstallingVirtualenvWrapper).***

You can check it works -- the prompt should gain the `(django19)` prefix, and you can check `which pip` returns the virtualenv pip: 

    (django19)14:51 ~ $ which pip
    /home/myusername/.virtualenvs/django19/bin/pip


If it hasn't, you'll need to activate the virtualenv, like this: 

    workon django19


You'll need to do this in any bash console when you're trying to install things to your virtualenv or run `manage.py`, otherwise you'll be trying to make changes to the system environment instead of your virtualenv. 

Now install Django: 

    (django19)14:51 ~ $ pip install django


(apologies if this takes a long time. We're working on speeding up disk I/O) 

Now you can check it worked: 

    (django19)15:02 ~ $ which django-admin.py
    /home/myusername/.virtualenvs/django19/bin/django-admin.py
    (django19)15:02 ~ $ django-admin.py --version
    1.9.1


Start a new django project: 

    (django19)15:02 ~ $ django-admin.py startproject mysite


Check it worked: 

    (django19)15:02 ~ $ tree mysite
    mysite
    ├── manage.py
    └── mysite
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py


Note the `mysite/mysite` folder -- it's definitely a version of Django later than 1.4 


###Using the virtualenv in your web app


Now go back to the **Web** tab and edit the WSGI file for your web app (There's a link on the web app tab) and delete everything except the Django section and then uncomment the Django section. Your WSGI file should look something like this: 

    # +++++++++++ DJANGO +++++++++++
    # To use your own django app use code like this:
    import os
    import sys

    # assuming your django settings file is at '/home/myusername/mysite/mysite/settings.py'
    path = '/home/myusername/mysite'
    if path not in sys.path:
        sys.path.append(path)

    os.environ['DJANGO_SETTINGS_MODULE'] = 'mysite.settings'

    ## Uncomment the lines below depending on your Django version
    ###### then, for django >=1.5:
    from django.core.wsgi import get_wsgi_application
    application = get_wsgi_application()
    ###### or, for older django <=1.4
    #import django.core.handlers.wsgi
    #application = django.core.handlers.wsgi.WSGIHandler()


Then, back on the web tab itself, edit the path to your virtualenv in the Virtualenv section. You can specify the full path, */home/myusername/.virtualenvs/django19*, or just the short name of the virtualenv, _django18, and the system will automatically expand it to the full path after you submit it. 

Save it, then go and hit the **Reload** button for your domain. 


###Checking it worked.


Go to site -- you should see the Django "it worked!" page. 

You're all set up, and now you can use your virtualenv ![:\)](/smile.png)


###Extra packages


Don't forget, the virtualenv has its own set of installed packages -- so if you want to use something you'll need to `pip install` it. A common example for Django is the Python 2 MySQL library, for which you'll need to install `mysql-python`: 

    (django19)15:12 ~/mysite $ pip install mysql-python    

The current version of mysql-python (1.5.2) does not support Python 3.  Python 3 users may want to consider alternatives such as mysqlclient

    (django19)15:12 ~/mysite $ pip install mysqlclient

###Static files


Because we had to use manual web app creation to use this virtualenv (instead of PythonAnywhere's built-in Django web app quick-start option, which you'll remember uses an older version of Django), one thing is missing -- configuration for static file mappings. This means that if you enable the Django admin pages, they'll work, but the formatting won't be there because it can't load the CSS where that formatting is specified. 

To fix this, go to the "Web" tab, select your domain at the left if it's not already selected, then in the "Static files" table: 

  * Click on the "Enter URL" and enter `/static/admin/`, then hit return. 
  * Click on the "Enter path" on the same line, and enter `/home/myusername/.virtualenvs/django18/lib/python2.7/site-packages/django/contrib/admin/static/admin`, replacing `myusername` with your username as usual. 
  * Reload the web app again. 

If you visit the admin site after doing that, all of the formatting will be there. 


###Developing with your virtualenv


Remember: whenever you want to get back and work on your virtualenv, you need to make sure it's active -- if you're opening a new console, for example. 

Look out for the little `(django19)` prefix at the command-line. 

It's also well worth checking `which pip` to make sure you're using the virtualenv pip when installing. 

If in doubt, run: 

    (django19)17:02 ~ $ source virtualenvwrapper.sh


To switch on virtualenvwrapper (you can add this to your `.bashrc`) 

And 

    (django19)17:02 ~ $ workon django19


to switch to working on your virtual environment. 
