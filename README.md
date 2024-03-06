# Sample Django Project

We will implement and achieve the following:

1. Setup a Django project
2. Configure URLs
3. Create database models
4. Render dynamic data with Templates
5. Use the Django Admin Panel

## 0 Steps

1. Set up Virtual Environment
2. Create Django Project and App
3. Link Django App with Project
4. Create URLs Roots and Views
5. Render dynamic data with Templates
6. Create database models
7. Rendering Database Entries
8. Django Admin Panel

## 1. Set up Virtual Environment

- Create a base folder to hold your Python projects.

        # Create folder to hold virtual environment
        mkdir PythonProjects

- Execute the following commands inside that folder

        cd PythonProjects

- Execute the following inside base folder to set up virtual environment

        # Create a virtual environment
        python3 -m venv myenv

        # Activate the virtual environment
        source myenv/bin/activate  # On Windows: myenv\Scripts\activate

        # Install Django
        pip install django

## 2. Create Django Project and App

- Execute the following to create Django project and app

        # Create Django project
        django-admin startproject SampleDjangoProject

        # Navigate to project directory
        cd SampleDjangoProject

        # Create Django app
        python manage.py startapp myapp

## 3. Link Django App with Project

- Update the INSTALLED_APPS section of `SampleDjangoProject/SampleDjangoProject/settings.py` with the the name of our new app (in String). See code below:

        # Application definition

        INSTALLED_APPS = [
            'django.contrib.admin',
            'django.contrib.auth',
            'django.contrib.contenttypes',
            'django.contrib.sessions',
            'django.contrib.messages',
            'django.contrib.staticfiles',
            "myapp"
        ]

## 4. Create URLs Roots and Views

1.  Enter the following in in the views file: `SampleDjangoProject/myapp/views.py`

        from django.shortcuts import render, HttpResponse

        # Create your views here.

        def home(request):
            return HttpResponse("hello earthlings!")

2.  Create a URL within the application

- Create a urls file inside myapp: `SampleDjangoProject/myapp/urls.py`
- This is where we create URL roots and connect them to the views
- We are going to import the views file into the new urls file
- Update with the following code:

        from django.urls import path
        from . import views

        urlpatterns = [
            path("", views.home, name="home")
        ]

3. Create the URL to the application

- Go to this file: `SampleDjangoProject/SampleDjangoProject/urls.py`
- Create a URL Root that would allow us to connect to our application

        from django.contrib import admin
        from django.urls import path, include

        urlpatterns = [
            path('admin/', admin.site.urls),
            path("", include("myapp.urls"))
        ]

## 5. Render Dynamic Data with Templates

1.  Create a templates folder inside myapp application: `SampleDjangoProject/myapp/templates`
2.  Inside that create a new file called base.html: `SampleDjangoProject/myapp/templates/base.html`

        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8" />
            <meta name="viewport" content="width=device-width, initial-scale=1.0" />
            <title>{% block title %} Django App {% endblock %}</title>
            <link
            rel="stylesheet"
            href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
            rel="stylesheet"
            />
        </head>
        <body>
            <nav class="navbar navbar-expand-lg navbar-light bg-light">
            <a class="navbar-brand" href="#">My Django App</a>
            <button
                class="navbar-toggler"
                type="button"
                data-toggle="collapse"
                data-target="#navbarNav"
                aria-controls="navbarNav"
                aria-expanded="false"
                aria-label="Toggle navigation"
            >
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav">
                <li class="nav-item active">
                    <a class="nav-link" href="#">Home</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">About</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">Contact</a>
                </li>
                </ul>
            </div>
            </nav>
            <div class="container">{% block content %} {% endblock %}</div>
            <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js"></script>
            <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.bundle.min.js"></script>
        </body>
        </html>

3.  Create another template file: `SampleDjangoProject/myapp/templates/home.html`

- This extends the `base.html` template

        {% extends "base.html" %} 
        
        {% block title %} 
            Home Page 
        {% endblock %}

        {% block content %}
            <p>This is the home page</p>
        {% endblock %}

4. Do the following to render the template

- Update the views file as follows: `SampleDjangoProject/myapp/views.py`

        from django.shortcuts import render, HttpResponse

        # Create your views here.

        def home(request):
            return render(request, "home.html")

## 6. Create Database Models

1. Create a database model that we can access and use from Django

- Include the following in the `SampleDjangoProject/myapp/models.py` to create a model

        class TodoItem(models,.Model):
            title = models.CharField(max_Length=200)
            completed = models.BooleanField(default=False)

2. Register the model so that it can appear in the Admin panel

- Update the admin.py file to include the following: `SampleDjangoProject/myapp/admin.py`

        from django.contrib import admin
        from .models import TodoItem

        # Register your models here.
        admin.site.register(TodoItem)

3. Make a migration.

- Do this every single time you make a change to the base of your project
- Run the following script in the base of the project

        python manage.py makemigrations
        python manage.py migrate

## 7. Rendering Database Entries

- Creating a new view - performing operations on the database.
- Create a new template: `SampleDjangoProject/myapp/templates/todos.html`

        {% extends "base.html" %} {% block content %}
        <h1>Todo List</h1>
        <ul>
            {% for todo in todos %}
            <li>
                {{ todo.title }}: {% if todo.completed %}Completed{% else %}Not completed{% endif %}
            </li>
            {% endfor %}
        </ul>
        {% endblock %}

- Create a view that renders the template: `SampleDjangoProject/myapp/views.py`

        from django.shortcuts import render, HttpResponse
        from .models import TodoItem

        # Create your views here.

        def home(request):
            return render(request, "home.html")

        def todos(request):
            items = TodoItem.objects.all()
            return render(request, "todos.html", {"todos": items})

- Create a URL for this template or route: `SampleDjangoProject/myapp/urls.py`
- Create a path:

        from django.urls import path
        from . import views

        urlpatterns = [
            path("", views.home, name="home"),
            path("todos/", views.todos, name="Todos")
        ]

- Run the server:

        python manage.py runserver

- Visit `http://127.0.0.1:8000/todos` to see the Todos page

## 8. Django Admin Panel

- This allows us to manage users and the database models
- Create a user to work with the Django Admin Panel

        python manage.py createsuperuser

- Accept default values and enter a password
- Run the server:

        python manage.py runserver

- Visit `http://127.0.0.1:8000/admin` to see the Todos page

## End.
