---
layout:     post
title:      Django Cheatsheet
date:       2022-02-18 12:23:25 +0100
categories: cheatsheets
---

## Project
To create a blank new project:
```bash
django-admin startproject mysite
```

To apply changes of a database:
```bash
python manage.py migrate
```

To start the dev server:
```bash
python manage.py runserver
```
If you want to run it on a specific port try:
```bash
python manage.py runserver 127.0.0.1:<port>
```

# Project Structure
* manage.py : command-line utility.
* mysite/ : project folder.
	* asgi.py : ASGI configuration (Asynchronous Web Server & App).
	* settings.py : project configuration.
	* urls.py : file to define your urls.
	* wsgi.py: WSGI configuration (Web Server Gateway Interface).

<hr>

## Apps
To create a new app
```bash
python manage.py startapp <appName>
```

You will need a model to design you app data schema. In every Django App, you have a file named **models.py** where you can create the models defining python classes. Each class represents a db table and its attributes are the fields of the table.
[Model Field Reference](https://docs.djangoproject.com/en/4.0/ref/models/fields/){:target="_blank"}

# App Structure
* admin.py : register models for django admin.
* app.py : main config of the app.
* migrations/ : contains databases migrations.
* models.py : data models of the app.
* tests.py : app tests.
* views.py : has the logic of the app.

When you change a model you need to run the django migrations system that propagates the changes to the database tables:
```bash
python manage.py makemigrations <app>
```

And then sync the project with the new model:
```bash
python manage.py migrate
```

<hr>

## SuperUser
Django has a built-in administration interface that can be usefull to see the data and edit it.
To create a superuser:
```bash
python manage.py createsuperuser
```

To add a model to your administration site, you'll have to edit admin.py file of your app:
```python
from django.contrib import admin
from .models import <model>

admin.site.register(<model>)
```
You can customize this interface to display the data as you want. [Django Admin Customization](https://realpython.com/customize-django-admin-python/#customizing-the-django-admin){:target="_blank"}

<hr>

## QuerySets and managers
You can define the database you are using in settings.py.
* Creating data - save()
* Getting data - get()
* Retrieving all objects - all()
* Retrieving filtered data - filter() or exclude()
* Ordering data - order_by()
* Deleting objects - delete()

# Managers 
The default manager is `objects` but you can define alternative managers to directly retrieve filtered data. Let's see an example of a blog application manager:
```python
class PublishedManager (models.Manager):
	def get_queryset(self):
		return super(PublishedManager, self).get_queryset().filter(status='published')

class Post(models.Model):
	# ...
	objects = models.Manager() # default manager (if you dont define it, it won't be created when you have custom managers)
	published = PublishedManager() # custom manager
```

<hr>

## Building List and detail views
Create a view inside of your app directory on `views.py`. Let's use again the blog site example:
```python
from django.shortcuts import render, get_object_or_404
from .models import Post

def post_list (request):
	posts = Post.published.all() # we're using the custom manager ;)
	return render(request, 'blog/post/list.html', {'posts' : posts})

def post_detail (request, year, month, day, post):
	post = get_object_or_404(Post, slug=post, ...)
	return render (request, 'blog/post/detail.html', {'posts': posts})
```

# Adding URL patterns for each view
You can create custom URL patterns for your apps by defining them in `urls.py` inside your app directory. Using the blog site it would look like this: 
```python
from django.urls import path
from . import views
app_name = 'blog'

urlpatterns = [
	# post views
	path('', views.post_list, name='post_list'),
	path('<int:year>/<int:month>/<int/day>/<slug:post>/',
			views.post_detail,
			name='post_detail'),
]
```
